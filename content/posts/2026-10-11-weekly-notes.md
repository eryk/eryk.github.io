---
title: "Obsidian 周记｜把判断交给机制"
date: 2026-10-11T21:01:26+08:00
draft: false
categories: ["技术随笔"]
tags: ["Obsidian", "周记", "Agent", "工程实践", "生成式AI", "工具"]
description: "10 月 5 日至 10 月 11 日收录：把多 Agent 的纠错、验证与合并边界写进确定性机制，以及两种照片再创作线索。"
---

记录范围：2026-10-05—2026-10-11。

## 技术阅读

- **把纠错往上推**：[poteto 的纠错层级](https://x.com/poteto/status/2089067865098113024)主张优先用架构、类型、lint 和测试消灭重复错误，再考虑规则与人工审查；这是作者方法，尚未在自己的项目中验证。
- **合并不交给编码 agent**：[martinshein 的流程草图](https://x.com/martinshein/status/2108547946413273432)把 builder、审查和持锁执行合并的组件分权；其规模数据未获独立核验，但“绿灯会话不等于可合并 PR”的边界值得保留。
- **验证应留下用户路径证据**：[pstack 的 verification skill](https://github.com/cursor/plugins/blob/main/pstack/skills/create-verification-skill/SKILL.md)将启动、只读诊断、真实操作、证据与清理拆开，提醒验证不能只靠内部开关或自评。

## 工具与创作线索

- **QDC507 的开源生态**：[一篇模块玩法汇总](https://x.com/Flowers_hurt/status/2093263414047211698)列出 Mac、Linux 与 Docker 管理工具，可作为蜂窝模块的研究入口；功能和项目维护状态均未逐项实测。
- **照片续写先保证连续性**：[Second World 提示词](https://x.com/Sukiea1008/status/2107363309846675598)要求从原照片中选一条结构穿过上下分界，再在纸张与手绘线条中改变其物理规则；这是可复用的构图约束，未作生成验证。

*按本周收录时间整理；外部观点和工具线索均按来源归属，不代表个人实测或当周新闻。*
