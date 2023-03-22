## 🦙🌲🤏 Alpaca-LoRA: Low-Rank LLaMA Instruct-Tuning

- 在Colab上尝试预训练的模型 [here](https://colab.research.google.com/drive/1eWAmesrW99p7e1nah5bipn0zikMb8XYC)
- 分享定制的LoRA适配器，包括用于较大的适配器。 [here](https://github.com/tloen/alpaca-lora/issues/52)
- Users have created a Discord server for discussion and support [here](https://discord.gg/prbq284xX5)
- `alpaca-lora-30b` can be used like ChatGPT; see [here](https://twitter.com/algo_diver/status/1637851640027041798)

This repository contains code for reproducing the [Stanford Alpaca](https://github.com/tatsu-lab/stanford_alpaca) results using [low-rank adaptation (LoRA)](https://arxiv.org/pdf/2106.09685.pdf).
我们提供的Instruct模型的质量类似于 `text-davinci-003` that can run [on a Raspberry Pi](https://twitter.com/miolini/status/1634982361757790209) (for research),
and the code is easily extended to the `13b`, `30b`, and `65b` models.

除了在单个RTX 4090上运行5个小时的训练代码外，我们还发布了一个用于下载和推理基础模型和LoRA的脚本。
以及[LoRA权重本身](https://huggingface.co/tloen/alpaca-lora-7b/tree/main)。
为了廉价而有效地进行微调，我们使用了Hugging Face的 [PEFT](https://github.com/huggingface/peft)
as well as Tim Dettmers' [bitsandbytes](https://github.com/TimDettmers/bitsandbytes).

在没有超参数调整的情况下，LoRA模型产生的输出与斯坦福大学的Alpaca模型相当。(请看下面包括的输出结果。)进一步的调整可能能够实现更好的性能；我邀请感兴趣的用户进行尝试并报告他们的结果。

### Setup

1. Install dependencies

```
pip install -r requirements.txt
```

2. If bitsandbytes doesn't work, [install it from source.](https://github.com/TimDettmers/bitsandbytes/blob/main/compile_from_source.md) Windows users can follow [these instructions](https://github.com/tloen/alpaca-lora/issues/17).

### Inference (`generate.py`)

该文件从Hugging Face模型中心读取基础模型，从`tloen/alpaca-lora-7b`读取LoRA权重，并在指定的输入上运行Gradio接口进行推理。用户应将其视为模型使用的样本代码，并根据需要进行修改。

### Training (`finetune.py`)

这个文件包含了PEFT对LLaMA模型的直接应用，以及一些与提示构建和tokenization有关的代码。
在这个文件的顶部有一组硬编码的超参数，你可以随意修改。我们随时欢迎对该代码进行调整以支持更大的模型的PR。

### Checkpoint export (`export_*_checkpoint.py`)
这些文件包含将LoRA权重合并到基础模型中的脚本，以便导出为Hugging Face格式和PyTorch `state_dicts`。它们应该帮助用户
who want to run inference in projects like [llama.cpp](https://github.com/ggerganov/llama.cpp)
or [alpaca.cpp](https://github.com/antimatter15/alpaca.cpp).

### Dataset
除了`alpaca_data.json`，它包含了原始的斯坦福羊驼数据集。
we also include `alpaca_data_cleaned.json`, which has been [stripped of various tokenization artifacts](https://github.com/tloen/alpaca-lora/pull/32)
with the help of @gururise.
现在训练脚本中默认使用这个文件。

@AndriyMulyar has also provided interactive, embedding-based visualizations of the original dataset's [instructions](https://atlas.nomic.ai/map/alpaca_instructions)
and [outputs](https://atlas.nomic.ai/map/alpaca_outputs),
as well as [clusters of bad examples](https://atlas.nomic.ai/map/d2139cc3-bc1c-441c-8d6f-3e6ffbbc2eda/838019ff-8fe2-42ba-809a-d86d2b98cd50/-18.11668742841587/-11.348087116836096/-20.88850316347706/-17.680468640801223/774455612).

### Notes

- 如果我们有一个更好的数据集，我们可能会大大改善我们的模型性能。考虑支持[LAION Open Assistant](https://open-assistant.io/)的努力，为监督下的微调制作一个高质量的数据集（或窃听他们发布他们的数据）。
- 我们正在不断地修复错误并进行训练，而Hugging Face Hub上的权重也在相应地更新。特别是，那些面临响应长度问题的人应该确保他们有最新版本的权重和代码。

### Example outputs

**Instruction**: Tell me about alpacas.

**Alpaca-LoRA**: Alpacas are members of the camelid family and are native to the Andes Mountains of South America. They are known for their soft, luxurious fleece, which is used to make clothing, blankets, and other items. Alpacas are herbivores and graze on grasses and other plants. They are social animals and live in herds of up to 20 individuals.

**Stanford Alpaca**: Alpacas are small, fluffy animals related to camels and llamas. They are native to Peru and Bolivia, and were first domesticated around 5,000 years ago. They are kept mainly for their fine, soft fleece, which is used to make knitwear and other garments. Alpacas are herd animals and live in small family groups, led by an older male. They are highly social animals and can form strong bonds with their owners.

**text-davinci-003**: Alpacas are a domesticated species of South American camelid, similar to llamas. They are native to the Andes Mountains in South America and are kept as livestock for their soft, luxurious wool. Alpacas are social animals and live in herds of up to 20 individuals. They are typically kept in small herds of two to five animals, and are relatively easy to care for. Alpacas are herbivores and feed on grass, hay, and other vegetation. They are also known for their gentle and friendly nature, making them popular as pets.

---

**Instruction**: Tell me about the president of Mexico in 2019.

**Alpaca-LoRA**: The president of Mexico in 2019 was Andrés Manuel López Obrador, who took office on December 1, 2018. He is a member of the National Regeneration Movement (MORENA) political party and is the first left-wing president of Mexico since 1946. He is known for his anti-corruption and anti-neoliberal policies, as well as his commitment to improving the living conditions of the Mexican people.

**Stanford Alpaca**: In 2019, Andres Manuel Lopez Obrador was sworn in as President of Mexico. He is the country’s 58th president and the first from the left-wing Morena party. He was elected in 2018 on a promise to combat corruption and reduce poverty and has faced criticism for his handling of the economy, crime and the country’s relationship with the United States. He is also known for his ambitious social welfare programs and has been praised for raising the minimum wage and providing aid to low-income families. He is seen as a champion of democracy and human rights in Mexico.

**text-davinci-003**: The president of Mexico in 2019 is Andrés Manuel López Obrador, commonly known as AMLO. He is a Mexican politician who has been the President of Mexico since December 1, 2018. He is a member of the National Regeneration Movement (MORENA) party and previously served as the Head of Government of Mexico City from 2000 to 2005. He is a left-wing populist and has promised to reduce corruption and inequality in Mexico. He has also promised to increase public spending on social programs and infrastructure projects.

---

**Instruction**: Tell me about the king of France in 2019.

**Alpaca-LoRA**: The King of France in 2019 is Emmanuel Macron, who was elected in May 2017. He is the youngest president in the history of the Fifth Republic and the first president to be born after World War II. He is also the first president to have never held elected office before.

**Stanford Alpaca**: In 2019, the King of France was Emmanuel Macron. He was sworn in as President of France on May 14, 2017, and has been leading the country ever since. He is a member of the centrist La République En Marche! Party. He is known for his reformist agenda and has faced a number of protests from both the right and left of the political spectrum. He is the youngest French president in history.

**text-davinci-003**: The King of France is a largely symbolic role, as France is a republic. The current President of France is Emmanuel Macron, who was elected in 2017.

---

**Instruction**: List all Canadian provinces in alphabetical order.

**Alpaca-LoRA**: Alberta, British Columbia, Manitoba, New Brunswick, Newfoundland and Labrador, Nova Scotia, Ontario, Prince Edward Island, Quebec, Saskatchewan

**Stanford Alpaca**: Alberta, British Columbia, Manitoba, New Brunswick, Newfoundland and Labrador, Nova Scotia, Ontario, Prince Edward Island, Quebec, Saskatchewan, Northwest Territories, Nunavut, Yukon.

**text-davinci-003**:

1. Alberta
2. British Columbia
3. Manitoba
4. New Brunswick
5. Newfoundland and Labrador
6. Northwest Territories
7. Nova Scotia
8. Nunavut
9. Ontario
10. Prince Edward Island
11. Quebec
12. Saskatchewan
13. Yukon

---

**Instruction**: Write a Python program that prints the first 10 Fibonacci numbers.

**Alpaca-LoRA**:

```python
def fibonacci(n):
    if n == 0:
        return 0
    elif n == 1:
        return 1
    else:
        return fibonacci(n-1) + fibonacci(n-2)

print(fibonacci(10))
```

**Stanford Alpaca**:

```python
numbers = [0, 1]

for i in range(2, 11):
    numbers.append(numbers[i-2] + numbers[i-1])

print(numbers)
# Output: [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

**text-davinci-003**:

```python
# Fibonacci numbers are the numbers in the following integer sequence, called the Fibonacci sequence, and characterized by the fact that every number after the first two is the sum of the two preceding ones.

# Fibonacci sequence: 0, 1, 1, 2, 3, 5, 8, 13, 21, 34, ...

# Program:

# Initialize the first two numbers of the Fibonacci sequence
a = 0
b = 1

# Print the first 10 numbers of the Fibonacci sequence
print(a)
print(b)

for i in range(2,10):
    c = a + b
    a = b
    b = c
    print(c)
```

---

**Instruction**: Write a program that prints the numbers from 1 to 100. But for multiples of three print 'Fizz' instead of the number and for the multiples of five print 'Buzz'. For numbers which are multiples of both three and five print 'FizzBuzz'.

**Alpaca-LoRA**:

```python
for i in range(1, 101):
    if i % 3 == 0 and i % 5 == 0:
        print("FizzBuzz")
    elif i % 3 == 0:
        print("Fizz")
    elif i % 5 == 0:
        print("Buzz")
    else:
        print(i)
```

**Stanford Alpaca**:

```python
for i in range(1, 101):
    if i % 3 == 0 and i % 5 == 0:
        print('FizzBuzz')
    elif i % 3 == 0:
        print('Fizz')
    elif i % 5 == 0:
        print('Buzz')
    else:
        print(i)
```

**text-davinci-003**:

```javascript
for (let i = 1; i <= 100; i++) {
  if (i % 3 === 0 && i % 5 === 0) {
    console.log("FizzBuzz");
  } else if (i % 3 === 0) {
    console.log("Fizz");
  } else if (i % 5 === 0) {
    console.log("Buzz");
  } else {
    console.log(i);
  }
}
```

---

**Instruction**: Tell me five words that rhyme with 'shock'.

**Alpaca-LoRA**: Flock, lock, rock, stock, and sock.

**Stanford Alpaca**: Five words that rhyme with shock are: rock, pop, shock, cook, and snock.

**text-davinci-003**:

1. Block
2. Lock
3. Dock
4. Clock
5. Rock

---

**Instruction**: Translate the sentence 'I have no mouth but I must scream' into Spanish.

**Alpaca-LoRA**: No tengo boca pero tengo que gritar.

**Stanford Alpaca**: No tengo boca, pero debo gritar.

**text-davinci-003**: No tengo boca pero debo gritar.
