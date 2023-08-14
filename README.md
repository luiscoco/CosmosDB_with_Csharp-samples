# CosmosDB_with_Csharp-samples

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
````

## Sample 8. 

```csharp
using Microsoft.Azure.Cosmos;

// New instance of CosmosClient class using an endpoint and key string
using CosmosClient client = new(
    accountEndpoint: Environment.GetEnvironmentVariable("COSMOS_ENDPOINT")!,
    authKeyOrResourceToken: Environment.GetEnvironmentVariable("COSMOS_KEY")!
);

// New instance of Database class referencing the server-side database
Database database1 = await client.CreateDatabaseAsync(
    id: "adventureworks-1"
);

// New instance of Database class referencing the server-side database
Database database2 = await client.CreateDatabaseIfNotExistsAsync(
    id: "adventureworks-2"
);

// <create_database_response>
// New instance of Database response class referencing the server-side database
DatabaseResponse response = await client.CreateDatabaseIfNotExistsAsync(
    id: "adventureworks-3"
);
// Parse additional response properties
Database database3 = response.Database;
```


## Sample 9

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

Product item = new(
    id: "68719518388",
    category: "gear-surf-surfboards",
    name: "Sunnox Surfboard",
    quantity: 8,
    sale: true
);

Product createdItem = await container.CreateItemAsync<Product>(
    item: item,
    partitionKey: new PartitionKey("gear-surf-surfboards")
);

Product replacedItem = await container.ReplaceItemAsync<Product>(
    item: item,
    id: "68719518388",
    partitionKey: new PartitionKey("gear-surf-surfboards")
);

Product upsertedItem = await container.UpsertItemAsync<Product>(
    item: item,
    partitionKey: new PartitionKey("gear-surf-surfboards")
);
```

## Sample 10

```csharp
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Cosmos.Linq;

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

// Create new items and add to container
Product firstNewItem = new(
    id: "68719518388",
    category: "gear-surf-surfboards",
    name: "Sunnox Surfboard",
    quantity: 8,
    sale: true
);

Product secondNewitem = new(
    id: "68719518398",
    category: "gear-surf-surfboards",
    name: "Noosa Surfboard",
    quantity: 15,
    sale: false
);

await container.CreateItemAsync<Product>(
    item: firstNewItem,
    partitionKey: new PartitionKey("gear-surf-surfboards")
);

await container.CreateItemAsync<Product>(
    item: secondNewitem,
    partitionKey: new PartitionKey("gear-surf-surfboards")
);

// Query multiple items from container
using FeedIterator<Product> feed = container.GetItemQueryIterator<Product>(
    queryText: "SELECT * FROM products"
);

// Iterate query result pages
while (feed.HasMoreResults)
{
    FeedResponse<Product> response = await feed.ReadNextAsync();

    // Iterate query results
    foreach (Product item in response)
    {
        Console.WriteLine($"Found item:\t{item.name}");
    }
}

// Build query definition
var parameterizedQuery = new QueryDefinition(
    query: "SELECT * FROM products p WHERE p.category = @partitionKey"
)
    .WithParameter("@partitionKey", "gear-surf-surfboards");

// Query multiple items from container
using FeedIterator<Product> filteredFeed = container.GetItemQueryIterator<Product>(
    queryDefinition: parameterizedQuery
);

// Iterate query result pages
while (filteredFeed.HasMoreResults)
{
    FeedResponse<Product> response = await filteredFeed.ReadNextAsync();

    // Iterate query results
    foreach (Product item in response)
    {
        Console.WriteLine($"Found item:\t{item.name}");
    }
}

// Get LINQ IQueryable object
IOrderedQueryable<Product> queryable = container.GetItemLinqQueryable<Product>();

// Construct LINQ query
var matches = queryable
    .Where(p => p.category == "gear-surf-surfboards")
    .Where(p => p.sale == false)
    .Where(p => p.quantity > 10);

// Convert to feed iterator
using FeedIterator<Product> linqFeed = matches.ToFeedIterator();

// Iterate query result pages
while (linqFeed.HasMoreResults)
{
    FeedResponse<Product> response = await linqFeed.ReadNextAsync();

    // Iterate query results
    foreach (Product item in response)
    {
        Console.WriteLine($"Matched item:\t{item.name}");
    }
}
```

## Sample 11

```csharp
using System.Net;
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

// Create new item and add to container
Product firstItem = new(
    id: "68719518388",
    category: "gear-surf-surfboards",
    name: "Sunnox Surfboard",
    quantity: 8,
    sale: true
);

Product secondItem = new(
    id: "68719518381",
    category: "gear-surf-surfboards",
    name: "Kalbar Surfboard",
    quantity: 4,
    sale: false
);

await container.CreateItemAsync<Product>(
    item: firstItem,
    partitionKey: new PartitionKey("gear-surf-surfboards")
);

await container.CreateItemAsync<Product>(
    item: secondItem,
    partitionKey: new PartitionKey("gear-surf-surfboards")
);

// Read existing item from container
Product readItem = await container.ReadItemAsync<Product>(
    id: "68719518388",
    partitionKey: new PartitionKey("gear-surf-surfboards")
);

// Read existing item from container
ItemResponse<Product> readResponse = await container.ReadItemAsync<Product>(
    id: "68719518388",
    partitionKey: new PartitionKey("gear-surf-surfboards")
);

// Get response metadata
double requestUnits = readResponse.RequestCharge;
HttpStatusCode statusCode = readResponse.StatusCode;

// Explicitly get item
Product readItemExplicit = readResponse.Resource;

// Read existing item from container
using ResponseMessage readItemStreamResponse = await container.ReadItemStreamAsync(
    id: "68719518388",
    partitionKey: new PartitionKey("gear-surf-surfboards")
);

// Get stream from response
using StreamReader readItemStreamReader = new(readItemStreamResponse.Content);

// (optional) Get stream content
string content = await readItemStreamReader.ReadToEndAsync();

// Create partition key object
PartitionKey partitionKey = new("gear-surf-surfboards");

// Create list of tuples for each item
List<(string, PartitionKey)> itemsToFind = new()
{
    ("68719518388", partitionKey),
    ("68719518381", partitionKey)
};

// Read multiple items
FeedResponse<Product> feedResponse = await container.ReadManyItemsAsync<Product>(
    items: itemsToFind
);

foreach (Product item in feedResponse)
{
    Console.WriteLine($"Found item:\t{item.name}");
}
```




























