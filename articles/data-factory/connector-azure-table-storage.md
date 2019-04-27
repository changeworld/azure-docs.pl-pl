---
title: Kopiowanie danych do i z usługi Azure Table storage przy użyciu usługi fabryka danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z obsługiwanego źródła magazynów w usłudze Azure Table storage lub Table storage przechowuje obsługiwane ujście, za pomocą usługi Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: 7ef8f80f44c921cc1f2524351c8acb78ebd713bf
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57434797"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Kopiowanie danych do i z usługi Azure Table storage za pomocą usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-azure-table-connector.md)
> * [Bieżąca wersja](connector-azure-table-storage.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych do i z usługi Azure Table storage. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do usługi Table storage. Możesz także skopiować dane z usługi Table storage do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła lub ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik obsługuje usługi Azure Table kopiowanie danych przy użyciu klucza konta i usługi udostępniane uwierzytelnienia sygnatury dostępu.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonego w usłudze Table storage.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

### <a name="use-an-account-key"></a>Użyj klucza konta

Utworzysz połączoną usługę Azure Storage przy użyciu klucza konta. Zapewnia usługi data factory za pomocą globalnego dostępu do magazynu. Następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **AzureTableStorage**. |Yes |
| connectionString | Określ informacje wymagane do połączenia z magazynem dla właściwości connectionString. <br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory. Możesz również umieścić klucz konta w usłudze Azure Key Vault i ściągania `accountKey` konfiguracji poza parametry połączenia. Zobacz poniższe przykłady i [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md) artykułu z bardziej szczegółowymi informacjami. |Yes |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

>[!NOTE]
>Jeśli używano "AzureStorage" typu połączonej usługi jest nadal obsługiwany jako — jest, gdy są zalecane, aby użyć tego nowego "AzureTableStorage" połączony typ usługi, w przyszłości.

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

**Przykład: przechowywanie klucza konta w usłudze Azure Key Vault**

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

Możesz również utworzyć połączonej usługi Storage przy użyciu sygnatury dostępu współdzielonego. Usługi data factory zapewnia ograniczony/czasowo dostęp do wszystkich/dla zasobów w magazynie.

Sygnatury dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Służy on do jest przyznanie klientowi ograniczonych uprawnień do obiektów na koncie magazynu w wyznaczonym czasie i z określonym zestawem uprawnień. Nie masz udostępniania kluczy dostępu do Twojego konta. Sygnatura dostępu współdzielonego to identyfikator URI, który obejmuje jego parametry zapytań, wszystkie informacje niezbędne do uwierzytelnionego dostępu do zasobu magazynu. Aby uzyskać dostęp do zasobów magazynu przy użyciu sygnatury dostępu współdzielonego, klient musi tylko przekazanej sygnatury dostępu współdzielonego do odpowiedniego konstruktora lub metody. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [sygnatur dostępu współdzielonego: Informacje o modelu sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Usługa Data Factory obsługuje teraz zarówno **sygnatury dostępu współdzielonego usługi** i **sygnatury dostępu współdzielonego konta**. Aby uzyskać więcej informacji na temat tych dwóch typów i jak je utworzyć, zobacz [rodzaje sygnatur dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). 

> [!TIP]
> Aby wygenerować sygnaturę dostępu współdzielonego usługi dla konta magazynu, można wykonać następujące polecenia programu PowerShell. Zastąp symbole zastępcze i udzielić wymaganych uprawnień.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Aby użyć uwierzytelniania sygnatury dostępu współdzielonego, następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **AzureTableStorage**. |Yes |
| sasUri | Określ identyfikator URI sygnatury dostępu Współdzielonego do tabeli identyfikatora URI sygnatury dostępu współdzielonego. <br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory. Możesz również umieścić tokenu sygnatury dostępu Współdzielonego w usłudze Azure Key Vault, aby wykorzystać automatyczne obracanie i usunąć część tokenu. Zobacz poniższe przykłady i [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md) artykułu z bardziej szczegółowymi informacjami. | Yes |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

>[!NOTE]
>Jeśli używano "AzureStorage" typu połączonej usługi jest nadal obsługiwany jako — jest, gdy są zalecane, aby użyć tego nowego "AzureTableStorage" połączony typ usługi, w przyszłości.

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

**Przykład: przechowywanie klucza konta w usłudze Azure Key Vault**

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
- Identyfikator URI powinny być tworzone na poziomie tabeli po prawej, zgodnie z potrzebami.

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych tabeli platformy Azure.

Aby skopiować dane do i z tabel Azure, należy ustawić właściwość typu zestawu danych na **AzureTable**. Następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa **AzureTable**. |Yes |
| tableName |Nazwa tabeli w wystąpieniu bazy danych magazynu tabel, które połączona usługa przywołuje. |Yes |

**Przykład:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schemat przez usługę Data Factory

Dla magazynów danych bez schematu, takich jak Azure Table Data Factory wnioskuje schemat w jednym z następujących sposobów:

* Jeśli określisz struktury danych przy użyciu **struktury** właściwości w definicji zestawu danych usługi Data Factory honoruje tej struktury Schema. W tym przypadku jeśli wiersz nie zawiera wartości dla kolumny, wartości null są udostępniane dla niego.
* Jeśli nie określisz struktury danych przy użyciu **struktury** właściwości w definicji zestawu danych usługi Data Factory wnioskuje schemat przy użyciu pierwszego wiersza danych. W takim przypadku pierwszy wiersz nie zawiera pełnej schematu, niektóre kolumny zostaną pominięte w wyniku operacji kopiowania.

Dla źródeł danych bez schematu, najlepszym rozwiązaniem jest określenie struktury danych przy użyciu **struktury** właściwości.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez usługi Azure Table źródła i ujścia.

### <a name="azure-table-as-a-source-type"></a>Tabela platformy Azure jako typ źródła

Aby skopiować dane z tabeli platformy Azure, należy ustawić typ źródłowego w działaniu kopiowania, aby **AzureTableSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być równa **AzureTableSource**. |Yes |
| azureTableSourceQuery |Zapytanie niestandardowe magazyn tabel umożliwia odczytywanie danych. Zobacz przykłady w poniższej sekcji. |Nie |
| azureTableSourceIgnoreTableNotFound |Wskazuje, czy należy zezwolić na wyjątek tabela nie istnieje.<br/>Dozwolone wartości to **True** i **False** (ustawienie domyślne). |Nie |

### <a name="azuretablesourcequery-examples"></a>Przykłady azureTableSourceQuery

W przypadku kolumny tabeli platformy Azure typu Data/Godzina:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Jeśli kolumna tabeli platformy Azure jest typu string:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Jeśli parametr potoku Rzutuj wartość daty/godziny mają właściwego formatu zgodnie z poprzednich przykładów.

### <a name="azure-table-as-a-sink-type"></a>Tabela platformy Azure jako typ ujścia

Aby skopiować dane do tabeli platformy Azure, należy ustawić typ ujścia w działaniu kopiowania, aby **AzureTableSink**. Następujące właściwości są obsługiwane w działaniu kopiowania **ujścia** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type ujścia działania kopiowania musi być równa **AzureTableSink**. |Yes |
| azureTableDefaultPartitionKeyValue |Domyślna wartość klucza partycji, które mogą być używane przez obiekt sink. |Nie |
| azureTablePartitionKeyName |Określ nazwę kolumny, których wartości są używane jako klucze partycji. Jeśli nie zostanie określony, "AzureTableDefaultPartitionKeyValue" jest używany jako klucz partycji. |Nie |
| azureTableRowKeyName |Określ nazwę kolumny, których wartości kolumny są używane jako klucz wiersza. Jeśli nie zostanie określony, użyj identyfikatora GUID dla każdego wiersza. |Nie |
| azureTableInsertType |Tryb Aby wstawić dane do usługi Azure Table. Ta właściwość określa, czy istniejące wiersze w tabeli danych wyjściowych ze zgodnymi kluczami partycji i wiersza mają wartości zastąpienia lub scalony. <br/><br/>Dozwolone wartości to **scalania** (ustawienie domyślne) i **Zastąp**. <br/><br> To ustawienie ma zastosowanie na poziomie wiersza nie na poziomie tabeli. Żadna z tych opcji spowoduje usunięcie wierszy w tabeli danych wyjściowych, które nie istnieją w danych wejściowych. Aby dowiedzieć się, jak działają ustawienia scalania i Zamień, zobacz [entity Insert lub merge](https://msdn.microsoft.com/library/azure/hh452241.aspx) i [Wstawianie lub zastępowanie jednostki](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Nie |
| writeBatchSize |Wstawia dane do tabeli platformy Azure, gdy writeBatchSize lub writeBatchTimeout tych limitów zostanie osiągnięty.<br/>Dozwolone wartości to liczby całkowitej (liczba wierszy). |Nie (wartość domyślna to 10 000) |
| writeBatchTimeout |Wstawia dane do tabeli platformy Azure, gdy writeBatchSize lub writeBatchTimeout tych limitów zostanie osiągnięty.<br/>Dozwolone wartości to przedziału czasu. Na przykład "00:20:00" (20 minut). |Nie (wartość domyślna to 90 sekund, domyślna wartość limitu czasu klienta usługi storage) |

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

Mapowanie kolumny źródłowej do kolumny docelowej przy użyciu **"w usłudze translator"** właściwości, zanim będzie można użyć kolumny docelowej jako azureTablePartitionKeyName.

W poniższym przykładzie kolumna źródłowa DivisionID jest mapowany na kolumny docelowej DivisionID:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID" jest określony jako klucza partycji.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Mapowanie typu danych dla tabeli platformy Azure

Podczas kopiowania danych z i do usługi Azure Table, następujące mapowania są używane do typów danych tymczasowych usługi Data Factory z typów danych usługi Azure Table. Aby uzyskać informacje dotyczące sposobu działania kopiowania mapowania typ schematu i danych źródła do ujścia, zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md).

Podczas przenoszenia danych do i z usługi Azure Table następujące [mapowania zdefiniowane przez usługi Azure Table](https://msdn.microsoft.com/library/azure/dd179338.aspx) są używane z typów OData tabeli platformy Azure, typ architektury .NET i na odwrót.

| Typ danych w usłudze Azure Table | Typ danych tymczasowych fabryki danych | Szczegóły |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |Tablica bajtów do 64 KB. |
| Edm.Boolean |bool |Wartość logiczna. |
| Edm.DateTime |DateTime |Wartość 64-bitową, wyrażone jako uniwersalny czas koordynowany (UTC). Obsługiwanym zakresem daty/godziny rozpocznie się o północy, 1 stycznia, 1601 r. N.E. (C.E.), UTC. Zakres kończy się 31 grudnia 9999 r. |
| Edm.Double |double |64-bitowy zmiennoprzecinkowy wartość punktu. |
| Edm.Guid |Guid |Globalnie unikatowy identyfikator 128-bitowego. |
| Edm.Int32 |Int32 |32-bitową liczbę całkowitą. |
| Edm.Int64 |Int64 |64-bitową liczbę całkowitą. |
| Edm.String |String |Wartość algorytmem UTF-16. Wartości typu ciąg może być maksymalnie 64 KB. |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
