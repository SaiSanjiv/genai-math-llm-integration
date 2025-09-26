## Integration of a Mathematical Calulations with a Chat Completion System using LLM Function-Calling

### AIM:
To design and implement a Python function for calculating the Area of Rectangle and integrate it with a chat completion system utilizing the function-calling feature of a large language model (LLM).

### PROBLEM STATEMENT: 
Design and implement a Python function to calculate the Area of Rectangle and integrate it with an LLM-powered chat system using function-calling. 

### DESIGN STEPS:

#### STEP 1: Load environment variables and set up the OpenAI API key for authentication.

#### STEP 2: Implement calculate_rectangle_area(base, height), which computes and returns the volume in JSON format.

#### STEP 3: Create a function definition in JSON format (functions list) to enable OpenAI's function-calling capability.

#### STEP 4: Send a user’s request to the OpenAI chat model (ChatCompletion.create) along with function metadata.

#### STEP 5:  If the model invokes calculate_rectangle_area, extract arguments, compute the result, append it to the conversation, and send a final request to get the AI's response.

### PROGRAM:
```
import os
import openai

from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv()) # read local .env file
openai.api_key = os.environ['OPENAI_API_KEY']

import json

def calculate_rectangle_area(length, width):
    area = length * width
    return json.dumps({
        "length": length,
        "width": width,
        "area": area
    })

functions = [
    {
        "name": "calculate_rectangle_area",
        "description": "Calculate the area of a rectangle given length and width",
        "parameters": {
            "type": "object",
            "properties": {
                "length": {
                    "type": "number",
                    "description": "The length of the rectangle"
                },
                "width": {
                    "type": "number",
                    "description": "The width of the rectangle"
                },
            },
            "required": ["length", "width"],
        },
    }
]

messages = [
    {
        "role": "user",
        "content": "What is the area of a rectangle with length 10 and width 5?",
    }
]
# Call the ChatCompletion endpoint
response = openai.ChatCompletion.create(
    # OpenAI Updates: As of June 2024, we are now using the GPT-3.5-Turbo model
    model="gpt-3.5-turbo",
    messages=messages,
    functions=functions
)
response_message = response["choices"][0]["message"]
response_message["content"]
json.loads(response_message["function_call"]["arguments"])
args = json.loads(response_message["function_call"]["arguments"])
calculate_rectangle_area(args["length"], args["width"])
messages = [
    {
        "role": "user",
        "content": "What is the area of a rectangle with length 10 and width 5?",
    }
]
response = openai.ChatCompletion.create(
    # OpenAI Updates: As of June 2024, we are now using the GPT-3.5-Turbo model
    model="gpt-3.5-turbo",
    messages=messages,
    functions=functions,
    function_call="none",
)
print(response)
```

### OUTPUT:

<img width="1078" height="789" alt="image" src="https://github.com/user-attachments/assets/2d1dbd4d-54ee-4316-ad01-3fa21a742616" />

### RESULT: 
The code enables LLM-driven Area of Rectangle calculation via function calling.
