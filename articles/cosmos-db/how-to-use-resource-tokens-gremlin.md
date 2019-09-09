---
title: Azure Cosmos DB tokeny zasobów z Gremlin
description: Dowiedz się, jak tworzyć tokeny zasobów i korzystać z nich w celu uzyskiwania dostępu do bazy danych programu Graph
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/06/2019
ms.author: olignat
ms.openlocfilehash: fcb18fb14cf787713735da07ca2048d0853fa46c
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806911"
---
# <a name="azure-cosmos-db-resource-tokens-with-gremlin"></a>Azure Cosmos DB tokeny zasobów z Gremlin
W tym artykule wyjaśniono, jak używać [tokenów zasobów Cosmos DB](secure-access-to-data.md) do uzyskiwania dostępu do bazy danych programu Graph za pośrednictwem zestawu SDK Gremlin.

## <a name="create-a-resource-token"></a>Tworzenie tokenu zasobu

TinkerPop Gremlin SDK nie ma interfejsu API do tworzenia tokenów zasobów. Token zasobu jest koncepcją Cosmos DBową. Aby utworzyć tokeny zasobów, Pobierz [zestaw Azure Cosmos DB SDK](sql-api-sdk-dotnet.md). Jeśli aplikacja musi utworzyć tokeny zasobów i używać ich w celu uzyskania dostępu do bazy danych programu Graph, potrzebne są dwa oddzielne zestawy SDK.

Hierarchia modelu obiektów powyżej tokenów zasobów:
- **Konto Cosmos DB** -najwyższego poziomu, z którym jest skojarzony system DNS, na przykład`contoso.gremlin.cosmos.azure.com`
  - **Baza danych Cosmos DB**
    - **Użytkownicy**
      - **Zezwolenie**
        - *Token* -właściwość obiektu **uprawnień** , który oznacza, jakie akcje są dozwolone lub odrzucane.

Token zasobu ma format `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`. Ten ciąg jest nieprzezroczysty dla klientów i powinien być używany bez modyfikacji ani interpretacji.

```csharp
// Notice that document client is created against .NET SDK end-point rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // Token will not be returned during ReadPermissionReedAsync() call.
  // This call will succeed only if database id, user id and permission id already exist. 
  // Note that <database id> is not a database name, it is a base64 string that represents database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Użycie tokenu zasobu
Tokenów zasobów można używać bezpośrednio jako właściwości "Password" podczas konstruowania `GremlinServer` klasy.

```csharp
// Gremlin application needs to be given a resource token. It can't discover the token on its own.
// Token can be obtained for a given permission using Cosmos DB SDK or passed into the application as command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure gremlin servier to use resource token rather than master key
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

Takie samo podejście działa we wszystkich zestawach SDK TinkerPop Gremlin.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Limit

Pojedyncze konto Gremlin może wystawiać nieograniczoną liczbę tokenów, ale tylko do **100** tokenów można używać jednocześnie w ciągu **1 godziny**. Jeśli aplikacja przekracza limit liczby tokenów na godzinę, żądanie uwierzytelnienia zostanie odrzucone z `"Exceeded allowed resource token limit of 100 that can be used concurrently"`komunikatem o błędzie. Zamknięcie aktywnych połączeń z określonymi tokenami w celu zwolnienia miejsc dla nowych tokenów nie będzie rozwijającemu. Cosmos DB aparat bazy danych Gremlin śledzi różne tokeny w ciągu ostatniej godziny przed żądaniem uwierzytelnienia.

## <a name="permission"></a>Uprawnienie

Typowe aplikacje o błędach są dostępne podczas korzystania z `"Insufficient permissions provided in the authorization header for the corresponding request. Please retry with another authorization header."`tokenów zasobów. Ten błąd jest zwracany, gdy przechodzenie Gremlin próbuje zapisać krawędź lub wierzchołek, ale token zasobu `Read` przyznaje tylko uprawnienia. Zbadaj przechodzenie, niezależnie od tego, czy zawiera on `.addV()`następujące `.addE()`kroki `.drop()`:, `.property()`, lub.

## <a name="next-steps"></a>Następne kroki
* [Kontrola dostępu oparta na rolach](role-based-access-control.md) w Azure Cosmos DB
* [Dowiedz się, jak zabezpieczyć dostęp do danych](secure-access-to-data.md) w Azure Cosmos DB
