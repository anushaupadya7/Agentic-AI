# Agentic Platform in Google Cloud Platform (GCP)

A comprehensive framework for developing and deploying AI agents using Google Cloud Platform services and the Agent Development Kit (ADK).

## 🤖 What is Agentic AI?

**Agentic AI** refers to advanced AI systems capable of autonomous **decision-making** and action to achieve specific goals, leveraging underlying models like LLMs to **perceive**, **reason**, and **interact** with tools.

### Generative AI vs Agentic AI

| **Generative AI** | **Agentic AI** |
|-------------------|----------------|
| **Creates** content | **Reasons, Plans, Acts** |
| Write articles, blog posts | Handle employee onboarding & vacation requests |
| Generate images, poems | Diagnose VPN issues and execute troubleshooting |
| Create project plans | Optimize supply chains with real-time data |
| Help with creative tasks | Autonomous business process management |

> **Key Insight**: Agentic AI often uses generative AI as its "brain" to understand and reason, but then extends that capability to perform tasks in dynamic environments.

## 🏗️ Platform Architecture

The Agentic Platform leverages several Google Cloud services:

### Core Components

- **Client**: Frontend interfaces (Web, SDK, API, Agent Builder Pack)
- **Agent Engine**: Managed sessions, memory, code execution, simulation environment
- **Agentspace**: Agent registration and management
- **Observability**: Cloud Trace, Cloud Logging, Cloud Monitoring
- **Evaluation**: Vertex AI Evaluation for agent performance

### Agent Frameworks & Tools

- **Agent Development Kit (ADK)**
- **LangGraph** - Graph-based agent workflows
- **LangChain** - Agent orchestration
- **LlamaIndex** - Data indexing and retrieval
- **CrewAI** - Multi-agent collaboration
- **AG2** - Advanced agent capabilities
- **Other Python frameworks**

### Agent2Agent Communication

- **A2A open protocol** for inter-agent communication
- Built-in tools, custom tools, ecosystem tools
- Google Cloud tools integration
- MCP tools and Open API tools support

### Models

- **Gemini** - Google's flagship LLM
- **Model Garden** - Diverse model selection
- **Fine-tuned models** - Specialized capabilities

## 🚀 Agent Development Kit (ADK)

The **Agent Development Kit (ADK)** is a flexible and modular framework for **developing and deploying AI agents**. ADK can be used with popular **LLMs** and **open-source generative AI tools** and is designed with a focus on **tight integration with the Google ecosystem and Gemini models**.

### Evolution of AI Systems

```
LLM + Prompt → LLM + Retrieval → LLM + Retrieval + Actions → + Many Tools & Reasoning Loop → Multi Agent Systems → Multi Agent Systems with Remote Agents
     ↑                                  ↑                                                              ↑
Generative AI                    Agentic AI                                              Advanced Multi-Agent
```

## 🔧 ADK Core Concepts

### 1. Agent Types

#### BaseAgent
The foundational agent class that can be extended into specialized agent types:

```python
root_agent = LlmAgent(
    model=os.getenv('MODEL_NAME', 'gemini-2.0-flash'),
    name='root_agent',
    instruction="You are a helpful customer support assistant for Example Co."
)
```

#### A. LLM-Based Agents
- **LlmAgent**: Reasoning, Tools, Transfer capabilities

#### B. Workflow Agents
- **SequentialAgent**: Executes sub-agents in order
- **ParallelAgent**: Runs multiple agents concurrently
- **LoopAgent**: Iterative agent execution with refinement

#### C. Custom Logic
- **CustomAgent**: Fully customizable agent behavior

### 2. Sequential Agent

Orchestrates a pipeline by running sub-agents in order:

```python
# This agent orchestrates the pipeline by running the sub_agents in order
code_pipeline_agent = SequentialAgent(
    name='CodePipelineAgent',
    sub_agents=[code_writer_agent, code_reviewer_agent, code_refactorer_agent],
    description="Executes a sequence of code writing, reviewing, and refactoring."
)
# The agents will run in the order provided: Writer -> Reviewer -> Refactorer
```

**Flow**: Input → sub_agent_1 → sub_agent_2 → Output

### 3. Parallel Agent

Runs multiple research agents concurrently and aggregates results:

```python
# This agent orchestrates the concurrent execution of the researchers
parallel_research_agent = ParallelAgent(
    name='ParallelWebResearchAgent',
    sub_agents=[researcher_electric_vehicle, researcher_carbon_emission, researcher_agent_3],
    description="Runs multiple research agents in parallel to gather information."
)
```

**Flow**: Input splits to multiple agents → Output_1, Output_2, Output_3

### 4. Loop Agent

Implements iterative refinement with exit conditions:

```python
refinement_loop = LoopAgent(
    name='RefinementLoop',
    # Agent order is crucial: Critique first, then Refine/Exit
    sub_agents=[
        critic_agent_in_loop,
        refiner_agent_in_loop,
    ],
    max_iterations=5  # Limit loops
)
```

**Flow**: Input → sub_agent_1 → sub_agent_2 → ... → Loop (max_iterations) → Output

### 5. Tools

