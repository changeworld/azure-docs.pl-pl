---
title: Używanie tokenów zasobów usługi Azure Cosmos DB przy użyciu zestawu SDK gremlin
description: Dowiedz się, jak tworzyć tokeny zasobów i używać ich do uzyskiwania dostępu do bazy danych Graph.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 42f3c7f3351bddab429489dccf28587549d76e18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897849"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>Używanie tokenów zasobów usługi Azure Cosmos DB przy użyciu zestawu SDK gremlin

W tym artykule wyjaśniono, jak używać [tokenów zasobów usługi Azure Cosmos DB](secure-access-to-data.md) w celu uzyskania dostępu do bazy danych Graph za pośrednictwem zestawu SDK gremlin.

## <a name="create-a-resource-token"></a>Tworzenie tokenu zasobu

Apache TinkerPop Gremlin SDK nie ma interfejsu API do tworzenia tokenów zasobów. Termin *token zasobu* jest koncepcją usługi Azure Cosmos DB. Aby utworzyć tokeny zasobów, pobierz [zestaw SDK usługi Azure Cosmos DB](sql-api-sdk-dotnet.md). Jeśli aplikacja musi utworzyć tokeny zasobów i używać ich do uzyskania dostępu do bazy danych Graph, wymaga dwóch oddzielnych zestawów SDK.

Hierarchia modelu obiektu powyżej tokenów zasobów jest zilustrowana w następującym konspekcie:

- **Konto usługi Azure Cosmos DB** — jednostka najwyższego poziomu, z `contoso.gremlin.cosmos.azure.com`którą jest skojarzony system DNS (na przykład ).
  - **Baza danych usługi Azure Cosmos DB**
    - **Użytkownik**
      - **Uprawnienie**
        - **Token** — właściwość obiektu permission, która oznacza, jakie akcje są dozwolone lub odrzucone.

Token zasobu używa następującego `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`formatu: . Ten ciąg jest nieprzezroczysty dla klientów i powinien być używany tak jak jest, bez modyfikacji lub interpretacji.

```csharp
// Notice that document client is created against .NET SDK endpoint, rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // The token isn't returned during the ReadPermissionReedAsync() call.
  // The call succeeds only if database id, user id, and permission id already exist. 
  // Note that <database id> is not a database name. It is a base64 string that represents the database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>.
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Używanie tokenu zasobu
Tokeny zasobów można używać bezpośrednio jako właściwości "hasło" podczas konstruowania klasy GremlinServer.

```csharp
// The Gremlin application needs to be given a resource token. It can't discover the token on its own.
// You can obtain the token for a given permission by using the Azure Cosmos DB SDK, or you can pass it into the application as a command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure the Gremlin server to use a resource token rather than a master key.
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

To samo podejście działa we wszystkich SDK TinkerPop Gremlin.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Limit

Z jednym kontem Gremlin, można wydać nieograniczoną liczbę tokenów. Jednak można użyć tylko do 100 tokenów jednocześnie w ciągu 1 godziny. Jeśli aplikacja przekracza limit tokenu na godzinę, żądanie uwierzytelniania zostanie odrzucone i zostanie wyświetlony następujący komunikat o błędzie: "Przekroczony dozwolony limit tokenu zasobu wynoszący 100, który może być używany jednocześnie." Nie działa, aby zamknąć aktywne połączenia, które używają określonych tokenów, aby zwolnić miejsca dla nowych tokenów. Aparat bazy danych usługi Azure Cosmos DB Gremlin śledzi unikatowe tokeny w ciągu godziny bezpośrednio przed żądaniem uwierzytelnienia.

## <a name="permission"></a>Uprawnienie

Typowym błędem, który napotykają aplikacje podczas korzystania z tokenów zasobów, jest "Niewystarczające uprawnienia podane w nagłówku autoryzacji dla odpowiedniego żądania. Ponów próbę przy innym nagłówku autoryzacji." Ten błąd jest zwracany, gdy przechodzenie przez gremlin próbuje napisać krawędzi lub wierzchołka, ale token zasobu udziela tylko uprawnienia *odczytu.* Sprawdź, czy przechodzi się, aby sprawdzić, czy zawiera on którykolwiek z następujących kroków: *.addV()*, *.addE(),* *.drop()* lub *.property()*.

## <a name="next-steps"></a>Następne kroki
* [Kontrola dostępu oparta na rolach](role-based-access-control.md) w usłudze Azure Cosmos DB
* [Dowiedz się, jak zabezpieczyć dostęp do danych](secure-access-to-data.md) w usłudze Azure Cosmos DB
