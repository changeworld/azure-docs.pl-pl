---
title: Kopiowanie danych z Impala przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z Impala do zbiornika obsługiwane magazyny danych za pomocą działania kopiowania w potoku fabryki danych.
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
ms.openlocfilehash: 366d0945bfac8546aa757648b6f797c2605a43ea
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045871"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory"></a>Kopiowanie danych z Impala przy użyciu fabryki danych Azure

Ten artykuł przedstawia sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z Impala. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

> [!IMPORTANT]
> Ten łącznik jest obecnie w przeglądzie. Możesz wypróbować jej możliwości i wyrazić swoją opinię. Jeśli w swoim rozwiązaniu chcesz wprowadzić zależność od łączników w wersji zapoznawczej, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z Impala żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła lub wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

 Fabryka danych zawiera wbudowane sterowników, aby umożliwić łączność. W związku z tym nie trzeba ręcznie zainstaluj sterownik używanie tego łącznika.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika Impala.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Następujące właściwości są obsługiwane przez Impala połączonej usługi.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną **Impala**. | Yes |
| host | IP adres lub nazwę hosta serwera Impala (czyli 192.168.222.160).  | Yes |
| port | Port TCP używany przez serwer Impala nasłuchiwanie dla połączeń klienta. Wartość domyślna to 21050.  | Nie |
| Typ authenticationType | Typ uwierzytelniania do użycia. <br/>Dozwolone wartości to **anonimowe**, **SASLUsername**, i **UsernameAndPassword**. | Yes |
| nazwa użytkownika | Nazwa użytkownika używana do uzyskiwania dostępu do serwera Impala. Wartość domyślna to anonimowe, korzystając z SASLUsername.  | Nie |
| hasło | Hasło, które odpowiada nazwie użytkownika, gdy używasz UsernameAndPassword. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| enableSsl | Określa, czy połączenia z serwerem są szyfrowane przy użyciu protokołu SSL. Wartość domyślna to **false**.  | Nie |
| trustedCertPath | Pełna ścieżka pliku PEM, który zawiera zaufane certyfikaty urzędu certyfikacji służącego do weryfikowania serwer podczas nawiązywania połączenia za pośrednictwem protokołu SSL. Tej właściwości można ustawić tylko wtedy, gdy na środowiska uruchomieniowego integracji Self-hosted za pomocą protokołu SSL. Wartość domyślna to plik cacerts.pem zainstalowane ze środowiskiem uruchomieniowym integracji.  | Nie |
| useSystemTrustStore | Określa, czy ma być używany certyfikat urzędu certyfikacji z magazynu zaufania systemu lub z określonego pliku PEM. Wartość domyślna to **false**.  | Nie |
| allowHostNameCNMismatch | Określa, czy wymagają nazwy certyfikat wystawiony przez urząd certyfikacji SSL do dopasowania nazwy hosta serwera podczas nawiązywania połączenia za pośrednictwem protokołu SSL. Wartość domyślna to **false**.  | Nie |
| allowSelfSignedServerCert | Określa, czy certyfikaty z podpisem własnym z serwera. Wartość domyślna to **false**.  | Nie |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych jest dostępny publicznie) można użyć środowiska uruchomieniowego integracji Self-hosted lub środowiska uruchomieniowego integracji Azure. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

**Przykład:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez zestaw danych Impala właściwości.

Aby skopiować dane z Impala, ustaw właściwość Typ zestawu danych do **ImpalaObject**. Nie ma dodatkowych właściwości określonego typu w tego typu dataset.

**Przykład**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez ten typ źródła Impala właściwości.

### <a name="impala-as-a-source-type"></a>Impala jako typ źródła

Aby skopiować dane z Impala, należy ustawić typ źródła w przypadku działania kopiowania do **ImpalaSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania **ImpalaSource**. | Yes |
| query | Użyj niestandardowych zapytania SQL można odczytać danych. Może to być na przykład `"SELECT * FROM MyTable"`. | Yes |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
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
