---
layout: post
title: 解决tidb有关执行计划的一个简单入门issue
category: 技术
tags: Jekyll
description: 记录提交到tidb的第一个pr
---

### 整体过程
1. 学会提交pr
2. fork+clone代码，进行代码分析
3. 修改代码进行调试验证
4. 提交代码，发起pr

#### 代码分析
本[issue](https://github.com/pingcap/tidb/issues/15387)目的是将"There are multiple NO_INDEX_MERGE hints, only the last one will take effect"的报错明确到具体哪个hint会生效
此类报错包含在tidb/planner/optimizer.go的handleStmtHints函数中

handleStmtHints函数被同一个go文件中的Optimize函数引用了3次
￼￼55行： 	stmtHints, warns := handleStmtHints(tableHints)
93行： 			stmtHints, warns = handleStmtHints(binding.Hint.GetFirstTableHints())
107行： 		curStmtHints, curWarns := handleStmtHints(binding.Hint.GetFirstTableHints())

Optimize函数被引用的次数就比较多了，包括：
1. executor/adapter.go
2. executor/compiler.go
3. executor/executor_test.go
4. executor/metrics_reader_test.go
5. executor/prepared.go
6. planner/core/cbo_test.go
7. planner/core/physical_plan_test.go
8. planner/core/point_get_plan_test.go


