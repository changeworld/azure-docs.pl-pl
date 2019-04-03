---
title: Funkcje i polecenia bazy danych Apache Cassandra obsługiwane przez interfejs API Cassandra usługi Azure Cosmos DB
description: Dowiedz się więcej o obsłudze funkcji bazy danych Apache Cassandra w interfejsie API Cassandra usługi Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 46eea21e1eafce1696ed1cf77a1f334798f0bc17
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58848399"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Funkcje bazy danych Apache Cassandra obsługiwane przez interfejs API Cassandra usługi Azure Cosmos DB 

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Możesz komunikować się z interfejsem API Cassandra usługi Azure Cosmos DB przy użyciu [protokołu przewodowego](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) języka Cassandra Query Language (CQL) w wersji 4 zgodnego ze [sterownikami](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) klienta Cassandra typu open source. 

Dzięki interfejsowi API Cassandra usługi Azure Cosmos DB możesz korzystać z interfejsów API Apache Cassandra, a także możliwości klasy przedsiębiorstwa zapewnianymi przez usługę Azure Cosmos DB. Możliwości klasy przedsiębiorstwa obejmują [globalną dystrybucję](distribute-data-globally.md), [automatyczne skalowanie partycjonowania w poziomie](partition-data.md), gwarancje dostępności i opóźnień, szyfrowanie nieużywanych danych, kopie zapasowe i wiele więcej.

## <a name="cassandra-protocol"></a>Protokół Cassandra 

Interfejs API Cassandra usługi Azure Cosmos DB jest zgodny z językiem CQL **w wersji 4**. Obsługiwane polecenia CQL, narzędzia, ograniczenia i wyjątki są wymienione poniżej. Dowolny sterownik klienta działający zgodnie z tymi protokołami umożliwia połączenie z interfejsem API Cassandra usługi Azure Cosmos DB.

## <a name="cassandra-driver"></a>Sterownik Cassandra

Następujące wersje sterowników Cassandra są obsługiwane przez interfejs API Cassandra usługi Azure Cosmos DB:

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 
## <a name="cql-data-types"></a>Typy danych CQL 

Interfejs API Cassandra usługi Azure Cosmos DB obsługuje następujące typy danych języka CQL:

* ascii  
* bigint  
* blob  
* wartość logiczna  
* counter  
* date  
* decimal  
* double  
* float  
* frozen  
* inet  
* int  
* list  
* set  
* smallint  
* tekst  
* time  
* sygnatura czasowa  
* timeuuid  
* tinyint  
* tuple  
* uuid  
* varchar  
* varint  
* tuples  
* udts  
* map  

## <a name="cql-functions"></a>Funkcje języka CQL

Interfejs API Cassandra usługi Azure Cosmos DB obsługuje następujące funkcje języka CQL:

* Token  
* Funkcje konwersji dużych obiektów binarnych 
  * typeAsBlob(value)  
  * blobAsType(value)
* Funkcje identyfikatorów UUID i timeuuid 
  * dateOf()  
  * now()  
  * minTimeuuid()  
  * unixTimestampOf()  
  * toDate(timeuuid)  
  * toTimestamp(timeuuid)  
  * toUnixTimestamp(timeuuid)  
  * toDate(timeuuid)  
  * toUnixTimestamp(timestamp)  
  * toTimestamp(date)  
  * toUnixTimestamp(date)  


## <a name="cassandra-query-language-limits"></a>Ograniczenia języka Cassandra Query Language

Interfejs API Cassandra usługi Azure Cosmos DB nie ma żadnych ograniczeń dotyczących rozmiaru danych przechowywanych w tabeli. Można przechowywać setki terabajtów lub petabajtów danych przy zapewnieniu uznania limitów klucza partycji. Podobnie encja lub równoważnik wiersza nie ma żadnych ograniczeń liczby kolumn, jednak całkowity rozmiar encji nie może przekraczać 2 MB.

