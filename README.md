# TOFU: Task of Fictitious Unlearning 🍢

The TOFU dataset serves as a benchmark for evaluating unlearning performance of large language models on realistic tasks. The dataset comprises question-answer pairs based on autobiographies of 200 different authors that do not exist and are completely fictitiously generated by the GPT-4 model. The goal of the task is to unlearn a fine-tuned model on various fractions of the forget set.

## Quick Links

- [**Website**](https://locuslab.github.io/tofu): The landing page for TOFU
- [**arXiv Paper**](http://arxiv.org/abs/2401.06121): Detailed information about the TOFU dataset and its significance in unlearning tasks.
- [**GitHub Repository**](https://github.com/locuslab/tofu): Access the source code, fine-tuning scripts, and additional resources for the TOFU dataset.
- [**Dataset on Hugging Face**](https://huggingface.co/datasets/locuslab/TOFU): Direct link to download the TOFU dataset.
- [**Leaderboard on Hugging Face Spaces**](https://huggingface.co/spaces/locuslab/tofu_leaderboard): Current rankings and submissions for the TOFU dataset challenges.
- [**Summary on Twitter**](https://x.com/_akhaliq/status/1745643293839327268): A concise summary and key takeaways from the project.


## Applicability 🚀

The dataset is in QA format, making it ideal for use with popular chat models such as Llama2, Mistral, or Qwen. However, it also works for any other large language model. The corresponding code base is written for the Llama2 chat, and Phi-1.5 models, but can be easily adapted to other models.

## Installation

```
conda create -n tofu python=3.10
conda activate tofu
conda install pytorch pytorch-cuda=11.8 -c pytorch -c nvidia
conda install -c "nvidia/label/cuda-11.8.0" cuda-toolkit
pip install -r requirements.txt
```

## Loading the Dataset

To load the dataset, use the following code:

```python
from datasets import load_dataset
dataset = load_dataset("locuslab/TOFU","full")
```

## Finetune your models

The code currently supports `Phi-1.5`, and `Llama2-7b chat` models. But newer models can directly be added in the `model_config.yaml` file. For the unlearning challenege, we fine-tuned `Phi-1.5` for 5 epochs using a maximum learning rate of `2e-5`, and the `Llama2-7b chat` model for the same duration at `1e-5`. Finetuning can be done as follows:

```
master_port=18765
split=full
model=phi
lr=2e-5
CUDA_VISIBLE_DEVICES=0,1 torchrun --nproc_per_node=2 --master_port=$master_port finetune.py --config-name=finetune.yaml split=${split} batch_size=4 gradient_accumulation_steps=4 model_family=${model} lr=${lr}
```

## Forget models
Make sure that the path of the model to be unlearned is correctly provided in the `config/model_config.yaml` file. To unlearn a model on a forget set, use the following command:
```
CUDA_VISIBLE_DEVICES=0,1 torchrun --nproc_per_node=2 --master_port=$master_port forget.py --config-name=forget.yaml split=${split} batch_size=4 gradient_accumulation_steps=4 model_family=${model} lr=${lr}
```


### Available forget sets are:

- `forget01`: Forgetting 1% of the original dataset, all entries correspond to a single author.
- `forget05`: Forgetting 5% of the original dataset, all entries correspond to a single author.
- `forget10`: Forgetting 10% of the original dataset, all entries correspond to a single author.

Retain sets corresponding to each forget set are also available, which can be used to train an Oracle model.


### Push to Leaderboard

Head over to our [**Leaderboard on Hugging Face Spaces**](https://huggingface.co/spaces/locuslab/tofu_leaderboard) and drop your evaluated results file!

## Citing Our Work

If you find our codebase and dataset beneficial, please cite our work:
```
@misc{tofu2024,
      title={TOFU: A Task of Fictitious Unlearning for LLMs}, 
      author={Pratyush Maini and Zhili Feng and Avi Schwarzschild and Zachary C. Lipton and J. Zico Kolter},
      year={2024},
      archivePrefix={arXiv},
      primaryClass={cs.LG}
}
```
