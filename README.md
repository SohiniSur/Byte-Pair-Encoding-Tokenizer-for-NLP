# Byte-Pair-Encoding-Tokenizer-for-NLP

This project implements a Byte Pair Encoding (BPE) tokenizer specifically designed for Bengali text. BPE is a data compression technique that is widely used in natural language processing (NLP) to reduce the vocabulary size and handle out-of-vocabulary words, especially in morphologically rich languages like Bengali.

## Project Overview

This notebook demonstrates the process of building a BPE tokenizer from scratch, using a large corpus of Bengali text. The key steps involve:

1.  **Data Acquisition**: Loading a Bengali dataset.
2.  **Data Preparation**: Processing raw text into a format suitable for BPE training.
3.  **BPE Algorithm Implementation**: Developing the core functions for BPE.
4.  **Tokenizer Training**: Applying BPE merges to construct a vocabulary.
5.  **Encoding and Decoding**: Implementing functions to convert text to token IDs and back.
6.  **Regex-based Tokenization**: Enhancing tokenization by segmenting text with regular expressions before applying BPE.

## Dataset

The tokenizer is trained on the Bengali split (`bn`) of the `ai4bharat/samanantar` dataset, which is accessed via the `datasets` library. The `tgt` (target) column of this dataset, containing Bengali sentences, is extracted and used as the training corpus.

## Data Preparation

The `tgt` column data is written to a file named `Train_txt.txt`. For BPE processing, characters from this file are converted into their Unicode ordinal values (`ord()`) to form the initial sequence of 'tokens' (character IDs). A maximum of 50,000,000 characters were loaded for training purposes.

## BPE Algorithm

The core of the BPE implementation consists of two functions:

*   `get_stats(ids)`: Calculates the frequency of all adjacent pairs of tokens in a given sequence of `ids`.
*   `merge(ids, pair, idx)`: Replaces all occurrences of a frequent `pair` of tokens with a new, single `idx` (token ID) in the `ids` sequence.

## Training the Tokenizer

The tokenizer is trained by iteratively applying merges based on the most frequent pairs. The configuration for training is:

*   `BASE_VOCAB`: Starting ID for new merged tokens, set to `2558` to avoid clashes with existing Bengali Unicode ordinal values.
*   `TARGET_MERGES`: The desired number of merges to perform (e.g., `30000`).
*   `FREQ_THRESHOLD`: An optional stopping condition; merging stops if the most frequent pair occurs `100` times or less.

The process generates a `merges` dictionary mapping each merged `pair` to its new `idx`.

## Vocabulary Construction

A `vocab` dictionary is built, where initial entries map character ordinals to their UTF-8 byte representations. As merges are performed, new entries are added, mapping the merged token IDs to the concatenation of the byte representations of their constituent pairs.

## Encoding and Decoding

Two encoding functions are provided:

*   `encode(text)`: Performs BPE tokenization on the input `text` by first converting it to a list of character ordinals and then iteratively applying the learned merges.
*   `encode_re(text)`: Utilizes a regular expression (`bengali_pat`) to pre-tokenize the input text into meaningful segments (Bengali words, numbers, punctuation, spaces). BPE merges are then applied independently within each segment, potentially leading to more linguistically sensible tokenization.

The `decode(ids)` function reconstructs the original text from a list of token IDs using the `vocab` dictionary, decoding the concatenated byte strings back into a UTF-8 string.

## Results

Initial tests show a compression ratio of approximately **1.11X to 1.28X** for sample Bengali texts, meaning the tokenized representation is smaller than the original character-level representation. This indicates the effectiveness of BPE in compressing Bengali text while preserving information.



ds = load_dataset("ai4bharat/samanantar", "bn", split="train")

