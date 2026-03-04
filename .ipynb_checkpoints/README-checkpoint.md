![logo_ironhack_blue 7](https://user-images.githubusercontent.com/23629340/40541063-a07a0a8a-601a-11e8-91b5-2f13e4e6b441.png)

# Lab | Data Models and Storage Engines

## Overview

This lab gives you hands-on practice with the three major data models — relational, document, and graph — and helps you reason about when each is the right choice. You will model the same domain in all three styles, execute queries against each, and compare how different workloads (transactional vs analytical) map to different storage designs. The focus is on building practical judgment: after completing this lab, you should be able to look at a real-world data problem and recommend a data model and processing paradigm that fits.

## Learning Goals

By the end of this lab, you should be able to:

- Design a normalized relational schema and demonstrate joins to reconstruct data
- Model the same data as a document collection and reason about locality trade-offs
- Build a simple graph representation and answer relationship-based questions through traversal
- Distinguish between workloads suited for transactional (OLTP) vs analytical (OLAP) processing
- Compare the strengths and weaknesses of each model for different query patterns

## Setup and Context

You will work in a Jupyter Notebook using pandas for relational modeling, dictionaries/JSON for document modeling, and simple Python data structures for graph representation. No external databases are required. All data is created in memory.

## Requirements

- Fork this repository to your own GitHub account.
- Clone your fork to your machine.
Install dependencies if needed:

```bash
pip install pandas numpy
```

## Getting Started

Create a new notebook and name it `m2-02-data-models-storage-engines-lab.ipynb`. Complete all tasks in this notebook with code and markdown explanations. Before you submit, restart your kernel and run the notebook top to bottom.

## Tasks

### Task 1: Build a Relational Schema

Model an online movie rental service. Design a normalized relational schema using pandas DataFrames for the following entities:

**Step 1:** Create these tables:
- `customers` with columns: `customer_id`, `name`, `email`, `city`, `signup_date`
- `movies` with columns: `movie_id`, `title`, `genre`, `release_year`, `rating` (1-5 scale)
- `rentals` with columns: `rental_id`, `customer_id`, `movie_id`, `rental_date`, `return_date`, `price`

Populate with at least 8 customers, 10 movies, and 25 rentals. Make sure some customers have multiple rentals and some movies are rented multiple times.

**Step 2:** Write and execute these queries using pandas operations:
1. Find all movies rented by a specific customer (requires joining rentals with movies).
2. Find the top 5 most-rented movies (group and count).
3. Compute the total revenue per genre (join rentals with movies, group by genre, sum price).
4. Find customers who have never rented a movie rated below 3 (multi-step filtering).

**Step 3:** Demonstrate normalization. Show what the data would look like as a single denormalized table (join all three tables). Write a markdown cell explaining what redundancy appears and why the normalized version is preferable for updates.

### Task 2: Model the Same Data as Documents

Represent the same movie rental data using the document model.

**Step 1:** Create a list of customer documents (Python dictionaries) where each document contains the customer's information and a nested list of their rental history, with movie details embedded in each rental entry.

**Step 2:** Execute the same four queries from Task 1 on the document collection:
1. Find all movies rented by a specific customer.
2. Find the top 5 most-rented movies.
3. Compute total revenue per genre.
4. Find customers who have never rented a movie rated below 3.

**Step 3:** Write a markdown cell comparing the document queries to the relational queries. Which queries were easier to write? Which were harder? For which query pattern does the document model have a clear advantage?

### Task 3: Model Relationships as a Graph

Build a graph representation of the rental data to answer relationship-based questions.

**Step 1:** Create a graph using Python dictionaries where:
- Nodes represent customers, movies, and genres.
- Edges represent relationships: `rented` (customer → movie), `belongs_to` (movie → genre), `lives_in` (customer → city).

```python
# Suggested structure
nodes = {
    "c001": {"type": "customer", "name": "Alice"},
    "m001": {"type": "movie", "title": "Inception"},
    "action": {"type": "genre", "name": "Action"},
    # ... more nodes
}

edges = [
    ("c001", "rented", "m001"),
    ("m001", "belongs_to", "action"),
    # ... more edges
]
```

**Step 2:** Write traversal functions to answer:
1. "Which movies did customer X rent?" — one hop from customer through `rented` edges.
2. "Which genres does customer X prefer?" — two hops: customer → movies → genres, then count.
3. "Which customers share a genre preference with customer X?" — three hops: customer X → movies → genres → other customers who rented movies in the same genre.
4. "Recommend movies for customer X" — find movies in their preferred genres that they have not yet rented.

**Step 3:** Write a markdown cell explaining why the recommendation query (Task 3, question 4) would be difficult to express in SQL or with documents. What makes the graph model natural for this kind of traversal?

### Task 4: OLTP vs OLAP Workload Analysis

This task asks you to reason about how different database types handle different workloads.

**Step 1:** Create a DataFrame representing a log of 5,000 e-commerce transactions with columns: `transaction_id`, `customer_id`, `product_id`, `amount`, `payment_method`, `timestamp`, `status` (completed/refunded/pending).

**Step 2:** Simulate OLTP-style operations:
1. Look up a single transaction by its ID (simulate a point query).
2. Insert a new transaction (append a row).
3. Update the status of a transaction from "pending" to "completed."
4. Check that the transaction is valid: the amount must be positive and the status must be one of the allowed values (simulate a consistency check).

**Step 3:** Simulate OLAP-style operations on the same data:
1. Compute total revenue by payment method.
2. Find the average transaction amount by month.
3. Identify the top 10 customers by total spending.
4. Compute the refund rate (percentage of refunded transactions).

**Step 4:** Write a markdown cell explaining: Why would OLTP operations typically use row-major storage while OLAP operations use column-major storage? How does the access pattern of each workload type map to the memory layout that serves it best?

### Task 5: Choosing the Right Model

For each of the following real-world scenarios, recommend a data model (relational, document, or graph) and a processing paradigm (OLTP or OLAP). Write 3-4 sentences justifying each choice.

1. A hospital patient record system where doctors need to quickly look up a patient's complete medical history.
2. A social network that needs to suggest "friends of friends" connections.
3. A financial reporting system that computes monthly revenue breakdowns across product categories and regions.
4. An IoT platform that receives sensor readings from 10,000 devices and needs to detect anomalies in real time.
5. A content management system where each article has a different set of metadata fields.

## Submission

### What to submit

Submit the following file:

- A notebook file `m2-02-data-models-storage-engines-lab.ipynb` containing all five tasks with code and markdown explanations

### Definition of done (checklist)

Before you submit, make sure:

- [ ] The notebook runs **top to bottom** without errors after a kernel restart.
- [ ] Task 1 includes normalized tables, four queries, and a normalization explanation.
- [ ] Task 2 includes document representations and the same four queries with comparison.
- [ ] Task 3 includes a graph representation with traversal functions and a difficulty comparison.
- [ ] Task 4 includes OLTP and OLAP simulations with an explanation of storage layout fit.
- [ ] Task 5 provides justified recommendations for all five scenarios.
- [ ] Markdown cells contain thoughtful analysis, not just code output.

### How to submit (Git workflow)

When you are done, make sure all changes are saved, then run:

```bash
git add .
git commit -m "Solved m2-02 lab"
git push -u origin HEAD
```

- Make a pull request.
- Paste the link to your pull request in the Student Portal.

## Evaluation Criteria

Your work will be evaluated on model design quality, query correctness, and analytical reasoning. Each data model implementation should be correct and complete. Queries should produce accurate results across all three models. Your markdown explanations should demonstrate genuine understanding of trade-offs, not just summarize what the code does. The goal is to show that you can evaluate data modeling options and make informed decisions for real-world scenarios.
