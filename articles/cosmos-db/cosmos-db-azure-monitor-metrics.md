---
title: Pobierz metryki usługi Azure Cosmos DB z usługi Azure Monitor
description: ''
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 2eb61a6b9afa3cabf1733be120dfbdacb7de4534
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276797"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>Monitorowania i debugowania metryk usługi Azure Cosmos DB z usługi Azure Monitor

Możesz wyświetlić metryki usługi Azure Cosmos DB z interfejsem API usługi Azure Monitor. Usługa Azure Monitor zapewnia kilka sposobów na korzystanie z metryk, łącznie z witryny Azure portal, uzyskując dostęp do nich za pośrednictwem interfejsu API REST lub ich zapytań przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Metryki usługi Azure Cosmos DB to wartości liczbowe o małych opóźnieniach, domyślnie zbierane częstotliwością co minutę, możesz także agregować tych metryk. Te metryki są zdolne do obsługi scenariuszy w czasie rzeczywistym.  

W tym artykule opisano różne usługi Azure Cosmos DB metryk, które można wyświetlić z usługi Azure Monitor przy użyciu witryny Azure portal. Jeśli interesuje Cię w typowych przypadkach użycia i jak metryki usługi Azure Cosmos DB re umożliwia analizowanie i debugowanie tych problemów, zobacz [monitorowania i debugowania za pomocą metryk w usłudze Azure Cosmos DB](use-metrics.md) artykułu. Spowoduje użyj jednej z istniejących kont usługi Azure Cosmos i wyświetlić różne metryki poziomie bazy danych, kontenerów, region, żądanie lub operacji. Dlatego upewnij się, masz konto usługi Azure Cosmos z przykładowymi danymi i wykonywanie operacji CRUD na tych danych.

