---
title: Kopiowanie danych z i do usługi Dynamics CRM lub Dynamics 365 (typowe danych usługa) przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z programu Microsoft Dynamics CRM, Microsoft Dynamics 365 (typowe usługi danych) do obsługiwanych sink magazyny danych lub z obsługiwane magazyny danych źródła do usługi Dynamics CRM lub Dynamics 365 za pomocą działania kopiowania w potoku fabryki danych.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jingwang
ms.openlocfilehash: 2f56443eb41e2a7f723e95f86f39c5cc47e82f6f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Kopiowanie danych z i do Dynamics 365 (typowe Usługa danych) lub usługi Dynamics CRM przy użyciu fabryki danych Azure

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z i do programu Microsoft Dynamics 365 lub Microsoft Dynamics CRM. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 fabryki danych, która jest ogólnie dostępna, zobacz [działanie kopiowania w wersji 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z Dynamics 365 (typowe Usługa danych) lub usługi Dynamics CRM żadnych obsługiwanych ujścia magazynu danych. Możesz również skopiować danych z dowolnego magazynu danych źródła obsługiwanych Dynamics 365 (typowe Usługa danych) lub usługi Dynamics CRM. Lista magazynów danych obsługiwane jako źródła lub wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

Ten łącznik Dynamics obsługuje następujące wersje programu Dynamics i typy uwierzytelniania. (IFD jest skrót wdrożenia internetowy).

| Dynamics wersji | Typy uwierzytelniania | Przykłady połączonej usługi |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM Online | Office365 | [Dynamics online + uwierzytelnianie usługi Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 lokalnej z IFD <br> Dynamics CRM 2016 na lokalnym IFD <br> Dynamics CRM 2015 lokalnej z IFD | IFD | [Dynamics lokalnej z IFD + IFD uwierzytelniania](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Dynamics 365 w szczególności następujące typy aplikacji są obsługiwane:

- Dynamics 365 sprzedaży
- Dynamics 365 dla klientów usługi
- Dynamics 365 usługi pola
- Dynamics 365 projektu usługi automatyzacji
- Dynamics 365 Marketing

Inna aplikacja typów, np. operacje i finansowych, talenty, itp. nie są obsługiwane.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do Dynamics.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Następujące właściwości są obsługiwane dla połączonej usługi Dynamics.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 i Dynamics CRM Online

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną **Dynamics**. | Yes |
| deploymentType | Typ wdrożenia wystąpienia programu Dynamics. Musi to być **"Online"** dla Dynamics w trybie online. | Yes |
| serviceUri | Adres URL usługi programu Dynamics wystąpienia, np. `https://adfdynamics.crm.dynamics.com`. | Yes |
| Typ authenticationType | Typ uwierzytelniania do nawiązania połączenia z serwerem programu Dynamics. Określ **"Usługi Office 365"** dla Dynamics w trybie online. | Yes |
| nazwa użytkownika | Określ nazwę użytkownika, aby nawiązać połączenie Dynamics. | Yes |
| hasło | Podaj hasło dla konta użytkownika, który został określony jako nazwy użytkownika. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. | Nie źródła, tak dla obiekt sink Jeśli źródło połączona usługa nie ma środowiska uruchomieniowego integracji |

>[!IMPORTANT]
>Po skopiowaniu danych do programu Dynamics domyślnego środowiska uruchomieniowego integracji Azure nie można wykonać kopiowania. Innymi słowy, jeśli połączone źródła usługa nie ma określonej integracji środowiska uruchomieniowego, jawnie [utworzyć środowiska uruchomieniowego integracji Azure](create-azure-integration-runtime.md#create-azure-ir) z lokalizacją w pobliżu wystąpienia Dynamics. Skojarzyć go w połączonej usłudze Dynamics jak w poniższym przykładzie.

>[!NOTE]
>Łącznik programu Dynamics, używany do identyfikacji wystąpienia usługi Dynamics CRM/365 Online za pomocą właściwości opcjonalne "nazwa_organizacji". Podczas jego śledzi pracy, są sugerowane aby zamiast tego określ nową właściwość "serviceUri", aby uzyskać lepszą wydajność odnajdywania dla wystąpienia.

**Przykład: Dynamics online przy użyciu uwierzytelniania usługi Office 365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 i Dynamics CRM na lokalnym IFD

*Dodatkowe właściwości, które porównania Dynamics online są "hosta" i "port".*

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną **Dynamics**. | Yes |
| deploymentType | Typ wdrożenia wystąpienia programu Dynamics. Musi to być **"OnPremisesWithIfd"** dla Dynamics lokalnego z IFD.| Yes |
| Nazwa hosta | Nazwa hosta serwera Dynamics lokalnego. | Yes |
| port | Port serwera Dynamics lokalnego. | Nie, domyślną jest 443 |
| Nazwa_organizacji | Nazwa organizacji Dynamics wystąpienia. | Yes |
| Typ authenticationType | Typ uwierzytelniania do nawiązania połączenia z serwerem Dynamics. Określ **"Ifd"** dla Dynamics lokalnego z IFD. | Yes |
| nazwa użytkownika | Określ nazwę użytkownika, aby nawiązać połączenie Dynamics. | Yes |
| hasło | Podaj hasło dla konta użytkownika, który został określony jako nazwy użytkownika. Można wybrać opcję Oznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w ADF lub przechowywania haseł w usłudze Azure Key Vault i umożliwić działanie kopiowania ściągnięcia stamtąd podczas wykonywania kopii danych — Dowiedz się więcej o [przechowywania poświadczeń w magazynie kluczy](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. | Brak źródła tak dla obiekt sink |

>[!IMPORTANT]
>Aby skopiować dane do programu Dynamics, jawnie [utworzyć środowiska uruchomieniowego integracji Azure](create-azure-integration-runtime.md#create-azure-ir) z lokalizacją w pobliżu wystąpienia Dynamics. Skojarzyć go w połączonej usłudze jak w poniższym przykładzie.

**Przykład: Dynamics na lokalnym przy użyciu uwierzytelniania IFD IFD**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez zestaw danych Dynamics właściwości.

Aby skopiować dane z i do programu Dynamics, ustaw właściwość Typ zestawu danych do **DynamicsEntity**. Następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwości typu zestawu danych musi mieć ustawioną **DynamicsEntity**. |Yes |
| entityName | Nazwa logiczna obiektu do pobrania. | Brak źródła (Jeśli określono parametr "zapytania" w źródle działania), tak dla obiekt sink |

> [!IMPORTANT]
>- Po skopiowaniu danych z programu Dynamics sekcji "structure" jest wymagany w elemencie dataset Dynamics. Definiuje typ danych kolumny danych Dynamics, który chcesz skopiować. Aby dowiedzieć się więcej, zobacz [struktury zestawu danych](concepts-datasets-linked-services.md#dataset-structure) i [mapowanie typu danych dla programu Dynamics](#data-type-mapping-for-dynamics).
>- Po skopiowaniu danych do programu Dynamics sekcji "structure" jest opcjonalna w zestawie danych Dynamics. Kolumny, które ma zostać skopiowany do zależy od schematu źródła danych. Jeśli źródłem jest pliku CSV bez nagłówka w zestawie danych wejściowych, określ "structure" z typem danych kolumny. Są one wykonywane na pola w pliku CSV jedna po drugiej w porządku.

**Przykład:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez Dynamics źródłowy i odbiorczy typów właściwości.

### <a name="dynamics-as-a-source-type"></a>Dynamics jako typ źródła

Aby skopiować dane z programu Dynamics, należy ustawić typ źródła w przypadku działania kopiowania do **DynamicsSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania **DynamicsSource**. | Yes |
| query | FetchXML jest język kwerendy zastrzeżonych, który jest używany w Dynamics (online i lokalnego). Zobacz poniższy przykład. Aby dowiedzieć się więcej, zobacz [tworzenia zapytań dotyczących FeachXML](https://msdn.microsoft.com/library/gg328332.aspx). | Nie (Jeśli określono parametr "Nazwa" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Przykładowe zapytanie FetchXML

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics jako typ ujścia

Aby skopiować dane do programu Dynamics, należy ustawić typ ujścia w działaniu kopiowania do **DynamicsSink**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **zbiornika** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość typu sink działania kopiowania **DynamicsSink**. | Yes |
| writeBehavior | Zachowanie zapisu operacji.<br/>Dozwolone wartości to **"Upsert"**. | Yes |
| writeBatchSize | Liczba wierszy z danymi zapisywanymi w Dynamics w każdej z partii. | Nie (wartość domyślna to 10) |
| ignoreNullValues | Wskazuje, czy mają zostać zignorowane wartości null w danych wejściowych (z wyjątkiem pól klucza) podczas operacji zapisu.<br/>Dozwolone wartości to **true** i **false**.<br>- **Wartość true,**: Pozostaw bez zmian danych w obiekcie docelowym, po wykonaniu operacji upsert/aktualizacji. Wstaw wartości domyślnej zdefiniowanej po wykonaniu operacji wstawiania.<br/>- **FALSE**: zaktualizować dane w obiekcie docelowym null po wykonaniu operacji upsert/aktualizacji. Wstaw wartość NULL, po wykonaniu operacji wstawiania. | Nie (wartość domyślna to false) |

>[!NOTE]
>Wartość domyślna writeBatchSize odbioru i działanie kopiowania [parallelCopies](copy-activity-performance.md#parallel-copy) dla obiekt sink Dynamics są obie 10. W związku z tym 100 rekordów są przesyłane do programu Dynamics jednocześnie.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Mapowanie do programu Dynamics typu danych

Po skopiowaniu danych z programu Dynamics następujące mapowania są używane z Dynamics typów danych do typów danych tymczasowych fabryki danych. Aby dowiedzieć się, jak aktywność kopiowania mapuje typ schemat i dane źródłowy obiekt sink, zobacz [schemat i dane typu mapowania](copy-activity-schema-and-type-mapping.md).

Skonfiguruj odpowiedni typ danych fabryki danych w strukturze zestawu danych na podstawie Twojego źródła Dynamics — typ danych przy użyciu poniższej tabeli mapowania.

| Dynamics — typ danych | Typ danych tymczasowych fabryki danych | Obsługiwane jako źródło | Obsługiwane jako odbioru |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Długie | ✓ | ✓ |
| AttributeTypeCode.Boolean | Wartość logiczna | ✓ | ✓ |
| AttributeType.DateTime | Data/godzina | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Podwójnej precyzji | ✓ | ✓ |
| AttributeType.EntityName | Ciąg | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Identyfikator GUID | ✓ | |
| AttributeType.ManagedProperty | Wartość logiczna | ✓ | |
| AttributeType.Memo | Ciąg | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Identyfikator GUID | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Identyfikator GUID | ✓ | ✓ |
| AttributeType.String | Ciąg | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |


> [!NOTE]
> Typy danych Dynamics AttributeType.CalendarRules i AttributeType.PartyList nie są obsługiwane.

## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
