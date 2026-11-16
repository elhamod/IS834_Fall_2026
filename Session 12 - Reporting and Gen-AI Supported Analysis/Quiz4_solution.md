# IS834 — Quiz 4 Solution

**Fall 2026 · Quiz given Thursday, November 12 · 19 points**

---

### The dataset

Every question uses the DataFrame `oj`: one row per purchase of orange juice at a grocery chain, 1,070 purchases in all. Each shopper bought one of two brands, **Citrus Hill (CH)** or **Minute Maid (MM)**.

| Column | Meaning |
| --- | --- |
| `Purchase` | The brand bought: `'CH'` or `'MM'` |
| `PriceCH` | Price of Citrus Hill that day, in dollars |
| `PriceMM` | Price of Minute Maid that day, in dollars |
| `LoyalCH` | How loyal the shopper is to Citrus Hill, from 0 (never buys it) to 1 (always buys it), based on their earlier purchases |
| `StoreID` | Which store: 1, 2, 3, 4 or 7 |

Three of its rows:

|  | Purchase | PriceCH | PriceMM | LoyalCH | StoreID |
| --- | --- | --- | --- | --- | --- |
| **966** | MM | 1.75 | 1.99 | 0.55 | 1 |
| **269** | MM | 1.86 | 2.18 | 0.53 | 2 |
| **461** | CH | 1.79 | 1.79 | 0.48 | 4 |

---

### Question 1 (2 points)

**Code:**

```python
oj['bought_mm'] = oj['Purchase'] == 'MM'
oj['bought_mm'].value_counts(normalize=True)
```

**The following is printed:**

```
bought_mm
False    0.61028
True     0.38972
Name: proportion, dtype: float64
```

A model predicts **CH** for every purchase in `oj`. What is its accuracy?

> **Answer:** 0.61 (61%)
>
> **Why:** A model that always says CH is right exactly on the purchases that really were CH, which is the `False` row: 61% of them. This is the **baseline**: the score any real model has to beat.
>
> - **0.39** is the share it gets *wrong* (the Minute Maid purchases).
> - **0** mixes up accuracy with "how many Minute Maid buyers it found". It finds none of them, but it is still right on every CH purchase.

---

### Question 2 (7 points)

**Code:**

```python
X = oj[['PriceCH', 'PriceMM', 'LoyalCH', 'StoreID']]
y = oj['bought_mm']
print(len(X))
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=595, stratify=y)
print(X_train.shape)
```

**The following is printed:**

```
1070
❶
```

**(a)** (2 points) Circle one. Predicting `bought_mm` is a: **CLASSIFICATION** / **REGRESSION** problem.

> **Answer:** CLASSIFICATION
>
> **Why:** `bought_mm` is a category (True or False), not a number to be estimated. True and False behave like 1 and 0 inside Python, but they still name two classes.

**(b)** (2 points) Circle one. What is printed at ❶?

- A. `(214, 4)`
- B. `(856, 4)`
- C. `(856, 5)`
- D. `(214, 5)`

> **Answer:** B, `(856, 4)`
>
> **Why:** `.shape` is (rows, columns). `test_size=0.2` holds back 20% of the 1,070 rows (214) for testing, so the training set keeps the other 856. `X` has the 4 columns listed in `X = oj[[...]]`, and the split keeps all of them.
>
> - **A** and **D** use 214, which is the size of the **test** set, not the training set.
> - **C** and **D** add a fifth column, but the target `y` goes into `y_train`, not into `X_train`.

**(c)** (3 points) What is the problem with giving the model `StoreID` as it is, and what would you change? *(Maximum: 2 sentences.)*

> **Answer:** `StoreID` is a label, not a quantity: store 7 is not "more" than store 1, but the model reads the numbers as amounts and can make rules like `StoreID <= 3.5`. Turn it into dummy variables, one True/False column per store, for example with `pd.get_dummies(..., columns=['StoreID'])`.
>
> **Why:** In class the problem column (`job`) was text, so it was easy to see it needed dummies. Here the store numbers already look numeric, which hides the same problem.

---

### Cheat sheet for Question 3

