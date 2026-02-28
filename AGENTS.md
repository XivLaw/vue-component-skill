# Contributing to vue-component-skill

Agent skills for Vue 3 SFC development — helps AI agents write correct Vue 3 components.

## Branch Strategy

| Branch | Purpose | Direct commits |
|--------|---------|----------------|
| `main` | Published/release branch | ❌ Forbidden |
| `dev`  | Development branch | Via PR only |

All work happens on `dev`. Maintainers sync `dev` → `main` via GitHub Action when ready to publish.

## Skill Philosophy

This skill only covers **patterns AI agents frequently get wrong**. Before adding a new rule, ask:

- **Capability**: Does the AI *fail* to solve this without the rule? (version-specific bugs, undocumented behavior)
- **Efficiency**: Does the AI produce *worse* solutions without the rule? (anti-patterns, bad defaults)

If neither applies, don't add it.

## Adding a New Reference

1. Create `reference/<topic-name>.md` (use kebab-case)
2. Follow the file format:

```markdown
# Title That Describes the Mistake

**Impact**: HIGH|MEDIUM|LOW — One sentence on why this matters.

## Problem

Short description of the mistake AI makes.

## ❌ Incorrect

```vue
<!-- code example of the incorrect pattern -->
```

## ✅ Correct

```vue
<!-- code example of the correct pattern -->
```

## Checklist

- [ ] Concrete, actionable check 1
- [ ] Concrete, actionable check 2
```

3. Add a one-line entry to the relevant section in `SKILL.md`:
   ```
   - Brief description of the pitfall → See [topic-name](reference/topic-name.md)
   ```

## Eval-Driven Development

**Create an eval FIRST**, before writing documentation. This validates the rule is actually needed.

### Eval Format

Each eval in `evals/evals.json` must have:

```json
{
  "id": 6,
  "prompt": "The exact prompt that causes AI to make the mistake",
  "expected_output": "One sentence describing the ideal overall output",
  "files": [],
  "expectations": [
    "Observable, verifiable outcome 1",
    "Observable, verifiable outcome 2"
  ]
}
```

`expectations` items must be:
- ✅ **Concrete**: checkable by reading the output
- ❌ Not vague ("code is correct", "uses best practices")

### Validation Checklist

Before merging a new rule:

- [ ] Baseline test: AI fails without the skill
- [ ] With-skill test: AI passes with the skill installed
- [ ] Reference file follows the standard format
- [ ] SKILL.md index updated with the new entry
- [ ] Eval added to `evals/evals.json`

## Reference File Naming

| Topic | Convention | Example |
|-------|-----------|---------|
| Prop / emit patterns | `<subject>-<problem>.md` | `prop-destructured-reactivity.md` |
| API misuse | `<api>-misuse.md` | `reactive-misuse.md` |
| Prohibited combos | `no-<thing>-with-<thing>.md` | `no-v-if-with-v-for.md` |
| Requirements | `<subject>-must-be-<state>.md` | `style-must-be-scoped.md` |
| Preference rules | `prefer-<good>-over-<bad>.md` | `prefer-props-emit-over-refs.md` |

## Current Reference Coverage

| File | Topic |
|------|-------|
| `prop-destructured-reactivity.md` | Props 解构丢失响应性 |
| `emit-type-based-syntax.md` | defineEmits 类型语法 |
| `v-model-component-breaking-change.md` | v-model Vue 2→3 破坏性变更 |
| `ref-no-value-in-template.md` | 模板中不要用 .value |
| `reactive-misuse.md` | reactive() 误用模式 |
| `computed-no-side-effects.md` | computed 禁止副作用 |
| `component-splitting-rules.md` | 组件拆分规则 |
| `prefer-props-emit-over-refs.md` | 用 props/emits 而非组件 ref |
| `smart-dumb-separation.md` | 智能/展示组件分离 |
| `no-v-if-with-v-for.md` | 禁止 v-if 与 v-for 同元素 |
| `v-for-key-must-be-stable.md` | v-for :key 必须稳定唯一 |
| `style-must-be-scoped.md` | 样式必须 scoped |
| `accessibility-essentials.md` | 无障碍基本要求 |

## Suggested Future References

- `reference/watch-vs-watcheffect.md` — when to use `watch` vs `watchEffect`
- `reference/defineexpose-minimal.md` — only expose what's necessary via `defineExpose`
- `reference/async-component-suspense.md` — async components with `<Suspense>`
- `reference/provide-inject-typing.md` — type-safe `provide/inject` with `InjectionKey`
- `reference/teleport-usage.md` — `<Teleport>` for modals/tooltips outside DOM hierarchy

## PR Checklist

- [ ] Branch from `dev`, not `main`
- [ ] One rule per PR (keep diffs small and reviewable)
- [ ] Eval added and manually verified
- [ ] Reference file follows naming convention
- [ ] SKILL.md index updated
