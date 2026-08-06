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
* Most dominant architecture for LMs is the 'transformer' arch based on attention mechanism. This helped processing multiple input tokens in parallel unlike RNN. But normal transformer architecture was also acting as a bottleneck.
* The solution was attention mechanism. The attention mechanism computes how much attention to give an input token by performing a dot product between the query vector and its key vector. A high score means that model will use more of the page's during generation of the summary.
![[Attention mechanism with transformers.png]]
* **Why is it hard to extend context length for transformer models?** : This is because each previous token has a key and value vector. The longer the sequence, the more key and value vectors need to be computed and stored.