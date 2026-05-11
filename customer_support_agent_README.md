# Customer Support Agent

An intelligent AI-powered customer support agent that automatically classifies, analyzes, and routes email inquiries using LLMs and RAG (Retrieval-Augmented Generation).

## Overview

This notebook implements a sophisticated email triage and response system using:
- **Google Generative AI** (Gemini 2.5 Flash) for classification and analysis
- **Chroma** vector database for document retrieval
- **LangChain** for orchestration and RAG workflows
- **LangGraph** for building a multi-step agent workflow

## Key Features

### 1. Email Classification
Automatically classifies incoming emails based on:
- **Urgency Level**: High, Medium, or Low
- **Topic**: Categories like Accounts, Billing, Password Reset, Technical Issues, etc.
- **Response Category**: Simple, Complex/Unresolved, needs analyst support, etc.
- **Follow-up Requirements**: Boolean indicator for required follow-up actions

### 2. Document Retrieval (RAG)
- Retrieves relevant knowledge base documents using semantic search
- Leverages `GoogleGenerativeAIEmbeddings` for embedding generation
- Uses Chroma as the vector store for efficient similarity search

### 3. Response Generation
- Analyzes retrieved documents in context of the email
- Generates draft responses tailored to the email's urgency and complexity
- Determines if follow-up actions are needed

### 4. Intelligent Escalation
- Automatically escalates high-urgency or complex issues to internal support teams
- Drafts internal escalation emails with:
  - Clear recipient routing
  - Detailed subject lines
  - Comprehensive context and analysis
- Escalation triggers:
  - Urgency = "High"
  - Response category = "Complex/Unresolved" or "needs customer support analyst"

## Architecture

### State Management
The agent uses `AgentState` TypedDict to manage workflow state:
```python
class AgentState(TypedDict):
    email_content: str                          # Input email
    urgency: Optional[EmailClassificationOutput] # Classification results
    analysis: Optional[str]                     # Detailed analysis
    response_draft: Optional[str]               # Generated response
    follow_up_required: Optional[bool]          # Follow-up indicator
    retrieved_docs: Optional[List[Document]]    # RAG documents
    escalation_required: Optional[bool]         # Escalation flag
    escalation_email_draft: Optional[str]       # Escalation email text
```

### Workflow Nodes

1. **classify_urgency**: Classifies email urgency and properties
2. **retrieve_docs**: Fetches relevant documents from knowledge base
3. **escalate_check**: Determines if escalation is needed
4. **draft_escalation_email**: Creates internal escalation email (conditional)
5. **analyze_and_draft**: Generates analysis and response draft (conditional)
6. **follow_up_check**: Confirms follow-up status

### Workflow Flow

```
classify_urgency → retrieve_docs → escalate_check
                                          ↓
                    ┌─────────────────────┴─────────────────────┐
                    ↓                                             ↓
            draft_escalation_email                         analyze_and_draft
                    ↓                                             ↓
                    └─────────────────────┬─────────────────────┘
                                          ↓
                                    follow_up_check
                                          ↓
                                        END
```

## Setup & Installation

### Prerequisites
```bash
# Core dependencies (auto-installed in the notebook)
pip install langchain_google_genai
pip install chromadb
pip install langchain_chroma
pip install langgraph
pip install langchain_community
pip install langchain_text_splitters
```

### Configuration

1. **API Key Setup**:
   ```python
   from google.colab import userdata
   os.environ["GOOGLE_API_KEY"] = userdata.get('GEMINI_API_KEY')
   ```

2. **Knowledge Base**:
   - Place your knowledge base file at `/content/knowledge_base.rtf`
   - The notebook automatically loads and indexes it

3. **Initialize Components**:
   ```python
   llm = ChatGoogleGenerativeAI(model="gemini-2.5-flash")
   embeddings = GoogleGenerativeAIEmbeddings(model="gemini-embedding-001")
   vector_db = Chroma.from_documents(docs, embeddings)
   retriever = vector_db.as_retriever()
   ```

## Usage Example

