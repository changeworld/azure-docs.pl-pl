---
title: Kopiowanie danych z usługi Google Analytics za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z usługi Google BigQuery do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi fabryka danych.
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
ms.openlocfilehash: c9320c8d0cf512bc9145accc07ab4c79630a7c84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60808893"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Kopiowanie danych z usługi Google Analytics za pomocą usługi Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z usługi Google Analytics. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z usługi Google Analytics, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła lub ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

Data Factory oferuje wbudowane sterowników, aby włączyć łączność. W związku z tym nie trzeba ręcznie zainstalować sterownik, aby użyć tego łącznika.

>[!NOTE]
>Ten łącznik Google BigQuery bazuje na interfejsach BigQuery. Należy pamiętać, że limity BigQuery maksymalna szybkość przychodzące żądania i wymusza odpowiednie limity przydziału dla poszczególnych projektów, odnoszą się do [limity przydziału i limity - żądań interfejsu API](https://cloud.google.com/bigquery/quotas#api_requests). Upewnij się, że nie wyzwalają za dużo współbieżnych żądań do konta.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika usługi Google BigQuery.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla usługi Google BigQuery połączoną usługę.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **GoogleBigQuery**. | Yes |
| project | Identyfikator projektu domyślny projekt BigQuery dla zapytania.  | Yes |
| additionalProjects | Rozdzielana przecinkami lista identyfikatorów projektu publicznych BigQuery projekty do dostępu.  | Nie |
| requestGoogleDriveScope | Określa, czy żądanie dostępu do usługi dysk Google. Zezwolenie na dostęp do usługi dysk Google umożliwia obsługę tabel federacyjnych, które łączą dane BigQuery przy użyciu danych z usługi dysk Google. Wartość domyślna to **false**.  | Nie |
| authenticationType | Mechanizm uwierzytelniania OAuth 2.0 używany do uwierzytelniania. ServiceAuthentication może być używany tylko dla środowiskiem Integration Runtime. <br/>Dozwolone wartości to **UserAuthentication** i **ServiceAuthentication**. Zapoznaj się sekcje poniżej tej tabeli na więcej właściwości i przykłady kodu JSON dla tych typów uwierzytelniania, odpowiednio. | Yes |

### <a name="using-user-authentication"></a>Przy użyciu uwierzytelniania użytkownika

Ustaw właściwość "authenticationType" **UserAuthentication**, a następnie określ następujące właściwości wraz z ogólne właściwości opisanych w poprzedniej sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| clientId | Identyfikator aplikacji, używany do generowania tokenu odświeżania. | Nie |
| clientSecret | Klucz tajny aplikacji używane do generowania tokenu odświeżania. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| refreshToken | Token odświeżania, uzyskany od firmy Google, służące do autoryzowania dostępu do BigQuery. Dowiedz się, jak je z [tokenów dostępu Uzyskiwanie OAuth 2.0](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) i [ten blog społeczności](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |

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

### <a name="using-service-authentication"></a>Przy użyciu uwierzytelniania usługi

Ustaw właściwość "authenticationType" **ServiceAuthentication**, a następnie określ następujące właściwości wraz z ogólne właściwości opisanych w poprzedniej sekcji. Ten typ uwierzytelniania może służyć tylko na środowiskiem Integration Runtime.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| email | Identyfikator konta usługi poczty e-mail, służąca do ServiceAuthentication. Może służyć tylko na środowiskiem Integration Runtime.  | Nie |
| keyFilePath | Pełna ścieżka do pliku klucza p12, który jest używany do uwierzytelniania adres e-mail konta usługi. | Nie |
| trustedCertPath | Pełna ścieżka pliku PEM, który zawiera certyfikatów zaufanego urzędu certyfikacji służącego do weryfikowania serwera, po nawiązaniu połączenia za pośrednictwem protokołu SSL. Tę właściwość można ustawić tylko wtedy, gdy używasz protokołu SSL na środowiskiem Integration Runtime. Wartość domyślna to plik cacerts.pem zainstalowane za pomocą środowiska integration runtime.  | Nie |
| useSystemTrustStore | Określa, czy ma być używany certyfikat urzędu certyfikacji z magazynu zaufania systemu lub z pliku określonego PEM. Wartość domyślna to **false**.  | Nie |

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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych usługi Google BigQuery.

Aby skopiować dane z usługi Google Analytics, należy ustawić właściwość typu zestawu danych na **GoogleBigQueryObject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **GoogleBigQueryObject** | Yes |
| tableName | Nazwa tabeli. | Nie (Jeśli określono parametr "zapytanie" w źródle działania) |

**Przykład**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez typ źródła w usłudze Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource jako typ źródła

Aby skopiować dane z usługi Google Analytics, należy ustawić typ źródłowego w działaniu kopiowania, aby **GoogleBigQuerySource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być równa **GoogleBigQuerySource**. | Yes |
| query | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Może to być na przykład `"SELECT * FROM MyTable"`. | Nie (Jeśli określono parametr "tableName" w zestawie danych) |

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
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
