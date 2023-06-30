LangChain: Agents
Outline:

    Using built in LangChain tools: DuckDuckGo search and Wikipedia
    Defining your own tools

import os

​

from dotenv import load_dotenv, find_dotenv

_ = load_dotenv(find_dotenv()) # read local .env file

​

import warnings

warnings.filterwarnings("ignore")

Built-in LangChain tools

#!pip install -U wikipedia

from langchain.agents.agent_toolkits import create_python_agent

from langchain.agents import load_tools, initialize_agent

from langchain.agents import AgentType

from langchain.tools.python.tool import PythonREPLTool

from langchain.python import PythonREPL

from langchain.chat_models import ChatOpenAI

llm = ChatOpenAI(temperature=0)

tools = load_tools(["llm-math","wikipedia"], llm=llm)

agent= initialize_agent(

    tools, 

    llm, 

    agent=AgentType.CHAT_ZERO_SHOT_REACT_DESCRIPTION,

    handle_parsing_errors=True,

    verbose = True)

agent("What is the 25% of 300?")



> Entering new AgentExecutor chain...
Thought: We need to calculate 25% of 300, which involves multiplication and division.

Action:
```
{
  "action": "Calculator",
  "action_input": "300*0.25"
}
```


Observation: Answer: 75.0
Thought:We have the answer to the question.

Final Answer: 75.0

> Finished chain.

{'input': 'What is the 25% of 300?', 'output': '75.0'}

question = "Tom M. Mitchell is an American computer scientist \

and the Founders University Professor at Carnegie Mellon University (CMU)\

what book did he write?"

result = agent(question) 



> Entering new AgentExecutor chain...
Thought: I should use Wikipedia to find the answer to this question.

Action:
```
{
  "action": "Wikipedia",
  "action_input": "Tom M. Mitchell"
}
```


Observation: Page: Tom M. Mitchell
Summary: Tom Michael Mitchell (born August 9, 1951) is an American computer scientist and the Founders University Professor at Carnegie Mellon University (CMU). He is a founder and former Chair of the Machine Learning Department at CMU. Mitchell is known for his contributions to the advancement of machine learning, artificial intelligence, and cognitive neuroscience and is the author of the textbook Machine Learning. He is a member of the United States National Academy of Engineering since 2010. He is also a Fellow of the American Academy of Arts and Sciences, the American Association for the Advancement of Science and a Fellow and past President of the Association for the Advancement of Artificial Intelligence. In October 2018, Mitchell was appointed as the Interim Dean of the School of Computer Science at Carnegie Mellon.

Page: Tom Mitchell (Australian footballer)
Summary: Thomas Mitchell (born 31 May 1993) is a professional Australian rules footballer playing for the Collingwood Football Club in the Australian Football League (AFL). He previously played for the Sydney Swans from 2012 to 2016, and the Hawthorn Football Club between 2017 and 2022. Mitchell won the Brownlow Medal as the league's best and fairest player in 2018 and set the record for the most disposals in a VFL/AFL match, accruing 54 in a game against Collingwood during that season.
Thought:The book that Tom M. Mitchell wrote is "Machine Learning".

Action:
```
{
  "action": "Wikipedia",
  "action_input": "Machine Learning (book)"
}
```


Observation: Page: Machine learning
Summary: Machine learning (ML) is a field devoted to understanding and building methods that let machines "learn" – that is, methods that leverage data to improve computer performance on some set of tasks.Machine learning algorithms build a model based on sample data, known as training data, in order to make predictions or decisions without being explicitly programmed to do so. Machine learning algorithms are used in a wide variety of applications, such as in medicine, email filtering, speech recognition, agriculture, and computer vision, where it is difficult or unfeasible to develop conventional algorithms to perform the needed tasks.A subset of machine learning is closely related to computational statistics, which focuses on making predictions using computers, but not all machine learning is statistical learning. The study of mathematical optimization delivers methods, theory and application domains to the field of machine learning. Data mining is a related field of study, focusing on exploratory data analysis through unsupervised learning.Some implementations of machine learning use data and artificial neural networks in a way that mimics the working of a biological brain.In its application across business problems, machine learning is also referred to as predictive analytics.

