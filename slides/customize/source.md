### 报表配置

```json
{
  // 接口定义 报表接口配置 (API/SQL)
  "source": {
    "type": "sql",
    "code": "with ty as (\nselect create_date, account, media_name, ..."
  },

  // 参数配置 接口请求参数配置 (游戏, 区服, 媒体 ..)
  "query": {
    "allowAllGames": true,
    "interval": false,
    "dateRange": false,
    "predicate": false,
    "filters": [
      "account_id"
    ],
    "defaultDateRange": "nearly_7",
    "extra": null
  },
  
  // 数据处理 接口返回数据处理 (运算/汇总/备注)
  "columns": [
    {
      "field": "_FIELD_076242",
      "title": "CPM",
      "getter": "cost / show_num * 1000",
      "render": "$",
      "summary": "sum()"
    },
    {
      "field": "_FILED_create_date",
      "title": "日期",
      "getter": "create_date",
      "render": "",
      "summary": "",
      "comment": ""
    },
    {
      "field": "_FILED_media_name",
      "title": "媒体",
      "getter": "media_name",
      "render": "",
      "summary": "",
      "comment": ""
    },
    {
      "field": "_FILED_account",
      "title": "账号",
      "getter": "account",
      "render": "",
      "summary": "",
      "comment": ""
    },
    {
      "field": "_FILED_cost",
      "title": "消耗",
      "getter": "cost",
      "render": "",
      "summary": "sum()",
      "comment": ""
    }
  ]
}
```