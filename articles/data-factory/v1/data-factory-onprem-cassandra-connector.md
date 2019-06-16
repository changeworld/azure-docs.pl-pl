---
title: Przenoszenie danych z bazy danych Cassandra przy użyciu usługi fabryka danych | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobach przenoszenia danych z bazy danych Cassandra w środowisku lokalnym za pomocą usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0a3adbd082c68121e762fd03c2221a0c800f0bc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60823984"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Przenoszenie danych z bazy danych Cassandra w środowisku lokalnym za pomocą usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](data-factory-onprem-cassandra-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-cassandra.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznika bazy danych Cassandra w wersji 2](../connector-cassandra.md).

W tym artykule wyjaśniono, jak użyć działania kopiowania w usłudze Azure Data Factory, aby przenieść dane z lokalnej bazy danych Cassandra. Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania.

Możesz skopiować dane z magazynu danych oprogramowania Cassandra w środowisku lokalnym, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako ujścia działania kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. Data factory obsługuje obecnie tylko przenosi dane z magazynu danych oprogramowania Cassandra do innych magazynów danych, ale nie przenosi dane z innych magazynów danych do magazynu danych oprogramowania Cassandra.

## <a name="supported-versions"></a>Obsługiwane wersje
Łącznik rozwiązania Cassandra obsługuje następujące wersje oprogramowania Cassandra: 2.x i 3.x. Działania uruchomione na własne środowisko IR, bazy danych Cassandra 3.x jest obsługiwana, ponieważ środowisko IR w wersji 3.7 lub nowszym.

## <a name="prerequisites"></a>Wymagania wstępne
Dla usługi Azure Data Factory można było połączyć się z lokalną bazą danych Cassandra należy zainstalować bramę zarządzania danymi na tym samym komputerze, który jest hostem bazy danych lub na osobnym komputerze, aby uniknąć rywalizując o zasoby z bazą danych. Brama zarządzania danymi jest składnikiem, który łączy z lokalnymi źródłami danych z usługami w chmurze w sposób bezpieczny i zarządzane. Zobacz [bramy zarządzania danymi](data-factory-data-management-gateway.md) artykuł, szczegółowe informacje na temat bramy zarządzania danymi. Zobacz [przenoszenie danych ze środowiska lokalnego do chmury](data-factory-move-data-between-onprem-and-cloud.md) artykuł, aby uzyskać instrukcje krok po kroku dotyczące konfigurowania bramy potoku danych do przenoszenia danych.

Należy użyć bramy do łączenia z bazą danych Cassandra, nawet wtedy, gdy baza danych znajduje się w chmurze, na przykład na maszynie Wirtualnej IaaS platformy Azure. Y może masz bramy na tej samej maszyny Wirtualnej, który jest hostem bazy danych lub osobne maszyny wirtualnej, tak długo, jak bramy można połączyć z bazą danych.

Po zainstalowaniu bramy, automatycznie instaluje sterownik Microsoft Cassandra ODBC, używane do łączenia z bazą danych Cassandra. W związku z tym nie trzeba ręcznie zainstalować dowolnego sterownika na maszynie bramy, podczas kopiowania danych z bazy danych Cassandra.

> [!NOTE]
> Zobacz [problemów bramy](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) porady dotyczące rozwiązywania problemów z bramy połączenia/problemy związane z usługą.

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, które przenosi dane z lokalnego magazynu danych Cassandra przy użyciu różnych narzędzi/interfejsów API.

- Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.
- Aby utworzyć potok umożliwia także następujących narzędzi: **Witryna Azure portal**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**i  **Interfejs API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania.
3. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON. Przykład przy użyciu definicji JSON dla jednostek fabryki danych, które są używane w celu skopiowania danych z lokalnego magazynu danych oprogramowania Cassandra, zobacz [przykład kodu JSON: Kopiowanie danych z bazy danych Cassandra do usługi Azure Blob](#json-example-copy-data-from-cassandra-to-azure-blob) dalszej części tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi fabryka danych określonej do magazynu danych oprogramowania Cassandra:

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Poniższa tabela zawiera opis dla elementów JSON, które są specyficzne dla rozwiązania Cassandra połączonej usługi.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi być równa: **OnPremisesCassandra** |Tak |
| host |Jeden lub więcej adresów IP lub nazw hostów serwerów bazy danych Cassandra.<br/><br/>Określ rozdzielaną przecinkami listę adresów IP lub nazw hostów, połączyć się z wszystkich serwerów jednocześnie. |Yes |
| port |Port TCP, którego serwer Cassandra korzysta do nasłuchiwania połączeń klientów. |Nie, wartość domyślna: 9042 |
| authenticationType |Podstawowe lub anonimowe |Tak |
| username |Określ nazwę użytkownika dla konta użytkownika. |Tak, jeśli element authenticationType ustawiany jest podstawowy. |
| password |Określ hasło dla konta użytkownika. |Tak, jeśli element authenticationType ustawiany jest podstawowy. |
| gatewayName |Nazwa bramy, która służy do łączenia z bazą danych Cassandra w środowisku lokalnym. |Tak |
| encryptedCredential |Poświadczenie szyfrowane przez bramę. |Nie |

>[!NOTE]
>Połączenie bazy danych Cassandra przy użyciu protokołu SSL nie jest obecnie obsługiwane.

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne Definiowanie zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów na zestaw danych (Azure SQL, obiektów blob platformy Azure, usługa Azure table itp.).

**TypeProperties** sekcji różni się dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Zestaw danych o typie sekcji typeProperties **CassandraTable** ma następujące właściwości

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| keyspace |Nazwa przestrzeni kluczy lub schemat bazy danych Cassandra. |Tak (Jeśli **zapytania** dla **CassandraSource** nie jest zdefiniowana). |
| tableName |Nazwa tabeli w bazie danych Cassandra. |Tak (Jeśli **zapytania** dla **CassandraSource** nie jest zdefiniowana). |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Natomiast właściwości, które są dostępne w sekcji typeProperties działania zależą od każdego typu działania. Działanie kopiowania ich różnią się w zależności od typów źródła i ujścia.

Jeśli źródło jest typu **CassandraSource**, w sekcji typeProperties dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowe można odczytać danych. |Zapytanie SQL 92 lub zapytanie języka CQL. Zobacz [odwołanie do języka CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Korzystając z zapytania SQL, określ **nazwa name.table przestrzeń kluczy** do reprezentowania tabeli, które chcesz zbadać. |Nie (jeśli są zdefiniowane tableName oraz przestrzeń kluczy w zestawie danych). |
| consistencyLevel |Poziom spójności Określa, jak wiele replik musi odpowiadać na żądania odczytu przed zwróceniem danych do aplikacji klienckiej. Bazy danych Cassandra sprawdza określoną liczbę replik dla danych do spełnienia żądania odczytu. |JEDEN, DWA, TRZY, KWORUM, WSZYSTKIE, LOCAL_QUORUM EACH_QUORUM, LOCAL_ONE. Zobacz [Konfigurowanie spójności danych](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) Aby uzyskać szczegółowe informacje. |Nie. Wartość domyślna to jeden. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>Przykład kodu JSON: Kopiowanie danych z bazy danych Cassandra do obiektów Blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [witryny Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Widoczny jest sposób skopiować dane z lokalnej bazy danych Cassandra do usługi Azure Blob Storage. Jednakże, można skopiować danych do dowolnego ujścia, o których wspomniano [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.

> [!IMPORTANT]
> W tym przykładzie przedstawiono fragmenty kodu JSON. Nie obejmuje instrukcje krok po kroku dotyczące tworzenia fabryki danych. Zobacz [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykuł, aby uzyskać instrukcje krok po kroku.

Przykład obejmuje następujących jednostek fabryki danych:

* Połączonej usługi typu [OnPremisesCassandra](#linked-service-properties).
* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [CassandraTable](#dataset-properties).
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [CassandraSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Bazy danych Cassandra połączoną usługę:**

W tym przykładzie użyto **Cassandra** połączoną usługę. Zobacz [Cassandra połączoną usługę](#linked-service-properties) sekcja dotycząca właściwości obsługiwanych przez tej połączonej usługi.

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

**Połączona usługa Azure Storage:**

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

**Wejściowy zestaw danych bazy danych Cassandra:**

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

Ustawienie **zewnętrznych** do **true** usługi Data Factory informuje, że zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

**Usługa Azure Blob wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob, co godzinę (frequency: godzina, interwał: 1).

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

**Działanie kopiowania w potoku za pomocą bazy danych Cassandra źródła i ujścia obiektu Blob:**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **CassandraSource** i **ujścia** ustawiono typ **BlobSink**.

Zobacz [właściwości typu RelationalSource](#copy-activity-properties) listy właściwości obsługiwanych przez RelationalSource.

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

### <a name="type-mapping-for-cassandra"></a>Mapowanie typu dla bazy danych Cassandra
| Typ bazy danych Cassandra | Typ oparte na platformie .NET |
| --- | --- |
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
>

## <a name="work-with-collections-using-virtual-table"></a>Praca z kolekcji za pomocą tabeli wirtualnej
Usługa Azure Data Factory używa wbudowanego sterownika ODBC, aby nawiązać połączenie i kopiowanie danych z bazy danych Cassandra. Dla typów kolekcji, łącznie z mapy, zestaw i listy sterownik renormalizes dane do odpowiedniego tabele wirtualne. W szczególności jeśli tabela zawiera żadnych kolumn kolekcji, sterownik generuje następujące tabele wirtualne:

* A **tabeli podstawowej**, który zawiera te same dane jako tabelę rzeczywistych, z wyjątkiem kolumn kolekcji. Tabela podstawowa używa tej samej nazwie jako rzeczywistych tabeli, który reprezentuje.
* A **tabeli wirtualnej** dla każdej kolumny kolekcji, która rozszerza zagnieżdżone dane. Wirtualne tabel, które reprezentują kolekcje są nazywane przy użyciu nazwy tabeli rzeczywistych separator "*vt*" i nazwę kolumny.

Tabele wirtualne odnoszą się do danych w tabeli rzeczywistych Włączanie sterownik dostępu do danych nieznormalizowany. Zobacz przykład sekcję, aby uzyskać szczegółowe informacje. Wykonywanie zapytań i przyłączanie tabel wirtualnego, ma dostęp do zawartości, kolekcji bazy danych Cassandra.

Możesz użyć [kreatora kopiowania](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuicyjnie wyświetlać listę tabel w bazie danych Cassandra, łącznie z tabelami wirtualnego i wyświetlić podgląd danych wewnątrz. Można również utworzyć kwerendę w kreatorze kopiowania i weryfikacji, aby wyświetlić wynik.

### <a name="example"></a>Przykład
Na przykład poniższy "ExampleTable" jest tabeli bazy danych Cassandra, która zawiera całkowitą kolumna klucza podstawowego o nazwie "pk_int", nazwanej wartości kolumny tekstowej, kolumna listy, kolumna mapy i zestawu kolumn (o nazwie "StringSet").

| pk_int | Wartość | List | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"przykład: wartość 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"przykład value 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Sterownik wygeneruje wiele tabel wirtualnego do reprezentowania jednej tabeli. Kolumny klucza obcego w tabelach wirtualnych odwoływać się do kolumny klucza podstawowego w tabeli rzeczywistych i wskazać, który wiersz tabeli rzeczywistych wiersz tabeli wirtualnej odpowiada.

Pierwsza tabela wirtualnego jest tabela bazowa o nazwie "ExampleTable" przedstawiono w poniższej tabeli. Tabela podstawowa zawiera te same dane, co oryginalnej tabeli bazy danych, z wyjątkiem kolekcji, które są pomijane w tej tabeli i rozwinięty w innych tabelach wirtualnych.

| pk_int | Wartość |
| --- | --- |
| 1 |"przykład: wartość 1" |
| 3 |"przykład value 3" |

W poniższych tabelach przedstawiono tabele wirtualne, które ponownie normalizować dane z listy, mapy i StringSet kolumn. Kolumny z nazwami, które kończą się "_index" lub "_klucz" wskazują pozycji danych w oryginalnej listy lub mapy. Kolumny z nazwami, które kończą się "_wartość" zawierają rozwiniętej danych z kolekcji.

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

## <a name="map-source-to-sink-columns"></a>Mapy źródła do ujścia kolumn
Aby uzyskać informacje dotyczące mapowania kolumn w zestaw danych źródłowych do kolumn w zestawie danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Odczyt powtarzalny ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnej bazie danych są przechowywane, Zachowaj powtarzalności należy pamiętać, aby uniknąć niezamierzonego wyników. W usłudze Azure Data Factory możesz ponownie uruchomić wycinek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, dzięki czemu wycinek będzie uruchamiana ponownie, gdy wystąpi błąd. Gdy wycinek będzie uruchamiana ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać źródła relacyjnego](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [wydajności działania kopiowania & przewodnika dostrajania](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i różne sposoby, aby zoptymalizować ją.
