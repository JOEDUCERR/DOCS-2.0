This section is explaining **how a Transformer "thinks" about relationships between words**. I'll go through it in the order the model actually executes it rather than the textbook order.

---

# Big Picture

Imagine the sentence:

> **"The cat sat on the mat because it was tired."**

When the model reaches the word **"it"**, it has to determine:

- Does "it" refer to **cat**?
    
- Does it refer to **mat**?
    

Attention is the mechanism that lets the model look back at previous words and decide which ones are important.

Everything in this chapter is about implementing that one idea mathematically.

---

# Step 1: Tokens become vectors

Suppose the sentence is

```
The cat sat
```

After tokenization it becomes

```
["The", "cat", "sat"]
```

Each token becomes an embedding vector.

For Llama-2 7B:

```
"The"

[
0.12,
-0.41,
...
4096 numbers
]
```

Every token is represented by **4096 floating point numbers**.

Think of this as

```
Token
   ↓
Embedding Layer
   ↓
4096-dimensional vector
```

So if there are 3 words

```
The
cat
sat
```

you now have

```
3 vectors

x1
x2
x3
```

Each vector has size

```
4096
```

---

# Step 2: Why do we create Q, K and V?

The embedding itself isn't enough.

The model wants each word to answer three different questions.

### Query (Q)

"What information am I looking for?"

---

### Key (K)

"What kind of information do I contain?"

---

### Value (V)

"What information should I actually give if someone attends to me?"

Think of a library.

Each book has

```
Book

Key
→ title, author, topic

Value
→ actual contents

Query
→ what you're searching for
```

When searching

```
Query:
"I want books about AI."

↓

Compare with every Key.

↓

If a Key matches

↓

Read its Value.
```

Exactly the same thing happens inside attention.

---

# Step 3: How are Q, K and V made?

The book writes

```
K = xWK

V = xWV

Q = xWQ
```

Let's simplify.

Suppose

```
x
```

is your embedding vector

```
4096 numbers
```

The model owns three huge matrices

```
WK

WV

WQ
```

Each is

```
4096 × 4096
```

These matrices are just **learned weights**.

They transform one representation into another.

```
Embedding

↓

WQ

↓

Query vector
```

Likewise

```
Embedding

↓

WK

↓

Key vector
```

and

```
Embedding

↓

WV

↓

Value vector
```

Notice:

Same input.

Different matrices.

Different outputs.

---

# Why three matrices?

Because one vector can't do three jobs simultaneously.

The model learns different representations.

For example

Input

```
cat
```

Embedding

```
x
```

becomes

Query

```
Looking for...
```

Key

```
I represent an animal
```

Value

```
Animal information
```

---

# Step 4: What happens next?

Now every token has

```
Q

K

V
```

Suppose we have

```
The
cat
sat
```

We now have

```
Q1 K1 V1

Q2 K2 V2

Q3 K3 V3
```

---

# Step 5: Compare Query with every Key

This is the heart of attention.

The equation

[  
QK^T  
]

means

> Compare every query against every key.

Suppose we are processing

```
sat
```

Its query is

```
Qs
```

We compare it with

```
K(The)

K(cat)

K(sat)
```

Suppose similarities become

```
0.1

8.5

2.0
```

This means

```
sat

mostly cares about

cat
```

---

# Step 6: Why divide by √d?

The equation is

[  
\frac{QK^T}{\sqrt d}  
]

where

```
d
```

is the size of each attention head.

For Llama

```
d = 128
```

Why divide?

Because when vectors become very large,

```
QK^T
```

can become huge.

Large numbers make Softmax unstable.

Dividing keeps numbers reasonable.

Think of it like normalization.

---

# Step 7: Softmax

Suppose similarities are

```
2

8

1
```

Softmax converts them into probabilities.

Result

```
0.002

0.996

0.002
```

Now we know

```
Almost all attention

goes to

cat
```

---

# Step 8: Multiply by Values

This is the last step.

We don't actually copy Keys.

We use them only for matching.

Once attention weights are known,

we combine the Values.

Mathematically

```
Attention

=

weights

×

Values
```

So if

