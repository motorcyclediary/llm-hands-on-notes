## Chapter 2 - Key intuitions

#### What is the big picture?

The machine learning models are ultimately mathemamtical models and require numerical representations of data to execute their transforms.

1. Splitting text into _tokens_ and given these tokens a numerical form is _tokenization_.
2. Transforming static, scalar numeric representations of into higher dimensional vectors that contain concepts of meaning and similarity is _embedding_.

### STEP 1 - Tokenization of text

Tokenisation is the process of transforming text into a numerical form. This process is carried out by applications called **Tokenizers**.

Very first step is to break the text into chunks. These are called **tokens**

There are various approaches to how words can be translated into tokens. These are called tokenisation schemes:

- Word
- Subword
- character
- Byte

The listed schemes reduce in order size word size. The larger the size the of token, the more 'information' it be deemed to carry. That's the same as saying that it carries more specific infomation than say subword and a character. Therefore the larger the token 'size' the larger the size of the Tokeniser **vocabulary**.

A tokenizer vocabulary completely describes in corpus of text through explicit mapping of string combination and special tokens to represent unknown or special characters.

A tokenizer is created from a corpus of text. Typically large and regarded as comprehensive wrt a particular domain but all be all enencompassing for use in the most powerful models.

There are 3 main tokenisation methods used by modern LLM models:

- Byte-Pair - Starting with byte representations of the input text, the Byte-Pair tokenisation recursively looking for repeating patterns between pair of token (staring at the byte scale) and adds to most frequently occuring pair to the base vocabulary of vocabulary. It repeats this recursively on the corpus using the updated token vocabulary, with the most frequently occuring token pair being added to the vocabulary in each cycle. With each iteration the size of the vocabulary grows by one until the desired vocabulary size is reached..
- WordPiece - The same as Byte-Pair with the change that the new token is selected based on the maximum likelihood value rather than frequency
- SentencePiece - This address the shortcoming of addressing of languages which are not decomposable into characters (i.e. Chinese). It treats the entire sentence as single stream and encodes any spaces as part of a word - **I don't have the intuition for this**

#### What's the end product of tokenisation?

A vocabulary of unique tokens is provided by a tokeniser. This is essentially a lookup table of an integer ID and the corresponding token.
Input text is broken into its corresponding token representation using the vocabulary. The text is physically stored using the numerical values (IDs) of the tokenizer vocabulary.

A tokenizer is chosen or designed with an application in mind and involves trade offs in terms model size and complexity, training data, training effort and domain specific optimisation (i.e. code, medical literature etc).

The design process involves making choices for the tokenisation method, vocabulary size, special tokens and source training corpus.

##### RESULT => We now have the text (human language) represented in raw numerical form (machine language).

### STEP 2 - The Embedding (meaning & similarity)

The **Embedding** is the next key step of the language processing within LLMs.

The embedding endows 2 crucial qualities on the text (now in numerical token form) that's been provided to the machine:

1. **Meaning** contained within/by the token
2. **Similarity** between tokens

The outcome of the embedding is that the tokens are translated into higher dimensional vector space. In it's naive, non-contextualised form, each token maps to a single vector value in the embedding space.

### Meaning

Within the embedding, the meaning (in some machine sense) of a token is expressed by the value of the vector. The dimensionality of the vector can be likened to the degree of nuance of meaning the the embedding is attempting to model.

### Similarity

This the other key purpose of an embedding. Represent the 'similarity' between tokens as the distance between vectors
machine representation of human language

The embedding can be designed to contain a textual body greated than a typical token, i.e. sentences or even whole paragraphs - which can be regarded as mega-tokens :)

When sequence or proximity of nearby vocabulary is taken into account, then the concept of embeddings reframed to power recommendation engines and robotics (we didn't cover any instances of this!).

# Word2Vec

Word2vec is method to create embedding from a source corpus of text. It has been superceded but it's a canonical algorithm to understand contextualised embedddings.

So the big picture...

### The intuition

Word2Vec creates an embedding for a token, which is word based so therefore a word, based on the the neighbouring words. That is to say the meaning representation of the word within the Word2Vec embedding of the word is derived from the words that surround the word.

If 2 different words are used identically in sentences then they should both, in theory, have the same vector value - i.e be at the a same location in the embedding vector space. Similarity is based on the degree to which they are synonymous. Being completely synonymous in usage should result in an identical (in naive instance - more likely very close) embedding vector value being used by both words.

## Embedding generation using Word2Vec

With the above intuition that meaning of the word is derived from the words it's used alongside, and words that can be used with identical neighbours - i.e. synonymously - the problem can now be framed as a classical superivised learnign problem to be addressed using a 2-layer feedforward neural net.

#

We now need to choose one of appoaches to generate training data

### Steps to generate training data for supervised learning

1. We first choose a 'window size' which is a length in words of a snipper of text from the source training corpus. A sliding approach can be taken centering on each word which is referred to as the target word below. The other words in the window are regarded as neighbours.

2. We then take either Skipgram or CBOW (continuous bag of words) approach to specifically specifiy the SL problem being solved and use it to generate training samples from each window.

   - a. **_Skipgram_**: The target word is being predicted using the neighbouring words. So assuming a window size of 3, let's say we have the following snippet: 'from over the'. We wil get 2 rows to represent each unique neighbouring word:<br>

     | Centre | Neighbour | Target |
     | ------ | --------- | ------ |
     | over   | from      | 1      |
     | over   | the       | 1      |

   - b. **_CBOW (Continuous Bag of Words)_**: Interestingly the text doesn't cover this approach. Here the centre word is used to predict the surrounding words. So using our simple example we would only yield one row for the given window size:

| Centre | Neighbour   | Target |
| ------ | ----------- | ------ |
| over   | [from, the] | 1      |

3. Negative samples of non-occuring combinations are generated to enable the model to accurately distinguish the correct neighbours (context) for any word from non-occuring neighbours. This is referred to as _contrastive estimation_

4. The NN is trained with the weights of the second (hidden) layer yielding the embedding matrix. The size of this second layer represents the dimensionality of the embedding.

Notes:

- Larger corpus / higher dimensions - slower - Skipgram
- Smaller coportaus - faster - CBOW

#### Extended usages of embeddings

The text illustrates how the principle neighbouring context to asribe similarity can be extended to non-NLP usage such as recommendation engine for a music playlist. The order of songs played is analagous to sequences of tokens with each playlist analagous to a sentence.
