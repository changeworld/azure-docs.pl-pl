---
title: Kopiuj dane do / z Eksploratora danych Azure przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak skopiować dane do / z Eksploratora danych platformy Azure za pomocą działania kopiowania w potoku usługi Azure Data Factory.
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
ms.date: 04/16/2019
ms.author: orspodek
ms.openlocfilehash: f501257903f3b7c621512f06d1c8c7109e22db1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60394510"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Kopiuj dane do / z Eksploratora danych Azure przy użyciu usługi Azure Data Factory

W tym artykule przedstawiono sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych do lub z [Eksploratora danych usługi Azure](../data-explorer/data-explorer-overview.md). Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do Eksploratora danych platformy Azure. Można również kopiować dane z Eksploratora danych usługi Azure, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła lub ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md) tabeli.

>[!NOTE]
>Kopiowanie danych z Eksploratora danych platformy Azure z i do na magazyn danych lokalnych przy użyciu środowiskiem Integration Runtime jest obsługiwane od wersji 3,14.

Łącznik Eksploratora danych usługi Azure można wykonać następujące czynności:

* Kopiowanie danych przy użyciu uwierzytelniania tokenu aplikacji usługi Azure Active Directory (Azure AD) przy użyciu **nazwy głównej usługi**.
* Jako źródło pobierać dane przy użyciu kwerendy KQL (Kusto).
* Jako obiekt sink dołączyć dane do tabeli docelowej.

## <a name="getting-started"></a>Wprowadzenie

>[!TIP]
>Aby zapoznać się z przewodnikiem za pomocą łącznika Azure Eksploratora danych, zobacz [kopiowanie danych do/z Eksploratora danych Azure przy użyciu usługi Azure Data Factory](../data-explorer/data-factory-load-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi Data Factory określonych do Eksploratora danych usługi Azure łącznika.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Łącznik Azure Eksplorator danych używa uwierzytelniania jednostki usługi. Wykonaj następujące kroki, aby uzyskać nazwy głównej usługi i udzielanie uprawnień:

1. Zarejestruj jednostki aplikacji w usłudze Azure Active Directory (Azure AD), postępując zgodnie z [Zarejestruj swoją aplikację z dzierżawy usługi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Zanotuj następujące wartości, które służą do definiowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Przyznaj usługi głównej odpowiednie uprawnienia w Eksploratorze danych platformy Azure. Zapoznaj się [uprawnienia bazy danych zarządzania Eksploratora usługi Azure Data](../data-explorer/manage-database-permissions.md) ze szczegółowymi informacjami o role i uprawnienia, a także przewodnik dotyczący zarządzania uprawnieniami. Ogólnie rzecz biorąc należy

    - **Jako źródło**, co najmniej udzielić **Podgląd bazy danych** roli bazy danych.
    - **Jako obiekt sink**, co najmniej udzielić **dużych możliwościach skalowania bazy danych** roli bazy danych.

>[!NOTE]
>Tworzenie za pomocą interfejsu użytkownika usługi ADF, operacje listę baz danych na połączonej usługi, lub listę tabel w zestawie danych może wymagać wyższej uprzywilejowanych przyznano uprawnienia do nazwy głównej usługi. Alternatywnie można ręcznie wprowadź nazwę bazy danych i nazwę tabeli. Kopiuj działania wykonywania działań, tak długo, jak nazwa główna usługi jest przyznawana z odpowiednimi uprawnieniami do odczytu/zapisu danych.

Następujące właściwości są obsługiwane w przypadku Eksploratora danych platformy Azure, połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość musi być równa **AzureDataExplorer** | Yes |
| endpoint | Adres URL punktu końcowego klastra Eksploratora danych usługi Azure, w tym formacie co `https://<clusterName>.<regionName>.kusto.windows.net`. | Yes |
| database | Nazwa bazy danych. | Yes |
| tenant | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Jest to, co zwykle wiedzieć jako "**identyfikator urzędu**" w [parametry połączenia Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Pobierz ją, umieszczając kursor myszy za pomocą myszy w prawym górnym rogu witryny Azure portal. | Yes |
| servicePrincipalId | Określ identyfikator klienta aplikacji. Jest to, co zwykle wiedzieć jako "**Identyfikatora klienta aplikacji AAD**" w [parametry połączenia Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Yes |
| servicePrincipalKey | Określ klucz aplikacji. Jest to, co zwykle wiedzieć jako "**klucz aplikacji usługi AAD**" w [parametry połączenia Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |

**Przykład właściwości połączonej usługi:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości, które są obsługiwane przez zestaw danych Eksploratora danych usługi Azure.

Aby skopiować dane do Eksploratora danych platformy Azure, należy ustawić właściwość typu zestawu danych na **AzureDataExplorerTable**.

Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość musi być równa **AzureDataExplorerTable** | Yes |
| table | Nazwa tabeli, która jest połączona usługa przywołuje. | Tak w przypadku ujścia; Brak źródła |

**Przykład właściwości zestawu danych**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "table": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez Eksploratora usługi Azure danych źródła i ujścia.

### <a name="azure-data-explorer-as-source"></a>Eksplorator danych usługi Azure jako źródło

Aby skopiować dane z Eksploratora danych usługi Azure, należy ustawić **typu** właściwości w źródle działania kopiowania **AzureDataExplorerSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** musi być równa właściwość źródła działania kopiowania: **AzureDataExplorerSource** | Yes |
| query | Żądanie tylko do odczytu w [KQL format](/azure/kusto/query/). Zapytanie niestandardowe KQL jest używana jako odwołanie. | Yes |
| queryTimeout | Czas oczekiwania przed żądaniem zapytania upłynie limit czasu. Wartość domyślna to 10 minut (00: 10:00); dozwolona maksymalna wartość to 1 godzina (01: 00:00). | Nie |

>[!NOTE]
>Usługa Azure źródło Eksploratora danych, domyślnie ma limit rozmiaru wynoszący 500 000 rekordów lub 64 MB. Aby pobrać wszystkie rekordy bez obcinania, można określić `set notruncation;` na początku zapytania. Zapoznaj się [zapytania limity](https://docs.microsoft.com/azure/kusto/concepts/querylimits) na więcej szczegółów.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Eksplorator danych usługi Azure jako obiekt sink

Aby skopiować dane do Eksploratora danych platformy Azure, należy ustawić właściwość type w ujścia działania kopiowania **AzureDataExplorerSink**. Następujące właściwości są obsługiwane w działaniu kopiowania **ujścia** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** musi być równa właściwości ujścia działania kopiowania: **AzureDataExplorerSink** | Yes |
| ingestionMappingName | Nazwa wstępnie utworzone **[mapowania](/azure/kusto/management/mappings#csv-mapping)** w tabeli Kusto. Mapowania kolumn ze źródła do Eksploratora danych platformy Azure — w dotyczy **[wszystkie obsługiwane magazyny/format źródła](copy-activity-overview.md#supported-data-stores-and-formats)** tym CSV/JSON/Avro formatuje itp., można użyć działania kopiowania [kolumny Mapowanie](copy-activity-schema-and-type-mapping.md) (niejawnie według nazwy lub jawnie, zgodnie z konfiguracją) i/lub mapowania Eksploratora danych usługi Azure. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>"
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

* Dowiedz się więcej o [kopiowanie danych z usługi Azure Data Factory do Eksploratora danych usługi Azure](/azure/data-explorer/data-factory-load-data).
