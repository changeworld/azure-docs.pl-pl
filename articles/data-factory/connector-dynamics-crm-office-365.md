---
title: Kopiowanie danych z i do programu Dynamics CRM lub Dynamics 365 (Common Data Service) za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z programu Microsoft Dynamics CRM lub Microsoft Dynamics 365 (usługa Common Data Service) do obsługiwanych magazynów danych ujścia lub z obsługiwanymi magazynami danych źródłowych do programu Dynamics CRM lub Dynamics 365, za pomocą działania kopiowania w potoku usługi fabryka danych.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: jingwang
ms.openlocfilehash: 3f7bf3ce8c01e82fa69b3b041b573b4b31a719d2
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514090"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Kopiowanie danych z i Dynamics 365 (Common Data Service) lub programu Dynamics CRM przy użyciu usługi Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do firmy Microsoft Dynamics 365 lub Microsoft Dynamics CRM. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z Dynamics 365 (Common Data Service) lub programu Dynamics CRM, do dowolnego obsługiwanego magazynu danych ujścia. Możesz także skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych Dynamics 365 (Common Data Service) lub programu Dynamics CRM. Aby uzyskać listę magazynów danych obsługiwanych jako źródła lub ujścia działania kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

Ten łącznik Dynamics obsługuje Dynamics w wersji 7.x 9.x zarówno w trybie online lub lokalnie. W szczególności

- W wersji 7.x mapy do programu Dynamics CRM 2015
- Wersja 8.x map, Dynamics CRM 2016 i wcześniejszą wersję programu Dynamics 365
- Wersja 9.x mapuje do nowszej wersji programu Dynamics 365

Zapoznaj się z poniższą tabelą na typy uwierzytelniania obsługiwane i konfiguracje dla odpowiednich Dynamics wersje produktów. (IFD jest mała w przypadku wdrożenia połączone z Internetem).

