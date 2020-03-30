---
title: Odwołanie do danych monitorowania usługi Azure Cosmos DB | Dokumenty firmy Microsoft
description: Rejestrowanie i metryki odwołania do monitorowania danych z usługi Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d243224192b5761af45d387690f5fb41b84481e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588726"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Dokumentacja dotycząca danych monitorowania usługi Azure Cosmos DB
W tym artykule zawarto dokumentację dotyczącą danych dziennika i metryk zebranych w celu analizy wydajności i dostępności usługi Azure Cosmos DB. Zobacz [Monitorowanie usługi Cosmos DB, aby](monitor-cosmos-db.md) uzyskać szczegółowe informacje na temat zbierania i analizowania danych monitorowania dla usługi Azure Cosmos DB.


## <a name="resource-logs"></a>Dzienniki zasobów
W poniższej tabeli wymieniono właściwości dzienników zasobów usługi Azure Cosmos DB, gdy są zbierane w dziennikach usługi Azure Monitor lub usłudze Azure Storage. W dziennikach usługi Azure Monitor są one zbierane w tabeli **AzureDiagnostics** z wartością **ResourceProvider** *firmy MICROSOFT. DOCUMENTDB*. 

| Pole lub właściwość usługi Azure Storage | Właściwość Dzienniki usługi Azure Monitor | Opis |
| --- | --- | --- |
| **Czas** | **TimeGenerated** | Data i godzina (UTC) po wykonaniu operacji. |
| **Resourceid** | **Zasobów** | Konto usługi Azure Cosmos DB, dla którego są włączone dzienniki.|
| **Kategorii** | **Kategoria** | Dla dzienników usługi Azure Cosmos DB, **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests** są dostępne typy dzienników. |
| **Operationname** | **Operationname** | Nazwa operacji. Ta wartość może być dowolną z następujących operacji: Tworzenie, Aktualizacja, Odczyt, Odczyt, Odczyt, Usuń, Zamień, Wykonaj, SqlQuery, Kwerenda, JSQuery, Head, HeadFeed lub Upsert.   |
| **Właściwości** | Nie dotyczy | Zawartość tego pola jest opisana w kolejnych wierszach. |
| **Activityid** | **activityId_g** | Unikatowy identyfikator GUID dla zarejestrowanej operacji. |
| **Useragent** | **userAgent_s** | Ciąg, który określa agenta użytkownika klienta, który wykonuje żądanie. Format to {user agent name}/{version}.|
| **typ źródła żądania** | **requestResourceType_s** | Typ zasobu, do który uzyskuje się dostęp. Ta wartość może być dowolną z następujących typów zasobów: Baza danych, Kontener, Dokument, Załącznik, Użytkownik, Uprawnienie, StoredProcedure, Trigger, UserDefinedFunction lub Oferta. |
| **Statuscode** | **statusCode_s** | Stan odpowiedzi operacji. |
| **requestResourceId** | **ResourceId** | Identyfikator zasobu, który odnosi się do żądania. Wartość może wskazywać databaseRid, collectionRid lub documentRid w zależności od wykonanej operacji.|
| **klientIpAddress** | **clientIpAddress_s** | Adres IP klienta. |
| **requestCharge (Obciążenie żądaniem)** | **requestCharge_s** | Liczba procesorów operacyjnych, które są używane przez operację |
| **kolekcjaRid** | **collectionId_s** | Unikatowy identyfikator kolekcji.|
| **Długość** | **duration_s** | Czas trwania operacji w milisekundach. |
| **wniosekLeni** | **requestLength_s** | Długość żądania w bajtach. |
| **odpowiedźLeni** | **responseLength_s** | Długość odpowiedzi w bajtach.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Ta wartość nie jest pusta, gdy [tokeny zasobów](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) są używane do uwierzytelniania. Wartość wskazuje identyfikator zasobu użytkownika. |

Aby uzyskać listę wszystkich kategorii dzienników usługi Azure Monitor i łączy do skojarzonych [schematów, zobacz Kategorie i schematy dzienników monitora platformy Azure](../azure-monitor/platform/diagnostic-logs-schema.md). 

## <a name="metrics"></a>Metryki
W poniższych tabelach przedstawiono metryki platformy zebrane dla usługi Azure CosmOS DB. Wszystkie metryki są przechowywane w **metrykach standardowych usługi Cosmos DB**obszaru nazw .

Aby uzyskać listę wszystkich metryk pomocy technicznej usługi Azure Monitor (w tym usługi CosmosDB), zobacz [metryki obsługiwane przez usługę Azure Monitor.](../azure-monitor/platform/metrics-supported.md) 

