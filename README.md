# Digital Publishing Opportunity Intelligence

### Can we predict whether a new Kindle book will become popular, and how many copies it can sell?

**Author:** Stefan Radević · Data Science Bootcamp · Capstone project

## Overview

Many people dream of publishing an e-book on Amazon Kindle and earning a small passive income. Before they start, they would like to know which books
actually find readers, and whether one book can really pay off. I wanted to see what data can tell a new author before the book is published.

The project has two parts:

1. **Popularity (notebooks 01–02).** Amazon does not publish copies sold, so I measure popularity with the number of ratings.
2. **Sales and income (notebooks 03–06).** I created **synthetic** sales data, to show how the project continues when copies
   sold are available.

The results are information for authors, not a promise of income.

**Research questions**

- **Part 1:** *Can we predict, from the characteristics of a book draft, whether a new Kindle book will reach at least 100 ratings in its first 12 months on Amazon?*
- **Part 2:** *How many copies a year can a new Kindle book expect, from its niche, price, length, series, publishing route and the author's experience?*
- **Part 2, sub-question:** *How well does this work for less-known authors without hits, and which niches pay them most?*

## Data

| Source | File | Use |
|---|---|---|
| [Amazon Reviews 2023](https://amazon-reviews-2023.github.io/), Kindle Store metadata (McAuley Lab; Hou et al., 2024) | `data/raw/meta_Kindle_Store.jsonl` | books, niches, prices, total ratings, publishers, pages |
| Amazon Reviews 2023, Kindle Store reviews and 0-core | `data/raw/Kindle_Store_0core.csv` and the reviews file | dates of reviews, verified buyers, review texts |
| [Kaggle: Amazon Kindle Books Dataset 2023](https://www.kaggle.com/datasets/asaniczka/amazon-kindle-books-dataset-2023-130k-books) | `data/raw/kindle_data-v2.csv` | checks only: Kindle Unlimited and Best Seller labels |
| Synthetic sales (part 2) | made in notebook 03 | copies sold per book and year, 2019–2022 |

The data files are large (about 25 GB) and are not stored in this repository.

| Step | Books |
|---|---:|
| Original catalog | 1,591,371 |
| Published from 2019 to 2022 | 438,830 |
| Random sample (seed 42) | 200,000 |
| After cleaning | 176,367 |
| Part 1 models: published by the end of 2021, with at least one dated review | **135,839** |
| Part 2 models: the same period, with a real list price | **55,690** |

## Approach

1. **EDA:** I cleaned a random sample of the catalog and looked at what popular books have in common.
2. **Target:** I defined a popular book as one with at least **100 estimated ratings in its first 12 months**, after testing six thresholds against five written rules.
3. **Split by author:** all books of one author stay on the same side, so the test shows **authors the model has never seen**.
4. **Models:** one model per notebook (logistic or linear regression, random forest, gradient boosting), each tuned with `GridSearchCV` on the training
   books only and compared with a simple baseline.
5. **Final test:** I chose the model by its validation score and looked at the test books only once.
6. **Part 2:** I made synthetic sales, explored them with the 2026 Kindle royalty rules, and repeated the modelling as a regression of copies per year.

## Key findings, notebook by notebook

### 01 · EDA (popularity)

- **Popularity is very uneven:** the top 10% of books hold 73.8% of first-year ratings.
- **Stars do not separate popular books:** the average is about 4.4–4.5 stars in every group. Popular books get **more** ratings, not better ones.
- **The author's experience shows the largest difference:** 13% of first books are popular, against 57% of books by authors with 10 or more earlier books.
- **Series** (45% against 25%), **publishers** (45% against 25% for self-published books) and **length** (16% under 100 pages, 46% at 300–399 pages) also matter.
- **Romance** (47.7%) and **Mystery, Thriller & Suspense** (41.7%) are the strongest niches; Nonfiction (5.2%) and Education & Teaching (9.3%) the weakest.
- A price of 0 usually does not mean a free book: 97.5% of the checked ones are in Kindle Unlimited.

### 02.1 · Logistic regression

- It clearly beats the baseline: **ROC AUC 0.786** against 0.500.
- The author's earlier books are the strongest sign, and self-published books have lower odds.
- It almost never predicts that a **first book** will be popular (recall 0.001).

### 02.2 · Random forest

- Better than logistic regression: **ROC AUC 0.807**, and it finds 49.3% of the popular books.
- Length matters more here, because trees can find curved effects.
- First books are still hard (recall 0.042).

### 02.3 · Gradient boosting

- The best validation result (**0.802**) and test **ROC AUC 0.809**.
- With **only the draft** (niche, pages, title words, series) the ROC AUC falls to **0.720**: the draft gives a useful but weaker signal than the author's history.

### 02.4 · Model comparison: answer to part 1

| Model | Validation ROC AUC | Test accuracy | Precision | Recall | Test ROC AUC |
|---|---:|---:|---:|---:|---:|
| Baseline | | 0.676 | 0.000 | 0.000 | 0.500 |
| Logistic regression | 0.7739 | 0.743 | 0.654 | 0.439 | 0.786 |
| Random forest | 0.7993 | 0.757 | 0.669 | 0.493 | 0.807 |
| **Gradient boosting** | **0.8018** | **0.757** | **0.662** | **0.511** | **0.809** |

**Answer: yes, to a moderate degree.** The model is clearly better than the baseline, and its probabilities are close to the real shares. It relies most on the
author's earlier books, then on length. Its main weakness is first books: at a threshold of 0.5 it finds only 3.8% of the popular ones.

### 03 · Synthetic sales

- The data has no copies sold, so I compared four ways to create them. **5 buyers per rating** gives 43.6 million copies in 2022, close to a rough market
  check (42.9 million), so I use it as the copies sold.
- Everything about how the sales were made is in this notebook; the later notebooks only use the result.

### 04 · EDA with sales data

| Route | Author's share at 4.99 dollars | Median copies a year | Median author income a year |
|---|---:|---:|---:|
| Self-published (KDP) | 65% (3.23 dollars) | 39 | 94 dollars |
| Small publisher (assumed 35% of net receipts) | 23% (1.13 dollars) | 120 | 146 dollars |
| Large publisher (25% of net receipts) | 17.5% (0.87 dollars) | 306 | 473 dollars |

- **A few hits decide the market:** the top 1% of books hold 47.0% of all copies and 45.3% of author income.
- **One book rarely gives a real passive income:** the median self-published author earns 94 dollars a year.
- **Per copy, self-publishing pays most.** A small publisher must sell about **2.9 times** and a large one **3.5–3.9 times** more copies to pay the author the same.
- **10–12.99 dollars** is the best price group for every route.
- **Without hits the niches change:** for less-known authors, Cookbooks, Health, Arts and Humor lead, while Romance falls to 16th and Mystery to 17th place.

### 05 · BERT sentiment

- A ready-trained BERT model read 20,000 reviews and was within one star of the reader for **95.2%** of them.
- The tone of reviews **does not separate popular books** and is not related to sales (correlation −0.03). Reviews describe satisfaction; sales depend on
  how many readers a book reaches.

### 06.1 · Linear regression (sales)

- It explains **22.4%** of the differences in copies between new books (R², log scale), against 0% for the baseline.
- A large publisher goes with about **3.9 times** more copies than self-publishing; Cookbooks and Health are the strongest niches.

### 06.2 · Random forest (sales)

- Better: **28.4%**. It shows curved effects, for example that books of about 400 pages sell most.
- It overfits a little (training 34.6%).

### 06.3 · Gradient boosting (sales)

- The best: **29.5%**, with little overfitting.
- The draft alone explains **17.1%**; the author's experience and the route add the rest.

### 06.4 · Model comparison: answer to part 2

| Model | Validation R² (log) | Test R² (log) | Within 2× of real copies | Typical miss |
|---|---:|---:|---:|---:|
| Baseline (always 79 copies) | – | −0.002 | 25.4% | 5.4 times |
| Linear regression (Ridge) | 0.216 | 0.224 | 29.6% | 4.3 times |
| Random forest | 0.270 | 0.284 | 31.8% | 4.0 times |
| **Gradient boosting** | **0.284** | **0.295** | 31.4% | 4.0 times |

*R²* is the share of the differences between books that the model explains. *Within 2×* means the prediction is at most 2 times too high or too low.

**Answer: partly.** The model explains about **30%** of the differences in yearly sales. It predicts the **typical** sales of a group well, but not one
particular book, and it cannot recognise hits. For example, a self-published first Romance book at 4.99 dollars sells about **38 copies** and earns about
**122 dollars a year**.

**Sub-question.** For less-known authors without hits the model works as well as for others (R² 0.22). It ranks **Cookbooks, Health, Sports, Arts and Crafts**
first, and Romance and Mystery lower, as the EDA showed.

In this data more expensive books sell more copies, because they are more often publisher books. This is an association, not proof that a higher price sells more.

## Limitations

- The popularity target is estimated from dated reviews, so books close to 100 ratings can fall on the wrong side.
- Ratings are not sales, and books without a rating are not in the data, so real chances for a new book are probably lower.
- The sales in part 2 are **synthetic** and made from ratings, so those numbers show the method, not real Amazon income.
- `self_published` comes from my rule, and `author_earlier_books` counts only books in this catalog.
- Marketing, writing quality and cover design are not in the data.
- The results show associations, not causes. A model like this should support an author's decision, not decide which authors deserve support.

## Conclusion

Data can tell a new author something useful, but not everything. Experience, a series and the publishing route are the strongest signs of success; the
draft alone gives a weaker signal. Typical income from one book is small, and a few hits earn most of the money. For a new author, practical non-fiction
looks steadier than the big fiction niches, and self-publishing keeps the largest share of each sale between 2.99 and 12.99 dollars.

Personally, this project taught me to go step by step from a 6.9 GB raw file to tested models, and showed me how much real sales data would add.

## Repository

```text
notebooks/
  01_eda.ipynb                         data, cleaning, EDA, definition of popularity
  02.1_logistic_regression.ipynb       popularity model 1
  02.2_random_forest.ipynb             popularity model 2
  02.3_gradient_boosting.ipynb         popularity model 3 and draft-only check
  02.4_model_comparison.ipynb          comparison and final choice
  03_synthetic_sales_generation.ipynb  how the synthetic sales were made
  04_eda_with_synthetic_sales.ipynb    EDA with sales: routes, royalties, prices, niches
  05_bert_sentiment.ipynb              BERT on review texts
  06.1_linear_regression.ipynb         sales model 1 (Ridge)
  06.2_random_forest.ipynb             sales model 2
  06.3_gradient_boosting.ipynb         sales model 3 and draft-only check
  06.4_model_comparison.ipynb          comparison, income by route and price, less-known authors
docs/
  project_summary.md                   project summary and presentation notes
  useful_links.md                      useful links
models/                                saved sales models (created by 06.1–06.3)
references/
  market_size_sources.md               public figures used for the market-size check in 03
requirements.txt
```

## How to run

1. Install Python 3.14 and run `pip install -r requirements.txt`.
2. Put the data files listed above in `data/raw/`.
3. Run the notebooks in order: `01`, `02.1`–`02.4`, `03`, `04`, `05`, `06.1`–`06.4`. Notebook `05` needs `torch` and `transformers` and works best with a graphics card.

## References

- Hou, Y., Li, J., He, Z., Yan, A., Chen, X., and McAuley, J. (2024). *Bridging Language and Items for Retrieval and Recommendation.* [Amazon Reviews 2023](https://amazon-reviews-2023.github.io/).
- [Kaggle: Amazon Kindle Books Dataset 2023](https://www.kaggle.com/datasets/asaniczka/amazon-kindle-books-dataset-2023-130k-books).
- Amazon KDP Help: [eBook Royalties](https://kdp.amazon.com/en_US/help/topic/G200644210), [List Price Requirements](https://kdp.amazon.com/help/topic/G200634560), [Digital Book Pricing](https://kdp.amazon.com/help/topic/G200634500) and [Sales Ranking](https://kdp.amazon.com/help/topic/G201648140).
- Authors Guild: [E-Book Royalty Math](https://authorsguild.org/news/e-book-royalty-math-the-house-always-wins-2/).
