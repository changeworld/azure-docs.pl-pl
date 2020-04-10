---
title: Przenoszenie danych z kasandry przy użyciu fabryki danych
description: Dowiedz się, jak przenosić dane z lokalnej bazy danych Cassandra przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0f96680f1ea91434c84d6606e3637c68c1cb5a84
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991505"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Przenoszenie danych z lokalnej bazy danych Cassandra przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-onprem-cassandra-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-cassandra.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Łącznik Cassandra w wersji 2](../connector-cassandra.md).

W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z lokalnej bazy danych Cassandra. Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z działaniem kopiowania.

Dane można kopiować z lokalnego magazynu danych Cassandra do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Fabryka danych obsługuje obecnie tylko przenoszenie danych z magazynu danych Cassandra do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych do magazynu danych Cassandra.

## <a name="supported-versions"></a>Obsługiwane wersje
Złącze Cassandra obsługuje następujące wersje cassandra: 2.x i 3.x. W przypadku działań uruchomionych w czasie wykonywania integracji hostowanego samodzielnie cassandra 3.x jest obsługiwana od czasu podczerwonych wersji 3.7 lub nowszych.

## <a name="prerequisites"></a>Wymagania wstępne
Aby usługa Azure Data Factory mogła nawiązać połączenie z lokalną bazą danych Cassandra, należy zainstalować bramę zarządzania danymi na tym samym komputerze, na którym znajduje się baza danych lub na oddzielnym komputerze, aby uniknąć konkurowania o zasoby z bazą danych. Brama zarządzania danymi to składnik, który łączy lokalne źródła danych z usługami w chmurze w bezpieczny i zarządzany sposób. Zobacz artykuł [Bramy zarządzania danymi, aby](data-factory-data-management-gateway.md) uzyskać szczegółowe informacje na temat bramy zarządzania danymi. Zobacz [Przenoszenie danych z lokalnego do chmury](data-factory-move-data-between-onprem-and-cloud.md) artykuł instrukcje krok po kroku dotyczące konfigurowania bramy potoku danych w celu przenoszenia danych.

Bramy należy użyć do łączenia się z bazą danych Cassandra, nawet jeśli baza danych jest hostowana w chmurze, na przykład na maszynie wirtualnej usługi Azure IaaS. Y Możesz mieć bramę na tej samej maszynie wirtualnej, która obsługuje bazę danych lub na oddzielnej maszynie wirtualnej, o ile brama może łączyć się z bazą danych.

Podczas instalowania bramy automatycznie instaluje sterownik OdBC firmy Microsoft Cassandra używany do łączenia się z bazą danych Cassandra. W związku z tym nie trzeba ręcznie zainstalować żadnego sterownika na komputerze bramy podczas kopiowania danych z bazy danych Cassandra.

