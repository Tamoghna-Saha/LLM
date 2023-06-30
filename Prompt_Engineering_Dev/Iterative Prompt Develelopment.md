Iterative Prompt Develelopment

In this lesson, you'll iteratively analyze and refine your prompts to generate marketing copy from a product fact sheet.
Setup

import openai

import os

​

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

Generate a marketing product description from a product fact sheet

fact_sheet_chair = """

OVERVIEW

- Part of a beautiful family of mid-century inspired office furniture, 

including filing cabinets, desks, bookcases, meeting tables, and more.

- Several options of shell color and base finishes.

- Available with plastic back and front upholstery (SWC-100) 

or full upholstery (SWC-110) in 10 fabric and 6 leather options.

- Base finish options are: stainless steel, matte black, 

gloss white, or chrome.

- Chair is available with or without armrests.

- Suitable for home or business settings.

- Qualified for contract use.

​

CONSTRUCTION

- 5-wheel plastic coated aluminum base.

- Pneumatic chair adjust for easy raise/lower action.

​

DIMENSIONS

- WIDTH 53 CM | 20.87”

- DEPTH 51 CM | 20.08”

- HEIGHT 80 CM | 31.50”

- SEAT HEIGHT 44 CM | 17.32”

- SEAT DEPTH 41 CM | 16.14”

​

OPTIONS

- Soft or hard-floor caster options.

- Two choices of seat foam densities: 

 medium (1.8 lb/ft3) or high (2.8 lb/ft3)

- Armless or 8 position PU armrests 

​

MATERIALS

SHELL BASE GLIDER

- Cast Aluminum with modified nylon PA6/PA66 coating.

- Shell thickness: 10 mm.

SEAT

- HD36 foam

​

COUNTRY OF ORIGIN

- Italy

"""

prompt = f"""

Your task is to help a marketing team create a 

description for a retail website of a product based 

on a technical fact sheet.

​

Write a product description based on the information 

provided in the technical specifications delimited by 

triple backticks.

​

Technical specifications: ```{fact_sheet_chair}```

"""

response = get_completion(prompt)

print(response)

​

Introducing our stunning mid-century inspired office chair, the perfect addition to any home or business setting. Part of a beautiful family of office furniture, including filing cabinets, desks, bookcases, meeting tables, and more, this chair is available in several options of shell color and base finishes to suit your style. Choose from plastic back and front upholstery (SWC-100) or full upholstery (SWC-110) in 10 fabric and 6 leather options.

The chair is constructed with a 5-wheel plastic coated aluminum base and features a pneumatic chair adjust for easy raise/lower action. It is available with or without armrests and comes with soft or hard-floor caster options. You can also choose between two seat foam densities: medium (1.8 lb/ft3) or high (2.8 lb/ft3).

The chair is made with high-quality materials, including a cast aluminum shell with modified nylon PA6/PA66 coating and a 10mm shell thickness. The seat is made with HD36 foam for ultimate comfort.

With a width of 53cm, depth of 51cm, and height of 80cm, this chair is the perfect size for any workspace. The seat height is 44cm and the seat depth is 41cm.

Choose from base finish options of stainless steel, matte black, gloss white, or chrome to complete the look. This chair is qualified for contract use and proudly made in Italy. Upgrade your workspace with our stylish and comfortable mid-century inspired office chair.

Issue 1: The text is too long

    Limit the number of words/sentences/characters.

prompt = f"""

Your task is to help a marketing team create a 

description for a retail website of a product based 

on a technical fact sheet.

​

Write a product description based on the information 

provided in the technical specifications delimited by 

triple backticks.

​

Use at most 50 words.

​

Technical specifications: ```{fact_sheet_chair}```

"""

response = get_completion(prompt)

print(response)

​

Introducing our mid-century inspired office chair, part of a beautiful furniture family. Available in various shell colors and base finishes, with plastic or full upholstery options in fabric or leather. Suitable for home or business use, with a 5-wheel base and pneumatic chair adjust. Made in Italy.

