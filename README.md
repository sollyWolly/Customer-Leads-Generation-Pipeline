# Lead Generation Overview
The algorithm consists of several stages, each performing specific tasks to identify and qualify potential leads.
The pipeline consists of the following main stages: Data Retrieval and Enrichment, Scoring, Lead Qualification, Result Processing

### Co-Authors: Solomon Cheung, Dria Lee, Kaiwen Che

![Flowchart](Flowchart%20-%20Frame%201.jpg)

## 1. Data Retrieval and Enrichment

### 1.1 Organization Retrieval
Task: get_organizations
- Fetches organization data from Apollo API
- Implements pagination to retrieve multiple pages of results
- Maximum pages retrieved: APOLLO_SEARCH_MAX_PAGES

### 1.2 Business Information Enrichment
Task: enrich_business_info
- Enriches organization data with additional business information from Apollo API
- Processes organizations in batches (size: APOLLO_BATCH_SIZE)
- Extracts keywords, industries, SEO description, and short description
- Implements batch processing to reduce API calls and improve efficiency

### 1.3 Traffic Information Enrichment
Task: enrich_traffic_info
- Queries Snowflake database for traffic data using SnowflakeQuery class
- Retrieves monthly visits for each organization
- Utilizes batch querying for improved performance

### 1.4 Tech Stack Enrichment
Task: enrich_tech_stack_info
- Enriches organization data with tech stack information from BuiltWith API
- Processes organizations in batches (size: BUILTWITH_BATCH_SIZE)
- Extracts technology names used by each organization
- Implements batch processing to reduce API calls and improve efficiency

## 2. Scoring

### 2.1 Business Information Scoring
Task: scoring_business_info
- Uses BusinessInfoAnalyzer class to score enriched business information
- Calculates similarity scores for keywords, industries, and descriptions using embeddings
- Embedding model: text-embedding-3-large
- Implements caching mechanism for embeddings to improve performance
- Utilizes vectorization techniques for runtime optimization

### 2.2 Traffic Scoring
Task: scoring_traffic_info
- Uses TrafficAnalyzer class to score traffic data
- Calculates percentile rank of organization's traffic compared to existing customers
- Optimized for faster processing of large datasets

### 2.3 Tech Stack Scoring
Task: scoring_tech_stack_info
- Uses TechStackAnalyzer class to score tech stack information
- Calculates similarity score for tech stack using embeddings
- Utilizes vectorization techniques for runtime optimization

## 3. Lead Qualification

### 3.1 Scoring Qualification
Tasks: openai_qualification_with_scores, gemini_qualification_with_scores, claude_qualification_with_scores
- Implements parallel lead qualification using three different LLM models: OpenAI (GPT-4o), Google Gemini 1.5 Flash, Anthropic Claude 3.5 Sonnet
- Each model analyzes scores and determines if the organization is a potential lead
- Models also recommend the most suitable type of salesperson for each lead
- Utilizes concurrent API requests for improved performance

### 3.2 Aggregate Results
Task: qualified_leads_aggregation
- Aggregates results from all three LLM models
- Calculates an average score based on the decisions of all models
- Determines the final decision on whether an organization is a potential lead
- Aggregates salesperson recommendations

## 4. Result Processing
Task: sales_routing
- Compiles all gathered information and qualification results
- Generates a pandas Dataframe with lead information, qualification decisions, and recommendations
- Uploads the rows in the Dataframe to Snowflake for further use by the sales team

## Key Components
- Embedding Analysis: Utilizes OpenAI's text embedding model for semantic similarity calculations in business info and tech stack scoring.
- Traffic Analysis: Uses percentile ranking to evaluate an organization's traffic in relation to existing customers.
- Multi-Model Qualification: Employs three different LLM models to provide diverse perspectives on lead qualification.
- Batch Processing: Implements batch processing for API calls to Apollo and BuiltWith to optimize performance and respect API rate limits.
- Caching: Uses a caching mechanism for embeddings to reduce redundant API calls and improve overall performance.
- Concurrent Processing: Utilizes parallel processing for LLM API calls to reduce overall qualification time.