| Wersje systemu Dynamics | Typy uwierzytelniania | Przykłady usługi połączonej |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM Online | Office365 | [Dynamics online i uwierzytelnianie usługi Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 w środowisku lokalnym za pomocą IFD <br> Dynamics CRM 2016 lokalnie przy użyciu IFD <br> Dynamics CRM 2015 lokalnie przy użyciu IFD | IFD | [Dynamics w środowisku lokalnym za pomocą IFD + IFD uwierzytelniania](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Dynamics 365 w szczególności następujące typy aplikacji są obsługiwane:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Pozostałe typy aplikacji np. Finanse i operacje, Talent, itp., nie są obsługiwane przez ten łącznik.

Ten łącznik Dynamics jest wbudowana w górnej części [narzędzi Dynamics XRM](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Aby skopiować dane z **Dynamics 365 Finance and Operations**, możesz użyć [łącznika Dynamics AX](connector-dynamics-ax.md).

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do usługi Dynamics.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku połączonej usługi Dynamics.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 i Dynamics CRM Online

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **Dynamics**. | Yes |
| deploymentType | Typ wdrażania wystąpienia programu Dynamics. Musi to być **"Online"** systemu Microsoft Dynamics online. | Yes |
| serviceUri | Np. adres URL usługi Dynamics, Twoje wystąpienie `https://adfdynamics.crm.dynamics.com`. | Yes |
| authenticationType | Typ uwierzytelniania, aby połączyć się z serwerem Dynamics. Określ **"Usługi Office 365"** systemu Microsoft Dynamics online. | Yes |
| username | Określ nazwę użytkownika, aby nawiązać połączenie Dynamics. | Yes |
| password | Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. | Nie dla źródła, tak ujścia Jeśli źródłem jest połączona usługa nie ma środowiska integration runtime |

>[!NOTE]
>Łącznik Dynamics, używany na potrzeby opcjonalne "nazwa_organizacji" właściwość identyfikuje Twoje wystąpienie usługi Dynamics CRM/365 Online. Gdy go nadal działa, są zalecane do określ nową właściwość "serviceUri" zamiast tego w celu uzyskania lepszej wydajności odnajdywania dla wystąpienia.

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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 i Dynamics CRM w środowisku lokalnym za pomocą IFD

*Dodatkowe właściwości, które porównania Dynamics online są "hostName" i "port".*

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **Dynamics**. | Yes |
| deploymentType | Typ wdrażania wystąpienia programu Dynamics. Musi to być **"OnPremisesWithIfd"** Dynamics i lokalnych przy użyciu IFD.| Yes |
| hostName | Nazwa hosta na lokalnym serwerze Dynamics. | Yes |
| port | Port lokalny serwer Dynamics. | Nie, port domyślny to 443 |
| organizationName | Nazwa organizacji wystąpienia programu Dynamics. | Yes |
| authenticationType | Typ uwierzytelniania, aby nawiązać połączenie z serwerem Dynamics. Określ **"Ifd"** Dynamics i lokalnych przy użyciu IFD. | Yes |
| username | Określ nazwę użytkownika, aby nawiązać połączenie Dynamics. | Yes |
| password | Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. Można wybrać opcję Oznacz to pole jako SecureString bezpiecznie przechowywać w usłudze ADF lub przechowywać haseł w usłudze Azure Key Vault i umożliwić działanie kopiowania pobierania w tym miejscu podczas kopiowania danych — Dowiedz się więcej z [Store poświadczeń w usłudze Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. | Brak źródła tak dla ujścia |

**Przykład: Dynamics lokalnie przy użyciu IFD przy użyciu uwierzytelniania IFD**

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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Dynamics.

Aby skopiować dane z i do usługi Dynamics, należy ustawić właściwość typu zestawu danych na **DynamicsEntity**. Następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa **DynamicsEntity**. |Yes |
| entityName | Nazwa logicznej jednostki do pobrania. | Nie dla źródła (Jeśli określono parametr "zapytanie" w źródle działania) tak dla ujścia |

> [!IMPORTANT]
>- Podczas kopiowania danych z systemu Dynamics, w sekcji "strukturę" jest opcjonalne, ale zdecydowanie recommanded w zestawie danych Dynamics, aby upewnić się, wynik deterministyczne kopiowania. Definiuje typ danych kolumny danych Dynamics, który chcesz skopiować. Aby dowiedzieć się więcej, zobacz [struktury zestawu danych](concepts-datasets-linked-services.md#dataset-structure-or-schema) i [mapowanie typu danych dla usługi Dynamics](#data-type-mapping-for-dynamics).
>- Podczas importowania schematu w tworzeniu interfejsu użytkownika, ADF wnioskowanie schematu przez próbkowanie pierwszych wierszy z wyników kwerendy Dynamics zainicjować konstrukcji struktury, pominąć przypadków kolumn bez wartości. Takie samo zachowanie ma zastosowanie do skopiowania wykonania, jeśli brak definicji struktury jawnej. Możesz przejrzeć i dodać większą liczbę kolumn w Dynamics zestawu danych schematu/strukturę zgodnie z potrzebami, które będą honorowane w czasie wykonywania kopii.
>- Podczas kopiowania danych do usługi Dynamics, w sekcji "strukturę" jest opcjonalna w zestawie danych Dynamics. Kolumny do skopiowania do są określane przez schemat danych źródłowych. Jeśli źródłem jest plik CSV, bez nagłówka w wejściowego zestawu danych, należy określić "strukturę" z typem danych kolumny. Mapują do pól w pliku CSV pojedynczo, w kolejności.

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

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez typów Dynamics źródła i ujścia.

### <a name="dynamics-as-a-source-type"></a>Dynamics jako typ źródła

Aby skopiować dane z systemu Dynamics, należy ustawić typ źródłowego w działaniu kopiowania, aby **DynamicsSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być równa **DynamicsSource**. | Yes |
| query | Narzędzia FetchXML jest język zapytania własności, który jest używany w usłudze Dynamics (online i lokalnego). Zobacz poniższy przykład. Aby dowiedzieć się więcej, zobacz [tworzenie zapytań przy użyciu narzędzia FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | Nie (Jeśli określono parametr "entityName" w zestawie danych) |

>[!NOTE]
>Kolumna klucza podstawowego zawsze zostaną skopiowane się, nawet jeśli projekcji kolumny, skonfigurowanych w zapytanie FetchXML nie zawiera on.

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

Aby skopiować dane do usługi Dynamics, należy ustawić typ ujścia w działaniu kopiowania, aby **DynamicsSink**. Następujące właściwości są obsługiwane w działaniu kopiowania **ujścia** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type ujścia działania kopiowania musi być równa **DynamicsSink**. | Yes |
| writeBehavior | Zachowanie zapisu operacji.<br/>Dozwolona wartość to **"Upsert"** . | Yes |
| writeBatchSize | Liczba wierszy danych zapisanych w każdej z partii Dynamics. | Nie (wartość domyślna to 10) |
| ignoreNullValues | Wskazuje, czy ignorować wartości null w danych wejściowych (z wyjątkiem pól klucza) podczas operacji zapisu.<br/>Dozwolone wartości to **true** i **false**.<br>- **True** : Pozostawiają dane w obiekcie docelowym niezmieniony po wykonaniu operacji upsert/aktualizacji. Po wykonaniu operacji wstawiania, należy wstawić zdefiniowana wartość domyślna.<br/>- **False**: Zaktualizować dane w obiekcie docelowym na wartość NULL, podczas wykonywania operacji upsert/aktualizacji. Po wykonaniu operacji wstawiania, należy wstawić wartość NULL. | Nie (wartość domyślna to false) |

>[!NOTE]
>Wartość domyślna obiektu sink "**writeBatchSize**"i działanie kopiowania" **[parallelCopies](copy-activity-performance.md#parallel-copy)** " ujścia Dynamics są oba 10. W związku z tym 100 rekordów są przesyłane do usługi Dynamics jednocześnie.

For Dynamics 365 online, obowiązuje limit [2 wywołań współbieżnych usługi batch w ramach jednej organizacji](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Przekroczeniu tego limitu błędu "Serwer jest zajęty" jest generowany, zanim pierwsze żądanie nigdy nie zostanie wykonany. Utrzymywanie "writeBatchSize" mniejsza lub równa 10 pozwoliłoby uniknąć takich ograniczania równoczesnych wywołań.

Optymalną kombinację możliwości "**writeBatchSize**"i"**parallelCopies**" jest zależny od schematu jednostki np. liczba kolumn, rozmiar wiersza, liczba wtyczek/przepływy pracy/działań przepływu pracy podłączyłeś do tych wywołań itd. Domyślne ustawienie 10 writeBatchSize * 10 parallelCopies to zalecenie, zgodnie z usługi Dynamics, która będzie działać w przypadku większości obiektów Dynamics jednak może nie być najlepszą wydajność. Możesz określić wydajność, dostosowując kombinacji w ustawieniach działania kopiowania.

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

## <a name="data-type-mapping-for-dynamics"></a>Typ danych mapowania dla usługi Dynamics

Podczas kopiowania danych z systemu Dynamics, następujące mapowania są używane z typów danych Dynamics na typy danych tymczasowych fabryki danych. Aby dowiedzieć się, jak działania kopiowania mapuje typ schematu i danych źródła do ujścia, zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md).

Skonfiguruj odpowiedni typ danych Data Factory w strukturze zestawu danych, na podstawie źródła Dynamics — typ danych przy użyciu poniższej tabeli mapowania.

| Typ danych Dynamics | Typ danych tymczasowych fabryki danych | Obsługiwane jako źródło | Obsługiwany jako ujście |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Długie | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolean | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓ (ze skojarzonego pojedynczy element docelowy) |
| AttributeType.ManagedProperty | Boolean | ✓ | |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Typy danych Dynamics AttributeType.CalendarRules, AttributeType.MultiSelectPicklist i AttributeType.PartyList nie są obsługiwane.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
