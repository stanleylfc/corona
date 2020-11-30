+++
title="bigquery 实战（四）| looker sql"
tags=["bigquery"]
categories=["bigquery"]
date="2020-11-19T00:00:00+08:00"
+++

## ANY_VALUE
- any_value()函数来抑制ONLY_FULL_GROUP_BY值被拒绝
- any_value()会选择被分到同一组的数据里第一条数据的指定列值作为返回数据
- coalesce()返回参数中的第一个非空表达式（从左向右依次类推）； 
```
  measure: total {
    type: number
    label: "总数"
    value_format_name: usd_2
    sql: ANY_VALUE(coalesce(${TABLE}.sum_spend, 0));;
  }
```

## value_format_name
```
  measure: diff_spend {
    type: number
    value_format_name: usd_2
    sql: ${promote_4_adcolony_check.sum_spend} - ${sum_rev} ;;
  }
```

## SAFE_DIVIDE
```
  measure: diff_spend_p {
    type: number
    value_format_name: percent_1
    sql: 1 - SAFE_DIVIDE(${promote_4_adcolony_check.sum_spend}, ${sum_rev}) ;;
  }
```

## ABS
```
  measure: abs_diff_spend {
    type: number
    value_format_name: usd_2
    sql: ABS(${diff_spend}) ;;
  }
```

## SAFE_DIVIDE
```
  measure: total_end_rate {
    type: number
    group_label: "rate"
    value_format_name: percent_1
    sql: SAFE_DIVIDE(${total_end_rate}, ${begin})  ;;
  }
```

## CASE Syntax
```
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    WHEN conditionN THEN resultN
    ELSE result
END;
```
```
  dimension: quest {
    type: number
    group_label: "quest"
    sql: CASE WHEN ${event_name} = "quest_end" AND ${v3} = '3' THEN 1 ELSE 0 END ;;
  }
``` 
## case_sensitive
```
  dimension: field_name {
    case_sensitive: no
  }
```