Page: Quantum machine learning
Summary: Quantum machine learning is the integration of quantum algorithms within machine learning programs. The most common use of the term refers to machine learning algorithms for the analysis of classical data executed on a quantum computer, i.e. quantum-enhanced machine learning. While machine learning algorithms are used to compute immense quantities of data, quantum machine learning utilizes qubits and quantum operations or specialized quantum systems to improve computational speed and data storage done by algorithms in a program. This includes hybrid methods that involve both classical and quantum processing, where computationally difficult subroutines are outsourced to a quantum device. These routines can be more complex in nature and executed faster on a quantum computer. Furthermore, quantum algorithms can be used to analyze quantum states instead of classical data. Beyond quantum computing, the term "quantum machine learning" is also associated with classical machine learning methods applied to data generated from quantum experiments (i.e. machine learning of quantum systems), such as learning the phase transitions of a quantum system or creating new quantum experiments. Quantum machine learning also extends to a branch of research that explores methodological and structural similarities between certain physical systems and learning systems, in particular neural networks. For example, some mathematical and numerical techniques from quantum physics are applicable to classical deep learning and vice versa. Furthermore, researchers investigate more abstract notions of learning theory with respect to quantum information, sometimes referred to as "quantum learning theory".

Page: Timeline of machine learning
Summary: This page is a timeline of machine learning. Major discoveries, achievements, milestones and other major events in machine learning are included.
Thought:Tom M. Mitchell wrote the book "Machine Learning".

Final Answer: "Machine Learning".

> Finished chain.

Python Agent

agent = create_python_agent(

    llm,

    tool=PythonREPLTool(),

    verbose=True

)

customer_list = [["Harrison", "Chase"], 

                 ["Lang", "Chain"],

                 ["Dolly", "Too"],

                 ["Elle", "Elem"], 

                 ["Geoff","Fusion"], 

                 ["Trance","Former"],

                 ["Jen","Ayai"]

                ]

agent.run(f"""Sort these customers by \

last name and then first name \

and print the output: {customer_list}""") 



> Entering new AgentExecutor chain...
I can use the sorted() function to sort the list of customers by last name and then first name. I will need to provide a key function to sorted() that returns a tuple of the last name and first name in that order.
Action: Python REPL
Action Input:
```
customers = [['Harrison', 'Chase'], ['Lang', 'Chain'], ['Dolly', 'Too'], ['Elle', 'Elem'], ['Geoff', 'Fusion'], ['Trance', 'Former'], ['Jen', 'Ayai']]
sorted_customers = sorted(customers, key=lambda x: (x[1], x[0]))
for customer in sorted_customers:
    print(customer)
```
Observation: ['Jen', 'Ayai']
['Lang', 'Chain']
['Harrison', 'Chase']
['Elle', 'Elem']
['Trance', 'Former']
['Geoff', 'Fusion']
['Dolly', 'Too']

Thought:The customers are now sorted by last name and then first name. 
Final Answer: [['Jen', 'Ayai'], ['Lang', 'Chain'], ['Harrison', 'Chase'], ['Elle', 'Elem'], ['Trance', 'Former'], ['Geoff', 'Fusion'], ['Dolly', 'Too']]

> Finished chain.

"[['Jen', 'Ayai'], ['Lang', 'Chain'], ['Harrison', 'Chase'], ['Elle', 'Elem'], ['Trance', 'Former'], ['Geoff', 'Fusion'], ['Dolly', 'Too']]"

View detailed outputs of the chains

import langchain

langchain.debug=True

agent.run(f"""Sort these customers by \

last name and then first name \

and print the output: {customer_list}""") 

langchain.debug=False

