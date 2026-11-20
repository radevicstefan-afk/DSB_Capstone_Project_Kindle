# Total book sales: a rough picture from public sources

Reviewed on 19 November 2026. These sources do not give sales for the books in this project. They are used only as a rough check that the total synthetic
numbers in `03_synthetic_sales_generation.ipynb` are of a reasonable size.

## Sources and figures

| Source | Figure | What it measures | Reliability |
|---|---|---|---|
| [AAP StatShot Annual 2024](https://publishers.org/news/aap-statshot-annual-report-publishing-revenues-totaled-32-5-billion-for-calendar-year-2024/) | e-books: **2.1 billion dollars** in 2024 (+1.5%); trade books 21.2 billion; total 32.5 billion | US publisher revenue; no number of copies, and probably does not cover most self-published books | **high**: industry association with a stated method |
| [WordsRated: Amazon Publishing Statistics](https://wordsrated.com/amazon-publishing-statistics/) | Amazon sells "over **487 million** e-books a year" through Kindle; share of e-book sales at least 67%, or 83% with Kindle Unlimited | copies, Kindle; no year stated | **low**: source and year of the figures not stated |
| WordsRated (same page) | KDP pays "over **520 million dollars**" to authors a year; 522 million expected for 2022 | KDP payouts to authors | **low**: source not stated |
| WordsRated (same page) | "over 1.4 million" self-published books a year; 32.8 million titles in the store | number of titles | **low** |
| [KDP Help](https://kdp.amazon.com/en_US/help/topic/G200641170) | KDP Select payout to authors for **October 2026: 71.1 million dollars** | one month of Kindle Unlimited payouts | **high** for one month; not a yearly total |
| [KDP Community](https://www.kdpcommunity.com/s/article/KDP-Select-Global-Fund-and-All-Stars-Bonus-Update---January-2026), [Written Word Media](https://www.writtenwordmedia.com/kdp-global-fund-payouts/) | monthly KDP Select fund **58.3–67.7 million dollars** in 2026; payout per page read 0.0042–0.0054 dollars | Kindle Unlimited | **medium**: sources stated, not checked separately |
| [Forte Labs](https://fortelabs.com/blog/how-i-used-amazon-reviews-to-predict-sales-of-my-book/) | about 1.5% of buyers leave a review (0.3–4.8%) | 20 bestsellers, estimated sales | **medium**: small and unusual sample |
| [KDP Help: Sales Ranking](https://kdp.amazon.com/help/topic/G201648140) | rank depends on customer activity compared with other books; no sales threshold is published | ranking rule | **high**, but no sales figures |

Links reviewed without a usable figure: [ebooks.com news feed](https://about.ebooks.com/ebook-industry-news-feed/) (access denied), an Amazon category page
(error 503), a YouTube video about bestsellers (only the title could be read), [Sellerview](https://sellerview.ai/blog/make-1000-a-month-selling-on-amazon)
and [Sell on Amazon](https://sell.amazon.com/blog/make-money-on-amazon) (sellers of physical products).

## How the sources were used

- A first version with **50 buyers per rating** gave **436 million** copies in 2022 for the sample alone, close to the WordsRated figure for all of Kindle.
  This was clearly too high.
- Notebook `03` therefore compares four levels and uses **5 buyers per rating**: **43.6 million** copies in 2022, close to the market check of
  **42.9 million** (the sample's 8.81% share of reviews × 487 million Kindle e-books).
- The implied publisher e-book revenue (about **2.8 billion dollars**) is of the same size as the AAP figure of **2.1 billion** for 2024.
- The author's percentages, the comparison of publishing routes and the break-even multiples (2.9 and 3.5–3.9 times) do not depend on the total size.
