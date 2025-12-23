# Tool-use Prompting

Tool-use prompting is how you teach an LLM to recognize when it needs external capabilities and how to invoke them correctly. The LLM doesn't actually execute tools - your framework does. The LLM just needs to output structured requests that your system can parse and execute.

**The Core Challenge:** LLMs are trained to generate text. You need to train them (via prompts) to generate *function calls* at the right moments with the right parameters.

## Three Key Elements

**1. Tool Definitions**
You must describe each tool clearly: what it does, when to use it, what parameters it needs, and what it returns. Think of this as API documentation for the LLM.

```
You have access to these tools:

search_database(query: string, filters: dict) -> list
Searches the customer database. Use this when you need to find customer information.
- query: natural language search terms
- filters: optional dict with keys like "status", "date_range"
Returns: list of matching customer records

send_email(to: string, subject: string, body: string) -> bool
Sends an email. Use this after you've gathered all necessary information.
Returns: true if sent successfully
```

**2. Invocation Format**
Specify exactly how the LLM should format tool calls. Most frameworks use XML or JSON. Be explicit and consistent.

```
To use a tool, output:
<tool_call>
<tool_name>search_database</tool_name>
<parameters>
{"query": "customers in California", "filters": {"status": "active"}}
</parameters>
</tool_call>
```

**3. Decision Logic**
Guide the LLM on *when* to use tools versus when to respond directly. This is where many agent prompts fail.

```
Before responding:
1. Can you answer directly from conversation history? If yes, respond immediately.
2. Do you need external information? If yes, use search_database.
3. Do you need to take action? If yes, use the appropriate action tool.

Never use tools unnecessarily. Never invent tool results.
```

## Practical Example

Here's how this looks in a customer service agent:

```
You are a customer service agent. You help users with their orders.

Available tools:

get_order(order_id: string) -> dict
Retrieves order details. Use when customer mentions an order number.

update_order(order_id: string, changes: dict) -> bool
Modifies an order. Only use after confirming changes with customer.

Tool format:
<tool_call>
<tool_name>function_name</tool_name>
<parameters>{"key": "value"}</parameters>
</tool_call>

Important: Always retrieve order details before making changes. Never assume order contents.
```

When the user says "I need to change order #12345 to express shipping", the LLM should output:

```
<tool_call>
<tool_name>get_order</tool_name>
<parameters>{"order_id": "12345"}</parameters>
</tool_call>
```

Then wait for your framework to execute it and return results before proceeding.

## Common Mistakes to Avoid

Don't describe tools vaguely. "Use search when you need information" is too broad. Specify: "Use search_database for customer records. Use search_web for general product information."

Don't let the LLM hallucinate tool results. After requesting a tool, the LLM must stop and wait. Your system prompt should say: "After outputting a tool call, stop generating. Wait for the tool result before continuing."

Don't give the LLM too many tools at once. If you have 20 tools, the LLM will struggle to choose correctly. Group tools by context or use a two-stage approach where the LLM first decides which category, then which specific tool.

## Framework Integration Note

In LangGraph, your tool calls become nodes in the graph. In ADK, they're actions the agent can take. But the prompting principle is the same: clear definitions, explicit format, smart decision logic.

The LLM is making a request. Your framework is the executor. Keep that separation clear in your prompts.

---

That's tool-use prompting. The foundation is: describe tools well, format calls clearly, guide decisions explicitly. Everything else builds on this.
