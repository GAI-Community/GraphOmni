# 🧠 GraphOmni: A Comprehensive and Extendable Benchmark Framework for Large Language Models on Graph-theoretic Tasks

[![arXiv](https://img.shields.io/badge/arXiv-2504.12764-b31b1b.svg)](https://arxiv.org/abs/2504.12764)
[![Dataset](https://img.shields.io/badge/🤗%20Dataset-GraphOmni-yellow)](https://huggingface.co/datasets/G-A-I/GraphOmni)

A benchmarking framework for evaluating large language models (LLMs) on fundamental graph reasoning tasks using a variety of synthetic graphs, prompt schemes, serialization formats, and difficulty settings.

---

## 🚀 Overview

This project provides a unified pipeline to test LLMs' ability to solve graph algorithm problems. It supports multiple models, tasks, prompting strategies, and graph representations. Results are automatically saved and evaluated.

---

## ✅ Supported Settings

- **Tasks**:  
  `connectivity`, `bfsorder`, `triangle`, `diameter`, `cycle`, `shortest_path`, `all`

- **Models**:  
  `Llama3.1`, `Mistral`, `Phi-4`, `Qwen2.5`, `Qwen3-8B`, ... (easily extendable)

- **Prompt Modes**:  
  `Algorithm`, `CoT`, `k-shot`, `Instruct`, `none`, `0-CoT`, `0-Instruct`, `0-Algorithm`, `LTM`, ... (easily extendable)

- **Serialization Formats**:  
  `Graph Modelling Language`, `Adjacency Set`, `Edge Set`, `Edge List`, `Adjacency Matrix`, `Adjacency List`, `GraphML`, ... (easily extendable)

- **Difficulty**:  
  `easy`, `medium`, `hard`

---

## Environment Setup

Install python packages

```
python -m venv venv_name && source venv/bin/activate && pip install -r requirements.txt
```

Set environment variables:

This project uses the python-dotenv library to manage environment variables. Please create a .env file to store sensitive information such as your Hugging Face token and OpenAI API key. These variables will be automatically loaded using load_dotenv().

## 📁 Data (available at [HF Repo](https://huggingface.co/datasets/G-A-I/GraphOmni))
```
└── query
    ├── query_json 
    │   ├── load_bfsorder.json
        ├── load_connectivity.json
    ...
```
The json file is a list of dictionaries, where each dictionary, contains the following information. 

name-id: Unique identifier for the query instance
query: The full input query
gt_answer: Ground-truth answer for the task
model_response: Dictionary of model outputs (initially empty)
prompt_type, task_type, serialization_type: Metadata describing the setup
graph_info: Graph content in Adjacency Matrix format
graph_type: Type or class of the graph
graph_token: Number of tokens in serialized graph
model_extract: Whether the model output contains an extractable answer (initially empty)
model_acc: Whether the model's answer is correct (initially empty)
difficulty: Difficulty level of the query

 
## Anwsering and evaluation 
If you want to evaluate a specified combination of parameters (including Tasks, Models, Prompt Modes, Serialization Formats and Difficulty), you can simply run: (**It will load the dataset directly from the HF repo if you haven't done so**)

```
python main.py
```

The default parameters are “Llama3.1”, “easy” “LTM”, “Adjacency Set”, “cycle”. 

To run an evaluation on a specific parameter combination (task, model, prompt, serialization format, difficulty), use: 

```
python main.py --model Llama3.1  --mode  easy --prompt LTM  --graph_representation  'Adjacency Set' --task  cycle
```

Use --task all to evaluate all six tasks with a single model:, such as 
```
python main.py --model Llama3.1  --task  all
```

Results are saved in the following directories:

```
└── results
    ├── agg_jsonfile 
    │   ├── load_bfsorder.json
        ├── load_connectivity.json
        ...
    ├── im_jsonfile
    │   ├── bfsorder
        ├── connectivity
        ...
 ```
Each combination will be saved in the folder corresponding to `im_jsonfile`, and the result of the integration will be saved in the JSON file corresponding to the task in `agg_jsonfile`.


**If you want to use the new LLM model for evaluation, add the new load model and the relevant code for prediction under predict.py**
```
└── openllm
    ├── predict.py
        ...
 ```

### Output file
Each output .json file contains a list of dictionaries, where each entry corresponds to a query instance. Example:
```
[
    {
        "name-id": "cycle-easy-LTM-Adjacency Set-0",
        "query": "\n Given a graph representation, your task is determining whether the graph has a cycle \nGraph: Adjacency Set is \n{1: {3, 4, 5, 6, 7, 8}, 3: {0, 1, 2}, 2: {3, 4, 5, 6, 7, 8}, 5: {0, 1, 2}, 0: {3, 4, 5, 6, 7, 8}, 7: {0, 1, 2}, 6: {0, 1, 2}, 8: {0, 1, 2}, 4: {0, 1, 2}}\n\nQ: Is there a cycle in this graph?\nA: Let's break down this problem:",
        "gt_answer": "True",
        "model_response": {
            "Llama3.1": "yes"
        },
        "prompt_type": "LTM",
        "task_type": "cycle",
        "serialization_type": "Adjacency Set",
        "graph_info": "[[0 0 0 1 1 1 1 1 1]\n [0 0 0 1 1 1 1 1 1]\n [0 0 0 1 1 1 1 1 1]\n [1 1 1 0 0 0 0 0 0]\n [1 1 1 0 0 0 0 0 0]\n [1 1 1 0 0 0 0 0 0]\n [1 1 1 0 0 0 0 0 0]\n [1 1 1 0 0 0 0 0 0]\n [1 1 1 0 0 0 0 0 0]]",
        "graph_type": "Bipartite-ERM",
        "graph_token": "154",
        "model_extract": {
            "Llama3.1": 1
        },
        "model_acc": {
            "Llama3.1": 1
        },
        "difficulty": "easy"
    },

    ...
    
]
```


### 🔒 Closed-Source Model Evaluation

In our project, we used batchapi to evaluate some closed-sourced models. The pipeline is as follows:

If you want to evaluate a specified combination of parameters (including Tasks, Models, Prompt Modes, Serialization Formats and Difficulty), you can simply run  
```
python batchapi_openai_6tasks.py
```
and retrieve it using 
```
python batchapi_openai_6tasks_retrive.py
```

Results are saved in the following directories:
```
└── closedllm
    ├── openailog
    │   ├── batch
        ├── content
        ├── log
```

The results will be in the ./closedllm/openailog/content/

Then use 'python main_closemodel.py --task all' to add the results and evaluate the performance of the closed-source model.

Thank you can evaluate the results via the very first pipeline.

