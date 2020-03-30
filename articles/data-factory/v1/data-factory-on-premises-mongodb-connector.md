---
title: Przenoszenie danych z MongoDB
description: Dowiedz się, jak przenosić dane z bazy danych MongoDB przy użyciu usługi Azure Data Factory.
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
ms.openlocfilehash: edddd100bddab1d642a8169353298a2d20620274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281342"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Przenoszenie danych z mongodb przy użyciu usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-on-premises-mongodb-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-mongodb.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [złącze MongoDB w wersji 2](../connector-mongodb.md).


W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z lokalnej bazy danych MongoDB. Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z działaniem kopiowania.

Dane można kopiować z lokalnego magazynu danych MongoDB do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Fabryka danych obsługuje obecnie tylko przenoszenie danych z magazynu danych MongoDB do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych do magazynu danych MongoDB.

## <a name="prerequisites"></a>Wymagania wstępne
Aby usługa Azure Data Factory mogła łączyć się z lokalną bazą danych mongodb, należy zainstalować następujące składniki:

- Obsługiwane wersje MongoDB to: 2.4, 2.6, 3.0, 3.2, 3.4 i 3.6.
- Brama zarządzania danymi na tym samym komputerze, na którym znajduje się baza danych lub na oddzielnym komputerze, aby uniknąć konkurowania o zasoby z bazą danych. Brama zarządzania danymi to oprogramowanie, które łączy lokalne źródła danych z usługami w chmurze w bezpieczny i zarządzany sposób. Zobacz artykuł [Bramy zarządzania danymi, aby](data-factory-data-management-gateway.md) uzyskać szczegółowe informacje na temat bramy zarządzania danymi. Zobacz [Przenoszenie danych z lokalnego do chmury](data-factory-move-data-between-onprem-and-cloud.md) artykuł instrukcje krok po kroku dotyczące konfigurowania bramy potoku danych w celu przenoszenia danych.

    Po zainstalowaniu bramy automatycznie instaluje sterownik OdBC Microsoft MongoDB używany do łączenia się z mongodb.

    > [!NOTE]
    > Musisz użyć bramy, aby połączyć się z mongodb, nawet jeśli jest hostowany w maszynach wirtualnych usługi Azure IaaS. Jeśli próbujesz połączyć się z wystąpieniem mongodb hostowane w chmurze, można również zainstalować wystąpienie bramy w IaaS Maszyny Wirtualnej.

