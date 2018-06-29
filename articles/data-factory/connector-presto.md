---
title: Kopiowanie danych z Presto przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z Presto do zbiornika obsługiwane magazyny danych za pomocą działania kopiowania w potoku fabryki danych Azure.
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
ms.date: 06/15/2017
ms.author: jingwang
ms.openlocfilehash: 4b3e022bd22242bdc246e1dd30aa6cc3e00134e0
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052627"
---
# <a name="copy-data-from-presto-using-azure-data-factory"></a>Kopiowanie danych z Presto przy użyciu fabryki danych Azure

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z Presto. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

> [!IMPORTANT]
> Ten łącznik jest obecnie w przeglądzie. Możesz wypróbować jej możliwości i przekaż nam swoją opinię. Jeśli w swoim rozwiązaniu chcesz wprowadzić zależność od łączników w wersji zapoznawczej, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z Presto żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

Fabryka danych Azure oferuje wbudowane sterowników, aby umożliwić łączność, w związku z tym nie trzeba ręcznie zainstalowania sterownika korzystania z tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do Presto łącznika.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Obsługiwane są następujące właściwości dla Presto połączonej usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **Presto** | Yes |
| host | Adres IP lub hosta nazwę Presto serwera. (i.e. 192.168.222.160)  | Yes |
| serverVersion | Wersja Presto serwera. (tj. 0.148-t)  | Yes |
| wykaz | Kontekst katalogu dla wszystkich żądań na serwerze.  | Yes |
| port | Port TCP używany przez serwer Presto nasłuchiwanie dla połączeń klienta. Wartość domyślna to 8080.  | Nie |
| Typ authenticationType | Mechanizm uwierzytelniania używany do łączenia się z serwerem Presto. <br/>Dozwolone wartości to: **anonimowe**, **LDAP** | Yes |
| nazwa użytkownika | Nazwa użytkownika używana do łączenia się z serwerem Presto.  | Nie |
| hasło | Hasło odpowiadający nazwie użytkownika. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| enableSsl | Określa, czy połączenia z serwerem są szyfrowane przy użyciu protokołu SSL. Wartość domyślna to false.  | Nie |
| trustedCertPath | Pełna ścieżka pliku PEM, zawierająca zaufane certyfikaty urzędu certyfikacji do weryfikowania serwera podczas nawiązywania połączenia za pośrednictwem protokołu SSL. Tej właściwości można ustawić tylko w przypadku korzystania z protokołu SSL na siebie IR. Wartość domyślna to plik cacerts.pem zainstalowane z IR.  | Nie |
| useSystemTrustStore | Określa, czy ma być używany certyfikat urzędu certyfikacji z magazynu zaufania systemu lub z określonego pliku PEM. Wartość domyślna to false.  | Nie |
| allowHostNameCNMismatch | Określa, czy wymagają nazwy certyfikat wystawiony przez urząd certyfikacji SSL do dopasowania nazwy hosta serwera podczas nawiązywania połączenia za pośrednictwem protokołu SSL. Wartość domyślna to false.  | Nie |
| allowSelfSignedServerCert | Określa, czy certyfikaty z podpisem własnym z serwera. Wartość domyślna to false.  | Nie |
| timeZoneID | Lokalnej strefy czasowej używane przez połączenie. Prawidłowe wartości dla tej opcji określono w bazie danych strefy czasowej przez organizację IANA. Wartość domyślna to strefą czasową systemu.  | Nie |

**Przykład:**

```json
{
    "name": "PrestoLinkedService",
    "properties": {
        "type": "Presto",
        "typeProperties": {
            "host" : "<host>",
            "serverVersion" : "0.148-t",
            "catalog" : "<catalog>",
            "port" : "<port>",
            "authenticationType" : "LDAP",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "timeZoneID" : "Europe/Berlin"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez Presto zestawu danych.

Aby skopiować dane z Presto, ustaw właściwość Typ zestawu danych do **PrestoObject**. Nie ma dodatkowych właściwości określonego typu w tego typu dataset.

**Przykład**

```json
{
    "name": "PrestoDataset",
    "properties": {
        "type": "PrestoObject",
        "linkedServiceName": {
            "referenceName": "<Presto linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez źródło Presto właściwości.

### <a name="prestosource-as-source"></a>PrestoSource jako źródło

Aby skopiować dane z Presto, należy ustawić typ źródła w przypadku działania kopiowania do **PrestoSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **PrestoSource** | Yes |
| query | Użyj niestandardowych zapytania SQL można odczytać danych. Na przykład: `"SELECT * FROM MyTable"`. | Yes |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromPresto",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Presto input dataset name>",
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
                "type": "PrestoSource",
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
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
