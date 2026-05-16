# Bengali BPE Tokenizer for Low-Resource Indic NLP

> A Byte Pair Encoding tokenizer built from scratch on 50 million tokens of Bengali text using the Samanantar Dataset from AI4Bharat.

**AI Research Internship | IAI, TCG CREST | Jun 2025 – Aug 2025**

---

## Why This Exists

Most NLP tokenizers are trained on English-dominant corpora. For low-resource languages like Bengali — with its 128 Unicode codepoints (U+0980–U+09FF), agglutinative morphology, and distinct script boundaries — off-the-shelf tokenizers produce poor compression and misalign subword boundaries.

This project builds a BPE tokenizer **from scratch in Python**, trained on the largest publicly available Indic parallel corpus, to produce linguistically meaningful Bengali subword units and inform downstream LLM architectural constraints.

---

## Dataset

| Property | Detail |
|---|---|
| Source | **Samanantar** corpus (AI4Bharat) — largest publicly available parallel corpora collection for Indic languages |
| Language | Bengali (bn) |
| Training tokens | **50 million** Unicode codepoints |
| Script range | U+0980 – U+09FF (128 Bengali codepoints) |

---

## Algorithm Overview

BPE iteratively compresses a token sequence by merging the most frequent adjacent pair at each step.

```
Raw Bengali Text
      │
      ▼
UTF-8 → Unicode Codepoints (integers)
      │
      ▼
Base Vocabulary: 128 Bengali codepoints + punctuation + digits
      │
      ▼
  ┌─────────────────────────────────┐
  │  LOOP until vocab_size reached  │
  │  1. get_stats()  → pair counts  │
  │  2. top_pair     → most frequent│
  │  3. merge()      → new token ID │
  │  4. replace all occurrences     │
  └─────────────────────────────────┘
      │
      ▼
Learned Merge Rules + Vocabulary
      │
      ▼
encode(text) / decode(tokens)
```

---

## Sample Learned Merges

The first 30 merges reveal core Bengali morphological patterns:

```
Merge: 'ে' (2503) + ' ' (32)  → 'ে '  (2558)   # vowel-sign + space
Merge: 'র' (2480) + ' ' (32)  → 'র '  (2559)   # ra + space
Merge: 'া' (2494) + ' ' (32)  → 'া '  (2560)   # aa-vowel + space
Merge: '।' (2404) + '\n'(10)  → '। '  (2561)   # danda + newline
Merge: 'ে' (2503) + 'র ' (2559)→ 'ের ' (2562)  # common suffix cluster
Merge: '্' (2509) + 'র' (2480) → '্র'  (2563)  # hasanta + ra (conjunct)
Merge: '্' (2509) + 'য' (2479) → '্য'  (2564)  # hasanta + ya (conjunct)
Merge: 'ক' (2453) + 'র' (2480) → 'কর'  (2568)  # 'kar' — high-frequency root
Merge: 'প' (2474) + '্র'(2563) → 'প্র' (2575)  # 'pra' — frequent prefix
```

The algorithm naturally discovers Bengali conjunct consonants (য়, ্র, ্য) and common morphological suffixes as high-frequency merge candidates.

---

## Results

| Configuration | Compression Ratio |
|---|---|
| Standard BPE (30 merges, training set) | **1.28×** |
| Standard BPE (30 merges, test set) | **1.26×** |
| BPE + Regex pre-tokenization (test set) | **1.11×** |

**Compression ratio** = original codepoint length ÷ post-BPE token length.

> Regex pre-tokenization enforces linguistic boundaries (Bengali words, numbers, punctuation, whitespace) before merging — preventing cross-boundary merges that produce linguistically invalid subwords.

---

## Encoding / Decoding

```python
# Encode raw Bengali text → token IDs
token_ids = encode("বাংলাদেশ একটি সুন্দর দেশ।")
# → [2579, 2453, 2404, ...]   (compressed integer sequence)

# Decode token IDs → original text
text = decode(token_ids)
# → "বাংলাদেশ একটি সুন্দর দেশ।"
```

---

## Tech Stack

| Tool | Usage |
|---|---|
| Python (stdlib only) | Full BPE implementation — no tokenizer libraries |
| Unicode / UTF-8 | Codepoint-level text representation |
| `re` (regex) | Pre-tokenization for linguistic boundary enforcement |
| Samanantar / AI4Bharat | Bengali training corpus |


---

## Further Reading

- Sennrich et al. (2016) — [Neural Machine Translation of Rare Words with Subword Units](https://arxiv.org/abs/1508.07909) (original BPE paper)
- AI4Bharat Samanantar: https://ai4bharat.iitm.ac.in/samanantar/
- Unicode Bengali block: U+0980–U+09FF

---

*Part of AI Research Internship at Institute for Advancing Intelligence (IAI), TCG CREST, Kolkata, Jun–Aug 2025.*
