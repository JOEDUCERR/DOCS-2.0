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