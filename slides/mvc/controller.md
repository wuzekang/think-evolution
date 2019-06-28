### controller

```php
class AdvReport extends CMS_Console_Controller {
  
    private function roi($type) {
        $get = $this->input->get(null, TRUE);
        $getData = array(
            'limit'   => isset($get['limit'])?intval($get['limit']):20,
            'offset'   => isset($get['limit'])?intval($get['offset']):0,
            'sort' => isset($get['sort'])?trim($get['sort']):'s_reg_date',
            'order' => isset($get['order'])?trim($get['order']):'desc',

            'type'   => isset($get['type'])? trim($get['type']) :'page_id',

            'game_id'   => isset($get['game_id'])?intval($get['game_id']):false,
            'app_id'   => isset($get['app_id'])?intval($get['app_id']):false,
            'media_id' => isset($get['media_id'])?intval($get['media_id']):false,
            'page_id' => isset($get['page_id'])?intval($get['page_id']):false,
            'group_id' => isset($get['group_id'])?intval($get['group_id']):false,

            'date_start' => isset($get['date_start'])?trim($get['date_start']):false,
            'date_end' => isset($get['date_end'])?trim($get['date_end']):false,
        );

        if ($this->input->is_ajax_request()) {
            $where = array(
                'type'          => $getData['type'],
                'game_id'       => $getData['game_id'],
                'app_id'        => $getData['app_id'],
                'media_id'      => $getData['media_id'],
                'page_id'       => $getData['page_id'],
                'group_id'      => $getData['group_id'],

                'date_start'    => $getData['date_start'],
                'date_end'      => $getData['date_end'],
            );
            $pageData = $this->AdvReportModel->roi($type, $where, $getData['limit'], $getData['offset'], $getData['sort'].' '.$getData['order']);
            $this->return_data($get, $pageData);
            exit;
        }
    }
}
```