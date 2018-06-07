---
title: Regionalnej pracy awaryjnej w usłudze Azure DB rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobie ręcznego i automatycznego działania trybu failover z bazy danych Azure rozwiązania Cosmos.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 947ecb2e6cd122ad98429db93e43b2b5c57744b7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34614003"
---
# <a name="automatic-regional-failover-for-business-continuity-in-azure-cosmos-db"></a>Automatyczne regionalnej pracy w trybie failover ciągłość prowadzenia działalności biznesowej w usłudze Azure DB rozwiązania Cosmos
Azure DB rozwiązania Cosmos upraszcza globalne rozkład danych oferując do pełnego zarządzania, [konta bazy danych w przypadku](distribute-data-globally.md) zawierających wyczyść wady i zalety między spójności, dostępność i wydajność, wszystkie z odpowiednimi gwarancje. Rozwiązania cosmos DB kont oferują wysoką dostępność, opóźnienia ms w pojedynczą cyfrą [dobrze zdefiniowane poziomy spójności](consistency-levels.md), przezroczysty regionalnej pracy awaryjnej z wielu interfejsów API oraz możliwość elastycznie skalować przepływność i Magazyn między globu. 

Rozwiązania cosmos bazy danych obsługuje zarówno jawnego i zasad zmiennych praca awaryjna które umożliwiają kontrolowanie zachowania systemu end-to-end w przypadku niepowodzenia. W tym artykule opisano:

* Jak działają ręcznej pracy awaryjnej w bazie danych rozwiązania Cosmos
* Jak pracy automatycznego przechodzenia w tryb failover do rozwiązania Cosmos bazy danych i co się dzieje, gdy dane Centrum umieszczane w dół?
* Jak można użyć ręcznej pracy awaryjnej w architekturach aplikacji?

Można także zapoznać się regionalnej pracy w trybie Failover w tym wideo według Andrew Liu Menedżera programów rozwiązania Cosmos bazy danych Azure, której przedstawiono funkcje globalne dystrybucji regionalnej pracy awaryjnej w tym.

