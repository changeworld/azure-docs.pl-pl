---
title: Regionalnej pracy awaryjnej w usłudze Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu ręcznego i automatycznego działania trybu failover za pomocą usługi Azure Cosmos DB.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: govindk
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 697be3a1eb07b2f2650f3dd94fd835b9431aec6b
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2018
ms.locfileid: "42056853"
---
# <a name="automatic-regional-failover-for-business-continuity-in-azure-cosmos-db"></a>Automatyczne rozwiązania regionalnej pracy awaryjnej w celu zachowania ciągłości w usłudze Azure Cosmos DB
Usługa Azure Cosmos DB upraszcza globalna dystrybucja danych, oferując w pełni zarządzana [multiregionalne konta baz danych](distribute-data-globally.md) dostarczające wyczyść kompromis między spójnością, dostępnością i wydajnością z odpowiadającego gwarancji. Konta usługi cosmos DB oferuje wysoką dostępność, pojedyncza cyfra ms przy odczycie [dobrze zdefiniowanych poziomów spójności](consistency-levels.md), przezroczyste rozwiązania regionalnej pracy awaryjnej z międzyregionalnymi interfejsami API oraz możliwość elastycznego skalowania przepływności i miejsca do magazynowania WE całym świecie. 

Usługa cosmos DB obsługuje zarówno jawne i zasad opartych na pracy w trybie Failover, umożliwiają kontrolowanie zachowania systemu end-to-end w przypadku awarii. W tym artykule przyjrzymy się:

* Jak działają ręcznej pracy awaryjnej w usłudze Cosmos DB?
* Jak pracy automatycznego przejścia w tryb failover w usłudze Cosmos DB i co się stanie po danych Centrum zbliża się w dół?
* Jak można użyć ręcznej pracy awaryjnej w architekturach aplikacji?

Możesz także dowiedzieć się wywoływania regionalnego trybu failover, w tym filmie pokazano przez Azure Menedżer programu usługi Cosmos DB Andrew Liu, który pokazuje funkcje globalnej dystrybucji, w tym rozwiązania regionalnej pracy awaryjnej.

