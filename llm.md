# How LLMs Actually Think: A Visual Guide

## 🎯 The Big Picture

When you ask an LLM a question, it doesn't "understand" like you do. Instead, it performs a sophisticated pattern-matching dance through billions of learned parameters. Here's what actually happens:

---

## 📚 Quick Terminology Guide

### What is a "Transformer"?
**Simple Definition:** A neural network architecture that processes text by letting every word "pay attention" to every other word simultaneously.

**Why it's called Transformer:** It transforms input text into output text by learning relationships between all parts at once.

**Example:** When reading "The bank by the river," it figures out "bank" means riverbank (not money) by looking at "river" - all in one pass!

---

### Encoder vs. Decoder
Think of these as two different reading strategies:

**🔍 Encoder (Understanding Mode)**
- Reads the ENTIRE sentence at once
- Can see words before AND after any word
- Like reading a book page - you can look anywhere
- **Used for:** Understanding, classification, answering questions
- **Examples:** BERT, RoBERTa

**✍️ Decoder (Generation Mode)**
- Reads LEFT to RIGHT only
- Can only see previous words, not future ones
- Like writing a story - you only know what you've written so far
- **Used for:** Text generation, chatbots, completion
- **Examples:** GPT, Claude (what you're using now!)

**Visual Example:**
```
Encoder reading "The cat sat on the mat":
The ←→ cat ←→ sat ←→ on ←→ the ←→ mat
(All words connected, can see everything)

Decoder generating "The cat sat on the mat":
The → cat → sat → on → the → mat
(Only sees left, generates right)
```

---

### What is "Attention"?
**Simple Definition:** A way for the model to decide which words are important when thinking about each word.

**The Problem It Solves:** 
Old models would forget the beginning of long sentences. Attention lets models "look back" and remember what matters.

**Real Example:**
```
Sentence: "The animal that had been living in the zoo escaped"

When processing "escaped", the model:
- Looks back at ALL previous words
- Decides "animal" is most important (85%)
- Decides "zoo" is somewhat important (10%)
- Ignores "the", "that", "had", etc. (5%)

Result: Understands that an animal escaped from a zoo
```

**Analogy:** Like highlighting important parts of a textbook. When studying for an exam, you focus on the highlighted parts, not every word.

---

## 📝 Stage 1: Breaking Down Your Words

### Tokenization
**What it does:** Splits your text into digestible chunks called tokens

**Your question:** "How does an LLM work?"

**What the model sees:**
```
["How", " does", " an", " L", "LM", " work", "?"]
```

**Why it matters:** 
- Common words = 1 token: "the", "cat", "work"
- Rare words = multiple tokens: "Anthropomorphization" → ["Anth", "rop", "omorph", "ization"]
- Most models use 32,000-100,000 different tokens

**Real Example:**
```
"Hello world!" → ["Hello", " world", "!"]  (3 tokens)
"antiestablishmentarianism" → ["anti", "establishment", "arian", "ism"]  (4 tokens)
```

**Better analogy:** Like autocomplete on your phone that learns common patterns ("ing", "tion") instead of memorizing every possible word.

---

## 🎨 Stage 2: Converting Words to Math

### Embeddings
**What it does:** Transforms each token into a list of numbers that captures its meaning

**Example:**
```
"cat"  → [0.2, -0.8, 0.5, ..., 0.1]  (768 numbers)
"dog"  → [0.3, -0.7, 0.4, ..., 0.2]  (similar numbers!)
"car"  → [-0.6, 0.1, -0.9, ..., 0.8] (very different numbers)
```

**Why similar words have similar numbers:**
The model learned from seeing millions of examples:
- "I pet my cat" / "I pet my dog" → both appear in similar contexts
- "I drive my car" → appears in different contexts

**Visual Concept:**
```
Meaning Space (simplified to 2D):

    kitten •
      cat •     • puppy
         dog •
              
              
    airplane •
         car •    • truck
```

**Better analogy:** Like GPS coordinates - similar words are neighbors in a 768-dimensional meaning-space.

---

## 📍 Stage 3: Adding Word Order

### Positional Encoding
**What it does:** Adds information about where each word appears in the sentence

**The Problem:**
Without position info:
- "Dog bites man" = "Man bites dog" (very different meanings!)
- "How does an LLM work?" = "Work does how LLM an?" (nonsense!)

**How it works:**
```
"How does an LLM work?"

Position 1 (How):   Add pattern → [+0.0, +1.0, +0.0, ...]
Position 2 (does):  Add pattern → [+0.8, +0.5, +0.8, ...]
Position 3 (an):    Add pattern → [+0.9, -0.4, +0.9, ...]
Position 4 (LLM):   Add pattern → [+0.1, -0.9, +0.1, ...]
Position 5 (work):  Add pattern → [-0.5, -0.7, -0.5, ...]
```

Each position gets a unique mathematical "signature" added to its meaning.

**Better analogy:** Like timestamps on a video - the content matters, but so does the sequence.

---

## 🧠 Stage 4: The Core Intelligence (Transformer Layers)

This is where the magic happens. The model has 20-100+ layers, each doing three main operations:

### 4A: Self-Attention (The "Ah-ha!" Moment)

**What it does:** Figures out which words should "pay attention" to which other words

**In your question "How does an LLM work?":**
```
When processing "work":

Step 1: Look at all other words
  "How"  → 15% relevant
  "does" → 5% relevant  
  "an"   → 2% relevant
  "LLM"  → 45% relevant  ← Most important!
  "work" → 33% relevant

Step 2: Combine their meanings based on relevance
  Result = (45% of "LLM" meaning) + (33% of "work" meaning) + (15% of "How") + ...
  
Step 3: Now "work" understands it's about "LLM" and it's a "How" question
```

**Real Example:**
```
Sentence: "The trophy doesn't fit in the suitcase because it is too large"

When processing "it":
- Looks at "trophy" → 80% relevant
- Looks at "suitcase" → 15% relevant
- Looks at "large" → 5% relevant

Conclusion: "it" = "trophy" (not suitcase)
```

**The Three Keys (Query, Key, Value):**
Think of it like a library search:
- **Query:** "What am I looking for?" (your search terms)
- **Key:** "What do I offer?" (book titles in the library)
- **Value:** "What information do I have?" (actual book contents)

**Better analogy:** Like a room full of experts where each person asks questions ("Who knows about engines?") and listens for relevant responses from others who have that expertise.

---

### 4B: Multi-Head Attention (Multiple Perspectives)

**What it does:** Runs 8-96 attention mechanisms simultaneously, each learning different patterns

**Think of it as a team of specialists:**
```
Your question: "How does an LLM work?"

Head 1 (Grammar Expert):
  "does" ↔ "work" → Verb agreement ✓

Head 2 (Subject Finder):
  "LLM" ↔ "work" → LLM is the subject doing the working

Head 3 (Question Structure):
  "How" ↔ "work" → This needs an explanation answer

Head 4 (Topic Detection):
  "LLM" = main topic

Head 5 (Technical Terms):
  "LLM" and "work" → Technical domain

Head 6-12 (Other patterns):
  Various other linguistic patterns
```

**Real Example:**
```
Sentence: "The cat sat on the mat because it was comfortable"

Head 1: Grammar → "cat" (subject) + "sat" (verb)
Head 2: Location → "sat" + "on" + "mat"  
Head 3: Causation → "because" + "comfortable"
Head 4: Pronoun → "it" refers to "mat"
```

**Better analogy:** Like a panel reviewing your essay - one checks grammar, one checks logic, one checks flow, one checks facts - all at the same time.

---

### 4C: Feed-Forward Network (The Refinement)

**What it does:** After attention mixes information, this layer processes and refines each word's understanding

**The Process:**
```
Input:  Word representation (768 numbers)
  ↓
Expand: Make it bigger (3,072 numbers) → More room to think
  ↓
Activate: Apply non-linear transformation → Complex reasoning
  ↓
Compress: Back to original size (768 numbers) → Final understanding
```

**Why it matters:**
- **Attention** connects words: "Paris" + "capital" → they're related
- **Feed-Forward** stores knowledge: "Paris" + "capital" → "France"

Most of the model's knowledge lives here (65% of all parameters).

**Better analogy:** 
- Attention is like **connecting the dots** in a puzzle
- Feed-forward is like **filling in the colors** and details

---

## 🎯 Stage 5: Predicting the Next Word

### Output Probabilities

After ~30-100 transformer layers, the model produces probability scores for every possible next word:
```
Your question: "How does an LLM work?"

Model's predictions for next token:
"A"            → 31%  ← Picked this!
"Large"        → 42%
"Language"     → 12%
"An"           → 8%
"They"         → 3%
"Well"         → 2%
... 49,994 other options with tiny probabilities
```

**Critical Point:** The model only predicts ONE word at a time!

**Autoregressive Generation Loop:**
```
Input:  "How does an LLM work?"
Step 1: Predict → "A" (31%)

Input:  "How does an LLM work? A"
Step 2: Predict → "large" (58%)

Input:  "How does an LLM work? A large"
Step 3: Predict → "language" (72%)

Input:  "How does an LLM work? A large language"
Step 4: Predict → "model" (85%)

... continues until complete sentence
```

**Why not plan ahead?**
The model doesn't "know" it will say "A large language model..." at the start. Each word influences the next, like writing a story one word at a time.

---

## 🎲 Stage 6: Choosing Tokens (Decoding Strategies)

The model doesn't always pick the highest probability. You can control creativity:

### **Greedy Decoding** (Always pick #1)
```
Always choose the most likely word

Example output:
"The cat sat on the mat."
(Safe, predictable, can be boring)
```

---

### **Temperature** (Control randomness)

**Temperature = 0.1** (Cold - Very Focused):
```
Original:  "Large"→42%, "A"→31%, "Language"→12%
After:     "Large"→95%, "A"→4%,  "Language"→1%

Output: Very predictable, factual
"A large language model is a neural network..."
```

**Temperature = 1.0** (Neutral - Balanced):
```
Original probabilities unchanged
"Large"→42%, "A"→31%, "Language"→12%

Output: Natural, conversational
```

**Temperature = 1.5** (Hot - Very Creative):
```
Original:  "Large"→42%, "A"→31%, "Language"→12%
After:     "Large"→30%, "A"→28%, "Language"→25%, "Interesting"→10%

Output: Creative, might be less accurate
"An interesting computational framework that processes..."
```

**When to use:**
- **Low (0.1-0.5):** Code generation, factual answers, math
- **Medium (0.7-1.0):** General conversation, explanations
- **High (1.2-2.0):** Creative writing, brainstorming, poetry

---

### **Top-p (Nucleus Sampling)** (Dynamic selection)

**How it works:** Pick from smallest set of words that cover p% probability

**Example with p=0.9 (90%):**
```
Token         Probability   Cumulative
"Large"       42%          42%
"A"           31%          73%
"Language"    12%          85%
"An"          8%           93%  ← Stop here! We've hit 90%+

Now randomly pick from just these 4 words
(ignore the other 49,996 low-probability options)
```

**Why it's smart:**
```
High confidence situation:
  "The capital of France is ___"
  "Paris": 95% → Only need 1 word to hit 90%
  Result: Focused, accurate

Low confidence situation:
  "The weather might be ___"
  Top 50 words needed to reach 90%
  Result: More variety, more creative
```

**Better analogy:** 
- Temperature = adjusting how adventurous your restaurant choice is
- Top-p = saying "give me options that cover 90% of what people usually order"

---

## 🔄 The Complete Loop (Enhanced)
```
┌────────────────────────────────────────────────────────────┐
│ YOUR INPUT: "How does an LLM work?"                        │
└──────────────────────────┬─────────────────────────────────┘
                           ↓
              ┌────────────────────────┐
              │ 1. TOKENIZATION        │ 
              │ Break into pieces      │
              │ ["How", " does", ...]  │
              └──────────┬─────────────┘
                         ↓
              ┌────────────────────────┐
              │ 2. EMBEDDINGS          │
              │ Words → Numbers        │
              │ "How" → [0.2, -0.5...] │
              └──────────┬─────────────┘
                         ↓
              ┌────────────────────────┐
              │ 3. POSITIONAL ENCODING │
              │ Add word order info    │
              │ Position 1, 2, 3...    │
              └──────────┬─────────────┘
                         ↓
   ┌──────────────────────────────────────────────┐
   │ 4. TRANSFORMER LAYERS (×30-100)              │
   │                                               │
   │    Each layer does this:                     │
   │                                               │
   │    ┌─────────────────────────────────────┐   │
   │    │ Self-Attention                      │   │
   │    │ • Figure out word relationships     │   │
   │    │ • "LLM" relates to "work"          │   │
   │    │ • "How" indicates question         │   │
   │    └──────────────┬──────────────────────┘   │
   │                   ↓                           │
   │    ┌─────────────────────────────────────┐   │
   │    │ Multi-Head Attention (×12 heads)   │   │
   │    │ • Grammar checking                  │   │
   │    │ • Meaning extraction                │   │
   │    │ • Context building                  │   │
   │    └──────────────┬──────────────────────┘   │
   │                   ↓                           │
   │    ┌─────────────────────────────────────┐   │
   │    │ Feed-Forward Network               │   │
   │    │ • Store & apply knowledge          │   │
   │    │ • Refine understanding             │   │
   │    └──────────────┬──────────────────────┘   │
   │                   ↓                           │
   │         (Repeat 30-100 times)                │
   │      Each layer builds deeper understanding  │
   └────────────────┬─────────────────────────────┘
                    ↓
         ┌────────────────────────┐
         │ 5. OUTPUT LAYER        │
         │ Convert to predictions │
         │ for all 50K+ words     │
         └──────────┬─────────────┘
                    ↓
         ┌────────────────────────┐
         │ 6. PROBABILITIES       │
         │ "A" → 31%              │
         │ "Large" → 42%          │
         │ "Language" → 12%       │
         │ ...                    │
         └──────────┬─────────────┘
                    ↓
         ┌────────────────────────┐
         │ 7. SAMPLING            │
         │ Pick one token using:  │
         │ • Temperature          │
         │ • Top-p                │
         │ • Top-k                │
         └──────────┬─────────────┘
                    ↓
              ┌──────────┐
              │ Output:  │
              │   "A"    │
              └────┬─────┘
                   ↓
         ┌────────────────────────┐
         │ 8. LOOP BACK           │
         │                        │
         │ Add "A" to input:      │
         │ "How does an LLM       │
         │  work? A"              │
         │                        │
         │ Go back to step 2 →    │
         │ Repeat steps 2-7       │
         │                        │
         │ Keep going until:      │
         │ • Complete sentence    │
         │ • Max length reached   │
         │ • Stop signal          │
         └────────────────────────┘
                   ↓
    ╔════════════════════════════════╗
    ║ FINAL OUTPUT:                  ║
    ║                                ║
    ║ "A large language model        ║
    ║ processes text by converting   ║
    ║ words to numbers, using        ║
    ║ attention mechanisms to find   ║
    ║ relationships between words,   ║
    ║ and generating responses one   ║
    ║ token at a time based on       ║
    ║ patterns learned from          ║
    ║ training data."                ║
    ╚════════════════════════════════╝
```

---

## 💡 Key Insights

### 🔸 No Real Understanding
The model doesn't "know" anything - it predicts patterns based on statistical correlations from training data.

**Example:**
```
Model sees in training: "Paris is the capital of France" (1000+ times)
Your question: "What is the capital of France?"
Model predicts: "Paris" (because this pattern was common)

It's not "knowing" - it's pattern recognition!
```

---

### 🔸 Context Window Matters
The model can only "see" recent tokens:
```
Small context (2K tokens):  ~1,500 words  ~5 pages
Medium context (8K tokens): ~6,000 words  ~20 pages  
Large context (128K tokens): ~96,000 words ~320 pages
Claude (200K tokens):      ~150,000 words ~500 pages
```

**What this means:**
- Can't remember your conversation from yesterday
- Has a "working memory" limit
- Longer context = better understanding, but slower/costlier

---

### 🔸 One Token at a Time
Even long responses are generated sequentially. The model doesn't plan ahead!

**Example:**
```
You ask: "Write a story with a twist ending"

Model generates:
"Once upon a time" → doesn't know the ending yet
"there was a detective" → still doesn't know
"who investigated a murder" → making it up as it goes
...
(400 tokens later)
"...and realized the murderer was himself all along"
↑ This twist wasn't planned from the start!
```

---

### 🔸 Training vs. Inference

**Training (Learning):**
```
Time:  Weeks to months
Cost:  $1,000,000 - $100,000,000
Data:  Billions of words from internet
Goal:  Learn language patterns
```

**Inference (Answering You):**
```
Time:  Seconds
Cost:  $0.001 - $0.10 per query  
Data:  Just your question
Goal:  Apply learned patterns
```

You're using inference right now - no new learning happens during our conversation!

---

### 🔸 Why It Works
Not because the model "understands," but because:
1. Language has predictable patterns
2. Transformers are excellent at capturing these patterns
3. Massive training data covers most situations
4. Scale (bigger models) = better pattern recognition

---

## ❓ Quick FAQ

**Q: Does it think?**
A: No. It recognizes patterns and predicts statistically likely continuations. No consciousness or true reasoning.

**Q: How does it know facts?**
A: Facts are encoded as pattern weights learned from training data. It's compressed pattern-matching, not looking up a database.

**Q: Why does it make mistakes?**
A: It optimizes for "likely" not "correct." If training data had biases or errors, the model learns those too. It also can't fact-check itself.

**Q: Can it plan ahead?**
A: Not really. It predicts one token at a time without true planning. Any apparent planning emerges from patterns it saw in training data.

**Q: Why do we use temperature and top-p?**
A: To balance accuracy with creativity. Low settings = safe/predictable, high settings = creative/risky.

---

## 🎓 The Real Magic

The transformer architecture's genius is **self-attention** - the ability to dynamically determine what's relevant:

**Old Models (RNNs):** 
Process left-to-right → "The" → "cat" → "sat"
By the time they reach "mat," they've forgotten "The cat"

**Transformers:** 
Look at everything at once → Figure out what matters → Decide
When processing "mat," can still remember and focus on "cat"

This enables:
- **Long-range dependencies:** Connect words that are far apart
- **Parallel processing:** Much faster training
- **Transfer learning:** Learn once, use for many tasks
- **Scaling:** Bigger = better (unlike older architectures)

---

## 🎨 Visual Summary: Token Journey
```
"How does an LLM work?"
         ↓
    [Tokenize]
         ↓
    ["How", " does", " an", " LLM", " work", "?"]
         ↓
    [Convert to numbers]
         ↓
    [[0.1, 0.3, ...], [0.2, -0.4, ...], ...]
         ↓
    [Add positions]
         ↓
    [[0.1+pos1, 0.3+pos1, ...], [0.2+pos2, -0.4+pos2, ...]]
         ↓
    [30+ Layers of Attention + Processing]
    Each layer asks: "What relates to what?"
         ↓
    [Final understanding vector]
         ↓
    [Predict next token probabilities]
         ↓
    Choose "A" (31%)
         ↓
    [Loop back with new input]
         ↓
    "How does an LLM work? A"
         ↓
    [Repeat entire process]
         ↓
    Choose "large" (58%)
         ↓
    Continue until complete answer...
```
