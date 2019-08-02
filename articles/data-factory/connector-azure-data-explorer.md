---
title: Kopiowanie danych do lub z usługi Azure Eksplorator danych przy użyciu Azure Data Factory
description: Dowiedz się, jak kopiować dane do lub z usługi Azure Eksplorator danych przy użyciu działania kopiowania w potoku Azure Data Factory.
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
ms.date: 08/01/2019
ms.author: orspodek
ms.openlocfilehash: a7ac0bdc2bd5eed802f6959a628dee4c8141dbd1
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720802"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Kopiowanie danych do lub z usługi Azure Eksplorator danych przy użyciu Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych do lub z [usługi Azure Eksplorator danych](../data-explorer/data-explorer-overview.md). Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Dane można kopiować z dowolnego obsługiwanego źródłowego magazynu danych na platformę Azure Eksplorator danych. Możesz również skopiować dane z usługi Azure Eksplorator danych do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła lub ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md) tabeli.

>[!NOTE]
>Kopiowanie danych do/z usługi Azure Eksplorator danych z/do lokalnego magazynu danych za pomocą samoobsługowego Integration Runtime jest obsługiwane od wersji 3,14.

Łącznik usługi Azure Eksplorator danych umożliwia wykonywanie następujących czynności:

* Kopiowanie danych za pomocą usługi Azure Active Directory (Azure AD) uwierzytelnianie tokenu aplikacji z jednostką **usług**.
* Jako źródło Pobierz dane przy użyciu zapytania KQL (Kusto).
* Jako ujścia Dodaj dane do tabeli docelowej.

## <a name="getting-started"></a>Wprowadzenie

>[!TIP]
>Aby zapoznać się z przewodnikiem dotyczącym korzystania z usługi Azure Eksplorator danych Connector, zobacz [Kopiowanie danych do/z platformy azure Eksplorator danych przy użyciu Azure Data Factory](../data-explorer/data-factory-load-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania Data Factory jednostek specyficznych dla łącznika usługi Azure Eksplorator danych.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Łącznik usługi Azure Eksplorator danych używa uwierzytelniania jednostki usługi. Wykonaj następujące kroki, aby uzyskać nazwę główną usługi i udzielić uprawnień:

1. Zarejestruj jednostki aplikacji w usłudze Azure Active Directory (Azure AD), postępując zgodnie z [Zarejestruj swoją aplikację z dzierżawy usługi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Zanotuj następujące wartości, które służą do definiowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Przyznaj jednostce usługi odpowiednie uprawnienia w usłudze Azure Eksplorator danych. Zapoznaj się z [uprawnieniami do zarządzania usługą Azure Eksplorator danych Database](../data-explorer/manage-database-permissions.md) , podając szczegółowe informacje na temat ról i uprawnień, a także wskazówki dotyczące zarządzania uprawnieniami. Ogólnie rzecz biorąc, należy

    - **Jako źródło**nadaj bazie danych co najmniej rolę **przeglądarki baz danych** .
    - **Jako ujścia**, udziel co najmniej roli pozyskiwania **bazy danych** do bazy danych.

>[!NOTE]
>Korzystając z interfejsu użytkownika usługi ADF do tworzenia, konto użytkownika logowania służy do wyświetlania listy klastrów, baz danych i tabel platformy Azure Eksplorator danych. Ręcznie wprowadź nazwę, jeśli nie masz uprawnień do takiej operacji.

Dla połączonej usługi Azure Eksplorator danych są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| — typ | Właściwość **Type** musi być ustawiona na wartość **AzureDataExplorer** | Tak |
| endpoint | Adres URL punktu końcowego klastra Eksplorator danych platformy Azure z formatem jako `https://<clusterName>.<regionName>.kusto.windows.net`. | Tak |
| database | Nazwa bazy danych. | Yes |
| tenant | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Jest to zazwyczaj znany jako "**Identyfikator urzędu**" w parametrach [połączenia Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Pobierz ją przez umieszczenie kursora myszy w prawym górnym rogu Azure Portal. | Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. Jest to zwykle znane jako "**Identyfikator klienta aplikacji usługi AAD**" w [parametrach połączenia Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Tak |
| servicePrincipalKey | Określ klucz aplikacji. Jest to zwykle znane jako "**klucz aplikacji usługi AAD**" w [parametrach połączenia Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |

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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości, które są obsługiwane przez zestaw danych Eksplorator danych platformy Azure.

Aby skopiować dane do usługi Azure Eksplorator danych, ustaw właściwość Type zestawu danych na **AzureDataExplorerTable**.

Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** musi być ustawiona na wartość **AzureDataExplorerTable** | Tak |
| table | Nazwa tabeli, do której odwołuje się połączona usługa. | Tak dla ujścia; Nie dla źródła |

**Przykład właściwości zestawu danych**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez usługę Azure Eksplorator danych Source i ujścia.

### <a name="azure-data-explorer-as-source"></a>Eksplorator danych platformy Azure jako źródło

Aby skopiować dane z usługi Azure Eksplorator danych, ustaw właściwość **Type** w źródle działania Copy na **AzureDataExplorerSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** źródła działania Copy musi mieć ustawioną wartość: **AzureDataExplorerSource** | Tak |
| query | Żądanie tylko do odczytu podawane w [formacie KQL](/azure/kusto/query/). Użyj niestandardowego zapytania KQL jako odwołania. | Tak |
| queryTimeout | Czas oczekiwania przed upływem limitu czasu żądania zapytania. Wartość domyślna to 10 min (00:10:00); dozwolona maksymalna wartość to 1 godzina (01:00:00). | Nie |
| notruncateer | Wskazuje, czy ma zostać obcięta zwracany zestaw wyników. Domyślnie wynik jest obcinany po 500 000 rekordach lub 64 MB. Obcinanie jest zdecydowanie zalecane do prawidłowego zachowania działania. |Nie |

>[!NOTE]
>Źródło Eksplorator danych platformy Azure domyślnie ma limit rozmiaru 500 000 rekordów lub 64 MB. Aby pobrać wszystkie rekordy bez obcinania, można określić `set notruncation;` na początku zapytania. Zapoznaj się z [limitami zapytania](https://docs.microsoft.com/azure/kusto/concepts/querylimits) , aby uzyskać więcej szczegółów.

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

### <a name="azure-data-explorer-as-sink"></a>Eksplorator danych platformy Azure jako ujścia

Aby skopiować dane do usługi Azure Eksplorator danych, ustaw właściwość Type w ujścia działania Copy na **AzureDataExplorerSink**. Następujące właściwości są obsługiwane w działaniu kopiowania **ujścia** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** ujścia działania Copy musi mieć ustawioną wartość: **AzureDataExplorerSink** | Yes |
| ingestionMappingName | Nazwa wstępnie utworzonego **[mapowania](/azure/kusto/management/mappings#csv-mapping)** dla tabeli Kusto. Aby zmapować kolumny ze źródła na Eksplorator danych platformy Azure, które mają zastosowanie do **[wszystkich obsługiwanych magazynów/formatów źródłowych](copy-activity-overview.md#supported-data-stores-and-formats)** , w tym formatów CSV/JSON/Avro itp., można użyć [mapowania kolumny](copy-activity-schema-and-type-mapping.md) działania kopiowania (niejawnie według nazwy lub jawnie zgodnie z konfiguracją). /or mapowania Eksplorator danych platformy Azure. | Nie |

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

* Dowiedz się więcej o [kopiowaniu danych z Azure Data Factory na platformę Azure Eksplorator danych](/azure/data-explorer/data-factory-load-data).