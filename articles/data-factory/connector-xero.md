---
title: Kopiowanie danych z Xero za pomocą Azure Data Factory
description: Informacje o kopiowaniu danych z programu Xero do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: d52e536170c649cbc84b6c6dce92afb76ffe3125
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680010"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Kopiowanie danych z Xero za pomocą Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z Xero. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Xero jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z Xero można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

Ten łącznik Xero obsługuje:

- Xero [aplikację prywatną](https://developer.xero.com/documentation/getting-started/api-application-types) , ale nie aplikację publiczną.
- Wszystkie tabele Xero (punkty końcowe interfejsu API) z wyjątkiem "Reports". 

Azure Data Factory udostępnia wbudowany sterownik umożliwiający połączenie, dlatego nie trzeba ręcznie instalować żadnego sterownika przy użyciu tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika Xero.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Xero są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć wartość: **Xero** | Tak |
| Host | Punkt końcowy serwera Xero (`api.xero.com`).  | Tak |
| consumerKey | Klucz klienta skojarzony z aplikacją Xero. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| privateKey | Klucz prywatny z pliku PEM, który został wygenerowany dla aplikacji prywatnej Xero, zobacz [Tworzenie pary kluczy publicznych/prywatnych](https://developer.xero.com/documentation/api-guides/create-publicprivate-key). Zwróć uwagę **, aby wygenerować PrivateKey. pem z numbitsem 512** przy użyciu `openssl genrsa -out privatekey.pem 512`; 1024 nie jest obsługiwana. Dołącz cały tekst z pliku PEM włącznie z zakończeniami wierszy systemu UNIX (\n), zobacz przykład poniżej.<br/><br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| useEncryptedEndpoints | Określa, czy punkty końcowe źródła danych są szyfrowane przy użyciu protokołu HTTPS. Wartość domyślna to true.  | Nie |
| useHostVerification | Określa, czy nazwa hosta jest wymagana w certyfikacie serwera, aby odpowiadała nazwie hosta serwera podczas nawiązywania połączenia za pośrednictwem protokołu SSL. Wartość domyślna to true.  | Nie |
| usePeerVerification | Określa, czy należy zweryfikować tożsamość serwera podczas nawiązywania połączenia za pośrednictwem protokołu SSL. Wartość domyślna to true.  | Nie |

**Przykład:**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "host" : "api.xero.com",
            "consumerKey": {
                 "type": "SecureString",
                 "value": "<consumerKey>"
            },
            "privateKey": {
                 "type": "SecureString",
                 "value": "<privateKey>"
            }
        }
    }
}
```

**Wartość przykładowego klucza prywatnego:**

Dołącz cały tekst z pliku PEM włącznie z zakończeniami wierszy systemu UNIX (\n).

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Xero.

Aby skopiować dane z Xero, ustaw właściwość Type zestawu danych na **XeroObject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość: **XeroObject** | Tak |
| tableName | Nazwa tabeli. | Nie (Jeśli określono parametr "query" w źródle działania) |

**Przykład**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Xero.

### <a name="xero-as-source"></a>Xero jako źródło

Aby skopiować dane z Xero, ustaw typ źródła w działaniu Copy na **XeroSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **XeroSource** | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Na przykład: `"SELECT * FROM Contacts"`. | Nie (Jeśli określono "TableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Podczas określania zapytania Xero należy zwrócić uwagę na następujące kwestie:

- Tabele ze złożonymi elementami zostaną podzielone na wiele tabel. Na przykład transakcje bankowe mają złożoną strukturę danych "LineItems", więc dane transakcji bankowej są mapowane na `Bank_Transaction` tabeli i `Bank_Transaction_Line_Items`, z `Bank_Transaction_ID` jako klucz obcy, aby połączyć je ze sobą.

- Dane Xero są dostępne w dwóch schematach: `Minimal` (domyślne) i `Complete`. Kompletny schemat zawiera tabele wywołań wymaganych wstępnie, które wymagają dodatkowych danych (np. kolumny identyfikatora) przed wykonaniem żądanego zapytania.

Poniższe tabele zawierają te same informacje w schemacie minimalnej i kompletnej. Aby zmniejszyć liczbę wywołań interfejsu API, użyj minimalnego schematu (domyślnie).

- Bank_Transactions
- Contact_Groups 
- Kontakty 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- Faktury 
- Invoices_Credit_Notes
- Invoices_ przedpłaty 
- Invoices_Overpayments 
- Manual_Journals 
- Nadpłaty 
- Overpayments_Allocations 
- Opłat 
- Prepayments_Allocations 
- Potwierdzenia 
- Receipt_Validation_Errors 
- Tracking_Categories

Tylko następujące tabele mogą być zapytania z kompletnym schematem:

- Ukończono. Bank_Transaction_Line_Items 
- Ukończono. Bank_Transaction_Line_Item_Tracking 
- Ukończono. Contact_Group_Contacts 
- Ukończ. Contacts_Contact_ 
- Ukończono. Credit_Note_Line_Items 
- Ukończono. Credit_Notes_Line_Items_Tracking 
- Ukończono. Expense_Claim_ płatności 
- Ukończono. Expense_Claim_Receipts 
- Ukończono. Invoice_Line_Items 
- Ukończono. Invoices_Line_Items_Tracking
- Ukończono. Manual_Journal_Lines 
- Ukończono. Manual_Journal_Line_Tracking 
- Ukończono. Overpayment_Line_Items 
- Ukończono. Overpayment_Line_Items_Tracking 
- Ukończono. Prepayment_Line_Items 
- Ukończono. Prepayment_Line_Item_Tracking 
- Ukończono. Receipt_Line_Items 
- Ukończono. Receipt_Line_Item_Tracking 
- Ukończono. Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="next-steps"></a>Następne kroki
Listę obsługiwanych magazynów danych przez działanie kopiowania można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
