---
title: Pobierz metryki Azure Cosmos DB z Azure Monitor
description: Dowiedz się, jak wyświetlać różne kategorie Azure Cosmos DB metryk z Azure Monitor przy użyciu Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: 905eca99c137af2fd40a1243de8fabd15314477c
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973740"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>Monitoruj i Debuguj Azure Cosmos DB metryki z Azure Monitor

Azure Cosmos DB metryki można przeglądać z poziomu interfejsu API Azure Monitor. Azure Monitor oferuje kilka sposobów współpracy z metrykami, w tym Azure Portal, uzyskiwania dostępu do nich za pośrednictwem interfejsu API REST lub wykonywania zapytań przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Metryki Azure Cosmos DB to wartości liczbowe o małym opóźnieniu, zbierane z częstotliwością jednej minuty domyślnie, można również agregować te metryki. Te metryki mogą obsługiwać scenariusze w czasie rzeczywistym.  

W tym artykule opisano różne Azure Cosmos DB metryki, które można wyświetlać z Azure Monitor przy użyciu Azure Portal. Jeśli interesuje Cię typowe przypadki użycia i sposób, w jaki Azure Cosmos DB metryki ponownie używane do analizowania i debugowania tych problemów, zobacz [monitorowanie i debugowanie za pomocą metryk w Azure Cosmos DB](use-metrics.md) artykule. Będziesz używać jednego z istniejących kont usługi Azure Cosmos i wyświetlać różne metryki na poziomie bazy danych, kontenera, regionu, żądania lub operacji. Upewnij się, że masz konto usługi Azure Cosmos z przykładowymi danymi i wykonywanie operacji CRUD na tych danych.

## <a name="view-metrics-from-azure-portal"></a>Wyświetl metryki z Azure Portal

