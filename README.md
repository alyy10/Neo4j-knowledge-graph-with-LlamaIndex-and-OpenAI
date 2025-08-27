# Neo4j-knowledge-graph-with-LlamaIndex-and-OpenAI
## Project Overview

This project presents a robust solution for constructing a knowledge graph-driven Retrieval-Augmented Generation (RAG) system tailored for financial sentiment analysis. It integrates Neo4j as the foundational graph database, LlamaIndex for efficient data indexing and retrieval, and OpenAI for intelligent query generation and comprehensive report summarization. The system's core functionality involves gathering real-time financial data through web scraping, organizing this information into a structured knowledge graph that highlights inter-entity relationships, and subsequently providing insightful, context-aware responses to user inquiries.

## Project Architecture

The system's architecture is designed to facilitate a seamless flow from data acquisition to insightful report generation. The process begins with a user query, which triggers the data gathering phase. Financial data is collected using Google APIs and web scraping techniques, forming a raw dataset. This raw data is then processed to build a knowledge graph within Neo4j, leveraging OpenAI for structuring and storing entity relationships. The resulting graph-based representation, combined with vector embeddings created using LlamaIndex, forms the basis for efficient data retrieval. When a user queries the system, the vector database is queried to fetch relevant information. This retrieved data is then provided as context to OpenAI, which generates a comprehensive and contextually accurate response back to the user.

<img width="772" height="899" alt="image" src="https://github.com/user-attachments/assets/afe1a126-dfcc-4556-914e-23f601605126" />

## Project Setup

To prepare your environment for this project, you will need to configure access to several external services: Google Custom Search API, OpenAI API, and Neo4j. Each of these services offers a free tier suitable for development and experimentation.

### Setting Up Google Custom Search API

This API enables programmatic searching of the web. To get started:

