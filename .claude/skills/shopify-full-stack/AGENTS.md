# Shopify Full-Stack - Agent Workflows

Guidance on which agents to use for specific Shopify development tasks.

## Agent Selection

| Task | Agent | Notes |
|------|-------|-------|
| New feature / extension | Cook | Use template workflow for theme extensions |
| Find files / understand codebase | Explore | Set thoroughness: quick, medium, very thorough |
| Complex feature design | Plan | Architecture decisions, trade-offs |
| Bug investigation | Debug | Root cause analysis, verification |
| Stuck on a problem | Problem-Solving | Creative approaches, inversion thinking |
| Simple file edit | Direct tool use | Read/Edit/Write tools directly |

## Theme Extension Workflow (Cook Agent)

When creating a Theme App Extension, **always instruct Cook agent** to:

1. Copy template: `cp -r .claude/skills/shopify-full-stack/templates/theme-extension extensions/<name>`
2. Modify copied files - **never create from scratch**
3. Validate Liquid with MCP `validate_theme`
4. Run `shopify extension check` before `shopify app dev`

## Multi-Agent Sequence (Complex Tasks)

```
Explore (understand) → Plan (design) → Cook (implement) → Debug (verify)
```

Each agent receives full conversation context - work flows seamlessly between them.
