# vue-component-skill

A Vue 3 SFC agent skill — covers only the patterns AI agents frequently get wrong, helping them write correct Vue 3 components.

## Repository Structure

```
vue-component-skill/
├── AGENTS.md                                       # Contribution guide & branch strategy
├── CLAUDE.md                                       # Claude Code project instructions
├── LICENSE
├── README.md
├── .claude-plugin/
│   └── marketplace.json                            # Claude plugin marketplace manifest
├── .github/
│   └── workflows/
│       └── sync-to-main.yml                        # dev → main sync workflow
└── skills/
    └── vue-component/
        ├── SKILL.md                                # Core index, loaded first by AI
        ├── reference/                              # On-demand reference files
        │   ├── prop-destructured-reactivity.md     # Props lose reactivity when destructured
        │   ├── emit-type-based-syntax.md           # Correct defineEmits type syntax
        │   ├── v-model-component-breaking-change.md # v-model Vue 2 → 3 breaking change
        │   ├── ref-no-value-in-template.md         # Don't use .value in templates
        │   ├── reactive-misuse.md                  # reactive() misuse patterns
        │   ├── computed-no-side-effects.md         # No side effects in computed
        │   ├── component-splitting-rules.md        # When and how to split components
        │   ├── prefer-props-emit-over-refs.md      # Prefer props/emits over component refs
        │   ├── smart-dumb-separation.md            # Smart vs presentational components
        │   ├── no-v-if-with-v-for.md              # Never use v-if with v-for on same element
        │   ├── v-for-key-must-be-stable.md        # v-for :key must be stable and unique
        │   ├── style-must-be-scoped.md            # Styles must be scoped
        │   └── accessibility-essentials.md        # ARIA & keyboard accessibility basics
        └── evals/
            └── evals.json                         # 5 validation scenarios
```

### Progressive Disclosure

```
Layer 1: name + description (~100 tokens)
  ↓ AI decides if relevant
Layer 2: SKILL.md body (index of all rules)
  ↓ AI picks which reference files to load
Layer 3: reference/xxx.md (loaded on demand, one file at a time)
```

---

## Installation

### Claude Code

Claude Code auto-discovers skills from two locations:

```bash
~/.claude/skills/           # Global — available in all projects
your-project/.claude/skills/ # Project-level — current project only
```

**Global install:**

```bash
# 1. Clone the repo
git clone https://github.com/XivLaw/vue-component-skill.git

# 2. Copy the skill
mkdir -p ~/.claude/skills/vue-component
cp -r vue-component-skill/skills/vue-component/* ~/.claude/skills/vue-component/

# 3. Verify
ls ~/.claude/skills/vue-component/
# Expected: SKILL.md  reference/  evals/
```

**Project-level install:**

```bash
mkdir -p .claude/skills/vue-component
cp -r vue-component-skill/skills/vue-component/* .claude/skills/vue-component/
```

No restart required — Claude Code detects changes automatically.

---

## Verify the Install

Ask Claude Code:

```
Create a Vue search input component that supports v-model
```

If the skill is active, Claude should:

- Use `defineModel()` or `modelValue` + `update:modelValue` (not Vue 2's `value` + `input`)
- Use `<script setup lang="ts">`
- Use `ref()` for simple state, not `reactive()`
- Include `scoped` on the style block

If it doesn't trigger automatically, prefix your prompt:

```
use vue-component skill, create a Vue search input that supports v-model
```

