---
title: Kopiowanie danych z bazy danych Cassandra przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z bazy danych Cassandra do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
ms.openlocfilehash: 743dad6032547f8f535543413adff416efb56ac0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640093"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Kopiowanie danych z bazy danych Cassandra przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Bieżąca wersja](connector-cassandra.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory, aby skopiować dane z bazy danych Cassandra. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z bazy danych Cassandra do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności obsługuje ten łącznik bazy danych Cassandra:

- Bazy danych Cassandra **wersji 2.x i 3.x**.
- Kopiowanie danych przy użyciu **podstawowe** lub **anonimowe** uwierzytelniania.

>[!NOTE]
>Działania uruchomione na własne środowisko IR, bazy danych Cassandra 3.x jest obsługiwana, ponieważ środowisko IR w wersji 3.7 lub nowszym.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skopiować dane z bazy danych Cassandra, który nie jest dostępny publicznie, musisz skonfigurować środowiskiem Integration Runtime. Zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby poznać szczegóły. Infrastruktura Integration Runtime zapewnia wbudowane sterownik Cassandra, dlatego nie trzeba ręcznie zainstalować dowolnego sterownika podczas kopiowania danych z i do bazy danych Cassandra.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do bazy danych Cassandra łącznika.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla bazy danych Cassandra połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Właściwość type musi być równa: **Cassandra** |Yes |
| host |Jeden lub więcej adresów IP lub nazw hostów serwerów bazy danych Cassandra.<br/>Określ rozdzielaną przecinkami listę adresów IP lub nazw hostów, połączyć się z wszystkich serwerów jednocześnie. |Yes |
| port |Port TCP, którego serwer Cassandra korzysta do nasłuchiwania połączeń klientów. |Nie (wartość domyślna to 9042) |
| authenticationType | Typ uwierzytelniania używany do łączenia z bazą danych Cassandra.<br/>Dozwolone wartości to: **Podstawowe**, i **anonimowe**. |Yes |
| username |Określ nazwę użytkownika dla konta użytkownika. |Tak, jeśli element authenticationType ustawiany jest podstawowy. |
| password |Określ hasło dla konta użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Tak, jeśli element authenticationType ustawiany jest podstawowy. |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Używając środowiskiem Integration Runtime lub Azure Integration Runtime (Jeśli magazyn danych jest publicznie dostępny). Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

>[!NOTE]
>Połączenie bazy danych Cassandra przy użyciu protokołu SSL nie jest obecnie obsługiwane.

