# LLM Fundamentals

This repository contains my hands-on practice with LLM API integration,
environment setup, prompting, structured output, and multi-turn AI
conversations.

---

## 📁 Project Structure

```text
LLM Fundamental/
│
├── venv/
│   └── Python virtual environment
│
├── .env
│   └── Stores the Gemini API key
|-- .gitignore - .env
                 venv/
│
└── llm_basics.py
    └── LLM learning and experimentation code

1. venv/
venv is the Python virtual environment created specifically for this
project.
It keeps the project's Python packages isolated from the system-wide
Python installation.

2. .env
The .env file is used to store sensitive configuration values such as
the Gemini API key.
Example:
GEMINI_API_KEY=your_api_key_here
Important: Never upload your actual API key to GitHub.
Add .env to .gitignore.

3. .gitignore
    inside this we need to write .env
                                 venv/

4. llm_basics.py
This is the main learning file of the project.
Different LLM concepts and experiments are being implemented and practiced
in this file.
-llm callin
- structured output
- multiturn conversation
- q/a from given content
- 2 way communication (ai-you , ai-you)



⚙️ Project Setup
1. Check Python Installation
Make sure Python is installed:
python --version
Example:
Python 3.x.x

2. Create the Project Folder
Create a folder for the project:
LLM Fundamental
Open the folder in VS Code.
🐍 Creating a Virtual Environment
A virtual environment provides an isolated Python environment for the
project.

Create it using:
python -m venv venv

This creates a folder named:
venv/

The packages installed inside this environment are isolated from the
global Python installation.

▶️ Activating the Virtual Environment
Windows PowerShell

Activate the environment using:
venv\Scripts\activate

After successful activation, the terminal will show:
(venv)
before the current directory.

Example:
(venv) PS C:\Users\Admin\Desktop\llm-fundamental>

This indicates that the virtual environment is active.

⚠️ PowerShell Execution Policy Issue

If PowerShell displays an error saying that script execution is disabled,
the activation script may be blocked by the execution policy.

Run:
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

Then activate the environment again:
venv\Scripts\activate
Temporary Alternative

If you don't want to change the execution policy permanently, you can use:

Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass

Then:

venv\Scripts\activate

The Process scope applies only to the current PowerShell session.

📦 Installing Required Packages
The project uses the Google Gemini Python SDK.

Install it using:
pip install google-genai

Install python-dotenv for loading environment variables:
pip install python-dotenv

Install Pydantic for defining structured output schemas:
pip install pydantic

You can also install everything together:
pip install google-genai python-dotenv pydantic

🔐 Setting Up the API Key

Create a file named:
.env

inside the project folder.
Add your Gemini API key:
GEMINI_API_KEY=your_api_key_here

The API key is loaded in Python using:

from dotenv import load_dotenv
import os
load_dotenv()
api_key = os.getenv("GEMINI_API_KEY")

The Gemini client is then created using:

from google import genai

client = genai.Client(api_key=api_key)
🚨 Protecting the API Key

Never commit your .env file to GitHub.

Create a .gitignore file in the project folder and add:

.env
venv/
__pycache__/

This prevents sensitive files and unnecessary project files from being
uploaded to GitHub.

🤖 LLM Basics

The llm_basics.py file contains different experiments performed while
learning the fundamentals of working with LLMs.

1. Calling an LLM

The first experiment was to make a basic request to a Gemini model.

The Gemini client is created using:

client = genai.Client(api_key=api_key)

A prompt can then be sent using:

response = client.models.generate_content(
    model="gemini-3.6-flash",
    contents="What is the square root of 49?"
)

print(response.text)

The basic flow is:

Python Application
       ↓
Gemini API
       ↓
Gemini Model
       ↓
Generated Response
       ↓
Python Program

This demonstrates how a Python application can communicate with an LLM
through an API.

2. Prompting

A prompt is the instruction or input provided to the LLM.

For example:

response = client.models.generate_content(
    model="gemini-3.6-flash",
    contents="Explain machine learning in simple words."
)

print(response.text)

The contents parameter contains the prompt given to the model.

Prompting allows us to control what we want the model to understand,
extract, generate, or explain.

3. Structured Output

LLMs normally return natural-language responses.

For example:

Aarushi Chouhan is an AI/ML Engineer working at Batchmaster...

However, applications often need predictable and machine-readable data.

Structured output allows the model to return information in a predefined
format such as JSON.

A Pydantic model was created to define the required structure:

class Employee(BaseModel):
    name: str
    company: str
    role: str
    experience: int
    department: str
    project: str

The schema is then provided to Gemini:

response = client.models.generate_content(
    model="gemini-3.6-flash",
    contents="""
    Extract the employee information from the following text:

    "Aarushi Chouhan joined the company Batchmaster as an AI/ML Engineer.
    She has 1 year of experience and works in the Technology department.
    Her current project is related to Generative AI."
    """,
    config=types.GenerateContentConfig(
        response_mime_type="application/json",
        response_schema=Employee,
    ),
)

The response can then be converted into Python data:

data = json.loads(response.text)

And displayed as formatted JSON:

print(json.dumps(data, indent=4))

Example output:

{
    "name": "Aarushi Chouhan",
    "company": "Batchmaster",
    "role": "AI/ML Engineer",
    "experience": 1,
    "department": "Technology",
    "project": "Generative AI"
}
Why Structured Output?

Structured output is useful when the response needs to be consumed by
another part of an application.

Examples include:

Employee information extraction
Invoice data extraction
Product information
Classification results
Form data
ERP data processing
API responses

The main idea is:

Unstructured Text
       ↓
      LLM
       ↓
Structured JSON
       ↓
Python Application
4. Asking Questions About Structured Data

After extracting the employee information into JSON, the structured data
can be passed into another prompt.

For example:

question = input("Ask something about the employee: ")

response = client.models.generate_content(
    model="gemini-3.6-flash",
    contents=f"""
    Here is the employee information:

    {json.dumps(data, indent=4)}

    Answer the user's question using only the employee information
    provided above.

    User question:
    {question}
    """
)

print("AI:", response.text)

The f before the triple quotes creates a Python formatted string.

It allows variables such as:

{data}

and:

{question}

to be inserted directly into the prompt.

For example:

You: Who is Aarushi?

AI: Aarushi Chouhan is an AI/ML Engineer at Batchmaster.
5. Multi-Turn / One-on-One AI Conversation

Another experiment was creating a chat session with Gemini.

chat = client.chats.create(
    model="gemini-3.6-flash"
)

Messages can then be sent one after another:

response = chat.send_message("My name is Aarushi.")
print("AI:", response.text)

response = chat.send_message("What is my name?")
print("AI:", response.text)

Example:

AI: Hello Aarushi! It's lovely to meet you.
AI: Your name is Aarushi!

The important concept here is that both messages are sent through the
same chat session.

6. Interactive AI Conversation

Instead of manually writing every question in the Python code, an
interactive loop can be used.

while True:

    question = input("\nYou: ")

    if question.lower() == "exit":
        print("Chat ended.")
        break

    response = chat.send_message(question)

    print("AI:", response.text)

This allows continuous interaction:

You: My favorite food is dosa.

AI: That's great! Which dosa do you like?

You: I like rava dosa.

AI: Nice choice!

You: What is my favorite food?

AI: Your favorite food is dosa.

You: exit

Chat ended.

The while True loop keeps the conversation running until the user
types:

exit
🧠 Concepts Practiced

Through this project, the following LLM concepts are being explored:

LLM API Integration

Connecting a Python application to a Gemini model through the Gemini API.

Prompting

Sending instructions and questions to an LLM.

Structured Output

Getting predictable JSON output according to a predefined schema.

Pydantic

Using Python models to define the expected structure of LLM responses.

JSON

Converting LLM responses into machine-readable data.

Environment Variables

Keeping API keys outside the Python source code using .env.

Virtual Environments

Isolating project dependencies using Python venv.

Multi-Turn Conversations

Maintaining a conversation through a Gemini chat session.

Interactive Chat

Using Python's input() and while loop to create a basic terminal
chat interface.

🔄 Overall Learning Flow

The project currently follows this progression:

Python Setup
     ↓
Virtual Environment
     ↓
Gemini API Setup
     ↓
Calling an LLM
     ↓
Prompting
     ↓
Structured Output
     ↓
JSON Processing
     ↓
Question Answering
     ↓
Multi-Turn Conversation
     ↓
Interactive AI Chat
🚀 Future Learning

The next concepts planned for this project include:

Tool Calling / Function Calling
Embeddings
Vector Databases
RAG (Retrieval-Augmented Generation)
LangChain
LangGraph
FastAPI integration
Connecting LLMs with databases
Building AI features for ERP applications
🛠️ Technologies Used
Python
Google Gemini API
Google GenAI Python SDK
Pydantic
python-dotenv
VS Code
Git & GitHub
📌 Notes

This repository is a hands-on learning project created to understand the
fundamentals of integrating and working with Large Language Models using
Python.

The code is intentionally kept simple and incremental so that each
concept can be understood independently before moving to more advanced
LLM application development.


### One thing I strongly recommend before pushing it to GitHub

Your repository should look like:

```text
LLM Fundamental/
│
├── .gitignore
├── .env                 ❌ DO NOT PUSH
├── venv/                ❌ DO NOT PUSH
├── llm_basics.py
└── README.md

Your .gitignore should contain:

.env
venv/
__pycache__/

Do not put your actual API key anywhere in the README or llm_basics.py. Use a placeholder such as your_api_key_here in documentation.
