### model

```php
class AdvReportModel extends PgCommonModel {

    public function roi($type, $where, $limit = 20, $offset = 0, $sort='') {
        $_before = array_sum(explode(' ', microtime()));

        $data = array('total'=> 0, 'rows'=> [], 'query_sql'=>'', 'query_time'=> 0, 'count_sql'=>'');

        $_where = "where page.rtype=3 and g.tag != '下线'";
        $_group = " group by s_reg_date";

        if ($where['page_id']) {
            $_where .= " and o.page_id = {$where['page_id']}";
        }

        if (isset($where['app_id']) && $where['app_id']) {
            $_where .= " and o.pid = {$where['app_id']}";
        }

        if ($where['game_id']) {
            $_where .= " and g.id = {$where['game_id']}";
        }

        if ($where['media_id']) {
            $_where .= " and m.id = {$where['media_id']}";
        }
        if ( $where['group_id']) {
            $_where .= " and gp.id = {$where['group_id']}";
        }

        $table = "ty_user_orders_$type";

        if (isset($where['date_start']) && $where['date_start'] && isset($where['date_end']) && $where['date_end']) {

            $_s = strtotime($where['date_start']);
            $_e = strtotime($where['date_end']);

            $_where .= " and o.reg_date between ". date('Ymd', $_s) ." and ". date('Ymd', $_e);
        }

        $select = "min(g.name) as s_gname,min(page.name) as s_tname, min(gp.name) as s_gpname,min(app.name) as s_aname,min(m.name) as s_mname,";

        if ($type == 'd') {
            $select .= "o.reg_date as s_reg_date,sum(o.p1) as p1,sum(o.p2) as p2, sum(o.p3) as p3, sum(o.p4) as p4, 
                    sum(o.p5) as p5,sum(o.p6) as p6,sum(o.p7) as p7,sum(o.p14) as p14,sum(o.p30) as p30, sum(o.p45) as p45, 
                    sum(o.p60) as p60, sum(o.p75) as p75, sum(o.p90) as p90, sum(o.p105) as p105, sum(o.p120) as p120, 
                    sum(o.total_money) as total_money";

        } else if ($type == 'w') {
            $select .= "to_char(to_date(o.reg_date::text,'YYYYMMDD'),'YYYY-WW') || '周' as s_reg_date,
                    sum(o.w1) as w1,sum(o.w2) as w2, sum(o.w3) as w3, sum(o.w4) as w4,sum(o.w5) as w5,sum(o.w6) as w6, 
                    sum(o.w7) as w7,sum(o.w8) as w8,sum(o.w9) as w9,sum(o.w10) as w10,
                    sum(o.w11) as w11, sum(o.w12) as w12, sum(o.total_money) as total_money";

        } else if ($type == 'm') {
            $select .= "to_char(to_date(o.reg_date::text,'yyyymmdd'),'yyyy-mm') || '月' as s_reg_date,
                    sum(o.m1) as m1,sum(o.m2) as m2, sum(o.m3) as m3, sum(o.m4) as m4, sum(o.m5) as m5,
                    sum(o.m6) as m6,sum(o.m7) as m7,sum(o.m8) as m8,sum(o.m9) as m9,sum(o.m10) as m10,
                    sum(o.m11) as m11,sum(o.m12) as m12, sum(o.total_money) as total_money";
        }

        $sql = "select $select, min(cost.cost) as page_cost from $table o 
                left join ty_page as page on o.page_id = page.id
                left join ty_game_app as app on app.id = o.pid
                left join ty_game as g on g.id = app.game_id
                left join ty_page_group as gp on gp.id = page.group_id
                left join ty_page_media as m on m.id = page.media_id
                left join ty_page_cost as cost on cost.p_id = page.id and cost.date = o.reg_date
                $_where $_group,o.page_id";

        // 组织排序
        if ($sort) {
            $sql .= " order by $sort";
        }

        $data['rows'] = $this->queryFindAll($sql);

        $data['query_sql'] = $sql;

        $data['query_time'] = array_sum(explode(' ', microtime())) - $_before;

        return $data;
    }
}

```

