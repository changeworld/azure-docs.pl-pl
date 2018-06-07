---
title: Kopiowanie danych do i z magazynem tabel Azure przy użyciu fabryki danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane ze sklepów obsługiwanych źródłowych do magazynu tabel Azure lub z magazynu tabel do zbiornika obsługiwanych magazynów przy użyciu fabryki danych.
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 3f6add6691b0e1f43d70399493fa6bf8db8f3833
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34617182"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Kopiowanie danych do i z magazynem tabel Azure przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-azure-table-connector.md)
> * [Wersja 2 — wersja zapoznawcza](connector-azure-table-storage.md)

Ten artykuł przedstawia sposób użycia działanie kopiowania w fabryce danych Azure można skopiować danych do i z magazynem tabel Azure. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 fabryki danych, która jest ogólnie dostępna, zobacz [tabeli łącznika magazynu w wersji 1](v1/data-factory-azure-table-connector.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Dane należy skopiować z dowolnego źródła obsługiwanych magazynu danych do magazynu tabel. Możesz również skopiować dane z magazynu tabel do żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła lub wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik obsługuje tabeli platformy Azure kopiowanie danych przy użyciu klucza konta i usługi udostępniane uwierzytelnienia podpisu dostępu.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do magazynu tabel.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

### <a name="use-an-account-key"></a>Użyj klucza konta

Połączoną usługą magazynu Azure można utworzyć przy użyciu klucza konta. Zapewnia fabryka danych z globalnego dostępu do magazynu. Następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną **AzureStorage**. |Yes |
| Parametry połączenia | Określ informacje wymagane do połączenia z magazynem dla właściwości connectionString. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych znajduje się w sieci prywatnej), można użyć środowiska uruchomieniowego integracji Azure lub Self-hosted integracji w czasie wykonywania. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

**Przykład:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
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

### <a name="use-service-shared-access-signature-authentication"></a>Użyj uwierzytelniania sygnatury dostępu współdzielonego usługi

Możesz również utworzyć połączoną usługą magazynu przy użyciu sygnatury dostępu współdzielonego. Zapewnia fabryka danych z ograniczonej/czas-powiązane z dostępem do określonego/wszystkie zasoby w magazynie.

Sygnatury dostępu współdzielonego umożliwiają dostęp delegowany do zasobów na koncie magazynu. Służy on przyznanie klienta ograniczone uprawnienia do obiektów na koncie magazynu przez określony czas i z określonym zestawem uprawnień. Nie trzeba udostępniać klucze dostępu do Twojego konta. Sygnatury dostępu współdzielonego to identyfikator URI, który obejmuje w jego parametrów zapytania, wszystkie informacje niezbędne do uwierzytelniony dostęp do zasobów magazynu. Aby uzyskać dostęp do zasobów magazynu przy użyciu sygnatury dostępu współdzielonego, klient musi tylko Przekaż sygnatury dostępu współdzielonego do odpowiedniego konstruktora lub metody. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [sygnatur dostępu współużytkowanego: zrozumienie modelu sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!IMPORTANT]
> Fabryka danych obsługuje obecnie tylko sygnatur dostępu usług udostępnionych, ale nie sygnatur dostępu udostępnionego konta. Aby uzyskać więcej informacji o tych dwóch typów i sposób ich tworzenia, zobacz [rodzaje sygnatur dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). Dostępu współdzielonego adres URL sygnatury wygenerowane z portalu Azure lub Eksploratora usługi Storage platformy Azure jest sygnatury dostępu współdzielonego konta, które nie jest obsługiwane.

