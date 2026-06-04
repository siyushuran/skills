# Claude Code Skills Backup

个人 Claude Code 全局技能备份仓库。包含 7 个技能，覆盖嵌入式开发、代码质量、写作研究和工程效率。

## 备份与恢复

```bash
# 克隆到全局 skills 目录
git clone https://github.com/siyushuran/skills.git ~/.claude/skills
```

---

## 技能列表

### embedded-systems — 嵌入式 MCU 固件开发

- **来源**：[jeffallan/claude-skills](https://github.com/jeffallan/claude-skills)（3.8K+ 安装量）
- **安装**：`npx skills add jeffallan/claude-skills@embedded-systems -y -g`
- **用途**：扮演资深嵌入式工程师，覆盖 STM32/ESP32/FreeRTOS/裸机编程、中断/DMA、外设驱动、功耗优化、实时系统。内置 6 步工作流（分析约束→设计架构→实现驱动→验证→优化→测试），5 份参考知识库（RTOS 模式、通信协议含 CAN/UART/I2C/SPI、内存优化、功耗优化、MCU 编程）
- **触发词**：firmware, microcontroller, RTOS, FreeRTOS, STM32, bare metal, interrupt, DMA, real-time

### misra-fix — MISRA C 2012 合规修复

- **来源**：自建（基于项目 MISRA C 规范）
- **安装**：已包含在仓库中
- **用途**：扫描 C 文件并修复 MISRA C 违规——整数字面量加 U 后缀、enum 赋值显式类型转换、有符号/无符号转换修复。不修改代码逻辑，仅添加后缀和类型转换，修复后报告违规数量变化
- **触发词**：MISRA, MISRA-C, 代码检查, 静态分析

### technical-writer — 技术文档写作

- **来源**：[shubhamsaboo/awesome-llm-apps](https://github.com/shubhamsaboo/awesome-llm-apps)
- **安装**：`npx skills add shubhamsaboo/awesome-llm-apps@technical-writer -y -g`
- **用途**：创建清晰的技术文档、API 参考、README 文件、教程、用户指南
- **触发词**：documentation, README, API reference, tutorial, technical writing

### hv-analysis — 横纵分析深度研究

- **来源**：自建（基于数字生命卡兹克的横纵分析法）
- **安装**：已包含在仓库中
- **用途**：系统性研究产品/公司/概念/技术/人物。纵轴追踪完整生命历程，横轴与竞品对比，交叉产出独到洞察，最终生成排版精美的 PDF 研究报告
- **触发词**：横纵分析、深度研究、调研、竞品分析

### khazix-writer — 公众号长文写作

- **来源**：自建（数字生命卡兹克写作风格）
- **安装**：已包含在仓库中
- **用途**：以卡兹克的个人风格撰写公众号长文，适用于内容创作和公众号输出
- **触发词**：写文章、写稿子、公众号、长文、续写、扩写

### neat-freak — 文档与记忆整理同步

- **来源**：自建（洁癖级项目知识库维护）
- **安装**：已包含在仓库中
- **用途**：会话结束后对项目文档（CLAUDE.md/README/docs/）和 agent memory 进行全面审查与同步，确保文档不腐化。也支持 CLAUDE.md 的编写和审计
- **触发词**：整理文档、同步一下、更新记忆、收尾、整理

### canvas-design — 视觉艺术设计

- **来源**：[sickn33/antigravity-awesome-skills](https://github.com/sickn33/antigravity-awesome-skills)
- **安装**：`npx skills add sickn33/antigravity-awesome-skills@canvas-design -y -g`
- **用途**：创建设计哲学并生成视觉作品（.png/.pdf），用于海报、艺术设计等静态视觉内容
- **触发词**：poster, art, design, create a poster

---

## 更新备份

安装新 skill 后执行以下步骤同步到备份仓库：

```bash
# 1. 如果新 skill 是 symlink，替换为实际目录
cd ~/.claude/skills
# 对于 npx 安装的 skill（在 ~/.agents/skills/ 下）：
rm <skill-name> && cp -r ~/.agents/skills/<skill-name> .

# 2. 提交并推送
git add -A
git commit -m "添加/更新 skill: <skill-name>"
git push origin master
```
