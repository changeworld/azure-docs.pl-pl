---
title: Kopiowanie danych z Xero przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z Xero do zbiornika obsługiwane magazyny danych za pomocą działania kopiowania w potoku fabryki danych Azure.
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
ms.date: 06/15/2018
ms.author: jingwang
ms.openlocfilehash: 17341e8431ffd5cc41fdda86a7511688dcabaf45
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045388"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Kopiowanie danych z Xero przy użyciu fabryki danych Azure

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z Xero. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

> [!IMPORTANT]
> Ten łącznik jest obecnie w przeglądzie. Możesz wypróbować jej możliwości i wyrazić swoją opinię. Jeśli w swoim rozwiązaniu chcesz wprowadzić zależność od łączników w wersji zapoznawczej, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z Xero żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik Xero obsługuje:

- Xero [prywatnej aplikacji](https://developer.xero.com/documentation/getting-started/api-application-types) , ale aplikacja nie jest publiczna.
- Wszystkie Xero tabele (punkty końcowe interfejsu API) z wyjątkiem "Raporty". 

Fabryka danych Azure oferuje wbudowane sterowników, aby umożliwić łączność, w związku z tym nie trzeba ręcznie zainstalowania sterownika korzystania z tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do Xero łącznika.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Xero połączone usługi, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **Xero** | Yes |
| host | Punkt końcowy serwera Xero (`api.xero.com`).  | Yes |
| consumerKey | Klucz klienta skojarzone z aplikacją Xero. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| privateKey | Klucz prywatny z pliku PEM został wygenerowany dla aplikacji prywatnej Xero, zobacz [utworzyć pary kluczy publiczny/prywatny](https://developer.xero.com/documentation/api-guides/create-publicprivate-key). Należy pamiętać, aby **Generowanie privatekey.pem z numbits 512** przy użyciu `openssl genrsa -out privatekey.pem 512`; 1024 nie jest obsługiwane. Obejmować cały tekst z pliku PEM, takich jak endings(\n) wiersza Unix, zobacz poniższy przykład.<br/><br/>Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| useEncryptedEndpoints | Określa, czy punkty końcowe źródła danych są szyfrowane przy użyciu protokołu HTTPS. Wartość domyślna to true.  | Nie |
| useHostVerification | Określa, czy nazwa hosta jest wymagany w certyfikacie serwera do dopasowania nazwy hosta serwera podczas nawiązywania połączenia za pośrednictwem protokołu SSL. Wartość domyślna to true.  | Nie |
| usePeerVerification | Określa, czy można zweryfikować tożsamości serwera podczas nawiązywania połączenia za pośrednictwem protokołu SSL. Wartość domyślna to true.  | Nie |

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

Obejmować cały tekst z pliku PEM, takich jak endings(\n) wiersza systemu Unix.

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez zestaw danych Xero właściwości.

Aby skopiować dane z Xero, ustaw właściwość Typ zestawu danych do **XeroObject**. Nie ma dodatkowych właściwości określonego typu w tego typu dataset.

**Przykład**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez źródło Xero właściwości.

### <a name="xero-as-source"></a>Xero jako źródło

Aby skopiować dane z Xero, należy ustawić typ źródła w przypadku działania kopiowania do **XeroSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **XeroSource** | Yes |
| query | Użyj niestandardowych zapytania SQL można odczytać danych. Na przykład: `"SELECT * FROM Contacts"`. | Yes |

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

Należy pamiętać, że podczas określania Xero zapytania:

- Tabele z złożonych elementów zostaną podzielone na wiele tabel. Na przykład transakcji bankowych ma to struktura danych złożonych "LineItems", więc danych bank transakcji jest mapowany na tabelę `Bank_Transaction` i `Bank_Transaction_Line_Items`, z `Bank_Transaction_ID` jako klucz obcy do nawiązania połączenia ze sobą.

- Xero danych jest dostępna za pośrednictwem dwóch schematów: `Minimal` (ustawienie domyślne) i `Complete`. Pełny schemat zawiera tabele wywołania wymagań wstępnych, które wymagają dodatkowych danych (np. w kolumnie identyfikator) przed wprowadzeniem żądanego zapytania.

Poniższe tabele zawierają tych samych informacji w schemacie minimalnego i kompletne. Aby zmniejszyć liczbę wywołań interfejsu API, Użyj schematu minimalnego (ustawienie domyślne).

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

Poniższe tabele mogą być przeszukiwane tylko ze schematem pełną:

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

## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwanych przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
