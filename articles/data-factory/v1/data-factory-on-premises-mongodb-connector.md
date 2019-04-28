---
title: Przenoszenie danych z bazy danych MongoDB przy użyciu usługi fabryka danych | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z bazy danych MongoDB przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 10ca7d9a-7715-4446-bf59-2d2876584550
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 433a8b2f9fb1f4c4599afbb807e9270992a98a52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824188"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Przenoszenie danych z bazy danych MongoDB przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](data-factory-on-premises-mongodb-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-mongodb.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznika MongoDB w wersji 2](../connector-mongodb.md).


W tym artykule wyjaśniono, jak użyć działania kopiowania w usłudze Azure Data Factory, aby przenieść dane z lokalnej bazy danych MongoDB. Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania.

Możesz skopiować dane z magazynu danych usługi MongoDB w środowisku lokalnym, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako ujścia działania kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. Data factory obsługuje obecnie tylko przenosi dane z magazynu danych bazy danych MongoDB do innych magazynów danych, ale nie przenosi dane z innych magazynów danych do magazynu danych z bazy danych MongoDB.

## <a name="prerequisites"></a>Wymagania wstępne
Dla usługi Azure Data Factory można było połączyć się z lokalną bazą danych MongoDB należy zainstalować następujące składniki:

