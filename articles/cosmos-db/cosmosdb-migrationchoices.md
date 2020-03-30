---
title: Opcje migracji bazy danych usługi Cosmos
description: W tym dopc opisano różne opcje migracji danych lokalnych lub danych w chmurze do usługi Azure Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 9111193bb441487b9e3c49bc9ee1a296d49f8a31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72882384"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Opcje migracji danych lokalnych lub danych w chmurze do usługi Azure Cosmos DB

Można załadować dane z różnych źródeł danych do usługi Azure Cosmos DB. Ponadto ponieważ usługa Azure Cosmos DB obsługuje wiele interfejsów API, obiekty docelowe mogą być dowolnym z istniejących interfejsów API. Aby obsługiwać ścieżki migracji z różnych źródeł do różnych interfejsów API usługi Azure Cosmos DB, istnieje wiele rozwiązań, które zapewniają wyspecjalizowaną obsługę dla każdej ścieżki migracji. W tym dokumencie wymieniono dostępne rozwiązania i opisano ich zalety i ograniczenia.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Czynniki wpływające na wybór narzędzia migracji

Wybór narzędzia migracji określa następujące czynniki:
* **Migracja online i offline:** wiele narzędzi migracji zapewnia ścieżkę do jednorazowej migracji. Oznacza to, że aplikacje uzyskujące dostęp do bazy danych mogą wystąpić okres przestoju. Niektóre rozwiązania migracji umożliwiają migrację na żywo, w której między źródłem a obiektem docelowym jest skonfigurowany potok replikacji.

* **Źródło danych**: Istniejące dane mogą znajdować się w różnych źródłach danych, takich jak Oracle DB2, Datastax Cassanda, Azure SQL Server, PostgreSQL itp. Dane mogą również znajdować się na istniejącym koncie usługi Azure Cosmos DB, a celem migracji może być zmiana modelu danych lub partycjonowanie danych w kontenerze z innym kluczem partycji.

* **Interfejs API usługi Azure Cosmos DB:** Dla interfejsu API SQL w usłudze Azure Cosmos DB istnieje wiele narzędzi opracowanych przez zespół usługi Azure Cosmos DB, które pomagają w różnych scenariuszach migracji. Wszystkie inne interfejsy API mają swój własny wyspecjalizowany zestaw narzędzi opracowanych i utrzymywanych przez społeczność. Ponieważ usługa Azure Cosmos DB obsługuje te interfejsy API na poziomie protokołu przewodowego, te narzędzia powinny działać w stanie— jest podczas migracji danych do usługi Azure Cosmos DB. Jednak mogą one wymagać niestandardowej obsługi dla ograniczania przepustowości, ponieważ ta koncepcja jest specyficzna dla usługi Azure Cosmos DB.

* **Rozmiar danych:** Większość narzędzi migracji działa bardzo dobrze w przypadku mniejszych zestawów danych. Gdy zestaw danych przekracza kilkaset gigabajtów, możliwości wyboru narzędzi migracji są ograniczone. 

