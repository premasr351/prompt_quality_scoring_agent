# Prompt Quality Scoring Agent

A comprehensive system for evaluating and scoring the quality of AI prompts using advanced language models and intelligent agents.

## Overview

This project provides tools to assess prompt quality across multiple dimensions including clarity, specificity, context-awareness, and effectiveness. It leverages LangChain and Google's Generative AI to create intelligent agents that can evaluate, score, and provide feedback on prompts.

## Features

- **Prompt Quality Assessment**: Evaluate prompts across multiple quality dimensions
- **Intelligent Scoring**: Automated scoring system using LLM-based agents
- **Travel Planning Agent**: Example implementation demonstrating a specialized agent for travel-related queries
- **Chat History Management**: Maintains conversation context with summarization capabilities
- **Streaming Responses**: Real-time response streaming for better user experience
- **Google Gemini Integration**: Uses Google's latest Gemini models for powerful language understanding

## Project Structure

```
prompt_quality_scoring_agent/
├── prompt_quality_scoring_agent.ipynb    # Main notebook for prompt quality scoring
├── travel_planning_agent.ipynb          # Example: Travel planning agent implementation
└── README.md                            # This file
```

## Notebooks

### 1. prompt_quality_scoring_agent.ipynb
The main notebook containing the prompt quality scoring system. This notebook demonstrates how to:
- Build prompts that evaluate other prompts
- Create agents for quality assessment
- Score and provide feedback on prompt quality
- Manage agent interactions and responses

### 2. travel_planning_agent.ipynb
An example implementation showing a specialized travel planning agent that:
- Responds only to travel-related questions
- Manages conversation history with context
- Implements automatic summarization every 5 exchanges
- Uses Google's Gemini API for responses
- Provides streaming responses for better UX

## Requirements

- Python 3.8+
- Google Colab (for the notebooks)
- Required Libraries:
  ```
  langchain
  langchain-google-genai
  langchain-core
  google-colab
  ```

## Installation

### For Google Colab

1. Open the notebook in Google Colab
2. Install required packages:
   ```python
   !pip install langchain-google-genai langchain-core
   ```

3. Set up your Google API key:
   - Go to [Google AI Studio](https://makersuite.google.com/app/apikey)
   - Create an API key for Gemini
   - In Colab, add it as a secret named `GEMINI_API_KEY`

### For Local Development

```bash
pip install langchain langchain-google-genai langchain-core
```

## Quick Start

### Travel Planning Agent Example

```python
from langchain_google_genai import ChatGoogleGenerativeAI
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_core.messages import HumanMessage, AIMessage, SystemMessage
import os

# Set API key
os.environ["GOOGLE_API_KEY"] = "your_api_key_here"

# Initialize LLM
llm = ChatGoogleGenerativeAI(model="gemini-2.5-flash")

# Create system prompt
system_prompt = """
You are an expert travel agent.
1. You should only answer travel related questions
2. For non-travel questions, respond with "I can't help with that"
"""

# Use the agent
response = llm.invoke(system_prompt + "How do I get from Athens to Santorini?")
```

## Key Components

### Chat History Management
- Maintains conversation context across multiple turns
- Automatic summarization every 5 user messages
- Preserves full context for continued interactions

### Streaming Responses
- Real-time output as model generates responses
- Better user experience with immediate feedback
- Full response accumulation for storage

### Prompt Templates
- System prompts for specialized behaviors
- Message placeholders for dynamic content
- Support for multi-turn conversations

## Usage Examples

### Basic Interaction
```python
chat_history = []
response = get_llm_response("Your question here", chat_history)
```

### Multiple Turns
```python
chat_history = []
get_llm_response("What's a good beach destination?", chat_history)
get_llm_response("Tell me about Greece.", chat_history)
get_llm_response("How do I get there?", chat_history)
# Automatically summarizes at 5 messages
```

## Configuration

### Models
- Primary: `gemini-2.5-flash` (recommended for speed and efficiency)
- Alternative: `gemini-pro` (for more complex tasks)

### Summarization
- Triggered every 5 user messages
- Customizable via the modulo operation in `get_llm_response`

## API Reference

### get_llm_response(user_message, history)
Processes user input and returns LLM response with streaming.

**Parameters:**
- `user_message` (str): The user's input message
- `history` (list): Current chat history

**Returns:**
- (str): Full accumulated response content

**Side Effects:**
- Updates global `chat_history` with user and AI messages
- Triggers summarization every 5 user messages

## Best Practices

1. **API Key Management**: Never hardcode API keys; use environment variables or secrets
2. **Chat History**: Monitor history length for large conversations
3. **Summarization**: Configure summarization frequency based on use case
4. **Streaming**: Beneficial for real-time applications but ensure proper buffering
5. **Error Handling**: Implement try-catch blocks for API calls

## Troubleshooting

### "API key not found"
- Verify API key is set in environment variables
- Check Google Colab secrets are properly configured

### Rate Limiting
- Add delays between requests
- Implement exponential backoff for retries

### Memory Issues
- Clear chat history periodically
- Reduce history window size for long conversations

## Contributing

Feel free to fork this repository and submit pull requests for:
- Additional prompt quality dimensions
- New specialized agents
- Improved evaluation metrics
- Performance optimizations

## License

This project is open source and available under the MIT License.

## Resources

- [LangChain Documentation](https://python.langchain.com/)
- [Google Generative AI](https://ai.google.dev/)
- [Google Colab](https://colab.research.google.com/)
- [Gemini API Documentation](https://ai.google.dev/api/rest)

## Author

Created by premasr351

## Acknowledgments

- Built with [LangChain](https://www.langchain.com/)
- Powered by [Google Gemini](https://ai.google.dev/)
- Developed in [Google Colab](https://colab.research.google.com/)
