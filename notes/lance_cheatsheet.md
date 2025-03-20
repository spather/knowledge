# Lance Cheatsheet

## Concepts
### Lance vs LanceDB
https://lancedb.github.io/lancedb/faq/#what-is-the-difference-between-lance-and-lancedb

### Datasets vs Tables
In the Lance ecosystem, there are two related but distinct concepts:

Lance Datasets (from the `lance` package):
* A Lance dataset is a file-based columnar data format (similar to Parquet)
When you use `lance.write_dataset()`, you're writing data to disk in the Lance format
* A dataset represents the physical storage of your data
The dataset is independent of any database - it's just data stored in files

LanceDB Tables (from the `lancedb` package):
* A table is a logical construct within the LanceDB database system
* Tables provide query functionality, indexing, and vector search capabilities
* Tables typically reference Lance datasets as their underlying storage

## Links
* [Lance Documentation](https://lancedb.github.io/lance/)
* [LanceDB Documentation](https://lancedb.github.io/lancedb/)
