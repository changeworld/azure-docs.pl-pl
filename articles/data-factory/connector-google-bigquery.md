---
title: Kopiowanie danych z Google BigQuery przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z Google BigQuery do zbiornika obsługiwane magazyny danych za pomocą działania kopiowania w potoku fabryki danych.
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
ms.date: 02/12/2018
ms.author: jingwang
ms.openlocfilehash: 51cacb385f28cf70a65b9c0e1c14d48e22be0a4d
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051114"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Kopiowanie danych z Google BigQuery przy użyciu fabryki danych Azure

Ten artykuł przedstawia sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z Google BigQuery. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z Google BigQuery żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła lub wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

 Fabryka danych zawiera wbudowane sterowników, aby umożliwić łączność. W związku z tym nie trzeba ręcznie zainstaluj sterownik używanie tego łącznika.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika Google BigQuery.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Następujące właściwości są obsługiwane przez Google BigQuery połączonej usługi.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną **GoogleBigQuery**. | Yes |
| projekt | Identyfikator projektu BigQuery domyślne zapytanie.  | Yes |
| additionalProjects | Rozdzielana przecinkami lista identyfikatorów projektów publicznych BigQuery projekty do dostępu.  | Nie |
| requestGoogleDriveScope | Określa, czy żądania dostępu do dysku Google. Zezwalanie na dostęp dysk Google umożliwia obsługę tabel federacyjnych łączące dane BigQuery z danymi w usłudze dysk Google. Wartość domyślna to **false**.  | Nie |
| Typ authenticationType | Mechanizm uwierzytelniania OAuth 2.0, używany do uwierzytelniania. ServiceAuthentication może być używany tylko Self-hosted integracji w czasie wykonywania. <br/>Dozwolone wartości to **UserAuthentication** i **ServiceAuthentication**. Odpowiednio można znaleźć w sekcjach poniżej tej tabeli na więcej właściwości i przykłady JSON dla tych typów uwierzytelniania. | Yes |

### <a name="using-user-authentication"></a>Uwierzytelnianie użytkownika

Ustaw dla właściwości "authenticationType" **UserAuthentication**, a następnie określ następujące właściwości wraz z właściwości ogólnych opisanych w poprzedniej sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| clientId | Identyfikator aplikacji, używane do generowania tokenu odświeżania. | Nie |
| clientSecret | Klucz tajny aplikacji używane do generowania tokenu odświeżania. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| refreshToken | Token odświeżania uzyskane z używany do autoryzacji dostępu do BigQuery Google. Dowiedz się, jak można uzyskać z [tokenów dostępu do uzyskania OAuth 2.0](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) i [ten blog społeczności](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |

**Przykład:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>Przy użyciu usługi uwierzytelniania

Ustaw dla właściwości "authenticationType" **ServiceAuthentication**, a następnie określ następujące właściwości wraz z właściwości ogólnych opisanych w poprzedniej sekcji. Ten typ uwierzytelniania może być używany tylko środowiska uruchomieniowego integracji Self-hosted.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| wyślij wiadomość e-mail | Identyfikator konta usługi poczty e-mail używany do ServiceAuthentication. Może służyć tylko na Self-hosted integracji w czasie wykonywania.  | Nie |
| keyFilePath | Pełna ścieżka do pliku klucza .p12, który jest używany do uwierzytelniania adres e-mail konta usługi. | Nie |
| trustedCertPath | Pełna ścieżka pliku PEM, który zawiera zaufane certyfikaty urzędu certyfikacji służącego do weryfikowania serwer podczas nawiązywania połączenia za pośrednictwem protokołu SSL. Tej właściwości można ustawić tylko wtedy, gdy na środowiska uruchomieniowego integracji Self-hosted za pomocą protokołu SSL. Wartość domyślna to plik cacerts.pem zainstalowane ze środowiskiem uruchomieniowym integracji.  | Nie |
| useSystemTrustStore | Określa, czy ma być używany certyfikat urzędu certyfikacji z magazynu zaufania systemu lub z pliku PEM określony. Wartość domyślna to **false**.  | Nie |

**Przykład:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
} 
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Google BigQuery.

Aby skopiować dane z Google BigQuery, ustaw właściwość Typ zestawu danych do **GoogleBigQueryObject**. Nie ma dodatkowych właściwości określonego typu w tego typu dataset.

**Przykład**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez ten typ źródła Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource jako typ źródła

Aby skopiować dane z Google BigQuery, należy ustawić typ źródła w przypadku działania kopiowania do **GoogleBigQuerySource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania **GoogleBigQuerySource**. | Yes |
| query | Użyj niestandardowych zapytania SQL można odczytać danych. Może to być na przykład `"SELECT * FROM MyTable"`. | Yes |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
