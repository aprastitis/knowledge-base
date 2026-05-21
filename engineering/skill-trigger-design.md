# Skill Trigger Design

The YAML frontmatter `description` field is the primary mechanism Claude uses to decide whether to load a skill. Getting this right is the difference between a skill that activates reliably and one that's ignored.

## The Description Formula

Every description must contain:

```
[What it does]. Use when [specific trigger phrases / situations].
```

**"What it does"** — Capability statement, what the skill enables
**"When to use it"** — Specific phrases users would say, situations that activate it

## Trigger Phrase Patterns

Include phrases that real users would actually say, not just technical descriptions:

```yaml
# ❌ Too technical — users won't say this
description: Orchestrates multi-step workflows via MCP integration.

# ✅ Uses natural language — matches how users actually ask
description: Creates documents, presentations, and spreadsheets. 
Use when asking to "make a report", "create a slide deck", 
"build a spreadsheet", or "generate a document from a template".
```

## Testing Triggering

**Quantitative metrics:**
- **90% trigger rate on relevant queries** — Run 10-20 test queries that should trigger. Track auto-load vs explicit invocation ratio.
- **0 trigger rate on irrelevant queries** — Verify it doesn't load on unrelated topics.

**How to test:**
1. Run obvious relevant queries — should auto-load
2. Run paraphrased versions — should still load
3. Run clearly unrelated queries — should not load
4. For MCP skills: check MCP server logs for invocation patterns

**Qualitative signals:**
- Users don't need to tell Claude what to do next
- Workflows complete without user correction
- Same request produces consistent results across sessions

## Common Trigger Failures

**Under-triggering** (skill never loads):
- Description too vague or technical
- No specific trigger phrases
- Not updated when user language changes

**Over-triggering** (loads on unrelated queries):
- Description too broad ("helps with coding" triggers on everything)
- Missing "when NOT to use" context
- Ambiguous capability description

## Fixing Trigger Issues

**If under-triggering:**
- Add more specific trigger phrases
- Include file types, tool names, specific scenarios
- Use exact phrases users have actually said

**If over-triggering:**
- Narrow the "when" clause
- Add exclusion conditions
- Be more specific about what the skill does NOT cover

## Structure Requirements

- **kebab-case** for name field, no spaces, no capitals
- **Under 1024 characters** for description
- **No XML angle brackets** (`<` or `>`) — security restriction
- name must match folder name

## Relationship to My Work

When I build a skill, the description is the first thing I should get right. Write the description first, before the full instructions — if I can't clearly express what it does and when to use it, the skill probably isn't well-defined yet.

## See Also

- [[progressive-disclosure-pattern]] — The three-level system that makes triggering work
- [[scaffolding-for-ai-agents]] — How tooling determines whether skills actually execute effectively