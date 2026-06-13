# Data Analytics Skills for Hermes

本仓库 fork 自 **[nimrodfisher/data-analytics-skills](https://github.com/nimrodfisher/data-analytics-skills)**（上游仓库），完整保留了上游仓库的所有内容，没有对上游仓库的skills做任何实质性的修改，调整仅限于适配Hermes使用环境和细微功能修补，具体调整请参见以下段落。

## 我们做了什么

- **格式迁移**：将所有 31 个 SKILL.md 的 frontmatter 从 Claude 最小格式升级为 **Hermes Agent 兼容格式**，添加 `version`、`platforms`、`tags`、`triggers` 字段，实现 Hermes 下的自动技能加载。
- **内容改进**：对 `metric-reconciliation`、`schema-mapper`、`visualization-builder` 三个技能的本体内容做了本地实践验证后的更新。
- **新增参考**：在 `visualization-builder` 下新增 `references/axis_common_mistakes.md`，记录可视化坐标轴常见陷阱（基线选择、正负值柱图、不连续时间线、刻度密度、suptitle 裁剪）及修复方法。
- **脚本、资产、分类结构没有变动。**

## 参考

- **上游仓库（原始项目）**：[https://github.com/nimrodfisher/data-analytics-skills](https://github.com/nimrodfisher/data-analytics-skills)  
  All credit for the 31 skills, scripts, assets, and category structure goes to the upstream maintainer.
- **Hermes Agent**：[https://hermes-agent.nousresearch.com](https://hermes-agent.nousresearch.com)

> 🤖 本仓库的格式迁移、内容改进和新增参考文件由 AI 代理自动生成，经人工审核后推送。

## 用法

```bash
# Hermes 用户
hermes skills install https://github.com/Hypnogamma/data-analytics-skills-hermes

# 或手动复制到本地 skills 目录
cp -r 01-data-quality-validation/programmatic-eda ~/.hermes/skills/
```
