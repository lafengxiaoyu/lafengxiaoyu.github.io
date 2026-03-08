---
layout: page
title: "KR4W - Knowledge Representation for Web with Pokemon Dataset"
description: Student course project on knowledge graph construction using Pokemon data with GraphDB and SPARQL queries.
img: assets/img/projects/kr4w/kr4w1.png
importance: 4
category: school
github: https://github.com/lafengxiaoyu/KRoW
---

A student course project for Knowledge Representation and Web (KR4W) focused on knowledge graph construction and SPARQL queries using Pokemon data from various sources.

**Repository:** https://github.com/lafengxiaoyu/KRoW

## Description

KR4W is a knowledge graph construction project for the Knowledge Representation and Web course, exploring the application of semantic web technologies to Pokemon data. The project demonstrates the complete workflow of building a knowledge graph from raw datasets, constructing RDF triples, and performing SPARQL queries for information retrieval.

## Project Overview

KR4W (Knowledge Representation for Web) is a course project completed as part of the Knowledge Representation and Web curriculum.

### Dataset

The project integrates Pokemon data from multiple sources:
- **Kaggle**: Pokemon datasets (https://www.kaggle.com/rounakbanik/pokemon)
- **Pokemon Wiki**: Additional Pokemon information and details
- **DBpedia**: External linked dataset for owl:sameAs relationships

### Data Conversion

The project uses **GraphDB** to construct the knowledge graph with SPARQL. Several semantic web relations are utilized:
- `owl:sameAs` - For linking entities to external datasets
- `foaf:depiction` - For associating Pokemon with images
- Standard RDF/OWL relationships for Pokemon attributes

**Converted File Formats:**
- `.nt` (N-Triples) - Triple data format
- `.nq` (N-Quads) - Quad data format with contexts
- `.ttl` (Turtle) - Terse RDF Triple Language

### SPARQL Queries

Five SPARQL queries were implemented and deployed on [Triply](https://krr.triply.cc/lafengzhenyu/datasetgifquery/queries):

1. **Battle System Queries** (2 queries)
   - Query for finding advantageous Pokemon types in battles
   - Query for battle strategy recommendations

2. **Find Enemy Queries** (2 queries)
   - Query for finding Pokemon that are strong against specific types
   - Query for locating Pokemon with particular characteristics

3. **owl:SameAs Query**
   - Query for constructing and verifying owl:SameAs relationships

## Key Features

- 🗄️ **Knowledge Graph Construction** - Building RDF graphs from heterogeneous data sources
- 🔗 **Data Integration** - Merging multiple datasets with proper relationships
- 📊 **SPARQL Querying** - Complex queries for battle strategies and type advantages
- 🔍 **Linked Data** - Integration with DBpedia using owl:sameAs
- 🐍 **Python Integration** - Jupyter notebooks for data processing
- 🖥️ **GraphDB** - Professional triple store for knowledge graph management

## Technology Stack

- **Knowledge Graph**: GraphDB (Ontotext)
- **Query Language**: SPARQL
- **Programming**: Python, Jupyter Notebook
- **Data Formats**: RDF, N-Triples, N-Quads, Turtle
- **Deployment**: Triply platform

## Project Structure

```
KR4W/
├── dataset/                      # Source datasets
├── pokemon2.csv.nq              # Converted quad format data
├── statements1.nt               # Triple statements
├── queryResults.ttl             # Query output in Turtle format
├── GraphDB Construction - Pokemon.txt
├── GraphDB Construction - Type.txt
├── Merge two files including triples.ipynb
├── KR4W_GAO_and_LIU.pdf         # Project report
└── README.md
```

## Usage

### Building the Knowledge Graph

1. **Data Collection**
   - Download Pokemon datasets from Kaggle
   - Extract additional information from Pokemon Wiki
   - Gather linked data from DBpedia

2. **Data Conversion**
   - Convert CSV data to RDF triples using Python scripts
   - Merge multiple triple files into a unified knowledge graph
   - Use GraphDB for knowledge graph construction

3. **Query Execution**
   - Load the knowledge graph into GraphDB
   - Run SPARQL queries for battle analysis
   - Retrieve results in various formats (Turtle, JSON, etc.)

### SPARQL Queries

The project includes five interactive SPARQL queries available on Triply:

- **Battle Analysis Queries**
  - Find Pokemon advantages in type matchups
  - Recommend optimal battle strategies

- **Enemy Finding Queries**
  - Identify Pokemon that counter specific types
  - Search for Pokemon with particular stat combinations

- **Relationship Queries**
  - Explore owl:sameAs relationships with external datasets

## Report

The complete project report is available in the repository as `KR4W_GAO_and_LIU.pdf`, containing:
- Detailed methodology and approach
- Dataset description and preprocessing steps
- Knowledge graph construction process
- SPARQL query designs and results
- Analysis and conclusions

## Team

- **Zhenyu Gao** - Student
- **Rui Liu** - Student

## Course Information

This project was completed as part of a knowledge representation and reasoning course.

## Screenshots

<div class="row">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.html path="assets/img/projects/kr4w/kr4w1.png" title="KR4W Project" class="img-fluid rounded z-depth-1" %}
  </div>
</div>

## License

This project is for educational purposes.

## Contact

For questions about this project, please open an issue on GitHub.
