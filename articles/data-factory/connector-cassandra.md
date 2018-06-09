---
title: Kopiowanie danych z Cassandra przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z Cassandra do zbiornika obsługiwane magazyny danych za pomocą działania kopiowania w potoku fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
ms.openlocfilehash: 94312edaa97a5d9a7502eed4c0551151ce2a06cc
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235281"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Kopiowanie danych z Cassandra przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-onprem-cassandra-connector.md)
> * [Wersja 2 — wersja zapoznawcza](connector-cassandra.md)

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z bazy danych Cassandra. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.


> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [Cassandra łącznika w wersji 1](v1/data-factory-onprem-cassandra-connector.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z bazy danych Cassandra żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik Cassandra obsługuje:

- Cassandra **wersji 2.x i 3.x**.
- Kopiowanie danych przy użyciu **podstawowe** lub **anonimowe** uwierzytelniania.

>[!NOTE]
>Dla działania systemem obsługi integracji Self-hosted, Cassandra 3.x jest obsługiwana, ponieważ IR wersji 3.7 lub nowszym.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skopiować dane z bazy danych Cassandra, który nie jest dostępny publicznie, należy skonfigurować środowisko uruchomieniowe Self-hosted integracji. Zobacz [środowiska uruchomieniowego integracji Self-hosted](create-self-hosted-integration-runtime.md) artykuł, aby poznać szczegóły. Środowiska uruchomieniowego integracji zawiera wbudowane sterownik Cassandra, dlatego nie trzeba ręcznie zainstalowania sterownika podczas kopiowania danych z/na Cassandra.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do Cassandra łącznika.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Cassandra połączone usługi, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Właściwość type musi mieć ustawioną: **Cassandra** |Yes |
| host |Jeden lub więcej adresów IP lub nazw hostów serwerów Cassandra.<br/>Określ rozdzielaną przecinkami listę adresów IP lub nazw hostów, aby nawiązać połączenie wszystkie serwery jednocześnie. |Yes |
| port |Port TCP używany przez serwer Cassandra nasłuchiwanie dla połączeń klienta. |Nie (wartość domyślna to 9042) |
| Typ authenticationType | Typ uwierzytelniania używany do łączenia z bazą danych Cassandra.<br/>Dozwolone wartości to: **podstawowe**, i **anonimowe**. |Yes |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Tak, jeśli authenticationType ustawiany jest podstawowy. |
| hasło |Określ hasło dla konta użytkownika. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Tak, jeśli authenticationType ustawiany jest podstawowy. |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych jest dostępny publicznie) można użyć środowiska uruchomieniowego integracji Self-hosted lub środowiska uruchomieniowego integracji Azure. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

>[!NOTE]
>Połączenie Cassandra przy użyciu protokołu SSL nie jest obecnie obsługiwane.

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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje zobacz artykuł zestawów danych. Ta sekcja zawiera listę obsługiwanych przez zestaw danych Cassandra właściwości.

Aby skopiować dane z Cassandra, ustaw właściwość Typ zestawu danych do **CassandraTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type zestawu danych: **CassandraTable** | Yes |
| przestrzeni kluczy |Nazwa schematu bazy danych Cassandra lub przestrzeni kluczy. |Nie (Jeśli określono parametr "zapytania" dla "CassandraSource") |
| tableName |Nazwa tabeli w bazie danych Cassandra. |Nie (Jeśli określono parametr "zapytania" dla "CassandraSource") |

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


Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez źródło Cassandra właściwości.

### <a name="cassandra-as-source"></a>Cassandra jako źródło

