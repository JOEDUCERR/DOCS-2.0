# Introduction
* The first language models emerged in the 1950s. These were basic next-word prediction models.
* You can think of language models as a completion machine.
* Basic unit of language in all kinds of language models is referred to as a "Token". It is the most basic unit of a language in accordance to LMs. These LMs go through a process of "Tokenization". The set of all tokens a model can work with is called "vocabulary".
* GPTs vocabulary size is 100k.
* Language models specifically blew up due to self supervision learning. Basically it is different from Unsupervised learning as it totally does not need labels yet Self supervision is the model assigning labels to the data understanding the data itself.
### Types of Language models
1. Masked language model: Trained to be able to fill in the blanks. Commonly used for sentiment analysis and text classification. It uses both context from before the blank and after the blank.
2. Auto regressive language model: Differs from masked language model as it only uses context as words before the blank. It is more widely used in this day and age.
Both of these types are purely predictive and is not guaranteed to be correct.

--------------------------------------------------------
* "Parameter" a variable in a model which is constantly updated through training. A models size is calculated by the number of parameters it has. First GPT had 117 million.
* "Foundation Models" like Gemini or GPT-4V are called as they are due to their breakthroughs in traditional AI research.
* A model that can work with more than one data modality is also called a multimodal model.
* ![[Multimodal model.png]]
* ![[Benchmark for LMs.png]]
* People have been building AI applications for over a decade—a process often known as ML engineering or MLOps (short for ML operations).
* AI engineering refers to the process of building applications on top of foundation models. Basically leverage existing ones.
* Involving humans in AI's decision making is called 'Human-in-the-loop'.
* AI Engineering differs from ML engineering where it is more about model adoption and tuning rather than model development in itself.
* Prompt engineering differs from model tuning where it is more about getting a closer to expectation response without updating model weights and the latter is vice versa. Basically we use fine tuning when we want that model to work on something that was not included in it's training.
* With the availability of foundation models, ML knowledge is no longer a must-have for building AI applications. I’ve met many wonderful and successful AI application builders who aren’t at all interested in learning about gradient descent. However, ML knowledge is still extremely valuable, as it expands the set of tools that you can use and helps troubleshooting when a model doesn’t work as expected.
* Traditional ML models are close ended meaning they can only give an output based on the data and is limited meanwhile Foundation models are open ended.
### Types of training:
PROMPT ENGINEERING IS NOT TRAINING. (But: By using a different prompt engineering technique, Gemini Ultra’s performance on MMLU [Massive Multitask Language Understanding] went from 83.7% to 90.04%.)
* Pre Training: Training a model from scratch with randomly initialized weights. There is a high value in the job market for people who are experts of pre training as one mistake during pre training costs a lot.
* Fine Tuning: Continue to train a previously trained model by modifying the weights. Much less costly than Pre Training.
* Post Training: Basically fine tuning done by the model developers themselves.

--------------------------------------------------------

* While many AI engineers come from traditional ML backgrounds, more are increasingly coming from web development or full-stack backgrounds. An advantage that full-stack engineers have over traditional ML engineers is their ability to quickly turn ideas into demos, get feedback, and iterate.

--------------------------------------------------------
# Foundation Models

* An extremely time-taking, costly and resource heavy task often done by professionals only who do not the secret sauce easily.
## Training Data + Real world Examples
* Non Profit Organization created 'Common Crawl' by crawling websites on the Internet. Google provides a clean version of this called C4. Used by: GPT-3 and Gemini.
* OpenAI used only the Reddit links that received at least three upvotes to train GPT-2.
* For the MASSIVE dataset, the median token length in English is 7, but the median length in Hindi is 32, and in Burmese, it’s a whopping 72, which is ten times longer than in English.
* One of the most famous domain-specific models is perhaps DeepMind’s AlphaFold, trained on the sequences and 3D structures of around 100,000 known proteins.
* More Medical Models: NVIDIA’s BioNeMo, Google’s Med-PaLM2.
## Modeling the model
* Most dominant architecture for LMs is the 'transformer' (2017) arch based on attention mechanism. This helped processing multiple input tokens in parallel unlike RNN. But normal transformer architecture was also acting as a bottleneck.
* The solution was attention mechanism. The attention mechanism computes how much attention to give an input token by performing a dot product between the query vector and its key vector. A high score means that model will use more of the page's during generation of the summary.

