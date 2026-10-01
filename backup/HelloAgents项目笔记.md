<html>
<body>
<!--StartFragment--><!DOCTYPE html><h1 cid="n0" mdtype="heading" class="md-end-block md-heading md-focus" style="box-sizing: border-box; white-space: pre-wrap; break-after: avoid-page; break-inside: avoid; orphans: 4; font-size: 2.5rem; margin: 2em 0px 1.5rem; font-family: &quot;Lucida Grande&quot;, Corbel, sans-serif; font-weight: normal; clear: both; overflow-wrap: break-word; padding: 0px; color: rgb(222, 222, 222); line-height: 2.75rem; letter-spacing: -1.5px; position: relative; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain md-expand" style="box-sizing: border-box;">第一章：初识智能体</span></h1><blockquote cid="n864" mdtype="blockquote" style="box-sizing: border-box; margin: 35px 0px 1.875rem 1.875rem; border-left: 2px solid rgb(71, 77, 84); padding-left: 30px; color: rgb(157, 162, 166); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; white-space: normal; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><p cid="n4" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 4; margin-top: 0px; margin-bottom: 0px; overflow-wrap: break-word; white-space: pre-wrap; position: relative;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">参考：</span><span md-inline="link" class="md-meta-i-c  md-link" style="box-sizing: border-box;"><a href="https://datawhalechina.github.io/hello-agents/#/./chapter1/%E7%AC%AC%E4%B8%80%E7%AB%A0%20%E5%88%9D%E8%AF%86%E6%99%BA%E8%83%BD%E4%BD%93" style="box-sizing: border-box; cursor: pointer; text-decoration: underline; outline: 0px; transition: 0.2s ease-in-out; color: rgb(224, 224, 224); -webkit-user-drag: none;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">第一章在线文档</span></a></span><span md-inline="plain" class="md-plain" style="box-sizing: border-box;"> ·</span></p></blockquote><h2 cid="n5" mdtype="heading" class="md-end-block md-heading" style="box-sizing: border-box; white-space: pre-wrap; break-after: avoid-page; break-inside: avoid; orphans: 4; font-size: 1.63rem; margin: 0px 0px 1.5rem; font-family: &quot;Lucida Grande&quot;, Corbel, sans-serif; font-weight: bold; clear: both; overflow-wrap: break-word; padding: 0px; color: rgb(222, 222, 222); line-height: 1.875rem; letter-spacing: -1px; position: relative; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">1.1 什么是智能体</span></h2><h3 cid="n6" mdtype="heading" class="md-end-block md-heading" style="box-sizing: border-box; white-space: pre-wrap; break-after: avoid-page; break-inside: avoid; orphans: 4; font-size: 1.17rem; margin: 0px 0px 1.5rem; font-family: &quot;Lucida Grande&quot;, Corbel, sans-serif; font-weight: bold; clear: both; overflow-wrap: break-word; padding: 0px; color: rgb(222, 222, 222); line-height: 1.5rem; letter-spacing: -1px; position: relative; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">定义：感知环境，并为目标采取行动</span></h3><p cid="n7" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 4; margin-top: 0px; margin-bottom: 1.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">智能体（Agent）是能够通过传感器获取环境信息，并通过执行器采取行动，以实现某个目标的实体。可以把它概括成四个要素：</span></p><figure class="md-table-fig table-figure" cid="n8" mdtype="table" style="box-sizing: border-box; margin: 1.2em 0px; overflow-x: auto; max-width: calc(100% + 16px); padding: 0px; cursor: default; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; white-space: normal; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;">
要素 | 含义 | 旅行助手示例
-- | -- | --
环境 | 智能体所处的外部世界 | 用户、天气服务、景点信息
传感器 | 获取环境信息的渠道 | 用户输入、天气 API、搜索 API
执行器 | 对外部环境采取行动的方式 | 发起 API 请求、调用搜索工具、向用户回复
目标 | 衡量行动是否有用的任务要求 | 结合天气推荐合适景点