>[!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

## <a id="ConfigureMultiRegionApplications"></a>Konfigurowanie aplikacji w przypadku
Zanim firma Microsoft Poznaj tryby pracy awaryjnej przyjrzymy się jak konfigurować aplikacji skorzystać w przypadku dostępności i zapewnienie odporności w wypadku regionalnej pracy w trybie Failover.

* Najpierw należy wdrożyć aplikację w wielu regionach
* Aby zapewnić dostęp małe opóźnienia z każdego regionu, wdrażania aplikacji, skonfigurować odpowiednie [listy regiony preferowanych](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations) dla każdego regionu, za pomocą jednego z obsługiwanych zestawów SDK.

Poniższy fragment kodu przedstawia sposób inicjowania aplikacji w przypadku. Tutaj, konto bazy danych Azure rozwiązania Cosmos `contoso.documents.azure.com` jest skonfigurowany z dwóch regionach - zachodnie stany USA, Europa Północna, Europa a. 

* Aplikacja jest wdrażana w regionu zachodnie stany USA, (na przykład przy użyciu usługi aplikacji Azure) 
* Skonfigurowany z użyciem `West US` jako pierwszy preferowany region dla małych opóźnieniach odczytuje
* Skonfigurowany z użyciem `North Europe` jako drugi preferowany region (wysokiej dostępności podczas awarii regionalne)

W interfejsie API SQL ta konfiguracja wygląda następujący fragment kodu:

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

Aplikacja jest wdrażana również w regionie Europa Północna, Europa w kolejności preferowanych regionów wycofane. Oznacza to region Europa Północna, Europa jest określony jako pierwszy dla odczytów małe opóźnienia. Następnie regionu zachodnie stany USA jest określana jako drugi preferowany region wysokiej dostępności podczas regionalnymi awariami.

Na poniższym diagramie architektury przedstawia wdrożenia aplikacji w przypadku których rozwiązania Cosmos bazę danych i aplikacji są skonfigurowane w czterech Azure regionów geograficznych.  

![Wdrożenie aplikacji rozproszonych globalnie z bazy danych Azure rozwiązania Cosmos](./media/regional-failover/app-deployment.png)

Teraz Przyjrzyjmy się jak usługa DB rozwiązania Cosmos obsługuje regionalnymi awariami za pomocą automatycznej pracy awaryjnej. 

## <a id="AutomaticFailovers"></a>Automatycznego przechodzenia w tryb failover
W rzadkich Azure regionalnej awarii lub awarii centrum danych rozwiązania Cosmos DB automatyczne wyzwolenie procesu przechodzenia w tryb failover wszystkich kont DB rozwiązania Cosmos obecności w regionie, których dotyczy. 

**Co się stanie, jeśli region odczytu ma awarii?**

Rozwiązania cosmos konta bazy danych za pomocą obszaru odczytu w jednym z odpowiednim regionów są odłączone od ich region zapisu i automatycznie oznaczone w trybie offline. Zestawy SDK DB rozwiązania Cosmos implementuje protokół regionalnych odnajdywania, który umożliwia im to automatycznie Wykryj, kiedy region jest dostępny i Przekierowanie do odczytu wywołań następny dostępny region na liście preferowanego regionu. Jeśli żaden regionów na liście preferowanych region nie jest dostępny, wywołania automatycznie wrócić do bieżącego obszaru zapisu. W kodzie aplikacji do obsługi regionalnej pracy w trybie Failover są konieczne żadne zmiany. W trakcie tego procesu całego gwarancje spójności nadal uznawane za DB rozwiązania Cosmos.

![Błędy region odczytu w usłudze Azure DB rozwiązania Cosmos](./media/regional-failover/read-region-failures.png)

Po awarii wznawia działanie dotyczy regionu, wszystkich odpowiednich kont DB rozwiązania Cosmos w regionie są automatycznie odzyskiwane przez usługę. Konta rozwiązania cosmos bazy danych, które ma odczytu regionu w regionie, których dotyczy, a następnie automatycznie synchronizować z bieżącego obszaru do zapisu i włączyć online. Zestawy SDK DB rozwiązania Cosmos odnajdywanie dostępności nowych regionu i ocenić, czy należy wybrać region jako bieżący obszar odczytu, na podstawie listy preferowanego regionu skonfigurowane przez aplikację. Kolejne odczyty są przekierowywane do regionu odzyskane bez konieczności wprowadzania jakichkolwiek zmian w kodzie aplikacji.

**Co się stanie, jeśli region zapisu ma awarii?**

Dotyczy region jest bieżący obszar zapisu automatycznej pracy awaryjnej jest włączone dla tego konta bazy danych rozwiązania Cosmos platformy Azure, region automatycznie zaznaczono w trybie offline. Następnie alternatywne region jest podwyższany jako region zapisu dotyczy konta bazy danych Azure rozwiązania Cosmos. Można włączyć automatycznej pracy awaryjnej i w pełni kontrolować kolejność zaznaczania region dla konta bazy danych rozwiązania Cosmos Azure za pośrednictwem portalu Azure lub [programowo](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange). 

![Priorytetów trybu failover dla bazy danych Azure rozwiązania Cosmos](./media/regional-failover/failover-priorities.png)

Podczas automatycznego przechodzenia w tryb failover bazy danych rozwiązania Cosmos Azure automatycznie wybierze kolejnego zapisu region dla danego konta bazy danych Azure rozwiązania Cosmos na podstawie kolejności określony priorytet. Aplikacje umożliwia wykrywanie zmian w regionie zapisu właściwości WriteEndpoint klasy DocumentClient.

![Zakończone niepowodzeniem regionu w usłudze Azure DB rozwiązania Cosmos](./media/regional-failover/write-region-failures.png)

Po awarii wznawia działanie dotyczy regionu, wszystkich odpowiednich kont DB rozwiązania Cosmos w regionie są automatycznie odzyskiwane przez usługę. 

* Opublikowane dane występujące w regionie zapisu poprzedniej, który nie został zreplikowany do odczytu regionów podczas awarii jako konflikt źródła danych. Aplikacji może odczytywać źródło konfliktu, rozwiąż konflikty oparte na konkretnej logiki aplikacji i zapisywać zaktualizowane dane na koncie Azure DB rozwiązania Cosmos zależnie od potrzeb. 
* Poprzednie region zapisu jest tworzony ponownie jako odczytu regionu i automatycznie przywrócony do trybu online. 
* Można ponownie skonfigurować odczytu region, który został przywrócony do trybu online automatycznie jako region zapisu, wykonując ręcznego przełączania trybu failover za pośrednictwem portalu Azure lub [programowo](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate).

Poniższy fragment kodu ilustruje sposób przetwarzania konflikty po odpowiednim region odzyskiwania z awarii.

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

## <a id="ManualFailovers"></a>Ręczne praca awaryjna

Oprócz automatycznej pracy awaryjnej bieżącego obszaru zapisu danego konta rozwiązania Cosmos bazy danych może być zmieniony ręcznie dynamicznie do jednego z istniejących regionów odczytu. Ręczne przechodzenia w tryb failover może być inicjowane za pośrednictwem portalu Azure lub [programowo](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate). 

Zapewnienia ręcznej pracy awaryjnej **zero utraty danych** i **zero dostępności** utratą i bezpiecznie stanu zapisu transferu ze starego zapisu region na nową dla określonego konta DB rozwiązania Cosmos. Podobnie jak automatycznego przechodzenia w tryb failover SDK DB rozwiązania Cosmos automatycznie obsługuje zapisu region zmiany podczas ręcznej pracy awaryjnej i zapewnia, że wywołania są automatycznie przekierowywane do nowego regionu zapisu. W aplikacji do zarządzania trybu failover nie są konieczne nie zmiany kodu lub konfiguracji. 

![Ręcznej pracy awaryjnej w usłudze Azure DB rozwiązania Cosmos](./media/regional-failover/manual-failovers.png)

Typowe scenariusze, w którym ręcznego przełączania trybu failover może być przydatna, należą:

**Postępuj zgodnie z modelem zegara**: Jeśli aplikacje wzorce ruchu przewidywalną na podstawie czasu dnia, okresowo można zmienić stanu zapisu na najbardziej aktywne regionu geograficznego na podstawie czasu dnia.

**Aktualizacja usługi**: niektóre wdrożenia aplikacji rozproszonej globalny może wymagać ponownego routingu ruchu w innym regionie za pośrednictwem Menedżera ruchu podczas ich aktualizacji plan. Takie wdrożenie aplikacji teraz umożliwia zachowanie stanu zapisu do regionu ręcznego przełączania trybu failover w przypadku, gdy ma być aktywne ruchu w oknie aktualizacji usługi.

**Ciągłość prowadzenia działalności biznesowej i odzyskiwania po awarii (BCDR) i rozwija wysokiej dostępności i odzyskiwania po awarii (HADR)**: większość aplikacji przedsiębiorstwa obejmują testy ciągłości biznesowej jako część procesu projektowania i wersji. BCDR i testowania HADR często jest to ważny krok w certyfikatach zgodności i gwarantujących dostępności usługi w przypadku regionalnej awarii. Można sprawdzić gotowość BCDR aplikacji korzystających z rozwiązania Cosmos bazy danych dla magazynu przez wyzwalania ręcznego przełączania trybu failover konta rozwiązania Cosmos bazy danych i/lub dodawanie i usuwanie region dynamicznie.

W tym artykule firma Microsoft przeglądowi sposób ręcznego i automatycznego pracy trybu failover do rozwiązania Cosmos bazy danych oraz sposobu konfigurowania kont DB rozwiązania Cosmos i aplikacje mają być dostępne globalnie. Przy użyciu funkcji obsługi replikacji globalne rozwiązania Cosmos DB, można zwiększyć czas oczekiwania na trasie i upewnij się, że są one wysokiej dostępności, nawet w przypadku niepowodzenia regionu. 

## <a id="NextSteps"></a>Następne kroki
* Dowiedz się więcej o sposobie obsługi DB rozwiązania Cosmos [dystrybucji globalne](distribute-data-globally.md)
* Dowiedz się więcej o [globalne spójności z bazy danych Azure rozwiązania Cosmos](consistency-levels.md)
* Tworzenie z wielu regionach, przy użyciu usługi Azure rozwiązania Cosmos DB [interfejsu API SQL](tutorial-global-distribution-sql-api.md)
* Dowiedz się, jak tworzyć [architektury w przypadku zapisywania](multi-region-writers.md) z bazy danych Azure rozwiązania Cosmos

