![3310fb06d318330482e3d0ea58c08fdd.png](./_resources/3310fb06d318330482e3d0ea58c08fdd.png)
![6dcb0dbc94cf24e79feef431d2d77287.png](./_resources/6dcb0dbc94cf24e79feef431d2d77287.png)
![8e34e6b7ebcd955a521a569b2cbeb59c.png](./_resources/8e34e6b7ebcd955a521a569b2cbeb59c.png)
```
from secret_key import openapi_key,serapi_key
import os
os.environ['OPENAI_API_KEY'] = openapi_key
os.environ['SERAPI_API_KEY'] = serapi_key
```

```
from langchain.llms import OpenAI

llm = OpenAI(temperature=0.9)
name = llm.predict("I want to open a restaurant for Indian food. Suggest a fency name for this.")
print(name)
```

```
nepali_name = llm("I want to open a restaurant for Nepali food. Suggest a fency name for this.")
print(nepali_name)
```
We can see that the query gets repeatative and is a bad way of writing a code so we use the prompt template
```
from langchain.prompts import PromptTemplate
prompt_template_name = PromptTemplate(
    input_variables=['country'],
    template = "Suggest some {country} cuisine"
)
p = prompt_template_name.format(country='Hyderabadi')
print(p)
```
p looks like `Suggest some Hyderabadi cuisine`
`print(llm.predict(p))`
![fd7899a4d9a1b617874c24add7f856f6.png](./_resources/fd7899a4d9a1b617874c24add7f856f6.png)
# Chains

```
chain = LLMChain (llm=llm, prompt = prompt_template_name,verbose =True)
print(chain.run("Korean"))
```
![283331eed3cb2125170f084ae24c4f29.png](./_resources/283331eed3cb2125170f084ae24c4f29.png)
Cuisine -> Resturant Name
```
llm = OpenAI(temperature=0.6)

prompt_template_name = PromptTemplate(
    input_variables =['cuisine'],
    template = "I want to open a restaurant for {cuisine} food. Suggest a fency name for this."
)

name_chain =LLMChain(llm=llm, prompt=prompt_template_name)

prompt_template_items = PromptTemplate(
    input_variables = ['restaurant_name'],
    template="""Suggest some menu items for {restaurant_name}"""
)

food_items_chain = LLMChain(llm=llm, prompt=prompt_template_items)

```
## Simple Sequential Chain
![ae79984fe8098a4831f42e166c1db823.png](./_resources/ae79984fe8098a4831f42e166c1db823.png)
where the rest
```
llm = OpenAI(temperature=0.6)

prompt_template_name = PromptTemplate(
    input_variables =['cuisine'],
    template = "I want to open a restaurant for {cuisine} food. Suggest a fency name for this."
)

name_chain =LLMChain(llm=llm, prompt=prompt_template_name)

prompt_template_items = PromptTemplate(
    input_variables = ['restaurant_name'],
    template="""Suggest some menu items for {restaurant_name}"""
)

food_items_chain = LLMChain(llm=llm, prompt=prompt_template_items)

from langchain.chains import SimpleSequentialChain
chain = SimpleSequentialChain(chains = [name_chain, food_items_chain])

content = chain.run("Hyderabadi")
print(content)
```
The  problems with the simple sequential chain are:
- the name of the suggested resturant wont come 
- The sequence matters in `chain = SimpleSequentialChain(chains = [name_chain, food_items_chain])`
- ![810c3de9d95f69ac5888cc1791e6d503.png](./_resources/810c3de9d95f69ac5888cc1791e6d503.png)
- ![3a8de6331be379892e180a8ac7ee64d2.png](./_resources/3a8de6331be379892e180a8ac7ee64d2.png)
> thats why we use the sequential chain

## Sequential Chain
![c4f5d439b32193891e462a8a3593f565.png](./_resources/c4f5d439b32193891e462a8a3593f565.png)
Sequential Chain can produce multiple outputs
```
llm = OpenAI(temperature=0.7)
prompt_template_name = PromptTemplate(
    input_variables =['cuisine'],
    template = "I want to open a restaurant for {cuisine} food. Suggest a fency name for this."
)

name_chain =LLMChain(llm=llm, prompt=prompt_template_name, output_key="restaurant_name")

prompt_template_items = PromptTemplate(
    input_variables = ['restaurant_name'],
    template="Suggest some menu items for {restaurant_name}."
)

food_items_chain =LLMChain(llm=llm, prompt=prompt_template_items, output_key="menu_items")

from langchain.chains import SequentialChain

chain = SequentialChain(
    chains = [name_chain, food_items_chain],
    input_variables = ['cuisine'],
    output_variables = ['restaurant_name', "menu_items"]
)
```
`chain({"cuisine": "Indian"})`
displays the Indian restaurant name and the menus of it in a dictionary form
![80a02d011c24c2ce5bb1e5a8681ac406.png](./_resources/80a02d011c24c2ce5bb1e5a8681ac406.png)
![68c0ae94a7c0488df2a9b53254b9374a.png](./_resources/68c0ae94a7c0488df2a9b53254b9374a.png)

# Agents
Agent is a poweful concept of the lanchain which gives the reasonable capacity to the OpenAi
![e8c7868bd04382a9ff62911fd4cf9023.png](./_resources/e8c7868bd04382a9ff62911fd4cf9023.png)

| Month | Savings |
| -------- | ------- |
| ![3b0b439ccf7d9baa17af43dd550e0188.png](./_resources/3b0b439ccf7d9baa17af43dd550e0188.png) | ![c9d320f010fc560e2b9ed51fc7b83cc9.png](./_resources/c9d320f010fc560e2b9ed51fc7b83cc9.png) |
|![b34ef7e554cf4620a5ab8f1a35cfcc9d.png](./_resources/b34ef7e554cf4620a5ab8f1a35cfcc9d.png)|![320bf1d030a80b6795cebeacadf5c30d.png](./_resources/320bf1d030a80b6795cebeacadf5c30d.png)|