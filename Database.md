# General
- Use [dbdiagram](https://dbdiagram.io/d) to implement ERD
- Always backup the latest ERD (**PDF** for diagram, **txt** for code) on Google Drive
- All entities should have `created_at` and `updated_at` except from mediate entities
- Should have `deleted_at` in important tables
- All `tinyint` columns should have comment explaining the meaning for each value. Ex: `0: false; 1: true` or `0: normal; 1: admin; 2: system admin`

# Name
- The **primary key** column should be `id` for number; `code` for string
- The **foreign key** column should have suffix `_id` at ending
- All **foreign key** column should be ordered following the  primary key
- The boolean column should have prefix `is_` at beginning
- The unique index name must have prefix `uidx_` at beginning, concat related column names by underscore `_`. Ex: `uidx_userid_loginid_deviceid`
- The index name must have prefix `idx_` at beginning, concat related column names by underscore `_`. Ex: `idx_userid_currencypairid `

# Data Type
- The **primary key** should be `bigint(20)` for number; `varchar(8)` or `varchar(16)` or `varchar(32)` or  `varchar(64)` or `varchar(128)` for string
- All `integer` values should be `int(11)`, `not null` and `default: 0`
- All `float` values should be `float`, `not null` and `default: 0`
- The data type of money should be `decimal(19,4)`, `not null` and `default: 0`
- All `boolean` values should be `tinyint(4)`, `not null`, `default: 0` (has 2 values `0` and `1` only)
- All `enum` values should be `tinyint(4)`, `not null`, `default: 0` instead of `enum` type
- The `created_at` column should be `timestamp`, `not null`, `default: CURRENT_TIMESTAMP`

# Some example

```sql
Enum EnumReviewObjectType {
  Comment
  Reply
}

Table users {
  id bigint(32) [pk]
  traderby_user_id varchar
  description varchar(255)
  win int(11) [not null, default: 0]
  loss int(11) [not null, default: 0]
  draw int(11) [not null, default: 0]
  wld_amount decimal(19,4) [not null, default: 0]
  available_asset decimal(19,4) [not null, default: 0]
  total_asset decimal(19,4) [default: 1000000]
  last_reset_asset decimal(19,4)
  last_reset datetime
  last_login datetime
  created_at timestamp [not null, default: `CURRENT_TIMESTAMP`]
  updated_at datetime
  deleted_at datetime
}

// Mediate entity
Table user_followers {
  id bigint(20) [pk]
  user_id bigint(20) [ref:> users.id]
  follower_id bigint(20) [ref:> users.id]
}

Table user_settings {
  user_id bigint(20) [pk, ref:- users.id]
  chart_settings JSON [default: '{"normal": {"kind": 1}}', note:"kind: 0: None; 1: Moving average; 2: Bollinger band"]
  notification_setting JSON
}

Table reviews {
  id bigint(20) [pk]
  reviewer_id bigint(20) [ref:> users.id]
  object_id bigint(20)
  object_type EnumReviewObjectType [not null, default: "Comment"]
  is_liked tinyint(4) [not null, default: 0]
  liked_at datetime
  created_at timestamp [not null, default: `CURRENT_TIMESTAMP`]
  updated_at datetime
}

Table orders {
  id bigint(20) [pk, increment]
  user_id bigint(20) [ref:> users.id]
  position_id bigint(20) [ref:> positions.id]
  currency_pair_id bigint(20)
  pair_code_uid varchar(36) [default: null]
  kind tinyint(4) [not null, default: 0, note:"0: New, 1: Limit, 2: Stop, 3: Support"]
  action tinyint(4) [not null, default: 0, note:"0: Buy, 1: Sell"]
  trade_division tinyint(4) [not null, default: 0, note:"0: New, 1: Settlement"]
  lot_quantity int(11) [not null, default: 0]
  order_rate decimal(19,4) [not null, default: 0]
  margin_amount decimal(19,4) [not null, default: 0]
  profit_loss_amount decimal(19,4) [not null, default: 0]
  expired_datetime datetime
  is_weekend_rate tinyint(4) [not null, default: 0]
  status tinyint(4) [not null, default: 0, note:"0: Pending; 1: Completed"]
  created_at timestamp [not null, default: `CURRENT_TIMESTAMP`]
  updated_at datetime
  deleted_at datetime
}

Table positions {
  id bigint(20) [pk, increment]
  user_id bigint(20) [ref:> users.id]
  lot_quantity int(11) [not null, default: 0]
  remain_lot_quantity int(11) [not null, default: 0]
  position_rate decimal(19,4) [not null, default: 0]
  margin_amount decimal(19,4) [not null, default: 0]
  status tinyint(4) [not null, default: 0, note:"0: Opened, 1: Closed"]
  created_at timestamp [not null, default: `CURRENT_TIMESTAMP`]
  updated_at datetime
}

Table ranks {
  user_id bigint(20) [pk, ref:- users.id]
  kind tinyint(4) [not null, default: 0, note:"0: Today, 1: 3 Days, 2: 1 Week"]
  level int(11) [not null, default: 0]
  trend int(11) [not null, default: 0]
  win int(11) [not null, default: 0]
  loss int(11) [not null, default: 0]
  draw int(11) [not null, default: 0]
  profit_loss_amount decimal(19,4) [not null, default: 0]
  created_at timestamp [not null, default: `CURRENT_TIMESTAMP`]
  updated_at datetime
}
```