> [!NOTE]
> Aby uzyskać wskazówki dotyczące rozwiązywania problemów związanych z połączeniem/bramą, zobacz [Rozwiązywanie problemów z bramą.](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, który przenosi dane z lokalnego magazynu danych Cassandra przy użyciu różnych narzędzi/interfejsów API.

- Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych.
- Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania.

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON. W przypadku przykładu z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych z lokalnego magazynu danych Cassandra, zobacz [przykład JSON: Kopiowanie danych z cassandra do usługi Azure Blob](#json-example-copy-data-from-cassandra-to-azure-blob) sekcji tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory specyficznych dla magazynu danych Cassandra:

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej Cassandra.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość typu musi być ustawiona na: **OnPremisesCassandra** |Tak |
| host |Co najmniej jeden adres IP lub nazwy hostów serwerów Cassandra.<br/><br/>Określ oddzieloną przecinkami listę adresów IP lub nazw hostów, aby połączyć się ze wszystkimi serwerami jednocześnie. |Tak |
| port |Port TCP używany przez serwer Cassandra do nasłuchiwać połączeń klientów. |Nie, wartość domyślna: 9042 |
| authenticationType |Podstawowy lub anonimowy |Tak |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Tak, jeśli authenticationType jest ustawiony na Podstawowy. |
| hasło |Określ hasło dla konta użytkownika. |Tak, jeśli authenticationType jest ustawiony na Podstawowy. |
| nazwa bramy |Nazwa bramy, która jest używana do łączenia się z lokalną bazą danych Cassandra. |Tak |
| encryptedCredential |Poświadczenia zaszyfrowane przez bramę. |Nie |

>[!NOTE]
>Obecnie połączenie z cassandra przy użyciu protokołu TLS nie jest obsługiwane.

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych (Azure SQL, Azure blob, tabela platformy Azure itp.).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Sekcja typeProperties dla zestawu danych typu **CassandraTable** ma następujące właściwości

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| przestrzeń kluczy |Nazwa przestrzeni kluczy lub schematu w bazie danych Cassandra. |Tak (Jeśli **kwerenda** dla **CassandraSource** nie jest zdefiniowana). |
| tableName |Nazwa tabeli w bazie danych Cassandra. |Tak (Jeśli **kwerenda** dla **CassandraSource** nie jest zdefiniowana). |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady są dostępne dla wszystkich typów działań.

Mając na uwadze, właściwości dostępne w sekcji typeProperties działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze.

Gdy źródło jest typu **CassandraSource,** następujące właściwości są dostępne w sekcji typeProperties:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. |Kwerenda SQL-92 lub kwerenda CQL. Zobacz [odwołanie CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Podczas korzystania z kwerendy SQL określ **nazwę name.table obszaru kluczowego,** aby reprezentować tabelę, którą chcesz zbadać. |Nie (jeśli zdefiniowano tableName i keyspace na zestawie danych). |
| spójnośćPoziom |Poziom spójności określa, ile replik musi odpowiedzieć na żądanie odczytu przed zwróceniem danych do aplikacji klienckiej. Cassandra sprawdza określoną liczbę replik danych w celu spełnienia żądania odczytu. |JEDEN, DWA, TRZY, KWORUM, WSZYSTKO, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Zobacz [Konfigurowanie spójności danych,](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) aby uzyskać szczegółowe informacje. |Nie. Wartość domyślna to ONE. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>Przykład JSON: Kopiowanie danych z cassandra do obiektu Blob platformy Azure
W tym przykładzie przedstawiono przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Pokazano w nim, jak kopiować dane z lokalnej bazy danych Cassandra do usługi Azure Blob Storage. Jednak dane mogą być kopiowane do dowolnego ujścia podane [w tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.

> [!IMPORTANT]
> Ten przykład zawiera fragmenty kodu JSON. Nie zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych. Aby uzyskać instrukcje krok po kroku, zobacz [przenoszenie danych między lokalizacjami lokalnymi a artykułami w chmurze.](data-factory-move-data-between-onprem-and-cloud.md)

Próbka ma następujące jednostki fabryki danych:

* Powiązana usługa typu [OnPremisesCassandra](#linked-service-properties).
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [CassandraTable](#dataset-properties).
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [CassandraSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Usługa łączona z Cassandra:**

W tym przykładzie użyto usługi połączonej **Cassandra.** Zobacz [sekcję usługi połączonej cassandra](#linked-service-properties) dla właściwości obsługiwanych przez tę usługę połączone.

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

**Usługa połączona z usługą Azure Storage:**

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

Ustawienie na **wartość true** **z zewnątrz** informuje usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych.

**Zestaw danych wyjściowych obiektów blob platformy Azure:**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1).

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

**Kopiowanie działania w potoku z cassandra źródła i ujścia obiektów Blob:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **CassandraSource** i typ **ujścia** jest ustawiony na **BlobSink**.

Zobacz [Właściwości typu relacyjne źródło](#copy-activity-properties) dla listy właściwości obsługiwanych przez RelationalSource.

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
| Typ Cassandra | Typ oparty na sieci .NET |
| --- | --- |
| ASCII |Ciąg |
| Bigint |Int64 |
| Blob |Bajt[] |
| Boolean |Wartość logiczna |
| Dziesiętnych |Wartość dziesiętna |
| Podwójne |Double |
| Float |Single |
| Inet |Ciąg |
| INT |Int32 |
| TEKST |Ciąg |
| Sygnatury czasowej |DateTime |
| TIMEUUID |Guid (identyfikator GUID) |
| Uuid |Guid (identyfikator GUID) |
| Varchar |Ciąg |
| Funkcja VARINT |Wartość dziesiętna |

> [!NOTE]
> W przypadku typów kolekcji (mapa, zestaw, lista itp.) zobacz [Praca z typami kolekcji Cassandra przy użyciu sekcji tabeli wirtualnej.](#work-with-collections-using-virtual-table)
>
> Typy zdefiniowane przez użytkownika nie są obsługiwane.
>
> Długość kolumn binarnych i długości kolumny ciąg nie może być większa niż 4000.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Praca z kolekcjami przy użyciu tabeli wirtualnej
Usługa Azure Data Factory używa wbudowanego sterownika ODBC do łączenia się z bazą danych Cassandra i kopiowania ich z bazy danych Cassandra. W przypadku typów kolekcji, w tym mapy, zestawu i listy, sterownik renormalizuje dane do odpowiednich tabel wirtualnych. W szczególności jeśli tabela zawiera kolumny kolekcji, sterownik generuje następujące tabele wirtualne:

* **Tabela bazowa**, która zawiera te same dane co tabela rzeczywista, z wyjątkiem kolumn kolekcji. Tabela bazowa używa tej samej nazwy co rzeczywista tabela, którą reprezentuje.
* **Tabela wirtualna** dla każdej kolumny kolekcji, która rozwija zagnieżdżone dane. Tabele wirtualne reprezentujące kolekcje są nazywane przy użyciu nazwy tabeli rzeczywistej, separatora "*vt*" i nazwy kolumny.

Tabele wirtualne odnoszą się do danych w rzeczywistej tabeli, umożliwiając kierowcy dostęp do danych denormalized. Zobacz przykład sekcji, aby uzyskać szczegółowe informacje. Dostęp do zawartości kolekcji Cassandra można uzyskać, odpytując i dołączając do tabel wirtualnych.

Za pomocą [Kreatora kopiowania](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) można intuicyjnie wyświetlić listę tabel w bazie danych Cassandra, w tym tabele wirtualne, i wyświetlić podgląd danych wewnątrz. Można również skonstruować kwerendę w Kreatorze kopiowania i sprawdź poprawność, aby zobaczyć wynik.

### <a name="example"></a>Przykład
Na przykład następująca "ExampleTable" to tabela bazy danych Cassandra zawierająca kolumnę klucza podstawowego o nazwie "pk_int", kolumnę tekstową o nazwie value, kolumnę listy, kolumnę mapy i kolumnę zestawu (o nazwie "StringSet").

| pk_int | Wartość | List | Mapa | Zestaw ciągów |
| --- | --- | --- | --- | --- |
| 1 |"wartość próbki 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"wartość próbki 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Sterownik wygeneruje wiele tabel wirtualnych do reprezentowania tej pojedynczej tabeli. Kolumny klucza obcego w tabelach wirtualnych odwołują się do kolumn klucza podstawowego w tabeli rzeczywistej i wskazują, który wiersz tabeli rzeczywistej odpowiada wierszowi tabeli wirtualnej.

Pierwsza tabela wirtualna to tabela bazowa o nazwie "ExampleTable" jest wyświetlana w poniższej tabeli. Tabela bazowa zawiera te same dane co oryginalna tabela bazy danych, z wyjątkiem kolekcji, które są pomijane w tej tabeli i rozwijane w innych tabelach wirtualnych.

| pk_int | Wartość |
| --- | --- |
| 1 |"wartość próbki 1" |
| 3 |"wartość próbki 3" |

W poniższych tabelach przedstawiono tabele wirtualne, które ponownie znormalizują dane z kolumn Lista, Mapa i StringSet. Kolumny z nazwami, które kończą się na "_index" lub "_key" wskazują położenie danych na oryginalnej liście lub mapie. Kolumny z nazwami, które kończą się na "_value" zawierają rozszerzone dane z kolekcji.

#### <a name="table-exampletable_vt_list"></a>Tabela "ExampleTable_vt_List":
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletable_vt_map"></a>Tabela "ExampleTable_vt_Map":
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletable_vt_stringset"></a>Tabela "ExampleTable_vt_StringSet":
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się więcej o mapowaniu kolumn w źródłowym zestawie danych na kolumny w zestawie danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Powtarzalny odczyt ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnych magazynów danych należy pamiętać o powtarzalności, aby uniknąć niezamierzonych wyników. W usłudze Azure Data Factory można ponownie uruchomić plasterek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, tak aby plasterek został ponownie uruchomny w przypadku wystąpienia błędu. Gdy plasterek jest uruchamiany ponownie w obu przypadkach, należy upewnić się, że te same dane są odczytywane bez względu na to, ile razy jest uruchamiany plasterek. Zobacz [Powtarzalny odczyt ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i strojenie
Zobacz [Kopiowanie wydajności działania & dostrajania przewodnika,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Copy Activity) w usłudze Azure Data Factory i na różne sposoby optymalizacji.