#### <a name="request-metrics"></a>Żądania metryki
            
|Metryka (metryka wyświetlana)|Jednostka (typ agregacji) |Opis|Wymiary| Ziarnistość czasu| Mapowanie metryk starszych | Sposób użycia |
|---|---|---|---| ---| ---| ---|
| TotalRequests (łączna liczba żądań) | Liczba (liczba) | Liczba złożonych wniosków| Nazwa bazy danych, nazwa kolekcji, region, kod stanu| Wszystkie | TotalRequests, http 2xx, http 3xx, http 400, http 401, wewnętrzny błąd serwera, usługa niedostępna, ograniczone żądania, średnia liczba żądań na sekundę | Służy do monitorowania żądań na kod stanu, kontener w minutę ziarnistość. Aby uzyskać średnią liczbę żądań na sekundę, użyj agregacji count w minutach i podziel przez 60. |
| Zapytania o metadane (żądania metadanych) |Liczba (liczba) | Liczba żądań metadanych. Usługa Azure Cosmos DB przechowuje kontener metadanych systemu dla każdego konta, który umożliwia bezpłatne wyliczanie kolekcji, baz danych itp. | Nazwa bazy danych, nazwa kolekcji, region, kod stanu| Wszystkie| |Służy do monitorowania ograniczania przepustowości z powodu żądań metadanych.|
| MongoRequests (Prośby Mongo) | Liczba (liczba) | Liczba złożonych wniosków mongo | Nazwa bazy danych, nazwa kolekcji, region, nazwa polecenia, kod błędu| Wszystkie |Mongo Query Request Rate, Mongo Update Request Rate, Mongo Delete Request Rate, Mongo Insert Request Rate, Mongo Count Request Rate| Służy do monitorowania błędów żądań Mongo, użycia na typ polecenia. |

#### <a name="request-unit-metrics"></a>Dane jednostki żądania

|Metryka (metryka wyświetlana)|Jednostka (typ agregacji)|Opis|Wymiary| Ziarnistość czasu| Mapowanie metryk starszych | Sposób użycia |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (opłata za żądanie Mongo) | Liczba (suma) |Jednostki żądające Mongo zużyte| Nazwa bazy danych, nazwa kolekcji, region, nazwa polecenia, kod błędu| Wszystkie |Mongo Zapytanie żądanie opłaty, Mongo Update Request Charge, Mongo Delete Request Charge, Mongo Insert Request Charge, Mongo Count Request Charge| Służy do monitorowania zasobów Mongo RUs w minutę.|
| TotalRequestUnits (łączna liczba jednostek żądań)| Liczba (suma) | Zużyte jednostki żądań| Nazwa bazy danych, nazwa kolekcji, region, kod stanu |Wszystkie| Jednostki TotalRequestUnits| Służy do monitorowania całkowitego użycia RU w ciągu minuty ziarnistości. Aby uzyskać średnią zużycie RU na sekundę, użyj całkowitej agregacji w minutę i podziel przez 60.|
| AprowizowanaWystarczajność (aprowizowana przepływność)| Liczba (maksymalnie) |Aprowizowana przepływność przy szczegółowości kontenera| Nazwa bazy danych, Nazwa kontenera| 5 mln| | Służy do monitorowania aprowizowanej przepływności na kontener.|

#### <a name="storage-metrics"></a>Dane magazynu

|Metryka (metryka wyświetlana)|Jednostka (typ agregacji)|Opis|Wymiary| Ziarnistość czasu| Mapowanie metryk starszych | Sposób użycia |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (dostępna pamięć masowa) |Bajty (suma) | Całkowita dostępna pamięć masowa zgłoszona przy 5-minutowej szczegółowości na region| Nazwa bazy danych, Nazwa kolekcji, Region| 5 mln| Dostępna pamięć| Służy do monitorowania dostępnej pojemności magazynu (dotyczy tylko stałych kolekcji magazynu) Minimalna szczegółowość powinna wynosić 5 minut.| 
| DataUsage (użycie danych) |Bajty (suma) |Całkowite wykorzystanie danych zgłoszone przy 5-minutowej szczegółowości na region| Nazwa bazy danych, Nazwa kolekcji, Region| 5 mln |Rozmiar danych | Służy do monitorowania całkowitego użycia danych w kontenerze i regionie, minimalna szczegółowość powinna wynosić 5 minut.|
| IndexUsage (użycie indeksu) | Bajty (suma) |Całkowite użycie indeksu zgłoszone przy 5-minutowej szczegółowości na region| Nazwa bazy danych, Nazwa kolekcji, Region| 5 mln| Rozmiar indeksu| Służy do monitorowania całkowitego użycia danych w kontenerze i regionie, minimalna szczegółowość powinna wynosić 5 minut. |
| DocumentQuota (przydział dokumentu) | Bajty (suma) | Całkowity przydział magazynowania zgłoszony przy 5-minutowej szczegółowości na region.| Nazwa bazy danych, Nazwa kolekcji, Region| 5 mln |Pojemność magazynu| Służy do monitorowania całkowitego przydziału w kontenerze i regionie, minimalna szczegółowość powinna wynosić 5 minut.|
| Liczba dokumentów (liczba dokumentów) | Liczba (suma) |Całkowita liczba dokumentów zgłoszonych przy 5-minutowej szczegółowości na region| Nazwa bazy danych, Nazwa kolekcji, Region| 5 mln |Liczba dokumentów|Służy do monitorowania liczby dokumentów w kontenerze i regionie, minimalna szczegółowość powinna wynosić 5 minut.|

