## Development and Deployment of a 'Chat with LLM' Application Using the Gradio Blocks Framework

### AIM:
To design and deploy a "Chat with LLM" application by leveraging the Gradio Blocks UI framework to create an interactive interface for seamless user interaction with a large language model.

### PROBLEM STATEMENT:
Large Language Models (LLMs) enable natural language conversations between humans and AI systems. The problem is to develop an application that allows users to interact with an LLM through a simple and user-friendly interface using the Gradio Blocks framework, enabling real-time question answering and conversational responses.

### DESIGN STEPS:

### STEP 1:
Install the necessary libraries such as gradio and the required LLM API or model dependencies, and configure the environment for the application.

### STEP 2:
Create a Python function that takes user input messages and sends them to the LLM to generate appropriate responses.

### STEP 3:
Build an interactive chat interface using the Gradio Blocks framework to display user messages and model responses in a conversational format.

### PROGRAM:
```
import os
import io
import gradio as gr
import IPython.display
from PIL import Image
import base64 
import requests 
requests.adapters.DEFAULT_TIMEOUT = 60

from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv()) # read local .env file
hf_api_key = os.environ['HF_API_KEY']

# Helper function
import requests, json
from text_generation import Client

#FalcomLM-instruct endpoint on the text_generation library
client = Client(os.environ['HF_API_FALCOM_BASE'], headers={"Authorization": f"Basic {hf_api_key}"}, timeout=120)

def format_chat_prompt(message, chat_history):
    prompt = ""
    for turn in chat_history:
        user_message, bot_message = turn
        prompt = f"{prompt}\nUser: {user_message}\nAssistant: {bot_message}"
    prompt = f"{prompt}\nUser: {message}\nAssistant:"
    return prompt

def respond(message, chat_history):
        formatted_prompt = format_chat_prompt(message, chat_history)
        bot_message = client.generate(formatted_prompt,
                                     max_new_tokens=1024,
                                     stop_sequences=["\nUser:", "<|endoftext|>"]).generated_text
        chat_history.append((message, bot_message))
        return "", chat_history

with gr.Blocks() as demo:
    chatbot = gr.Chatbot(height=500) 
    msg = gr.Textbox(label="Prompt")
    btn = gr.Button("Submit")
    clear = gr.ClearButton(components=[msg, chatbot], value="Clear console")

    btn.click(respond, inputs=[msg, chatbot], outputs=[msg, chatbot])
    msg.submit(respond, inputs=[msg, chatbot], outputs=[msg, chatbot]) #Press enter to submit

gr.close_all()
demo.launch(share=True, server_port=7868)
```
### OUTPUT:

### RESULT:
The application successfully enables users to interact with a large language model through a Gradio Blocks interface, providing real-time conversational responses and demonstrating an effective chat-based AI system.
