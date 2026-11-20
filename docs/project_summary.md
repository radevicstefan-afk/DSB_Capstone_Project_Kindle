# Project summary and presentation notes

## Questions

**Part 1 (popularity):**

> **Can we predict, from the characteristics of a book draft, whether a new Kindle book will reach at least 100 ratings in its first 12 months on Amazon?**

**Part 2 (sales, synthetic data):**

> **How many copies a year can a new Kindle book expect, from its niche, price, length, series, publishing route and the author's experience?**
>
> Sub-question: **How well does this work for less-known authors without hits, and which niches pay them most?**

## Part 1: popularity (notebooks 01–02)

**Short answer:** yes, to a moderate degree. Gradient boosting reaches ROC AUC **0.809** and accuracy **75.7%** on test books from authors it has not seen.
The baseline has 0.500 and 67.6%.

### What I found

1. **The data has no copies sold.** Neither the original data nor the public data sets I checked have reliable Kindle sales for this period, so I measure
   popularity with the number of ratings.
2. **A price of 0 is usually not a free book.** Among the checked books with price 0, 97.5% are in Kindle Unlimited.
3. **Popularity is very uneven.** The top 10% of books hold 73.8% of first-year ratings.
4. **Stars do not separate popular books.** The average is about 4.4–4.5 in every group.
5. **The threshold of 100 is justified.** Of six tested thresholds, 100 ratings in the first year is the only one that passes all five rules: 32.4% of the
   books are popular, and they hold 94.4% of first-year ratings.
6. **Popular books have recognisable traits.** They come more often from experienced authors, are part of a series, are longer, and belong to niches such
   as Romance.

| Group | Share of popular books |
|---|---:|
| Author's first book | 13% |
| Author with 10+ earlier books | 57% |
| In a series / not in a series | 45% / 25% |
| Publisher / self-published | 45% / 25% |
| Under 100 pages | 16% |
| 300–399 pages | 46% |
| Romance | 47.7% |
| Education & Teaching | 9.3% |

### Model results

| Model | Accuracy | Precision | Recall | F1 | ROC AUC |
|---|---:|---:|---:|---:|---:|
| Baseline | 0.676 | 0 | 0 | 0 | 0.500 |
| Logistic regression | 0.743 | 0.654 | 0.439 | 0.525 | 0.786 |
| Random forest | 0.757 | 0.669 | 0.493 | 0.568 | 0.807 |
| **Gradient boosting** | **0.757** | **0.662** | **0.511** | **0.577** | **0.809** |

- The strongest signal is the **author's earlier books**, then the **number of pages**.
- With only the draft features (niche, pages, title words, series) the ROC AUC is **0.720**. With the publishing route and the author's earlier books it is **0.809**.
- With a threshold of 0.3 the model finds **79.0%** of popular books, and it is right in **53.7%** of the cases where it calls a book popular.
- Main weakness: at a threshold of 0.5 it finds only **3.8%** of popular first books.

### Price and possible income (context, not the target)

All amounts are **scenarios**, because real sales are not known.

- A self-published author keeps about **64–66%** of the price at 2.99–9.99 dollars. With an assumed standard publisher contract the author gets **17.5%**.
- With 50 buyers per rating, a typical self-published book earns about **920 dollars a year**; with 20 or 100 buyers per rating, 368 or 1,840 dollars.
- Income falls quickly: year 2 brings **26.7%** and year 3 **14.1%** of the first year's income.

## Part 2: what is possible with sales data (notebooks 03–06)

The original data has no sales, so part 1 studies popularity. Part 2 shows how the project continues when copies sold are available. The sales are
**synthetic** and serve as an example.

**03 · How the synthetic sales were made.** I compared four levels of sales. Verified purchases only give 0.10 million copies in 2022, ratings × share of
verified buyers 4.0 million, **5 buyers per rating 43.6 million**, and 50 buyers per rating 435.8 million. The level with 5 buyers per rating agrees with a
rough market check (42.9 million) and is used as the copies sold.