[chain/start] [1:chain:AgentExecutor] Entering Chain run with input:
{
  "input": "Sort these customers by last name and then first name and print the output: [['Harrison', 'Chase'], ['Lang', 'Chain'], ['Dolly', 'Too'], ['Elle', 'Elem'], ['Geoff', 'Fusion'], ['Trance', 'Former'], ['Jen', 'Ayai']]"
}
[chain/start] [1:chain:AgentExecutor > 2:chain:LLMChain] Entering Chain run with input:
{
  "input": "Sort these customers by last name and then first name and print the output: [['Harrison', 'Chase'], ['Lang', 'Chain'], ['Dolly', 'Too'], ['Elle', 'Elem'], ['Geoff', 'Fusion'], ['Trance', 'Former'], ['Jen', 'Ayai']]",
  "agent_scratchpad": "",
  "stop": [
    "\nObservation:",
    "\n\tObservation:"
  ]
}
[llm/start] [1:chain:AgentExecutor > 2:chain:LLMChain > 3:llm:ChatOpenAI] Entering LLM run with input:
{
  "prompts": [
    "Human: You are an agent designed to write and execute python code to answer questions.\nYou have access to a python REPL, which you can use to execute python code.\nIf you get an error, debug your code and try again.\nOnly use the output of your code to answer the question. \nYou might know the answer without running any code, but you should still run the code to get the answer.\nIf it does not seem like you can write code to answer the question, just return \"I don't know\" as the answer.\n\n\nPython REPL: A Python shell. Use this to execute python commands. Input should be a valid python command. If you want to see the output of a value, you should print it out with `print(...)`.\n\nUse the following format:\n\nQuestion: the input question you must answer\nThought: you should always think about what to do\nAction: the action to take, should be one of [Python REPL]\nAction Input: the input to the action\nObservation: the result of the action\n... (this Thought/Action/Action Input/Observation can repeat N times)\nThought: I now know the final answer\nFinal Answer: the final answer to the original input question\n\nBegin!\n\nQuestion: Sort these customers by last name and then first name and print the output: [['Harrison', 'Chase'], ['Lang', 'Chain'], ['Dolly', 'Too'], ['Elle', 'Elem'], ['Geoff', 'Fusion'], ['Trance', 'Former'], ['Jen', 'Ayai']]\nThought:"
  ]
}
[llm/end] [1:chain:AgentExecutor > 2:chain:LLMChain > 3:llm:ChatOpenAI] [7.14s] Exiting LLM run with output:
{
  "generations": [
    [
      {
        "text": "I can use the sorted() function to sort the list of customers by last name and then first name. I will need to provide a key function to sorted() that returns a tuple of the last name and first name in that order.\nAction: Python REPL\nAction Input:\n```\ncustomers = [['Harrison', 'Chase'], ['Lang', 'Chain'], ['Dolly', 'Too'], ['Elle', 'Elem'], ['Geoff', 'Fusion'], ['Trance', 'Former'], ['Jen', 'Ayai']]\nsorted_customers = sorted(customers, key=lambda x: (x[1], x[0]))\nfor customer in sorted_customers:\n    print(customer)\n```",
        "generation_info": null,
        "message": {
          "content": "I can use the sorted() function to sort the list of customers by last name and then first name. I will need to provide a key function to sorted() that returns a tuple of the last name and first name in that order.\nAction: Python REPL\nAction Input:\n```\ncustomers = [['Harrison', 'Chase'], ['Lang', 'Chain'], ['Dolly', 'Too'], ['Elle', 'Elem'], ['Geoff', 'Fusion'], ['Trance', 'Former'], ['Jen', 'Ayai']]\nsorted_customers = sorted(customers, key=lambda x: (x[1], x[0]))\nfor customer in sorted_customers:\n    print(customer)\n```",
          "additional_kwargs": {},
          "example": false
        }
      }
    ]
  ],
  "llm_output": {
    "token_usage": {
      "prompt_tokens": 327,
      "completion_tokens": 144,
      "total_tokens": 471
    },
    "model_name": "gpt-3.5-turbo"
  }
}
[chain/end] [1:chain:AgentExecutor > 2:chain:LLMChain] [7.14s] Exiting Chain run with output:
{
  "text": "I can use the sorted() function to sort the list of customers by last name and then first name. I will need to provide a key function to sorted() that returns a tuple of the last name and first name in that order.\nAction: Python REPL\nAction Input:\n```\ncustomers = [['Harrison', 'Chase'], ['Lang', 'Chain'], ['Dolly', 'Too'], ['Elle', 'Elem'], ['Geoff', 'Fusion'], ['Trance', 'Former'], ['Jen', 'Ayai']]\nsorted_customers = sorted(customers, key=lambda x: (x[1], x[0]))\nfor customer in sorted_customers:\n    print(customer)\n```"
}
[tool/start] [1:chain:AgentExecutor > 4:tool:Python REPL] Entering Tool run with input:
"```
customers = [['Harrison', 'Chase'], ['Lang', 'Chain'], ['Dolly', 'Too'], ['Elle', 'Elem'], ['Geoff', 'Fusion'], ['Trance', 'Former'], ['Jen', 'Ayai']]
sorted_customers = sorted(customers, key=lambda x: (x[1], x[0]))
for customer in sorted_customers:
    print(customer)