![[Attention mechanism with transformers.png]]

* **Why is it hard to extend context length for transformer models?** : This is because each previous token has a key and value vector. The longer the sequence, the more key and value vectors need to be computed and stored.
* To see more in-depth detail about how a transformer model works mathematically we see [[How a Transformer thinks]].
* A few advancements have been made to traditional transformer model by introducing SSS (Structured State Spaces) and then moving onto H3, Mamba and Jamba. These use multiple techniques to outperform models having more parameters using these techniques.
## Model Size
The scale of a model can be determined by Number of Parameters, Number of training tokens and number of FLOPs needed for training.
* **How do we determine which Memory Size GPU needed to do inference using a model?**: The number of parameters helps us estimate the compute resources needed to train and run this model. For example, if a model has 7 billion parameters, and each parameter is stored using 2 bytes (16 bits), then we can calculate that the GPU memory needed to do inference using this model will be at least 14 billion bytes (14 GB).
* Models are trained these days using datasets like: Llama 1: 1.4 trillion tokens, Llama 2: 2 trillion, Llama 3: 15 trillion (these are training tokens unlike model's own dataset tokens).
* Assume that you have 256 H100s. If you can use them at their maximum capacity and make no training mistakes, it’d take you (3.14 × 1023) / (256 × 5.2 × 1018) = ~236 days, or approximately 7.8 months, to train GPT-3-175B. (this would cost around 4 million USD [Rs. 38,09,58,600]).
* Anthropic discovered in 2022 that Post-training a model too much leads to it not aligning with human preference and the model tends to be more politically and socially firm: it starts taking sides.
* According to 'Chinchilla scaling law', number of training tokens should be **20 times the model size**. E.g. 3B model needs 60B training tokens.
* Smaller models are trained multiple times so we can edit their hyperparameters like epoch, learning rate, batch size etc. But this is not possible for extremely large models as they require heavy resources for one training itself. The industry uses techniques like **Scaling extrapolation: comparing how hyper parameters from smaller models translate to larger ones**. And then transfer them.
* Main bottlenecks: Training data and electricity. There is a probability that there will be a lack of dataset in terms of size. Plus many websites now have changed their data terms to avoid scraping.
## Post Training
The difference is that unlike Pre training where the prediction of the model is trained, Post training optimizes the generated responses to match the user preference much better.
* Basically optimizing the model to generate responses that users prefer. (Basically understanding how to use the knowledge already learnt).
* Many model builders use Reinforcement learning to avoid the issues.

![[Shoggoth AI.png]]

* A. **Supervised Finetuning** (Finetune model on high quality instruction data) and B. **Preference finetuning** (Typically done using Reinforcement learning from human feedback [RLHF]).

![[Training workflow with Pre and Post methods.png]]

* Basically, Self-Supervised training -> Supervised finetuning -> Preference finetuning [RLHF].
* [[Post Training]] : Deep Dive.
## Sampling
Process of constructing output of a model. Modifying it can make the outputs more creative or suitable for application or predictable.
* For an LLM to generate the next token, the model first computes the probability distribution over all tokens.
* Greedy Sampling: always picking the sample with the highest probability. (Works for classification but creates non creative outputs for LLMs).
* Instead of greedy, LLMs compare the probability distribution over all tokens and will just just pick the highest blindly.
* LLMs output a 'Logit' vector where each logit corresponds to one token of vocabulary. (logits can be converted to probabilistic values using a softmax layer)
* **Temperature**: Higher temp reduces the probabilities of common tokens (more creative responses). **Temp adjust the logits**. On 0 temp, the model does Greedy Sampling.
* To solve the issue where there is a large vocab size, instead of logits we use 'Logprobs' where logarithm is used as for a large vocab, probabilities can become really small. (OpenAI  uses Logprobs)
* Most model providers provide little to no access to their logprobs as it becomes easier to replicate the model.
* **Top-k**: Normal softmax requires two passes over all possible values and is computationally expensive. So after ligits computation, we pick only the top-k and perform softmax on those.
* **Top-p**: Also known as nucleus sampling. Model sums the probabilities of most likely values in descending orders till sum reaches p. This allows for more dynamic selection of number of logits to be sampled. It helps target only the relevant values for each context.
* This means that a 100-million-parameter model that uses a verifier can perform on par with a 3-billion-parameter model that doesn’t use a verifier. (thats how they train low parameter models to perform on par with larger models).
* It can be said that allocating more compute to generate more outputs during inference can be more efficient than simply increasing model size.
* A model is considered robust if it doesn’t dramatically change its outputs with small variations in the input.
* Structured outputs: Firstly models must be able to do text-to-sql or text-to-regex to give output to specific values and SQL queries must be of correct syntax to work. They have to be of valid classes. Secondly, models must be able to generate content in formats which might be used by different apps like json format or .md file format. (models can only verify the output will be json, not the assurity that json code will be correct.)
* You can guide a model to generate structured outputs at different layers of the AI stack: prompting, post-processing, test time compute, constrained sampling, and finetuning. The first three are more like bandages. They work best if the model is already pretty good at generating structured outputs and just needs a little nudge. For intensive treatment, you need constrained sampling and finetuning.
* **Constrained Sampling**: Technique used for guiding generation of text toward certain constraints. In this sampling techniques only logits which meet the constraints are sampled.
* ![[Filter out logits.png]]
* In many cases constrained sampling needs explicitly hardcoded grammar to specify what is and is not allowed at each step (e.g. json grammar is different from yaml).
* **Finetuning**: You can retrain the whole model end-to-end or part of the model like the classifier head (between embeddings and logits). The former requires more resources but promises better results.
* "The way outputs are sampled in all the techniques mentioned here, it makes the model probabilistic and not deterministic." This can cause hallucinations.
## Probabilistic Nature
This makes the model creative and fun to talk to, but can lead to inconsistencies and hallucinations.
* Models can be inconsistent.
* Can also hallucinate. **Snowballing hallucination** happens when a model generates the wrong assumption and then continues hallucinating using that wrong info.
* To solve this: The first technique comes from reinforcement learning, in which the model is made to differentiate between user-provided prompts (called observations about the world in reinforcement learning) and tokens generated by the model (called the model’s actions). The second technique leans on supervised learning, in which factual and counterfactual signals are included in the training data. (You can also ask the model to fetch a credible source).
* During SFT, models are trained to mimic responses written by labelers. If these responses use the knowledge that the labelers have but the model doesn’t have, we’re effectively teaching the model to hallucinate.
* Hallucinations are way worse for models using both RLHF and SFT at once rather than just SFT.
-----------------------------------
## Evaluation
* For an open ended model (unlike traditional ML models), it is way harder to evaluate them. We cannot just prompt and check each ground truth. Foundation models are **Black Boxes** as providers cannot expose details.
* 2018 Glue (General Language Understanding Evaluation), 2019 Super-GLUE, Super-NatualInstructions 2022, MMLU 2020 all these Benchmarks have become obsolete as soon as a model achieves the perfect score. Now MMLU-Pro is used.
* Evaluation lags behind in funding, attention and research.
### Model Metrics
* Entropy: The number of bits used in a token. e.g. if you want to describe a location within a square you need two values, hence in one token more bits. Hence its entropy would be 2. If only 1 bit us being used in a token its entropy will be 1. It also means how difficult it is to predict what comes next in a language.
* Higher entropy languages are more difficult to predict.
* **Cross Entropy**: Language model's cross entropy on a dataset measures how difficult it is for the language to predict what comes next in the dataset. 1. Training data's predictability is tested by training data's entropy. 2. How language model's distribution differs from true distribution of the data.
* Language models are trained to minimize their cross entropy with respect to training data.
* **Perplexity**: Exponential of entropy and cross entropy. It measures the amount of uncertainty it has when predicting the next token. Higher the uncertainty, higher the possible options for the next token.
* More Structured data: Lower perplexity, Big vocab: Higher perplexity, Long context length: Lower perplexity.
* Perplexity might not be a great proxy to evaluate models that have been trained by SFT or RLHF. As the models perplexity increases after post training, it becomes harder to predict the next token. [Quantization-A technique to reduce model's numerical precision and memory footprint also changes models perplexity].
* cross entropy, perplexity, BPC, and BPB are variations of language models’ predictive accuracy measurements.
* **PPL is also used to check the usefulness of a benchmark. If the PPL comes low during benchmarks, the benchmark itself becomes less trustworthy as the model might be performing well as it is being testing with data it was exactly trained on.**
* PPL is the highest for unpredictable texts.
* We also can check if a model is giving factual answers or not by Functional correctness (if the model does what it was intented to) and Similarity measurement against reference data (outputs are checked against ground truths).