>[!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

## <a id="ConfigureMultiRegionApplications"></a>Konfigurowanie aplikacji w wielu regionach
Zanim zaczniemy tryby failover przedstawiony sposób konfigurowania aplikacji mogą skorzystać z wielu regionów, dostępności i być odporne na błędy w przypadku wywoływania regionalnego trybu failover.

* Najpierw należy wdrożyć aplikację w wielu regionach
* Aby zapewnić dostępie z małymi opóźnieniami z każdego regionu, aplikacja jest wdrażana, skonfigurować odpowiednie [listy preferowane regiony](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations) dla każdego regionu, przy użyciu jednej z obsługiwanych zestawów SDK.

Poniższy fragment kodu pokazuje, jak zainicjować aplikacji dla wielu regionów. Oto, konto usługi Azure Cosmos DB `contoso.documents.azure.com` skonfigurowano przy użyciu dwóch regionach — zachodnie stany USA i Europa Północna. 

* Aplikacja jest wdrażana w regionie zachodnie stany USA (na przykład przy użyciu usługi Azure App Services) 
* Skonfigurowany z użyciem `West US` jako pierwszy preferowany region w celu uzyskania niskich opóźnień odczytuje
* Skonfigurowany z użyciem `North Europe` jako drugi preferowany region (wysoką dostępność podczas awarii regionalnego)

W interfejsie API SQL ta konfiguracja wygląda podobnie jak poniższy fragment kodu:

```cs
ConnectionPolicy usConnectionPolicy = new ConnectionPolicy 
{ 
    ConnectionMode = ConnectionMode.Direct,
    ConnectionProtocol = Protocol.Tcp
};

usConnectionPolicy.PreferredLocations.Add(LocationNames.WestUS);
usConnectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

DocumentClient usClient = new DocumentClient(
    new Uri("https://contosodb.documents.azure.com"),
    "<Fill your Cosmos DB account's AuthorizationKey>",
    usConnectionPolicy);
```

Aplikacja zostanie również wdrożona w regionie Europa Północna z zamówieniem preferowane regiony odwrócony. Oznacza to, że w regionie Europa Północna jest określony jako pierwszy dla odczytów z małymi opóźnieniami. Następnie regionu zachodnie stany USA jest określony jako drugi preferowany region do wysoką dostępność podczas awarii regionalnego.

Poniższy diagram architektury przedstawia wdrożenia aplikacji w wielu regionach, gdzie Cosmos DB i aplikacji są skonfigurowane jako dostępna w czterech regionach geograficznych platformy Azure.  

![Wdrażanie aplikacji globalnie dystrybuowane za pomocą usługi Azure Cosmos DB](./media/regional-failover/app-deployment.png)

Teraz Przyjrzyjmy się jak usługi Cosmos DB obsługuje regionalnych awarii za pomocą automatycznego przejścia w tryb failover. 

## <a id="AutomaticFailovers"></a>Automatycznego przejścia w tryb failover
W rzadkich Azure regionalnej awarii lub awarii centrum danych usługi Cosmos DB automatyczne wyzwolenie przejścia w tryb failover wszystkich kont usługi Cosmos DB z obecnością w regionie, których to dotyczy. 

**Co się stanie, jeśli region odczytu wystąpiła awaria?**

Konta usługi cosmos DB z regionem odczytu, w jednym z wykorzystywanych regionów są odłączone od ich region zapisu i automatycznie oznaczony w trybie offline. Zestawy SDK Cosmos DB implementuje protokołu odnajdowania regionalnych, który umożliwia im próbę automatycznego wykrycia, gdy region jest dostępny i Przekierowanie odczytu wywołania do następnego dostępnego regionu na liście preferowany region. Jeśli żaden z regionów na liście preferowany region jest dostępny, wywołania automatycznie wrócić do bieżącego regionu zapisu. Żadne zmiany nie są wymagane w kodzie aplikacji do obsługi wywoływania regionalnego trybu failover. Podczas całego procesu gwarancji spójności nadal uznawane przez usługi Cosmos DB.

![Błędy regionów odczytu w usłudze Azure Cosmos DB](./media/regional-failover/read-region-failures.png)

Po dotyczy region odzyskiwania sprawności awarii, wszystkie objęte konta usługi Cosmos DB w regionie są automatycznie odzyskiwane przez usługę. Konta usługi cosmos DB, w których regionów odczytu w regionie, których to dotyczy będzie, a następnie automatycznie synchronizować z bieżącego regionu zapisu i Włącz online. Zestawy SDK usługi DB Cosmos odnajdywanie dostępności nowego regionu i ocenić, czy należy wybrać region jako bieżący region odczytu, na podstawie listy preferowany region skonfigurowany przez aplikację. Dalsze operacje odczytu są przekierowywane do regionu odzyskane bez wprowadzania jakichkolwiek zmian w kodzie aplikacji.

**Co się stanie, jeśli region zapisu wystąpiła awaria?**

Jeśli region dotyczy to bieżącego regionu zapisu i automatycznej pracy awaryjnej jest włączona dla konta usługi Azure Cosmos DB, następnie region jest automatycznie oznaczony jako w trybie offline. Następnie inny region jest podnoszony jako regionu zapisu, dla których to dotyczy konta usługi Azure Cosmos DB. Można włączyć automatyczny tryb failover i w pełni kontrolować kolejność zaznaczania region dla kont usługi Azure Cosmos DB w witrynie Azure portal lub [programowo](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange). 

![Priorytety trybu failover dla usługi Azure Cosmos DB](./media/regional-failover/failover-priorities.png)

Podczas automatycznego przejścia w tryb failover usługi Azure Cosmos DB automatycznie wybiera następnego zapisu region dla danego konta usługi Azure Cosmos DB w kolejności określony priorytet. Aplikacje można użyć właściwości WriteEndpoint klasy DocumentClient wykrył zmiany w regionie zapisu.

![Zakończone niepowodzeniem regionów w usłudze Azure Cosmos DB](./media/regional-failover/write-region-failures.png)

Po dotyczy region odzyskiwania sprawności awarii, wszystkie objęte konta usługi Cosmos DB w regionie są automatycznie odzyskiwane przez usługę. 

* Dane w poprzednim regionie zapisu, który nie był replikowany do regionów odczytu w czasie awarii jest publikowany jako konflikt źródła danych. Aplikacji może odczytywać źródło danych konflikt, rozwiąż konflikty, w oparciu o logikę specyficzną dla aplikacji i zapisywać zaktualizowane dane z powrotem do konta usługi Azure Cosmos DB, zgodnie z potrzebami. 
* W poprzednim regionie zapisu jest tworzony ponownie jako regionu odczytu i w tryb online automatycznie. 
* Możesz ponownie skonfigurować regionem odczytu, który został w tryb online automatycznie jako region zapisu, wykonując ręcznej pracy awaryjnej w witrynie Azure portal lub [programowo](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate).

Poniższy fragment kodu ilustruje sposób przetwarzania konflikty po usterce region odzyskiwania sprawności awarii.

```cs
string conflictsFeedContinuationToken = null;
do
{
    FeedResponse<Conflict> conflictsFeed = client.ReadConflictFeedAsync(collectionLink,
        new FeedOptions { RequestContinuation = conflictsFeedContinuationToken }).Result;

    foreach (Conflict conflict in conflictsFeed)
    {
        Document doc = conflict.GetResource<Document>();
        Console.WriteLine("Conflict record ResourceId = {0} ResourceType= {1}", conflict.ResourceId, conflict.ResourceType);

        // Perform application specific logic to process the conflict record / resource
    }

    conflictsFeedContinuationToken = conflictsFeed.ResponseContinuation;
} while (conflictsFeedContinuationToken != null);
```

## <a id="ManualFailovers"></a>Ręcznej pracy awaryjnej

Oprócz automatycznego przejścia w tryb failover bieżącego regionu zapisu danego konta usługi Cosmos DB można ręcznie zmienić dynamicznie do jednego z istniejących regionów odczytu. Ręcznej pracy awaryjnej można zainicjować za pomocą witryny Azure portal lub [programowo](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate). 

Upewnij się, ręcznej pracy awaryjnej **zero utraty danych** i **zero dostępności** strat i bez problemu zmieniała stanu zapisu transferu ze starego regionu zapisu na nową dla określonego konta usługi Cosmos DB. Podobnie jak w automatycznego przejścia w tryb failover, zestaw SDK Cosmos DB automatycznie obsługuje zmiany region zapisu podczas ręcznej pracy awaryjnej i gwarantuje, że wywołania są automatycznie przekierowywane do nowego regionu zapisu. Nie kodu lub zmiany konfiguracji są wymagane w aplikacji do zarządzania przejścia w tryb failover. 

![Ręcznej pracy awaryjnej w usłudze Azure Cosmos DB](./media/regional-failover/manual-failovers.png)

Typowe scenariusze, w którym ręcznego przełączania trybu failover może być przydatne, należą:

**Postępuj zgodnie z modelem zegara**: Jeśli aplikacje wzorców ruchu przewidywalne, na podstawie czasu dnia, okresowo można zmienić stanu zapisu na najbardziej aktywnych regionu geograficznego na podstawie czasu dnia.

**Aktualizacja usługi**: niektóre wdrożenia globalnie rozproszonych aplikacji mogą obejmować przekierowywanie ruchu do innego regionu za pomocą usługi traffic manager podczas ich aktualizacja planowanej usługi. Takie wdrożenie aplikacji teraz umożliwia Zachowaj stan zapisu w regionie ręcznej pracy awaryjnej w przypadku, gdy będzie aktywny ruch przedziale aktualizacji usługi.

**Ciągłość prowadzenia działalności biznesowej i odzyskiwania po awarii (BCDR) oraz wysokiej dostępności i odzyskiwania awaryjnego (HADR) awarii**: większość aplikacji enterprise zawiera testy ciągłości biznesowej, jako część procesu opracowywania i wydawania. BCDR i testowania HADR często jest ważnym krokiem związane ze zgodnością i gwarantujące dostępność usług w przypadku regionalnej awarii. Możesz przetestować gotowości BCDR aplikacji korzystających z usługi Cosmos DB do przechowywania wyzwalanie ręczne przełączenie konta usługi Cosmos DB i/lub dodając i usuwając region, dynamicznie.

W tym artykule zapoznaliśmy się, jak ręczne i automatyczne pracy przejścia w tryb failover w usłudze Cosmos DB i jak można skonfigurować do dostępnie aplikacji i kont usługi Cosmos DB, na których. Korzystając z obsługi globalnej replikacji usługi Cosmos DB, możesz Zmniejszaj opóźnienia end-to-end i zapewnienia ich wysokiej dostępności nawet w przypadku awarii region. 

## <a id="NextSteps"></a>Następne kroki
* Dowiedz się więcej o sposobie obsługi usługi Cosmos DB [dystrybucja globalna](distribute-data-globally.md)
* Dowiedz się więcej o [globalnego spójności za pomocą usługi Azure Cosmos DB](consistency-levels.md)
* Programowanie z użyciem wielu regionów za pomocą usługi Azure Cosmos DB [interfejsu API SQL](tutorial-global-distribution-sql-api.md)
* Dowiedz się, jak tworzyć [architektury zapisywania wielu regionów](multi-region-writers.md) za pomocą usługi Azure Cosmos DB

