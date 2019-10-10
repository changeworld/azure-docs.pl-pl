---
title: Opcje migracji Cosmos DB
description: W tym dokumencie opisano różne opcje migracji danych lokalnych lub w chmurze do Azure Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: bharathb
ms.openlocfilehash: 0a2423421c6dfda02646546a6e071c8c78396f2c
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170687"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Opcje migracji danych lokalnych lub w chmurze do Azure Cosmos DB

Dane z różnych źródeł danych można ładować do Azure Cosmos DB. Ponadto, ponieważ Azure Cosmos DB obsługuje wiele interfejsów API, obiekty docelowe mogą być dowolnymi z istniejących interfejsów API. Aby można było obsługiwać ścieżki migracji z różnych źródeł do różnych Azure Cosmos DB interfejsów API, istnieje wiele rozwiązań, które zapewniają wyspecjalizowaną obsługę dla każdej ścieżki migracji. Ten dokument zawiera listę dostępnych rozwiązań oraz opis ich zalet i ograniczeń.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Czynniki wpływające na wybór narzędzia migracji

Wybór narzędzia migracji zależy od następujących czynników:
* **Migracja**w trybie online i offline: wiele narzędzi migracji udostępnia ścieżkę do przeprowadzenia jednorazowej migracji. Oznacza to, że aplikacje uzyskujące dostęp do bazy danych mogą być wykorzystane przez pewien czas przestoju. Niektóre rozwiązania migracji umożliwiają przeprowadzenie migracji na żywo, w której jest skonfigurowany potok replikacji między źródłem a obiektem docelowym.

* **Źródło danych**: istniejące dane mogą znajdować się w różnych źródłach danych, takich jak Oracle DB2, DataStax Cassanda, Azure SQL Server, PostgreSQL itd. Dane mogą również znajdować się w istniejącym koncie Azure Cosmos DB i zamiar migracji może zmienić model danych lub ponownie podzielić dane w kontener z innym kluczem partycji.

* **Azure Cosmos DB API**: w przypadku interfejsu API SQL w programie Azure Cosmos DB istnieją różne narzędzia opracowane przez zespół Azure Cosmos DB, który pomaga w różnych scenariuszach migracji. Wszystkie inne interfejsy API mają własny, wyspecjalizowany zestaw narzędzi opracowanych i konserwowanych przez społeczność. Ponieważ Azure Cosmos DB obsługuje te interfejsy API na poziomie protokołu przewodowego, narzędzia te powinny być wykonywane w trakcie migracji danych do Azure Cosmos DB. Mogą jednak wymagać niestandardowej obsługi dla ograniczania przepustowości, ponieważ ta koncepcja jest specyficzna dla Azure Cosmos DB.

* **Rozmiar danych**: większość narzędzi do migracji działa bardzo dobrze w przypadku mniejszych zestawów danych. Gdy zestaw danych przekracza kilka setek gigabajtów, Opcje narzędzi migracji są ograniczone. 