* **Oczekiwany czas trwania migracji:** Migracje mogą być skonfigurowane tak, aby odbywały się w powolnym, przyrostowym tempie, które zużywa mniej przepływności lub może zużywać całą przepływność aprowizowaną w docelowym kontenerze usługi Azure Cosmos DB i zakończyć migrację w krótszym czasie.

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB — interfejs SQL API
|**Typ migracji**|**Rozwiązanie**|**Zagadnienia dotyczące**|
|---------|---------|---------|
|W trybie offline|[Narzędzie do migracji danych](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Łatwa konfiguracja i obsługa wielu źródeł <br/>&bull;Nie nadaje się do dużych zestawów danych|
|W trybie offline|[Fabryka danych platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Łatwa konfiguracja i obsługa wielu źródeł <br/>&bull;Korzysta z biblioteki zbiorczego executora usługi Azure Cosmos DB <br/>&bull;Nadaje się do dużych zestawów danych <br/>&bull;Brak punktów kontrolnych — oznacza to, że jeśli w trakcie migracji wystąpi problem, należy ponownie uruchomić cały proces migracji<br/>&bull;Brak kolejki utraconych wiadomości — oznacza to, że kilka błędnych plików może zatrzymać cały proces migracji.|
|W trybie offline|[Łącznik platformy Azure Cosmos DB Spark](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull;Korzysta z biblioteki zbiorczego executora usługi Azure Cosmos DB <br/>&bull;Nadaje się do dużych zestawów danych <br/>&bull;Wymaga niestandardowej konfiguracji platformy Spark <br/>&bull;Iskra jest wrażliwa na niespójności schematu i może to być problem podczas migracji |
|W trybie offline|[Niestandardowe narzędzie z biblioteką zbiorczą executor usługi Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;Zapewnia funkcje punktów kontrolnych, dead-lettering, które zwiększają odporność migracji <br/>&bull;Nadaje się do bardzo dużych zestawów danych (10 TB+)  <br/>&bull;Wymaga niestandardowej konfiguracji tego narzędzia jako usługi aplikacji |
|Online|[Funkcje usługi Cosmos DB + interfejs API ChangeFeed](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull;Łatwa konfiguracja <br/>&bull;Działa tylko wtedy, gdy źródłem jest kontener usługi Azure Cosmos DB <br/>&bull;Nie nadaje się do dużych zestawów danych <br/>&bull;Nie przechwytuje usuwa z kontenera źródłowego |
|Online|[Niestandardowa usługa migracji przy użyciu kanału zmian](https://aka.ms/CosmosDBMigrationSample)|&bull;Zapewnia śledzenie postępu <br/>&bull;Działa tylko wtedy, gdy źródłem jest kontener usługi Azure Cosmos DB <br/>&bull;Działa również w przypadku większych zestawów danych <br/>&bull;Wymaga od użytkownika skonfigurowania usługi App Service do obsługi procesora pliku danych Zmian <br/>&bull;Nie przechwytuje usuwa z kontenera źródłowego|
|Online|[Okręg wyborczy Striim](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;Współpracuje z wieloma źródłami, takimi jak Oracle, DB2, SQL Server <br/>&bull;Łatwe tworzenie rurociągów ETL i zapewnia pulpit nawigacyjny do monitorowania <br/>&bull;Obsługa większych zestawów danych <br/>&bull;Ponieważ jest to narzędzie innej firmy, należy je kupić w witrynie marketplace i zainstalować w środowisku użytkownika.|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API
|**Typ migracji**|**Rozwiązanie**|**Zagadnienia dotyczące**|
|---------|---------|---------|
|W trybie offline|[Narzędzie do migracji danych](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Łatwa konfiguracja i obsługa wielu źródeł <br/>&bull;Nie nadaje się do dużych zestawów danych|
|W trybie offline|[Fabryka danych platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Łatwa konfiguracja i obsługa wielu źródeł <br/>&bull;Korzysta z biblioteki zbiorczego executora usługi Azure Cosmos DB <br/>&bull;Nadaje się do dużych zestawów danych <br/>&bull;Brak punktów kontrolnych oznacza, że każdy problem w trakcie migracji wymagałby ponownego uruchomienia całego procesu migracji<br/>&bull;Brak kolejki martwych liter oznaczałoby, że kilka błędnych plików może zatrzymać cały proces migracji <br/>&bull;Potrzebuje niestandardowego kodu, aby zwiększyć przepływność odczytu dla niektórych źródeł danych|
|W trybie offline|[Istniejące narzędzia Mongo (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Łatwa konfiguracja i integracja <br/>&bull;Wymaga niestandardowej obsługi przepustów|
|Online|[Usługa migracji bazy danych platformy Azure](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull;Korzysta z biblioteki zbiorczego executora usługi Azure Cosmos DB <br/>&bull;Nadaje się do dużych zestawów danych i zajmuje się replikowaniem zmian na żywo <br/>&bull;Działa tylko z innymi źródłami MongoDB|

## <a name="azure-cosmos-db-cassandra-api"></a>Interfejs API Cassandra usługi Azure Cosmos DB
|**Typ migracji**|**Rozwiązanie**|**Zagadnienia dotyczące**|
|---------|---------|---------|
|W trybie offline|[cqlsh COPY, polecenie](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull;Łatwa konfiguracja <br/>&bull;Nie nadaje się do dużych zestawów danych <br/>&bull;Działa tylko wtedy, gdy źródłem jest tabela Cassandra|
|W trybie offline|[Kopiuj tabelę z iskrą](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;Może korzystać z możliwości platformy Spark w celu zrównania transformacji i pozyskiwania <br/>&bull;Wymaga konfiguracji z niestandardową zasadą ponawiania próby do obsługi przepustnic|
|Online|[Striim (od Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;Współpracuje z wieloma źródłami, takimi jak Oracle, DB2, SQL Server <br/>&bull;Łatwe tworzenie rurociągów ETL i zapewnia pulpit nawigacyjny do monitorowania <br/>&bull;Obsługa większych zestawów danych <br/>&bull;Ponieważ jest to narzędzie innej firmy, należy je kupić w witrynie marketplace i zainstalować w środowisku użytkownika.|
|Online|[Blitzz (od Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull;Obsługa większych zestawów danych <br/>&bull;Ponieważ jest to narzędzie innej firmy, należy je kupić w witrynie marketplace i zainstalować w środowisku użytkownika.|

## <a name="other-apis"></a>Inne interfejsy API
W przypadku interfejsów API innych niż interfejs API SQL, interfejs API Mongo i interfejs API Cassandra istnieją różne narzędzia obsługiwane przez każdy z istniejących ekosystemów interfejsu API. 

**Interfejs API tabel** 
* [Narzędzie do migracji danych](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzCopy (Polski)](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Gremlin API**
* [Biblioteka zbiorczych executorów wykresu](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Iskra Gremlin](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej, wypróbowywając przykładowe aplikacje zużywające bibliotekę zbiorczą executora w [programach .NET](bulk-executor-dot-net.md) i [Java](bulk-executor-java.md). 
* Biblioteka executora zbiorczego jest zintegrowana ze łącznikiem platformy Spark usługi Cosmos DB, aby dowiedzieć się więcej, zobacz artykuł [łącznika platformy Azure Cosmos DB Spark.](spark-connector.md)  
* Skontaktuj się z zespołem produktów usługi Azure Cosmos DB, otwierając bilet pomocy technicznej w obszarze typu problemu "Poradnik ogólny" i podtyp problemu "Duże (TB+), aby uzyskać dodatkową pomoc dotyczącą migracji na dużą skalę.