</figure><p cid="n388" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 4; margin-top: 0px; margin-bottom: 1.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="strong" class="md-pair-s " style="box-sizing: border-box;"><strong style="box-sizing: border-box; font-weight: bold; color: rgb(222, 222, 222);"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">适用场景</span></strong></span><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">：规则清晰、合规要求严格、每一步都能预先规定的退款判断适合以 Workflow 为主；材料复杂、表达不统一、需要综合订单与商品状态的部分，可以由 Agent 协助分析。</span></p><p cid="n389" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 4; margin-top: 0px; margin-bottom: 1.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="strong" class="md-pair-s " style="box-sizing: border-box;"><strong style="box-sizing: border-box; font-weight: bold; color: rgb(222, 222, 222);"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">方案 C：混合式流程</span></strong></span><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">。先由 Workflow 执行明确的硬规则（例如商品类别、申请期限、金额阈值）；Agent 负责从用户描述和订单材料中提取信息、总结理由、识别异常并给出建议；Workflow 再按政策决定自动处理或转人工。Agent 不应绕过退款硬规则。对高金额或证据不足的申请，可要求人工审批并记录每一步依据。</span></p><p cid="n390" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 4; margin-top: 0px; margin-bottom: 1.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">因此，如果我是负责人，会采用 Workflow 管住政策和审批边界，让 Agent 处理语义理解和复杂材料，并保留人工处理例外情况。比起让 Agent 独立批准所有退款，这种组合更容易兼顾灵活性和可控性。</span></p><h3 cid="n391" mdtype="heading" class="md-end-block md-heading" style="box-sizing: border-box; white-space: pre-wrap; break-after: avoid-page; break-inside: avoid; orphans: 4; font-size: 1.17rem; margin: 0px 0px 1.5rem; font-family: &quot;Lucida Grande&quot;, Corbel, sans-serif; font-weight: bold; clear: both; overflow-wrap: break-word; padding: 0px; color: rgb(222, 222, 222); line-height: 1.5rem; letter-spacing: -1px; position: relative; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">4. 为旅行助手增加记忆、备选推荐与反思</span></h3><h4 cid="n392" mdtype="heading" class="md-end-block md-heading" style="box-sizing: border-box; white-space: pre-wrap; break-after: avoid-page; break-inside: avoid; orphans: 4; font-size: 1.12rem; margin: 0px 0px 1.5rem; font-family: &quot;Lucida Grande&quot;, Corbel, sans-serif; font-weight: normal; clear: both; overflow-wrap: break-word; padding: 0px; color: white; line-height: 1.375rem; position: relative; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">记住用户偏好</span></h4><p cid="n393" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 4; margin-top: 0px; margin-bottom: 1.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">把偏好写进 system prompt 可以在一次运行中作为上下文提供，但 system prompt 主要是固定角色和规则，不适合独自承担跨会话记忆。更合适的做法是把用户明确表达的偏好保存为结构化状态，例如景点类型、预算、步行接受程度；每轮从状态中取出当前任务相关的信息，作为上下文交给 LLM。用户偏好改变时更新记录，并让用户能更正或删除。</span></p><h4 cid="n394" mdtype="heading" class="md-end-block md-heading" style="box-sizing: border-box; white-space: pre-wrap; break-after: avoid-page; break-inside: avoid; orphans: 4; font-size: 1.12rem; margin: 0px 0px 1.5rem; font-family: &quot;Lucida Grande&quot;, Corbel, sans-serif; font-weight: normal; clear: both; overflow-wrap: break-word; padding: 0px; color: white; line-height: 1.375rem; position: relative; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">景点售罄时推荐备选</span></h4><p cid="n395" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 4; margin-top: 0px; margin-bottom: 1.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">增加查询门票/开放状态的工具。若 Observation 返回“售罄”，将该结果和已推荐景点记入历史；下一轮让 Agent 根据天气、偏好和预算选择未售罄的备选项。为搜索设置重试上限；若没有可用选项，就清楚说明情况并询问用户是否调整条件。</span></p><h4 cid="n396" mdtype="heading" class="md-end-block md-heading" style="box-sizing: border-box; white-space: pre-wrap; break-after: avoid-page; break-inside: avoid; orphans: 4; font-size: 1.12rem; margin: 0px 0px 1.5rem; font-family: &quot;Lucida Grande&quot;, Corbel, sans-serif; font-weight: normal; clear: both; overflow-wrap: break-word; padding: 0px; color: white; line-height: 1.375rem; position: relative; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">连续拒绝三次后调整策略</span></h4><p cid="n397" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 4; margin-top: 0px; margin-bottom: 1.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">在状态中记录拒绝次数、已经推荐过的景点，以及用户给出的拒绝原因。每次拒绝都作为新的 Observation 返回；累计三次后触发重新规划：总结已知偏好与被拒绝原因，调整推荐条件，必要时先向用户询问更明确的偏好。记录已拒绝的候选，避免原样重复推荐，并继续保留最大循环次数作为停止条件。</span></p><p cid="n398" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 4; margin-top: 0px; margin-bottom: 1.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">这三个功能都沿用同一个改法：把新信息放进状态或 Observation，让下一轮决策能够利用它；需要持久保留的偏好单独存储，不依赖模型“自动记住”。</span></p><h3 cid="n399" mdtype="heading" class="md-end-block md-heading" style="box-sizing: border-box; white-space: pre-wrap; break-after: avoid-page; break-inside: avoid; orphans: 4; font-size: 1.17rem; margin: 0px 0px 1.5rem; font-family: &quot;Lucida Grande&quot;, Corbel, sans-serif; font-weight: bold; clear: both; overflow-wrap: break-word; padding: 0px; color: rgb(222, 222, 222); line-height: 1.5rem; letter-spacing: -1px; position: relative; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">5. 用医疗分诊助手说明系统 1 与系统 2</span></h3><p cid="n400" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 4; margin-top: 0px; margin-bottom: 1.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">可以设计一个面向健身用户的健康分诊助手，帮助识别运动风险并给出安全建议。系统 1/系统 2 是理解快慢决策的类比，不代表程序里真的有两个人类式思维系统。</span></p><ul class="ul-list" cid="n401" mdtype="list" data-mark="-" style="box-sizing: border-box; margin-top: 0px; margin-bottom: 1.5rem; padding: 0px 0px 0px 1.875rem; list-style: square; position: relative; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; white-space: normal; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><li class="md-list-item" cid="n402" mdtype="list_item" style="box-sizing: border-box; margin: 0px; position: relative;"><p cid="n403" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 1; margin: 0px 0px 0.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative;"><span md-inline="strong" class="md-pair-s " style="box-sizing: border-box;"><strong style="box-sizing: border-box; font-weight: bold; color: rgb(222, 222, 222);"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">系统 1：快速筛查。</span></strong></span><span md-inline="plain" class="md-plain" style="box-sizing: border-box;"> 迅速读取心率、症状和用户描述，识别明显异常模式；例如出现胸痛、晕厥等危险信号，或生理指标超过医生设定的安全阈值时，立即建议停止运动并寻求专业帮助。模式识别可由模型辅助，明确的安全阈值和禁行动作应由规则约束。</span></p></li><li class="md-list-item" cid="n404" mdtype="list_item" style="box-sizing: border-box; margin: 0px; position: relative;"><p cid="n405" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 1; margin: 0px 0px 0.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative;"><span md-inline="strong" class="md-pair-s " style="box-sizing: border-box;"><strong style="box-sizing: border-box; font-weight: bold; color: rgb(222, 222, 222);"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">系统 2：谨慎分析。</span></strong></span><span md-inline="plain" class="md-plain" style="box-sizing: border-box;"> 对非紧急情形综合用户病史、药物、近期训练、恢复情况和目标，检索可靠资料，比较多种解释，评估不确定性，再调整训练建议或转交专业人员。</span></p></li><li class="md-list-item" cid="n406" mdtype="list_item" style="box-sizing: border-box; margin: 0px; position: relative;"><p cid="n407" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 1; margin: 0px 0px 0.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative;"><span md-inline="strong" class="md-pair-s " style="box-sizing: border-box;"><strong style="box-sizing: border-box; font-weight: bold; color: rgb(222, 222, 222);"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">协作方式：</span></strong></span><span md-inline="plain" class="md-plain" style="box-sizing: border-box;"> 系统 1 负责快速发现风险并触发安全措施；系统 2 对复杂情况补充上下文、核对证据并决定下一步；遇到高风险或证据不足时由人工专业人员接手。快速筛查结果和后续反馈可用于改进识别与流程。</span></p></li></ul><p cid="n408" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 4; margin-top: 0px; margin-bottom: 1.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">这个例子体现神经符号结合的思路：神经模型擅长从复杂信号中识别模式，符号规则擅长表达阈值、禁忌和必须遵守的约束，两者配合能够兼顾灵活识别与明确控制。</span></p><h3 cid="n409" mdtype="heading" class="md-end-block md-heading" style="box-sizing: border-box; white-space: pre-wrap; break-after: avoid-page; break-inside: avoid; orphans: 4; font-size: 1.17rem; margin: 0px 0px 1.5rem; font-family: &quot;Lucida Grande&quot;, Corbel, sans-serif; font-weight: bold; clear: both; overflow-wrap: break-word; padding: 0px; color: rgb(222, 222, 222); line-height: 1.5rem; letter-spacing: -1px; position: relative; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">6. LLM 智能体的局限与评估</span></h3><h4 cid="n410" mdtype="heading" class="md-end-block md-heading" style="box-sizing: border-box; white-space: pre-wrap; break-after: avoid-page; break-inside: avoid; orphans: 4; font-size: 1.12rem; margin: 0px 0px 1.5rem; font-family: &quot;Lucida Grande&quot;, Corbel, sans-serif; font-weight: normal; clear: both; overflow-wrap: break-word; padding: 0px; color: white; line-height: 1.375rem; position: relative; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">为什么会产生幻觉</span></h4><p cid="n411" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 4; margin-top: 0px; margin-bottom: 1.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">LLM 根据上下文生成可能合适的后续文本，语言流畅不等于事实正确。当知识缺失、问题含糊、上下文过长或信息过时时，模型可能用看似合理的内容填补空白。Agent 还会受到工具返回错误、检索遗漏、参数错误和多步推理误差的影响；前一步的错误观察可能被后续步骤继续放大。</span></p><p cid="n412" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 4; margin-top: 0px; margin-bottom: 1.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">降低风险的方法包括：让需要实时性的结论依赖可核验的工具结果；检查来源和参数；明确表达不确定性；对重要决定设置规则校验或人工复核。工具调用本身也需要验证，不能把“用了工具”当作“结果必然正确”。</span></p><h4 cid="n413" mdtype="heading" class="md-end-block md-heading" style="box-sizing: border-box; white-space: pre-wrap; break-after: avoid-page; break-inside: avoid; orphans: 4; font-size: 1.12rem; margin: 0px 0px 1.5rem; font-family: &quot;Lucida Grande&quot;, Corbel, sans-serif; font-weight: normal; clear: both; overflow-wrap: break-word; padding: 0px; color: white; line-height: 1.375rem; position: relative; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">没有最大循环次数会怎样</span></h4><p cid="n414" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 4; margin-top: 0px; margin-bottom: 1.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">如果 Agent 无法判断任务已经结束，它可能反复调用同一工具、重复搜索、在两个行动之间来回循环，造成时间、Token 和 API 费用持续增加。若工具会产生副作用（例如下单、退款、发邮件），重复调用还可能造成重复操作。</span></p><p cid="n415" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 4; margin-top: 0px; margin-bottom: 1.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">最大轮数提供一个简单停止边界。实际系统还可结合总运行时间、费用预算、重复 Action 检测、工具调用权限和人工接管条件。</span></p><h4 cid="n416" mdtype="heading" class="md-end-block md-heading" style="box-sizing: border-box; white-space: pre-wrap; break-after: avoid-page; break-inside: avoid; orphans: 4; font-size: 1.12rem; margin: 0px 0px 1.5rem; font-family: &quot;Lucida Grande&quot;, Corbel, sans-serif; font-weight: normal; clear: both; overflow-wrap: break-word; padding: 0px; color: white; line-height: 1.375rem; position: relative; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">如何评价智能体</span></h4><p cid="n417" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 4; margin-top: 0px; margin-bottom: 1.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">只看准确率不够。Agent 不只是给出答案，也要决定行动、正确使用工具并安全完成任务。可以组合评估：</span></p><ul class="ul-list" cid="n418" mdtype="list" data-mark="-" style="box-sizing: border-box; margin-top: 0px; margin-bottom: 1.5rem; padding: 0px 0px 0px 1.875rem; list-style: square; position: relative; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; white-space: normal; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><li class="md-list-item" cid="n419" mdtype="list_item" style="box-sizing: border-box; margin: 0px; position: relative;"><p cid="n420" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 1; margin: 0px 0px 0.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative;"><span md-inline="strong" class="md-pair-s " style="box-sizing: border-box;"><strong style="box-sizing: border-box; font-weight: bold; color: rgb(222, 222, 222);"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">任务成功率</span></strong></span><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">：是否达到用户目标，关键步骤是否完成。</span></p></li><li class="md-list-item" cid="n421" mdtype="list_item" style="box-sizing: border-box; margin: 0px; position: relative;"><p cid="n422" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 1; margin: 0px 0px 0.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative;"><span md-inline="strong" class="md-pair-s " style="box-sizing: border-box;"><strong style="box-sizing: border-box; font-weight: bold; color: rgb(222, 222, 222);"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">事实质量</span></strong></span><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">：回答是否准确，是否有来源支持，是否能表达不确定性。</span></p></li><li class="md-list-item" cid="n423" mdtype="list_item" style="box-sizing: border-box; margin: 0px; position: relative;"><p cid="n424" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 1; margin: 0px 0px 0.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative;"><span md-inline="strong" class="md-pair-s " style="box-sizing: border-box;"><strong style="box-sizing: border-box; font-weight: bold; color: rgb(222, 222, 222);"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">工具能力</span></strong></span><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">：是否选对工具、参数是否正确、是否正确使用返回结果。</span></p></li><li class="md-list-item" cid="n425" mdtype="list_item" style="box-sizing: border-box; margin: 0px; position: relative;"><p cid="n426" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 1; margin: 0px 0px 0.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative;"><span md-inline="strong" class="md-pair-s " style="box-sizing: border-box;"><strong style="box-sizing: border-box; font-weight: bold; color: rgb(222, 222, 222);"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">鲁棒性</span></strong></span><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">：输入换一种说法、数据缺失或工具失败时，能否妥善恢复。</span></p></li><li class="md-list-item" cid="n427" mdtype="list_item" style="box-sizing: border-box; margin: 0px; position: relative;"><p cid="n428" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 1; margin: 0px 0px 0.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative;"><span md-inline="strong" class="md-pair-s " style="box-sizing: border-box;"><strong style="box-sizing: border-box; font-weight: bold; color: rgb(222, 222, 222);"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">安全与约束</span></strong></span><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">：是否遵守政策、权限和停止条件，有无不当副作用。</span></p></li><li class="md-list-item" cid="n429" mdtype="list_item" style="box-sizing: border-box; margin: 0px; position: relative;"><p cid="n430" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 1; margin: 0px 0px 0.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative;"><span md-inline="strong" class="md-pair-s " style="box-sizing: border-box;"><strong style="box-sizing: border-box; font-weight: bold; color: rgb(222, 222, 222);"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">效率与体验</span></strong></span><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">：延迟、Token/API 成本、交互轮数，以及用户是否能理解并接受结果。</span></p></li></ul><p cid="n431" mdtype="paragraph" class="md-end-block md-p" style="box-sizing: border-box; line-height: inherit; orphans: 4; margin-top: 0px; margin-bottom: 1.5rem; overflow-wrap: break-word; white-space: pre-wrap; position: relative; color: rgb(184, 191, 198); font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, &quot;Segoe UI Emoji&quot;, &quot;SF Pro&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span md-inline="plain" class="md-plain" style="box-sizing: border-box;">适合为不同任务准备代表性测试案例，既看最终结果，也检查中间行动轨迹。单一指标可能掩盖“答案正确但工具调用危险”或“平均准确但遇到边界情形就失败”等问题。</span></p><br class="Apple-interchange-newline"><!--EndFragment-->
</body>
</html># 第一章：初识智能体

