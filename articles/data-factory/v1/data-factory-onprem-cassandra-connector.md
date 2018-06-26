---
title: Przenoszenia danych z Cassandra przy użyciu fabryki danych | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z Cassandra lokalną bazą danych przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e0d7c2caecba22b30d3e0cf5f80fda5b203e867b
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753476"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Przenoszenia danych z Cassandra lokalną bazą danych przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-onprem-cassandra-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-cassandra.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [Cassandra łącznika w wersji 2](../connector-cassandra.md).

W tym artykule opisano sposób używania działania kopiowania w fabryce danych Azure do przenoszenia danych z lokalną bazą danych Cassandra. Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania.

Można skopiować danych z lokalnego magazynu danych Cassandra żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych obsługiwane jako wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. Fabryka danych aktualnie obsługuje tylko dane przenoszenie, z magazynu danych Cassandra do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych w magazynie danych Cassandra. 

## <a name="supported-versions"></a>Obsługiwane wersje
Łącznik Cassandra obsługuje następujące wersje Cassandra: 2.x i 3.x. Dla działania systemem obsługi integracji Self-hosted, Cassandra 3.x jest obsługiwana, ponieważ IR wersji 3.7 lub nowszym.

## <a name="prerequisites"></a>Wymagania wstępne
Dla usługi fabryka danych Azure można było nawiązać połączenia z lokalną bazą danych Cassandra należy zainstalować bramę zarządzania danymi na tym samym komputerze, który jest hostem bazy danych lub na osobnym komputerze, aby uniknąć konkurowanie o zasoby z bazą danych. Brama zarządzania danymi jest składnikiem, który łączy lokalnych źródeł danych do usługi w chmurze w sposób bezpieczny i zarządzanie nimi. Zobacz [brama zarządzania danymi](data-factory-data-management-gateway.md) artykułu, aby uzyskać więcej informacji dotyczących bramy zarządzania danymi. Zobacz [przenoszenia danych z lokalnymi do chmury](data-factory-move-data-between-onprem-and-cloud.md) artykułu, aby uzyskać instrukcje krok po kroku dotyczące konfigurowania bramy danych potoku do przenoszenia danych.

Należy używać bramy do połączenia z bazą danych Cassandra nawet wtedy, gdy baza danych znajduje się w chmurze, na przykład na maszynie Wirtualnej platformy Azure IaaS. Y może masz bramy na tej samej maszyny Wirtualnej, który jest hostem bazy danych lub na oddzielnych maszynie Wirtualnej tak długo, jak bramy można połączyć z bazą danych.  

Po zainstalowaniu bramy, automatycznie instaluje sterownik Microsoft Cassandra ODBC używane do łączenia z bazą danych Cassandra. W związku z tym nie trzeba ręcznie zainstalowania sterownika na komputerze bramy, podczas kopiowania danych z bazy danych Cassandra. 

> [!NOTE]
> Zobacz [rozwiązywania problemów bramy](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) porady dotyczące rozwiązywania problemów z bramy/połączenia problemy związane z.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działania kopiowania, który przenosi dane z magazynu lokalnego Cassandra danych przy użyciu różnych narzędzi/interfejsów API. 

- Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych. 
- Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager** , **Interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. 

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych:

1. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych.
2. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. 
3. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Dla przykładu z definicji JSON dla jednostek fabryki danych, które służą do kopiowania danych z magazynu danych Cassandra lokalnych, zobacz [przykład JSON: kopiowanie danych z Cassandra do obiektów Blob platformy Azure](#json-example-copy-data-from-cassandra-to-azure-blob) sekcji tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej w magazynie danych Cassandra:

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Poniższa tabela zawiera opis specyficzne dla usługi Cassandra połączone elementy JSON.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi mieć ustawioną: **OnPremisesCassandra** |Yes |
| host |Jeden lub więcej adresów IP lub nazw hostów serwerów Cassandra.<br/><br/>Określ rozdzielaną przecinkami listę adresów IP lub nazw hostów, aby nawiązać połączenie wszystkie serwery jednocześnie. |Yes |
| port |Port TCP używany przez serwer Cassandra nasłuchiwanie dla połączeń klienta. |Nie, wartość domyślna: 9042 |
| Typ authenticationType |Podstawowa lub anonimowe |Yes |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Tak, jeśli authenticationType ustawiany jest podstawowy. |
| hasło |Określ hasło dla konta użytkownika. |Tak, jeśli authenticationType ustawiany jest podstawowy. |
| gatewayName |Nazwa bramy, która służy do łączenia z bazą danych Cassandra lokalnymi. |Yes |
| encryptedCredential |Poświadczenie szyfrowane przez bramę. |Nie |

>[!NOTE]
>Połączenie Cassandra przy użyciu protokołu SSL nie jest obecnie obsługiwane.

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów obiektów dataset (Azure SQL, obiektów blob platformy Azure, Azure tabeli itp.).

**TypeProperties** sekcja jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. TypeProperties sekcja dla zestawu danych typu **CassandraTable** ma następujące właściwości

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| przestrzeni kluczy |Nazwa schematu bazy danych Cassandra lub przestrzeni kluczy. |Tak (Jeśli **zapytania** dla **CassandraSource** nie jest zdefiniowana). |
| tableName |Nazwa tabeli w bazie danych Cassandra. |Tak (Jeśli **zapytania** dla **CassandraSource** nie jest zdefiniowana). |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Właściwości, które są dostępne w sekcji typeProperties działania różnić się z każdym typem działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink.

Jeśli źródło jest typu **CassandraSource**, w sekcji typeProperties dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj niestandardowych zapytania można odczytać danych. |Zapytania SQL 92 lub CQL zapytania. Zobacz [odwołania CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Korzystając z zapytania SQL, określ **przestrzeni kluczy name.table nazwy** do reprezentowania tabeli ma dotyczyć zapytanie. |Nie (jeśli są zdefiniowane tableName oraz przestrzeni kluczy w zestawie danych). |
| consistencyLevel |Poziom spójności Określa, jak wiele replik musi odpowiedzieć na żądanie odczytu przed zwróceniem danych do aplikacji klienckiej. Cassandra sprawdza określonej liczby replik danych do spełnienia żądania odczytu. |JEDNĄ, DWIE, TRZY, KWORUM, WSZYSTKIE, LOCAL_QUORUM EACH_QUORUM, LOCAL_ONE. Zobacz [Konfigurowanie spójność danych](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) szczegółowe informacje. |Nie. Domyślna wartość to jeden. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>Przykład JSON: kopiowanie danych z Cassandra do obiektów Blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicje JSON, które można użyć, aby utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Widoczny jest sposób kopiowania danych z lokalną bazą danych Cassandra do magazynu obiektów Blob Azure. Jednak można skopiować danych do dowolnego wychwytywanie podane [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.

> [!IMPORTANT]
> W tym przykładzie przedstawiono fragmenty kodu JSON. Zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych. Zobacz [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu, aby uzyskać instrukcje krok po kroku.

Przykład zawiera następujące obiekty fabryki danych:

* Połączonej usługi typu [OnPremisesCassandra](#linked-service-properties).
* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [CassandraTable](#dataset-properties).
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [CassandraSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Cassandra połączonej usługi:**

W tym przykładzie użyto **Cassandra** połączonej usługi. Zobacz [Cassandra połączona usługa](#linked-service-properties) sekcji właściwości obsługiwanych przez tej połączonej usługi.  

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Połączonej usługi magazynu Azure:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

**Zestaw danych wejściowych Cassandra:**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Ustawienie **zewnętrznych** do **true** usługi fabryka danych informuje, czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

**Azure Blob wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Aktywność kopiowania w potoku z Cassandra źródłowy i odbiorczy obiektów Blob:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **CassandraSource** i **zbiornika** ustawiono typ **BlobSink**.

Zobacz [właściwości typu RelationalSource](#copy-activity-properties) listę obsługiwanych przez RelationalSource właściwości.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]    
    }
}
```

### <a name="type-mapping-for-cassandra"></a>Mapowanie typu dla Cassandra
| Typ Cassandra | Typ na podstawie .net |
| --- | --- |
| ASCII |Ciąg |
| BIGINT |Int64 |
| OBIEKT BLOB |Byte[] |
| WARTOŚĆ LOGICZNA |Wartość logiczna |
| DECIMAL |Decimal |
| O PODWÓJNEJ PRECYZJI |podwójne |
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
>

## <a name="work-with-collections-using-virtual-table"></a>Praca z kolekcji za pomocą tabeli wirtualnej
Fabryka danych Azure używa wbudowanego sterownika ODBC do nawiązania połączenia, a następnie skopiować dane z bazy danych Cassandra. Dla typów kolekcji, w tym mapy, zestaw i listy sterownik renormalizes dane w odpowiadających jej tabel wirtualnego. W szczególności jeśli tabela zawiera wszystkie kolumny w kolekcji, sterownik generuje następujące tabele wirtualne:

* A **tabeli podstawowej**, który zawiera te same dane jako prawdziwe tabeli z wyjątkiem kolumny kolekcji. Tabela podstawowa używa tej samej nazwie jako prawdziwe tabeli, która reprezentuje.
* A **tabeli wirtualnej** dla każdej kolekcji kolumny, która rozszerza zagnieżdżone dane. Wirtualny tabel, które reprezentują kolekcje są nazywane przy użyciu nazwy tabeli rzeczywistych separator "*vt*" i nazwę kolumny.

Tabele wirtualne odwołują się do danych w tabeli prawdziwe, włączanie dostępu do danych nieznormalizowany sterownika. Zobacz sekcję przykład, aby uzyskać szczegółowe informacje. Ma dostęp do zawartości Cassandra kolekcji, zapytań i przyłączanie tabel wirtualnego.

Można użyć [kreatora kopiowania](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuicyjnie wyświetlić listę tabel w bazie danych Cassandra tym tabele wirtualne i przeglądać dane wewnątrz. Można również utworzyć zapytanie w kreatorze kopiowania i weryfikacji, aby zobaczyć wynik.

### <a name="example"></a>Przykład
Na przykład następujące "ExampleTable" jest Cassandra tabeli bazy danych, która zawiera całkowitą kolumna klucza podstawowego o nazwie "pk_int", kolumna tekst o nazwie wartość kolumny listy, kolumny mapy i zestawu kolumn (o nazwie "StringSet").

| pk_int | Wartość | List | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"Przykładowa wartość 1" |["1", "2", "3"] |{"S1": "", "S2": "b"} |{"A", "B", "C"} |
| 3 |"przykład value 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Sterownik przetwarzający generuje wiele tabel wirtualnego do reprezentowania tej pojedynczej tabeli. Kolumny klucza obcego w tabeli wirtualnej odwołania kolumn klucza podstawowego w tabeli rzeczywistych i wskazać, który odpowiada wiersz tabeli wirtualnej wiersz tabeli prawdziwe.

Pierwsza tabela wirtualnego jest tabeli podstawowej o nazwie "ExampleTable" przedstawiono w poniższej tabeli. Podstawowa tabela zawiera te same dane jako oryginalnej tabeli bazy danych, z wyjątkiem kolekcje, które są pominięte w tej tabeli i rozwinięty w innych tabelach wirtualnych.

| pk_int | Wartość |
| --- | --- |
| 1 |"Przykładowa wartość 1" |
| 3 |"przykład value 3" |

W poniższych tabelach przedstawiono wirtualnego tabel, które renormalize danych z listy, mapy i StringSet kolumn. Kolumny o nazwach kończyć "_index" lub "_klucza" wskazać pozycję danych w oryginalnej listy lub mapy. Kolumny o nazwach kończących się znakiem "_value" zawierają rozszerzonych danych z kolekcji.

#### <a name="table-exampletablevtlist"></a>Tabela "ExampleTable_vt_List":
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletablevtmap"></a>Tabela "ExampleTable_vt_Map":
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletablevtstringset"></a>Tabela "ExampleTable_vt_StringSet":
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Obiekt sink kolumn mapy źródła
Aby uzyskać informacje dotyczące mapowania kolumn w zestawie źródła danych do kolumn w zestawie danych zbiornika, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Odczyt powtarzalny ze źródłami relacyjnymi
Podczas kopiowania danych z danych relacyjnych przechowuje, należy pamiętać, aby uniknąć niezamierzone wyniki powtarzalności. Fabryka danych Azure możesz ponownie ręcznie wycinek. Można również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek jest uruchamiany ponownie, gdy wystąpi błąd. Podczas wycinek zostanie uruchomiona ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać źródłami relacyjnymi](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajania
Zobacz [wydajności działania kopiowania & dostrajanie przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby.
