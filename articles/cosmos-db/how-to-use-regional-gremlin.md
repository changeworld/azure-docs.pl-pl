---
title: Regionalne punkty końcowe bazy danych programu Azure Cosmos DB Graph
description: Dowiedz się, jak połączyć się z najbliższym punktem końcowym bazy danych Graph dla aplikacji
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/09/2019
ms.openlocfilehash: 7aa1e0aa6bbbee9d40eb0d48318a8e2908a75f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897863"
---
# <a name="regional-endpoints-for-azure-cosmos-db-graph-account"></a>Regionalne punkty końcowe dla konta programu Azure Cosmos DB Graph
Baza danych usługi Azure Cosmos DB Graph jest [dystrybuowana globalnie,](distribute-data-globally.md) dzięki czemu aplikacje mogą używać wielu punktów końcowych odczytu. Aplikacje, które wymagają dostępu do zapisu w wielu lokalizacjach, powinny włączyć funkcje [multi-master.](how-to-multi-master.md)

Powody, dla których warto wybrać więcej niż jeden region:
1. **Skalowalność odczytu w poziomie** — wraz ze wzrostem obciążenia aplikacji może być rozsądne kierowanie ruchu odczytu do różnych regionów platformy Azure.
2. **Mniejsze opóźnienie** — można zmniejszyć obciążenie opóźnienia sieci każdego przechodzenia przez routingu ruchu odczytu i zapisu do najbliższego regionu platformy Azure.

Wymóg **rezydencji danych** jest osiągany przez ustawienie zasad usługi Azure Resource Manager na koncie usługi Cosmos DB. Klient może ograniczyć regiony, do których usługa Cosmos DB replikuje dane.

## <a name="traffic-routing"></a>Routing ruchu

Aparat bazy danych programu Cosmos Db Graph działa w wielu regionach, z których każdy zawiera wiele klastrów. Każdy klaster ma setki maszyn. Konto Graf usługi Cosmos Graph *accountname.gremlin.cosmos.azure.com* jest rozpoznawany jako rekord dns klastra. Pojedynczy adres IP modułu równoważenia obciążenia ukrywa wewnętrzna topologię klastra.