len(response.split())

47

Issue 2. Text focuses on the wrong details

    Ask it to focus on the aspects that are relevant to the intended audience.

prompt = f"""

Your task is to help a marketing team create a 

description for a retail website of a product based 

on a technical fact sheet.

​

Write a product description based on the information 

provided in the technical specifications delimited by 

triple backticks.

​

The description is intended for furniture retailers, 

so should be technical in nature and focus on the 

materials the product is constructed from.

​

Use at most 50 words.

​

Technical specifications: ```{fact_sheet_chair}```

"""

response = get_completion(prompt)

print(response)

Introducing our mid-century inspired office chair, perfect for both home and business settings. With a range of shell colors and base finishes, including stainless steel and matte black, this chair is available with or without armrests. The 5-wheel plastic coated aluminum base and pneumatic chair adjust make it easy to raise and lower. Made in Italy with a cast aluminum shell and HD36 foam seat.

prompt = f"""

Your task is to help a marketing team create a 

description for a retail website of a product based 

on a technical fact sheet.

​

Write a product description based on the information 

provided in the technical specifications delimited by 

triple backticks.

​

The description is intended for furniture retailers, 

so should be technical in nature and focus on the 

materials the product is constructed from.

​

At the end of the description, include every 7-character 

Product ID in the technical specification.

​

Use at most 50 words.

​

Technical specifications: ```{fact_sheet_chair}```

"""

response = get_completion(prompt)

print(response)

Introducing our mid-century inspired office chair, perfect for home or business settings. With a range of shell colors and base finishes, and the option of plastic or full upholstery, this chair is both stylish and comfortable. Constructed with a 5-wheel plastic coated aluminum base and pneumatic chair adjust, it's also practical. Available with or without armrests and suitable for contract use. Product ID: SWC-100, SWC-110.

Issue 3. Description needs a table of dimensions

    Ask it to extract information and organize it in a table.

prompt = f"""

Your task is to help a marketing team create a 

description for a retail website of a product based 

on a technical fact sheet.

​

Write a product description based on the information 

provided in the technical specifications delimited by 

triple backticks.

​

The description is intended for furniture retailers, 

so should be technical in nature and focus on the 

materials the product is constructed from.

​

At the end of the description, include every 7-character 

Product ID in the technical specification.

​

Keep this description under 75 words.

​

After the description, include a table that gives the 

product's dimensions. The table should have two columns.

In the first column include the name of the dimension. 

In the second column include the measurements in inches only.

​

Give the table the title 'Product Dimensions'.

​

Format everything as HTML that can be used in a website. 

Place the description in a <div> element.

​

Technical specifications: ```{fact_sheet_chair}```

"""

​

response = get_completion(prompt)

print(response)

<div>
<p>The mid-century inspired office chair is a stylish and functional addition to any workspace. Available in a range of shell colors and base finishes, with plastic or full upholstery options in fabric or leather. The chair features a 5-wheel plastic coated aluminum base and pneumatic chair adjust for easy raise/lower action. Suitable for home or business settings and qualified for contract use. Product ID: SWC-100, SWC-110.</p>
</div>

<table>
  <caption>Product Dimensions</caption>
  <tr>
    <th>Width</th>
    <td>53 cm | 20.87"</td>
  </tr>
  <tr>
    <th>Depth</th>
    <td>51 cm | 20.08"</td>
  </tr>
  <tr>
    <th>Height</th>
    <td>80 cm | 31.50"</td>
  </tr>
  <tr>
    <th>Seat Height</th>
    <td>44 cm | 17.32"</td>
  </tr>
  <tr>
    <th>Seat Depth</th>
    <td>41 cm | 16.14"</td>
  </tr>
</table>

Load Python libraries to view HTML

from IPython.display import display, HTML

display(HTML(response))