- Obsługiwane wersje bazy danych MongoDB to: 2.4, 2.6, 3.0, 3.2, 3.4 i 3.6.
- Brama zarządzania danymi na tym samym komputerze, który jest hostem bazy danych lub na osobnym komputerze w celu uniknięcia rywalizując o zasoby z bazą danych. Brama zarządzania danymi to oprogramowanie, które lokalnymi źródłami danych łączy się z usługami w chmurze w sposób bezpieczny i zarządzane. Zobacz [bramy zarządzania danymi](data-factory-data-management-gateway.md) artykuł, szczegółowe informacje na temat bramy zarządzania danymi. Zobacz [przenoszenie danych ze środowiska lokalnego do chmury](data-factory-move-data-between-onprem-and-cloud.md) artykuł, aby uzyskać instrukcje krok po kroku dotyczące konfigurowania bramy potoku danych do przenoszenia danych.

    Po zainstalowaniu bramy, automatycznie instaluje sterownik Microsoft MongoDB ODBC, używany do łączenia z bazą danych MongoDB.

    > [!NOTE]
    > Należy użyć bramy do łączenia z bazą danych MongoDB, nawet wtedy, gdy jest ona hostowana na maszynach wirtualnych IaaS platformy Azure. Jeśli próbujesz połączyć się z wystąpieniem bazy danych MongoDB hostowanej w chmurze, wystąpień bramy można także zainstalować na maszynie Wirtualnej IaaS.

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, które przenosi dane z magazynu danych usługi MongoDB w środowisku lokalnym przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Aby utworzyć potok umożliwia także następujących narzędzi: **Witryna Azure portal**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**i  **Interfejs API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania.
3. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON.  Przykładowe definicje JSON dotyczące jednostek usługi Data Factory, które są używane do kopiowania danych z magazynu danych usługi MongoDB w środowisku lokalnym, możesz znaleźć [przykład kodu JSON: Kopiowanie danych z bazy danych MongoDB do usługi Azure Blob](#json-example-copy-data-from-mongodb-to-azure-blob) dalszej części tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi fabryka danych określonej do źródłowej bazy danych MongoDB:

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Poniższa tabela zawiera opis dla elementów JSON, które są specyficzne dla **OnPremisesMongoDB** połączoną usługę.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi być równa: **OnPremisesMongoDb** |Yes |
| serwer |Adres IP lub hosta nazwę serwera bazy danych MongoDB. |Yes |
| port |Port TCP używany przez serwer bazy danych MongoDB do nasłuchiwania połączeń klientów. |Opcjonalne, wartość domyślna: 27017 |
| Element authenticationType |Podstawowe, lub anonimowe. |Yes |
| nazwa użytkownika |Konto użytkownika, aby dostęp do bazy danych MongoDB. |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| password |Hasło użytkownika. |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| authSource |Nazwa bazy danych MongoDB, który chcesz użyć, aby sprawdzić swoje poświadczenia dla uwierzytelniania. |Opcjonalnie (Jeśli używane jest uwierzytelnianie podstawowe). domyślne: korzysta z konta administratora i baza danych określona za pomocą właściwości databaseName. |
| databaseName |Nazwa bazy danych MongoDB, który chcesz uzyskać dostęp. |Yes |
| gatewayName |Nazwa bramy, który uzyskuje dostęp do magazynu danych. |Yes |
| encryptedCredential |Poświadczenie szyfrowane przez bramę. |Optional (Opcjonalność) |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne Definiowanie zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów na zestaw danych (Azure SQL, obiektów blob platformy Azure, usługa Azure table itp.).

**TypeProperties** sekcji różni się dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Zestaw danych o typie sekcji typeProperties **MongoDbCollection** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| collectionName |Nazwa kolekcji w bazie danych MongoDB. |Yes |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w **typeProperties** sekcji działania z drugiej strony zależą od każdego typu działania. Działanie kopiowania ich różnią się w zależności od typów źródła i ujścia.

Jeśli źródło jest typu **MongoDbSource** w sekcji typeProperties dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL 92. Na przykład: Wybierz * z MyTable. |Nie (Jeśli **collectionName** z **dataset** zostanie określona) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>Przykład kodu JSON: Kopiowanie danych z bazy danych MongoDB do obiektów Blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [witryny Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Prezentuje sposób kopiowania danych z bazy danych MongoDB w środowisku lokalnym, do usługi Azure Blob Storage. Jednakże, można skopiować danych do dowolnego ujścia, o których wspomniano [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.

Przykład obejmuje następujących jednostek fabryki danych:

1. Połączonej usługi typu [OnPremisesMongoDb](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [MongoDbCollection](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [MongoDbSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z wyniku kwerendy w bazie danych MongoDB do obiektu blob na godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

Pierwszym krokiem konfiguracji bramy zarządzania danymi zgodnie z instrukcjami w [bramy zarządzania danymi](data-factory-data-management-gateway.md) artykułu.

**Bazy danych MongoDB połączoną usługę:**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
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

**Wejściowy zestaw danych bazy danych MongoDB:** Ustawienie "external": "true" informuje usługę fabryka danych, w tabeli zewnętrznej dla fabryki danych i nie jest generowany przez działanie w usłudze data factory.

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Usługa Azure Blob wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob, co godzinę (frequency: godzina, interwał: 1). Ścieżka folderu dla obiektu blob jest dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc, dzień i części godzin od zaplanowanej godziny rozpoczęcia.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Działanie kopiowania w potoku za pomocą bazy danych MongoDB źródła i ujścia obiektu Blob:**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do używania powyższe dane wejściowe i wyjściowe zestawy danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **MongoDbSource** i **ujścia** ustawiono typ **BlobSink**. Zapytanie SQL, określony dla **zapytania** właściwość wybiera dane w ciągu ostatniej godziny do skopiowania.

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```


## <a name="schema-by-data-factory"></a>Schemat przez usługę Data Factory
Usługa Azure Data Factory wnioskuje schemat z kolekcji usługi MongoDB przy użyciu najnowszych 100 dokumentów w kolekcji. Jeśli te 100 dokumentach nie zawierają pełnego schematu, niektóre kolumny mogą ignorowane podczas operacji kopiowania.

## <a name="type-mapping-for-mongodb"></a>Mapowanie typu dla bazy danych MongoDB
Jak wspomniano w [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, działanie kopiowania wykonuje operację automatyczne konwersje z typów źródła do ujścia typów przy użyciu następujących podejść krok 2:

1. Konwersji z typów natywnych źródła na typ architektury .NET
2. Przekonwertowanie z platformy .NET na typ ujścia natywne

Podczas przenoszenia danych do bazy danych MongoDB następujące mapowania są używane do typów .NET z typów bazy danych MongoDB.

| Typ bazy danych MongoDB | Typ .NET framework |
| --- | --- |
| Binarny |Byte[] |
| Boolean |Boolean |
| Date |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |String |
| Ciąg |String |
| IDENTYFIKATOR UUID |Guid |
| Object |Renormalized do spłaszczenia kolumn z "_" jako separatora zagnieżdżonych |

> [!NOTE]
> Aby dowiedzieć się więcej o obsłudze tablic przy użyciu wirtualnego tabel, zobacz [obsługę złożonych typów, przy użyciu tabele wirtualne](#support-for-complex-types-using-virtual-tables) poniższej sekcji.

Obecnie nie są obsługiwane następujące typy danych bazy danych MongoDB: Klucz DBPointer, JavaScript, maksymalna liczba na minutę, wyrażenie regularne, symboli, Timestamp, niezdefiniowane

## <a name="support-for-complex-types-using-virtual-tables"></a>Obsługa złożonych typów, przy użyciu tabele wirtualne
Usługa Azure Data Factory używa wbudowanego sterownika ODBC, aby nawiązać połączenie i kopiowanie danych z bazy danych MongoDB. W przypadku typów złożonych takich jak tablicami i obiektami z różnymi typami wszystkich dokumentów sterownik ponownie normalizuje dane na odpowiednie tabele wirtualne. W szczególności jeśli tabela zawiera takich kolumn, sterownik generuje następujące tabele wirtualne:

* A **tabeli podstawowej**, który zawiera te same dane jako tabelę rzeczywistych, z wyjątkiem kolumn typu złożonego. Tabela podstawowa używa tej samej nazwie jako rzeczywistych tabeli, który reprezentuje.
* A **tabeli wirtualnej** dla każdej kolumny na typ złożony, który rozwija zagnieżdżone dane. Tabele wirtualne są nazywane przy użyciu nazwy tabeli rzeczywistych, separator "_" i nazwę tablicy lub obiektu.

Tabele wirtualne odnoszą się do danych w tabeli rzeczywistych Włączanie sterownik dostępu do danych nieznormalizowany. Sekcja przykład zobacz poniższe szczegóły. Wykonywanie zapytań i przyłączanie tabel wirtualnego, ma dostęp do zawartości, tablic bazy danych MongoDB.

Możesz użyć [kreatora kopiowania](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuicyjnie wyświetlać listę tabel w tym wirtualne tabele w bazie danych MongoDB i Wyświetl podgląd danych wewnątrz. Można również utworzyć kwerendę w kreatorze kopiowania i weryfikacji, aby wyświetlić wynik.

### <a name="example"></a>Przykład
Na przykład "ExampleTable" poniżej jest tabeli bazy danych MongoDB, która zawiera jedną kolumnę z tablicy obiektów w każdej komórce — faktury i jedną kolumnę z tablicą typów skalarnych — klasyfikacji.

| _identyfikator | Nazwa klienta | Faktury | Poziom usług | Klasyfikacje |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: elementu "123": "tostera", price: "456" rabat w wysokości: "0,2"}, {invoice_id: elementu "124": "piec", price: discount "1235": "0,2"}] |Srebrny |[5,6] |
| 2222 |XYZ |[{invoice_id: element "135": "lodówko", price: Rabat w wysokości "12543": "0.0"}] |Złoty |[1,2] |

Sterownik wygeneruje wiele tabel wirtualnego do reprezentowania jednej tabeli. Pierwszej tabeli wirtualnej to podstawowa tabela o nazwie "ExampleTable" poniżej. Tabela podstawowa zawiera wszystkie dane z oryginalnej tabeli, ale dane z tablic została pominięta i jest rozwinięty w tabelach wirtualnych.

| _identyfikator | Nazwa klienta | Poziom usług |
| --- | --- | --- |
| 1111 |ABC |Srebrny |
| 2222 |XYZ |Złoty |

W poniższych tabelach przedstawiono wirtualnych tabel, które reprezentują oryginalnego tablic w przykładzie. Te tabele zawierają następujące czynności:

* Odwołanie z powrotem do oryginalnego kolumny klucza podstawowego odpowiadającą wierszowi tablicy oryginalnej (za pośrednictwem kolumnie _identyfikator)
* Wskazanie pozycji danych w oryginalnym tablicy
* Rozwinięty danych dla każdego elementu w tablicy

Tabela "ExampleTable_Invoices":

| _identyfikator | ExampleTable_Invoices_dim1_idx | invoice_id | Element | price | Rabat |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |tostera |456 |0.2 |
| 1111 |1 |124 |Piec |1235 |0.2 |
| 2222 |0 |135 |lodówko |12543 |0.0 |

Tabela "ExampleTable_Ratings":

| _identyfikator | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Mapy źródła do ujścia kolumn
Aby uzyskać informacje dotyczące mapowania kolumn w zestaw danych źródłowych do kolumn w zestawie danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Odczyt powtarzalny ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnej bazie danych są przechowywane, Zachowaj powtarzalności należy pamiętać, aby uniknąć niezamierzonego wyników. W usłudze Azure Data Factory możesz ponownie uruchomić wycinek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, dzięki czemu wycinek będzie uruchamiana ponownie, gdy wystąpi błąd. Gdy wycinek będzie uruchamiana ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać źródła relacyjnego](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [wydajności działania kopiowania & przewodnika dostrajania](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i różne sposoby, aby zoptymalizować ją.

## <a name="next-steps"></a>Następne kroki
Zobacz [przenoszenie danych między lokalizacją lokalną i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykuł instrukcje krok po kroku do tworzenia potoku danych, które przenosi dane z lokalnego magazynu danych do magazynu danych na platformie Azure.
