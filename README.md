# TokenTrackingUsage
This notebook provides a guide on how to track token usage for specific calls, specifically for the OpenAI API. Token tracking is essential for managing costs and staying within API usage limits. The examples provided demonstrate token tracking for both single and multiple calls, as well as more complex scenarios involving agent-based chains.

# Prerequisites
Before using this notebook, ensure you have the following:

OpenAI API key: Set your API key as the environment variable OPENAI_API_KEY. For example:
```
import os
os.environ["OPENAI_API_KEY"] = "your-api-key"
```
SERPAPI API key (optional): If you plan to use the SERPAPI tool in the agent-based chain example, set your API key as the environment variable SERPAPI_API_KEY. For example:
```
import os
os.environ["SERPAPI_API_KEY"] = "your-api-key"
```
Required Python libraries: The notebook relies on the following Python libraries, which can be installed via pip:
```
pip install langchain
```
Example 1: Tracking Token Usage for a Single LLM Call
This example demonstrates how to track token usage for a single call to the OpenAI Language Model (LLM).
```
from langchain.llms import OpenAI
from langchain.callbacks import get_openai_callback
```
# Initialize the OpenAI model
```
llm = OpenAI(model_name="text-davinci-002", n=2, best_of=2)
```
# Track token usage using the get_openai_callback() context manager
```
with get_openai_callback() as cb:
    result = llm("Tell me a joke")
    print(cb)
The output will display the token usage information:
```
Output result:-
```
Tokens Used: 42
    Prompt Tokens: 4
    Completion Tokens: 38
Successful Requests: 1
Total Cost (USD): $0.00084
Any code inside the context manager will be tracked for token usage.
```

Example 2: Tracking Token Usage for Multiple Calls
This example demonstrates how to track token usage for multiple calls in sequence.
```
with get_openai_callback() as cb:
    result = llm("Tell me a joke")
    result2 = llm("Tell me a joke")
    print(cb.total_tokens)
```
The output will display the cumulative token usage for both calls:
Output result:-
```
83
```
The total_tokens attribute of the callback object retrieves the total number of tokens used.

Example 3: Tracking Token Usage in Agent-Based Chains
This example showcases token tracking for a more complex scenario involving an agent-based chain with multiple steps. The example assumes the availability of the SERPAPI tool and uses it alongside the OpenAI Language Model.
```
import os
os.environ["SERPAPI_API_KEY"] = "your-serpapi-api-key"
from langchain.agents import load_tools
from langchain.agents import initialize_agent
from langchain.agents import AgentType
from langchain.llms import OpenAI
```

# Initialize the OpenAI model and load additional tools
```
llm = OpenAI(temperature=0)
tools = load_tools(["serpapi", "llm-math"], llm=llm)
```

# Initialize the agent with specific tools and settings
```
agent = initialize_agent(tools, llm, agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION, verbose=True)
```

# Track token usage using the get_openai_callback() context manager
```
with get_openai_callback() as cb:
    response = agent.run(
        "Who is Olivia Wilde's boyfriend? What is his current age raised to the 0.23 power?"
    )
    print(f"Total Tokens: {cb.total_tokens}")
    print(f"Prompt Tokens: {cb.prompt_tokens}")
    print(f"Completion Tokens: {cb.completion_tokens}")
    print(f"Total Cost (USD): ${cb.total_cost}")
```

The output will display token usage information for the agent-based chain:
Output result:-
```
Total Tokens: 1498
Prompt Tokens: 1340
Completion Tokens: 158
Total Cost (USD): $0.02996
```
This example demonstrates how token usage is tracked throughout the execution of the agent, including each step of the chain.

By following this guide, you can effectively track token usage for specific calls in your OpenAI API applications. Token tracking is crucial for cost optimization and resource management. Feel free to customize and extend the examples to suit your specific requirements.
