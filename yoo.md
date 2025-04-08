# AI-Driven Customer Support System

A comprehensive multi-agent system for automating and enhancing customer support interactions through AI-powered analysis, recommendation, and task management.

## Table of Contents

1. [System Overview](#system-overview)
2. [Architecture](#architecture)
3. [Complete Workflow](#complete-workflow)
4. [Detailed Component Descriptions](#detailed-component-descriptions)
5. [Technical Implementation](#technical-implementation)
6. [Example Interactions](#example-interactions)
7. [Setup and Deployment](#setup-and-deployment)
8. [Troubleshooting](#troubleshooting)

## System Overview

The AI-Driven Customer Support System is a sophisticated platform designed to streamline customer support operations by leveraging multiple specialized AI agents working in concert. This system:

- Processes customer inquiries and support requests
- Automatically classifies user intent and determines the appropriate response strategy
- Extracts actionable items from conversations
- Generates concise summaries of customer issues
- Recommends solutions based on historical data
- Determines optimal team routing
- Estimates resolution time
- Stores conversation data in a Supabase database for analytics and reference

The system uses a Streamlit-based interface and Ollama's local LLM implementation to provide a seamless experience while maintaining data privacy and control.

## Architecture

The system follows a modular architecture with several key components:

![System Architecture Diagram]

### Core Components

1. **Web Interface (Streamlit)**: User-facing interface for customer support interactions
2. **Multi-Agent System**: Collection of specialized AI agents for different aspects of support
3. **Supabase Database**: Storage for ticket data, conversation history, and analytics
4. **Ollama Integration**: Local LLM engine powering the AI agents
5. **Utility Modules**: Shared functions for conversation processing and data handling

## Complete Workflow

The complete lifecycle of a customer interaction follows these steps:

### 1. System Initialization
- The system starts with `run.py`, which:
  - Checks for required dependencies
  - Verifies Ollama is running with the required models
  - Sets up sample data if needed
  - Launches the Streamlit application

### 2. User Interface Loading
- The Streamlit app (`app.py`) initializes:
  - Creates a connection to the Supabase database
  - Initializes all the agent classes
  - Sets up the user interface with chat and insights panels

### 3. Customer Interaction
- When a customer sends a message:
  - The message is added to the conversation history
  - The IntentClassifierAgent determines the intent and nature of the message

### 4. Intent-based Processing
- Based on the classification:
  - **Simple queries** (greetings, farewells, casual chat) are handled directly with appropriate responses
  - **Support issues** trigger the full agent pipeline

### 5. Full Agent Pipeline (for support issues)
- **Summary Generation**: SummaryAgent creates a concise summary of the conversation
- **Action Extraction**: ActionAgent identifies specific actions needed to resolve the issue
- **Recommendation Generation**: RecommendationAgent suggests solutions based on the issue and historical data
- **Team Routing**: RoutingAgent determines which team(s) should handle the issue
- **Time Estimation**: TimeEstimationAgent estimates the resolution time

### 6. Response Generation
- A customer-facing response is generated based on the agents' outputs
- The response prioritizes actionable recommendations

### 7. Database Storage
- The complete ticket data is saved to the Supabase database:
  - Conversation history
  - Agent-generated insights (summary, actions, recommendations, etc.)
  - Metadata (timestamps, status)

### 8. Insight Display
- The agent insights panel updates with:
  - Conversation summary
  - Action items
  - Recommended solutions
  - Team routing information
  - Resolution time estimate

### 9. Ongoing Interaction
- As the conversation continues, the process repeats from step 3
- Previous context is maintained and agents build upon earlier insights

### 10. Ticket Resolution
- Support agents can update the ticket status (Open, In Progress, Resolved)
- Resolution time is recorded for analytics

## Detailed Component Descriptions

### Agent System

Each agent specializes in a specific aspect of customer support:

#### IntentClassifierAgent (`agents/intent_classifier_agent.py`)
- **Purpose**: Determines the type and intent of customer messages
- **Functionality**:
  - Classifies messages into categories: issue, greeting, casual, farewell, unknown
  - Determines if a message requires the full agent pipeline
  - Provides confidence scores for classifications
  - Generates casual responses for non-issue messages
- **Example**: For "Hi, I'm having trouble logging into my account", it would classify this as an "issue" with high confidence, requiring the full agent pipeline

#### SummaryAgent (`agents/summary_agent.py`)
- **Purpose**: Creates concise summaries of customer conversations
- **Functionality**:
  - Generates 3-5 sentence summaries focusing on key issues
  - Updates existing summaries with new information
- **Example**: "Customer is unable to log into their account after a recent password change. They've tried resetting their password twice but aren't receiving the reset email. This has been ongoing for 2 days and is preventing them from accessing important documents."

#### ActionAgent (`agents/action_agent.py`)
- **Purpose**: Extracts specific actionable items from conversations
- **Functionality**:
  - Identifies clear, specific actions needed to resolve the issue
  - Can prioritize actions by urgency and impact
- **Example Actions**:
  - "Check email delivery system for errors with password reset emails"
  - "Verify the customer's email address is correct in the system"
  - "Manually generate a password reset link for the customer"

#### RecommendationAgent (`agents/recommendation_agent.py`)
- **Purpose**: Suggests solutions based on the current issue and historical data
- **Functionality**:
  - Retrieves similar past tickets from the database
  - Generates 2-3 specific, actionable recommendations
  - Can evaluate the likely effectiveness of recommendations
- **Example Recommendations**:
  - "Send a manual password reset link to the customer's alternative email address"
  - "Guide the customer to check their spam/junk folder for the reset emails"
  - "Temporarily set a password for the account and provide it via an alternate verification method"

#### RoutingAgent (`agents/routing_agent.py`)
- **Purpose**: Determines the optimal team(s) to handle the issue
- **Functionality**:
  - Identifies the primary team responsible for resolution
  - Suggests additional teams if cross-department collaboration is needed
  - Can evaluate routing accuracy
- **Example Routing**:
  - Primary Team: Account Management
  - Additional Teams: Technical Engineering

#### TimeEstimationAgent (`agents/time_agent.py`)
- **Purpose**: Estimates how long it will take to resolve the issue
- **Functionality**:
  - Uses historical resolution time data for similar issues
  - Considers complexity, action items, and team assignments
  - Provides time estimates with confidence levels
- **Example Estimate**:
  - "Estimated Resolution Time: 2-4 hours
    Confidence Level: Medium
    Factors: Similar authentication issues typically resolved within this timeframe, depends on email system status"

### Base Agent Class (`agents/base_agent.py`)
- **Purpose**: Provides common functionality for all agents
- **Functionality**:
  - Handles communication with the LLM (Ollama)
  - Implements retry logic for failed requests
  - Provides logging capabilities

### Database Integration (`database/supabase_client.py`)
- **Purpose**: Manages all database interactions
- **Functionality**:
  - Saves ticket data (conversation, agent outputs)
  - Retrieves historical ticket data for reference
  - Updates ticket status
  - Provides similar tickets for recommendation generation
  - Retrieves resolution time data for time estimation

### Conversation Utilities (`utils/conversation_utils.py`)
- **Purpose**: Provides helper functions for conversation processing
- **Functionality**:
  - Formats conversation history for agent consumption
  - Extracts key points from conversations
  - Calculates sentiment
  - Extracts conversation metadata
  - Validates ticket data

### Web Interface (`app.py`)
- **Purpose**: Provides the user-facing interface
- **Functionality**:
  - Displays conversation history
  - Collects user input
  - Shows agent insights
  - Displays performance metrics
  - Allows ticket status updates

### System Initialization (`run.py`)
- **Purpose**: Handles application setup and startup
- **Functionality**:
  - Checks dependencies
  - Verifies Ollama installation
  - Sets up sample data
  - Launches the Streamlit application

## Technical Implementation

### LLM Integration

The system uses Ollama's implementation of Llama 3 (llama3.2:1b) for all AI operations. Each agent communicates with the LLM through the BaseAgent's `query_llm` method, which:

1. Formats the appropriate prompt
2. Sends a request to the Ollama API
3. Processes the response
4. Implements retry logic in case of failures

Example LLM query:
```python
prompt = f"""
You are a customer support summary specialist. Your task is to create a concise, 
factual summary of the following customer support conversation. 
Focus on the main customer issue, relevant details, and current status.

Conversation:
{conversation}

Create a brief, objective summary (3-5 sentences) that captures the essence of this conversation:
"""

response = self.query_llm(prompt)
```

### Database Structure

The Supabase database uses a table called `Historical_ticket_data` with the following key fields:

- `ticket_id`: Unique identifier for the ticket
- `issue_category`: Brief categorization of the issue
- `sentiment`: Customer sentiment analysis
- `priority`: Issue priority (Critical, High, Medium, Low)
- `solution`: Implemented or recommended solution
- `resolution_status`: Current status (Open, In Progress, Resolved)
- `date_of_resolution`: When the ticket was resolved

### Conversation Flow

Conversations are stored in the session state as a list of message dictionaries:

```python
{
    "role": "user" or "assistant",
    "content": "Message text",
    "timestamp": "YYYY-MM-DD HH:MM:SS"
}
```

## Example Interactions

### Example 1: Login Issue

**Customer**: "I can't log into my account. I've tried resetting my password three times but I'm not getting the reset email."

**System Process**:
1. IntentClassifierAgent identifies this as an "issue" requiring full processing
2. SummaryAgent generates a summary: "Customer is unable to login to their account and is not receiving password reset emails after multiple attempts."
3. ActionAgent extracts actions:
   - "Check email delivery system for reset emails"
   - "Verify customer's email address in the system"
   - "Manually generate a password reset link"
4. RecommendationAgent provides recommendations:
   - "Send a manual password reset link to the customer's email"
   - "Check spam/junk folders for the reset emails"
5. RoutingAgent determines routing:
   - Primary Team: Account Management
   - Additional Teams: Technical Engineering
6. TimeEstimationAgent estimates:
   - "Estimated Resolution Time: 1-2 hours
     Confidence Level: High
     Factors: Common issue with established resolution process"

**Agent Response**: "I understand you're having trouble logging in and aren't receiving the password reset emails. Let me help you with that. First, please check your spam or junk folders for the reset emails. In the meantime, I'll send a manual password reset link to your registered email address. If that doesn't work, we can try an alternative verification method."

### Example 2: Billing Inquiry

**Customer**: "I was charged twice for my monthly subscription. Can you refund one of the charges?"

**System Process**:
1. IntentClassifierAgent identifies this as an "issue" requiring full processing
2. SummaryAgent generates a summary: "Customer reports being charged twice for their monthly subscription and is requesting a refund for the duplicate charge."
3. ActionAgent extracts actions:
   - "Verify duplicate charges in billing system"
   - "Process refund for duplicate charge"
   - "Check for system errors causing duplicate charges"
4. RecommendationAgent provides recommendations:
   - "Issue a refund for the duplicate charge"
   - "Provide confirmation number once refund is processed"
5. RoutingAgent determines routing:
   - Primary Team: Billing Department
   - Additional Teams: None
6. TimeEstimationAgent estimates:
   - "Estimated Resolution Time: 24-48 hours
     Confidence Level: Medium
     Factors: Refund processing typically takes 1-2 business days"

**Agent Response**: "I apologize for the duplicate charge on your monthly subscription. I'll verify the charges and process a refund for the duplicate payment right away. Refunds typically take 24-48 hours to appear on your account. I'll provide you with a confirmation number once the refund is processed."

## Setup and Deployment

### Prerequisites

- Python 3.8+
- Ollama installed and running with llama3.2:1b model
- Supabase account (for database functionality)
- Required Python packages (see dependencies in `run.py`)

### Installation Steps

1. Clone the repository
2. Install dependencies:
   ```
   pip install -r requirements.txt
   ```
3. Ensure Ollama is running with the llama3.2:1b model:
   ```
   ollama pull llama3.2:1b
   ollama run llama3.2:1b
   ```
4. Run the setup script:
   ```
   python run.py
   ```
5. Access the application at http://localhost:8501

## Troubleshooting

### Common Issues

1. **"Error checking Ollama"**
   - Ensure Ollama is installed and running
   - Verify the llama3.2:1b model is installed with `ollama list`

2. **"Error initializing Supabase client"**
   - Check your Supabase credentials
   - Verify internet connection

3. **Slow response times**
   - The llama3.2:1b model is optimized for performance on consumer hardware
   - Consider using a more powerful machine for production deployments

4. **Agent errors**
   - Check the console logs for specific error messages
   - Ensure all dependencies are correctly installed

### Performance Optimization

- For faster response times, consider:
  - Using a more powerful local machine
  - Implementing response caching for common queries
  - Using a smaller LLM for intent classification and a larger one for complex reasoning

---

## System Lifecycle Overview

Here's a step-by-step walkthrough of how the system handles a customer support interaction:

1. **System Startup**
   - `run.py` verifies the environment and starts the Streamlit app
   - Agents and database connections are initialized

2. **Customer Interaction**
   - Customer enters their issue in the chat interface
   - Message is added to conversation history
   - IntentClassifierAgent determines if it's a support issue

3. **Issue Processing**
   - For support issues, all agents work in sequence to:
     - Summarize the issue
     - Extract required actions
     - Generate recommendations
     - Determine routing
     - Estimate resolution time

4. **Response Generation**
   - System generates a response based on agent outputs
   - Response is added to conversation history
   - UI refreshes to show the updated conversation

5. **Agent Insights Update**
   - Right panel updates with all agent outputs
   - Support staff can view the summary, actions, recommendations, etc.

6. **Database Storage**
   - Ticket data is saved to the Supabase database
   - Data becomes available for future reference and analytics

7. **Ongoing Interaction**
   - Process repeats for each customer message
   - Agents maintain context across the conversation

8. **Ticket Resolution**
   - Support staff updates ticket status when resolved
   - Resolution time is recorded for performance metrics

This workflow combines the strengths of different specialized AI agents to provide comprehensive, efficient customer support while maintaining a clean, intuitive interface for both customers and support staff.
