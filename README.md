# Optimizing E-Commerce Product Titles for SEO 
 This project demonstrates how to optimize e-commerce product titles for better SEO performance using Python and regular expressions. The solution cleans and refines product titles by removing unnecessary product codes, color adjectives, and filler words while preserving key details such as measurement suffixes to create concise, impactful titles. It showcases practical data wrangling, regex pattern design, and algorithmic problem-solving to improve online visibility and user engagement.

# Product Title Optimization Report

## Table of Contents
- [Introduction](#introduction)
- [Dataset Overview](#dataset-overview)
- [Data Cleaning and Preparation](#data-cleaning-and-preparation)
- [Methodology](#methodology)
- [Challenges Encountered](#challenges-encountered)
- [Key Insights](#key-insights)
- [Recommendations](#recommendations)
- [Conclusion](#conclusion)

## Introduction
In the realm of e-commerce, optimizing product titles is crucial for improving search visibility, enhancing user engagement, and driving sales. This project focuses on refining product titles using **Python and regular expressions (regex)** to clean, standardize, and structure them for SEO compliance. The goal is to remove redundant details while retaining essential product attributes, ensuring clarity and conciseness.

## Dataset Overview
- **Dataset Source:** `productdata.xlsx`
- **Total Rows:** 3,847 products (before initial cleaning)
- **Total Columns:** 6
- **Key Features:**
  - `PRODUCTID`: Unique identifier for each product.
  - `TITLE`: Original product title.
  - `BULLET_POINTS`: Product highlights.
  - `DESCRIPTION`: Detailed product description.
  - `PRODUCTTYPEID` and `ProductLength`: Additional numerical attributes.

## Data Cleaning and Preparation
Before optimizing titles, we conducted a thorough data cleaning process to enhance consistency and accuracy.

### 1. **Loading the Dataset**
The dataset was imported using Pandas:
```python
import pandas as pd
product = pd.read_excel("productdata.xlsx")
```
We verified the dataset structure using:
```python
product.shape  # Output: (3,847, 6)
product.info()  # Displays column data types and null counts
```
Observations:
- **3,847 rows and 6 columns** before cleaning.
- **Presence of missing values** in key fields like `BULLET_POINTS` and `DESCRIPTION`.

### 2. **Handling Duplicate Entries**
To ensure unique product records, we identified and removed duplicate rows:
```python
duplicates = product.duplicated().sum()
product = product.drop_duplicates()
```
- **217 duplicate rows** were removed.

### 3. **Handling Missing Values**
A check for missing values revealed:
```python
missing_values = product.isnull().sum()
```
Findings:
- **41% missing values** in `BULLET_POINTS`
- **56% missing values** in `DESCRIPTION`

Approach:
- Retained records where `TITLE` was present.
- Filled missing values in `BULLET_POINTS` and `DESCRIPTION` with `"No information available"`:
```python
product["BULLET_POINTS"].fillna("No information available", inplace=True)
product["DESCRIPTION"].fillna("No information available", inplace=True)
```

### 4. **Standardizing Column Names**
For consistency, column names were converted to lowercase with underscores:
```python
product.columns = product.columns.str.lower().str.replace(" ", "_")
```
### 5. **Exploratory Data Analysis**
To understand title distribution, we visualized character lengths:
```python
import matplotlib.pyplot as plt
import seaborn as sns

plt.figure(figsize=(10,5))
sns.histplot(product['title'].str.len(), bins=30, kde=True)
plt.title("Distribution of Title Lengths")
plt.xlabel("Character Length")
plt.ylabel("Frequency")
plt.show()
```
Observations:
- Majority of titles ranged **between 50-80 characters**, exceeding the recommended **50-60 characters**.
- Certain titles contained excessive descriptions, affecting clarity.

### 6. **Removing Unnecessary Special Characters**
To eliminate unwanted symbols, a regex-based approach was applied:
```python
import re
product['title'] = product['title'].apply(lambda x: re.sub(r'[^A-Za-z0-9 ]+', '', str(x)))
```
### 7. **Fixing Formatting Issues**
- **Uppercase words:** Converted fully uppercase words to title case, except for acronyms.
- **Unspaced words:** Ensured proper spacing using regex.

## Methodology
### 1. **Product Code and Color Removal**
Regex was used to filter unnecessary elements:
```python
color_list = ["black", "white", "red", "blue", "green", "yellow", "purple", "orange", "pink", "gray", "grey", "brown", "beige", "gold", "silver", "navy", "teal", "maroon"]
def remove_colors(title):
    return ' '.join([word for word in title.split() if word.lower() not in color_list])

product['title'] = product['title'].apply(remove_colors)
```
### 2. **Measurement Suffix Extraction**
Ensuring essential numerical details were preserved:
```python
measurement_pattern = r'(-?\d+(?:\.\d+)?\s*[A-Za-z]+(?:\s+[A-Za-z]+)?)$'
def extract_measurement(title):
    match = re.search(measurement_pattern, title)
    return match.group(1) if match else ""

product['measurement'] = product['title'].apply(extract_measurement)
```
### 3. **Word Count Limiting**
Titles were capped at **6-7 words** for clarity:
```python
def limit_words(title, max_words=7):
    words = title.split()
    return ' '.join(words[:max_words]) if len(words) > max_words else title

product['title'] = product['title'].apply(limit_words)
```

## Challenges Encountered
- **Inconsistent Formatting:** Different naming conventions required adaptable filtering.
- **Measurement Extraction Complexity:** Retaining essential numerical attributes without redundancy.
- **Edge Cases:** Book titles needed special handling to prevent over-truncation.
- **Word Count Optimization:** Balancing brevity while keeping key details.

## Key Insights
- **Concise Titles Improve Readability:** Shorter titles enhance clarity and engagement.
- **Essential Attributes Must Be Retained:** Filtering improves structure without losing key information.
- **Category-Specific Adjustments May Be Needed:** Some products require unique processing rules.

## Recommendations
- **Enhance Regex Patterns:** Improve differentiation between necessary and redundant numerical data.
- **Implement Category-Based Processing:** Tailor cleaning logic for books, electronics, and apparel.
- **Improve Measurement Handling:** Support broader measurement formats.
- **Automate Review Process:** Use machine learning to flag ambiguous cases.
- **SEO Testing:** Conduct A/B tests to measure ranking improvements.

## Conclusion
This project successfully optimized product titles by refining data through cleaning and regex-based filtering. The methodology ensures clarity, maintains essential product attributes, and aligns with SEO best practices. Future improvements could include **category-specific processing** and **machine learning-driven enhancements** for even greater precision.