#### <a name="latency-metrics"></a>Metryki opóźnienia

|Metryka (metryka wyświetlana)|Jednostka (typ agregacji)|Opis|Wymiary| Ziarnistość czasu| Sposób użycia |
|---|---|---|---| ---| ---|
| Opóźnienie replikacji (opóźnienie replikacji)| Milisekundy (minimum, maksimum, średnia) | P99 Opóźnienie replikacji w regionach źródłowych i docelowych dla konta z obsługą geograficzną| Region źródłowy, region docelowy| Wszystkie | Służy do monitorowania opóźnienia replikacji P99 między dowolnymi dwoma regionami dla konta replikowanego geograficznie. |
| Opóźnienie po stronie serwera| Milisekundy (średnia) | Czas przetwarzania żądania przez serwer. | Nazwa kolekcji, ConnectionMode, DatabaseName, OperationType, PublicAPIType, Region | Wszystkie | Służy do monitorowania opóźnienia żądania na serwerze usługi Azure Cosmos DB. |



#### <a name="availability-metrics"></a>Metryki dostępności

|Metryka (metryka wyświetlana) |Jednostka (typ agregacji)|Opis| Ziarnistość czasu| Mapowanie metryk starszych | Sposób użycia |
|---|---|---|---| ---| ---|
| Dostępność usługi (dostępność usługi)| Procent (minimum, maksimum) | Dostępność żądań konta przy jednej godzinie szczegółowości| 1H | Dostępność usług | Reprezentuje procent całkowitej przekazanych żądań. Żądanie jest uważane za nie powiodło się z powodu błędu systemu, jeśli kod stanu jest 410, 500 lub 503 Służy do monitorowania dostępności konta w godzinach szczegółowości. |


#### <a name="cassandra-api-metrics"></a>Metryki interfejsu API Cassandra

|Metryka (metryka wyświetlana)|Jednostka (typ agregacji)|Opis|Wymiary| Ziarnistość czasu| Sposób użycia |
|---|---|---|---| ---| ---|
| CassandraRequests (Prośby Kasandry) | Liczba (liczba) | Liczba złożonych żądań api Cassandra| Nazwa bazy danych, nazwa kolekcji, kod błędu, region, typ operacji, typ zasobu| Wszystkie| Służy do monitorowania żądań Cassandra w minutę ziarnistość. Aby uzyskać średnią liczbę żądań na sekundę, użyj agregacji count w minutach i podziel przez 60.|
| CassandraRequestCharges (opłaty za żądanie Cassandra) | Liczba (Suma, Min, Max, Średnia) | Jednostki żądań używane przez żądania interfejsu API Cassandra| Nazwa bazy danych, nazwa kolekcji, region, typ operacji, typ zasobu| Wszystkie| Służy do monitorowania procesorów innych niż minutę przez konto interfejsu API Cassandra.|
| CassandraConnectionClosures (Zamknięcia połączeń Cassandra) |Liczba (liczba) |Liczba zamkniętych połączeń Cassandra| ClosureReason, Region| Wszystkie | Służy do monitorowania łączności między klientami i interfejsu API Cassandra usługi Azure Cosmos DB.|

## <a name="see-also"></a>Zobacz też

- Zobacz [monitorowanie usługi Azure Cosmos DB](monitor-cosmos-db.md) opis monitorowania usługi Azure Cosmos DB.
- Zobacz [monitorowanie zasobów platformy Azure za pomocą usługi Azure Monitor, aby](../azure-monitor/insights/monitor-azure-resource.md) uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure.
