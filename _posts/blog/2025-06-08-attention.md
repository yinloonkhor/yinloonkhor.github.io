---
layout: post
categories: [blog]
description: > 
  Transformer architecture
image: 
  path: https://github.com/user-attachments/assets/eca2af63-30e0-4ae6-bd82-1455977d2a0b
---

# Attention Is All You Need: A Gentle Dive into Transformers
Transformers revolutionized natural language processing by replacing recurrence with attention, enabling models to understand context better, scale more efficiently, and outperform previous architectures on nearly every NLP task. Let’s walk through how this architecture works—from the input embeddings all the way to output generation.

In this post, we’ll **deep dive into the original paper _“Attention Is All You Need”_**, unpacking how the Transformer architecture works—from input embeddings to multi-head attention to output generation.

---

## 1. Embeddings: Turning Words into Numbers
The journey begins by converting words into vectors—a process called *embedding*. For example, the input sentence **“How are you”** is mapped into a series of numerical vectors such as:

`“How” → [0.123, 0.432, 0.286, ..., 0.754]  # (768 dimensions)`

Assuming an embedding dimension of 768 (common in models like BERT), the full sentence becomes a matrix of shape **(3 × 768)** where each row represents a word vector.

These embeddings capture semantic information learned during pretraining, so similar words (like "happy" and "joyful") have vectors close to each other in this high-dimensional space.

---

## 2. Positional Encoding: Injecting Word Order

Unlike RNNs, transformers process all tokens *in parallel*. This means they don’t inherently understand word order. To solve this, **positional encodings** are added to the embeddings to inject information about the position of each word in the sequence.

The authors used sine and cosine functions of different frequencies:

$$
\text{PE}_{(pos, 2i)} = \sin\left(\frac{pos}{10000^{2i/d}}\right), \quad
\text{PE}_{(pos, 2i+1)} = \cos\left(\frac{pos}{10000^{2i/d}}\right)
$$

These fixed encodings allow the model to learn relative positions effectively without requiring learned parameters.

---

## 3. Multi-Head Attention: The Heart of the Transformer

![Attention Head](https://github.com/user-attachments/assets/3c63b7d7-c31f-4900-9748-a81957b96403)
Overview of Scaled Dot-Product Attention and Multi-Head Attention
{:.figure}

Attention mechanisms let the model **weigh the importance of different words in a sentence**, regardless of their distance from one another.

Let’s break it down:

### a) Scaled Dot-Product Attention

From our embedding matrix **(3 × 768)**, we project the vectors into three distinct spaces using learned linear layers to form:

- **Q (Query)**  
- **K (Key)**  
- **V (Value)**  

A clear explanation of queries, keys, and values can be found here: [StackExchange](https://stats.stackexchange.com/questions/421935/what-exactly-are-keys-queries-and-values-in-attention-mechanisms).
In other words, queries and keys are embeddings used to measure similarity, while values are embeddings used to generate the next word.

These are calculated as:

$$
Q = XW^Q,\quad K = XW^K,\quad V = XW^V
$$

Each of these projection matrices has shape **(768 × 256)** if using 3 heads (768 / 3 = 256), resulting in Q, K, and V of shape **(3 × 256)**.

The attention scores are computed as:

$$
\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^\top}{\sqrt{d_k}}\right)V
$$

The division by \\(\sqrt{d_k}\\) (where \\(d_k = 256\\)) helps stabilize gradients and prevents extremely large values that might squash softmax outputs.

Why do this? Because a word like **“apple”** can mean either a fruit or a tech brand. Attention lets the model decide which meaning is appropriate by emphasizing relevant context (e.g., “I ate an apple” vs. “I bought an Apple laptop”).

### b) Multi-Head Attention

Instead of relying on a single attention calculation, we split our embedding space into **multiple heads**. Each head independently computes Q, K, V, learns different relationships, and captures different aspects of the context.

After computing each head’s output (e.g., **(3 × 256)** for 3 heads), we concatenate them back into a single matrix of shape **(3 × 768)** and apply one final linear transformation—optionally reducing it to a smaller dimension like **(3 × 512)** if needed.

---

## 4. Residual Connections, Layer Normalization, and Feedforward Networks

Each attention block is followed by a few more components:

- **Residual Connection:** Adds the original input to the output of the layer (skip connection), helping prevent gradient vanishing and making training deeper networks easier.
- **Layer Normalization:** Stabilizes training by normalizing activations across features.
- **Feedforward Network (FFN):** Applies a small neural network (typically two linear layers with ReLU in between) to each position independently. This allows the model to process information more deeply.

Each encoder block contains these components, and transformer models often stack multiple such blocks—sometimes up to 96 in large models!

---

## 5. The Decoder: Generating Text One Token at a Time

The decoder in the transformer is similar to the encoder with a few key differences:

1. **Masked Self-Attention:** Prevents the decoder from “looking ahead” during training. When generating the next word, it should only see the words that have already been generated.
2. **Cross-Attention:** The decoder attends over the encoder’s final outputs to integrate understanding of the input sentence.

Here’s an example:

- Input: "How are you"
- Decoder begins with the `<start>` token
- Output: "I"
- Decoder input becomes `<start> I` → output "am"
- And so on...

During training, the entire output sentence **`“<start> I am fine”`** is passed in. **Masking** ensures that the model can’t "cheat" by looking at future tokens like "fine" when generating "I".

Finally, a **linear layer + softmax** maps the decoder’s output to a large vocabulary (e.g., 10,000+ words), outputting probabilities for each word. The word with the highest probability becomes the model’s prediction.

---

## Why Transformers Work So Well

Transformers don’t suffer from the *short-term memory* limitations of RNNs. Their ability to attend globally across an entire sequence makes them well-suited for capturing long-range dependencies. Whether you’re analyzing a tweet or an entire book, transformers handle both with ease.

Their parallel nature also makes them highly efficient on modern hardware—paving the way for large-scale pretraining (as seen in models like BERT, GPT, and T5).

---

## Conclusion

The transformer architecture—driven by multi-head attention, positional encoding, and deep stacking—has redefined the state of the art in natural language processing. Thanks to it, machines can now translate, summarize, and even generate text that’s strikingly human-like.

And it all started with a bold claim in 2017: **“Attention is all you need.”**

---

### References and Further Reading
- Vaswani et al., *Attention Is All You Need* (2017): [Arxiv](https://arxiv.org/pdf/1706.03762)
- Illustrated Guide to Transformers Neural Network: A step by step explanation by The AI Hacker: [YouTube](https://www.youtube.com/watch?v=4Bdc55j80l8)
- Natural Language Processing and Large Language Models series by Serrano.Academy: [YouTube Playlist](https://www.youtube.com/playlist?list=PLs8w1Cdi-zvYskDS2icIItfZgxclApVLv)  
- Detailed explanation of Q/K/V: [StackExchange](https://stats.stackexchange.com/questions/421935/what-exactly-are-keys-queries-and-values-in-attention-mechanisms)