> [!TIP]
> Można wykonywać następujące polecenia programu PowerShell, aby wygenerować sygnaturę dostępu współdzielonego usługi dla konta magazynu. Zastąp symbole zastępcze i przyznanie odpowiedniego uprawnienia.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Aby użyć uwierzytelniania sygnatury dostępu współdzielonego usługi, są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną **AzureStorage**. |Yes |
| sasUri | Określ sygnatury dostępu współdzielonego identyfikator URI do zasobów magazynu obiektów blob, kontenera lub tabeli. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. Używając środowiska uruchomieniowego integracji Azure lub środowiska uruchomieniowego integracji Self-hosted (Jeśli w magazynie danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

**Przykład:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Po utworzeniu identyfikatora URI sygnatury dostępu współdzielonego, należy wziąć pod uwagę następujące kwestie:

- Ustaw uprawnienia odczytu/zapisu w odpowiedniej obiekty zależności używania połączonej usługi (Odczyt, zapis, Odczyt/zapis) w fabryce danych.
- Ustaw **czas wygaśnięcia** odpowiednio. Upewnij się, że dostęp do magazynu obiektów nie wygasa w aktywnym okresie potoku.
- Identyfikator URI utworzony na poziomie tabeli po prawej, oparte na potrzeby.

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych tabel Azure.

Aby skopiować dane do i z tabel Azure, ustaw właściwość Typ zestawu danych do **AzureTable**. Następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwości typu zestawu danych musi mieć ustawioną **AzureTable**. |Yes |
| tableName |Nazwa tabeli w wystąpieniu bazy danych magazynu tabel, odnoszący się do połączonej usługi. |Yes |

**Przykład:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schemat fabryka danych

Dla magazynów danych bez schematu, takie jak tabel Azure fabryki danych wnioskuje schemat w jednym z następujących sposobów:

* Jeśli określisz struktury danych za pomocą **struktury** tej struktury Schema honoruje właściwości w definicji zestawu danych, fabryki danych. W tym przypadku jeśli wiersza nie zawiera wartości dla kolumny, wartość null podano dla niego.
* Jeśli nie określisz struktury danych za pomocą **struktury** właściwości w definicji zestawu danych, fabryki danych wnioskuje schemat za pomocą pierwszego wiersza w danych. W takim przypadku jeśli pierwszy wiersz nie zawiera pełnej schematu, niektóre kolumny zostaną pominięte w wyniku operacji kopiowania.

Dla źródeł danych bez schematu, najlepszym rozwiązaniem jest określenie struktury danych za pomocą **struktury** właściwości.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez tabel Azure źródłowy i odbiorczy właściwości.

### <a name="azure-table-as-a-source-type"></a>Tabeli platformy Azure jako typ źródła

Aby skopiować dane z tabel Azure, należy ustawić typ źródła w przypadku działania kopiowania do **AzureTableSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania **AzureTableSource**. |Yes |
| azureTableSourceQuery |Użyj niestandardowych zapytania magazynu tabeli można odczytać danych. Przykłady w następnej sekcji. |Nie |
| azureTableSourceIgnoreTableNotFound |Wskazuje, czy zezwolić na wyjątek tabela nie istnieje.<br/>Dozwolone wartości to **True** i **False** (ustawienie domyślne). |Nie |

### <a name="azuretablesourcequery-examples"></a>Przykłady azureTableSourceQuery

W przypadku tabel Azure kolumny typu Data/Godzina:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Jeśli kolumna tabeli platformy Azure jest typu string:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Jeśli parametr potoku rzutować wartość daty i godziny do właściwego formatu zgodnie z poprzedniej próbki.

### <a name="azure-table-as-a-sink-type"></a>Tabeli platformy Azure jako typ ujścia

Aby skopiować dane do tabeli platformy Azure, należy ustawić typ ujścia w działaniu kopiowania do **AzureTableSink**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **zbiornika** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość typu sink działania kopiowania **AzureTableSink**. |Yes |
| azureTableDefaultPartitionKeyValue |Domyślna wartość klucza partycji, które mogą być używane przez obiekt sink. |Nie |
| azureTablePartitionKeyName |Określ nazwę kolumny, których wartości są używane jako klucze partycji. Jeśli nie zostanie określony, "AzureTableDefaultPartitionKeyValue" jest używana jako klucza partycji. |Nie |
| azureTableRowKeyName |Określ nazwę kolumny, których wartości kolumn używanych jako klucz wiersza. Jeśli nie zostanie określony, użyj identyfikatora GUID dla każdego wiersza. |Nie |
| azureTableInsertType |Tryb do wstawiania danych do tabeli platformy Azure. Ta właściwość określa, czy wartości zastąpienia lub scalić zostać istniejących wierszy w tabeli wyników ze zgodnymi kluczami partycji i wiersza. <br/><br/>Dozwolone wartości to **scalania** (ustawienie domyślne) i **Zastąp**. <br/><br> To ustawienie jest stosowane na poziomie wiersza nie poziomu tabeli. Żadna z tych opcji usuwa wiersze w tabeli danych wyjściowych, które nie istnieją w danych wejściowych. Aby dowiedzieć się, jak działają ustawienia scalania i Zamień, zobacz [jednostki Insert lub merge](https://msdn.microsoft.com/library/azure/hh452241.aspx) i [Wstawianie lub zastępowanie jednostki](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Nie |
| writeBatchSize |Wstawia dane do tabeli platformy Azure, gdy zostaje trafiony writeBatchSize lub writeBatchTimeout.<br/>Dozwolone wartości to liczba całkowita (liczba wierszy). |Nie (wartość domyślna to 10 000) |
| writeBatchTimeout |Wstawia dane do tabeli platformy Azure, gdy zostaje trafiony writeBatchSize lub writeBatchTimeout.<br/>Dozwolone wartości to timespan. Na przykład "00:20:00" (20 minut). |Nie (wartość domyślna to 90 sekund, klient magazynu domyślny limit czasu) |

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

Mapowanie kolumny źródłowej do kolumny docelowej przy użyciu **"translator"** właściwości przed użyciem jako azureTablePartitionKeyName kolumny docelowej.

W poniższym przykładzie kolumna źródłowa DivisionID jest zamapowany na kolumny docelowej DivisionID:

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

Po skopiowaniu danych od i do tabel Azure, następujące mapowania są używane z tabel Azure typów danych do typów danych tymczasowych fabryki danych. Aby poznać sposób działania kopiowania mapowania typu źródłowego: schemat i dane sink, zobacz temat [schemat i dane typu mapowania](copy-activity-schema-and-type-mapping.md).

Podczas przenoszenia danych do i z tabel Azure, następujące [mapowania zdefiniowane przez tabel Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) są używane z typów OData tabeli platformy Azure na typ architektury .NET i na odwrót.

| Typ danych tabeli platformy Azure | Typ danych tymczasowych fabryki danych | Szczegóły |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |Tablica bajtów do 64 KB. |
| Edm.Boolean |wartość logiczna |Wartość logiczna. |
| Edm.DateTime |DateTime |Wartość 64-bitowa, wyrażone jako uniwersalny czas koordynowany (UTC). Północy, 1 stycznia, 1601 r. N.E. rozpoczyna się obsługiwanym zakresem daty i godziny (C.E.), UTC. Zakres kończy się 31 grudnia 9999 r. |
| Edm.Double |double |64-bitowej zmiennej punktu wartości. |
| Edm.Guid |Identyfikator GUID |Globalnie unikatowy identyfikator 128-bitowego. |
| Edm.Int32 |Int32 |32-bitową liczbę całkowitą. |
| Edm.Int64 |Int64 |64-bitową liczbę całkowitą. |
| Edm.String |Ciąg |Wartość algorytmem UTF-16. Ciągi mogą być maksymalnie 64 KB. |

## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