## <a name="tools"></a>Narzędzia 

Interfejs API Cassandra usługi Azure Cosmos DB to platforma usług zarządzanych. Nie wymaga żadnego narzutu związanego z zarządzaniem ani narzędzi, takich jak moduł odzyskiwania pamięci, wirtualna maszyna Java (JVM) i narzędzie nodetool do zarządzania klastrem. Obsługuje narzędzia, takie jak cqlsh, korzystające ze zgodności binarnej z językiem CQL w wersji 4. 

* Eksplorator danych witryny Azure Portal, metryki, dzienniki diagnostyczne, program PowerShell i interfejsu wiersza polecenia są innymi obsługiwanymi mechanizmami do zarządzania kontem.

## <a name="cql-shell"></a>Powłoka CQL  

Narzędzie wiersza polecenia CQLSH jest dostarczane z bazą danych Apache Cassandra 3.1.1 i jest gotowe do działania po włączeniu następujących zmiennych środowiskowych:

Przed uruchomieniem następujących poleceń [dodaj certyfikat główny Baltimore do magazynu cacerts](https://docs.microsoft.com/java/azure/java-sdk-add-certificate-ca-store?view=azure-java-stable#to-add-a-root-certificate-to-the-cacerts-store). 

**Windows:** 

```bash
set SSL_VERSION=TLSv1_2 
SSL_CERTIFICATE=<path to Baltimore root ca cert>
set CQLSH_PORT=10350 
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```
**Unix/Linux/Mac:**

```bash
export SSL_VERSION=TLSv1_2 
export SSL_CERTFILE=<path to Baltimore root ca cert>
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```

## <a name="cql-commands"></a>Polecenie języka CQL

Usługa Azure Cosmos DB obsługuje następujące polecenia bazy danych na kontach interfejsu API Cassandra.

* CREATE KEYSPACE 
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* UPDATE 
* BATCH — obsługiwane są tylko polecenia nierejestrowane 
* DELETE

Wszystkie operacje CRUD wykonywane za pośrednictwem zestawu SDK zgodnego z CQLV4 zwracają dodatkowe informacje o błędzie, zużyciu jednostek żądania i identyfikatorze aktywności. Polecenia usuwania i aktualizowania trzeba obsługiwać, biorąc pod uwagę zapewnienie ładu zasobów, aby uniknąć użytkowania ustanowionych zasobów. 
* Uwaga: jeśli wartość gc_grace_seconds została określona, musi być równa zero.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

## <a name="consistency-mapping"></a>Mapowanie spójności 

Interfejs API Cassandra usługi Azure Cosmos DB zapewnia możliwość wyboru spójności operacji odczytu. Wszystkie operacje zapisu, niezależnie od spójności konta, są zawsze zapisywane z użyciem wydajności zapisu umowy ustalonym w umowach SLA.

## <a name="permission-and-role-management"></a>Zarządzanie uprawnieniami i rolami

Usługa Azure Cosmos DB obsługuje kontrolę dostępu opartą na rolach (RBAC) oraz hasła/klucze do odczytu-zapisu i tylko do odczytu, które można uzyskać w witrynie [Azure Portal](https://portal.azure.com). Usługa Azure Cosmos DB nie obsługuje jeszcze użytkowników i ról do aktywności planu danych. 

## <a name="planned-support"></a>Planowana pomoc techniczna 
* Nazwa regionu w poleceniu dotyczącym tworzenia przestrzeni kluczy jest obecnie ignorowana. Dystrybucja danych jest implementowana na podstawowej platformie usługi Cosmos DB i ujawniana za pośrednictwem portalu lub programu PowerShell w ramach konta. 





## <a name="next-steps"></a>Kolejne kroki

- Rozpocznij od [utworzenia konta, bazy danych i tabeli interfejsu API Cassandra](create-cassandra-api-account-java.md) przy użyciu aplikacji w języku Java

