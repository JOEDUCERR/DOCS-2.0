# Supervised Finetuning
* Before Post training the model is only capable of completing sentences, as it has no idea it is supposed to be a conversation.
* To encourage the model to give right responses we tend to feed it with similar pattern data later on like "Q and A" type conversations.

![[Pasted image 20260808171129.png]]

* GPT used highly educated labelers to generate demonstration data.
# Preference Finetuning
* This is highly important as models often tend to take sides and answer wrongly in tricky questions which relate to gender, race, religious backgrounds, abortion, gun control, etc.
* Companies used RLHF to train models to give the appropriate responses to these kinds of topics. That's where labelers come in. Many companies hired AI response labelers whose data would be used in PF.
* We create a **Reward mode**l using the labeled data where a pair of prompt and response with the output score is given. This comes under an ML task.
* Finally the Reward Model is then used to evaluate the main models responses in order to finetune it.