Tools represent specific capabilities provided to AI agents, enabling them to perform actions and interact with the world beyond core text generation and reasoning abilities.

#### Built-In Tools
Ready-to-use functions:
- Google Search
- Code Execution  
- Vertex AI Search
- Big Query

#### Function Tools
Custom-created for specific needs:

```python
from google.adk.tools import FunctionTool

def get_weather_report(city: str) -> dict:
    """Retrieves the current weather report for a specified city."""
    if city.lower() == "london":
        return {"status": "success", "report": "The current weather in London is cloudy with a temperature of 18 degrees Celsius and a chance of rain."}
    elif city.lower() == "paris":
        return {"status": "success", "report": "The weather in Paris is sunny with a temperature of 25 degrees Celsius."}
    else:
        return {"status": "error", "error_message": f"Weather information for '{city}' is not available."}

weather_tool = FunctionTool(func=get_weather_report)
```

#### Agent-as-a-Tool

Agents can be used as tools by other agents:

```python
summary_agent = Agent(
    model="gemini-2.0-flash",
    name="summary_agent",
    instruction="You are an expert summarizer. Please read the following text and provide a concise summary.",
    description="Agent to summarize text",
)

root_agent = Agent(
    model='gemini-2.0-flash',
    name='root_agent',
    instruction="You are a helpful assistant. When the user provides a text, use the 'summarize' tool to generate a summary. Always forward the user's message exactly as received to the 'summarize' tool, without modifying or summarizing it yourself. Present the response from the tool to the user.",
    tools=[AgentTool(agent=summary_agent)]
)
```

#### Third-Party Tools
Integrate external libraries and services.

### 6. Callbacks

Callbacks provide hooks to observe, debug, customize, and control agent execution:

```python
def my_before_model_logic(
    callback_context: CallbackContext, llm_request: LlmRequest
) -> Optional[LlmResponse]:
    # ... your custom logic here ...
    return None

# --- Register it during Agent creation ---
my_agent = LlmAgent(
    name="MyCallbackAgent",
    model="gemini-2.0-flash",  # Or your desired model
    instruction="Be helpful.",
    # Other agent parameters...
    before_model_callback=my_before_model_logic
)
```

#### Use Cases
- **Observe & Debug**: Log detailed information at critical steps
- **Customize & Control**: Modify data flowing through the agent
- **Implement Guardrails**: Enforce safety rules and validate inputs/outputs
- **Manage State**: Read or update agent's session state dynamically
- **Integrate & Enhance**: Trigger external actions or add caching

## 📊 Key Features

### Observability
- **Cloud Trace**: Request tracing across services
- **Cloud Logging**: Centralized log management  
- **Cloud Monitoring**: Performance metrics and alerting

### Evaluation
- **Vertex AI Evaluation**: Comprehensive agent performance assessment
- Automated testing and validation workflows

### Agent Management
- **Agentspace**: Centralized agent registration and discovery
- Version control and deployment management
- Agent lifecycle management

## 🛠️ Getting Started

### Prerequisites
- Google Cloud Platform account
- Python 3.8+
- Required GCP APIs enabled

### Installation

```bash
pip install google-adk
```

### Quick Start

```python
from google.adk import LlmAgent
import os

# Create a simple agent
agent = LlmAgent(
    model=os.getenv('MODEL_NAME', 'gemini-2.0-flash'),
    name='helpful_assistant',
    instruction="You are a helpful AI assistant."
)

# Use the agent
response = agent.run("Help me understand agentic AI")
print(response)
```

## 📚 Advanced Usage

### Multi-Agent Workflows

```python
# Create specialized agents
researcher = LlmAgent(name='researcher', instruction="Research topics thoroughly")
writer = LlmAgent(name='writer', instruction="Write clear, engaging content")
editor = LlmAgent(name='editor', instruction="Edit and refine content")

# Create a sequential workflow
content_pipeline = SequentialAgent(
    name='ContentPipeline',
    sub_agents=[researcher, writer, editor],
    description="Research, write, and edit content"
)
```

### Custom Tools Integration

```python
from google.adk.tools import FunctionTool

def custom_api_call(query: str) -> dict:
    # Your custom logic here
    return {"result": "processed"}

custom_tool = FunctionTool(func=custom_api_call)
agent_with_tools = LlmAgent(
    name='enhanced_agent',
    tools=[custom_tool],
    instruction="Use tools to enhance responses"
)
```

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guidelines](CONTRIBUTING.md) for details.

## 📄 License

This project is licensed under the Apache 2.0 License - see the [LICENSE](LICENSE) file for details.

## 🔗 Resources

- [Google Cloud AI Platform](https://cloud.google.com/ai-platform)
- [Vertex AI Documentation](https://cloud.google.com/vertex-ai/docs)
- [Agent Development Kit Guides](https://cloud.google.com/adk)
- [Community Examples](https://github.com/googleapis/agentic-examples)

## 📞 Support

For support and questions:
- [GitHub Issues](https://github.com/your-repo/issues)
- [Google Cloud Support](https://cloud.google.com/support)
- [Community Forums](https://cloud.google.com/community)

---

**Built with ❤️ by the Google Cloud AI Platform Team**