**Przykład:**

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "Cassandra",
        "typeProperties": {
            "host": "<host>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych bazy danych Cassandra.

Aby skopiować dane z bazy danych Cassandra, należy ustawić właściwość typu zestawu danych na **CassandraTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **CassandraTable** | Yes |
| keyspace |Nazwa przestrzeni kluczy lub schemat bazy danych Cassandra. |Nie (Jeśli określono parametr "zapytanie" do "CassandraSource") |
| tableName |Nazwa tabeli w bazie danych Cassandra. |Nie (Jeśli określono parametr "zapytanie" do "CassandraSource") |

**Przykład:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania


Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło bazy danych Cassandra.

### <a name="cassandra-as-source"></a>Bazy danych Cassandra jako źródło

Aby skopiować dane z bazy danych Cassandra, należy ustawić typ źródłowego w działaniu kopiowania, aby **CassandraSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **CassandraSource** | Yes |
| query |Użyj zapytania niestandardowe można odczytać danych. Zapytanie SQL 92 lub zapytanie języka CQL. Zobacz [odwołanie do języka CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Korzystając z zapytania SQL, określ **nazwa name.table przestrzeń kluczy** do reprezentowania tabeli, które chcesz zbadać. |Nie (Jeśli zostanie określona "nazwa tableName" i "przestrzeń kluczy" w zestawie danych). |
| consistencyLevel |Poziom spójności Określa, jak wiele replik musi odpowiadać na żądania odczytu przed zwróceniem danych do aplikacji klienckiej. Bazy danych Cassandra sprawdza określoną liczbę replik dla danych do spełnienia żądania odczytu. Zobacz [Konfigurowanie spójności danych](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) Aby uzyskać szczegółowe informacje.<br/><br/>Dozwolone wartości to: **JEDEN**, **dwóch**, **trzy**, **KWORUM**, **wszystkich**, **LOCAL_QUORUM**, **EACH_QUORUM**, i **LOCAL_ONE**. |Nie (wartość domyślna to `ONE`) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromCassandra",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cassandra input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CassandraSource",
                "query": "select id, firstname, lastname from mykeyspace.mytable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-cassandra"></a>Typ danych mapowania dla bazy danych Cassandra

Podczas kopiowania danych z bazy danych Cassandra, następujące mapowania są używane z typów danych bazy danych Cassandra do typów danych tymczasowych usługi Azure Data Factory. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ danych Cassandra | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| ASCII |String |
| BIGINT |Int64 |
| BLOB |Byte[] |
| BOOLEAN |Boolean |
| DECIMAL |Decimal |
| DOUBLE |Double |
| FLOAT |Single |
| INET |String |
| INT |Int32 |
| TEXT |String |
| TIMESTAMP |DateTime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |String |
| VARINT |Decimal |

> [!NOTE]
> Kolekcja typów (mapa, zestaw, listy itp.), można znaleźć [pracują z typami kolekcji bazy danych Cassandra przy użyciu tabeli wirtualnej](#work-with-collections-using-virtual-table) sekcji.
>
> Typy zdefiniowane przez użytkownika nie są obsługiwane.
>
> Długość binarne kolumny i kolumny typu String długości nie może być większa niż 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Praca z kolekcji za pomocą tabeli wirtualnej

Usługa Azure Data Factory używa wbudowanego sterownika ODBC, aby nawiązać połączenie i kopiowanie danych z bazy danych Cassandra. Dla typów kolekcji, łącznie z mapy, zestaw i listy sterownik renormalizes dane do odpowiedniego tabele wirtualne. W szczególności jeśli tabela zawiera żadnych kolumn kolekcji, sterownik generuje następujące tabele wirtualne:

* A **tabeli podstawowej**, który zawiera te same dane jako tabelę rzeczywistych, z wyjątkiem kolumn kolekcji. Tabela podstawowa używa tej samej nazwie jako rzeczywistych tabeli, który reprezentuje.
* A **tabeli wirtualnej** dla każdej kolumny kolekcji, która rozszerza zagnieżdżone dane. Wirtualne tabel, które reprezentują kolekcje są nazywane przy użyciu nazwy tabeli rzeczywistych separator "*vt*" i nazwę kolumny.

Tabele wirtualne odnoszą się do danych w tabeli rzeczywistych Włączanie sterownik dostępu do danych nieznormalizowany. Zobacz przykład sekcję, aby uzyskać szczegółowe informacje. Wykonywanie zapytań i przyłączanie tabel wirtualnego, ma dostęp do zawartości, kolekcji bazy danych Cassandra.

### <a name="example"></a>Przykład

Na przykład poniższy "ExampleTable" jest tabeli bazy danych Cassandra, która zawiera całkowitą kolumna klucza podstawowego o nazwie "pk_int", nazwanej wartości kolumny tekstowej, kolumna listy, kolumna mapy i zestawu kolumn (o nazwie "StringSet").

| pk_int | Wartość | List | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"przykład: wartość 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"przykład value 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Sterownik wygeneruje wiele tabel wirtualnego do reprezentowania jednej tabeli. Kolumny klucza obcego w tabelach wirtualnych odwoływać się do kolumny klucza podstawowego w tabeli rzeczywistych i wskazać, który wiersz tabeli rzeczywistych wiersz tabeli wirtualnej odpowiada.

Pierwsza tabela wirtualnych znajduje się tabela bazowa o nazwie "ExampleTable" przedstawiono w poniższej tabeli: 

| pk_int | Wartość |
| --- | --- |
| 1 |"przykład: wartość 1" |
| 3 |"przykład value 3" |

Tabela podstawowa zawiera te same dane, co oryginalnej tabeli bazy danych, z wyjątkiem kolekcji, które są pomijane w tej tabeli i rozwinięty w innych tabelach wirtualnych.

W poniższych tabelach przedstawiono tabele wirtualne, które ponownie normalizować dane z listy, mapy i StringSet kolumn. Kolumny z nazwami, które kończą się "_index" lub "_klucz" wskazują pozycji danych w oryginalnej listy lub mapy. Kolumny z nazwami, które kończą się "_wartość" zawierają rozwiniętej danych z kolekcji.

**Tabela "ExampleTable_vt_List":**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**Tabela "ExampleTable_vt_Map":**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

**Tabela "ExampleTable_vt_StringSet":**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
