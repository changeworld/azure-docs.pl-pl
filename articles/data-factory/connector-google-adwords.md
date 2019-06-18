---
title: Kopiowanie danych z Google AdWords przy użyciu usługi Azure Data Factory (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z Google AdWords do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
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
ms.openlocfilehash: 0f68627e2db3c08049f0273045906057526bd6aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61094922"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory-preview"></a>Kopiowanie danych z Google AdWords przy użyciu usługi Azure Data Factory (wersja zapoznawcza)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych ze Google AdWords. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

> [!IMPORTANT]
> Ten łącznik jest obecnie w wersji zapoznawczej. Możesz wypróbować tę funkcję i przekazać opinię. Jeśli w swoim rozwiązaniu chcesz wprowadzić zależność od łączników w wersji zapoznawczej, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z Google AdWords, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

Usługa Azure Data Factory udostępnia wbudowanego sterownika, aby umożliwić łączność, dlatego nie trzeba ręcznie zainstalować dowolnego sterownika, za pomocą tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika Google AdWords.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla Google AdWords połączoną usługę:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **GoogleAdWords** | Yes |
| clientCustomerID | Identyfikator klienta klienta konta AdWords, który chcesz pobrać raportować dane.  | Yes |
| developerToken | Skojarzone z kontem menedżera, których używasz do udzielania dostępu do interfejsu API AdWords token dla deweloperów.  Istnieje możliwość Oznacz to pole jako SecureString bezpiecznie przechowywać w usłudze ADF lub przechowywać haseł w usłudze Azure Key Vault i pozwól usłudze ADF kopiowania ściągnięcia działania z tego miejsca, podczas wykonywania kopii danych — Dowiedz się więcej z [Store poświadczeń w usłudze Key Vault](store-credentials-in-key-vault.md). | Yes |
| authenticationType | Mechanizm uwierzytelniania OAuth 2.0 używany do uwierzytelniania. ServiceAuthentication można używać tylko na Self-Hosted IR <br/>Dozwolone wartości to: **ServiceAuthentication**, **UserAuthentication** | Yes |
| refreshToken | Token odświeżania, uzyskany od firmy Google do autoryzowania dostępu do AdWords dla UserAuthentication. Istnieje możliwość Oznacz to pole jako SecureString bezpiecznie przechowywać w usłudze ADF lub przechowywać haseł w usłudze Azure Key Vault i pozwól usłudze ADF kopiowania ściągnięcia działania z tego miejsca, podczas wykonywania kopii danych — Dowiedz się więcej z [Store poświadczeń w usłudze Key Vault](store-credentials-in-key-vault.md). | Nie |
| clientId | Identyfikator klienta aplikacji google używane w celu uzyskania tokenu odświeżania. Istnieje możliwość Oznacz to pole jako SecureString bezpiecznie przechowywać w usłudze ADF lub przechowywać haseł w usłudze Azure Key Vault i pozwól usłudze ADF kopiowania ściągnięcia działania z tego miejsca, podczas wykonywania kopii danych — Dowiedz się więcej z [Store poświadczeń w usłudze Key Vault](store-credentials-in-key-vault.md). | Nie |
| clientSecret | Klucz tajny klienta aplikacji google używane w celu uzyskania tokenu odświeżania. Istnieje możliwość Oznacz to pole jako SecureString bezpiecznie przechowywać w usłudze ADF lub przechowywać haseł w usłudze Azure Key Vault i pozwól usłudze ADF kopiowania ściągnięcia działania z tego miejsca, podczas wykonywania kopii danych — Dowiedz się więcej z [Store poświadczeń w usłudze Key Vault](store-credentials-in-key-vault.md). | Nie |
| email | Identyfikator wiadomości e-mail konta usługi, który służy do ServiceAuthentication i można używać tylko na Self-Hosted IR  | Nie |
| keyFilePath | Pełna ścieżka do pliku klucza p12, który jest używany do uwierzytelniania adres e-mail konta usługi i można używać tylko na Self-Hosted IR  | Nie |
| trustedCertPath | Pełna ścieżka pliku PEM, zawierająca zaufane certyfikaty urzędu certyfikacji w celu sprawdzenia serwer podczas nawiązywania połączenia za pośrednictwem protokołu SSL. Tę właściwość można ustawić tylko w przypadku korzystania z protokołu SSL na Self-Hosted IR Wartością domyślną jest instalowany z wewnątrz pliku cacerts.pem  | Nie |
| useSystemTrustStore | Określa, czy ma być używany certyfikat urzędu certyfikacji z magazynu zaufania systemu lub z określonego pliku PEM. Wartość domyślna to false.  | Nie |

**Przykład:**

```json
{
    "name": "GoogleAdWordsLinkedService",
    "properties": {
        "type": "GoogleAdWords",
        "typeProperties": {
            "clientCustomerID" : "<clientCustomerID>",
            "developerToken": {
                "type": "SecureString",
                "value": "<developerToken>"
            },
            "authenticationType" : "ServiceAuthentication",
            "refreshToken": {
                "type": "SecureString",
                "value": "<refreshToken>"
            },
            "clientId": {
                "type": "SecureString",
                "value": "<clientId>"
            },
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            },
            "email" : "<email>",
            "keyFilePath" : "<keyFilePath>",
            "trustedCertPath" : "<trustedCertPath>",
            "useSystemTrustStore" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Google AdWords.

Aby skopiować dane z Google AdWords, należy ustawić właściwość typu zestawu danych na **GoogleAdWordsObject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **GoogleAdWordsObject** | Yes |
| tableName | Nazwa tabeli. | Nie (Jeśli określono parametr "zapytanie" w źródle działania) |

**Przykład**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}

```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Google AdWords.

### <a name="google-adwords-as-source"></a>Google AdWords jako źródło

Aby skopiować dane z Google AdWords, należy ustawić typ źródła w działaniu kopiowania, aby **GoogleAdWordsSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **GoogleAdWordsSource** | Yes |
| query | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Na przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono parametr "tableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromGoogleAdWords",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleAdWords input dataset name>",
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
                "type": "GoogleAdWordsSource",
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
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