```python
# Create initial state
initial_state = AgentState(
    email_content="Subject: Server Down\n\nOur production server is down!",
    urgency=None,
    analysis=None,
    response_draft=None,
    follow_up_required=None,
    retrieved_docs=None,
    escalation_required=None,
    escalation_email_draft=None
)

# Run the workflow
final_state = app.invoke(initial_state)

# Access results
print(f"Urgency: {final_state['urgency'].urgency}")
print(f"Topic: {final_state['urgency'].topic}")
print(f"Escalated: {final_state['escalation_required']}")
print(f"Response: {final_state['response_draft']}")
```

## Structured Outputs

### EmailClassificationOutput
```python
class EmailClassificationOutput(BaseModel):
    urgency: str                    # High, Medium, or Low
    topic: str                      # Main topic category
    response_text_category: str     # Type of response needed
    follow_up_required: bool        # Boolean flag
```

### EscalationEmailOutput
```python
class EscalationEmailOutput(BaseModel):
    recipient: str      # Internal team email
    subject: str        # Email subject line
    body: str          # Detailed email body
```

### AnalysisOutput
```python
class AnalysisOutput(BaseModel):
    analysis: str              # Detailed analysis
    response_draft: str        # Draft response text
    follow_up_required: bool   # Follow-up indicator
```

## Key Functions

### Node Functions

- **classify_email_urgency_node()**: Invokes the urgency classification chain
- **rag_node()**: Retrieves relevant documents from the knowledge base
- **escalation_node()**: Determines escalation requirements
- **draft_escalation_email_node()**: Generates escalation emails
- **analyze_and_draft_node()**: Creates analysis and response drafts
- **follow_up_node()**: Confirms follow-up status

## Dependencies

| Package | Version | Purpose |
|---------|---------|---------|
| langchain_google_genai | Latest | Google Gemini integration |
| chromadb | <2.0.0, >=1.3.5 | Vector database |
| langchain_chroma | 1.1.0+ | Chroma wrapper for LangChain |
| langgraph | Latest | Workflow orchestration |
| langchain_community | Latest | Community integrations |
| pydantic | >=2.0 | Data validation |

## Example Outputs

### High Urgency Email (Escalated)
```
Urgency: High
Topic: Technical Issue
Response Category: needs customer support analyst
Escalation Required: True
Escalation Email Draft:
  To: noc@example.com
  Subject: Escalation: HIGH URGENCY - Server Down
  Body: [Detailed escalation context...]
```

### Low Urgency Email (Non-Escalated)
```
Urgency: Low
Topic: Company Event
Response Category: Informational
Escalation Required: False
Response Draft: [Auto-generated customer response...]
Follow-up Required: False
```

## Knowledge Base Format

The knowledge base should be structured with clear sections for different topics (e.g., Technical Issues, Billing, Password Reset, Dark Mode, etc.). The RAG system will semantically match email conten[...]

## Future Enhancements

- [ ] Integration with email systems (IMAP/SMTP)
- [ ] Persistent storage of processed emails
- [ ] Multi-language support
- [ ] Custom escalation routing rules
- [ ] Performance metrics and analytics
- [ ] A/B testing for response templates
- [ ] Feedback loop for model improvements

## Notes

- **API Calls**: Each email invokes multiple LLM calls (classification, analysis, potentially escalation)
- **Latency**: Typical processing time is 5-10 seconds per email
- **Costs**: Consider API costs based on token usage (Gemini 2.5 Flash is cost-effective)
- **Knowledge Base**: Ensure knowledge base is comprehensive for better RAG results
- **Customization**: Modify prompts and output schemas for your specific use case

## Troubleshooting

### Issue: Missing Knowledge Base
**Solution**: Ensure `/content/knowledge_base.rtf` exists and contains valid content

### Issue: API Key Errors
**Solution**: Verify GEMINI_API_KEY is set in Google Colab secrets

### Issue: Poor Response Quality
**Solution**: 
- Update knowledge base with relevant information
- Adjust embedding model if needed
- Modify system prompts for better context

## License

This notebook is part of the prompt_quality_scoring_agent project.

## Author

Created with LangChain, Chroma, and Google Generative AI