Minute Maid (MM) is the **positive** class: a purchase predicted as MM is a "positive" prediction.

```
                     predicted CH           predicted MM
actual CH      TN (true negative)     FP (false positive)
actual MM      FN (false negative)    TP (true positive)
```

| Metric | Formula | In plain English |
| --- | --- | --- |
| Accuracy | (TP + TN) / (TP + TN + FP + FN) | Of all the cases, the share the model got right |
| Precision | TP / (TP + FP) | Of the cases the model **predicted** as positive, the share that really were positive |
| Recall | TP / (TP + FN) | Of the cases that **really** were positive, the share the model predicted as positive |

---

### Question 3 (6 points)

**Code:**

```python
model = DecisionTreeClassifier(max_depth=3, min_samples_leaf=20, random_state=595)
model.fit(X_train, y_train)
y_pred = model.predict(X_test)

cm = confusion_matrix(y_test, y_pred)
print(pd.DataFrame(cm, index=["actual CH", "actual MM"],
                   columns=["predicted CH", "predicted MM"]))
```

**The following is printed:**

```
           predicted CH  predicted MM
actual CH           116            15
actual MM            32            51
```

**(a)** (2 points) Circle one. Of the shoppers who really bought Minute Maid, what fraction did the model catch?

- A. 51 / 66
- B. 51 / 83
- C. 116 / 131
- D. 167 / 214

> **Answer:** B, 51 / 83
>
> **Why:** "Of the shoppers who really bought Minute Maid" is the `actual MM` row: 32 + 51 = 83 shoppers. The model caught (predicted MM for) 51 of them. This is the **recall** for Minute Maid, TP / (TP + FN).
>
> - **A** (51 / 66) reads down the `predicted MM` column instead of across the row: that is precision.
> - **C** (116 / 131) is the same idea for Citrus Hill, not Minute Maid.
> - **D** (167 / 214) is the overall accuracy.

The analyst refits the same tree with `class_weight="balanced"` and gets:

```
           predicted CH  predicted MM
actual CH            92            39
actual MM            13            70
```

**(b)** (2 points) Circle one. The balanced model catches more than 70% of the shoppers who really bought Minute Maid. **TRUE** / **FALSE**

> **Answer:** TRUE
>
> **Why:** Read the `actual MM` row of the new table: 70 caught out of 70 + 13 = 83, and 70 / 83 is about 84%. A quick check without a calculator: 70 / 83 is more than 70 / 100.
>
> - Reading down the `predicted MM` column gives 70 / 109, about 64%: that is precision, not "how many it caught".
> - Using the first table gives 51 / 83, about 61%: that is the first model, not the balanced one.

**(c)** (2 points) Circle one. Compared with the first model, the balanced model makes fewer errors. **TRUE** / **FALSE**

> **Answer:** FALSE
>
> **Why:** An error is any purchase the model got wrong: the two cells off the diagonal (FP + FN). First model: 15 + 32 = **47** errors. Balanced model: 39 + 13 = **52** errors. Balancing cut the missed Minute Maid buyers (32 → 13) but raised the Citrus Hill buyers wrongly predicted as MM (15 → 39). It trades one kind of error for the other; it does not remove errors.

---

### Question 4 (4 points)

The chain's online store wants a model that predicts, when a shopper opens the orange juice page, whether they will buy Minute Maid, so it can show them a Minute Maid ad. The analyst adds one more column to `X`, `mm_coupon_used`: whether the shopper entered a Minute Maid coupon code at checkout on that purchase. The analyst then retrains the model with this column as a feature, alongside the other four. The new model's test-set accuracy is 0.96, up from 0.78.

Should the store use this new model? Explain why. *(Maximum: 2 sentences.)*

> **Answer:** No. The coupon is entered at checkout, after the shopper has already chosen the brand, so the store cannot know it when the shopper opens the page and the ad has to be shown. The column gives the answer away (this is called **target leakage**), so the 0.96 will not hold when the model is actually used.
>
> **Why:** A high test-set score is usually the honest number, but only if every feature would really be available at the moment the model is used. Always ask *when* each column becomes known. This is not overfitting: the high score is on the test set.
