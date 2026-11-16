# IS834 — Quiz 2 Solution

**Fall 2026 · Quiz given Thursday, October 8 · 13 points**

## Point breakdown

| Question | Item | Points | Answer |
| --- | --- | --- | --- |
| 1 | (a) | 2 | `no tip` |
| 1 | (b) | 4 | `low`, then `generous` |
| 2 | | 2 | **D** — None of the above (the answer is 0) |
| 3 | (a) | 2 | `'payment'` |
| 3 | (b) | 3 | **5** columns; **TRUE** |
| | **Total** | **13** | |

---

## The dataset

```python
taxis = pd.read_csv(DATA + "taxis.csv")
taxis = taxis[['passengers', 'fare', 'tip', 'payment']]
print(taxis.shape)
taxis.head()
```

```
(6433, 4)
```

| | passengers | fare | tip | payment |
|---|---|---|---|---|
| **0** | 1 | 7.0 | 2.15 | credit card |
| **1** | 1 | 5.0 | 0.00 | cash |
| **2** | 1 | 7.5 | 2.36 | credit card |
| **3** | 1 | 27.0 | 6.15 | credit card |
| **4** | 3 | 9.0 | 1.10 | credit card |

---

## Question 1 (6 points)

```python
def tip_rating(row, cutoff=0.25):
    if row['tip'] == 0:
        return "no tip"
    elif row['tip'] / row['fare'] < cutoff:
        return "low"
    else:
        return "generous"

taxis['rating'] = taxis.apply(tip_rating, axis=1)
taxis[['fare', 'tip', 'rating']].head(4)
```

```
   fare   tip    rating
0   7.0  2.15  generous
1   5.0  0.00         ❶
2   7.5  2.36  generous
3  27.0  6.15       low
```

**(a) What is printed at ❶? — `no tip`** (2 points)

The tip is `0.00`, so the first test, `row['tip'] == 0`, is `True` and the function returns
`"no tip"` right away. Python stops at the first `True` branch, so the `elif` is never checked.

**(b) What does each `print` line print?** (4 points)

```python
ride = {'fare': 10.0, 'tip': 2.0}

print(tip_rating(ride))
print(tip_rating(ride, 0.1))
```

- `print(tip_rating(ride))` prints **`low`**. **(2 points)** No second value is passed, so
  `cutoff` takes its default, `0.25`. The tip is 2 on a fare of 10, which is 0.2, and 0.2 is below
  0.25.
- `print(tip_rating(ride, 0.1))` prints **`generous`**. **(2 points)** Now `cutoff` is `0.1`.
  0.2 is not below 0.1, so the `elif` fails and the `else` runs.

Each line is graded on its own. Writing `error` for the first line is wrong: a parameter with a
default value does not have to be passed.

---

## Question 2 (2 points)

```python
print(taxis.passengers.value_counts())
```

```
passengers
1    4678
2     876
5     277
3     243
6     153
4     110
0      96
Name: count, dtype: int64
```

**How many trips had 7 passengers? — D. None of the above** (2 points)

`.value_counts()` lists **every** distinct value in the column, with how many times it occurs.
`7` is not in the list, so no trip had 7 passengers: the answer is 0, which is not among A–C.

The list is sorted by count, from most to least common, not by number of passengers. So the last
line (`0  96`) is not "the next value", and `6  153` is not "6 or more".

---

## Question 3 (5 points)

```python
taxis.head(1)
```

| | passengers | fare | tip | payment | rating |
|---|---|---|---|---|---|
| **0** | 1 | 7.0 | 2.15 | credit card | generous |

```python
# keep only the rides that were paid in cash
cash_rides = taxis[taxis[❶] == 'cash']
```

**(a) What belongs at ❶? — `'payment'`** (2 points)

`'cash'` is a value in the `payment` column (see index 1 of the dataset table), so the filter
compares that column to `'cash'`. `'payment'` or `"payment"` both earn full credit; `payment`
without quotation marks is also accepted. `'cash'` is wrong: that is the value, and it is already
on the line. `'Payment'` is wrong: column names are case sensitive.

**(b) Columns and rows of `cash_rides`** (3 points)

- **5** columns. **(1 point)** A filter keeps or drops whole **rows**; every column comes along.
  `taxis.head(1)` shows 5 columns: the 4 loaded at the top plus the `rating` column Question 1
  added.
- **TRUE**. **(2 points)** Index 1 of the dataset table is a cash ride, so at least one row is
  kept: more than 0. Indexes 0, 2, 3 and 4 were paid by credit card, so at least those are
  dropped: fewer than 6,433.
