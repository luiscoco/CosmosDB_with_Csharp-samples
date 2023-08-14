# CosmosDB_with_Csharp-sample2

## Sample 0. Set a connection string

```csharp
using Microsoft.Azure.Cosmos;

// New instance of CosmosClient class using a connection string
using CosmosClient client = new(
    connectionString: Environment.GetEnvironmentVariable("COSMOS_CONNECTION_STRING")!
);
```

## Sample 1. Set the environmental variables

```csharp
using Microsoft.Azure.Cosmos;

using CosmosClient client = new(
    accountEndpoint: Environment.GetEnvironmentVariable("COSMOS_ENDPOINT")!,
    authKeyOrResourceToken: Environment.GetEnvironmentVariable("COSMOS_KEY")!
);

//1. Create a database
Database database = await client.CreateDatabaseIfNotExistsAsync(
    id: "cosmicworks"
);
Console.WriteLine($"New database:\t{database.Id}");

//2. Container reference with creation if it does not already exist
Container container = await database.CreateContainerIfNotExistsAsync(
    id: "products",
    partitionKeyPath: "/categoryId",
    throughput: 400
);
Console.WriteLine($"New container:\t{container.Id}");

//3. Create new object and upsert (create or replace) to container
Product newItem = new(
    id: "70b63682-b93a-4c77-aad2-65501347265f",
    categoryId: "61dba35b-4f02-45c5-b648-c6badc0cbd79",
    categoryName: "gear-surf-surfboards",
    name: "Yamba Surfboard",
    quantity: 12,
    sale: false
);
Product createdItem = await container.CreateItemAsync<Product>(
    item: newItem,
    partitionKey: new PartitionKey("61dba35b-4f02-45c5-b648-c6badc0cbd79")
);
Console.WriteLine($"Created item:\t{createdItem.id}\t[{createdItem.categoryName}]");

//4. Point read item from container using the id and partitionKey
Product readItem = await container.ReadItemAsync<Product>(
    id: "70b63682-b93a-4c77-aad2-65501347265f",
    partitionKey: new PartitionKey("61dba35b-4f02-45c5-b648-c6badc0cbd79")
);

//5. Create query using a SQL string and parameters
var query = new QueryDefinition(
    query: "SELECT * FROM products p WHERE p.categoryId = @categoryId"
)
    .WithParameter("@categoryId", "61dba35b-4f02-45c5-b648-c6badc0cbd79");

using FeedIterator<Product> feed = container.GetItemQueryIterator<Product>(
    queryDefinition: query
);

while (feed.HasMoreResults)
{
    FeedResponse<Product> response = await feed.ReadNextAsync();
    foreach (Product item in response)
    {
        Console.WriteLine($"Found item:\t{item.name}");
    }
}
```

## Sample 2. . Set the environmental variables

```csharp
using Azure.Core;
using Azure.Identity;
using Microsoft.Azure.Cosmos;

// Credential class for testing on a local machine or Azure services
TokenCredential credential = new DefaultAzureCredential();

// New instance of CosmosClient class using a connection string
using CosmosClient client = new(
    accountEndpoint: Environment.GetEnvironmentVariable("COSMOS_ENDPOINT")!,
    tokenCredential: credential
);
```

## Sample 3. Set the environmental variables

```csharp
using Microsoft.Azure.Cosmos;

// New instance of CosmosClient class using an endpoint and key string
using CosmosClient client = new(
    accountEndpoint: Environment.GetEnvironmentVariable("COSMOS_ENDPOINT")!,
    authKeyOrResourceToken: Environment.GetEnvironmentVariable("COSMOS_KEY")!
);
```

## Sample 4. 

```csharp
using Azure.Core;
using Azure.Identity;
using Microsoft.Azure.Cosmos;

// Custom credential class for servers and clients outside of Azure
TokenCredential credential = new ClientSecretCredential(
    tenantId: Environment.GetEnvironmentVariable("AAD_TENANT_ID")!,
    clientId: Environment.GetEnvironmentVariable("AAD_CLIENT_ID")!,
    clientSecret: Environment.GetEnvironmentVariable("AAD_CLIENT_SECRET")!,
    options: new TokenCredentialOptions()
);

// New instance of CosmosClient class using a connection string
using CosmosClient client = new(
    accountEndpoint: Environment.GetEnvironmentVariable("COSMOS_ENDPOINT")!,
    tokenCredential: credential
);
```

## Sample 5. 

```csharp
using Microsoft.Azure.Cosmos;

// New instance of CosmosClient class using an endpoint and key string
using CosmosClient client = new(
    accountEndpoint: Environment.GetEnvironmentVariable("COSMOS_ENDPOINT")!,
    authKeyOrResourceToken: Environment.GetEnvironmentVariable("COSMOS_KEY")!
);

// New instance of Database class referencing the server-side database
Database database = await client.CreateDatabaseIfNotExistsAsync(
    id: "adventureworks"
);

// New instance of Container class referencing the server-side container
Container container = await database.CreateContainerIfNotExistsAsync(
    id: "products",
    partitionKeyPath: "/category",
    throughput: 400
);
```

## Sample 6. 

```csharp
using Microsoft.Azure.Cosmos;

// New instance of CosmosClient class using an endpoint and key string
using CosmosClient client = new(
    accountEndpoint: Environment.GetEnvironmentVariable("COSMOS_ENDPOINT")!,
    authKeyOrResourceToken: Environment.GetEnvironmentVariable("COSMOS_KEY")!
);

// New instance of Database class referencing the server-side database
Database database = await client.CreateDatabaseIfNotExistsAsync(
    id: "adventureworks"
);

// New instance of Container class referencing the server-side container
Container container1 = await database.CreateContainerAsync(
    id: "products-1",
    partitionKeyPath: "/category",
    throughput: 400
);

// New instance of Container class referencing the server-side container
Container container2 = await database.CreateContainerIfNotExistsAsync(
    id: "products-2",
    partitionKeyPath: "/category",
    throughput: 400
);

// New instance of Container class referencing the server-side container
ContainerResponse response = await database.CreateContainerIfNotExistsAsync(
    id: "products-3",
    partitionKeyPath: "/category",
    throughput: 400
);

// Parse additional response properties
Container container3 = response.Container;
```

## Sample 7. 

```csharp

````


































