---
title: Kopiowanie danych do i z usługi Azure Table Storage przy użyciu Data Factory | Microsoft Docs
description: Informacje o kopiowaniu danych z obsługiwanych magazynów źródłowych do usługi Azure Table Storage lub z magazynu tabel do obsługiwanych magazynów ujścia przy użyciu Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: jingwang
ms.openlocfilehash: 5920fe4a1addd2188f53a15c1d2232f505009087
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061497"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Kopiowanie danych do i z usługi Azure Table Storage przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-azure-table-connector.md)
> * [Bieżąca wersja](connector-azure-table-storage.md)

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych do i z usługi Azure Table Storage. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Dane można kopiować z dowolnego obsługiwanego źródłowego magazynu danych do usługi Table Storage. Możesz również skopiować dane z magazynu tabel do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła lub ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W każdym przypadku ten łącznik tabeli platformy Azure obsługuje kopiowanie danych przy użyciu uwierzytelniania sygnatury dostępu współdzielonego z użyciem klucza konta i usługi.

## <a name="get-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek Data Factory specyficznych dla magazynu tabel.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

### <a name="use-an-account-key"></a>Użyj klucza konta

Połączoną usługę Azure Storage można utworzyć przy użyciu klucza konta. Zapewnia fabrykę danych z dostępem globalnym do magazynu. Następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type musi być ustawiona na wartość **AzureTableStorage**. |Tak |
| connectionString | Określ informacje wymagane do połączenia z magazynem dla właściwości connectionString. <br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory. Możesz również umieścić klucz konta w Azure Key Vault i ściągnąć `accountKey` konfigurację z parametrów połączenia. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. |Tak |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

>[!NOTE]
>Jeśli używasz połączonej usługi typu "AzureStorage", jest ona nadal obsługiwana w stanie takim, w jakim będziesz mieć możliwość użycia nowego typu połączonej usługi "AzureTableStorage" do przodu.

**Przykład:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: Zapisz klucz konta w Azure Key Vault**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-shared-access-signature-authentication"></a>Korzystanie z uwierzytelniania sygnatury dostępu współdzielonego

Możesz również utworzyć połączoną usługę Storage przy użyciu sygnatury dostępu współdzielonego. Zapewnia ona fabrykę danych z ograniczonym dostępnym przez czas dostępem do wszystkich/określonych zasobów w magazynie.

Sygnatury dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Można jej użyć do udzielenia klientowi ograniczonych uprawnień do obiektów na koncie magazynu przez określony czas i z określonym zestawem uprawnień. Nie masz udostępniania kluczy dostępu do Twojego konta. Sygnatura dostępu współdzielonego to identyfikator URI, który obejmuje jego parametry zapytań, wszystkie informacje niezbędne do uwierzytelnionego dostępu do zasobu magazynu. Aby uzyskać dostęp do zasobów magazynu przy użyciu sygnatury dostępu współdzielonego, klient musi tylko przekazanej sygnatury dostępu współdzielonego do odpowiedniego konstruktora lub metody. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [sygnatury dostępu współdzielonego: Poznaj model](../storage/common/storage-dotnet-shared-access-signature-part-1.md)sygnatury dostępu współdzielonego.

> [!NOTE]
> Usługa Data Factory obsługuje teraz zarówno **sygnatury dostępu współdzielonego usługi** i **sygnatury dostępu współdzielonego konta**. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../storage/common/storage-sas-overview.md). 

> [!TIP]
> Aby wygenerować sygnaturę dostępu współdzielonego usługi dla konta magazynu, można wykonać następujące polecenia programu PowerShell. Zastąp symbole zastępcze i udzielić wymaganych uprawnień.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Aby można było korzystać z uwierzytelniania sygnatury dostępu współdzielonego, obsługiwane są następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type musi być ustawiona na wartość **AzureTableStorage**. |Tak |
| sasUri | Określ identyfikator URI SAS sygnatury dostępu współdzielonego do tabeli. <br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory. Token sygnatury dostępu współdzielonego można również umieścić w Azure Key Vault, aby wykorzystać funkcję autorotacji i usunąć część tokenu. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. | Tak |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

>[!NOTE]
>Jeśli używasz połączonej usługi typu "AzureStorage", jest ona nadal obsługiwana w stanie takim, w jakim będziesz mieć możliwość użycia nowego typu połączonej usługi "AzureTableStorage" do przodu.

**Przykład:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<account>.table.core.windows.net/<table>?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: Zapisz klucz konta w Azure Key Vault**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<account>.table.core.windows.net/<table>>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Podczas tworzenia identyfikatora URI sygnatury dostępu współdzielonego, należy wziąć pod uwagę następujące kwestie:

- Ustaw uprawnienia odczytu/zapisu odpowiednie obiekty w zależności od sposobu połączonej usługi (Odczyt, zapis, Odczyt/zapis) jest używany w fabryce danych.
- Ustaw **czas wygaśnięcia** odpowiednio. Upewnij się, że dostęp do obiektów nie wygasa w aktywnym okresie potoku.
- Identyfikator URI należy utworzyć na poziomie właściwej tabeli w zależności od potrzeb.

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych tabeli platformy Azure.

Aby skopiować dane do i z tabeli platformy Azure, ustaw właściwość Type zestawu danych na **Azure**. Następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość **Azure**. |Tak |
| tableName |Nazwa tabeli w wystąpieniu bazy danych magazynu tabel, do której odwołuje się połączona usługa. |Tak |

**Przykład:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schemat przez usługę Data Factory

W przypadku magazynów danych bez schematu, takich jak Azure Table, Data Factory wnioskuje schemat w jeden z następujących sposobów:

