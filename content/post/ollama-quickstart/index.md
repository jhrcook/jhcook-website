---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Quickstart for playing with LLMs locally"
subtitle: "A simple tutorial for getting up and running with LLMs running on your local computer."
summary: ""
authors: []
tags: [tutorial, ML, AI, LLM]
categories: [Programming]
date: 2024-01-31T07:30:13-05:00
lastmod: 2024-01-31T07:30:13-05:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: true

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

With so much hype around LLMs (e.g. Chat-GPT), I've been playing around with various models in the hope that when I come up with a use case, I will have the skill-set to actually build the tool.
For privacy and usability reasons, I'm particularly interested in running these models locally, especially since I have a fancy MacBook Pro with Apple Silicon that can execute inference on these giant models relatively quickly (usually just a couple of seconds).
With [yesterday's](https://venturebeat.com/ai/meta-releases-code-llama-70b-an-open-source-behemoth-to-rival-private-ai-development/) release of a new version of [Code Llama](https://ai.meta.com/research/publications/code-llama-open-foundation-models-for-code/), I figured it could be helpful to put together a short post on how to get started playing with these models so others can join in on the fun.

The following tutorial will show you how to:

1. get set up with [Ollama](https://ollama.ai),
1. create a Python virtual environment,
1. and provide and explain a simple Python script for interacting with the model using [LangChain](https://www.langchain.com).

## Setting up Ollama

[Ollama](https://ollama.ai) is the model provider.
Another popular option is HuggingFace, but I have found using Ollama to be very easy and fast.

There are multiple installation options.
The first is to just download the application from the Ollama website, <https://ollama.ai/download>, but this comes with an app icon and status bar icon that I really don't need cluttering up my workspace.
Instead, I opted to install it with homebrew, a popular package manager for Mac:

```bash
brew install ollama
```

With Ollama installed, you just need to start the server to interact with it.

```bash
ollama serve
```

> The Ollama server will run in this terminal, so you'll need to open another to continue with the tutorial.
> You'll need to start up the server anytime you want to interact with Ollama (e.g. downloading a new model, running inference).

We can now interact with Ollama, including downloading models with the `pull` command.
The available models are listed [here](https://ollama.ai/library).
Some models have different versions that are larger or for specific use cases.
Here, we'll download the Python-fine tuned version of Code Llama.
Note that there are also larger versions of this model that may improve it's quality.

```bash
ollama pull codellama:python
```

That's it!
We now have Ollama running and ready to execute inference on the latest Python Code Llama model.

## Setting up the Python virtual environment

This is a routine process, not specific to LLMs, but I figured I'd include it here for those unfamiliar.
Below, I create a Python virtual environment, activate it, and then install the necessary LangChain libraries from [PyPI](https://pypi.org).

```bash
python -m venv .env
source .env/bin/activate
pip install --upgrade pip
pip install langchain langchain-community
```

The above commands use the default version of Python installed on your system.
To exercise more control over the versions of Python, I use ['pyenv'](https://github.com/pyenv/pyenv), though this is a bit more complicated and I won't cover using it here.
It is worth mentioning though for those with a bit more experince.

## Interacting with Code Llama using LangChain

"LangChain is a framework for developing applications powered by language models."
It is a powerful tool for interacting with LLMs – scaling from very simple to highly complex use cases and easily swapping out LLM backends.
I'm still learning how to use it's more advanced features, but LangChain is very easy to [get started](https://python.langchain.com/docs/get_started/quickstart) with.
The documentation has plenty of examples and is a great place to start with for learning more about the tool.

Here, I'll provide the code for a simple Python script using LangChain to interact with the Python Code Llama model downloaded above.
I hope this offers a starting point for those wishing to explore playing with these models, but are overwhelmed by the myriad options available.

> Note, that you need to have the Ollama server running in the background by executing `ollama serve` in another terminal (or already running from the previous step).

Below is the code for those who want to take it and run.
Following it, I have more information about what it is actually doing.

```python
"""Demonstration of using the Python Code Llama LLM."""

from langchain.prompts import PromptTemplate
from langchain_community.llms import Ollama
from langchain_core.output_parsers import StrOutputParser



def main() -> None:
    prompt = PromptTemplate.from_template(
        "You are a Python programmer who writes simple and concise code. Complete the"
        " following code using type hints in function definitions:"
        "\n\n# {input}"
    )
    llm = Ollama(model="codellama:python")
    output_parser = StrOutputParser()

    chain = prompt | llm | output_parser
    
    response = chain.invoke(
        {"input": "Request a wikipedia article and pull out the tables."}
    )
    print(response)


if __name__ == "__main__":
    main()
```

If the above code is copied to a file `app.py`, this script can be executed with the following:

```bash
python app.py
```

There are three sections to this script:

1. the import statements that load the relevant LangChain libraries,
1. the `main()` function that executes the demo (described in detail below),
1. and the `if` statement that executes the `main()` function if this file is run as a script.

The `main()` function holds the actual code for interacting with the LLM.
It starts by creating `prompt`, a LangChain Prompt that will take the input from the user and pass it to the model, first wrapping it with some instructions for the LLM.
The LLM object is then created, specifying the model with the same name we used to download it earlier in the tutorial.
The last component is just a simple output parser that converts the model's output to a string for easy printing.

These three components are then combined into a chain using the pipe (`|`) operator that LangChain has overloaded to support it's clever chaining syntax.
The chain's `invoke()` method is then executed to pass a request to the LLM.
Note that a dictionary is passed with a key matching the keyword `input` in the prompt template.
The text passed as "input" will be inserted into the template and the result will then be sent to the LLM.

## Conclusion

That's pretty much it.
These few lines of simple code can get you up a running with an LLM running on your local machine!
I hope this has provided you with some guidance for getting started and was relatively easy to follow.
I would recommend getting the demo running and then perhaps playing with some variables such as:

- experimenting with different prompts,
- trying different types of tasks such as having the model inspect code for bugs for writing tests,
- comparing the results from different models, such as larger Code Llama options, the general vs. Python-specific models, try swapping in a ChatGPT backend, or even figure out a use case for multi-modal models (e.g. [llava](https://ollama.ai/library/llava)).

---

For fun, here is the output from running the above code on the `codellama:13b-python` model and input "Download a wikipedia article on marmots and extract any tables it contains."

```python
# Save each table as a separate csv file in your working directory. 

from bs4 import BeautifulSoup
import requests, re
def get_marmot_article(url):
    r = requests.get(url)
    html_content = r.text
    soup = BeautifulSoup(html_content,'lxml')
    return soup


def find_tables_in_soup(soup):
    tables = []
    for table in soup.findAll('table'):
        if 'class' in table.attrs:
            if table['class'] == 'wikitable':
                tables.append(table)
    return tables

def extract_from_tables(tables, filename):
    dfs = []
    for i, t in enumerate(tables):
        headers = 
        rows = [row.text.split('\n')[0].strip() 
                if len(row.text.split('\n')) >=2 else '' 
                for row in t.findAll('tr', recursive=False)][1:]
        
        data = list(zip(* + ))
        
        dfs.append(pd.DataFrame(data, columns=['Species','Fur color', 'Range']))
        dfs.to_csv('marmot_{}.csv'.format(i), index=False)
    return dfs    

```