* **Oczekiwany czas trwania migracji**: migracje można skonfigurować tak, aby odbywały się w powolnym, przyrostowym tempie, który zużywa mniejszą przepływność, lub zużywać całą przepustowość zainicjowaną w docelowym kontenerze Azure Cosmos DB i ukończyć migrację w mniej pierwszym.

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB — interfejs SQL API
|**Typ migracji**|**Rozwiązanie**|**Zagadnienia do rozważenia**|
|---------|---------|---------|
|Stanie|[Narzędzie do migracji danych](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; Łatwa konfiguracja i obsługa wielu źródeł <br/>&bull; nieodpowiedni dla dużych zestawów danych|
|Stanie|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; Łatwa konfiguracja i obsługa wielu źródeł <br/>&bull; wykorzystuje bibliotekę wykonawców zbiorczych Azure Cosmos DB <br/>&bull; odpowiednie dla dużych zestawów danych <br/>&bull; brak punktów kontrolnych — oznacza to, że w przypadku wystąpienia problemu w trakcie migracji należy ponownie uruchomić cały proces migracji.<br/>&bull; braku kolejki utraconych wiadomości — oznacza to, że kilka błędnych plików może zatrzymać cały proces migracji.|
|Stanie|[Łącznik Azure Cosmos DB Spark](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull; wykorzystuje bibliotekę wykonawców zbiorczych Azure Cosmos DB <br/>&bull; odpowiednie dla dużych zestawów danych <br/>&bull; potrzebuje niestandardowej konfiguracji platformy Spark <br/>&bull; Spark jest wrażliwa na niespójności schematu i może to być problem podczas migracji |
|Stanie|[Narzędzie niestandardowe z Cosmos DB zbiorczego wykonawcy biblioteki](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull; zawiera punkty kontrolne, funkcje obsługi utraconych wiadomości, które zwiększają odporność migracji <br/>&bull; odpowiednie dla bardzo dużych zestawów danych (10 TB +)  <br/>&bull; wymaga instalacji niestandardowej tego narzędzia działającego jako App Service |
|Online|[Funkcje Cosmos DB i interfejs API ChangeFeed](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull; Łatwa konfiguracja <br/>&bull; działa tylko wtedy, gdy źródło jest kontenerem Azure Cosmos DB <br/>&bull; nieodpowiedni dla dużych zestawów danych <br/>&bull; nie przechwytuje usunięć z kontenera źródłowego |
|Online|[Niestandardowa usługa migracji przy użyciu ChangeFeed](https://aka.ms/CosmosDBMigrationSample)|&bull; przedstawia śledzenie postępu <br/>&bull; działa tylko wtedy, gdy źródło jest kontenerem Azure Cosmos DB <br/>&bull; działa również w przypadku większych zestawów danych <br/>&bull; wymaga, aby użytkownik skonfigurował App Service do obsługi procesora źródła zmian <br/>&bull; nie przechwytuje usunięć z kontenera źródłowego|
|Online|[Striim](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull; współpracuje z wieloma różnymi źródłami, takimi jak Oracle, DB2, SQL Server <br/>&bull; łatwa kompilacja potoków ETL i udostępnia pulpit nawigacyjny do monitorowania <br/>&bull; obsługuje większe zestawy danych <br/>&bull; ponieważ jest to narzędzie innej firmy, należy je zakupić w witrynie Marketplace i zainstalować w środowisku użytkownika|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB interfejs API Mongo
|**Typ migracji**|**Rozwiązanie**|**Zagadnienia do rozważenia**|
|---------|---------|---------|
|Stanie|[Narzędzie do migracji danych](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; Łatwa konfiguracja i obsługa wielu źródeł <br/>&bull; nieodpowiedni dla dużych zestawów danych|
|Stanie|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; Łatwa konfiguracja i obsługa wielu źródeł <br/>&bull; wykorzystuje bibliotekę wykonawców zbiorczych Azure Cosmos DB <br/>&bull; odpowiednie dla dużych zestawów danych <br/>&bull; brak punktów kontrolnych oznacza, że każdy problem w trakcie migracji wymaga ponownego uruchomienia całego procesu migracji<br/>&bull; braku kolejki utraconych wiadomości oznacza, że kilka błędnych plików może zatrzymać cały proces migracji <br/>&bull; wymaga niestandardowego kodu w celu zwiększenia przepływności odczytu dla niektórych źródeł danych|
|Stanie|[Istniejące narzędzia Mongo (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; Łatwa konfiguracja i integracja <br/>&bull; wymaga obsługi niestandardowej dla ograniczania przepustowości|
|Online|[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull; wykorzystuje bibliotekę wykonawców zbiorczych Azure Cosmos DB <br/>&bull; odpowiednie dla dużych zestawów danych i należy zachować ostrożność replikowania zmian na żywo <br/>&bull; działa tylko z innymi źródłami MongoDB|

## <a name="azure-cosmos-db-cassandra-api"></a>Interfejs Azure Cosmos DB Cassandra API
|**Typ migracji**|**Rozwiązanie**|**Zagadnienia do rozważenia**|
|---------|---------|---------|
|Stanie|[cqlsh — polecenie kopiowania](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull; Łatwa konfiguracja <br/>&bull; nieodpowiedni dla dużych zestawów danych <br/>&bull; działa tylko wtedy, gdy źródłem jest tabela Cassandra|
|Stanie|[Kopiuj tabelę za pomocą platformy Spark](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull; może korzystać z możliwości platformy Spark w celu zrównoleglanie transformacji i pozyskiwania <br/>&bull; wymaga konfiguracji z niestandardowymi zasadami ponawiania, aby obsłużyć ograniczenia|
|Online|[Striim (z Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull; współpracuje z wieloma różnymi źródłami, takimi jak Oracle, DB2, SQL Server <br/>&bull; łatwa kompilacja potoków ETL i udostępnia pulpit nawigacyjny do monitorowania <br/>&bull; obsługuje większe zestawy danych <br/>&bull; ponieważ jest to narzędzie innej firmy, należy je zakupić w witrynie Marketplace i zainstalować w środowisku użytkownika|
|Online|[Blitzz (z Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull; obsługuje większe zestawy danych <br/>&bull; ponieważ jest to narzędzie innej firmy, należy je zakupić w witrynie Marketplace i zainstalować w środowisku użytkownika|

## <a name="other-apis"></a>Inne interfejsy API
W przypadku interfejsów API innych niż interfejs API SQL, interfejs API Mongo i interfejs API Cassandra są dostępne różne narzędzia obsługiwane przez każdy z istniejących ekosystemów interfejsu API. 

**Interfejs API tabel** 
* [Narzędzie do migracji danych](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [Narzędzie AzCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Interfejs API Gremlin**
* [Biblioteka wykonawców zbiorczych wykresu](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej, pobierając przykładowe aplikacje zużywające zbiorczą bibliotekę wykonawczą w programie [.NET i środowisku](bulk-executor-dot-net.md) [Java](bulk-executor-java.md). 
* Biblioteka wykonawców zbiorczych jest zintegrowana z łącznikiem Cosmos DB Spark, aby dowiedzieć się więcej, zobacz Azure Cosmos DB artykuł dotyczący [łącznika Spark](spark-connector.md) .  
* Skontaktuj się z zespołem produktu Azure Cosmos DB, otwierając bilet pomocy technicznej w ramach typu problemu "ogólny poradnik" i "duże (TB +) migracje", aby uzyskać dodatkową pomoc dotyczącą migracji z dużą skalą. 
* Użyj [programu Bootstrap Cosmos DB](https://azurecosmosdb.github.io/CosmosBootstrap/) , aby przyspieszyć Kompilowanie lub Migrowanie aplikacji na Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Program Cosmos DB Bootstrap](https://azurecosmosdb.github.io/CosmosBootstrap/)