1. Zaloguj się do [portalu Azure](https://portal.azure.com/).

1. Wybierz pozycję **monitor** na pasku nawigacyjnym po lewej stronie, a następnie wybierz pozycję **metryki**.

   ![Okienko metryki w Azure Monitor](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. W okienku **metryki** > **Wybierz zasób** > Wybierz wymaganą **subskrypcję**i **grupę zasobów**. W polu **Typ zasobu**wybierz pozycję **konta Azure Cosmos DB**, wybierz jedno z istniejących kont usługi Azure Cosmos i wybierz pozycję **Zastosuj**. 

   ![Wybierz konto Cosmos DB, aby wyświetlić metryki](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. Następnie możesz wybrać metrykę z listy dostępnych metryk. Można wybrać metryki specyficzne dla jednostek żądań, magazynu, opóźnień, dostępności, Cassandra i innych. Aby uzyskać szczegółowe informacje na temat wszystkich dostępnych metryk na tej liście, zobacz sekcję [metryki według kategorii](#metrics-by-category) w tym artykule. W tym przykładzie wybieramy **jednostki żądania** i **średnika** jako wartość agregacji. 

   Oprócz tych szczegółów można także wybrać **zakres czasu** i **stopień szczegółowości** metryk. Co więcej, można wyświetlić metryki z ostatnich 30 dni.  Po zastosowaniu filtru na podstawie filtru zostanie wyświetlony wykres. W wybranym okresie można zobaczyć średnią liczbę jednostek żądań zużytych na minutę.  

   ![Wybierz metrykę z Azure Portal](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>Dodawanie filtrów do metryk

Można również filtrować metryki i wykres wyświetlany przez określoną **CollectionName**, **DatabaseName**, **OperationType**, **region**i **StatusCode**. Aby odfiltrować metryki, wybierz pozycję **Dodaj filtr** i wybierz wymaganą właściwość, taką jak **OperationType** , i wybierz wartość, taką jak **zapytanie**. Następnie Wykres wyświetla jednostki żądania wykorzystane dla operacji zapytania w wybranym okresie. Operacje wykonywane za pośrednictwem procedury składowanej nie są rejestrowane, więc nie są dostępne w ramach metryki operacji.

![Dodaj filtr, aby wybrać stopień szczegółowości metryki](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

Metryki można grupować przy użyciu opcji **Zastosuj dzielenie** . Na przykład można grupować jednostki żądań na typ operacji i wyświetlać wykres dla wszystkich operacji na raz, jak pokazano na poniższej ilustracji: 

![Dodaj filtr podziału zastosowania](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>Metryki według kategorii

### <a name="request-metrics"></a>Metryki żądań
            
|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji) |Opis|Wymiary| Stopień szczegółowości czasu| Mapowanie starszej metryki | Użycie |
|---|---|---|---| ---| ---| ---|
| TotalRequests (całkowita liczba żądań) | Liczba (liczba) | Liczba wykonanych żądań| DatabaseName, CollectionName, region, StatusCode| Wszystko | TotalRequests, http 2xx, http 3xx, http 400, HTTP 401, wewnętrzny błąd serwera, Usługa niedostępna, żądania ograniczone, średnia liczba żądań na sekundę | Służy do monitorowania żądań według kodu stanu, kontenera z dokładnością do minuty. Aby uzyskać średnią liczbę żądań na sekundę, użyj agregacji Count na minutę i Podziel ją na 60. |
| MetadataRequests (żądania metadanych) |Liczba (liczba) | Liczba żądań metadanych. Azure Cosmos DB utrzymuje kontener metadanych systemu dla każdego konta, który umożliwia Wyliczenie kolekcji, baz danych itp., a ich konfiguracje, bez opłat. | DatabaseName, CollectionName, region, StatusCode| Wszystko| |Służy do monitorowania ograniczania przepustowości ze względu na żądania metadanych.|
| MongoRequests (żądania Mongo) | Liczba (liczba) | Liczba wykonanych żądań Mongo | DatabaseName, CollectionName, region, CommandName, ErrorCode| Wszystko |Częstotliwość żądań zapytań Mongo, szybkość żądania aktualizacji Mongo, częstotliwość żądań usunięcia Mongo, Mongo wstawiania liczby żądań, Mongo| Służy do monitorowania błędów żądania Mongo, użycia na typ polecenia. |

### <a name="request-unit-metrics"></a>Metryki jednostki żądania

|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji)|Opis|Wymiary| Stopień szczegółowości czasu| Mapowanie starszej metryki | Użycie |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo żądania) | Liczba (łącznie) |Wykorzystane jednostki żądania Mongo| DatabaseName, CollectionName, region, CommandName, ErrorCode| Wszystko |Mongo naliczanie żądań zapytań, opłata za żądanie aktualizacji Mongo, opłata za żądanie usunięcia żądania, Mongo, opłata za żądanie| Służy do monitorowania Mongo zasobów jednostek RU w ciągu minuty.|
| TotalRequestUnits (łączna liczba jednostek żądań)| Liczba (łącznie) | Wykorzystane jednostki żądania| DatabaseName, CollectionName, region, StatusCode |Wszystko| TotalRequestUnits| Służy do monitorowania całkowitego użycia RU z dokładnością do minuty. Aby obliczyć średnią liczbę zużytych jednostek RU na sekundę, użyj łącznej agregacji na minutę i Podziel ją na 60.|
| ProvisionedThroughput (zainicjowana przepływność)| Liczba (maksimum) |Elastyczna przepływność na poziomie szczegółowości kontenera| DatabaseName, ContainerName| 5 M| | Służy do monitorowania zainicjowanej przepływności na kontener.|

### <a name="storage-metrics"></a>Metryki magazynu

|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji)|Opis|Wymiary| Stopień szczegółowości czasu| Mapowanie starszej metryki | Użycie |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (dostępny magazyn) |Bajty (łącznie) | Łączna ilość dostępnego magazynu zgłoszona z dokładnością do 5 minut na region| DatabaseName, CollectionName, region| 5 M| Dostępny magazyn| Służy do monitorowania dostępnej pojemności magazynu (dotyczy tylko stałych kolekcji magazynów) minimalny stopień szczegółowości powinien wynosić 5 minut.| 
| Datausage (użycie danych) |Bajty (łącznie) |Całkowite użycie danych zgłoszone z dokładnością do 5 minut na region| DatabaseName, CollectionName, region| 5 M |Rozmiar danych | Służy do monitorowania łącznego użycia danych w kontenerze i regionie, minimalny stopień szczegółowości powinien wynosić 5 minut.|
| IndexUsage (użycie indeksu) | Bajty (łącznie) |Całkowite użycie indeksów zgłoszone w 5-minutowych stopnia szczegółowości na region| DatabaseName, CollectionName, region| 5 M| Rozmiar indeksu| Służy do monitorowania łącznego użycia danych w kontenerze i regionie, minimalny stopień szczegółowości powinien wynosić 5 minut. |
| DocumentQuota (przydział dokumentu) | Bajty (łącznie) | Łączny przydział magazynu zgłoszony z dokładnością do 5 minut na region.| DatabaseName, CollectionName, region| 5 M |Pojemność magazynu| Służy do monitorowania łącznego limitu przydziału w kontenerze i regionie. minimalny stopień szczegółowości powinien wynosić 5 minut.|
| DocumentCount (liczba dokumentów) | Liczba (łącznie) |Łączna liczba dokumentów zgłoszona z dokładnością do 5 minut na region| DatabaseName, CollectionName, region| 5 M |Liczba dokumentów|Używane do monitorowania liczby dokumentów w kontenerze i regionie, minimalny stopień szczegółowości powinien wynosić 5 minut.|

### <a name="latency-metrics"></a>Metryki opóźnienia

|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji)|Opis|Wymiary| Stopień szczegółowości czasu| Użycie |
|---|---|---|---| ---| ---|
| ReplicationLatency (opóźnienie replikacji)| Milisekundy (minimum, maksimum, średnia) | Opóźnienie replikacji poziomie P99 w regionach źródłowym i docelowym dla konta z obsługą geograficzną| SourceRegion, TargetRegion| Wszystko | Służy do monitorowania opóźnienia replikacji poziomie P99 między dowolnymi dwoma regionami dla konta z replikacją geograficzną. |


### <a name="availability-metrics"></a>Metryki dostępności

|Metryka (nazwa wyświetlana metryki) |Jednostka (typ agregacji)|Opis| Stopień szczegółowości czasu| Mapowanie starszej metryki | Użycie |
|---|---|---|---| ---| ---|
| Serviceavailability (dostępność usługi)| Procent (minimum, maksimum) | Dostępność żądań konta z dokładnością do 1 godziny| 1 godz | Dostępność usługi | Reprezentuje procent całkowitej liczby żądań zakończonych niepowodzenie. Żądanie jest uznawane za niepowodzenie z powodu błędu systemu, jeśli kod stanu to 410, 500 lub 503 używany do monitorowania dostępności konta na poziomie szczegółowości godzin. |


### <a name="cassandra-api-metrics"></a>Metryki interfejs API Cassandra

|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji)|Opis|Wymiary| Stopień szczegółowości czasu| Użycie |
|---|---|---|---| ---| ---|
| CassandraRequests (żądania Cassandra) | Liczba (liczba) | Liczba wykonanych żądań interfejs API Cassandra| DatabaseName, CollectionName, ErrorCode, region, OperationType, ResourceType| Wszystko| Służy do monitorowania żądań Cassandra z dokładnością do minuty. Aby uzyskać średnią liczbę żądań na sekundę, użyj agregacji Count na minutę i Podziel ją na 60.|
| CassandraRequestCharges (Cassandra żądania) | Count (sum, min, Max, AVG) | Jednostki żądań używane przez żądania interfejs API Cassandra| DatabaseName, CollectionName, region, OperationType, ResourceType| Wszystko| Służy do monitorowania jednostek ru używanych na minutę przez konto interfejs API Cassandra.|
| CassandraConnectionClosures (zamknięcia połączeń Cassandra) |Liczba (liczba) |Liczba zamkniętych połączeń Cassandra| ClosureReason, region| Wszystko | Służy do monitorowania łączności między klientami i interfejs API Cassandra Azure Cosmos DB.|

## <a name="next-steps"></a>Następne kroki

* [Wyświetl i monitoruj metryki z poziomu okienka metryki konta Azure Cosmos DB](use-metrics.md)

* [Rejestrowanie diagnostyczne w Azure Cosmos DB](logging.md)
