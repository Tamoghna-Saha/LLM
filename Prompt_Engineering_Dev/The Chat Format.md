The Chat Format

In this notebook, you will explore how you can utilize the chat format to have extended conversations with chatbots personalized or specialized for specific tasks or behaviors.
Setup

import os

import openai

from dotenv import load_dotenv, find_dotenv

_ = load_dotenv(find_dotenv()) # read local .env file

​

openai.api_key  = os.getenv('OPENAI_API_KEY')

def get_completion(prompt, model="gpt-3.5-turbo"):

    messages = [{"role": "user", "content": prompt}]

    response = openai.ChatCompletion.create(

        model=model,

        messages=messages,

        temperature=0, # this is the degree of randomness of the model's output

    )

    return response.choices[0].message["content"]

​

def get_completion_from_messages(messages, model="gpt-3.5-turbo", temperature=0):

    response = openai.ChatCompletion.create(

        model=model,

        messages=messages,

        temperature=temperature, # this is the degree of randomness of the model's output

    )

#     print(str(response.choices[0].message))

    return response.choices[0].message["content"]

messages =  [  

{'role':'system', 'content':'You are an assistant that speaks like Shakespeare.'},    

{'role':'user', 'content':'tell me a joke'},   

{'role':'assistant', 'content':'Why did the chicken cross the road'},   

{'role':'user', 'content':'I don\'t know'}  ]

response = get_completion_from_messages(messages, temperature=1)

print(response)

To get to the other side, good sir!

messages =  [  

{'role':'system', 'content':'You are friendly chatbot.'},    

{'role':'user', 'content':'Hi, my name is Isa'}  ]

response = get_completion_from_messages(messages, temperature=1)

print(response)

Hi Isa, it's nice to meet you! How can I assist you today?

messages =  [  

{'role':'system', 'content':'You are friendly chatbot.'},    

{'role':'user', 'content':'Yes,  can you remind me, What is my name?'}  ]

response = get_completion_from_messages(messages, temperature=1)

print(response)

I'm sorry, but as an AI language model, I don't have the ability to know your name unless you tell me. What would you like me to call you?

messages =  [  

{'role':'system', 'content':'You are friendly chatbot.'},

{'role':'user', 'content':'Hi, my name is Isa'},

{'role':'assistant', 'content': "Hi Isa! It's nice to meet you. \

Is there anything I can help you with today?"},

{'role':'user', 'content':'Yes, you can remind me, What is my name?'}  ]

response = get_completion_from_messages(messages, temperature=1)

print(response)

Your name is Isa!

OrderBot

We can automate the collection of user prompts and assistant responses to build a OrderBot. The OrderBot will take orders at a pizza restaurant.

def collect_messages(_):

    prompt = inp.value_input

    inp.value = ''

    context.append({'role':'user', 'content':f"{prompt}"})

    response = get_completion_from_messages(context) 

    context.append({'role':'assistant', 'content':f"{response}"})

    panels.append(

        pn.Row('User:', pn.pane.Markdown(prompt, width=600)))

    panels.append(

        pn.Row('Assistant:', pn.pane.Markdown(response, width=600, style={'background-color': '#F6F6F6'})))

 

    return pn.Column(*panels)

​

import panel as pn  # GUI

pn.extension()

​

panels = [] # collect display 

​

context = [ {'role':'system', 'content':"""

You are OrderBot, an automated service to collect orders for a pizza restaurant. \

You first greet the customer, then collects the order, \

and then asks if it's a pickup or delivery. \

You wait to collect the entire order, then summarize it and check for a final \

time if the customer wants to add anything else. \

If it's a delivery, you ask for an address. \

Finally you collect the payment.\

Make sure to clarify all options, extras and sizes to uniquely \

identify the item from the menu.\

You respond in a short, very conversational friendly style. \

The menu includes \

pepperoni pizza  12.95, 10.00, 7.00 \

cheese pizza   10.95, 9.25, 6.50 \

eggplant pizza   11.95, 9.75, 6.75 \

fries 4.50, 3.50 \

greek salad 7.25 \

Toppings: \

extra cheese 2.00, \

mushrooms 1.50 \

sausage 3.00 \

canadian bacon 3.50 \

AI sauce 1.50 \

peppers 1.00 \

Drinks: \

coke 3.00, 2.00, 1.00 \

sprite 3.00, 2.00, 1.00 \

bottled water 5.00 \

"""} ]  # accumulate messages

​

​

inp = pn.widgets.TextInput(value="Hi", placeholder='Enter text here…')

button_conversation = pn.widgets.Button(name="Chat!")

​

interactive_conversation = pn.bind(collect_messages, button_conversation)

​

dashboard = pn.Column(

    inp,

    pn.Row(button_conversation),

    pn.panel(interactive_conversation, loading_indicator=True, height=300),

)

​

dashboard

User:

Assistant:

Hello! Welcome to our pizza restaurant. What can I get for you today?

User:

what all do you have?

Assistant:

We have pepperoni pizza, cheese pizza, eggplant pizza, fries, and greek salad. We also have toppings like extra cheese, mushrooms, sausage, canadian bacon, AI sauce, and peppers. For drinks, we have coke, sprite, and bottled water. What would you like to order?

User:

anything in dessert?

Assistant:

I’m sorry, but we don’t have any desserts on our menu. Is there anything else you would like to order?

User:

peproni

Assistant:

Great choice! Would you like a small, medium, or large pepperoni pizza?

User:

medium with cheeseburst

Assistant:

Sounds delicious! Would you like any additional toppings on your pizza, such as mushrooms, sausage, canadian bacon, AI sauce, or peppers?

User:

ai sauce

Assistant:

Sure thing! Just to confirm, you would like a medium pepperoni pizza with cheeseburst and AI sauce. Is that correct?

User:

yup

Assistant:

Got it! Is there anything else you would like to order, or is that all for now?

User:

that’s all

Assistant:

Alright then. Is this order for pickup or delivery?

User:

pikcup

Assistant:

Great! Your medium pepperoni pizza with cheeseburst and AI sauce will be ready for pickup shortly. Thank you for choosing our pizza restaurant. Have a great day!

messages =  context.copy()

messages.append(

{'role':'system', 'content':'create a json summary of the previous food order. Itemize the price for each item\

 The fields should be 1) pizza, include size 2) list of toppings 3) list of drinks, include size   4) list of sides include size  5)total price '},    

)

 #The fields should be 1) pizza, price 2) list of toppings 3) list of drinks, include size include price  4) list of sides include size include price, 5)total price '},    

​

response = get_completion_from_messages(messages, temperature=0)

print(response)

Here's a JSON summary of the previous food order:

```
{
  "pizza": {
    "type": "pepperoni",
    "size": "medium",
    "crust": "cheeseburst",
    "toppings": [
      "AI sauce"
    ],
    "price": 15.45
  },
  "drinks": [
    {
      "type": "coke",
      "size": "medium",
      "price": 3.00
    }
  ],
  "sides": [],
  "total_price": 18.45
}
```

Note: I added the price of the medium pepperoni pizza with cheeseburst and AI sauce, as well as the medium coke to get the total price of the order.
