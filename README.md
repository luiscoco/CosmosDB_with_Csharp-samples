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

## Sample 2


## Sample 3


## Sample 4


## Sample 5








































