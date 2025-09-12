Integration of a Mathematical Calculations with a Chat Completion System using LLM Function-Calling
AIM:

To design and implement a Python function for calculating the volume of a cylinder, and integrate it with a chat completion system utilizing the function-calling feature of a large language model (LLM).

PROBLEM STATEMENT:

Users often ask natural-language questions about geometry and volume. Instead of manually writing parsing logic, we can integrate a cylinder volume calculator function with an LLM. The LLM decides when to call the function, extracts the parameters (radius, height), and produces a clear, natural-language response.

DESIGN STEPS:
STEP 1:

Import required libraries (openai, os, dotenv) and set up the API key.

STEP 2:

Define the Python function cylinder_volume(radius, height) that calculates the volume of a cylinder.

STEP 3:

Create a JSON schema describing the function for the LLM (name, description, parameters, required fields).

STEP 4:

Pass user queries as messages to the ChatCompletion API, making the function available.

STEP 5:

Check if the response contains a function call. If yes, extract arguments, call the Python function, and append the result to the conversation.

STEP 6:

Ask the model again with the function result so it can reply in natural language.

STEP N’s:

Test with different queries — both mathematical (to trigger the function) and non-mathematical (to get a normal reply).

PROGRAM:
import os
import json
import openai
from dotenv import load_dotenv, find_dotenv

# Load API key
_ = load_dotenv(find_dotenv())
openai.api_key = os.environ["OPENAI_API_KEY"]

# Step 1: Define the function
def cylinder_volume(radius, height):
    """Calculate the volume of a cylinder"""
    volume = 3.14159 * (radius ** 2) * height
    return volume

# Step 2: Define function schema
functions = [
    {
        "name": "cylinder_volume",
        "description": "Calculate the volume of a cylinder given radius and height",
        "parameters": {
            "type": "object",
            "properties": {
                "radius": {"type": "number", "description": "Radius of the cylinder"},
                "height": {"type": "number", "description": "Height of the cylinder"}
            },
            "required": ["radius", "height"]
        }
    }
]

# Step 3: User query
messages = [{"role": "user", "content": "What is the volume of a cylinder with radius 5 and height 10?"}]

# Step 4: First call to model
response = openai.ChatCompletion.create(
    model="gpt-4-0613", 
    messages=messages,
    functions=functions,
    function_call="auto"
)

response_message = response["choices"][0]["message"]

# Step 5: Handle function call
if "function_call" in response_message:
    args = json.loads(response_message["function_call"]["arguments"])
    result = cylinder_volume(**args)

    # Add function call and result back into conversation
    messages.append(response_message)
    messages.append({
        "role": "function",
        "name": response_message["function_call"]["name"],
        "content": str(result),
    })

    # Step 6: Second call to get natural reply
    second_response = openai.ChatCompletion.create(
        model="gpt-4-0613",
        messages=messages,
    )
    print(second_response["choices"][0]["message"]["content"])
else:
    print("Model reply:", response_message.get("content"))

## OUTPUT:

Example run in Jupyter might give:

The volume of a cylinder with radius 5 and height 10 is approximately 785.4 cubic units.

## RESULT:

The Python function for cylinder volume calculation was successfully integrated with the chat completion system using LLM function-calling. The system identifies when to call the function, executes it, and provides a natural-language response.

The Python function for cylinder volume calculation was successfully integrated with the chat completion system using LLM function-calling. The system correctly identifies when to call the function, executes it, and provides a natural-language response.