**04 · EDA with sales data** (55,690 books, 2026 Kindle rules):

| Route | Author's share at 4.99 dollars | Median copies a year | Median income a year |
|---|---:|---:|---:|
| Self-published | 65% (3.23 dollars) | 39 | 94 dollars |
| Small publisher | 23% (1.13 dollars) | 120 | 146 dollars |
| Large publisher | 17.5% (0.87 dollars) | 306 | 473 dollars |

- The top 1% of books hold 47.0% of copies and 45.3% of author income.
- For the same book, a small publisher must sell **2.9 times** and a large one **3.5–3.9 times** more copies for the author to earn the same.
- The best price group for every route is **10–12.99 dollars**.
- **Without hits the niches change:** for less-known authors Cookbooks, Health, Arts and Humor lead, while Romance falls to 16th and Mystery to 17th of 19.

**05 · BERT** on 20,000 reviews: within one star of the reader in 95.2% of the cases. The tone of the text does not separate popular books and is not
related to sales.

**06 · Sales models.** Regression on `log(1 + copies)`, with the same author split as in part 1 (44,675 / 11,015 books).

| Model | Validation R² | Test R² | Within 2× of real sales |
|---|---:|---:|---:|
| Baseline (median, 79 copies) | – | −0.002 | 25.4% |
| Linear regression (Ridge) | 0.216 | 0.224 | 29.6% |
| Random forest | 0.270 | 0.284 | 31.8% |
| **Gradient boosting (chosen)** | **0.284** | **0.295** | 31.4% |

- The model predicts the typical sales of groups well (niche medians: Spearman 0.90), but not hits. The draft alone explains 17.1%.
- A self-published first Romance book at 4.99 dollars: about 38 copies and **122 dollars a year**.
- **Sub-question, less-known authors without hits:** the model works as well (R² 0.22); Cookbooks, Health, Sports, Arts and Crafts lead, while Romance and
  Mystery are lower.
- Sales rising with price is an association in this data (more expensive books are more often publisher books), not proof that a higher price sells more.

## Main conclusions

1. The popularity of a new Kindle book can be partly predicted.
2. The author's experience carries the most information, but the draft alone also gives a useful signal.
3. The models are useful as an estimate of chances and typical sales, not as a guarantee of success.
4. For new authors the results are weaker, because the data has no marketing, writing quality or covers.
5. Ratings measure reader response, not sales; the sales in part 2 are synthetic.

## Presentation flow

1. **Problem:** an author wants to know which books find readers, and sales are not public.
2. **Data:** from 1.6 million books to 135,839 books for the model.
3. **Definition of success:** why 100 ratings in the first 12 months.
4. **EDA:** unevenness and the traits of popular books.
5. **Models:** baseline, three models and the split by author.
6. **Result:** gradient boosting and what ROC AUC 0.809 means.
7. **Errors:** first books, and the difference between the draft and the author.
8. **Part 2:** synthetic sales, income by route and price, sales models and less-known authors.
9. **Limitations and next steps.**

## Possible questions

**Why exactly 100 ratings?** I tested six thresholds against five rules written in advance, and only 100 passes all of them. The limits of the rules are my decision.

**Why not copies sold in part 1?** There is no reliable figure for these books, and estimates from other data sets would add a new assumption. Part 2 shows
what would be possible with sales data.

**Why split by author?** So that the model does not learn from other books by the same author, and is tested on authors it has not seen.

**Is this really a "draft"?** Only four features describe the draft, and they give ROC AUC 0.720. The full model also uses the publishing route and the
author's history and gives 0.809.

**Why is review sentiment not a model feature?** Reviews appear after publication, so they do not exist when there is only a draft. Notebook 05 analyses
them separately with BERT.

**Is the synthetic sales data feature engineering?** No. It creates a new **target** (copies sold), not new inputs. Because it is made from ratings, the
number of ratings is not used as a model input, to avoid data leakage.
