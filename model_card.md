# Model Card: Mood Machine

This model card is for the Mood Machine project, which includes **two** versions of a mood classifier:

1. A **rule based model** implemented in `mood_analyzer.py`
2. A **machine learning model** implemented in `ml_experiments.py` using scikit learn

You may complete this model card for whichever version you used, or compare both if you explored them.

## 1. Model Overview

**Model type:**
I used both — the rule based model and the ML model — and compared them.

**Intended purpose:**
Classify short social media style text messages as positive, negative, neutral, or mixed.

**How it works (brief):**
The rule based model scores text by counting how many positive and negative words appear. It also handles negation, so “not happy” flips the score. The ML model trains on the same posts and labels using scikit-learn and learns patterns automatically without hand coded rules.

## 2. Data

**Dataset description:**
The dataset has 11 posts total. It started with 6 and I added 5 more to cover more variety, including slang, mixed feelings, and edge cases.

**Labeling process:**
I labeled each post manually based on how I read the tone. Some were easy like “Today was a terrible day” being negative. The harder ones were posts with mixed signals, like being stressed but also hopeful, those I labeled mixed. One post (“I am so Dunkin smoothering”) was unclear enough that I went with neutral since there was no real sentiment I could identify.

**Important characteristics of your dataset:**

- Contains emojis and emoticons like :) :( 😊 😢
- Some posts have mixed feelings
- Contains slang and informal language
- A few posts are ambiguous or hard to label confidently

**Possible issues with the dataset:**
The dataset is very small (11 posts), so the model is basically memorizing examples rather than learning general patterns. There is also no sarcasm in the current posts, which is a big gap since sarcasm is common in real social media text.

## 3. How the Rule Based Model Works (if used)

**Your scoring rules:**

- Each positive word adds 1 to the score, each negative word subtracts 1
- Negation is handled — “not happy” flips to negative, “not bad” flips to positive
- Emojis and emoticons are included in the word lists, so 😊 and :) count as positive signals
- If a token has an emoji glued to it (like “great😊”), the last character is checked separately
- score > 0 returns positive, score < 0 returns negative, score == 0 returns neutral

**Strengths of this approach:**
It is transparent and easy to debug. You can always explain exactly why it gave a certain label. It also works on short simple posts pretty reliably when the words are in the list.

**Weaknesses of this approach:**
It completely fails on sarcasm. It also misses slang it has never seen. If a word is not in the list it gets ignored, so “cooked” (slang for failing) scores neutral instead of negative. Punctuation attached to words also causes misses if preprocessing doesn't catch it.

## 4. How the ML Model Works (if used)

**Features used:**
Bag of words using CountVectorizer — each word becomes a feature and the model learns which words correlate with each label.

**Training data:**
The model trained on `SAMPLE_POSTS` and `TRUE_LABELS` from `dataset.py`.

**Training behavior:**
Accuracy was 1.00, but that is because the model is tested on the same data it trained on. It is basically memorizing the examples, not actually learning to generalize.

**Strengths and weaknesses:**
Strength is that it learns automatically without me having to manually pick every word. Weakness is that with only 11 examples it overfits immediately and would probably fail on new posts it has never seen.

## 5. Evaluation

**How you evaluated the model:**
Both models were evaluated on the 11 labeled posts in `dataset.py`. The rule based model got 1.00 accuracy, and so did the ML model.

**Examples of correct predictions:**

- “I love this class so much” → positive (matched “love”)
- “Today was a terrible day” → negative (matched “terrible”)
- “So excited for the weekend” → positive (matched “excited”)

**Examples of incorrect predictions:**
The model struggles with posts where the key word is not in the list. For example “so cooked” — the model does not know “cooked” is slang for doing badly, so it only catches “awful” and misses the full negative sentiment. Sarcasm would also completely break it.

## 6. Limitations

- Dataset is very small — 11 posts is not enough to generalize
- The model cannot detect sarcasm at all
- Rule based model only knows words explicitly added to the list
- Both models are tested on training data, so 1.00 accuracy is misleading
- Preprocessing still has gaps — punctuation attached to words can cause mismatches

## 7. Ethical Considerations

- If this were used in a real app, misclassifying a message expressing distress as neutral could be harmful — someone asking for help might get ignored
- Slang and emojis used by certain communities might not be in the word list, meaning the model performs worse for those groups
- Analyzing personal messages for mood without someone knowing raises privacy concerns

## 8. Ideas for Improvement

- Add a real separate test set so accuracy numbers are actually meaningful
- Add more labeled data, especially sarcasm and slang examples
- Improve preprocessing to fully handle punctuation and emojis attached to words
- Use regex in preprocess to split emojis from words before scoring
- Add weighted words so stronger words like “hate” count more than “upset”
- Try TF-IDF instead of CountVectorizer for the ML model
