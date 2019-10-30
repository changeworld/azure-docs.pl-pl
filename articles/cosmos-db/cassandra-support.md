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
ms.openlocfilehash: b48384b8333a5affc76d8af8e057bd911f14068b
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043486"
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
* Funkcje agregujące
  * min, Max, AVG, Count
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
  


## <a name="cassandra-api-limits"></a>Limity interfejs API Cassandra

Interfejs API Cassandra usługi Azure Cosmos DB nie ma żadnych ograniczeń dotyczących rozmiaru danych przechowywanych w tabeli. Można przechowywać setki terabajtów lub petabajtów danych przy zapewnieniu uznania limitów klucza partycji. Podobnie każdy odpowiednik jednostki lub wiersza nie ma żadnych ograniczeń dotyczących liczby kolumn, jednak łączny rozmiar jednostki nie powinien przekraczać 2 MB. Dane na klucz partycji nie mogą być dłuższe niż 10 GB, podobnie jak w przypadku wszystkich innych interfejsów API.

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

* Utwórz miejsce na dysku (ustawienia replikacji dla tego polecenia są ignorowane)
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* AKTUALIZACJA 
* BATCH — obsługiwane są tylko polecenia nierejestrowane 
* DELETE

Wszystkie operacje CRUD wykonywane za poorednictwem zgodnego z CQLV4 zestaw SDK zwróci dodatkowe informacje na temat błędu, zużyte jednostki żądania. Polecenia Delete i Update muszą być obsługiwane z uwzględnieniem nadzoru zasobów, aby zapewnić prawidłowe korzystanie z zainicjowanej przepływności. 
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

Interfejs API Cassandra usługi Azure Cosmos DB zapewnia możliwość wyboru spójności operacji odczytu.  Mapowanie spójności jest szczegółowo opisane w [tym miejscu](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-across-apis#cassandra-mapping).

## <a name="permission-and-role-management"></a>Zarządzanie uprawnieniami i rolami

Azure Cosmos DB obsługuje kontrolę dostępu opartą na rolach (RBAC) na potrzeby aprowizacji, obracania klawiszy, wyświetlania metryk oraz haseł do odczytu i zapisu oraz kluczy, które można uzyskać za pośrednictwem [Azure Portal](https://portal.azure.com). Azure Cosmos DB nie obsługuje ról dla działań CRUD.

## <a name="keyspace-and-table-options"></a>Opcje przestrzeni kluczy i tabeli

Opcje dla nazwy regionu, klasy, replication_factor i centrum danych w poleceniu "Utwórz przestrzeń kluczy" są obecnie ignorowane. System używa podstawowej metody replikacji [dystrybucji](https://docs.microsoft.com/en-us/azure/cosmos-db/global-dist-under-the-hood) Azure Cosmos DB, aby dodać regiony. Jeśli potrzebujesz obecności danych między regionami, możesz ją włączyć na poziomie konta przy użyciu programu PowerShell, interfejsu wiersza polecenia lub portalu, aby dowiedzieć się więcej, zobacz artykuł [jak dodać regiony](how-to-manage-database-account.md#addremove-regions-from-your-database-account) . Nie można wyłączyć Durable_writes, ponieważ Azure Cosmos DB gwarantuje, że każdy zapis jest trwały. W każdym regionie Azure Cosmos DB replikuje dane w zestawie replik, który składa się z 4 replik i nie można zmodyfikować tej [konfiguracji](global-dist-under-the-hood.md) zestawu replik.
 
Wszystkie opcje są ignorowane podczas tworzenia tabeli, z wyjątkiem gc_grace_seconds, które powinny mieć wartość zero.
Przestrzeń kluczy i tabela mają dodatkową opcję o nazwie "cosmosdb_provisioned_throughput" o minimalnej wartości 400 RU/s. Przepływność przestrzeni kluczy umożliwia udostępnianie przepływności w wielu tabelach i jest przydatne w scenariuszach, gdy wszystkie tabele nie korzystają z zainicjowanej przepływności. Polecenie ALTER TABLE umożliwia zmianę zainicjowanej przepływności w regionach. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```


## <a name="usage-of-cassandra-retry-connection-policy"></a>Użycie zasad połączenia Cassandra retry

Azure Cosmos DB jest systemem zarządzanym przez zasoby. Oznacza to, że można wykonać określoną liczbę operacji w danej sekundzie na podstawie jednostek żądań używanych przez operacje. Jeśli aplikacja przekroczy ten limit w danej sekundzie, żądania są ograniczone, a wyjątki zostaną zgłoszone. Interfejs API Cassandra w Azure Cosmos DB tłumaczy te wyjątki na przeciążone błędy w protokole natywnym Cassandra. Aby upewnić się, że aplikacja może przechwycić i ponowić próbę żądania w przypadku ograniczenia szybkości przypadków, dostępne są rozszerzenia [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) i [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) . Jeśli używasz innych zestawów SDK do uzyskiwania dostępu do interfejs API Cassandra w Azure Cosmos DB, Utwórz zasady połączenia, aby ponowić próbę wykonania tych wyjątków.

## <a name="next-steps"></a>Następne kroki

- Rozpocznij od [utworzenia konta, bazy danych i tabeli interfejsu API Cassandra](create-cassandra-api-account-java.md) przy użyciu aplikacji w języku Java

