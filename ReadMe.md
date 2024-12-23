# RAG Chatbot with Knowledge Graph

## Contents:

- [Install Prerequisites](#-pre-requisites-start-here-)
  - [Install the Dependencies](#pre-requisite-1-install-the-dependencies)
  - [Pull Llama3.1 via Ollama](#pre-requisite-2-run-llama31-via-ollama)
  - [Neo4j Desktop Setup](#pre-requisite-3-neo4j-desktop-setup)
- [Exercise-2 Execution Steps](#excercise-execution)
- [Sample Data File Description for the Knowledge Graph](#sample-data-file-description-for-the-knowledge-graph)

## ** Pre-requisites start here **

### Pre-requisite 1: Install the Dependencies:

- langchain
- langchain_community
- langchain_experimental
- langchain_ollama
- langchain_core
- gradio [About Gradio](https://www.gradio.app/guides/the-interface-class)

```bash
pip3 install langchain langchain_community langchain_experimental langchain_ollama langchain_core gradio
```

### Pre-requisite 2: Run Llama3.1 via Ollama

We can run a compact model - Llama3.1 8b

```
ollama pull llama3.1:8b
```

### Pre-requisite 3: Neo4j Desktop Setup

The step-by-step process for installing and setting up Neo4j Desktop for this exercise are listed below.
You can also watch this Neo4j Installation Guide Video for more reference: [Neo4j Installation Guide Video](https://youtu.be/pPhJi9twN9Q?si=rzfvD6OWd33VF84C)

#### STEP-1: Installation and Local Setup of Neo4j Desktop Setup

1.1. Download the latest free version of Neo4j Desktop from here: https://neo4j.com/download/.

Fill the required form with basic details on the website to start the download.

<div> 
    <img src="./images/1.png" alt="download neo4j desktop" width="50%"/>
</div>
<br/>

You can also view the various installation options for Neo4j on the [Neo4j Deployment Center](https://neo4j.com/deployment-center/?desktop-gdb)

1.2. Copy the Neo4j Desktop Activation Key shown in the browser after downloading the Neo4j Desktop app.

<div> 
    <img src="./images/2.png" alt="neo4j desktop activation key step" width="50%"/>
</div>
<br/>

1.3. Open the downloaded .dmg installer file on your local for installing Neo4j Desktop.

<div> 
    <img src="./images/3.png" alt="open the downloaded .dmg installer file" width="50%"/>
</div>
<br/>

1.4. Paste the Neo4j Desktop Activation Key when asked during the installation steps.

<div> 
    <img src="./images/4.png" alt="paste the neo4j desktop activation key" width="50%"/>
</div>
<br/>

<div> 
    <img src="./images/5.png" alt="click on activate" width="50%"/>
</div>
<br/>

1.5. Wait for few seconds or minutes for the installation to get completed.

<div> 
    <img src="./images/6.png" alt="wait for installation completion" width="50%"/>
</div>
<br/>

#### STEP-2: Create a new Project and local DBMS in Neo4j Desktop

2.1. Create a new project in Neo4j Desktop.

<div> 
    <img src="./images/7.png" alt="create new project" width="50%"/>
</div>
<br/>

2.2. Create a local DBMS (5.20.x) named Graph DBMS in this new project. Set the password and remember it.

<div> 
    <img src="./images/8.png" alt="local DBMS setup" width="50%"/>
</div>
<br/>

2.3. Click on Start the DBMS. It should start showing Active status once started along with the neo4j (default) database instance inside this DBMS.

<div> 
    <img src="./images/9.png" alt="start DBMS" width="50%"/>
</div>

#### STEP-3: Install the required plugins

3.1. Click on the DBMS to open the right side panel. Click on the Plugins tab in the right side panel in Neo4j Desktop app.

3.2. Install the APOC (Awesome Procedures on Cypher) plugin. The [APOC library](https://neo4j.com/docs/apoc/5/overview/) consists of many functions to help with various different tasks in areas like collections manipulation, graph algorithms, and data conversion.

<div> 
    <img src="./images/10.png" alt="connect to neo4j server" width="50%"/>
</div>
<br/>

3.3. Install the GDS (Graph Data Science) plugin. The [Neo4j Graph Data Science (GDS) library](https://neo4j.com/docs/graph-data-science/current/?ref=desktop) provides extensive analytical capabilities centered around graph algorithms.

<div> 
    <img src="./images/11.png" alt="connect to neo4j server" width="50%"/>
</div>
<br/>

#### STEP-4: Connecting to the DBMS in the Graph Data Science Playground

4.1. Now, click on the Graph Apps option in the left side panel in the Neo4j Desktop app.

4.2. Click on the Graph Data Science Playground to launch it.

4.3. The Graph Data Science Playground (NEuler) will be launched in a new window. It is a project of Neo4j Labs and is an excellent way to explore smaller graphs.

4.4. Ensure all checks are passing and connect to the local neo4j server.

<div> 
    <img src="./images/12.png" alt="connect to neo4j server" width="50%"/>
</div>
<br/>

4.5. Select the pre-selected neo4j (default) database instance

<div> 
    <img src="./images/13.png" alt="select neo4j default instance" width="50%"/>
</div>
<br/>

4.6. Kudos, the local Neo4j Database Connection is done, you're good to go ahead.

<div> 
    <img src="./images/14.png" alt="select neo4j default instance" width="50%"/>
</div>
<br/>

## ** Pre-requisites ends here **

# Excercise execution

## RAG Chatbot with Langchain, LLM, and knowledge graph

We will build a RAG chatbot - this time we will add a knowledge graph to enable a more intelligent chatbot, which can answer more complex user questions.

## Architecture

<div> 
    <img src="./images/example2-diagram.png" width="50%"/>
</div>
<br/>

**Steps:**

### Step 1: Initialize Neo4j

- Install Neo4j Desktop using above installation steps and replace the password value in code.

- Create Neo4j database wrapper for performing the graph operations.
  [About Neo4jGraph](https://api.python.langchain.com/en/latest/graphs/langchain_community.graphs.neo4j_graph.Neo4jGraph.html)

```
    graph = Neo4jGraph()
```

### Step 2: Convert textual documents into graph-based documents using LLMGraphTransformer

- Create an instance of LLMGraphTransformer, it converts textual documents into graph-based documents using LLM.
  [About LLMGraphTransformer](https://api.python.langchain.com/en/latest/graph_transformers/langchain_experimental.graph_transformers.llm.LLMGraphTransformer.html)

  It includes passing below parameters:

  - llm: Pass the Llama3 instance running using Ollama
  - allowed_nodes: Specifies which node types are allowed in the graph. Defaults to an empty list, allowing all node types.
  - allowed_relationships: Specifies which relationship types are allowed in the graph. Defaults to an empty list, allowing all relationship types.

```
    llm_transformer = LLMGraphTransformer(
        llm=llm,
        allowed_nodes=["Company", "Person", "Product", "Supplier", "Warehouse", "Store", "Shipment", "Customer", "External Factor", "Supply Chain", "Location", "Weather Condition", "Holiday", "Region", "Sales Trend"],
        allowed_relationships=["WORKS_FOR", "CREATES", "SUPPLIES", "STOCKS", "DELIVERS_TO", "LOCATED_AT", "AFFECTED_BY", "AFFECTS", "MANAGES", "CONTAINS", "HAS_CONTRACT_WITH", "HAS_INVENTORY", "INFLUENCES" ],
    )
```

- Extract graph data by converting a sequence of documents into graph documents.
  [About LLMGraphTransformer.convert_to_graph_documents()](https://api.python.langchain.com/en/latest/graph_transformers/langchain_experimental.graph_transformers.llm.LLMGraphTransformer.html#langchain_experimental.graph_transformers.llm.LLMGraphTransformer.convert_to_graph_documents)

```
   graph_documents = llm_transformer.convert_to_graph_documents(documents)
   print("graph documents", graph_documents)
```

### Step-3: Store to neo4j

- Store to neo4j using add_graph_documents(), this method constructs nodes and relationships in the graph based on the provided GraphDocument objects.
  [About Neo4jGraph add_graph_documents()](https://api.python.langchain.com/en/latest/graphs/langchain_community.graphs.neo4j_graph.Neo4jGraph.html#langchain_community.graphs.neo4j_graph.Neo4jGraph.add_graph_documents)

```
   graph.add_graph_documents(
      graph_documents,
      baseEntityLabel=True,
      include_source=True
   )
   print("Documents successfully added to Graph DataBase")
```

### Step-4: Initialize and return a Neo4jVector instance from existing graph using OllamaEmbeddings

- Instantiate the Ollama embedding model and generate embeddings using the locally running LLaMA3.1-8b
  [About OllamaEmbeddings](https://python.langchain.com/api_reference/ollama/embeddings/langchain_ollama.embeddings.OllamaEmbeddings.html)

```
    local_embeddings = OllamaEmbeddings(model="llama3.1:8b")
```

- Initialize and return a Neo4jVector instance from existing graph. This method initializes and returns a Neo4jVector instance using the provided parameters and the existing graph. It validates the existence of the indices and creates new ones if they don’t exist.
  [About Neo4jVector.from_existing_graph()](https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.neo4j_vector.Neo4jVector.html#langchain_community.vectorstores.neo4j_vector.Neo4jVector.from_existing_graph)

```
   vector_index = Neo4jVector.from_existing_graph(
      embedding=local_embeddings,
      search_type="hybrid",
      node_label="Document",
      text_node_properties=["text"],
      embedding_node_property="embedding"
   )
```

### Step-5: Create a RetrievalQA chain for question-answering

- Create custom prompt template. A prompt template consists of a string template, it can contain the system prompt, human question and context.

```
   template = """Use the following pieces of context to answer the question at the end.
   If you don't know the answer, just say that you don't know, don't try to make up an answer.
   Use three sentences maximum and keep the answer as concise as possible.
   Always say "thanks for asking!" at the end of the answer.
   {context}
   Question: {question}
   Helpful Answer:"""
```

- Create a PromptTemplate instance with the custom prompt template for the language model.
  [About PromptTemplate](https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.prompt.PromptTemplate.html)

```
   QA_CHAIN_PROMPT = PromptTemplate(input_variables=["context", "question"], template=template)
```

- Create a RetrievalQA Chain for question-answering against an index using the Neo4jVector as the retriever.
  [About RetrievalQA](https://api.python.langchain.com/en/latest/chains/langchain.chains.retrieval_qa.base.RetrievalQA.html#)

```
   qa_chain = RetrievalQA.from_chain_type(
      llm,
      retriever=vector_index.as_retriever(),
      chain_type_kwargs={"prompt": QA_CHAIN_PROMPT}
   )
```

### Step-6: Execute the Exercise-2 code

Run the below command to execute the exercise-2 code.

```
    python3 knowledge-graph-rag.py
```

### Final output

<div> 
    <img src="./images/kg_output.png" width="60%"/>
</div>
<br/>

### Knowledge Graph Visualization

In Neo4j Browser, you can use the below Cypher Query to view the visualization for the graph data for this exercise:

```
   MATCH (s)-[r:!MENTIONS]->(t) RETURN s,r,t LIMIT 30
```

Neo4j Graph Visualization Output Example Screenshot:

<div> 
    <img src="./images/neo4j-knowledge-graph-visualization.png" width="60%"/>
</div>
<br/>

## Sample Data File Description for the Knowledge Graph

Here’s an explanation of the entities and relationships for the Lumina knowledge graph:

### _Entities:_

1. _Company_

   - _Lumina_: The central entity in the graph, representing the startup that operates in various sectors including supply chain management, customer service, and product sales.

2. _Person_

   - _Anna_: A logistics manager at Lumina, responsible for overseeing the supply chain and supplier relationships.
   - _Carlos_: A store manager at Lumina, known for managing the Downtown Store and overseeing the Central Warehouse.

3. _Supplier_

   - _EcoSupplies_: A supplier that provides products to Lumina, known for its reliability and high contract value.
   - _GreenTech_: A supplier that experiences frequent delays, primarily due to adverse weather conditions.

4. _Warehouse_

   - _North Warehouse_: An efficient warehouse known for effective stocking and delivery.
   - _South Warehouse_: A warehouse that struggles with stockouts, particularly for specific products.
   - _Midwest Warehouse_: A warehouse often affected by snowstorms, impacting delivery times.
   - _Central Warehouse_: Managed by Carlos and known for high efficiency in stocking.
   - _East Coast Warehouse_: A warehouse frequently disrupted by hurricanes, affecting operations.

5. _Store_

   - _Downtown Store_: A high-performing store with the highest customer satisfaction rating, managed by Carlos.
   - _Uptown Store_: A store located in a busy urban area, known for high sales and efficient inventory turnover.

6. _Product_

   - _EcoBag_: A product frequently running out of stock, especially at the South Warehouse.
   - _GreenBottle_: Another product that often experiences stockouts, influenced by supply chain issues.
   - _SolarLantern_: A top-selling product during holiday seasons.

7. _Shipment/Delivery_

   - _Deliveries by EcoSupplies_: Shipments made by EcoSupplies to Lumina.
   - _Deliveries by GreenTech_: Shipments made by GreenTech to Lumina.
   - _Deliveries to North Warehouse_: Shipments arriving at the North Warehouse.
   - _Deliveries to South Warehouse_: Shipments arriving at the South Warehouse.

8. _Customer_

   - _Customers at the Downtown Store_: Shoppers who frequent the Downtown Store, contributing to its high satisfaction rating.
   - _Customers at the Uptown Store_: Shoppers at the Uptown Store, influencing its sales performance.

9. _External Factors_
   - _Weather_: Includes conditions like snowstorms and hurricanes that affect delivery times and warehouse operations.
   - _Holidays_: Special days such as Christmas and Thanksgiving that impact sales trends and inventory needs.
   - _Economic Events_: Broader economic conditions that influence sales trends.

### _Relationships:_

1. _Works At_

   - _Anna → Lumina_: Anna is employed by Lumina.
   - _Carlos → Lumina_: Carlos works for Lumina.

2. _Creates_

   - _Lumina → Products (EcoBag, GreenBottle, SolarLantern)_: Lumina is the creator and distributor of these products.

3. _Supplies_

   - _EcoSupplies → Lumina_: EcoSupplies supplies products to Lumina.
   - _GreenTech → Lumina_: GreenTech also supplies products to Lumina, though with occasional delays.

4. _Stocks_

   - _North Warehouse → Products (EcoBag, GreenBottle, SolarLantern)_: The North Warehouse stocks these products.
   - _South Warehouse → Products (EcoBag, GreenBottle)_: The South Warehouse stocks these products but experiences stockouts.

5. _Delivers To_

   - _EcoSupplies → North Warehouse_: EcoSupplies delivers products to the North Warehouse.
   - _GreenTech → South Warehouse_: GreenTech delivers products to the South Warehouse.

6. _Located At_

   - _North Warehouse → Midwest Region_: The North Warehouse is located in the Midwest.
   - _South Warehouse → Southeast Region_: The South Warehouse is located in the Southeast.
   - _Downtown Store → Urban Area_: The Downtown Store is situated in a high-foot-traffic urban area.
   - _Uptown Store → Urban Area_: The Uptown Store is also in a busy urban area.

7. _Affects_

   - _Weather (Snowstorms) → Midwest Warehouse Deliveries_: Snowstorms affect the delivery times for the Midwest Warehouse.
   - _Weather (Hurricanes) → East Coast Warehouse Operations_: Hurricanes disrupt operations at the East Coast Warehouse.

8. _Manages_

   - _Carlos → Downtown Store_: Carlos manages the Downtown Store.
   - _Carlos → Central Warehouse_: Carlos oversees operations at the Central Warehouse.

9. _Contains_

   - _Warehouses → Products (EcoBag, GreenBottle, SolarLantern)_: Various warehouses hold and manage inventory of these products.

10. _Has Contract With_

    - _Lumina → EcoSupplies_: Lumina has a contract with EcoSupplies.
    - _Lumina → GreenTech_: Lumina also has a contract with GreenTech, though it has been renegotiated due to delays.

11. _Has Inventory_

    - _Downtown Store → EcoBag_: The Downtown Store keeps EcoBag in stock.
    - _Uptown Store → SolarLantern_: The Uptown Store stocks SolarLantern.

12. _Influences_
    - _Holidays → Sales Trends_: Holidays influence sales trends, leading to increased sales during Christmas and Thanksgiving.
    - _Economic Events → Sales Trends_: Economic conditions affect overall sales trends.

These entities and relationships help build a comprehensive knowledge graph for Lumina, allowing you to analyze and visualize the company’s operations, supply chain, customer interactions, and external influences.

### Example Questions

Here are the questions based on the Neo4j queries, formatted with question marks:

1. _Who is the most reliable supplier for Lumina?_

2. _Which products are frequently out of stock at Lumina’s warehouses?_

3. _How does weather impact delivery times to Lumina’s warehouses?_

4. _Which warehouse is the most efficient at Lumina?_

5. _Which supplier has the highest contract value with Lumina?_

6. _Which store at Lumina has the highest customer satisfaction rating?_

7. _How do holiday seasons influence sales at Lumina’s stores?_

8. _Which products are purchased the most during external events like holidays?_

9. _How do inventory levels affect customer satisfaction at Lumina’s stores?_

10. _Which products have the highest sales during peak seasons like holidays?_

11. _Who is the most efficient manager at Lumina based on inventory turnover rates?_

12. _Which warehouses at Lumina are disrupted by weather conditions?_

13. _How does the location of a store impact its performance at Lumina?_

14. _How do suppliers impact product availability at Lumina’s warehouses?_

15. _Which products are most impacted by delays and weather conditions at Lumina?_

16. _Which regions are considered high-potential areas for opening new Lumina stores?_

17. _What external factors most influence sales trends at Lumina?_

18. _Which products should be stocked the most during holiday seasons at Lumina?_

19. _What are the common causes of delivery delays to Lumina’s warehouses?_

20. _Which suppliers should Lumina renegotiate contracts with due to issues?_

### Key Points

**What is RAG?**

Retrieval-Augmented Generation (RAG) is a method that combines a knowledge retrieval system with a generative language model to create more informed, accurate, and context-aware responses. 
Here’s a simple breakdown of how it works, followed by an example.

How RAG Works
**Retrieval Step:** When a question is asked, the system first searches for relevant information (documents, snippets, data) from an external knowledge base, such as Wikipedia, company documents, or a custom database.
**Augmentation Step:** The retrieved information is then fed into a generative language model (like GPT) that uses it to generate an informed and relevant response.
Example of RAG: Imagine a customer support chatbot that helps users troubleshoot issues with their devices.

**Without RAG**
The chatbot might generate responses based only on general patterns it has learned. If a customer asks, "How do I reset my Model-X Wifi router?", 
the chatbot might generate a generic answer about router resetting without specific instructions for the Model-X Wifi router.

**With RAG**
Using RAG, the chatbot first retrieves specific documents from the company’s knowledge base with instructions for resetting the "Model-X Pro router".
Then, it uses this information to generate a customized response that is accurate and specific to the user's device. 
So, instead of a general response, the chatbot might say:

“To reset your Model-X Wifi router, hold down the reset button on the back for 10 seconds until the light flashes red. Make sure the device is plugged in throughout the process.”

**Advantages of RAG**
**Enhanced Accuracy:** By pulling specific data from a knowledge base, RAG can provide more accurate answers.
**Up-to-Date Information:** RAG can pull from constantly updated databases, making it easier to stay current.
**Cost Efficiency:** For large language models, retrieving precise data rather than relying on all-purpose training data saves resources.
**Improved User Satisfaction:** Because responses are specific and accurate, user satisfaction typically increases, especially in customer service applications.

In summary, RAG allows models to generate more accurate and context-relevant answers, as they can “look up” information before responding. 
This is especially valuable in dynamic or highly specialized areas where accuracy is key.

**How Knowledge Graph helps to improve RAG?**

A Knowledge Graph enhances the Retrieval-Augmented Generation (RAG) system by organizing and structuring information in a way that makes the retrieval process faster, more accurate, and contextually richer. 
Here’s how it helps improve RAG and why this is beneficial.

**How Knowledge Graph Enhances RAG**

**Organized and Contextualized Data:**

A knowledge graph organizes information in a network of entities (like people, places, events, or concepts) and defines relationships between them. This structure allows the RAG model to understand and retrieve information based on how different entities relate to each other, 
improving the relevance of retrieved data.

**Enhanced Precision in Retrieval:**

In traditional retrieval systems, documents are fetched mainly by keyword matching, which can sometimes lead to irrelevant or overly broad results. A knowledge graph, however, enables retrieval based on specific relationships and entity connections. 
This leads to more precise and contextually relevant results that the generative model can use.

**Disambiguation of Queries:**

Knowledge graphs help disambiguate entities in a query. For example, if someone asks about "Apple," the knowledge graph can distinguish between "Apple" as a fruit and "Apple Inc." as a company by looking at the context provided by the query. 
This helps the RAG model retrieve the correct data and reduces the chance of errors.

**Enrichment of Retrieved Information:**

Knowledge graphs contain rich metadata and relationships, providing context around each piece of information. 
For instance, if a user asks about "renewable energy sources," the knowledge graph might not only retrieve the definition but also related topics like "solar power," "wind energy," and "climate change policies," which the generative model can use to create a more informed response.

**Scalability and Adaptability:**

Knowledge graphs are scalable and can be updated or expanded with new information, making it easy to add emerging knowledge without retraining the entire model. 
This flexibility allows RAG systems to stay current and relevant in fields with frequently updated information, like healthcare or technology.

**Example: Knowledge Graph in Action in RAG**

Imagine a medical RAG system where a user asks, “What are the treatment options for Type 2 Diabetes?”

**With a knowledge graph:**

**Relevant Connections:** The system retrieves information directly connected to Type 2 Diabetes, including "medication options," "lifestyle changes," and "risk factors," as well as related entities like "insulin resistance" and "dietary management."
**Contextual Insights:** It adds context by retrieving relationships, such as "medications effective for Type 2 Diabetes," and related concepts like "side effects" and "long-term efficacy."
**Disambiguation:** If there’s another medical term or condition with a similar name, the knowledge graph ensures that only data relevant to Type 2 Diabetes is retrieved.
The generative model then uses this structured, contextually rich information to create a comprehensive answer.

**Advantages of Using a Knowledge Graph in RAG**
**Higher Relevance:** Knowledge graphs provide better-structured information, enhancing the RAG system's ability to retrieve and generate highly relevant responses.
**Reduced Ambiguity:** Queries are interpreted more accurately, reducing the chance of confusing similar terms or concepts.
**Broader Context:** By pulling in related entities, knowledge graphs allow RAG to provide broader, more comprehensive answers.
**Easy Updates:** Adding or updating information in a knowledge graph is straightforward, keeping RAG responses accurate and up-to-date.

**Summary**
A knowledge graph enriches the RAG system by adding structured, contextualized, and disambiguated data, resulting in responses that are more accurate, relevant, and comprehensive. 
It’s a powerful addition that allows RAG to handle complex queries effectively, especially in specialized or rapidly changing fields.
