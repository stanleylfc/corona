+++
title="bigquery 实战（三）| looker model"
tags=["bigquery"]
categories=["bigquery"]
date="2020-11-19T00:00:00+08:00"
+++
## 1.创建model
### 1.1 直接创建 
### 1.2 基本table创建
### 1.3 基于dashboard创建
### 1.4 基于现存model 创建新的model
```
view: xxx_new {
  suggestions: no
  derived_table: {
    explore_source: xxx {
      column: dimension_date {}
      column: total {}
    
      bind_filters: {
        from_field: yyy.date
        to_field: xxx.date
      }
      bind_filters: {
        from_field: yyy.select_dimension_date
        to_field: xxx.select_dimension_date
      }
      filters: {
        field: xxxx.network
        value: "const"
      }
    }
  }

  dimension: dimension_date {
    hidden: yes
  }
  measure: total {
    type: number
    label: "总数"
    sql: ${sql} ;;
  }
}
```

## 2.编写model
### 2.1 基本框架
```
## model 开头规范
include: "/gta_datacenter.model.lkml"
view: events_mod_level {
  suggestions: no

  extends: [
    events_root,
    events_mod_ad_root,
    events_mod_iap_root,
    events_mod_item_root,
    events_mod_level_root,
  ]
  ....
}

## 内容书写
## title ## {
  ## 0. field1 dimension {
  dimension: xxx {
    type: number
    group_label: "xxx"
    sql: {$sql} ;;
  }
  
  measure: xxxx {
    type: count_distinct
    group_label: "xxxx"
    sql: ${xxxx} ;;
  }

  ## end 0. field1 dimension}
## title }    
```
### 2.3 dimension
```
  dimension: spend {
    type: number
    sql: ${TABLE}.spend ;;
  }
```
### 2.4 measure 
```
  measure: view_user {
    type: count_distinct
    group_label: "quest"
    sql: ${view_user_d}  ;;
  }

  measure: end {
    type: sum
    group_label: "quest"
    sql: ${end_d}  ;;
  }


```
### 2.5 export 导出
- 可以在exporter 里查看
#### 2.5.1 一对一关联导出
```
explore: m_xxx {
  group_label: "label"
  join: m_4_xxx {
    relationship: one_to_one
    type: left_outer
    sql_on: ${m_xxx.dimension_date} = ${m_4_xxx.dimension_date};;
  }
}
```