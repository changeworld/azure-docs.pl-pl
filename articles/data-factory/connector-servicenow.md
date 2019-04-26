---
title: Kopiowanie danych z usługi ServiceNow przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z usługi ServiceNow do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: 234b78a97c2663121d0d585154695887a58b9522
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203418"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Kopiowanie danych z usługi ServiceNow przy użyciu usługi Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z usługi ServiceNow. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z usługi ServiceNow, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

Usługa Azure Data Factory udostępnia wbudowanego sterownika, aby umożliwić łączność, dlatego nie trzeba ręcznie zainstalować dowolnego sterownika, za pomocą tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika usługi ServiceNow.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla usługi ServiceNow, połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **ServiceNow** | Yes |
| endpoint | Punkt końcowy serwera usługi ServiceNow (`http://<instance>.service-now.com`).  | Yes |
| Element authenticationType | Typ uwierzytelniania do użycia. <br/>Dozwolone wartości to: **Podstawowe**, **OAuth2** | Yes |
| nazwa użytkownika | Nazwa użytkownika używana do łączenia się z serwerem usługi ServiceNow dla uwierzytelniania Basic i protokołu OAuth2.  | Yes |
| hasło | Hasło odpowiadający nazwie użytkownika dla uwierzytelniania Basic i protokołu OAuth2. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| clientId | Identyfikator klienta do uwierzytelniania protokołu OAuth2.  | Nie |
| clientSecret | Klucz tajny klienta do uwierzytelniania protokołu OAuth2. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| useEncryptedEndpoints | Określa, czy punkty końcowe źródła danych są szyfrowane przy użyciu protokołu HTTPS. Wartość domyślna to true.  | Nie |
| useHostVerification | Określa, czy wymagają zgodności nazwy hosta w certyfikacie serwera, aby dopasować nazwę hosta serwera podczas nawiązywania połączenia za pośrednictwem protokołu SSL. Wartość domyślna to true.  | Nie |
| usePeerVerification | Określa, czy do zweryfikowania tożsamości serwera, podczas nawiązywania połączenia za pośrednictwem protokołu SSL. Wartość domyślna to true.  | Nie |

**Przykład:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych usługi ServiceNow.

Aby skopiować dane z usługi ServiceNow, należy ustawić właściwość typu zestawu danych na **ServiceNowObject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **ServiceNowObject** | Yes |
| tableName | Nazwa tabeli. | Nie (Jeśli określono parametr "zapytanie" w źródle działania) |

**Przykład**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło usługi ServiceNow.

### <a name="servicenow-as-source"></a>Usługi ServiceNow jako źródło

Aby skopiować dane z usługi ServiceNow, należy ustawić typ źródła w działaniu kopiowania, aby **ServiceNowSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **ServiceNowSource** | Yes |
| query | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Na przykład: `"SELECT * FROM Actual.alm_asset"`. | Nie (Jeśli określono parametr "tableName" w zestawie danych) |

Należy pamiętać, że podczas określania schematu i kolumn dla usługi ServiceNow w zapytaniu i **dotyczą [porady dotyczące wydajności](#performance-tips) na domniemanie wydajności kopiowania**.

- **Schemat:** Określ schemat, jak `Actual` lub `Display` kwerendę usługi ServiceNow, które można przyjrzeć się go jako parametr `sysparm_display_value` PRAWDA lub FAŁSZ, gdy wywołanie [interfejsów API restful usługi ServiceNow](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Kolumna:** nazwa kolumny do rzeczywistej wartości w obszarze `Actual` schemat jest `[column name]_value`, natomiast w przypadku wartości wyświetlanej w obszarze `Display` schemat jest `[column name]_display_value`. Uwaga nazwa kolumny musi mapowania do schematu, używany w zapytaniu.

**Przykładowe zapytanie:**
`SELECT col_value FROM Actual.alm_asset` OR 
`SELECT col_display_value FROM Display.alm_asset`

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="performance-tips"></a>Porady dotyczące wydajności

### <a name="schema-to-use"></a>Schematu do użycia

Usługi ServiceNow 2 z różnymi schematami, jeden to **"Rzeczywiste"** zwraca dane rzeczywiste, druga **"Wyświetlanie"** zwraca wartości wyświetlania danych. 

Jeśli masz filtr w zapytaniu, należy użyć schemat "Rzeczywiste", który ma lepsze skopiuj wydajności. Podczas wykonywania zapytań względem schematu "Rzeczywiste", ServiceNow natywnie obsługuje filtru podczas pobierania danych, aby zwracać tylko filtrowanym zestawie wyników, podczas wykonywania zapytań względem schematu "Display", ADF pobrać wszystkich danych i Zastosuj filtr wewnętrznie.

### <a name="index"></a>Indeks

Indeks tabeli ServiceNow może pomóc poprawić wydajność zapytań, zobacz [Tworzenie indeksu tabeli](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
