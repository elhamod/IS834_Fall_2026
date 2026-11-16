# IS834 — Quiz 1 Solution

**Fall 2026 · Quiz given Thursday, September 24 · 24 points**

## Point breakdown

| Question | Item | Points | Answer |
| --- | --- | --- | --- |
| 1 | (a) | 2 | `10` |
| 1 | (b) | 2 | `6` |
| 1 | (c) | 2 | **A** — `int` |
| 1 | (d) | 3 | **D** — `n + 1` |
| 1 | (e) | 4 | Replace `len(numbers)` in the `print` with `count`; `len()` then runs once |
| 2 | (a) | 2 | **B** — `Bob` |
| 2 | (b) | 2 | `not admitted` |
| 2 | (c) | 3 | **C** — `["Austin", "TX"]` |
| 2 | (d) | 4 | No — see below |
| | **Total** | **24** | |

---

## Question 1 (13 points)

```python
numbers = list(range( ❶ ))
count = len(numbers)

total = 0
for n in numbers:
    total = total + n
    print("n:", n, "| count:", len(numbers), "| running total:", total)

average = total / count
print("average:", average)
```

```
n: 0 | count: 6 | running total: 0
n: 1 | count: 6 | running total: 1
n: 2 | count: 6 | running total: 3
n: 3 | count: 6 | running total: 6
n: 4 | count: 6 | running total: ❷
n: 5 | count: 6 | running total: 15
average: 2.5
```

**(a) What value belongs at ❷? — `10`** (2 points)

The previous running total is 6, and this pass adds `n`, which is 4: 6 + 4 = 10.

**(b) What number was written at ❶? — `6`** (2 points)

`range(6)` produces `0, 1, 2, 3, 4, 5`: six values, the last one 5. The stop value is not
included, so the answer is `6`, not `5`.

**(c) What is the type of `total`? — A. `int`** (2 points)

`total` starts at `0` and only ever has integers added to it, so it stays an integer (the output
shows `15`, not `15.0`). The `2.5` belongs to `average`, which is a float because it comes from
division with `/`.

**(d) How many times does `len()` run, if `numbers` holds `n` values? — D. `n + 1`** (3 points)

Once at `count = len(numbers)`, and once more on every pass of the loop, because the `print` calls
`len(numbers)` again. That is `n` passes plus 1.

**(e) What one change makes `len()` run as few times as possible?** (4 points)

Replace `len(numbers)` inside the `print` with `count`, which already holds that number.
**(3 points)** `len()` then runs **once**, on the line before the loop. **(1 point)**

Writing the number `6` into the `print` instead earns 1 point at most: it stops being correct as
soon as `numbers` holds a different list, which the question rules out.

---

## Question 2 (11 points)

```python
people = [
    {"name": "Alice", "location": "Boston,MA",    "income": 62000},
    {"name": "Bob",   "location": "Austin,TX",    "income": 74000},
    {"name": "Carla", "location": "Worcester,MA", "income": 48000},
]

for person in people:
    location = person["location"].split(",")
    state = location[1]

    if state == "MA":
        print(person["name"].upper())
        if person["income"] > 50000:
            print("admitted")
        else:
            print("not admitted")
    else:
        print(person["name"])
        if person["income"] > 80000:
            print("admitted")
        else:
            print("not admitted")
    print(" ")
```

**(a) What is printed at ❶? — B. `Bob`** (2 points)

Bob's state is `TX`, so the `else` branch runs. That branch prints `person["name"]` with no method
called on it, so the name comes out exactly as stored.

**(b) What is printed at ❷? — `not admitted`** (2 points)

Bob is outside Massachusetts, so his threshold is 80000. His 74000 does not clear it.

**(c) On Bob's pass, what does `location` hold? — C. `["Austin", "TX"]`** (3 points)

`.split(",")` returns a **list** of the pieces. `"TX"` is `state`, on the next line, not
`location`.

**(d) Will `admitted` be printed for Dev (`"Salem,ma"`, income 62000)?** (4 points)

- **No** — `not admitted` is printed. **(1 point)**
- `"Salem,ma".split(",")` gives `['Salem', 'ma']`, so `state` is `"ma"`. Python is case sensitive,
  so `"ma" == "MA"` is `False` and the `else` branch runs. **(2 points)**
- That branch holds Dev to the 80000 threshold instead of 50000, so his 62000 fails, even though he
  lives in Massachusetts and would clear the Massachusetts threshold. **(1 point)**

Answering "No" with a different reason, such as "62000 is too low", without saying which threshold
applies or why, earns the first point only.
