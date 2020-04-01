---
title: Funkcje bazy danych Apache Cassandra obsługiwane przez interfejs API Cassandra usługi Azure Cosmos DB
description: Dowiedz się więcej o obsłudze funkcji bazy danych Apache Cassandra w interfejsie API Cassandra usługi Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 223544f7ceddce6bc2071d561da1cff1c0d4b53b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420151"
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
  * min, maks., śr., liczba
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
  


## <a name="cassandra-api-limits"></a>Ograniczenia interfejsu API Cassandra

Interfejs API Cassandra usługi Azure Cosmos DB nie ma żadnych ograniczeń dotyczących rozmiaru danych przechowywanych w tabeli. Można przechowywać setki terabajtów lub petabajtów danych przy zapewnieniu uznania limitów klucza partycji. Podobnie każda jednostka lub odpowiednik wiersza nie ma żadnych ograniczeń co do liczby kolumn. Jednak całkowity rozmiar jednostki nie powinien przekraczać 2 MB. Dane na klucz partycji nie może przekraczać 20 GB, jak we wszystkich innych interfejsów API.

## <a name="tools"></a>narzędzia 

Interfejs API Cassandra usługi Azure Cosmos DB to platforma usług zarządzanych. Nie wymaga żadnego narzutu związanego z zarządzaniem ani narzędzi, takich jak moduł odzyskiwania pamięci, wirtualna maszyna Java (JVM) i narzędzie nodetool do zarządzania klastrem. Obsługuje narzędzia, takie jak cqlsh, korzystające ze zgodności binarnej z językiem CQL w wersji 4. 

* Eksplorator danych usługi Azure portal, metryki, diagnostyka dziennika, program PowerShell i cli są inne obsługiwane mechanizmy do zarządzania kontem.

## <a name="cql-shell"></a>Powłoka CQL  

CQLSH narzędzie wiersza polecenia pochodzi z Apache Cassandra 3.1.1 i działa po wyjęciu z pudełka, ustawiając niektóre zmienne środowiskowe.

**Windows:**

Jeśli korzystasz z systemu Windows, zalecamy włączenie [systemu plików Windows dla systemu Linux](https://docs.microsoft.com/windows/wsl/install-win10#install-the-windows-subsystem-for-linux). Następnie możesz postępować zgodnie z poniższymi poleceniami linuksowymi.

**Unix/Linux/Mac:**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

## <a name="cql-commands"></a>Polecenie języka CQL

Usługa Azure Cosmos DB obsługuje następujące polecenia bazy danych na kontach interfejsu API Cassandra.

* TWORZENIE PRZESTRZENI KLAWISZY (ustawienia replikacji dla tego polecenia są ignorowane)
* CREATE TABLE 
* TWORZENIE INDEKSU (bez określania nazwy indeksu i indeksów w pełni zablokowanych, które nie są jeszcze obsługiwane)
* ZEZWALAJ NA FILTROWANIE
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* UPDATE 
* BATCH — obsługiwane są tylko polecenia nierejestrowane 
* DELETE

Wszystkie operacje CRUD, które są wykonywane za pośrednictwem CQL v4 zgodny SDK zwróci dodatkowe informacje o błędach i jednostek żądania używane. Polecenia DELETE i UPDATE powinny być obsługiwane z uwzględnieniem zarządzania zasobami, aby zapewnić najbardziej efektywne wykorzystanie aprowizowanej przepływności.

* Uwaga: jeśli wartość gc_grace_seconds została określona, musi być równa zero.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($"CustomPayload:  {key}: {value}"); 
        } 
```

## <a name="consistency-mapping"></a>Mapowanie spójności 

Interfejs API Cassandra usługi Azure Cosmos DB zapewnia możliwość wyboru spójności operacji odczytu.  Mapowanie spójności jest szczegółowo [opisane tutaj](consistency-levels-across-apis.md#cassandra-mapping).

## <a name="permission-and-role-management"></a>Zarządzanie uprawnieniami i rolami

Usługa Azure Cosmos DB obsługuje sterowanie dostępem oparte na rolach (RBAC) do inicjowania obsługi administracyjnej, obracania kluczy, przeglądania metryk oraz haseł/kluczy tylko do odczytu i do odczytu, które można uzyskać za pośrednictwem [portalu Azure.](https://portal.azure.com) Usługa Azure Cosmos DB nie obsługuje ról dla działań CRUD.

## <a name="keyspace-and-table-options"></a>Opcje przestrzeni kluczy i tabeli

Opcje nazwy regionu, klasy, replication_factor i centrum danych w poleceniu "Utwórz przestrzeń kluczy" są obecnie ignorowane. System używa podstawowej metody replikacji [dystrybucji globalnej](global-dist-under-the-hood.md) usługi Azure Cosmos DB w celu dodania regionów. Jeśli potrzebujesz międzyregionowej obecności danych, możesz włączyć je na poziomie konta w programie PowerShell, CLI lub portalu, aby dowiedzieć się więcej, zobacz [artykuł jak dodawać regiony.](how-to-manage-database-account.md#addremove-regions-from-your-database-account) Durable_writes nie można wyłączyć, ponieważ usługa Azure Cosmos DB zapewnia, że każdy zapis jest trwały. W każdym regionie usługa Azure Cosmos DB replikuje dane w zestawie replik składającym się z czterech replik i nie można zmodyfikować tej [konfiguracji](global-dist-under-the-hood.md) zestawu replik.
 
Wszystkie opcje są ignorowane podczas tworzenia tabeli, z wyjątkiem gc_grace_seconds, które powinny być ustawione na zero.
Przestrzeń kluczy i tabela mają dodatkową opcję o nazwie "cosmosdb_provisioned_throughput" o minimalnej wartości 400 RU/s. Przepływność przestrzeni kluczy umożliwia współużytkowanie przepływności w wielu tabelach i jest przydatne w scenariuszach, gdy wszystkie tabele nie są przy użyciu aprowizowanej przepływności. Polecenie Zmień tabelę umożliwia zmianę aprowizowanej przepływności w regionach. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```


## <a name="usage-of-cassandra-retry-connection-policy"></a>Użycie zasad ponawiania próby połączenia bazy danych Cassandra

Usługa Azure Cosmos DB to system zarządzany przez zasoby. Oznacza to, że można wykonać określoną liczbę operacji w danej sekundzie na podstawie jednostek żądań używanych przez operacje. Jeśli aplikacja przekracza ten limit w danym drugim, żądania są ograniczone stawki i zostaną odrzucone wyjątki. Interfejs API Cassandra w usłudze Azure Cosmos DB tłumaczy te wyjątki na przeciążone błędy w protokole macierzystym Cassandra. Aby upewnić się, że aplikacja może przechwytywać i ponawiać żądania w przypadku ograniczenia szybkości, [iskry](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) i rozszerzenia [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) są dostarczane. Jeśli używasz innych zestawów SDK, aby uzyskać dostęp do interfejsu API Cassandra w usłudze Azure Cosmos DB, utwórz zasady połączenia, aby ponowić próbę tych wyjątków.

## <a name="next-steps"></a>Następne kroki

- Rozpocznij od [utworzenia konta, bazy danych i tabeli interfejsu API Cassandra](create-cassandra-api-account-java.md) przy użyciu aplikacji w języku Java

