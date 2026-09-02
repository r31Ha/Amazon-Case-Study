# Amazon Marketplace Analysis

An end-to-end data analysis project on 42,000+ Amazon product listings — data cleaning, exploratory analysis, and a predictive model for Best Seller status.

📄 **[Read the full report](./Report.md)** for findings, methodology, and conclusions.

## Dataset

This project uses the [Amazon Products Sales Dataset (42K+ Items) – 2025](https://www.kaggle.com/datasets/srisyra02/amazon-product-sales-data-42k-items-2025/data) from Kaggle. The raw CSV is not included in this repo (file size); download it yourself via one of the methods below.

**Option A — Kaggle website:** download directly from the link above and place the CSV in the project root.

**Option B — kagglehub:**
```python
import kagglehub

path = kagglehub.dataset_download("srisyra02/amazon-product-sales-data-42k-items-2025")
print("Path to dataset files:", path)
```

## Project Structure

```
.
├── README.md
├── Report.md   # full write-up: findings, methodology, limitations
├── amazon.ipynb                          # notebook: cleaning, EDA, modeling
└── .gitignore
```

## Setup

```bash
git clone https://github.com/r31Ha/Amazon-Case-Study
cd <repo-name>
pip install pandas numpy matplotlib scikit-learn
```

Download the dataset (see above), place the CSV in the project root, then open `analysis.ipynb`.

## What's Inside

- **Data cleaning** — parsing inconsistent text formats (embedded units, currency symbols, bucketed values, overloaded badge columns)
- **Exploratory analysis** — demand concentration, price/discount effects, sponsored vs. organic comparison, Best Seller characteristics, product segmentation
- **Predictive modeling** — classifying Best Seller status under severe class imbalance

See the [report](./Report.md) for details, results, and interpretation.