## view

```html
<!-- BEGIN CONTENT BODY -->
<div class="page-content" >
    <!-- BEGIN PAGE BAR -->
    <div class="page-bar">
        <ul class="page-breadcrumb">
            <li>
                <a href="<?php echo get_url('Welcome/portal'); ?>">首页</a>
                <i class="fa fa-caret-right"></i>
            </li>
            <li>
                <span>投放报表</span>
                <i class="fa fa-caret-right"></i>
            </li>
            <li>
                <span>日投产ROI</span>
                <span style="color: red">( 数据最近更新时间：<?php echo $last_update_time; ?> )</span>
            </li>
        </ul>
        <div class="portlet-title bg-grey-cararra-smh" style="float: right; margin-top: 3px;">
            <div class="actions">
                <div id="switch_date"><!--时间选择工具--></div>
            </div>
        </div>
    </div>
    <!-- END PAGE BAR -->

    <!-- END PAGE HEADER-->
    <div class="portlet light portlet-fit bordered" ng-app="app" ng-controller="myCtrl">
        <div class="portlet-body">
            <form class="slot-filter row" role="form" id="search_form" onsubmit="return false;">
                <div class="col-md-12">
                    <div class="slot-filter-choose col-md-3">
                        <div class="mt-radio-inline inline-block">
                            <label class="mt-radio mt-radio-outline slot_filter_switch">
                                <input type="radio" name="type" value="app_id"> 游戏包
                                <span></span>
                            </label>
                            <label class="mt-radio mt-radio-outline slot_filter_switch" >
                                <input type="radio" name="type" value="group_id"> 分组
                                <span></span>
                            </label>
                            <label class="mt-radio mt-radio-outline slot_filter_switch">
                                <input type="radio" name="type" value="page_id" checked> 推广活动
                                <span></span>
                            </label>
                        </div>  
                        <div class="form-group slot_filter slot_filter_group_id" style="display: none;">
                            <select class="form-control select2" id="group_id" name="group_id">
                                <option value="">选择一个分组</option>
                            </select>
                        </div>
                        <div class="form-group slot_filter slot_filter_page_id">
                            <select class="form-control select2" id="page_id" name="page_id">
                                <option value="">选择一个推广活动</option>
                            </select>
                        </div>
                        <input type="hidden" id="game_id" name="game_id" value="<?php echo isset($_REQUEST['game_id']) ? $_REQUEST['game_id']: '';?>">

                        <div class="form-group slot_filter slot_filter_app_id" style="display: none;">
                            <select class="form-control select2" id="app_id" name="app_id">
                                <option value="">选择一个游戏包</option>
                            </select>
                        </div>
                    </div>
                    <div class="slot-filter-date col-md-2">
                        <div class="form-group">
                            <label>媒体选择</label>
                            <select id="media_id" name="media_id" class="form-control select2">
                                <option value="">选择一个媒体</option>
                            </select>
                        </div>
                    </div>
                    
                    <input type="hidden" name="date_start" id="date_start">
                    <input type="hidden" name="date_end" id="date_end">

                    <div class="col-md-2">
                        <div class="form-actions">
                            <button type="submit" class="btn green"><i class="fa fa-search"></i> 查询</button>
                            <button type="reset" class="btn default"><i class="fa fa-rotate-left"></i> 重置</button>
                        </div>
                    </div>
                </div>
            </form>
        </div>
        <div class="portlet-body">

            <div class="col-md-8 col-sm-12" style="padding-left:0;">
                <div id="table_info"></div>
            </div>
            <div id="toolbarBox"></div>
            <div id="tableBox" style="height: 600px;" class="ag-theme-blue"></div>
        </div>
    </div>
    <!-- END CONTENT BODY -->
</div>
<script language="javascript">
    
    var common_clolumn = [
        {title: '成本', field: "page_cost"},
        {title: '总回收', field: "total_money"},
        {title: 'ROI(总回收/成本)', field: "total_money/page_cost", render:"%", tag:"ROI"},
        {title: '首日回收', field: "p1", tag:"回收"}, {title: 'ROI1', field: "p1/page_cost", render:"%",tag:"ROI"},
        <?php foreach ($cols as $k => $v) { ?>
            {title: '<?php echo $v;?>日回收', field: "p<?php echo $v;?>", tag:"回收"}, {title: 'ROI<?php echo $v;?>', field: "p<?php echo $v;?>/page_cost", render:"%",tag:"ROI"},
        <?php } ?>
    ];
    var data_page = {columns: [
        {title: '日期', field: "s_reg_date"},
        {title: '游戏', field: "s_gname"},
        {title: '游戏包', field: "s_aname"},
        {title: '媒体', field: "s_mname"},
        {title: '组名称', field: "s_gpname"},
        {title: '投放名称', field: "s_tname"},
    ].concat(common_clolumn), rows: []};

    $(document).ready(function() {

        In.ready('bootstrap-daterangepicker', 'biview',function () {
            
            var biview = BIView("tableBox", data_page, {
                showToolPanel: false,
            });
            biview.renderToolbar("toolbarBox");

            $('#search_form').submit(function () {
                load_data();
            });

            $('.slot_filter_switch input').on('change', function() {
                var type = $(this).val();
                $('.slot_filter').hide();
                $('.slot_filter_'+type).show();
                
                $('#' + type).select2('open');

                $('#page_id').val('').trigger('change');
                $('#app_id').val('').trigger('change');
                $('#group_id').val('').trigger('change');
            });

            admin.load_select_input($('#app_id'), '选择一个游戏包', "<?php if (isset($_REQUEST['game_id'])) { echo 'console/Ajax/ajaxSelectGameAppList&game_id='.$_REQUEST['game_id']; } else { echo 'console/Ajax/ajaxSelectGameAppList'; }?>", 'id', 'text');

            admin.load_select_input($('#group_id'), '选择一个分组', 'console/Ajax/ajaxSelectPageGroupList', 'id', 'name');
            
            admin.load_select_input($('#page_id'), '选择一个推广活动', "<?php if (isset($_REQUEST['game_id'])) { echo 'console/Ajax/ajaxSelectPageList&game_id='.$_REQUEST['game_id']; } else { echo 'console/Ajax/ajaxSelectPageList'; }?>", 'id', 'name');


            admin.load_data_get('<?php echo get_url("Ajax/ajaxSelectMediaList");?>', {}, function(res) {
                $('#media_id').select2({
                    placeholder: "选择一个媒体",
                    allowClear: true,
                    data: admin.format_game_app(res)
                });
            });

            admin.init_switch_date_tool($('#switch_date'), {
                startDateInput : 'date_start', // 开始时间
                endDateInput : 'date_end', // 结束时间
                show: 'switch_date_show', // 时间展示div
                active: 2, // 当前选择的时间按钮
                format: 'yyyy-MM-dd', // 控件时间格式
                init: function () {
                    //load_data();
                },
                callback: function () {
                    load_data();
                }
            });
            
            function load_data() {
                biview.showLoading();
                var params = $('#search_form').serialize();
                admin.load_data_get('<?php echo get_url("AdvReport/day_roi");?>', params , function(res){
                    
                    biview.loadData(res.rows);

                }, function(res) {
                    admin.msg_error('服务器故障.');
                    biview.hideOverlay();
                });
            }
        });
    });
</script>
```