## <a name="getting-started"></a>Wprowadzenie
Potoku można utworzyć z działaniem kopiowania, które przenosi dane z lokalnego magazynu danych mongodb przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych.

Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania.

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON.  W przypadku przykładu z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych z lokalnego magazynu danych mongodb, zobacz [przykład JSON: Kopiowanie danych z mongodb do usługi Azure Blob](#json-example-copy-data-from-mongodb-to-azure-blob) sekcji tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek fabryki danych specyficznych dla źródła mongodb:

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej **OnPremisesMongoDB.**

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość type musi być ustawiona na: **OnPremisesMongoDb** |Tak |
| serwer |Adres IP lub nazwa hosta serwera MongoDB. |Tak |
| port |Port TCP używany przez serwer MongoDB do nasłuchiwać połączeń klientów. |Opcjonalna, wartość domyślna: 27017 |
| authenticationType |Podstawowy lub Anonimowy. |Tak |
| nazwa użytkownika |Konto użytkownika, aby uzyskać dostęp do MongoDB. |Tak (jeśli używane jest uwierzytelnianie podstawowe). |
| hasło |Hasło użytkownika. |Tak (jeśli używane jest uwierzytelnianie podstawowe). |
| źródło authSource |Nazwa bazy danych MongoDB, której chcesz użyć do sprawdzenia poświadczeń w celu uwierzytelnienia. |Opcjonalne (jeśli używane jest uwierzytelnianie podstawowe). domyślnie: używa konta administratora i bazy danych określonej przy użyciu właściwości databaseName. |
| Databasename |Nazwa bazy danych MongoDB, do której chcesz uzyskać dostęp. |Tak |
| nazwa bramy |Nazwa bramy, która uzyskuje dostęp do magazynu danych. |Tak |
| encryptedCredential |Poświadczenia zaszyfrowane przez bramę. |Optional (Opcjonalność) |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych (Azure SQL, Azure blob, tabela platformy Azure itp.).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Sekcja typeProperties dla zestawu danych typu **MongoDbCollection** ma następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| Collectionname |Nazwa kolekcji w bazie danych MongoDB. |Tak |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w sekcji **typeProperties** działania z drugiej strony różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze.

Gdy źródło jest typu **MongoDbSource** następujące właściwości są dostępne w sekcji typeProperties:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL-92. Na przykład: wybierz * z MyTable. |Nie (jeśli określono nazwa zestawu **danych** **collectionName)** |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>Przykład JSON: kopiowanie danych z mongodb do obiektu Blob platformy Azure
W tym przykładzie przedstawiono przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Pokazano w nim, jak kopiować dane z lokalnej bazy danych MongoDB do usługi Azure Blob Storage. Jednak dane mogą być kopiowane do dowolnego ujścia podane [w tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.

Próbka ma następujące jednostki fabryki danych:

1. Powiązana usługa typu [OnPremisesMongoDb](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [MongoDbCollection](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [MongoDbSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy kopiuje dane z wyniku kwerendy w bazie danych MongoDB do obiektu blob co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

W pierwszym kroku należy skonfigurować bramę zarządzania danymi zgodnie z instrukcjami zawartymi w artykule [Brama zarządzania danymi.](data-factory-data-management-gateway.md)

**Usługa połączona z MongoDB:**

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

**Zestaw danych wejściowych MongoDB:** Ustawienie "zewnętrzne": "true" informuje usługę Data Factory, że tabela jest zewnętrzna dla fabryki danych i nie jest wytwarzana przez działanie w fabryce danych.

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

**Zestaw danych wyjściowych obiektów blob platformy Azure:**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu obiektu blob jest dynamicznie oceniana na podstawie czasu rozpoczęcia przetwarzanego plasterka. Ścieżka folderu używa części czasu rozpoczęcia z roku, miesiąca, dnia i godziny.

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

**Kopiowanie działania w potoku ze źródłem MongoDB i ujściem obiektów Blob:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z powyższych danych wejściowych i wyjściowych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **MongoDbSource** i typ **ujścia** jest ustawiony na **BlobSink**. Kwerenda SQL określona dla właściwości **kwerendy** wybiera dane w ciągu ostatniej godziny do skopiowania.

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


## <a name="schema-by-data-factory"></a>Schemat według fabryki danych
Usługa Azure Data Factory wywnioskować schemat z kolekcji MongoDB przy użyciu najnowszych dokumentów 100 w kolekcji. Jeśli te 100 dokumentów nie zawiera pełnego schematu, niektóre kolumny mogą być ignorowane podczas operacji kopiowania.

## <a name="type-mapping-for-mongodb"></a>Mapowanie typów dla mongodb
Jak wspomniano w artykule działania związane z [przenoszeniem danych,](data-factory-data-movement-activities.md) działanie kopiowania wykonuje automatyczne konwersje typów z typów źródłowych do typów ujścia z następującym podejściem dwuetapowym:

1. Konwertowanie z natywnych typów źródeł na typ .NET
2. Konwertowanie z typu .NET na natywny typ ujścia

Podczas przenoszenia danych do mongodb następujące mapowania są używane z typów MongoDB do typów .NET.

| Typ MongoDB | Typ programu .NET Framework |
| --- | --- |
| plików binarnych |Bajt[] |
| Wartość logiczna |Wartość logiczna |
| Data |DateTime |
| NumberDouble (LiczbaDouble) |Double |
| LiczbaInt |Int32 |
| LiczbaLong |Int64 |
| ObjectID |Ciąg |
| Ciąg |Ciąg |
| Uuid |Guid (identyfikator GUID) |
| Obiekt |Przegrupowane na spłaszczone kolumny z "_" jako separatorem zagnieżdżanym |

> [!NOTE]
> Aby dowiedzieć się więcej na temat obsługi tablic przy użyciu tabel wirtualnych, zapoznaj się z [obsługą typów złożonych przy użyciu sekcji tabel wirtualnych](#support-for-complex-types-using-virtual-tables) poniżej.

Obecnie następujące typy danych MongoDB nie są obsługiwane: DBPointer, JavaScript, Max/Min, Wyrażenie regularne, Symbol, Sygnatura czasowa, Niezdefiniowany

## <a name="support-for-complex-types-using-virtual-tables"></a>Obsługa typów złożonych przy użyciu tabel wirtualnych
Usługa Azure Data Factory używa wbudowanego sterownika ODBC do łączenia się z bazą danych mongodb i kopiowania ich. W przypadku typów złożonych, takich jak tablice lub obiekty o różnych typach w dokumentach, sterownik ponownie normalizuje dane do odpowiednich tabel wirtualnych. W szczególności jeśli tabela zawiera takie kolumny, sterownik generuje następujące tabele wirtualne:

* **Tabela bazowa**, która zawiera te same dane co tabela rzeczywista, z wyjątkiem kolumn typu złożonego. Tabela bazowa używa tej samej nazwy co rzeczywista tabela, którą reprezentuje.
* **Tabela wirtualna** dla każdej kolumny typu złożonego, która rozwija zagnieżdżone dane. Tabele wirtualne są nazywane przy użyciu nazwy tabeli rzeczywistej, separatora "_" i nazwy tablicy lub obiektu.

Tabele wirtualne odnoszą się do danych w rzeczywistej tabeli, umożliwiając kierowcy dostęp do danych denormalized. Zobacz przykład sekcji poniżej szczegóły. Dostęp do zawartości tablic MongoDB można uzyskać, odwołując zapytania i dołączając do tabel wirtualnych.

Za pomocą [Kreatora kopiowania](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) można intuicyjnie wyświetlić listę tabel w bazie danych MongoDB, w tym tabele wirtualne, i wyświetlić podgląd danych wewnątrz. Można również skonstruować kwerendę w Kreatorze kopiowania i sprawdź poprawność, aby zobaczyć wynik.

### <a name="example"></a>Przykład
Na przykład "ExampleTable" poniżej jest tabela MongoDB, która ma jedną kolumnę z tablicą obiektów w każdej komórce - Faktury i jedną kolumnę z tablicą typów skalarnych — Oceny.

| _id | Nazwa klienta | Faktury | Poziom usług | Klasyfikacje |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", poz invoice_id. |Srebrny |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", poz. |Złoty |[1,2] |

Sterownik wygeneruje wiele tabel wirtualnych do reprezentowania tej pojedynczej tabeli. Pierwsza tabela wirtualna to tabela bazowa o nazwie "ExampleTable", pokazana poniżej. Tabela bazowa zawiera wszystkie dane oryginalnej tabeli, ale dane z tablic zostały pominięte i rozwinięte w tabelach wirtualnych.

| _id | Nazwa klienta | Poziom usług |
| --- | --- | --- |
| 1111 |ABC |Srebrny |
| 2222 |XYZ |Złoty |

W poniższych tabelach przedstawiono tabele wirtualne reprezentujące oryginalne tablice w przykładzie. Tabele te zawierają następujące tabele:

* Odwołanie do oryginalnej kolumny klucza podstawowego odpowiadającej wierszowi oryginalnej tablicy (za pośrednictwem kolumny _id)
* Wskazanie położenia danych w oryginalnej tablicy
* Rozszerzone dane dla każdego elementu w tablicy

Tabela "ExampleTable_Invoices":

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | element | price | Rabat |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Toster |456 |0.2 |
| 1111 |1 |124 |Piekarnik |1235 |0.2 |
| 2222 |0 |135 |Lodówka |12543 |0.0 |

Tabela "ExampleTable_Ratings":

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się więcej o mapowaniu kolumn w źródłowym zestawie danych na kolumny w zestawie danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Powtarzalny odczyt ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnych magazynów danych należy pamiętać o powtarzalności, aby uniknąć niezamierzonych wyników. W usłudze Azure Data Factory można ponownie uruchomić plasterek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, tak aby plasterek został ponownie uruchomny w przypadku wystąpienia błędu. Gdy plasterek jest uruchamiany ponownie w obu przypadkach, należy upewnić się, że te same dane są odczytywane bez względu na to, ile razy jest uruchamiany plasterek. Zobacz [Powtarzalny odczyt ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i strojenie
Zobacz [Kopiowanie wydajności działania & dostrajania przewodnika,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Copy Activity) w usłudze Azure Data Factory i na różne sposoby optymalizacji.

## <a name="next-steps"></a>Następne kroki
Zobacz [Przenoszenie danych między lokalnymi i chmurowymi](data-factory-move-data-between-onprem-and-cloud.md) artykułami, aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku danych, który przenosi dane z lokalnego magazynu danych do magazynu danych platformy Azure.
