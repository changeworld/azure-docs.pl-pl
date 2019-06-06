---
title: Kopiowanie danych z usługi Office 365 przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z usługi Office 365 do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: jingwang
ms.openlocfilehash: 1a8d622aa280794d9a4d6fe7320ddcc21ac044f4
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475652"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Kopiowanie danych z usługi Office 365 na platformie Azure przy użyciu usługi Azure Data Factory

Usługa Azure Data Factory integruje się z [łączenie danych programu Microsoft Graph](https://docs.microsoft.com/graph/data-connect-concept-overview), co pozwala na używanie bogate dane organizacji w usługi Office 365 dzierżawy na platformę Azure w sposób skalowalny i tworzenia aplikacji do analizowania i wyciągać wnioski na podstawie te zasoby cennych danych. Integracja z usługą Privileged Access Management zapewnia kontrolę bezpiecznego dostępu do cennych danych nadzorowanych w usłudze Office 365.  Zapoznaj się [ten link](https://docs.microsoft.com/graph/data-connect-concept-overview) omówienie danych programu Microsoft Graph łączenie i odnoszą się do [ten link](https://docs.microsoft.com/graph/data-connect-policies#licensing) informacje na temat licencjonowania.

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z usługi Office 365. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje
Łącznik usługi Office 365 dla usługi ADF i danych programu Microsoft Graph Łączenie pozwala na skalowanie pozyskiwanie pomiarów dotyczących różnych typów zestawów danych z skrzynek poczty E-mail programu Exchange, włączone kontaktów z książki adresowej, zdarzeniami kalendarza, wiadomości e-mail, informacje o użytkowniku, ustawienia skrzynki pocztowej i itd.  Zapoznaj się [tutaj](https://docs.microsoft.com/graph/data-connect-datasets) aby zobaczyć pełną listę dostępnych zestawów danych.

Teraz wewnątrz działania elementu pojedynczej kopii można jedynie **kopiowanie danych z usługi Office 365 do [usługi Azure Blob Storage](connector-azure-blob-storage.md), [usługi Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), i [usługi Azure Data Lake Storage Gen2 ](connector-azure-data-lake-storage.md) w formacie JSON** (typ setOfObjects). Jeśli chcesz załadować usługi Office 365 do innych typów magazynów danych lub w innych formatach, można połączyć w łańcuch pierwsze działanie kopiowania z działaniem kopiowania kolejnych do dalszego ładowania danych do dowolnego [obsługiwane magazyny docelowego ADF](copy-activity-overview.md#supported-data-stores-and-formats) (zobacz" obsługiwany jako obiekt sink"kolumna w tabeli"Obsługiwane magazyny danych i formatów").

>[!IMPORTANT]
>- Subskrypcja platformy Azure zawierająca fabryki danych i magazynu danych ujścia musi być objęty tą samą dzierżawą usługi Azure Active Directory (Azure AD) jako dzierżawy usługi Office 365.
>- Region platformy Azure Integration Runtime, użyty dla aktywności kopiowania upewnij się, jak również miejsce docelowe jest w tym samym regionie, w którym znajduje się skrzynki pocztowej użytkownika dzierżawy usługi Office 365. Zapoznaj się [tutaj](concepts-integration-runtime.md#integration-runtime-location) Aby zrozumieć, jak lokalizacja IR platformy Azure jest określana. Zapoznaj się [tabeli w tym miejscu](https://docs.microsoft.com/graph/data-connect-datasets#regions) listę obsługiwanych regionów pakietu Office i odpowiednie regiony platformy Azure.
>- Uwierzytelnianie jednostki usługi jest tylko mechanizm uwierzytelniania obsługiwany dla usługi Azure Blob Storage, Azure Data Lake Storage Gen1 i Azure Data Lake Storage Gen2 jako docelowy.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skopiować dane z usługi Office 365 na platformie Azure, musisz wykonać następujące kroki wymagań wstępnych:

- Administrator dzierżawy usługi Office 365 należy wykonać akcje na pokład, zgodnie z opisem [tutaj](https://docs.microsoft.com/graph/data-connect-get-started).
- Utwórz i skonfiguruj aplikację sieci web usługi Azure AD w usłudze Azure Active Directory.  Aby uzyskać instrukcje, zobacz [Utwórz aplikację usługi Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Zanotuj następujące wartości, które będą używane do definiowania połączonej usługi dla usługi Office 365:
    - Identyfikator dzierżawy. Aby uzyskać instrukcje, zobacz [uzyskanie Identyfikatora dzierżawy](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
    - Klucz Identyfikatora aplikacji i aplikacji.  Aby uzyskać instrukcje, zobacz [Get aplikacji Identyfikatora i klucza uwierzytelniania](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
- Dodaj tożsamość użytkownika, który powoduje ustawienie żądania dostępu do danych jako właściciel aplikacji sieci web usługi Azure AD (aplikacji internetowej z usługi Azure AD > Ustawienia > właścicieli > Dodaj właściciela). 
    - Tożsamość użytkownika musi być w organizacji usługi Office 365 są pobieranie danych z i nie może być użytkownikiem-gościem.

## <a name="approving-new-data-access-requests"></a>Zatwierdzanie żądań dostępu do nowych danych

Jeśli po raz pierwszy są wnioskujące o udostępnienie danych dla tego kontekstu (kombinację danych, które tabeli jest dostęp, lokalizację docelową konta znajdują się dane są ładowane i tożsamości użytkownika, który osiągnął dane żądanie dostępu), zostanie wyświetlony działania kopiowania stan jako "W toku", a tylko wtedy, gdy klikniesz w ["Szczegóły" link w kolumnie Akcje](copy-activity-overview.md#monitoring) będzie widoczny stan jako "RequestingConsent".  Członek grupy osoba zatwierdzająca dostęp do danych musi zatwierdzić żądanie w zarządzania dostępem uprzywilejowanym, wyodrębnianie danych mógł kontynuować działanie.

Zapoznaj się [tutaj](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) na jak osoba zatwierdzająca mogą zatwierdzać dostęp do żądania danych, a można znaleźć [tutaj](https://docs.microsoft.com/graph/data-connect-pam) objaśnienia na ogólną integrację z usługa Privileged Access Management, w tym sposób konfigurowania danych Grupa osoba zatwierdzająca dostęp.

## <a name="policy-validation"></a>Sprawdzanie poprawności zasad

Jeśli przypisań zasad platformy Azure są dokonywane na zasoby w grupie zasobów zarządzania usługi ADF jest tworzony w ramach zarządzaną aplikację, następnie dla każdego uruchomienia, działania kopiowania usługi ADF sprawdzi, upewnij się, że przypisania zasad są wymuszane. Zapoznaj się [tutaj](https://docs.microsoft.com/graph/data-connect-policies#policies) Aby uzyskać listę obsługiwanych zasadach.

## <a name="getting-started"></a>Wprowadzenie

>[!TIP]
>Aby zapoznać się z przewodnikiem za pomocą łącznika usługi Office 365, zobacz [ładowanie danych z usługi Office 365](load-office-365-data.md) artykułu.

Aby utworzyć potok za pomocą działania kopiowania, przy użyciu jednej z następujących narzędzi lub zestawów SDK. Wybierz łącze, aby przejść do samouczka krok po kroku instrukcje tworzenia potoku za pomocą działania kopiowania. 

- [Azure Portal](quickstart-create-data-factory-portal.md)
- [Zestaw SDK platformy .NET](quickstart-create-data-factory-dot-net.md)
- [Zestaw SDK dla języka Python](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [Interfejs API REST](quickstart-create-data-factory-rest-api.md)
- [Szablon usługi Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md). 

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika usługi Office 365.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla usługi Office 365, połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **Office365** | Tak |
| office365TenantId | Identyfikator dzierżawy usługi Azure, do której należy konto usługi Office 365. | Yes |
| servicePrincipalTenantId | Określ informacje o dzierżawy, pod którą znajduje się aplikacja sieci web usługi Azure AD. | Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Yes |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory. | Tak |
| connectVia | Integration Runtime, który ma być używany do łączenia się z magazynem danych.  Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. | Nie |

>[!NOTE]
> Różnica między **office365TenantId** i **servicePrincipalTenantId** i odpowiadająca wartość w celu zapewnienia:
>- Jeśli Deweloper w przedsiębiorstwie tworzenia aplikacji, danych usługi Office 365 za użycie własnej organizacji, a następnie należy podać tej samej dzierżawie identyfikator obie te właściwości, która jest dzierżawą usługi AAD w Twojej organizacji identyfikatora.
>- Jeśli jesteś niezależnym dostawcą oprogramowania dla deweloperów tworzenia aplikacji dla swoich klientów, office365TenantId będą klienta (Instalator aplikacji) identyfikator dzierżawy usługi AAD i servicePrincipalTenantId będą stosowane w firmie usługi AAD z identyfikatorem dzierżawy.

**Przykład:**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych usługi Office 365.

Aby skopiować dane z usługi Office 365, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **Office365Table** | Tak |
| tableName | Nazwa zestawu danych w celu wyodrębnienia z usługi Office 365. Zapoznaj się [tutaj](https://docs.microsoft.com/graph/data-connect-datasets#datasets) listę zestawów danych w usłudze Office 365 dostępne do wyodrębnienia. | Tak |
| allowedGroups | Predykat wyboru grupy.  Użyj tej właściwości, aby wybrać maksymalnie 10 grup użytkowników, dla których będzie można odzyskać danych.  Jeśli nie określono żadnych grup, dane zostaną zwrócone dla całej organizacji. | Nie |
| userScopeFilterUri | Gdy `allowedGroups` właściwość nie zostanie określona, można użyć predykatu wyrażenie, które są stosowane w całej dzierżawie, aby filtrować określonych wierszy w celu wyodrębnienia z usługi Office 365. Format predykatu powinny odpowiadać format kwerendy interfejsów API programu Microsoft Graph, np. `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`. | Nie |
| dateFilterColumn | Nazwa kolumny Filtr daty/godziny. Aby ograniczyć zakres czasu, dla których usługi Office 365 dane są wyodrębniane, należy użyć tej właściwości. | Tak, jeśli zestaw danych zawiera co najmniej jedną kolumnę daty/godziny. Zapoznaj się [tutaj](https://docs.microsoft.com/graph/data-connect-filtering#filtering) listę zestawów danych, które wymagają ten filtr daty/godziny. |
| startTime | Rozpocznij wartość daty/godziny do filtrowania. | Tak, jeśli `dateFilterColumn` określono |
| endTime | Końcowa wartość daty/godziny do filtrowania. | Tak, jeśli `dateFilterColumn` określono |

**Przykład**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "Id",
                "type": "String",
                "description": "The unique identifier of the event."
            },
            {
                "name": "CreatedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was created."
            },
            {
                "name": "LastModifiedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was last modified."
            },
            {
                "name": "ChangeKey",
                "type": "String",
                "description": "Identifies the version of the event object. Every time the event is changed, ChangeKey changes as well. This allows Exchange to apply changes to the correct version of the object."
            },
            {
                "name": "Categories",
                "type": "String",
                "description": "The categories associated with the event. Format: ARRAY<STRING>"
            },
            {
                "name": "OriginalStartTimeZone",
                "type": "String",
                "description": "The start time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "OriginalEndTimeZone",
                "type": "String",
                "description": "The end time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "ResponseStatus",
                "type": "String",
                "description": "Indicates the type of response sent in response to an event message. Format: STRUCT<Response: STRING, Time: STRING>"
            },
            {
                "name": "iCalUId",
                "type": "String",
                "description": "A unique identifier that is shared by all instances of an event across different calendars."
            },
            {
                "name": "ReminderMinutesBeforeStart",
                "type": "Int32",
                "description": "The number of minutes before the event start time that the reminder alert occurs."
            },
            {
                "name": "IsReminderOn",
                "type": "Boolean",
                "description": "Set to true if an alert is set to remind the user of the event."
            },
            {
                "name": "HasAttachments",
                "type": "Boolean",
                "description": "Set to true if the event has attachments."
            },
            {
                "name": "Subject",
                "type": "String",
                "description": "The text of the event's subject line."
            },
            {
                "name": "Body",
                "type": "String",
                "description": "The body of the message associated with the event.Format: STRUCT<ContentType: STRING, Content: STRING>"
            },
            {
                "name": "Importance",
                "type": "String",
                "description": "The importance of the event: Low, Normal, High."
            },
            {
                "name": "Sensitivity",
                "type": "String",
                "description": "Indicates the level of privacy for the event: Normal, Personal, Private, Confidential."
            },
            {
                "name": "Start",
                "type": "String",
                "description": "The start time of the event. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "End",
                "type": "String",
                "description": "The date and time that the event ends. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "Location",
                "type": "String",
                "description": "Location information of the event. Format: STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>>"
            },
            {
                "name": "IsAllDay",
                "type": "Boolean",
                "description": "Set to true if the event lasts all day. Adjusting this property requires adjusting the Start and End properties of the event as well."
            },
            {
                "name": "IsCancelled",
                "type": "Boolean",
                "description": "Set to true if the event has been canceled."
            },
            {
                "name": "IsOrganizer",
                "type": "Boolean",
                "description": "Set to true if the message sender is also the organizer."
            },
            {
                "name": "Recurrence",
                "type": "String",
                "description": "The recurrence pattern for the event. Format: STRUCT<Pattern: STRUCT<Type: STRING, `Interval`: INT, Month: INT, DayOfMonth: INT, DaysOfWeek: ARRAY<STRING>, FirstDayOfWeek: STRING, Index: STRING>, `Range`: STRUCT<Type: STRING, StartDate: STRING, EndDate: STRING, RecurrenceTimeZone: STRING, NumberOfOccurrences: INT>>"
            },
            {
                "name": "ResponseRequested",
                "type": "Boolean",
                "description": "Set to true if the sender would like a response when the event is accepted or declined."
            },
            {
                "name": "ShowAs",
                "type": "String",
                "description": "The status to show: Free, Tentative, Busy, Oof, WorkingElsewhere, Unknown."
            },
            {
                "name": "Type",
                "type": "String",
                "description": "The event type: SingleInstance, Occurrence, Exception, SeriesMaster."
            },
            {
                "name": "Attendees",
                "type": "String",
                "description": "The collection of attendees for the event. Format: ARRAY<STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>, Status: STRUCT<Response: STRING, Time: STRING>, Type: STRING>>"
            },
            {
                "name": "Organizer",
                "type": "String",
                "description": "The organizer of the event. Format: STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>>"
            },
            {
                "name": "WebLink",
                "type": "String",
                "description": "The URL to open the event in Outlook Web App."
            },
            {
                "name": "Attachments",
                "type": "String",
                "description": "The FileAttachment and ItemAttachment attachments for the message. Navigation property. Format: ARRAY<STRUCT<LastModifiedDateTime: STRING, Name: STRING, ContentType: STRING, Size: INT, IsInline: BOOLEAN, Id: STRING>>"
            },
            {
                "name": "BodyPreview",
                "type": "String",
                "description": "The preview of the message associated with the event. It is in text format."
            },
            {
                "name": "Locations",
                "type": "String",
                "description": "The locations where the event is held or attended from. The location and locations properties always correspond with each other. Format:  ARRAY<STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>, LocationEmailAddress: STRING, LocationUri: STRING, LocationType: STRING, UniqueId: STRING, UniqueIdType: STRING>>"
            },
            {
                "name": "OnlineMeetingUrl",
                "type": "String",
                "description": "A URL for an online meeting. The property is set only when an organizer specifies an event as an online meeting such as a Skype meeting"
            },
            {
                "name": "OriginalStart",
                "type": "DateTime",
                "description": "The start time that was set when the event was created in UTC time."
            },
            {
                "name": "SeriesMasterId",
                "type": "String",
                "description": "The ID for the recurring series master item, if this event is part of a recurring series."
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1",
            "dateFilterColumn": "CreatedDateTime",
            "startTime": "2019-04-28T16:00:00.000Z",
            "endTime": "2019-05-05T16:00:00.000Z",
            "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło usługi Office 365.

### <a name="office-365-as-source"></a>Usługi Office 365 jako źródło

Aby skopiować dane z usługi Office 365, należy ustawić typ źródłowego w działaniu kopiowania, aby **Office365Source**. Żadne dodatkowe właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji.

**Przykład:**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
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
                "type": "Office365Source"
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
