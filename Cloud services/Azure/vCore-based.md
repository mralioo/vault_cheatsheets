

Explore vCore-based Azure Cosmos DB for MongoDB, covering everything from basic concepts to initial setup. Understand the cost and scalability aspects, and how this service integrates with Azure for application development.

## Learning objectives

- Learn the vCore-based Azure Cosmos DB for MongoDB concepts.
- Learn how to set up a vCore-based Azure Cosmos DB for MongoDB cluster.



vCore-based Azure Cosmos DB for MongoDB is a **fully managed MongoDB-compatible database service**, offering seamless Azure integration, and a low total cost of ownership.
vCore-based Azure Cosmos DB for MongoDB is perfect for developers planning to migrate existing applications or build new ones. vCore-based Azure Cosmos DB for MongoDB provides a familiar architecture along with the scalability and flexibility Azure is known for.


In this module, you learned how to set up a vCore-based Azure Cosmos DB for MongoDB cluster. This setup is essential for advanced MongoDB applications that require high scalability, complex query processing, and high availability. The module covered the key advantages of using a vCore-based cluster. These advantages include: the ease of migration for existing MongoDB workloads, suitability for complex workloads, robust scalability with various vCore tiers, and support for high availability with 99.995% uptime. Additionally, it highlighted the native support for vector embeddings, enhancing AI-enhanced applications.

The main takeaways include understanding the compatibility of vCore-based Azure Cosmos DB with MongoDB, which allows seamless integration and use of familiar tools and SDKs. You also learned the requirements and methods for creating your cluster, either through the Azure portal or using Azure CLI commands. The module provided an example CLI command and a Bicep template for deploying a vCore-based Azure Cosmos DB for MongoDB cluster, demonstrating the practical steps involved in setting up the cluster.

## Migration 

Explore migrating to vCore-based Azure Cosmos DB for MongoDB, utilizing Azure Data Studio, MongoDB migration tools, and Azure Databricks for data migration.

## Learning objectives

- Plan and assess the readiness of a migration from MongoDB to vCore-based Azure Cosmos DB for MongoDB.
- Use Azure Data Studio for migration assessment and execution.
- Employing native MongoDB tools for efficient data transfer.
- Use advanced migration techniques with Azure Databricks and Azure Database Migration Service.



# Building App

Enhance your application's data retrieval by combining Azure OpenAI with vCore-based Azure Cosmos DB for MongoDB. You learn how to make your searches smarter and your answers more relevant using vector databases, embeddings, and Retrieval Augmentation Generation (RAG) systems. This technology combination lets you take full advantage of Azure OpenAI's capabilities and vCore-based Azure Cosmos DB for MongoDB's robust data management.

The topics covered in this module include:

- Implementing vector indexes and creating embeddings with Azure OpenAI.
- Enhancing search functionalities using vector-based querying.
- Integrating RAG systems to produce accurate and relevant responses.

By the end of this module, you have the skills to effectively utilize Azure OpenAI within your vCore-based Azure Cosmos DB for MongoDB applications, ensuring they not only perform efficiently but also deliver contextually enhanced responses.


The Azure OpenAI Service offers API access to advanced language models such as GPT-4, GPT-4 Turbo with Vision, GPT-3.5-Turbo, and the Embeddings series. These models are flexible, supporting tasks like content generation, summarization, and image understanding. You can access the models through REST APIs, language specific SDKs, or directly via the Azure OpenAI Studio.


### Vector database 

Vector databases, embeddings, and Retrieval Augmentation Generation (RAG) are crucial technologies for enhancing AI-driven applications, particularly in the context of vCore-based Azure Cosmos DB for MongoDB. Understanding these concepts is essential for implementing advanced functionalities such as vector search querying your own data.


## Explore Retrieval Augmented Generation (RAG)

RAG systems improve large language models by adding an information retrieval system. This system grounds AI responses in relevant, specific data, such as vectorized documents and images created from the data in your enterprise.

A RAG system using vector databases typically follows these steps:

1. Embeds the input question or query and retrieves the relevant data from a vector database.
2. Generates a prompt including the initial input and the data retrieved to provide context.
3. Queries the generative AI model with the combined prompt to generate a response.

![Diagram of a Retrieval Augmentation Generation system.](https://learn.microsoft.com/en-us/training/wwl-data-ai/build-ai-copilot-vcore-based-azure-cosmos-db-mongodb/media/3-retrieval-augmentation-generation.png)

[ref](https://learn.microsoft.com/en-us/training/modules/build-ai-copilot-vcore-based-azure-cosmos-db-mongodb/4-implement-retrieval-augmentation-generation-vcore-based-cosmos-db-mongodb)

This process first requires configuring your vCore-based Azure Cosmos DB for MongoDB to support vector search. This configuration involves three main steps:

1. Add vector fields to your documents to store their text data embeddings.
2. Generate embeddings from your document's text data and store them in the vector fields.
3. Create vector indexes for these vector fields.