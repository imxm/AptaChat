# Specification Document  
## AI Chat Bot with ETL System  

### Executive Summary  

This document outlines the solution architecture for an AI-powered chatbot integrated with blockchain data and third-party APIs, designed to answer natural language queries related to blockchain data, token metrics, wallet activity, and social sentiment. The system will leverage both **Manual** and **Automatic ETL** (Extract, Transform, Load) processes to keep data in sync with the blockchain and third-party providers. The chatbot will use a flexible, scalable ETL pipeline to process user queries efficiently, prioritize cached data for speed, and ensure accuracy in reporting.

Examples of user queries include:
- "Extract wallets that bought X tokens and created a trading volume of $200M. Also, provide token market cap, FDV, and social sentiment."
- "Find wallets holding Y tokens and report their transaction history."
- "List all tokens launched in the past 3 months with FDV > $100M and social media activity trends."

To serve these queries efficiently:
- The bot must prioritize locally cached data to minimize latency.
- An ETL (Extract, Transform, Load) pipeline is required to keep local data in sync with blockchains and third-party data providers.
- The system must be modular to handle the increasing complexity of user queries.

This document explores two approaches to achieving this:
- **Manual ETL**: A predefined, static pipeline to fetch, process, and cache data.
- **Automatic ETL**: A dynamic system where transformers and tasks are deployed and scaled based on query requirements.

### Problem Statement  

The primary challenge is enabling users to interact with blockchain data and third-party sources through natural language queries. These queries could be complex, such as analyzing token ownership, transaction volumes, and associated social sentiment.

The solution must:
- Synthesize real-time data from various sources (blockchain and third-party APIs).
- Perform complex data extraction and transformation operations to meet the detailed requirements of the user’s query.
- Optimize response times through caching, while ensuring data accuracy and freshness.
- Enable easy scalability of the data pipeline to incorporate new data sources and meet evolving query needs (adding more data points).

### Solution Overview  

The system architecture comprises the following components:
- **AI Chat Bot**: The front-end user interface that accepts natural language queries and provides responses.
- **Data Layer**: Composed of blockchain nodes, third-party APIs, and locally cached data.
- **ETL Pipeline**: Extracts, transforms, and loads data from the blockchain and third-party providers into a local cache.
- **Query Execution Engine**: Constructs and executes queries based on user input and available data.
- **Data Transformation Layer**: Responsible for transforming raw data into useful information based on user queries.

### System Components and Workflow  

#### AI Chat Bot  

- **Natural Language Understanding (NLU)**: Converts user queries into machine-readable intents and data extraction requirements.
- **Query Handler**: Passes these intents to the appropriate backend system to fetch, transform, and return the necessary data.
- **User Interface**: Displays the query results and allows users to refine their queries interactively.

#### ETL Pipeline Design and Evolution  

The ETL pipeline ensures that data from blockchain nodes and third-party APIs is efficiently extracted, processed, cached, and synchronized to provide actionable insights to the user. To accommodate the evolving nature of user queries and the need for dynamic data points, the ETL pipeline is designed to operate in two phases: short-term (fixed data points) and long-term (dynamic and parameterized data points).

##### Short-term Approach: Fixed Data Points  

In the short term, the ETL pipeline is built around a predefined set of fixed data points, agreed upon during system design. These include common data points such as wallet transactions, token metrics, market cap, FDV, and social sentiment.

- **Pipeline Flow**:  
  - **Source Configuration**: Endpoints are pre-configured, including:
    - Blockchain RPC nodes (e.g., Ethereum, Solana).
    - Third-party APIs for token descriptions, market data, and social trends.
  - **Request Execution**: Data is fetched using predefined API calls or queries tailored to static endpoints.
  - **Transformers**: Static logic processes and filters the data to extract required insights, such as:
    - Aggregating wallet trading volume.
    - Filtering transactions by token type or date range.
    - Computing FDV and market cap.
  - **Storage in Cache**: Transformed data is loaded into a local cache or database for efficient retrieval, ensuring low latency for repetitive queries.

**Example Workflow for Fixed Data Points**

