# improve-efficiency-skills

这个仓库收集了一组用于提升 Codex 本地工作效率的技能。每个顶层目录都是一个独立技能，并且都包含自己的 `SKILL.md`。

## 技能列表

| 技能目录 | 技能名称 | 作用 | 适用场景 |
| --- | --- | --- | --- |
| `kimi-review` | `kimi` | 调用 Kimi CLI 做代码分析、重构、自动化编辑或代码评审。 | 需要 Codex 让 Kimi 作为第二个评审者，或让 Kimi 在当前仓库中执行一个实现任务。 |
| `claude-code-review` | `claude-code-review` | 调用 Claude Code CLI 作为外部代码评审工具。 | 需要 Claude Code 评审本地改动，然后由 Codex 复核问题是否真实有效。 |
| `op-gpt5.5-prompt` | `op-gpt5.5-prompt` | 根据 GPT-5.5 提示词最佳实践改写提示词草稿。 | 粘贴一段粗糙提示词，让 Codex 优化成更清晰、可执行的任务契约。 |

## 安装方式

安装方式一：把需要的技能目录复制到本地 Codex 技能目录中。

```bash
mkdir -p ~/.codex/skills
cp -R kimi-review ~/.codex/skills/
cp -R claude-code-review ~/.codex/skills/
cp -R op-gpt5.5-prompt ~/.codex/skills/
```

安装方式二：使用 Codex 自带的技能安装器从 GitHub 安装单个技能。

```bash
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --url https://github.com/newlakeshen/improve-efficiency-skills/tree/main/kimi-review
```

如果要安装其他技能，把 URL 里的 `kimi-review` 替换成 `claude-code-review` 或 `op-gpt5.5-prompt`。

安装或更新后，重启 Codex，让新的技能元数据生效。

## 依赖要求

- `kimi-review` 需要本机已安装并登录 `kimi` CLI，且 `kimi` 在 `PATH` 中可用。
- `claude-code-review` 需要本机已安装并登录 `claude` CLI，且 `claude` 在 `PATH` 中可用。
- `op-gpt5.5-prompt` 没有额外本地 CLI 依赖。
