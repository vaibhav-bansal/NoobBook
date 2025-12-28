# Refactoring Guide

## Backend Service Architecture

```
ai_agents/          → Orchestration only (the "brain")
ai_services/        → Single-purpose AI functions (the "skills")
tool_executors/     → Tool execution logic (the "hands")
```

---

## The Pattern

### ai_agents/ - Orchestration

**Purpose:** Run the agentic loop. Manage messages. Decide what to do next.

**Should contain:**
- Message loop (user → Claude → tool → Claude → ...)
- Stop conditions (termination tool, max iterations)
- Message serialization
- Execution logging

**Should NOT contain:**
- Tool execution logic
- File I/O operations
- External API calls
- Business logic

```python
# GOOD: Agent delegates to executor
result = some_executor.execute_tool(tool_name, tool_input, project_id)

# BAD: Agent does the work itself
if tool_name == "create_file":
    with open(file_path, 'w') as f:
        f.write(content)
```

---

### ai_services/ - Single-Purpose AI Functions

**Purpose:** One AI call. One job. Returns result.

**Should contain:**
- Single Claude API call
- Prompt construction
- Response parsing
- Return structured result

**Should NOT contain:**
- Loops or iterations
- Multiple API calls
- Tool handling
- State management

```python
# GOOD: Single purpose
def extract_pdf_page(page_bytes, page_num) -> Dict:
    response = claude_service.send_message(...)
    return parse_extraction(response)

# BAD: Does too much
def process_entire_pdf(pdf_path) -> Dict:
    for page in pages:
        # multiple calls, loops, state...
```

---

### tool_executors/ - Tool Execution

**Purpose:** Execute a tool. Handle the messy details. Return clean result.

**Should contain:**
- Tool-specific logic
- File operations
- External API calls
- Error handling
- Result formatting

**Should NOT contain:**
- Claude API calls
- Message management
- Loop logic

```python
# GOOD: Executor handles details
class WebsiteToolExecutor:
    def execute(self, tool_name, tool_input, context):
        if tool_name == "create_file":
            return self._create_file(tool_input, context)

    def _create_file(self, tool_input, context):
        # All the file I/O, validation, placeholder replacement...
        return {"success": True, "message": "File created"}
```

---

## Refactoring Checklist

When refactoring an agent:

1. **Identify tool handlers** - Any `if tool_name == "xyz":` block with >5 lines
2. **Extract to executor** - Move logic to `tool_executors/{agent}_executor.py`
3. **Agent calls executor** - `result = executor.execute_tool(name, input, context)`
4. **Executor returns dict** - Clean result the agent can use

---

## The Goal

**Before:** 760-line agent with everything mixed in

**After:**
- 150-line agent (orchestration only)
- 200-line executor (tool logic)
- Clear separation, easy to test, easy to extend