```"
[tool/end] [1:chain:AgentExecutor > 4:tool:Python REPL] [0.472ms] Exiting Tool run with output:
"['Jen', 'Ayai']
['Lang', 'Chain']
['Harrison', 'Chase']
['Elle', 'Elem']
['Trance', 'Former']
['Geoff', 'Fusion']
['Dolly', 'Too']"
[chain/start] [1:chain:AgentExecutor > 5:chain:LLMChain] Entering Chain run with input:
{
  "input": "Sort these customers by last name and then first name and print the output: [['Harrison', 'Chase'], ['Lang', 'Chain'], ['Dolly', 'Too'], ['Elle', 'Elem'], ['Geoff', 'Fusion'], ['Trance', 'Former'], ['Jen', 'Ayai']]",
  "agent_scratchpad": "I can use the sorted() function to sort the list of customers by last name and then first name. I will need to provide a key function to sorted() that returns a tuple of the last name and first name in that order.\nAction: Python REPL\nAction Input:\n```\ncustomers = [['Harrison', 'Chase'], ['Lang', 'Chain'], ['Dolly', 'Too'], ['Elle', 'Elem'], ['Geoff', 'Fusion'], ['Trance', 'Former'], ['Jen', 'Ayai']]\nsorted_customers = sorted(customers, key=lambda x: (x[1], x[0]))\nfor customer in sorted_customers:\n    print(customer)\n```\nObservation: ['Jen', 'Ayai']\n['Lang', 'Chain']\n['Harrison', 'Chase']\n['Elle', 'Elem']\n['Trance', 'Former']\n['Geoff', 'Fusion']\n['Dolly', 'Too']\n\nThought:",
  "stop": [
    "\nObservation:",
    "\n\tObservation:"
  ]
}
[llm/start] [1:chain:AgentExecutor > 5:chain:LLMChain > 6:llm:ChatOpenAI] Entering LLM run with input:
{
  "prompts": [
    "Human: You are an agent designed to write and execute python code to answer questions.\nYou have access to a python REPL, which you can use to execute python code.\nIf you get an error, debug your code and try again.\nOnly use the output of your code to answer the question. \nYou might know the answer without running any code, but you should still run the code to get the answer.\nIf it does not seem like you can write code to answer the question, just return \"I don't know\" as the answer.\n\n\nPython REPL: A Python shell. Use this to execute python commands. Input should be a valid python command. If you want to see the output of a value, you should print it out with `print(...)`.\n\nUse the following format:\n\nQuestion: the input question you must answer\nThought: you should always think about what to do\nAction: the action to take, should be one of [Python REPL]\nAction Input: the input to the action\nObservation: the result of the action\n... (this Thought/Action/Action Input/Observation can repeat N times)\nThought: I now know the final answer\nFinal Answer: the final answer to the original input question\n\nBegin!\n\nQuestion: Sort these customers by last name and then first name and print the output: [['Harrison', 'Chase'], ['Lang', 'Chain'], ['Dolly', 'Too'], ['Elle', 'Elem'], ['Geoff', 'Fusion'], ['Trance', 'Former'], ['Jen', 'Ayai']]\nThought:I can use the sorted() function to sort the list of customers by last name and then first name. I will need to provide a key function to sorted() that returns a tuple of the last name and first name in that order.\nAction: Python REPL\nAction Input:\n```\ncustomers = [['Harrison', 'Chase'], ['Lang', 'Chain'], ['Dolly', 'Too'], ['Elle', 'Elem'], ['Geoff', 'Fusion'], ['Trance', 'Former'], ['Jen', 'Ayai']]\nsorted_customers = sorted(customers, key=lambda x: (x[1], x[0]))\nfor customer in sorted_customers:\n    print(customer)\n```\nObservation: ['Jen', 'Ayai']\n['Lang', 'Chain']\n['Harrison', 'Chase']\n['Elle', 'Elem']\n['Trance', 'Former']\n['Geoff', 'Fusion']\n['Dolly', 'Too']\n\nThought:"
  ]
}

[llm/end] [1:chain:AgentExecutor > 5:chain:LLMChain > 6:llm:ChatOpenAI] [3.10s] Exiting LLM run with output:
{
  "generations": [
    [
      {
        "text": "The customers have been sorted by last name and then first name, and the output has been printed. \nFinal Answer: [['Jen', 'Ayai'], ['Lang', 'Chain'], ['Harrison', 'Chase'], ['Elle', 'Elem'], ['Trance', 'Former'], ['Geoff', 'Fusion'], ['Dolly', 'Too']]",
        "generation_info": null,
        "message": {
          "content": "The customers have been sorted by last name and then first name, and the output has been printed. \nFinal Answer: [['Jen', 'Ayai'], ['Lang', 'Chain'], ['Harrison', 'Chase'], ['Elle', 'Elem'], ['Trance', 'Former'], ['Geoff', 'Fusion'], ['Dolly', 'Too']]",
          "additional_kwargs": {},
          "example": false
        }
      }
    ]
  ],
  "llm_output": {
    "token_usage": {
      "prompt_tokens": 526,
      "completion_tokens": 75,
      "total_tokens": 601
    },
    "model_name": "gpt-3.5-turbo"
  }
}
[chain/end] [1:chain:AgentExecutor > 5:chain:LLMChain] [3.10s] Exiting Chain run with output:
{
  "text": "The customers have been sorted by last name and then first name, and the output has been printed. \nFinal Answer: [['Jen', 'Ayai'], ['Lang', 'Chain'], ['Harrison', 'Chase'], ['Elle', 'Elem'], ['Trance', 'Former'], ['Geoff', 'Fusion'], ['Dolly', 'Too']]"
}
[chain/end] [1:chain:AgentExecutor] [10.24s] Exiting Chain run with output:
{
  "output": "[['Jen', 'Ayai'], ['Lang', 'Chain'], ['Harrison', 'Chase'], ['Elle', 'Elem'], ['Trance', 'Former'], ['Geoff', 'Fusion'], ['Dolly', 'Too']]"
}

Define your own tool

#!pip install DateTime

from langchain.agents import tool

from datetime import date

@tool

def time(text: str) -> str:

    """Returns todays date, use this for any \

    questions related to knowing todays date. \

    The input should always be an empty string, \

    and this function will always return todays \

    date - any date mathmatics should occur \

    outside this function."""

    return str(date.today())

agent= initialize_agent(

    tools + [time], 

    llm, 

    agent=AgentType.CHAT_ZERO_SHOT_REACT_DESCRIPTION,

    handle_parsing_errors=True,

    verbose = True)

Note:

The agent will sometimes come to the wrong conclusion (agents are a work in progress!).

If it does, please try running it again.

try:

    result = agent("whats the date today?") 

except: 

    print("exception on external access")



> Entering new AgentExecutor chain...
Question: whats the date today?
Thought: I can use the `time` tool to get today's date
Action:
```
{
  "action": "time",
  "action_input": ""
}
```

Observation: 2023-06-25
Thought:I have successfully used the `time` tool to get today's date.
Action:
```
{
  "action": "time",
  "action_input": ""
}
```


Observation: 2023-06-25
Thought:I already have the answer to the question.
Final Answer: Today's date is 2023-06-25.

> Finished chain.

