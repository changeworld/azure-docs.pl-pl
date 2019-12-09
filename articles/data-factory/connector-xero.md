---
title: Kopiowanie danych z Xero za pomocą Azure Data Factory
description: Dowiedz się, jak skopiować dane z usługi Xero do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: 1f6404da163e075b63a99a1d8474cdba4e064b06
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930879"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Kopiowanie danych z Xero za pomocą Azure Data Factory

W tym artykule opisano sposób użycia działania kopiowania w usłudze Azure Data Factory, aby skopiować dane z usługi Xero. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik Xero jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Możesz skopiować dane z usługi Xero, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik usługi Xero obsługuje:

- Xero [prywatnego aplikacji](https://developer.xero.com/documentation/getting-started/api-application-types) , ale aplikacja nie jest publiczny.
- Wszystkie usługi Xero tabele (punkty końcowe interfejsu API) z wyjątkiem "Raporty". 

Usługa Azure Data Factory udostępnia wbudowanego sterownika, aby umożliwić łączność, dlatego nie trzeba ręcznie zainstalować dowolnego sterownika, za pomocą tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika usługi Xero.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku usługi Xero połączone:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **Xero** | Tak |
| host | Punkt końcowy serwera usługi Xero (`api.xero.com`).  | Tak |
| consumerKey | Klucz klienta skojarzonego z aplikacją usługi Xero. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| privateKey | Klucz prywatny z pliku PEM został wygenerowany dla aplikacji prywatnych Xero, zobacz [tworzenia pary kluczy publiczny/prywatny](https://developer.xero.com/documentation/api-guides/create-publicprivate-key). Należy pamiętać, aby **Generowanie privatekey.pem z numbits 512** przy użyciu `openssl genrsa -out privatekey.pem 512`; 1024 nie jest obsługiwane. Obejmować cały tekst z pliku PEM, w tym endings(\n) wiersza systemu Unix, zobacz poniższy przykład.<br/><br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| useEncryptedEndpoints | Określa, czy punkty końcowe źródła danych są szyfrowane przy użyciu protokołu HTTPS. Wartość domyślna to true.  | Nie |
| useHostVerification | Określa, czy nazwa hosta jest wymagany w certyfikacie serwera, aby dopasować nazwę hosta serwera podczas nawiązywania połączenia za pośrednictwem protokołu SSL. Wartość domyślna to true.  | Nie |
| usePeerVerification | Określa, czy do zweryfikowania tożsamości serwera, podczas nawiązywania połączenia za pośrednictwem protokołu SSL. Wartość domyślna to true.  | Nie |

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

**Wartość klucza prywatnego próbek:**

Obejmować cały tekst z pliku PEM, w tym endings(\n) wiersza systemu Unix.

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestawu danych Xero.

Aby skopiować dane z usługi Xero, należy ustawić właściwość typu zestawu danych na **XeroObject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **XeroObject** | Tak |
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

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Xero.

### <a name="xero-as-source"></a>Xero jako źródło

Aby skopiować dane z usługi Xero, należy ustawić typ źródła w działaniu kopiowania, aby **XeroSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **XeroSource** | Tak |
| query | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Na przykład: `"SELECT * FROM Contacts"`. | Nie (Jeśli określono parametr "tableName" w zestawie danych) |

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

Podczas określania zapytania Xero, należy pamiętać o następujących:

- Tabele z złożonych elementów zostanie ona podzielona na wiele tabel. Na przykład transakcji bankowych ma strukturę złożoną danych "LineItems", dlatego dane transakcji bankowych jest mapowany do tabeli `Bank_Transaction` i `Bank_Transaction_Line_Items`, za pomocą `Bank_Transaction_ID` jako klucz obcy, aby połączyć je ze sobą.

- Xero dane są dostępne za pośrednictwem dwóch schematów: `Minimal` (ustawienie domyślne) i `Complete`. Pełne schemat zawiera tabele wywołanie wymagań wstępnych, które wymagają dodatkowych danych (np. kolumna ID) przed wprowadzeniem żądanego zapytania.

Poniższe tabele mają te same informacje w schemacie minimalnym i kompletne. Aby zmniejszyć liczbę wywołań interfejsu API, Użyj schematu minimalny (ustawienie domyślne).

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
- Przedpłaty Invoices_ 
- Invoices_Overpayments 
- Manual_Journals 
- Nadpłat 
- Overpayments_Allocations 
- Przedpłaty 
- Prepayments_Allocations 
- Potwierdzenia 
- Receipt_Validation_Errors 
- Tracking_Categories

Poniższe tabele może być odpytywany tylko ze schematem ukończone:

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Osoby Complete.Contacts_Contact_ 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ płatności 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę obsługiwanych magazynów danych przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
