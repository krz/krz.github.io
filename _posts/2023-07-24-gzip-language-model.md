---
layout: post
title: The gzip language model
categories: [machine learning]
---

You may have seen a [preprint online](https://arxiv.org/pdf/2212.09410.pdf) about a weird way of doing text classification tasks. Some researchers used __gzip__, a compression algorithm, instead of a neural network, and got amazing results (beating BERT and other neural networks for text classification).

![wait, what?](https://media.tenor.com/EksWJ-xY5EsAAAAM/wait-what.gif)

__How is that possible?__

It’s because compression and language models are both based on information theory. They both try to encode text efficiently, but with different goals. Compression wants to make the file size as small as possible, without losing any information. Language models want to capture meaningful linguistic features, without restoring the original text.

So gzip is a good language model, even if it doesn’t care about meaning. It can pass many language benchmarks, because it encodes text in a way that is similar to Huffman coding, a common compression algorithm.

## But what’s the point of this experiment?

Well, it’s not to replace neural language models with gzip. Gzip is not trainable or adaptable, so it can’t handle different domains or tasks. Neural networks can encode text in different ways, depending on the data. But it’s still fascinating to see how a simple compression algorithm can do so well on complex language tasks.

## How does it work?

[Gzip](https://en.wikipedia.org/wiki/Gzip) is a software application that can compress and decompress files using the [Deflate](https://en.wikipedia.org/wiki/Deflate) algorithm. Deflate is a combination of two techniques: LZ77 and Huffman coding. LZ77 reduces redundancy by finding repeated sequences of bytes and replacing them with references to their previous occurrences. [Huffman coding](https://en.wikipedia.org/wiki/Huffman_coding) assigns shorter codes to more frequent symbols and longer codes to less frequent ones, thus minimizing the number of bits needed to represent the data.

Huffman coding is based on information theory, which studies how to measure and communicate information. One of the key concepts in information theory is entropy, which measures the uncertainty or randomness of a source of data. The more predictable the data is, the lower its entropy is, and the more it can be compressed.

A language model is a probabilistic model that assigns probabilities to sequences of words or symbols. A good language model should capture the regularities and patterns of natural language, such as syntax, semantics and pragmatics. A good language model should also have low entropy, meaning that it can predict the next word or symbol with high accuracy.

__So how does gzip use Huffman coding as a language model?__

The answer is that Huffman coding implicitly encodes some linguistic information in the compressed data. For example, if a word or symbol is very frequent in the data, it will have a short code in the Huffman tree. This means that it will also have a high probability in the language model. Conversely, if a word or symbol is very rare in the data, it will have a long code in the Huffman tree, and a low probability in the language model.

This way, gzip can capture some basic features of natural language, such as word frequency, Zipf’s law and n-gram statistics. However, gzip does not care about meaning or context, so it cannot handle more complex linguistic phenomena, such as synonyms, antonyms, idioms or metaphors.

Therefore, gzip is not a very sophisticated language model, but it is still surprisingly effective for some tasks.