## <a name="view-metrics-from-azure-portal"></a>Wyświetl metryki w witrynie Azure portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz **Monitor** z lewym pasku nawigacyjnym, a wybierz **metryki**.

   ![W okienku metryk w usłudze Azure Monitor](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. Z **metryki** okienko > **wybierz zasób** > Wybierz wymagane **subskrypcji**, i **grupy zasobów**. Dla **typ zasobu**, wybierz opcję **kont usługi Azure Cosmos DB**, wybierz jedną z istniejących kont usługi Azure Cosmos i wybierz **Zastosuj**. 

   ![Wybierz konto usługi Cosmos DB, aby wyświetlić metryki](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. Następnie możesz wybrać metryki z listy dostępnych metryk. Możesz wybrać metryki specyficzne dla jednostek żądania, magazynu, opóźnienia, dostępności, Cassandra i inne. Aby dowiedzieć się więcej szczegółów na temat wszystkich dostępnych metryk na tej liście, zobacz [metryki według kategorii](#metrics-by-category) dalszej części tego artykułu. W tym przykładzie wybierzemy **jednostek żądania** i **Avg** jako wartość agregacji. 

   Oprócz tych szczegółów możesz również wybrać **zakres czasu** i **stopień szczegółowości czasu** metryk. W przypadku osiągnięcia maksymalnej liczby można wyświetlać metryki w ciągu ostatnich 30 dni.  Po zastosowaniu filtru wyświetlania wykresu oparte na filtr. Widać to średnia liczba jednostek żądania używane na minutę w wybranym okresie.  

   ![Wybierz metrykę w witrynie Azure portal](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>Dodaj filtry do metryk

Można również filtrować metryki i wykres wyświetlane przez konkretną **CollectionName**, **DatabaseName**, **OperationType**, **Region**, i **StatusCode**. Aby filtrować metryki, wybierz **Dodaj filtr** i wybierz polecenie wymaganej właściwości, takie jak **OperationType** i wybierz wartość, takich jak **zapytania**. Wykres przedstawia następnie jednostek żądania, używane dla operacji zapytania dla wybranego okresu. Operacje wykonywane przy użyciu procedury składowanej nie są rejestrowane, dzięki czemu nie są dostępne w obszarze metryki OperationType.

![Dodaj filtr, aby wybrać metryki stopień szczegółowości](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

Możesz grupować metryki za pomocą **zastosować podział** opcji. Można na przykład grupy jednostek żądania na typ operacji i wyświetlić wykres dla wszystkich operacji na raz, jak pokazano na poniższej ilustracji: 

![Dodaj filtr podziału](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>Metryki według kategorii

### <a name="request-metrics"></a>Metryki żądania
            
|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji) |Opis|Wymiary| Stopniach szczegółowości czasu| Starsze mapowania metryki | Sposób użycia |
|---|---|---|---| ---| ---| ---|
| TotalRequests (łączna liczba żądań) | Count (licznik) | Liczba żądań| DatabaseName, StatusCode CollectionName, Region,| Wszyscy | TotalRequests, Http 2xx, 3xx Http, Http 400, Http 401, wewnętrzny błąd serwera, Usługa niedostępna, ograniczenia żądań, żądań na sekundę | Używana do monitorowania żądań na został zwrócony kod stanu kolekcji na minutę stopień szczegółowości. Aby uzyskać średnia liczba żądań na sekundę, użycia agregacji liczba na minutę, a następnie podzielić przez 60. |
| MetadataRequests (liczba żądań metadanych) |Count (licznik) | Liczba żądań metadanych. Usługa Azure Cosmos DB obsługuje kolekcji metadanych systemowych dla każdego konta, które umożliwiają wyliczanie w kolekcji, bazami danych itp., i ich konfiguracji bezpłatnie. | DatabaseName, StatusCode CollectionName, Region,| Wszyscy| |Używana do monitorowania ograniczenia ze względu na żądania metadanych.|
| MongoRequests (Mongo żądań) | Count (licznik) | Liczba żądań Mongo | DatabaseName CollectionName, Region, CommandName, kod błędu| Wszyscy |Liczba żądań zapytanie MONGO, Mongo aktualizacji żądania wskaźnik, Mongo usunąć liczba żądań, Mongo Wstaw liczba żądań Mongo liczby żądań zakończonych| Umożliwia monitorowanie błędów żądania Mongo, użycia dla polecenia należy wpisać. |

### <a name="request-unit-metrics"></a>Metryki jednostki żądania

|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji)|Opis|Wymiary| Stopniach szczegółowości czasu| Starsze mapowania metryki | Sposób użycia |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo żądanie opłaty) | Liczba (łącznie) |Wykorzystane jednostki żądania MONGO| DatabaseName CollectionName, Region, CommandName, kod błędu| Wszyscy |Opłata za żądanie wyrażana MONGO zapytania, Mongo aktualizacji żądanie — opłata, Mongo usunąć opłata za żądanie wyrażana, Mongo Wstaw opłata za żądanie wyrażana, liczba Mongo żądania opłata| Używana do monitorowania zasobów Mongo (RUS) w ciągu minuty.|
| TotalRequestUnits (łączna liczba żądań jednostki)| Liczba (łącznie) | Używane jednostki żądania| DatabaseName, StatusCode CollectionName, Region, |Wszyscy| TotalRequestUnits| Umożliwia monitorowanie użycia łączna liczba jednostek RU na minutę stopień szczegółowości. Aby uzyskać RU średnie użycie na sekundę, użycia agregacji łączna liczba na minutę i podzielić przez 60.|
| ProvisionedThroughput (Aprowizowanej przepływności)| Liczba (maksymalnie) |Przepływność aprowizowana w kolekcji stopień szczegółowości| DatabaseName, CollectionName| 5 MIN| | Używana do monitorowania aprowizowanej przepływności na kolekcję.|

### <a name="storage-metrics"></a>Metryki magazynu

|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji)|Opis|Wymiary| Stopniach szczegółowości czasu| Starsze mapowania metryki | Sposób użycia |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (dostępnego magazynu) |Bajty (łącznie) | Całkowita ilość miejsca dostępna zgłoszone w 5 minut szczegółowości na region| DatabaseName, CollectionName, Region| 5 MIN| Dostępna pamięć| Służy do monitorowania dostępny magazyn szczegółowości minimalnej pojemności (dotyczy tylko w przypadku magazynu stałej kolekcji) powinien wynosić 5 minut.| 
| DataUsage (Korzystanie z danych) |Bajty (łącznie) |Łączna ilość danych użycia zgłoszonych w 5 minut szczegółowości na region| DatabaseName, CollectionName, Region| 5 MIN |Rozmiar danych | Umożliwia monitorowanie użycia łączna ilość danych, kolekcji i region, minimalna szczegółowość powinien wynosić 5 minut.|
| IndexUsage (użycie indeksu) | Bajty (łącznie) |Łączne użycie indeksu zgłoszone w 5 minut szczegółowości na region| DatabaseName, CollectionName, Region| 5 MIN| Rozmiar indeksu| Umożliwia monitorowanie użycia łączna ilość danych, kolekcji i region, minimalna szczegółowość powinien wynosić 5 minut. |
| DocumentQuota (limit przydziału dokumentu) | Bajty (łącznie) | Przydział pamięci masowej zgłoszone w 5 minut szczegółowości na region.| DatabaseName, CollectionName, Region| 5 MIN |Pojemność magazynu| Umożliwia monitorowanie łącznego limitu przydziału na poziomie kolekcji i region, minimalna szczegółowość powinien wynosić 5 minut.|
| DocumentCount (liczba dokumentów) | Liczba (łącznie) |Łączną liczbę dokumentów zgłoszone w 5 minut szczegółowości na region| DatabaseName, CollectionName, Region| 5 MIN |Liczba dokumentów|Używane do monitorowania liczby dokumentów w kolekcji i region, minimalna szczegółowość powinien wynosić 5 minut.|

### <a name="latency-metrics"></a>Opóźnienie metryki

|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji)|Opis|Wymiary| Stopniach szczegółowości czasu| Sposób użycia |
|---|---|---|---| ---| ---|
| ReplicationLatency (opóźnienie replikacji)| Milisekundy (minimalna, maksymalna i średnia) | Poziomie P99 opóźnienie replikacji między regionami źródłowych i docelowych dla konta włączono geograficznie| SourceRegion, TargetRegion| Wszyscy | Używana do monitorowania na poziomie P99 opóźnienie replikacji między dwoma regionami oddalonymi dowolnego konta z replikacją geograficzną. |


### <a name="availability-metrics"></a>Metryki dostępności

|Metryka (nazwa wyświetlana metryki) |Jednostka (typ agregacji)|Opis| Stopniach szczegółowości czasu| Starsze mapowania metryki | Sposób użycia |
|---|---|---|---| ---| ---|
| ServiceAvailability (dostępność usługi)| Procent (Minimum, maksimum) | Dostępność żądania konta na jedną godzinę stopień szczegółowości| 1H | Dostępność usługi | Reprezentuje procent sumy przekazane żądania. Żądanie uznaje się niepowodzeniem z powodu błędu systemu, jeśli 410, jest kod stanu 500 lub 503 służy do monitorowania dostępności kont na godzinę stopień szczegółowości. |


### <a name="cassandra-api-metrics"></a>Metryki interfejsu API rozwiązania Cassandra

|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji)|Opis|Wymiary| Stopniach szczegółowości czasu| Sposób użycia |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra żądań) | Count (licznik) | Liczba żądań interfejsu API rozwiązania Cassandra| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Wszyscy| Umożliwia monitorowanie bazy danych Cassandra żądań na minutę stopień szczegółowości. Aby uzyskać średnia liczba żądań na sekundę, użycia agregacji liczba na minutę, a następnie podzielić przez 60.|
| CassandraRequestCharges (Cassandra żądania opłaty) | Liczba (Sum, Min, Max, Avg) | Jednostki używane przez żądania interfejsu API rozwiązania Cassandra żądania| DatabaseName, CollectionName, Region, OperationType, ResourceType| Wszyscy| Używana do monitorowania jednostek ru na minutę posługują się konta interfejsu API rozwiązania Cassandra.|
| CassandraConnectionClosures (zamknięć połączenia bazy danych Cassandra) |Count (licznik) |Liczba połączeń bazy danych Cassandra z zamknięty| ClosureReason, Region| Wszyscy | Używane do monitorowania łączności między klientami i interfejsu API rozwiązania Cassandra usługi Azure Cosmos DB.|

## <a name="next-steps"></a>Kolejne kroki

* [Wyświetl i Monitoruj metryki z okienka metryki konta usługi Azure Cosmos DB](use-metrics.md)

* [Rejestrowanie diagnostyczne w usłudze Azure Cosmos DB](logging.md)