User Query: "Show wallets that traded Token X with a volume > $200M and their FDV."  
- **Data Source**: Fetch wallet transaction history and token metrics from RPC and third-party APIs.
- **Transformation**: Filter wallets with transaction volume > $200M and compute FDV from the token’s circulating supply and price.
- **Response**: Serve processed data from the cache.

##### Long-term Approach: Dynamic and Parameterized Data Points  

As user queries grow in complexity and demand for additional data points arises, the ETL pipeline will evolve into a dynamic and parameterized system. This approach enables the system to adapt by extracting new data points automatically based on user needs, dynamically configuring the pipeline as follows:

- **Parameterized Data Points**: Core Concepts  
  - **Source**: Configurable sources, including:
    - Blockchain Nodes: Dynamically connect to EVM-based chains (Ethereum, Polygon) or non-EVM chains (Solana, Cosmos) based on user requests.
    - Third-party APIs: Dynamically query endpoints for market data, token metadata, or social sentiment.
  - **Request**:  
    - Data Load Configuration: Users specify the amount of data required, e.g., "Fetch last 1,000 transactions."
    - Request types support GraphQL queries, REST API calls, and direct blockchain node interactions.
  - **Transformer**: Customizable Transformers:
    - Filter logic to operate directly on nodes (e.g., filter token transactions by date or wallet).
    - Data extraction logic for specific fields, such as FDV, token price, and wallet activity.
    - Transformers can be deployed dynamically to extract actionable insights in real-time.
  - **Store**: Parameterized storage mechanisms enable:
    - Schema Updates: Add, delete, or modify columns dynamically as new data points are introduced.
    - Deferred Tasks: Schedule updates for large datasets asynchronously to minimize downtime.
    - Migration: Automatically migrate schema changes to maintain consistency across cache and storage systems.

##### Benefits of the Dynamic ETL Approach  

- **Flexibility**: Adapts to new data points and query requirements without major redesigns.
- **Scalability**: Handles large-scale data processing dynamically as data sources and user demands grow.
- **Modularity**: Each component (source, request, transformer, store) is independently configurable, ensuring ease of maintenance and upgrades.
- **Optimized Query Execution**: Queries are constructed and executed dynamically based on user needs, ensuring faster and more precise results.

### Manual ETL Process (Short-Term Solution)  

In the manual ETL setup, we focus on predefined steps to ensure that the required data points are extracted, transformed, and loaded into the system. These steps are executed sequentially, utilizing fixed data sources and static configurations for optimal performance. Here’s how the process is structured:

1. **Data Extraction**  
   The first step involves fetching data from predefined, static endpoints. These endpoints could be:
   - Blockchain Node RPCs: Connecting to a blockchain node to retrieve raw data such as token transactions, wallet activity, or market metrics.
   - Third-party APIs: Fetching data related to social sentiment, news, token descriptions, and other external data points relevant to blockchain analytics.

2. **Transformation**  
   Once the data is extracted, it needs to undergo a transformation process. Predefined transformation logic will be applied based on the data points needed. This could involve:
   - Aggregating data, such as calculating trading volumes from wallet transaction histories.
   - Filtering the data to only include relevant transactions, wallets, or tokens based on user query parameters.
   - Computing necessary values, such as market capitalization, FDV (Fully Diluted Valuation), and social sentiment scores.

3. **Load to Cache**  
   The final step in the manual ETL process involves loading the transformed data into a local cache or database. This allows for fast retrieval of frequently requested data, reducing latency for the AI chatbot. The cache will store pre-aggregated data, filtered results, and computed metrics, which can be retrieved quickly for similar future queries.

### GraphQL Schema and Queries for Data Serving  

To efficiently serve data points to the chatbot, we will create a GraphQL schema that defines the structure and relationships of the data points. This schema will be used to facilitate data retrieval via GraphQL queries.

**GraphQL Schema Example**

```graphql
type Wallet {
  address: String
  transactionHistory: [Transaction]
  tokenMetrics: TokenMetrics
  socialSentiment: SocialSentiment
}

type Transaction {
  timestamp: String
  volume: Float
}

type TokenMetrics {
  marketCap: Float
  fdv: Float
}

type SocialSentiment {
  positiveSentiment: Float
  negativeSentiment: Float
}

type Query {
  wallets(tokenAddress: String!, minVolume: Float!): [Wallet]
}