> 参考：[[第一章在线文档](https://datawhalechina.github.io/hello-agents/#/./chapter1/%E7%AC%AC%E4%B8%80%E7%AB%A0%20%E5%88%9D%E8%AF%86%E6%99%BA%E8%83%BD%E4%BD%93)](https://datawhalechina.github.io/hello-agents/#/./chapter1/%E7%AC%AC%E4%B8%80%E7%AB%A0%20%E5%88%9D%E8%AF%86%E6%99%BA%E8%83%BD%E4%BD%93) ·

## 1.1 什么是智能体

### 定义：感知环境，并为目标采取行动

智能体（Agent）是能够通过传感器获取环境信息，并通过执行器采取行动，以实现某个目标的实体。可以把它概括成四个要素：

| 要素   | 含义                       | 旅行助手示例                            |
| ------ | -------------------------- | --------------------------------------- |
| 环境   | 智能体所处的外部世界       | 用户、天气服务、景点信息                |
| 传感器 | 获取环境信息的渠道         | 用户输入、天气 API、搜索 API            |
| 执行器 | 对外部环境采取行动的方式   | 发起 API 请求、调用搜索工具、向用户回复 |
| 目标   | 衡量行动是否有用的任务要求 | 结合天气推荐合适景点                    |

智能体的关键不在于“会说话”，而在于能围绕目标，根据当前信息选择下一步行动，并利用行动结果继续决策。

### 传统智能体的演进

传统智能体的几类典型架构，可以看作决策能力逐步扩展的过程：

| 类型             | 决策方式                        | 例子与局限                                       |
| ---------------- | ------------------------------- | ------------------------------------------------ |
| 简单反射型       | 根据当前输入匹配“条件—动作”规则 | 恒温器检测到温度高于设定值就制冷；不保留历史状态 |
| 基于模型的反射型 | 结合内部状态或世界模型判断情况  | 汽车可以根据先前信息估计暂时看不见的车辆位置     |
| 基于目标型       | 选择能把当前状态带向目标的行动  | 导航系统搜索一条抵达目的地的路线                 |
| 基于效用型       | 比较多个方案，并优化综合满意度  | 同时权衡耗时、距离、油耗和拥堵                   |
| 学习型           | 根据行动结果和反馈调整策略      | 下棋系统通过大量对弈改进策略                     |

这条演进路线说明，智能体从即时规则响应，逐渐发展出状态建模、目标规划、多目标权衡和从经验中学习等能力。

### LLM 驱动的智能体

大语言模型（LLM）为智能体提供了通用的语言理解和决策能力。用户可以用自然语言提出较高层级的目标，LLM 再把目标分解成步骤、选择工具，并根据工具返回的信息调整后续行动。

例如，面对“根据今天北京的天气推荐一个景点”，LLM 可以先判断需要查询天气；得到天气结果后，再决定查询适合当日天气的景点，最后组织答案。**模型承担推理和决策，外部工具则提供实时信息或执行操作**。

### 智能体的其他分类视角

除了按内部决策架构分类，还可以从两个角度理解智能体：

1. **按决策时间与反应性**
   - 反应式：响应快、延迟低，适合必须快速处理的情况，但规划较少。
   - 规划式：行动前考虑多个步骤，能处理复杂目标，但需要更多时间和计算。
   - 混合式：结合快速反应与较长程规划。LLM 智能体常通过多轮“**思考—行动—观察**”把大任务拆成一连串小决策。

2. **按知识表示方式**
   - 亚符号主义：知识以神经网络中学到的模式为主，擅长从数据中识别规律。
   - 符号主义：通过明确的符号、规则和逻辑表示知识，推理过程较容易追踪。
   - 神经符号主义：尝试结合神经网络的模式识别能力与符号系统的结构化推理能力。

这些角度不是互相排斥的标签，而是帮助设计者分析一个系统如何决策、擅长什么，以及需要承担什么代价。

## 1.2 智能体的任务环境与运行原理

### 用 PEAS 描述任务环境

PEAS 用四个维度描述智能体要完成的任务：

- **P — Performance（性能度量）**：怎样算任务完成得好。
- **E — Environment（环境）**：智能体面对的世界。
- **A — Actuators（执行器）**：智能体能采取哪些行动。
- **S — Sensors（传感器）**：智能体能获取的信息。

对于旅行助手，可以这样理解：

| PEAS 维度 | 旅行助手中的内容                                   |
| --------- | -------------------------------------------------- |
| 性能度量  | 推荐有依据、符合天气和用户需求，信息准确且表达清楚 |
| 环境      | 用户、天气数据、景点信息和搜索服务                 |
| 执行器    | 查询天气、搜索景点、向用户提供建议                 |
| 传感器    | 用户提出的问题、天气 API 结果、搜索结果            |

PEAS 的价值在于提醒我们：设计智能体时要同时想清楚任务目标、可用信息来源、可执行动作和评价标准。

### 环境会影响智能体设计

实际环境通常具有以下特点：

- **部分可观察**：智能体每次只能获得环境的一部分信息，例如一个天气接口只返回特定地点和时刻的数据。
- **不确定性**：搜索结果或实时数据会变化，重复调用可能得到不同结果。
- **动态性与序贯性**：环境会持续变化；当前行动和结果会影响后续步骤。
- **多行动者**：用户、其他服务或其他系统的行为也可能改变环境状态。

因此，**智能体不能只在任务开始时做一次判断。它需要利用每步行动带回的反馈更新对环境的理解。**

### Agent Loop：感知—思考—行动—观察

文档把智能体的运行过程描述为持续循环：

~~~mermaid
flowchart LR
    A[感知：接收用户请求或环境反馈] --> B[思考：理解目标、规划下一步]
    B --> C[工具选择：确定工具和参数]
    C --> D[行动：调用工具或服务]
    D --> E[环境产生结果]
    E --> F[观察：整理并记录结果]
    F --> A
    B --> G{任务已完成？}
    G -->|是| H[生成最终答复]
    G -->|否| C
~~~

其中：

- **Perception（感知）**：接收用户请求，或接收上一步工具调用的返回结果。
- **Thought（思考）**：理解目前进展，拆解任务，并选择下一步工具。
- **Action（行动）**：实际执行工具调用，对环境产生影响。
- **Observation（观察）**：把工具返回值整理成下一轮可使用的信息。

在代码中，模型通常生成结构化的行动文本；外部解析器读取其中的 Action 并调用对应 Python 函数。工具执行后的结果被整理成 Observation，再反馈给模型。这样，LLM 的语言决策能力和 Python 的执行能力就连成了闭环。

## 1.3 动手体验：智能旅行助手

### 任务目标

示例要求助手先查询北京当天的天气，再根据天气推荐景点。这个任务包含两个有先后依赖的子任务：

1. 查询天气。
2. 根据天气搜索景点并总结建议。

第二步要用到第一步的结果，因此不能把两步当成彼此无关的操作。这正适合展示智能体如何根据反馈连续决策。

### 程序的组件

#### 1. 指令模板：规定角色、工具和输出格式

系统提示词（system prompt）相当于智能体的操作说明。示例告诉模型：

- 它是一个旅行助手。
- 可用工具包括天气查询和景点搜索，并说明每个工具的参数。
- 每轮回答都要包含 Thought 和 Action。
- Action 可以是某个工具调用，也可以是表示完成任务的 Finish。

这个约定把模型的自由文本限制成程序比较容易处理的形式。提示词只负责告诉模型工具的名字和用法；Python 函数仍由程序本身保存和执行。

#### 2. 天气工具：调用天气服务并整理结果

天气函数通过 HTTP 库请求天气服务，解析返回的 JSON，提取天气描述和温度，并整理成简短文字。它还处理了网络异常、返回数据缺字段等情况。

这一步体现了工具函数的基本职责：**对接外部服务、整理机器数据、返回可供模型使用的结果。**

#### 3. 景点工具：调用搜索服务

景点函数把城市和天气组合成搜索问题，调用 Tavily 搜索 API，并将搜索摘要或结果整理成文字返回。它需要 Tavily API 密钥。

天气服务、景点搜索服务和 LLM 是不同的服务，承担不同职责：天气服务返回天气；搜索服务查找景点信息；LLM 负责决定何时调用工具，以及怎样使用工具结果。

#### 4. 工具注册表：把名称映射到 Python 函数

程序把工具名称和函数对象放进一个字典。例如，模型请求调用 <code>get_weather</code>，程序就从字典中找到对应函数。这个注册表也是工具白名单：只有登记过的工具能由主循环调用。

#### 5. LLM 客户端：向模型发送上下文

示例封装了一个兼容 OpenAI 风格接口的客户端。调用时需要提供模型名称、API 密钥和服务地址。客户端把 system prompt 与当前 prompt 组成消息，发送请求，再从响应中取出模型生成的文字。

其中，prompt 不只包含最初的用户请求，也会在后续轮次包含已经发生的 Thought、Action 和 Observation。模型由此能够根据前面收集到的信息继续处理任务。

### 主循环如何连接所有部分

主循环的逻辑可以概括为：

~~~text
保存用户请求
最多重复 5 轮：
    把系统指令和历史交给 LLM
    取得模型生成的 Thought 和 Action
    如果 Action 表示 Finish：
        输出最终答案并结束
    否则：
        解析工具名称和参数
        从工具注册表中找到函数并执行
        把工具结果记为 Observation
        将模型输出和 Observation 加入历史
~~~

<code>range(5)</code> 设置了循环上限，模型可以通过 Finish 提前结束。这个上限是保护措施：当模型没能按预期结束时，程序不会无限调用模型。

### 跟着运行示例走一遍

| 轮次    | 模型的决定     | Python 的动作             | 反馈                   |
| ------- | -------------- | ------------------------- | ---------------------- |
| 第 1 轮 | 先查天气       | 调用天气函数              | 返回天气描述和温度     |
| 第 2 轮 | 根据天气查景点 | 调用景点搜索函数          | 返回搜索摘要或景点信息 |
| 第 3 轮 | 已获得足够信息 | 不再调用工具，返回 Finish | 输出最终推荐           |

从这个流程可以看到模型和 Python 各自承担的职责：模型生成“下一步做什么”的决策；Python 解析决策并执行注册好的函数；工具把环境信息带回来；模型再结合这些新信息做下一步判断。

### 这个小例子展示了什么

1. **任务分解**：把一个整体目标拆成天气查询和景点推荐。
2. **工具选择与调用**：根据任务进度选择不同工具。
3. **上下文利用**：把工具结果写入历史，让后续决策有依据。
4. **结果整合**：收集信息后，由模型形成面向用户的最终答复。

### 阅读代码时需要留意的细节

- 文档中的 API 密钥、服务地址和模型名称都是占位值，运行前要按实际服务配置。密钥应保存在环境变量等配置中，避免提交到公开代码仓库。
- 景点搜索依赖 Tavily 凭证。示例中密钥占位写法出现了重复赋值；实际使用时要确保环境变量里放入真实密钥，而不是占位字符串。
- 示例通过正则表达式解析模型生成的工具调用文本，适合展示原理，但对格式变化比较敏感。若模型少写字段、改变引号或多输出内容，解析可能失败。
- 示例只允许注册表中的工具被调用，并设置最多五轮循环；这些约束能让这个入门程序比较容易理解和控制。
- 文档运行日志中的天气和搜索结果是示例输出。实际结果会随时间、服务返回内容和模型输出而变化。

## 小结

1.1 建立了智能体的定义和分类视角；1.2 介绍了如何描述任务环境，以及智能体如何通过循环与环境交互；1.3 把这些概念落实成一个能调用天气与搜索工具的旅行助手。

理解本章最有用的一句话是：**LLM 根据目标和当前上下文决定下一步，程序负责执行工具并把结果反馈给 LLM。**智能体正是通过这种循环，把自然语言目标逐步转化为可执行的行动和最终结果。

## 1.4 FirstAgentTest.py

下面按照程序实际执行的顺序，梳理这份脚本如何运行。

### 代码内容

```python
import requests
# 系统提示词
AGENT_SYSTEM_PROMPT = """
你是一个智能旅行助手。你的任务是分析用户的请求，并使用可用工具一步步地解决问题。

# 可用工具:
- `get_weather(city: str)`: 查询指定城市的实时天气。
- `get_attraction(city: str, weather: str)`: 根据城市和天气搜索推荐的旅游景点。

# 输出格式要求:
你的每次回复必须严格遵循以下格式，包含一对Thought和Action：

Thought: [你的思考过程和下一步计划]
Action: [你要执行的具体行动]

Action的格式必须是以下之一：
1. 调用工具：function_name(arg_name="arg_value")
2. 结束任务：Finish[最终答案]

# 重要提示:
- 每次只输出一对Thought-Action
- Action必须在同一行，不要换行
- 当收集到足够信息可以回答用户问题时，必须使用 Action: Finish[最终答案] 格式结束

请开始吧！
"""


# 请求天气状况
def get_weather(city: str) ->str:
    url = f"http://wttr.in/{city}?format=j1"
    
    try:
        # 发起request
        response = requests.get(url)
        # 检查请求是否成功
        response.raise_for_status()
        # 解析JSON数据
        data = response.json()
                
        # 提取天气信息
        current_condition = data['current_condition'][0]
        weather_desc = current_condition['weatherDesc'][0]['value']
        temp_c = current_condition['temp_C']
        
        # 返回天气信息
        return f"{city}的当前天气: {weather_desc}, 温度: {temp_c}°C"
    except requests.exceptions.RequestException as e:
        return f"错误：网络请求超时: {e}"
    except (KeyError, IndexError) as e:
        return f"错误：无法解析天气数据，可能是城市名称无效: {e}"


import os
from tavily import TavilyClient

# 景点搜索服务
def get_attraction(city: str, weather: str)->str:
    # 使用Tavily Search API获取景点信息
        
    # 读取API Key
    api_key = os.getenv("TAVILY_API_KEY")
    if not api_key:
        return "错误：未设置TAVILY_API_KEY环境变量"
    
    # 创建Tavily客户端
    tavily_client = TavilyClient(api_key=api_key)
    # 构建查询
    query = f"在'{city}'在'{weather}'天气下最值得去的旅游景点推荐及理由"
    
    try:
        # 调用API, include_answers=True会返回一个综合性的回答
        response = tavily_client.search(query=query, search_depth="basic",include_answers=True)
        
        if response.get("answer"):
            return response["answer"]
        
        formatted_results = []
        for result in response.get("results", []):
            formatted_results.append(f"- {result['title']}: {result['content']}")
        
        if not formatted_results:
             return "抱歉，没有找到相关的旅游景点推荐。"

        return "根据搜索，为您找到以下信息:\n" + "\n".join(formatted_results)

    except Exception as e:
        return f"错误:执行Tavily搜索时出现问题 - {e}"
    

# 将所有工具函数放入一个字典，方便后续调用
available_tools = {
    "get_weather": get_weather,
    "get_attraction": get_attraction,
}

# 接入LLM服务的客户端
from openai import OpenAI

class OpenAICompatibleClient:
    """
    一个用于调用任何兼容OpenAI接口的LLM服务的客户端。
    """
    def __init__(self, model: str, api_key: str, base_url: str):
        self.model = model
        self.client = OpenAI(api_key=api_key, base_url=base_url)

    def generate(self, prompt: str, system_prompt: str) -> str:
        """调用LLM API来生成回应。"""
        print("正在调用大语言模型...")
        try:
            messages = [
                {'role': 'system', 'content': system_prompt},
                {'role': 'user', 'content': prompt}
            ]
            response = self.client.chat.completions.create(
                model=self.model,
                messages=messages,
                stream=False
            )
            answer = response.choices[0].message.content
            print("大语言模型响应成功。")
            return answer
        except Exception as e:
            print(f"调用LLM API时发生错误: {e}")
            return "错误:调用语言模型服务时出错。"
        
import re

# --- 1. 配置LLM客户端 ---
# 请根据您使用的服务，将这里替换成对应的凭证和地址
API_KEY = "sk-09a86d1ecdce43b0b2efdef740f6effb"
BASE_URL = "https://api.deepseek.com"
MODEL_ID = "deepseek-flash"
TAVILY_API_KEY="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"
os.environ['TAVILY_API_KEY'] = "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"

llm = OpenAICompatibleClient(
    model=MODEL_ID,
    api_key=API_KEY,
    base_url=BASE_URL
)

# --- 2. 初始化 ---
user_prompt = "我计划去北京旅游，想知道现在的天气情况，并推荐一些适合当前天气的景点。"
prompt_history = [f"用户请求: {user_prompt}"]

print(f"用户输入: {user_prompt}\n" + "="*40)

# --- 3. 运行主循环 ---
for i in range(5): # 设置最大循环次数
    print(f"--- 循环 {i+1} ---\n")
    
    # 3.1. 构建Prompt
    full_prompt = "\n".join(prompt_history)
    
    # 3.2. 调用LLM进行思考
    llm_output = llm.generate(full_prompt, system_prompt=AGENT_SYSTEM_PROMPT)
    # 模型可能会输出多余的Thought-Action，需要截断
    match = re.search(r'(Thought:.*?Action:.*?)(?=\n\s*(?:Thought:|Action:|Observation:)|\Z)', llm_output, re.DOTALL)
    if match:
        truncated = match.group(1).strip()
        if truncated != llm_output.strip():
            llm_output = truncated
            print("已截断多余的 Thought-Action 对")
    print(f"模型输出:\n{llm_output}\n")
    prompt_history.append(llm_output)
    
    # 3.3. 解析并执行行动
    action_match = re.search(r"Action: (.*)", llm_output, re.DOTALL)
    if not action_match:
        observation = "错误: 未能解析到 Action 字段。请确保你的回复严格遵循 'Thought: ... Action: ...' 的格式。"
        observation_str = f"Observation: {observation}"
        print(f"{observation_str}\n" + "="*40)
        prompt_history.append(observation_str)
        continue
    action_str = action_match.group(1).strip()

    if action_str.startswith("Finish"):
        final_answer = re.match(r"Finish\[(.*)\]", action_str).group(1)
        print(f"任务完成，最终答案: {final_answer}")
        break
    
    tool_name = re.search(r"(\w+)\(", action_str).group(1)
    args_str = re.search(r"\((.*)\)", action_str).group(1)
    kwargs = dict(re.findall(r'(\w+)="([^"]*)"', args_str))

    if tool_name in available_tools:
        observation = available_tools[tool_name](**kwargs)
    else:
        observation = f"错误:未定义的工具 '{tool_name}'"

    # 3.4. 记录观察结果
    observation_str = f"Observation: {observation}"
    print(f"{observation_str}\n" + "="*40)
    prompt_history.append(observation_str)
```

### 代码流程

1. **定义 system prompt**：告诉模型它是旅行助手，列出天气和景点两个工具，并要求每轮生成 Thought、Action；任务完成时返回 Finish。
2. **定义两个外部工具**：
   - <code>get_weather(city)</code> 请求 wttr.in，解析 JSON，提取天气描述和温度。
   - <code>get_attraction(city, weather)</code> 把城市和天气组成搜索问题，调用 Tavily，再把搜索摘要整理成文字。
3. **注册工具**：<code>available_tools</code> 把工具名映射到对应 Python 函数，后续只允许调用这个字典中登记的函数。
4. **创建 LLM 客户端**：<code>OpenAICompatibleClient</code> 使用 DeepSeek 的 OpenAI 兼容接口。每轮把 system prompt 和当前历史作为消息发给模型，并取回模型返回文本。
5. **初始化任务历史**：用户提出“了解北京当前天气并推荐适合景点”，这句话先放进 <code>prompt_history</code>。
6. **进入最多五轮的 Agent Loop**：每轮将历史拼成 prompt，调用 LLM，再解析模型输出的 Action。
7. **执行 Action**：
   - Action 是 <code>get_weather(...)</code> 或 <code>get_attraction(...)</code> 时，程序解析函数名和参数，从注册表找到函数并执行。
   - Action 以 <code>Finish[...]</code> 开头时，程序取出最终答案并结束循环。
8. **回填 Observation**：工具结果被包装成 Observation 并追加到历史。下一轮模型便能看到前一步的结果，决定继续搜索还是结束。

~~~mermaid
sequenceDiagram
    participant U as 用户
    participant L as LLM
    participant P as Python 主循环
    participant W as 天气工具
    participant T as Tavily 搜索
    U->>P: 北京天气 + 景点推荐
    P->>L: system prompt + 用户请求
    L-->>P: Action: get_weather(...)
    P->>W: 查询天气
    W-->>P: Observation: 天气描述和温度
    P->>L: 历史 + 天气观察
    L-->>P: Action: get_attraction(...)
    P->>T: 按城市和天气搜索
    T-->>P: Observation: 景点搜索结果
    P->>L: 历史 + 搜索观察
    L-->>P: Action: Finish[最终建议]
    P-->>U: 输出最终建议
~~~

最重要的分工是：**LLM 选择下一步，Python 执行工具，工具结果再交给 LLM。** 模型输出的函数调用格式只是文本；真正执行函数的是主循环中的 Python 代码。

### 小结

这份代码已经包含一个最小 Agent 的核心闭环：**用户目标 → LLM 选择工具 → Python 执行工具 → Observation 加入历史 → LLM 继续决策 → Finish 返回答案**。眼下优先修正密钥管理和 Tavily 参数，再给网络请求加超时，并为解析失败补上检查。


## 1.5：智能体应用的协作模式

本节从智能体在任务中的角色和自主程度出发，介绍两种常见协作模式：**作为人的开发工具**和**作为自主协作者**。

### 1.5.1 作为开发者工具：人主导，智能体辅助

在这种模式中，智能体被放进人的工作流程里，帮助完成代码补全、问答、重构或调试等任务。人仍负责确定目标、判断建议是否正确并决定最终结果；智能体减少重复劳动，让人把精力放在更重要的工作上。文档以 GitHub Copilot、Claude Code、Trae、Cursor 等工具为例。

可以把这种关系理解为：**人拆分并管理任务，智能体在工作流程中提供能力。**自动补全属于较小范围的辅助；能读取代码库、编辑文件、运行测试的编程助手则拥有更多行动能力，但仍处于人的开发流程中。

### 1.5.2 作为自主协作者：给目标，智能体规划与执行

在这种模式中，用户把较高层级的目标委托给智能体。智能体需要自行规划、推理、调用工具、观察结果并调整行动，直到产出成果。人与 AI 的协作重心从逐步下达操作指令，转向描述目标并委托完成。

几类实现思路：

1. **单智能体自主循环**：一个通用智能体通过思考、规划、执行和反思反复迭代，完成开放式任务。
2. **多智能体协作**：多个智能体按角色或职责配合。可以是角色扮演式对话，也可以像团队一样按分工和流程组织工作；还可以让开发者自定义智能体之间的交互方式。
3. **图结构控制流**：把执行过程表示成状态图，通过节点和分支组织循环、分支、回溯及人工介入等流程。

~~~mermaid
flowchart TD
    A[智能体应用] --> B[作为开发工具]
    A --> C[作为自主协作者]
    B --> B1[嵌入人的工作流]
    B --> B2[人负责目标和最终判断]
    C --> C1[单智能体：规划、执行、反思]
    C --> C2[多智能体：按角色或职责协作]
    C --> C3[状态图：循环、分支、回溯和人工介入]
~~~

### 1.5.3 Workflow 与 Agent 的差异

两者都能自动化任务，核心差异在于**谁决定接下来做什么**：

| 对比维度     | Workflow（工作流）             | Agent（智能体）                              |
| ------------ | ------------------------------ | -------------------------------------------- |
| 流程由谁决定 | 开发者预先编排步骤和分支       | 智能体根据目标、当前信息和工具结果选择下一步 |
| 运行方式     | 按确定流程推进                 | 通过规划和反馈动态调整行动                   |
| 优势         | 过程明确、结果较容易预测和审核 | 灵活，能处理表达模糊、步骤不完全确定的目标   |
| 适合的任务   | 规则稳定、步骤明确的流程       | 需要判断、搜集信息和逐步适应的开放任务       |
| 主要挑战     | 遇到流程外情况时需要修改编排   | 行动和结果更难预测，需要边界、校验和停止条件 |

例如，固定的报销审批规则适合由工作流明确规定条件和审批顺序；“根据天气和偏好规划一日游”则需要智能体先收集信息，再决定下一步。实际系统也可以结合两者：由 Workflow 负责稳定、必须遵守的主流程，在其中需要理解语义或动态选择工具的环节交给 Agent。这里的混合设计是根据两种范式特点得出的实践思路。

##  1.6：本章小结

第一章依次回答了四个问题：

1. **什么是智能体？** 能感知环境、围绕目标做决策并采取行动；传统智能体从规则响应发展到建模、规划、效用权衡和学习，LLM 则提供了更通用的语言理解与决策能力。
2. **智能体如何工作？** 它通过感知、思考、行动和观察反馈形成循环。对 LLM 智能体来说，Thought、Action、Observation 描述了模型决策与外部执行之间的交互。
3. **如何构建一个最小智能体？** 旅行助手把任务拆成查天气、按天气找景点、组织最终建议；LLM 选择工具，Python 执行工具，并把结果反馈给下一轮。
4. **智能体怎样与人协作？** 它可以作为人的工作工具，也可以作为接收目标后自主推进任务的协作者；Workflow 与 Agent 的取舍取决于流程是否固定、任务是否需要动态判断。

从 1.1 到 1.5，可以串成一条主线：**先定义目标与环境，再让智能体感知信息、选择行动、调用工具、接收反馈，最后根据任务需要决定由固定 Workflow 控制流程，还是让 Agent 自主规划。**


## 本章习题：参考答案与解析

### 1. 四个 case 是否属于智能体

| Case            | 判断与分类                                                   | 理由与补充                                                   |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| A：超级计算机   | 单看这台计算机硬件，不属于智能体                             | 峰值算力只表示计算能力。题目没有给出它要达成的目标、感知环境的输入、根据状态选择行动的策略和影响环境的执行器。它可以运行智能体程序，但“能运行程序”本身不等于它就是智能体。你的结论方向正确，可以把理由说得更具体。 |
| B：自动驾驶系统 | 属于智能体；可归为快速反应的反应式智能体，也可能是基于模型的反射型智能体 | 系统感知道路和障碍物，选择刹车或变道来降低风险。若它维护车辆、车道和障碍物的位置/速度等内部状态，再据此判断，就是基于模型的反射型。若题目只描述“看到障碍就触发固定动作”，则更接近简单反射型。毫秒级响应说明它需要反应快，不代表系统完全没有规划能力。 |
| C：AlphaGo      | 属于学习型智能体；同时具有目标导向和规划特征                 | 它通过训练（包括自我对弈）获得策略；对局时以赢棋为目标，评估局面并搜索后续落子。按决策时间分类，它属于规划/审议式智能体。若描述其评估局面的价值函数，也可说它具有效用导向的决策特征。你的答案抓住了“学习型”，还可补上目标和规划两个维度。 |
| D：LLM 智能客服 | 若能查询订单等工具并据此采取行动，属于 LLM 驱动的工具型智能体 | 它接收投诉，查询订单信息，把工具返回结果作为观察，再分析原因、选择解决方式并答复用户。它具有目标导向和多步决策特征。若 ChatGPT 只是扮演客服角色、没有工具或环境交互，则更像对话模型，而不是具备完整行动闭环的智能体。 |

分类时最好说明依据，而不要只贴一个标签。同一个智能体可以从架构、反应性、学习能力和目标等多个维度描述。

### 2. 智能健身教练的 PEAS 与环境特性

| PEAS             | 设计描述                                                     |
| ---------------- | ------------------------------------------------------------ |
| **P — 性能度量** | 训练建议是否符合用户目标和当前状态；训练计划执行情况与阶段性进展；是否及时识别风险并提醒休息/停止；指导是否易懂、用户是否能持续使用。避免只用“分析精确”作指标，因为传感器有误差，身体状态也不能被完整观测。 |
| **E — 环境**     | 用户的身体状态、健身目标、训练历史、作息与饮食；运动场地、器材和可用时间；可穿戴设备及其数据服务；必要时还包括天气、温度等影响运动的条件。 |
| **A — 执行器**   | 调整训练计划、动作顺序和强度；语音指导与动作纠正；建议休息、补水或停止训练；记录训练结果；发现明显风险时提醒用户并建议联系专业人员。 |
| **S — 传感器**   | 心率、运动强度、步数和加速度等穿戴设备数据；用户输入的疼痛、疲劳、目标和健康限制；训练日志、饮食记录；如果具备摄像头，还可以使用姿态/动作信息。 |

你给出的四类 PEAS 已经抓到基本方向：特别是把调整计划和动作纠正列为行动，把身体状态和动作列为观察信息。可以继续完善的是：P 应尽量描述可评价的结果；E 不只是“健身计划、饮食状况”，还包括用户、设备、器材和运动场景；S 需要覆盖获取这些信息的具体渠道。

这个任务环境具有以下性质：

- **部分可观察**：心率和动作数据只能反映身体状态的一部分；穿戴设备会有测量误差，用户也可能漏报疲劳或饮食情况。
- **随机/不确定**：相同训练对不同人的影响不同；睡眠、情绪、疾病和测量噪声都会改变结果。
- **动态**：心率、疲劳和动作质量在训练过程中不断变化，环境信息需要实时更新。
- **序贯**：今天的训练会影响之后的疲劳、恢复和训练安排，不能只看单次动作。
- **连续**：心率、时间、运动强度等是连续变化的数值，指导也可能需要实时调整。

基本设定可以看作用户与一个智能体交互；如果医生、教练或其他服务也参与决策，再按具体系统扩展为多行动者环境。

### 3. 售后退款：Workflow、Agent 与混合方案

你的核心区别是对的：Workflow 的步骤预先确定，Agent 能根据情况调整。但“Workflow 无法处理特殊情况”和“Agent 能处理各种意外”都说得太绝对。Workflow 可以设置异常分支或转人工；Agent 也可能误判、漏信息或给出不一致的决定。

| 方案     | 优点                                                         | 局限                                                         |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Workflow | 规则和路径明确，运行稳定、结果一致，通常更容易审计和预测     | 规则覆盖不全时需要添加分支；业务变化会带来流程维护成本；难以理解复杂的自由文本或未预见情形 |
| Agent    | 能理解自然语言材料，综合多种信息，在开放情形下动态规划和选择工具 | 输出有不确定性，可能误解政策或产生幻觉；调用时间和费用可能更高；需要校验、安全边界和审计机制 |

**适用场景**：规则清晰、合规要求严格、每一步都能预先规定的退款判断适合以 Workflow 为主；材料复杂、表达不统一、需要综合订单与商品状态的部分，可以由 Agent 协助分析。

**方案 C：混合式流程**。先由 Workflow 执行明确的硬规则（例如商品类别、申请期限、金额阈值）；Agent 负责从用户描述和订单材料中提取信息、总结理由、识别异常并给出建议；Workflow 再按政策决定自动处理或转人工。Agent 不应绕过退款硬规则。对高金额或证据不足的申请，可要求人工审批并记录每一步依据。

因此，如果我是负责人，会采用 Workflow 管住政策和审批边界，让 Agent 处理语义理解和复杂材料，并保留人工处理例外情况。比起让 Agent 独立批准所有退款，这种组合更容易兼顾灵活性和可控性。

### 4. 为旅行助手增加记忆、备选推荐与反思

#### 记住用户偏好

把偏好写进 system prompt 可以在一次运行中作为上下文提供，但 system prompt 主要是固定角色和规则，不适合独自承担跨会话记忆。更合适的做法是把用户明确表达的偏好保存为结构化状态，例如景点类型、预算、步行接受程度；每轮从状态中取出当前任务相关的信息，作为上下文交给 LLM。用户偏好改变时更新记录，并让用户能更正或删除。

#### 景点售罄时推荐备选

增加查询门票/开放状态的工具。若 Observation 返回“售罄”，将该结果和已推荐景点记入历史；下一轮让 Agent 根据天气、偏好和预算选择未售罄的备选项。为搜索设置重试上限；若没有可用选项，就清楚说明情况并询问用户是否调整条件。

#### 连续拒绝三次后调整策略

在状态中记录拒绝次数、已经推荐过的景点，以及用户给出的拒绝原因。每次拒绝都作为新的 Observation 返回；累计三次后触发重新规划：总结已知偏好与被拒绝原因，调整推荐条件，必要时先向用户询问更明确的偏好。记录已拒绝的候选，避免原样重复推荐，并继续保留最大循环次数作为停止条件。

这三个功能都沿用同一个改法：把新信息放进状态或 Observation，让下一轮决策能够利用它；需要持久保留的偏好单独存储，不依赖模型“自动记住”。

### 5. 用医疗分诊助手说明系统 1 与系统 2

可以设计一个面向健身用户的健康分诊助手，帮助识别运动风险并给出安全建议。系统 1/系统 2 是理解快慢决策的类比，不代表程序里真的有两个人类式思维系统。

- **系统 1：快速筛查。** 迅速读取心率、症状和用户描述，识别明显异常模式；例如出现胸痛、晕厥等危险信号，或生理指标超过医生设定的安全阈值时，立即建议停止运动并寻求专业帮助。模式识别可由模型辅助，明确的安全阈值和禁行动作应由规则约束。
- **系统 2：谨慎分析。** 对非紧急情形综合用户病史、药物、近期训练、恢复情况和目标，检索可靠资料，比较多种解释，评估不确定性，再调整训练建议或转交专业人员。
- **协作方式：** 系统 1 负责快速发现风险并触发安全措施；系统 2 对复杂情况补充上下文、核对证据并决定下一步；遇到高风险或证据不足时由人工专业人员接手。快速筛查结果和后续反馈可用于改进识别与流程。

这个例子体现神经符号结合的思路：神经模型擅长从复杂信号中识别模式，符号规则擅长表达阈值、禁忌和必须遵守的约束，两者配合能够兼顾灵活识别与明确控制。

### 6. LLM 智能体的局限与评估

#### 为什么会产生幻觉

LLM 根据上下文生成可能合适的后续文本，语言流畅不等于事实正确。当知识缺失、问题含糊、上下文过长或信息过时时，模型可能用看似合理的内容填补空白。Agent 还会受到工具返回错误、检索遗漏、参数错误和多步推理误差的影响；前一步的错误观察可能被后续步骤继续放大。

降低风险的方法包括：让需要实时性的结论依赖可核验的工具结果；检查来源和参数；明确表达不确定性；对重要决定设置规则校验或人工复核。工具调用本身也需要验证，不能把“用了工具”当作“结果必然正确”。

#### 没有最大循环次数会怎样

如果 Agent 无法判断任务已经结束，它可能反复调用同一工具、重复搜索、在两个行动之间来回循环，造成时间、Token 和 API 费用持续增加。若工具会产生副作用（例如下单、退款、发邮件），重复调用还可能造成重复操作。

最大轮数提供一个简单停止边界。实际系统还可结合总运行时间、费用预算、重复 Action 检测、工具调用权限和人工接管条件。

#### 如何评价智能体

只看准确率不够。Agent 不只是给出答案，也要决定行动、正确使用工具并安全完成任务。可以组合评估：

- **任务成功率**：是否达到用户目标，关键步骤是否完成。
- **事实质量**：回答是否准确，是否有来源支持，是否能表达不确定性。
- **工具能力**：是否选对工具、参数是否正确、是否正确使用返回结果。
- **鲁棒性**：输入换一种说法、数据缺失或工具失败时，能否妥善恢复。
- **安全与约束**：是否遵守政策、权限和停止条件，有无不当副作用。
- **效率与体验**：延迟、Token/API 成本、交互轮数，以及用户是否能理解并接受结果。

适合为不同任务准备代表性测试案例，既看最终结果，也检查中间行动轨迹。单一指标可能掩盖“答案正确但工具调用危险”或“平均准确但遇到边界情形就失败”等问题。