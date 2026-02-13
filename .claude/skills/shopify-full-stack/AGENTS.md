# Shopify Full-Stack Skills - Agent Tasks

This document describes the specialized agents available for Shopify full-stack development tasks and when to use them.

## Available Agents

### 1. Cook Agent

**Purpose**: Feature implementation specialist

**Use for**:
- Implementing new features in Shopify apps
- Building UI components with Polaris
- Creating app routes and handlers
- Adding functionality step-by-step

**Do NOT use for**:
- Research-only tasks (use Explore agent)
- Simple file edits (use Edit tool directly)
- Queries about documentation (use docs-seeker or context7)

**Example triggers**:
- "Implement product review system"
- "Add settings page to app"
- "Create webhook handler for orders"

### 2. Explore Agent

**Purpose**: Fast codebase exploration

**Use for**:
- Understanding project structure
- Finding files by patterns (e.g., "src/components/**/*.tsx")
- Searching code for keywords
- Answering questions about codebase organization

**Use when**:
- You don't know where something is located
- Need to understand architecture before implementing
- Open-ended exploration tasks

**Thoroughness levels**:
- `quick` - Basic searches, minimal detail
- `medium` - Moderate exploration, good balance
- `very thorough` - Comprehensive analysis, multiple locations

### 3. Plan Agent

**Purpose**: Software architecture and implementation planning

**Use for**:
- Designing implementation strategies
- Breaking down complex tasks
- Identifying critical files and trade-offs
- Creating step-by-step plans before coding

**Example triggers**:
- "Design a multi-currency system"
- "Plan refactoring of billing logic"
- "How should I approach caching strategy?"

### 4. Debug Agent

**Purpose**: Systematic problem diagnosis and fixing

**Use for**:
- Finding root causes of bugs
- Implementing verification gates
- Testing fixes before claiming completion
- Multi-stage debugging workflows

**Process**:
1. Analyze the problem
2. Identify potential causes
3. Test hypotheses
4. Implement fix
5. Verify solution

### 5. Problem-Solving Agent

**Purpose**: Creative solutions for stuck points

**Use for**:
- Breaking through complex blockers
- Inversion thinking (approach from opposite angle)
- Pattern recognition
- Simplification strategies

**When stuck**:
- Conventional approaches aren't working
- Need creative problem-solving
- Facing architectural deadlock

## When NOT to Use Agents

**Direct tool use is better when**:
- Reading a specific file (use Read)
- Simple text replacement (use Edit)
- Running git commands (use Bash)
- Searching for specific code pattern (use Grep/Glob)

**Use skills instead**:
- `/commit` - Create git commits
- `/review-pr` - Review pull requests
- `/shopify-dev-mcp:*` - Query Shopify documentation

## Agent Workflow Examples

### Feature Implementation

```
User: "Add product rating feature to app"

1. Use Explore agent (medium) to understand:
   - Current app structure
   - Where products are displayed
   - Existing database schema

2. Use Plan agent to design:
   - Rating data model
   - UI components needed
   - Admin integration points

3. Use Cook agent to implement:
   - Database migrations
   - API endpoints
   - Frontend components
```

### Bug Investigation

```
User: "Webhooks aren't being received"

1. Use Debug agent to:
   - Check webhook configuration
   - Verify Shopify registration
   - Test webhook endpoints
   - Implement fix with verification
```

### Codebase Understanding

```
User: "How is authentication handled?"

1. Use Explore agent (quick):
   - Find auth-related files
   - Identify middleware/functions
   - Summarize flow
```

## Agent Selection Guide

| Task Type | Best Agent | Alternative |
|-----------|-------------|-------------|
| New feature | Cook | Plan (for complex features) |
| Bug fix | Debug | Problem-solving (if stuck) |
| Code exploration | Explore | Read (single file) |
| Architecture decision | Plan | Sequential-thinking |
| Documentation lookup | docs-seeker | context7 |
| Shopify API query | shopify-dev-mcp | GraphQL schema introspection |

## Multi-Agent Coordination

For complex tasks, use agents in sequence:

1. **Explore** - Understand current state
2. **Plan** - Design approach
3. **Cook** - Implement changes
4. **Debug** - Verify and fix issues

Each agent receives full conversation context, so work flows seamlessly between them.
