---
title: Przenieść dane z bazy danych MongoDB przy użyciu fabryki danych | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z bazy danych MongoDB przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 10ca7d9a-7715-4446-bf59-2d2876584550
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0afdfb7b7d1f74d3df40b22bb97afc0f39bcc6d1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Przenoszenie danych z bazy danych MongoDB przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-on-premises-mongodb-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-mongodb.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [łącznika bazy danych MongoDB w wersji 2](../connector-mongodb.md).


W tym artykule opisano sposób używania działania kopiowania w fabryce danych Azure do przenoszenia danych z lokalną bazą danych MongoDB. Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania.

Można skopiować danych z magazynu danych bazy danych MongoDB lokalnymi żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych obsługiwane jako wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. Fabryka danych aktualnie obsługuje tylko dane przenoszenie, z magazynu danych bazy danych MongoDB do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych do magazynu danych bazy danych MongoDB. 

## <a name="prerequisites"></a>Wymagania wstępne
Dla usługi fabryka danych Azure można było nawiązać połączenia z lokalną bazą danych MongoDB należy zainstalować następujące składniki:

- Obsługiwane wersje bazy danych MongoDB: 2.4, 2.6, 3.0, 3.2 3.4 i 3,6.
- Brama zarządzania danymi na tym samym komputerze, który jest hostem bazy danych lub na osobnym komputerze w celu uniknięcia konkurowanie o zasoby z bazą danych. Brama zarządzania danymi to oprogramowanie, które łączy lokalnych źródeł danych do usługi w chmurze w sposób bezpieczny i zarządzanie nimi. Zobacz [brama zarządzania danymi](data-factory-data-management-gateway.md) artykułu, aby uzyskać więcej informacji dotyczących bramy zarządzania danymi. Zobacz [przenoszenia danych z lokalnymi do chmury](data-factory-move-data-between-onprem-and-cloud.md) artykułu, aby uzyskać instrukcje krok po kroku dotyczące konfigurowania bramy danych potoku do przenoszenia danych.

    Po zainstalowaniu bramy, automatycznie instaluje sterownik Microsoft MongoDB ODBC używany do łączenia z bazy danych MongoDB.

    > [!NOTE]
    > Należy używać bramy do łączenia się bazy danych MongoDB, nawet jeśli jest obsługiwany na maszynach wirtualnych Azure IaaS. Jeśli próbujesz połączyć się z wystąpieniem bazy danych MongoDB hostowanych w chmurze, można także zainstalować wystąpienie bramy na maszynie wirtualnej IaaS.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działania kopiowania, który przenosi dane z magazynu danych bazy danych MongoDB lokalnymi przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. 

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych: 

1. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych.
2. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. 
3. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Dla przykładu z definicji JSON dla jednostek fabryki danych, które są używane do skopiowania danych na lokalnym magazynem danych MongoDB, zobacz [przykład JSON: kopiowanie danych z bazy danych MongoDB do obiektów Blob platformy Azure](#json-example-copy-data-from-mongodb-to-azure-blob) sekcji tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej do bazy danych MongoDB źródła:

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Poniższa tabela zawiera opis elementów JSON specyficzne dla **OnPremisesMongoDB** połączonej usługi.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi mieć ustawioną: **OnPremisesMongoDb** |Yes |
| serwer |Adres IP lub hosta nazwę serwera bazy danych MongoDB. |Yes |
| port |Port TCP używany przez serwer bazy danych MongoDB do nasłuchiwania dla połączeń klienta. |Opcjonalne, wartość domyślna: 27017 |
| Typ authenticationType |Podstawowy, lub anonimowe. |Yes |
| nazwa użytkownika |Konto użytkownika do bazy danych MongoDB. |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| hasło |Hasło dla użytkownika. |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| authSource |Nazwa bazy danych MongoDB, który ma być używany w celu sprawdzenia poświadczeń dla uwierzytelniania. |Opcjonalnie (jeśli jest używane uwierzytelnianie podstawowe). domyślne: używa konta administratora i baza danych określona za pomocą właściwości databaseName. |
| databaseName |Nazwa bazy danych MongoDB, które chcesz uzyskać dostęp. |Yes |
| gatewayName |Nazwa bramy, która uzyskuje dostęp do magazynu danych. |Yes |
| encryptedCredential |Poświadczenie szyfrowane przez bramę. |Optional (Opcjonalność) |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów obiektów dataset (Azure SQL, obiektów blob platformy Azure, Azure tabeli itp.).

**TypeProperties** sekcja jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. TypeProperties sekcja dla zestawu danych typu **MongoDbCollection** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| CollectionName |Nazwa kolekcji w bazie danych MongoDB. |Yes |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w **typeProperties** sekcji działania z drugiej strony zależą od każdego typu działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink.

Jeśli źródło jest typu **MongoDbSource** w sekcji typeProperties dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL 92. Na przykład: Wybierz * z MyTable. |Nie (Jeśli **collectionName** z **dataset** jest określona) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>Przykład JSON: kopiowanie danych z bazy danych MongoDB do obiektów Blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicje JSON, które można użyć, aby utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Widoczny jest sposób skopiować dane z lokalnej bazy danych MongoDB do magazynu obiektów Blob Azure. Jednak można skopiować danych do dowolnego wychwytywanie podane [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.

Przykład zawiera następujące obiekty fabryki danych:

1. Połączonej usługi typu [OnPremisesMongoDb](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [MongoDbCollection](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [MongoDbSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z wyniku kwerendy w bazie danych MongoDB do obiektu blob co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

Pierwszym krokiem instalacji bramy zarządzania danymi, zgodnie z instrukcjami wyświetlanymi w [brama zarządzania danymi](data-factory-data-management-gateway.md) artykułu.

**Bazy danych MongoDB połączonej usługi:**

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

**Wejściowy zestaw danych MongoDB:** ustawienie "external": "prawda" informuje usługi fabryka danych czy tabeli zewnętrznej dla fabryki danych i nie jest generowany przez działanie w fabryce danych.

```json
{
     "name":  "MongoDbInputDataset",
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

**Azure Blob wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Ścieżka folderu dla obiekt blob jest dynamicznie obliczane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godziny części czas rozpoczęcia.

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

**Aktywność kopiowania w potoku z bazy danych MongoDB źródłowy i odbiorczy obiektów Blob:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania powyższe dane wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **MongoDbSource** i **zbiornika** ustawiono typ **BlobSink**. Określony dla zapytania SQL **zapytania** właściwości wybiera dane w ostatniej godziny do skopiowania.

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
                        "query": "$$Text.Format('select * from  MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
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


## <a name="schema-by-data-factory"></a>Schemat fabryka danych
Usługi fabryka danych Azure wnioskuje schemat z kolekcji bazy danych MongoDB przy użyciu najnowszych 100 dokumentów w kolekcji. Jeśli te dokumenty 100 nie zawierają pełny schemat, niektóre kolumny można zignorować podczas operacji kopiowania.

## <a name="type-mapping-for-mongodb"></a>Mapowanie typu dla bazy danych MongoDB
Jak wspomniano w [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, wykonuje działanie kopiowania automatyczne konwersje z typów źródła do zbiornika typów o następujące podejście krok 2:

1. Konwertowanie typów natywnych źródła na typ architektury .NET
2. Konwertowanie na typ macierzysty ujścia typ architektury .NET

Podczas przenoszenia danych do bazy danych MongoDB z bazy danych MongoDB typy są używane następujące mapowania do typów .NET.

| Typ bazy danych MongoDB | Typ programu .NET framework |
| --- | --- |
| Binarny |Byte[] |
| Wartość logiczna |Wartość logiczna |
| Date |DateTime |
| NumberDouble |Podwójnej precyzji |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Ciąg |
| Ciąg |Ciąg |
| IDENTYFIKATOR UUID |Identyfikator GUID |
| Obiekt |Renormalized do spłaszczenia kolumn z "_" jako separatora zagnieżdżonych |

> [!NOTE]
> Aby dowiedzieć się więcej o obsługę tablic za pomocą tabele wirtualne, zapoznaj się [obsługę złożonych typów przy użyciu tabele wirtualne](#support-for-complex-types-using-virtual-tables) poniższej sekcji.

Obecnie nie są obsługiwane następujące typy danych MongoDB: DBPointer, JavaScript, maksymalna liczba na minutę klucza, wyrażenie regularne, Symbol, Timestamp, niezdefiniowane

## <a name="support-for-complex-types-using-virtual-tables"></a>Obsługę złożonych typów przy użyciu tabele wirtualne
Fabryka danych Azure używa wbudowanego sterownika ODBC do nawiązania połączenia, a następnie skopiować dane z bazy danych MongoDB. Typów złożonych takich jak macierze lub obiektów z różnymi typami wszystkich dokumentów sterownik ponownie normalizuje danych do odpowiednich tabel wirtualnego. W szczególności jeśli tabela zawiera takich kolumn, sterownik generuje następujące tabele wirtualne:

* A **tabeli podstawowej**, który zawiera te same dane jako prawdziwe tabeli z wyjątkiem kolumny typu złożonego. Tabela podstawowa używa tej samej nazwie jako prawdziwe tabeli, która reprezentuje.
* A **tabeli wirtualnej** dla każdej kolumny typu złożonego, która rozszerza zagnieżdżone dane. Tabele wirtualne są nazywane przy użyciu nazwy tabeli prawdziwe, separator "_" i nazwę tablicy lub obiektu.

Tabele wirtualne odwołują się do danych w tabeli prawdziwe, włączanie dostępu do danych nieznormalizowany sterownika. Zobacz przykład sekcję poniżej szczegółowe informacje. Ma dostęp do zawartości tablic bazy danych MongoDB, zapytań i przyłączanie tabel wirtualnego.

Można użyć [kreatora kopiowania](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuicyjnie wyświetlić listę tabel w bazie danych MongoDB, łącznie z tabelami wirtualnego i przeglądać dane wewnątrz. Można również utworzyć zapytanie w kreatorze kopiowania i weryfikacji, aby zobaczyć wynik.

### <a name="example"></a>Przykład
Na przykład "ExampleTable" poniżej znajduje się tabela bazy danych MongoDB o jedną kolumnę z tablicę obiektów w każdej komórce — faktury i jedną kolumnę z tablicą typów skalarnych — klasyfikacji.

| _id | Nazwa klienta | Faktury | Poziom usług | Klasyfikacje |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: elementu "123",: "tostera", cena: Rabat "456",: "0,2"}, {invoice_id: "124", element: "piec", cena: Zniżka "1235": "0,2"}] |Srebrny |[5,6] |
| 2222 |XYZ |[{invoice_id: element "135": "lodówko", cena: Rabat "12543": "0,0"}] |Złoty |[1,2] |

Sterownik przetwarzający generuje wiele tabel wirtualnego do reprezentowania tej pojedynczej tabeli. Pierwszy tabeli wirtualnej jest tabela podstawowa o nazwie "ExampleTable", pokazano poniżej. Podstawowa tabela zawiera wszystkie dane z oryginalnej tabeli, ale dane z macierzami została pominięta i jest rozwinięta w tabelach wirtualnych.

| _id | Nazwa klienta | Poziom usług |
| --- | --- | --- |
| 1111 |ABC |Srebrny |
| 2222 |XYZ |Złoty |

W poniższych tabelach przedstawiono wirtualnego tabel, które reprezentują oryginalnego tablic w przykładzie. Te tabele zawierać następujące informacje:

* Odwołanie do oryginalnego kolumna klucza podstawowego odpowiadający wiersza oryginalnej tablicy (za pomocą kolumny _id)
* Wskazanie pozycji danych w oryginalnej tablicy
* Rozwinięte danych dla każdego elementu w tablicy

Tabela "ExampleTable_Invoices":

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | Element | price | Rabat |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |tostera |456 |0.2 |
| 1111 |1 |124 |Piec |1235 |0.2 |
| 2222 |0 |135 |lodówko |12543 |0.0 |

Tabela "ExampleTable_Ratings":

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Obiekt sink kolumn mapy źródła
Aby uzyskać informacje dotyczące mapowania kolumn w zestawie źródła danych do kolumn w zestawie danych zbiornika, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Odczyt powtarzalny ze źródłami relacyjnymi
Podczas kopiowania danych z danych relacyjnych przechowuje, należy pamiętać, aby uniknąć niezamierzone wyniki powtarzalności. Fabryka danych Azure możesz ponownie ręcznie wycinek. Można również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek jest uruchamiany ponownie, gdy wystąpi błąd. Podczas wycinek zostanie uruchomiona ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać źródłami relacyjnymi](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajania
Zobacz [wydajności działania kopiowania & dostrajanie przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby.

## <a name="next-steps"></a>Następne kroki
Zobacz [przenoszenie danych między lokalnymi i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykuł instrukcje krok po kroku dla tworzenie potoku danych, które przenosi dane z lokalnego magazynu danych do magazynu danych Azure.