1. **Create a Google Cloud Platform (GCP) Project:** Navigate to the [Google Cloud Console](https://console.cloud.google.com/), create a new project, and select it.
2. **Enable the Custom Search JSON API:** In your GCP project, go to `APIs & Services` > `Library`, search for `Custom Search JSON API`, and enable it.
3. **Generate an API Key:** Under `APIs & Services` > `Credentials`, create a new API key. Securely save this key.
4. **Set Up a Custom Search Engine (CSE):** Visit the [Google Programmable Search Engine](https://cse.google.com/). Create a new search engine, specifying the sites to search (e.g., `*.com` for the entire web). After creation, obtain your Search Engine ID (CX value) from `Control Panel` > `Search Engine ID`.

**Free Tier Considerations:** The free tier typically allows 100 queries per day, with each query fetching up to 10 results. Be mindful of rate limits.

### Setting Up OpenAI API Key

Access to OpenAI's powerful language models requires an API key:

1. **Create an OpenAI Account:** Sign up on the [OpenAI website](https://platform.openai.com/).
2. **Generate an API Key:** Log in to the [OpenAI Platform](https://platform.openai.com/), go to `API Keys` under your account settings, and create a new secret key. Copy and save it immediately.

**API Charges:** OpenAI API usage is token-based. New accounts often receive a free trial credit. Once exhausted, billing setup is required. You can set spending limits to manage costs.

### Neo4j Setup

Neo4j is a graph database crucial for storing the knowledge graph:

1. **Create a Neo4j Account:** Visit the [Neo4j Aura Website](https://neo4j.com/cloud/platform/aura-graph-database/) and sign up for a free account.
2. **Launch a Free Tier Project:** From your Neo4j Aura dashboard, create a new instance and select the `Free Tier Plan`. This plan supports up to 200,000 nodes and relationships.
3. **Access Database Credentials:** Once your instance is ready, note down the Bolt URL, Username (default `neo4j`), and the temporary Password. Change the password after your first login.
4. **Generate an API Key (for External Access):** Use your database credentials to authenticate external connections from tools or libraries like Python.

**Free Tier Limitations:** The free tier provides one active database instance with a size limit of 200,000 nodes and relationships, offering 24/7 access and full API/driver support.

## File Analysis

This section provides a detailed breakdown of each file within the repository, outlining its role and contribution to the overall project functionality.

### `README.md`

This document serves as the primary entry point for understanding the project. It provides a comprehensive overview, detailing the project's purpose, architectural design, setup procedures, and a thorough analysis of the codebase. Its aim is to equip users with the necessary information to quickly grasp the project's scope and begin their engagement.

### `default__vector_store.json`

This JSON file is integral to the LlamaIndex persistence layer, storing the default vector embeddings. These embeddings are numerical representations of textual data, crucial for enabling efficient similarity searches within the RAG system. The file maps these embeddings to their respective document identifiers, allowing the system to load pre-computed data and avoid redundant computation.

### `docstore.json`

Serving as a document store, this file manages metadata and content hashes for all documents processed by LlamaIndex. It facilitates the tracking and retrieval of original documents using unique identifiers, which is vital for maintaining data integrity and ensuring accurate context retrieval during response generation within the RAG framework.

### `engine.py`

This Python script encapsulates the core logic for querying the knowledge graph and generating analytical reports. It features:

* `query_and_generate_reports(queries: List[str])`: This function processes a list of user queries, leveraging the knowledge graph query engine to aggregate relevant context. It then utilizes an OpenAI language model to synthesize this context into a summary report.
* `save_reports_to_file(results: List[Dict[str, str]], filename: str)`: This utility function is responsible for persisting the generated reports, along with their original queries and contexts, into a specified text file.

The script also handles the initialization of the Neo4j graph store connection, loads the `PropertyGraphIndex`, and configures the query engine. It includes illustrative examples of financial sentiment analysis queries and demonstrates the process of executing them and saving the outcomes.

### `graph_store.json`

As part of the LlamaIndex storage context, this file contains metadata and configuration pertinent to the Neo4j graph database. While the actual graph data resides within Neo4j, this file assists LlamaIndex in managing its interactions with the graph store, facilitating efficient indexing and querying operations based on the defined schema.

### `graph_store_creation.py`

This script is dedicated to the initial setup and population of the Neo4j knowledge graph. Its primary functions include:

* **Search Query Generation:** It employs an OpenAI model to formulate precise search queries based on user input, targeting specific financial sentiment analysis needs.
* **Data Acquisition:** It orchestrates the collection of web content by invoking functions from `utils.py`, which execute the generated search queries, retrieve full web page content, and organize it into a local dataset of text files.
* **Knowledge Graph Indexing:** The script establishes a `Neo4jPropertyGraphStore` connection and then uses `PropertyGraphIndex.from_documents` to ingest the gathered documents into the Neo4j graph database. It integrates `OpenAIEmbedding` for creating text embeddings and `SchemaLLMPathExtractor` with `OpenAI` for extracting the graph schema.
* **Persistence:** Finally, it saves the LlamaIndex storage context to a local directory (`./storage`), ensuring that the knowledge graph can be efficiently reloaded for subsequent operations.

This script is fundamental for preparing the knowledge graph, a prerequisite for the RAG system's query capabilities.

### `image__vector_store.json`

This JSON file is intended for storing vector embeddings related to image data, should the project incorporate visual analysis. In the context of this project, which focuses on text-based financial sentiment, this file is currently empty, indicating that image embeddings are not actively utilized. It may serve as a placeholder or an artifact from a broader LlamaIndex configuration that supports multimodal data types.

### `index_store.json`

This LlamaIndex storage context file holds critical metadata about the indices created within the system. It specifically details the `PropertyGraphIndex`, including its unique identifier and summary. This information is crucial for LlamaIndex to correctly load and manage the index from persistent storage, thereby enabling efficient retrieval operations without the need for repeated index reconstruction.

### `projectFile.ipynb`

This Jupyter Notebook serves as a comprehensive, executable demonstration of the entire project workflow. It includes:

* **Detailed Explanations:** Provides in-depth descriptions of the problem, the role of Generative AI, and the project's objectives.
* **Methodology Breakdown:** Outlines the step-by-step approach, covering data collection, knowledge graph construction, vector embedding, indexing, and RAG workflow integration.
* **Setup Instructions:** Offers detailed guidance for configuring Google Custom Search API, OpenAI API, and Neo4j, including insights into their free tier limitations and credential generation.
* **Code Implementation:** Contains executable Python code cells that illustrate each stage of the project, from API setup and data collection to knowledge graph creation and querying.

This notebook is an invaluable resource for anyone looking to understand, replicate, or extend the project, offering both theoretical context and practical implementation details.

### `utils.py`

This Python script provides a collection of utility functions that support various data processing and AI-related tasks within the project. Key functions include:

* `search_with_google_api(query)`: Executes a Google Custom Search using the configured API key and search engine ID, returning relevant search results.
* `generate_search_queries(user_input)`: Leverages OpenAI (GPT-4) to generate a list of 5-7 specific and pertinent search queries for financial sentiment analysis, based on a given user input. This function acts as an intelligent data acquisition assistant.
* `fetch_full_content(url)`: Retrieves the complete textual content of a specified web page. It employs `requests` for HTTP requests and `BeautifulSoup` for parsing HTML, incorporating error handling and user-agent headers for robust web scraping.
* `create_dataset_from_queries(queries, directory="dataset")`: Manages the data collection pipeline. It takes a list of queries, uses `search_with_google_api` to obtain search results, and then `fetch_full_content` to extract content from relevant links. The collected data is saved as individual text files within a designated directory (`dataset/`), aiming to gather up to 10 valid documents per query.
* `generate_summary_report(context: str, query: str)`: Utilizes OpenAI (GPT-4) to produce a detailed summary report for financial sentiment analysis. It takes the aggregated context from the knowledge graph and the original query, generating a comprehensive report that includes identified trends, sentiments, underlying factors, and actionable insights.

This script centralizes the core data acquisition and AI-driven text generation logic, contributing to the modularity and clarity of the main project scripts (`engine.py`, `graph_store_creation.py`).

## Getting Started

To set up and run this project, follow these steps:

1. **Clone the Repository:**

   ```bash
   git clone https://github.com/alyy10/Neo4j-knowledge-graph-with-LlamaIndex-and-OpenAI.git
   cd Neo4j-knowledge-graph-with-LlamaIndex-and-OpenAI
   ```
2. **Configure API Keys and Credentials:**

   * Obtain your Google Custom Search API key, Search Engine ID, OpenAI API key, and Neo4j credentials (Bolt URL, Username, Password) by following the instructions in the [Project Setup](#project-setup) section.
   * Create a file named `constants.py` in the project's root directory and populate it with your credentials:

   ```python
   # constants.py
   google_api = "YOUR_GOOGLE_API_KEY"
   search_engine_id = "YOUR_SEARCH_ENGINE_ID"
   open_ai_key = "YOUR_OPENAI_API_KEY"
   neo4j_username = "YOUR_NEO4J_USERNAME"
   neo4j_password = "YOUR_NEO4J_PASSWORD"
   neo4j_url = "YOUR_NEO4J_BOLT_URL"
   ```
3. **Install Dependencies:**

   * It is highly recommended to use a Python 3.10 virtual environment. Create and activate one, then install the required packages using `pip`:

   ```bash
   pip install -r requirements.txt
   ```
4. **Build the Knowledge Graph:**

   * Execute the `graph_store_creation.py` script to initiate data collection and populate your Neo4j knowledge graph. This process may take some time depending on the volume of data.

   ```bash
   python graph_store_creation.py
   ```
5. **Query the Knowledge Graph and Generate Reports:**

   * After the knowledge graph is built, run the `engine.py` script to execute predefined queries and generate financial sentiment reports.

   ```bash
   python engine.py
   ```

   * The generated reports will be saved to `financial_sentiment_reports.txt` in the project directory.

## Usage

Once the project is set up and the knowledge graph is populated, you can customize the `engine.py` script to perform your own financial sentiment analysis. The `query_and_generate_reports` function can be invoked with any list of strings representing your desired queries, allowing for flexible and targeted analysis.
