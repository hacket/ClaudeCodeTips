# CLAUDE.md 参考资料

本目录收集各类 CLAUDE.md 开发准则和 Prompt 模板，供不同项目参考使用。

## 目录

### 开发准则

- [开发准则](开发准则/) — 完整的 Claude Code 开发规范，涵盖编码标准、验证机制、质量审查、Git 工作流等，适用于正式项目的 CLAUDE.md 配置

### Miles

- [Miles](Miles/) — Miles 的 Technical Co-Founder Prompt，核心思路是用组织管理逻辑驾驭 AI 而非指令驱动，将 AI 角色从 Coder 升维为技术联合创始人，强制分阶段交付（Discovery → Planning → Building → Polish → Handoff），要求敢于质疑假设、凡事有交代、生产级质量。来源：[AYi 推文](https://x.com/AYi_AInotes/status/2020345793098707305)

找到 Prompt 里绿色的 [My Idea] 和 [How serious I am] 这两处方括号
• 把 [My Idea] 换成你想做的东西（比如：我想做一个番茄钟 App，要能统计时间...）
• 把 [How serious I am] 选一个（比如：I want to launch it publicly）
打开 Claude或者 Cursor 的对话框，粘贴进去，回车

这个prompt的本质是把『提示工程』升级成了『AI团队管理学』。当我们不再把AI当工具，而是当一个有主动性、有框架、有责任感的『员工』时，产出质量就完全不一样了。 这可能预示着未来AI协作的真正范式：不是更聪明的模型，而是更聪明的『管理方式』。Miles这个模板，值得所有重度AI用户收藏并迭代

Prompt 收藏到：
https://promptup.net/prompt/en-build-any-app-the-technical-co-founder