* Jeśli określisz Mapowanie kolumn w działaniu kopiowania, Data Factory Użyj listy kolumn po stronie źródłowej, aby pobrać dane. W takim przypadku, jeśli wiersz nie zawiera wartości dla kolumny, zostanie dla niego podana wartość null.
* Jeśli nie określisz mapowania kolumn w działaniu kopiowania, Data Factory wnioskuje schemat przy użyciu pierwszego wiersza w danych. W tym przypadku, jeśli pierwszy wiersz nie zawiera pełnego schematu (np. niektóre kolumny mają wartość null), niektóre kolumny są pominięte w wyniku operacji kopiowania.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i obiekt ujścia tabeli platformy Azure.

### <a name="azure-table-as-a-source-type"></a>Tabela platformy Azure jako typ źródła

Aby skopiować dane z tabeli platformy Azure, ustaw typ źródła w działaniu Copy na **AzureTableSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość **AzureTableSource**. |Tak |
| azureTableSourceQuery |Użyj zapytania magazynu tabel niestandardowych do odczytu danych. Zobacz przykłady w poniższej sekcji. |Nie |
| azureTableSourceIgnoreTableNotFound |Wskazuje, czy zezwolić na wyjątek nieistniejącej tabeli.<br/>Dozwolone wartości to **True** i **False** (ustawienie domyślne). |Nie |

### <a name="azuretablesourcequery-examples"></a>Przykłady azureTableSourceQuery

Jeśli kolumna tabeli platformy Azure ma typ DateTime:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Jeśli kolumna tabeli platformy Azure ma typ String:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

W przypadku użycia parametru potoku należy rzutować wartość DateTime na odpowiedni format zgodnie z poprzednimi przykładami.

### <a name="azure-table-as-a-sink-type"></a>Tabela platformy Azure jako typ ujścia

Aby skopiować dane do tabeli platformy Azure, ustaw typ ujścia w działaniu Copy na **AzureTableSink**. W sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type ujścia działania Copy musi być ustawiona na wartość **AzureTableSink**. |Tak |
| azureTableDefaultPartitionKeyValue |Domyślna wartość klucza partycji, która może być używana przez ujścia. |Nie |
| azureTablePartitionKeyName |Określ nazwę kolumny, której wartości są używane jako klucze partycji. Jeśli nie zostanie określony, jako klucz partycji zostanie użyta wartość "AzureTableDefaultPartitionKeyValue". |Nie |
| azureTableRowKeyName |Określ nazwę kolumny, której wartości kolumn są używane jako klucz wiersza. Jeśli nie zostanie określony, użyj identyfikatora GUID dla każdego wiersza. |Nie |
| azureTableInsertType |Tryb wstawiania danych do tabeli platformy Azure. Ta właściwość określa, czy istniejące wiersze w tabeli wyjściowej ze zgodnymi partycjami i kluczami wierszy mają zamienione lub scalone wartości. <br/><br/>Dozwolone wartości to **merge** (wartość domyślna) i **Zamień**. <br/><br> To ustawienie jest stosowane na poziomie wiersza, a nie na poziomie tabeli. Żadna z tych opcji nie powoduje usunięcia wierszy w tabeli wyjściowej, które nie istnieją w danych wejściowych. Aby dowiedzieć się, jak działają ustawienia scalania i zamieniania, zobacz [Wstawianie lub scalanie](https://msdn.microsoft.com/library/azure/hh452241.aspx) jednostki oraz [Wstawianie lub zamienianie jednostki](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Nie |
| writeBatchSize |Wstawia dane do tabeli platformy Azure, gdy trafi writeBatchSize lub writeBatchTimeout.<br/>Dozwolone wartości to liczba całkowita (liczba wierszy). |Nie (domyślnie 10 000) |
| writeBatchTimeout |Wstawia dane do tabeli platformy Azure, gdy trafi writeBatchSize lub writeBatchTimeout.<br/>Dozwolone wartości to TimeSpan. Przykładem jest "00:20:00" (20 minut). |Nie (wartość domyślna to 90 sekund, domyślny limit czasu klienta magazynu) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

Mapowanie kolumny źródłowej do kolumny docelowej przy użyciu właściwości **"Translator"** , aby można było użyć kolumny docelowej jako azureTablePartitionKeyName.

W poniższym przykładzie kolumna źródłowa DivisionID jest mapowana na kolumnę docelową DivisionID:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

Jako klucz partycji określono wartość "DivisionID".

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Mapowanie typu danych dla tabeli platformy Azure

Podczas kopiowania danych z i do tabeli platformy Azure następujące mapowania są używane w ramach typów danych tabeli platformy Azure do Data Factory danych pośrednich. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych na ujścia, zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md).

Po przeniesieniu danych do i z tabeli platformy Azure następujące [mapowania zdefiniowane przez usługę Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) Table są używane w ramach typów OData w tabeli platformy Azure do typu .NET i na odwrót.

| Typ danych tabeli platformy Azure | Typ danych tymczasowych fabryki danych | Szczegóły |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |Tablica bajtów do 64 KB. |
| Edm.Boolean |bool |Wartość logiczna. |
| Edm.DateTime |DateTime |Wartość 64-bitowa wyrażona jako uniwersalny czas koordynowany (UTC). Obsługiwany zakres DateTime rozpoczyna się od północy, 1 stycznia, 1601 N.E. (C.E.), UTC. Zakres zostaje zakończony 31 grudnia 9999. |
| Edm.Double |double |64-bitowa wartość zmiennoprzecinkowa. |
| Edm.Guid |Guid |128-bitowy unikatowy identyfikator globalny. |
| Edm.Int32 |Int32 |32-bitowa liczba całkowita. |
| Edm.Int64 |Int64 |64-bitowa liczba całkowita. |
| Edm.String |String |Wartość zakodowana w formacie UTF-16. Wartości ciągu mogą należeć do 64 KB. |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
