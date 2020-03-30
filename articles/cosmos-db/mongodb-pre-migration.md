---
title: Kroki przed migracją w celu migracji danych do interfejsu API usługi Azure Cosmos DB dla usługi MongoDB
description: Ten doc zawiera omówienie wymagań wstępnych dla migracji danych z mongodb do usługi Cosmos DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: 73ac1a6ffd5fc2b2d52f169e1e0332044638f9f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942073"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Kroki przed migracją migracji danych z mongodb do interfejsu API usługi Azure Cosmos DB dla usługi MongoDB

Przed migracją danych z mongodb (lokalnie lub w chmurze) do interfejsu API usługi Azure Cosmos DB dla mongodb, należy:

1. [Przeczytaj najważniejsze zagadnienia dotyczące korzystania z interfejsu API usługi Azure Cosmos DB dla usługi MongoDB](#considerations)
2. [Wybierz opcję migracji danych](#options)
3. [Oszacuj przepustowość potrzebną dla obciążeń](#estimate-throughput)
4. [Wybierz optymalny klucz partycji dla danych](#partitioning)
5. [Opis zasad indeksowania, które można ustawić na danych](#indexing)

Jeśli powyższych wymagań wstępnych dotyczących migracji można [migrować dane mongodb do interfejsu API usługi Azure Cosmos DB dla mongodb przy użyciu usługi migracji bazy danych Azure](../dms/tutorial-mongodb-cosmos-db.md). Ponadto jeśli konto nie zostało utworzone, możesz przeglądać dowolny z [przewodników Szybki start,](create-mongodb-dotnet.md) które pokazują kroki tworzenia konta.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>Zagadnienia dotyczące korzystania z interfejsu API usługi Azure Cosmos DB dla usługi MongoDB

Poniżej przedstawiono określone cechy interfejsu API usługi Azure Cosmos DB dla usługi MongoDB:

- **Model pojemności:** Pojemność bazy danych w usłudze Azure Cosmos DB jest oparta na modelu opartym na przepływności. Ten model jest oparty na [jednostkach żądań na sekundę,](request-units.md)która jest jednostką reprezentującą liczbę operacji bazy danych, które mogą być wykonywane względem kolekcji na sekundę. Tę pojemność można przydzielić na [poziomie bazy danych lub kolekcji](set-throughput.md)i można ją aprowizować w modelu alokacji lub przy użyciu modelu [AutoPilot](provision-throughput-autopilot.md).

- **Jednostki żądań:** Każda operacja bazy danych ma skojarzony koszt jednostek żądań (RUs) w usłudze Azure Cosmos DB. Po wykonaniu jest to odejmowane od poziomu dostępnych jednostek żądań w danej sekundzie. Jeśli żądanie wymaga więcej ru niż aktualnie przydzielone RU/s istnieją dwie opcje, aby rozwiązać problem - zwiększyć ilość ru lub poczekać, aż następna sekunda uruchamia, a następnie ponów próbę wykonania operacji.

- **Pojemność elastyczna:** pojemność dla danej kolekcji lub bazy danych może ulec zmianie w dowolnym momencie. Dzięki temu bazy danych elastycznie dostosować się do wymagań dotyczących przepływności obciążenia.

- **Automatyczne dzielenie na fragmenty:** Usługa Azure Cosmos DB zapewnia automatyczny system partycjonowania, który wymaga tylko fragmentu (lub klucza partycji). Mechanizm [automatycznej partycjonowania](partition-data.md) jest współużytkowane przez wszystkie interfejsy API usługi Azure Cosmos DB i umożliwia bezproblemowe dane i skalowanie za pośrednictwem dystrybucji poziomej.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>Opcje migracji interfejsu API usługi Azure Cosmos DB dla usługi MongoDB

[Usługa migracji bazy danych Azure dla interfejsu API usługi Azure Cosmos DB dla usługi MongoDB](../dms/tutorial-mongodb-cosmos-db.md) zapewnia mechanizm, który upraszcza migrację danych, zapewniając w pełni zarządzaną platformę hostingową, opcje monitorowania migracji i automatyczną obsługę ograniczania przepustowości. Pełna lista opcji jest następująca:

|**Typ migracji**|**Rozwiązanie**|**Zagadnienia dotyczące**|
|---------|---------|---------|
|W trybie offline|[Narzędzie do migracji danych](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Łatwa konfiguracja i obsługa wielu źródeł <br/>&bull;Nie nadaje się do dużych zestawów danych.|
|W trybie offline|[Fabryka danych platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Łatwa konfiguracja i obsługa wielu źródeł <br/>&bull;Korzysta z biblioteki zbiorczego executora usługi Azure Cosmos DB <br/>&bull;Nadaje się do dużych zestawów danych <br/>&bull;Brak punktów kontrolnych oznacza, że każdy problem w trakcie migracji wymagałby ponownego uruchomienia całego procesu migracji<br/>&bull;Brak kolejki martwych liter oznaczałoby, że kilka błędnych plików może zatrzymać cały proces migracji <br/>&bull;Potrzebuje niestandardowego kodu, aby zwiększyć przepływność odczytu dla niektórych źródeł danych|
|W trybie offline|[Istniejące narzędzia Mongo (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Łatwa konfiguracja i integracja <br/>&bull;Wymaga niestandardowej obsługi przepustów|
|Online|[Usługa migracji bazy danych platformy Azure](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull;W pełni zarządzana usługa migracji.<br/>&bull;Zapewnia rozwiązania hostingowe i monitorujące dla zadania migracji. <br/>&bull;Nadaje się do dużych zestawów danych i zajmuje się replikowaniem zmian na żywo <br/>&bull;Działa tylko z innymi źródłami MongoDB|


## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a>Oszacuj zapotrzebowanie na przepustowość dla obciążeń

W usłudze Azure Cosmos DB przepływność jest aprowizowana z wyprzedzeniem i jest mierzona w jednostkach żądań (RU) na sekundę. W przeciwieństwie do maszyn wirtualnych lub serwerów lokalnych, procesory RU są łatwe do skalowania w górę i w dół w dowolnym momencie. Można natychmiast zmienić liczbę aprowizowanych procesorów RU. Aby uzyskać więcej informacji, zobacz [Żądania jednostek w usłudze Azure Cosmos DB](request-units.md).

Za pomocą [kalkulatora pojemności usługi Azure Cosmos DB można](https://cosmos.azure.com/capacitycalculator/) określić ilość jednostek żądań na podstawie konfiguracji konta bazy danych, ilość danych, rozmiar dokumentu i wymagane odczyty i zapisy na sekundę.

Poniżej przedstawiono kluczowe czynniki wpływające na liczbę wymaganych procesorów RU:
- **Rozmiar dokumentu**: Wraz ze wzrostem rozmiaru elementu/dokumentu zwiększa się również liczba procesorów RU zużytych do odczytu lub zapisu elementu/dokumentu.

- **Liczba właściwości dokumentu:Liczba**obiektów obiektów innych niż w celu utworzenia lub zaktualizowania dokumentu jest związana z liczbą, złożonością i długością jego właściwości. Można zmniejszyć zużycie jednostki żądania dla operacji [zapisu, ograniczając liczbę właściwości indeksowanych](mongodb-indexing.md).

- **Wzorce kwerend:** Złożoność kwerendy wpływa na liczbę jednostek żądań zużywanych przez kwerendę. 

Najlepszym sposobem zrozumienia kosztów zapytań jest użycie przykładowych danych w usłudze Azure Cosmos DB i `getLastRequestStastistics` [uruchamianie przykładowych zapytań z powłoki MongoDB](connect-mongodb-account.md) przy użyciu polecenia, aby uzyskać opłatę za żądanie, która spowodujewynienie liczby zużytych obiektów wyeksja:

`db.runCommand({getLastRequestStatistics: 1})`

To polecenie spowoduje wysunie dokument JSON podobny do następującego:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Można również użyć [ustawień diagnostycznych,](cosmosdb-monitor-resource-logs.md) aby zrozumieć częstotliwość i wzorce zapytań wykonywanych za pomocą usługi Azure Cosmos DB. Wyniki z dzienników diagnostycznych mogą być wysyłane do konta magazynu, wystąpienia EventHub lub [usługi Azure Log Analytics.](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>Wybieranie klucza partycji
Partycjonowanie, znany również jako dzielenie na fragmenty, jest kluczowym punktem uwagi przed migracją danych. Usługa Azure Cosmos DB używa w pełni zarządzane partycjonowanie, aby zwiększyć pojemność w bazie danych, aby spełnić wymagania dotyczące magazynu i przepływności. Ta funkcja nie wymaga hostingu ani konfiguracji serwerów routingu.   

W podobny sposób możliwość partycjonowania automatycznie dodaje pojemność i odpowiednio równoważy dane. Szczegółowe informacje i zalecenia dotyczące wybierania odpowiedniego klucza partycji dla danych można znaleźć w [artykule Wybieranie klucza partycji](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a name="index-your-data"></a><a id="indexing"></a>Indeksowanie danych
Domyślnie usługa Azure Cosmos DB zapewnia automatyczne indeksowanie wszystkich wstawionych danych. Możliwości indeksowania udostępniane przez usługę Azure Cosmos DB obejmują dodawanie indeksów złożonych, unikatowych indeksów i indeksów czasu wygaśnięcia (TTL). Interfejs zarządzania indeksem jest `createIndex()` mapowany do polecenia. Dowiedz się więcej na [temat Indeksowania w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB](mongodb-indexing.md).

[Usługa migracji bazy danych Azure](../dms/tutorial-mongodb-cosmos-db.md) automatycznie migruje kolekcje mongodb z unikatowymi indeksami. Jednak unikatowe indeksy muszą zostać utworzone przed migracją. Usługa Azure Cosmos DB nie obsługuje tworzenia unikatowych indeksów, gdy istnieją już dane w kolekcjach. Aby uzyskać więcej informacji, zobacz [Unikatowe klucze w usłudze Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Następne kroki
* [Migrowanie danych mongodb do usługi Cosmos DB przy użyciu usługi migracji bazy danych.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Aprowizuj przepływność w kontenerach i bazach danych usługi Azure Cosmos](set-throughput.md)
* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partition-data.md)
* [Dystrybucja globalna w usłudze Azure Cosmos DB](distribute-data-globally.md)
* [Indeksowanie w usłudze Azure Cosmos DB](index-overview.md)
* [Jednostki żądania w usłudze Azure Cosmos DB](request-units.md)
