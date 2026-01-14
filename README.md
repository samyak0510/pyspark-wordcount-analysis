# CSE 587 - Distributed Computing Assignment 2

## Word Count and Data Analysis with PySpark

**Author:** Samyak Shah  
**Course:** CSE 587 - Data Intensive Computing (Fall 2024)

---

## Overview

This project implements distributed text processing and analysis using Apache PySpark. The analysis is performed on "History of the World War, Volume 1" from Project Gutenberg, chosen for its comprehensive content, public domain availability, and structured narrative that facilitates effective text processing.

## Project Structure

```
.
├── src/
│   └── dic_task.ipynb       # PySpark implementation notebook
├── docs/
│   └── assignment.pdf       # Assignment requirements
├── reports/
│   └── report.pdf           # Detailed analysis report with DAG screenshots
├── .gitignore
└── README.md
```

---

## Part 1: Word Count and Data Analysis [50 Points]

### 1.1 Basic Word Count Implementation [20 Points]

Uses PySpark RDD transformations to count word occurrences:

```python
wordCounts = textFile.flatMap(lambda line: line.split()) \
                    .map(lambda word: (word, 1)) \
                    .reduceByKey(lambda a, b: a + b)
sortedWordCounts = wordCounts.sortBy(lambda x: x[1], ascending=False)
```

**Key Operations:**
- `flatMap`: Splits each line into words
- `map`: Transforms each word into a (word, 1) pair
- `reduceByKey`: Aggregates counts for each word

### 1.2 Extended Word Count [20 Points]

Enhanced preprocessing with:
- Case normalization (lowercase conversion)
- Punctuation removal using `str.maketrans`
- Stop words filtering (common words like "the", "and", "to", etc.)

**Top 15 Words After Cleaning:**

| Rank | Word    | Count |
|------|---------|-------|
| 1    | german  | 219   |
| 2    | war     | 196   |
| 3    | against | 166   |
| 4    | army    | 160   |
| 5    | germany | 158   |
| 6    | french  | 101   |
| 7    | one     | 100   |
| 8    | general | 99    |
| 9    | great   | 97    |
| 10   | first   | 97    |
| 11   | austria | 93    |
| 12   | made    | 90    |
| 13   | project | 85    |
| 14   | russia  | 81    |
| 15   | any     | 79    |

### 1.3 Data Flow Analysis [10 Points]

The word count execution is broken into multiple stages visible in the Spark DAG. The lineage graph shows the transformation chain:

```
textFile (RDD)
    ↓ flatMap
Words (RDD)
    ↓ map
Word-Count Pairs (RDD)
    ↓ reduceByKey
Aggregated Counts (RDD)
    ↓ sortBy
Sorted Results (RDD)
```

See `reports/report.pdf` for DAG screenshots from Spark UI.

---

## Part 2: Word Co-Occurrence [30 Points]

### 2.1 Bigram Implementation [20 Points]

Generates and counts consecutive word pairs (bigrams):

```python
def generateBigrams(words):
    return zip(words, words[1:])

bigrams = processedText.flatMap(lambda line: generateBigrams(line.split()))
filteredBigrams = bigrams.filter(lambda bigram: bigram[0] not in stopWords and bigram[1] not in stopWords)
bigramCounts = filteredBigrams.map(lambda bigram: (bigram, 1)).reduceByKey(lambda a, b: a + b)
```

**Top 10 Most Frequent Bigrams:**

| Rank | Bigram             | Count |
|------|--------------------|-------|
| 1    | project gutenberg  | 48    |
| 2    | against germany    | 41    |
| 3    | united states      | 39    |
| 4    | army corps         | 20    |
| 5    | german army        | 18    |
| 6    | world war          | 17    |
| 7    | von hindenburg     | 16    |
| 8    | against austria    | 16    |
| 9    | electronic works   | 16    |

### 2.2 Analysis of Co-Occurrence [10 Points]

Bigrams provide deeper insights than single word frequencies:

- **Contextual Understanding:** Captures the context in which words appear
- **Phrase Identification:** Identifies common expressions crucial for language modeling
- **Semantic Relationships:** Reveals word relationships for sentiment analysis and topic modeling
- **Enhanced ML Models:** Bigram features improve NLP model performance

---

## Part 3: Data Bias and Review [20 Points]

### 3.1 Bias Identification [10 Points]

**Scenario 1: Fitness app excluding offline users**
- **Type:** Selection Bias
- **Issue:** Dataset doesn't represent all user populations
- **Solution:** Implement offline data synchronization with automatic sync on reconnection

**Scenario 2: Recruitment algorithm favoring top universities**
- **Type:** Algorithmic Bias (Favoritism Bias)
- **Issue:** Excludes qualified candidates from less prestigious institutions
- **Solution:** Redesign to include work experience, skills, certifications, and practical assessments

### 3.2 Paper Review [10 Points]

**"Discretized Streams: Fault-Tolerant Streaming Computation at Scale"**

The paper presents a framework for handling streaming data using discretized streams (D-Streams). Key contributions:
- Divides continuous streams into manageable micro-batches
- Bridges real-time stream processing with batch processing paradigms
- Provides fault tolerance through lineage-based recovery
- Demonstrates high scalability with low latency

Applications include IoT data processing, social media monitoring, and financial analytics.

---

## Requirements

```bash
pip install pyspark pyngrok
```

## Usage

1. Open `src/dic_task.ipynb` in Jupyter Notebook or Google Colab
2. Ensure the text file from Project Gutenberg is accessible
3. Run cells sequentially
4. Access Spark UI via ngrok tunnel for DAG visualization

---

## References

1. Zaharia, M., Das, T., Li, H., Hunter, T., Shenker, S., & Stoica, I. (2013). *Discretized Streams: Fault-Tolerant Streaming Computation at Scale*. ACM SIGMOD.
2. Project Gutenberg. *History of the World War, Volume 1*. https://www.gutenberg.org/cache/epub/74726/pg74726.txt
3. Apache Spark Documentation. https://spark.apache.org/docs/latest/api/python/
