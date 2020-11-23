+++
title="bigquery 实战（四）| looker sql"
tags=["bigquery","looker"]
categories=["bigquery","looker"]
date="2020-11-19T00:00:00+08:00"
+++

  measure: total {
    type: number
    label: "总数"
    value_format_name: usd_2
    sql: ANY_VALUE(coalesce(${TABLE}.sum_spend, 0));;
  }


  measure: diff_spend {
    type: number
    value_format_name: usd_2
    sql: ${promote_4_adcolony_check.sum_spend} - ${sum_rev} ;;
  }

  measure: diff_spend_p {
    type: number
    value_format_name: percent_1
    sql: 1 - SAFE_DIVIDE(${promote_4_adcolony_check.sum_spend}, ${sum_rev}) ;;
  }

  measure: abs_diff_spend {
    type: number
    value_format_name: usd_2
    sql: ABS(${diff_spend}) ;;
  }

  measure: total_end_rate {
    type: number
    group_label: "rate"
    value_format_name: percent_1
    sql: SAFE_DIVIDE(${total_end_rate}, ${begin})  ;;
  }

  dimension: quest_end_stage3_d {
    type: number
    group_label: "quest"
    sql: CASE WHEN ${event_name} = "quest_end" AND ${v3} = '3' THEN 1 ELSE 0 END ;;
  }