Aby skopiować dane z Cassandra, należy ustawić typ źródła w przypadku działania kopiowania do **CassandraSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **CassandraSource** | Yes |
| query |Użyj niestandardowych zapytania można odczytać danych. |Zapytania SQL 92 lub CQL zapytania. Zobacz [odwołania CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Korzystając z zapytania SQL, określ **przestrzeni kluczy name.table nazwy** do reprezentowania tabeli ma dotyczyć zapytanie. |Nie (Jeśli określono "Nazwa_tabeli" i "przestrzeni kluczy" w zestawie danych). |
| consistencyLevel |Poziom spójności Określa, jak wiele replik musi odpowiedzieć na żądanie odczytu przed zwróceniem danych do aplikacji klienckiej. Cassandra sprawdza określonej liczby replik danych do spełnienia żądania odczytu. Zobacz [Konfigurowanie spójność danych](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) szczegółowe informacje.<br/><br/>Dozwolone wartości to: **jeden**, **dwóch**, **trzy**, **KWORUM**, **wszystkie**, **LOCAL_ KWORUM**, **EACH_QUORUM**, i **LOCAL_ONE**. |Nie (domyślnie jest `ONE`) |

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

## <a name="data-type-mapping-for-cassandra"></a>Mapowanie dla Cassandra typu danych

Podczas kopiowania danych z Cassandra, następujące mapowania są używane z Cassandra typów danych do typów danych tymczasowych fabryki danych Azure. Zobacz [schemat i dane typu mapowania](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typu źródłowego: schemat i dane sink.

| Typ danych Cassandra | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| ASCII |Ciąg |
| BIGINT |Int64 |
| OBIEKT BLOB |Byte[] |
| WARTOŚĆ LOGICZNA |Wartość logiczna |
| DECIMAL |Decimal |
| O PODWÓJNEJ PRECYZJI |O podwójnej precyzji |
| FLOAT |Pojedyncze |
| INET |Ciąg |
| INT |Int32 |
| TEKST |Ciąg |
| ZNACZNIK CZASU |DateTime |
| TIMEUUID |Identyfikator GUID |
| IDENTYFIKATOR UUID |Identyfikator GUID |
| VARCHAR |Ciąg |
| VARINT |Decimal |

> [!NOTE]
> Kolekcja typów (mapy, zestaw, listy itp.), można znaleźć w [współpracy z typami kolekcji Cassandra za pomocą tabeli wirtualnej](#work-with-collections-using-virtual-table) sekcji.
>
> Typy definiowane przez użytkownika nie są obsługiwane.
>
> Długość kolumny typu Binary i kolumny typu String długości nie może być większa niż 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Praca z kolekcji za pomocą tabeli wirtualnej

Fabryka danych Azure używa wbudowanego sterownika ODBC do nawiązania połączenia, a następnie skopiować dane z bazy danych Cassandra. Dla typów kolekcji, w tym mapy, zestaw i listy sterownik renormalizes dane w odpowiadających jej tabel wirtualnego. W szczególności jeśli tabela zawiera wszystkie kolumny w kolekcji, sterownik generuje następujące tabele wirtualne:

* A **tabeli podstawowej**, który zawiera te same dane jako prawdziwe tabeli z wyjątkiem kolumny kolekcji. Tabela podstawowa używa tej samej nazwie jako prawdziwe tabeli, która reprezentuje.
* A **tabeli wirtualnej** dla każdej kolekcji kolumny, która rozszerza zagnieżdżone dane. Wirtualny tabel, które reprezentują kolekcje są nazywane przy użyciu nazwy tabeli rzeczywistych separator "*vt*" i nazwę kolumny.

Tabele wirtualne odwołują się do danych w tabeli prawdziwe, włączanie dostępu do danych nieznormalizowany sterownika. Zobacz sekcję przykład, aby uzyskać szczegółowe informacje. Ma dostęp do zawartości Cassandra kolekcji, zapytań i przyłączanie tabel wirtualnego.

### <a name="example"></a>Przykład

Na przykład następujące "ExampleTable" jest Cassandra tabeli bazy danych, która zawiera całkowitą kolumna klucza podstawowego o nazwie "pk_int", kolumna tekst o nazwie wartość kolumny listy, kolumny mapy i zestawu kolumn (o nazwie "StringSet").

| pk_int | Wartość | List | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"Przykładowa wartość 1" |["1", "2", "3"] |{"S1": "", "S2": "b"} |{"A", "B", "C"} |
| 3 |"przykład value 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Sterownik przetwarzający generuje wiele tabel wirtualnego do reprezentowania tej pojedynczej tabeli. Kolumny klucza obcego w tabeli wirtualnej odwołania kolumn klucza podstawowego w tabeli rzeczywistych i wskazać, który odpowiada wiersz tabeli wirtualnej wiersz tabeli prawdziwe.

Podstawowa tabela o nazwie "ExampleTable" przedstawiono w poniższej tabeli znajduje się pierwszy tabeli wirtualnej: 

| pk_int | Wartość |
| --- | --- |
| 1 |"Przykładowa wartość 1" |
| 3 |"przykład value 3" |

Podstawowa tabela zawiera te same dane jako oryginalnej tabeli bazy danych, z wyjątkiem kolekcje, które są pominięte w tej tabeli i rozwinięty w innych tabelach wirtualnych.

W poniższych tabelach przedstawiono wirtualnego tabel, które renormalize danych z listy, mapy i StringSet kolumn. Kolumny o nazwach kończyć "_index" lub "_klucza" wskazać pozycję danych w oryginalnej listy lub mapy. Kolumny o nazwach kończących się znakiem "_value" zawierają rozszerzonych danych z kolekcji.

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
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