Regionalny rekord CNAME DNS jest tworzony dla każdego regionu konta programu Cosmos DB Graph. Format regionalnego punktu końcowego jest *accountname-region.gremlin.cosmos.azure.com*. Segment regionu regionalnego punktu końcowego uzyskuje się przez usunięcie wszystkich przestrzeni z nazwy [regionu platformy Azure.](https://azure.microsoft.com/global-infrastructure/regions) Na przykład `"East US 2"` region `"contoso"` dla konta globalnej bazy danych będzie miał dns CNAME *contoso-eastus2.gremlin.cosmos.azure.com*

Klient TinkerPop Gremlin jest przeznaczony do pracy z jednym serwerem. Aplikacja może używać globalnego zapisywalny DNS CNAME do odczytu i zapisu ruchu. Aplikacje obsługujące region powinny używać regionalnego punktu końcowego dla ruchu odczytu. Użyj regionalnego punktu końcowego dla ruchu zapisu tylko wtedy, gdy określony region jest skonfigurowany do akceptowania zapisów. 

> [!NOTE]
> Aparat wykresu bazy danych usługi Cosmos można zaakceptować operacji zapisu w regionie odczytu przez proxy ruchu do zapisu regionu. Nie jest zalecane wysyłanie zapisów do regionu tylko do odczytu, ponieważ zwiększa opóźnienie przechodzenia i podlega ograniczeniom w przyszłości.

Nazwa CNAME konta globalnej bazy danych zawsze wskazuje prawidłowy region zapisu. Podczas pracy awaryjnej po stronie serwera regionu zapisu usługa Cosmos DB aktualizuje cname konta globalnej bazy danych, aby wskazać nowy region. Jeśli aplikacja nie może obsłużyć przekierowania ruchu po przełączeniu w błąd, należy użyć globalnego konta bazy danych DNS CNAME.

> [!NOTE]
> Usługa Cosmos DB nie trasy ruchu na podstawie geograficznej bliskości obiektu wywołującego. To do każdej aplikacji, aby wybrać odpowiedni region w zależności od unikatowych potrzeb aplikacji.

## <a name="portal-endpoint-discovery"></a>Odnajdowanie punktu końcowego portalu

Najprostszym sposobem, aby uzyskać listę regionów dla konta programu Azure Cosmos DB Graph jest przegląd bloku w witrynie Azure portal. Będzie działać dla aplikacji, które nie zmieniają regionów często lub mają sposób, aby zaktualizować listę za pomocą konfiguracji aplikacji.

![Pobieranie regionów konta programu Cosmos DB Graph z portalu](./media/how-to-use-regional-gremlin/get-end-point-portal.png )

Przykład poniżej przedstawia ogólne zasady dostępu do regionalnego punktu końcowego Gremlin. Aplikacja powinna wziąć pod uwagę liczbę regionów, aby wysłać ruch do i liczbę odpowiednich klientów Gremlin do wystąpienia.

```csharp
// Example value: Central US, West US and UK West. This can be found in the overview blade of you Azure Cosmos DB Gremlin Account. 
// Look for Write Locations in the overview blade. You can click to copy and paste.
string[] gremlinAccountRegions = new string[] {"Central US", "West US" ,"UK West"};
string gremlinAccountName = "PUT-COSMOSDB-ACCOUNT-NAME-HERE";
string gremlinAccountKey = "PUT-ACCOUNT-KEY-HERE";
string databaseName = "PUT-DATABASE-NAME-HERE";
string graphName = "PUT-GRAPH-NAME-HERE";

foreach (string gremlinAccountRegion in gremlinAccountRegions)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = $"{gremlinAccountName}-{gremlinAccountRegion.ToLowerInvariant().Replace(" ", string.Empty)}.gremlin.cosmos.azure.com";

  GremlinServer regionalGremlinServer = new GremlinServer(
    hostname: regionalGremlinEndPoint, 
    port: 443,
    enableSsl: true,
    username: "/dbs/" + databaseName + "/colls/" + graphName,
    password: gremlinAccountKey);

  GremlinClient regionalGremlinClient = new GremlinClient(
    gremlinServer: regionalGremlinServer,
    graphSONReader: new GraphSON2Reader(),
    graphSONWriter: new GraphSON2Writer(),
    mimeType: GremlinClient.GraphSON2MimeType);
}
```

## <a name="sdk-endpoint-discovery"></a>Odnajdowanie punktu końcowego sdk

Aplikacja może używać [usługi Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) do odnajdywania lokalizacji odczytu i zapisu dla konta Graph. Te lokalizacje mogą ulec zmianie w dowolnym momencie za pomocą ręcznej ponownej konfiguracji po stronie serwera lub automatycznego trybu failover.

TinkerPop Gremlin SDK nie ma interfejsu API do odnajdowania regionów kont bazy danych programu Cosmos Db Graph. Aplikacje, które wymagają odnajdowania punktu końcowego środowiska uruchomieniowego muszą obsługiwać 2 oddzielne sdk w przestrzeni procesu.

```csharp
// Depending on the version and the language of the SDK (.NET vs Java vs Python)
// the API to get readLocations and writeLocations may vary.
IDocumentClient documentClient = new DocumentClient(
    new Uri(cosmosUrl),
    cosmosPrimaryKey,
    connectionPolicy,
    consistencyLevel);

DatabaseAccount databaseAccount = await cosmosClient.GetDatabaseAccountAsync();

IEnumerable<DatabaseAccountLocation> writeLocations = databaseAccount.WritableLocations;
IEnumerable<DatabaseAccountLocation> readLocations = databaseAccount.ReadableLocations;

// Pick write or read locations to construct regional endpoints for.
foreach (string location in readLocations)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = location
    .Replace("http:\/\/", string.Empty)
    .Replace("documents.azure.com:443/", "gremlin.cosmos.azure.com");
  
  // Use code from the previous sample to instantiate Gremlin client.
}
```

## <a name="next-steps"></a>Następne kroki
* [Jak zarządzać kontrolą kont bazy danych](how-to-manage-database-account.md) w usłudze Azure Cosmos DB
* [Wysoka dostępność](high-availability.md) w usłudze Azure Cosmos DB
* [Globalna dystrybucja z usługą Azure Cosmos DB — pod maską](global-dist-under-the-hood.md)
* [Przykłady interfejsu wiersza polecenia platformy Azure](cli-samples.md) dla usługi Azure Cosmos DB