```
cat

gets 99%

attention
```

its Value contributes almost everything.

The final output becomes

```
0.99 × V(cat)

+

0.01 × others
```

---

# Multi-head Attention

Instead of one attention calculation,

Llama does **32 at the same time**.

Originally

```
4096 numbers
```

are split into

```
32 heads

128 dimensions each
```

```
4096

↓

Head1 128

Head2 128

Head3 128

...

Head32 128
```

Each head learns something different.

Example:

Head 1

```
Grammar
```

Head 2

```
Pronouns
```

Head 3

```
Verb agreement
```

Head 4

```
Locations
```

Head 5

```
Time
```

...

This lets the model look at multiple types of relationships simultaneously.

---

# Why concatenate?

Each head produces

```
128 numbers
```

There are

```
32 heads
```

So

```
32 × 128

=

4096
```

They are stacked back together.

```
Head1

Head2

...

Head32

↓

4096 numbers
```

---

# Output Projection

The book mentions another matrix.

```
WO
```

After concatenating

```
4096

↓

WO

↓

4096
```

This lets the model mix information from different heads.

Without this step,

the heads would never interact.

---

# Transformer Block

A Transformer block consists of two major components:

```
Input

↓

Attention

↓

Residual Add

↓

LayerNorm

↓

MLP

↓

Residual Add

↓

LayerNorm

↓

Output
```

Modern models like Llama use this structure (with some variations such as RMSNorm instead of LayerNorm).

The book simplifies this into:

```
Attention

↓

MLP
```

---

# What is the MLP?

Attention allows tokens to exchange information.

The MLP processes each token **independently** to build richer features.

It is essentially a small neural network.

Typical flow:

```
4096

↓

Linear

↓

11008

↓

Activation

↓

4096
```

The activation (such as GELU or SwiGLU in newer Llama models) introduces nonlinearity, allowing the network to learn complex patterns that simple matrix multiplication cannot.

---

# Embedding Layer

Before any Transformer blocks:

```
Token

↓

Embedding

↓

Position Encoding

↓

Transformer
```

The model needs both:

- **What** the token is.
    
- **Where** it appears in the sequence.
    

For example:

```
Dog bites man

≠

Man bites dog
```

The words are the same, but their positions change the meaning.

---

# Output Layer

After all Transformer blocks:

```
4096 numbers

↓

Unembedding Matrix

↓

Vocabulary logits

↓

Softmax

↓

Next token probabilities
```

Suppose the vocabulary has 128,000 tokens.

The output layer produces one score for every token.

```
cat      9.2

dog      1.3

mouse    3.1

apple   -2.5
```

Softmax converts these scores into probabilities, and the model samples the next token.

---

# Putting It All Together

For one input sentence, the data flows like this:

```
Text
        │
        ▼
Tokenizer
        │
        ▼
Embedding Layer
        │
        ▼
4096-dimensional vectors
        │
        ▼
Create Q, K, V
        │
        ▼
Multi-Head Attention
        │
        ▼
Concatenate Heads
        │
        ▼
Output Projection
        │
        ▼
MLP (Feedforward Network)
        │
        ▼
Repeat for every Transformer Block (e.g., 32 layers in Llama 2-7B)
        │
        ▼
Final Hidden Vector
        │
        ▼
Output (Unembedding) Layer
        │
        ▼
Vocabulary Scores
        │
        ▼
Softmax
        │
        ▼
Next Token
```

A useful way to think about the division of responsibilities is:

- **Embeddings**: represent the meaning of each token.
    
- **Q, K, V projections**: create specialized representations for searching (Q), matching (K), and sharing information (V).
    
- **Attention**: determines which previous tokens are relevant.
    
- **Output projection**: combines information from all attention heads.
    
- **MLP**: transforms each token's representation into a richer one.
    
- **Repeating Transformer blocks**: progressively refine the understanding of the entire sequence.
    
- **Output layer**: converts the final representation into probabilities over the vocabulary.
    

Once this pipeline is clear, the mathematical expressions in the attention mechanism become much more intuitive—they are simply the implementation of "look up the relevant tokens, combine what they know, refine the representation, and repeat."