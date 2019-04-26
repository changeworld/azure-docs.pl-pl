---
title: Kopiowanie danych z usługi Office 365 przy użyciu usługi Azure Data Factory (wersja zapoznawcza) | Dokumentacja firmy Microsoft
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
ms.date: 01/29/2019
ms.author: jingwang
ms.openlocfilehash: 5d2d5948d817cbe80d00b74ef104ebaffcb511fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405974"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory-preview"></a>Kopiowanie danych z usługi Office 365 na platformie Azure przy użyciu usługi Azure Data Factory (wersja zapoznawcza) 

Usługa Azure Data Factory pozwala przenieść bogate dane organizacji w usługi Office 365 dzierżawy na platformę Azure w sposób skalowalny i kompilowanie aplikacji do analizowania i wyodrębnić szczegółowe informacje, w oparciu o te zasoby cennych danych. Integracja z usługą Privileged Access Management zapewnia kontrolę bezpiecznego dostępu do cennych danych nadzorowanych w usłudze Office 365.  Aby uzyskać więcej informacji dotyczących danych programu Microsoft Graph connect, zapoznaj się [ten link](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki).

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z usługi Office 365. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Teraz wewnątrz działania elementu pojedynczej kopii można jedynie **kopiowanie danych z usługi Office 365 do [usługi Azure Blob Storage](connector-azure-blob-storage.md), [usługi Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), i [usługi Azure Data Lake Storage Gen2 ](connector-azure-data-lake-storage.md) w formacie JSON** (typ setOfObjects). Jeśli chcesz załadować usługi Office 365 do innych typów magazynów danych lub w innych formatach, można połączyć w łańcuch pierwsze działanie kopiowania z działaniem kopiowania kolejnych do dalszego ładowania danych do dowolnego [obsługiwane magazyny docelowego ADF](copy-activity-overview.md#supported-data-stores-and-formats) (zobacz" obsługiwany jako obiekt sink"kolumna w tabeli"Obsługiwane magazyny danych i formatów").

>[!IMPORTANT]
>- Subskrypcja platformy Azure zawierająca fabryki danych i magazynu danych ujścia musi być objęty tą samą dzierżawą usługi Azure Active Directory (Azure AD) jako dzierżawy usługi Office 365.
>- Region platformy Azure Integration Runtime, użyty dla aktywności kopiowania upewnij się, jak również miejsce docelowe jest w tym samym regionie, w którym znajduje się skrzynki pocztowej użytkownika dzierżawy usługi Office 365. Zapoznaj się [tutaj](concepts-integration-runtime.md#integration-runtime-location) Aby zrozumieć, jak lokalizacja IR platformy Azure jest określana. Zapoznaj się [tabeli w tym miejscu](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#data-regions) listę obsługiwanych regionów pakietu Office i odpowiednie regiony platformy Azure.
>-  Ładowania danych usługi Office 365 do **usługi Azure Blob Storage** jako miejsce docelowe, upewnij się, którego używasz **[uwierzytelnianie jednostki usługi](connector-azure-blob-storage.md#service-principal-authentication)** podczas definiowania połączonej Usługi Azure Blob Storage, a nie przy użyciu [klucz konta](connector-azure-blob-storage.md#account-key-authentication), [sygnatury dostępu współdzielonego](connector-azure-blob-storage.md#shared-access-signature-authentication) lub [zarządzanych tożsamości dla zasobów platformy Azure](connector-azure-blob-storage.md#managed-identity) uwierzytelnienia.
>-  Ładowania danych usługi Office 365 do **usługi Azure Data Lake Storage Gen1** jako miejsce docelowe, upewnij się, którego używasz [ **uwierzytelnianie jednostki usługi** ](connector-azure-data-lake-store.md#use-service-principal-authentication) podczas definiowania Połączona usługa Azure Data Lake Storage Gen1 i nie używa [zarządzanych tożsamości do uwierzytelniania zasobów platformy Azure](connector-azure-data-lake-store.md#managed-identity).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skopiować dane z usługi Office 365 na platformie Azure, musisz wykonać następujące kroki wymagań wstępnych:

- Administrator dzierżawy usługi Office 365 należy wykonać akcje na pokład, zgodnie z opisem [tutaj](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/On-boarding).
- Utwórz i skonfiguruj aplikację sieci web usługi Azure AD w usłudze Azure Active Directory.  Aby uzyskać instrukcje, zobacz [Utwórz aplikację usługi Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Zanotuj następujące wartości, które będą używane do definiowania połączonej usługi dla usługi Office 365:
    - Identyfikator dzierżawy. Aby uzyskać instrukcje, zobacz [uzyskanie Identyfikatora dzierżawy](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).
    - Klucz Identyfikatora aplikacji i aplikacji.  Aby uzyskać instrukcje, zobacz [Get aplikacji Identyfikatora i klucza uwierzytelniania](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key).
- Dodaj tożsamość użytkownika, który powoduje ustawienie żądania dostępu do danych jako właściciel aplikacji sieci web usługi Azure AD (aplikacji internetowej z usługi Azure AD > Ustawienia > właścicieli > Dodaj właściciela). 
    - Tożsamość użytkownika musi być w organizacji usługi Office 365 są pobieranie danych z i nie może być użytkownikiem-gościem.

## <a name="approving-new-data-access-requests"></a>Zatwierdzanie żądań dostępu do nowych danych

Jeśli po raz pierwszy są wnioskujące o udostępnienie danych dla tego kontekstu (kombinację danych, które tabeli jest dostęp, lokalizację docelową konta znajdują się dane są ładowane i tożsamości użytkownika, który osiągnął dane żądanie dostępu), zostanie wyświetlony działania kopiowania stan jako "W toku", a tylko wtedy, gdy klikniesz w ["Szczegóły" link w kolumnie Akcje](copy-activity-overview.md#monitoring) będzie widoczny stan jako "RequestingConsent".  Członek grupy osoba zatwierdzająca dostęp do danych musi zatwierdzić żądanie w zarządzania dostępem uprzywilejowanym, wyodrębnianie danych mógł kontynuować działanie.

Zapoznaj się [tutaj](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Approving-data-access-requests) na jak osoba zatwierdzająca mogą zatwierdzać dostęp do żądania danych, a można znaleźć [tutaj](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#integration-with-privileged-access-management) objaśnienia na ogólną integrację z usługa Privileged Access Management, w tym sposób konfigurowania danych Grupa osoba zatwierdzająca dostęp.

## <a name="policy-validation"></a>Sprawdzanie poprawności zasad

Jeśli przypisań zasad platformy Azure są dokonywane na zasoby w grupie zasobów zarządzania usługi ADF jest tworzony w ramach zarządzaną aplikację, następnie dla każdego uruchomienia, działania kopiowania usługi ADF sprawdzi, upewnij się, że przypisania zasad są wymuszane. Zapoznaj się [tutaj](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#policies) Aby uzyskać listę obsługiwanych zasadach.

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
| type | Właściwość type musi być równa: **Office365** | Yes |
| office365TenantId | Identyfikator dzierżawy usługi Azure, do której należy konto usługi Office 365. | Yes |
| servicePrincipalTenantId | Określ informacje o dzierżawy, pod którą znajduje się aplikacja sieci web usługi Azure AD. | Yes |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Yes |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory. | Yes |
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
| type | Właściwość typu elementu dataset musi być równa: **Office365Table** | Yes |
| tableName | Nazwa zestawu danych w celu wyodrębnienia z usługi Office 365. Zapoznaj się [tutaj](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#datasets) listę zestawów danych w usłudze Office 365 dostępne do wyodrębnienia. | Yes |
| Predykat | Wyrażenie predykatu, który może służyć do filtrowania określonych wierszy w celu wyodrębnienia z usługi Office 365.  Zapoznaj się [tutaj](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#filters) Aby dowiedzieć się, kolumny, które mogą służyć do filtrowania przewidywania dla każdej tabeli i format wyrażenia filtru. | Nie<br>(Jeśli nie dostarczono żadnych predykat, wartość domyślna to do wyodrębniania danych w ciągu ostatnich 30 dni) |

**Przykład**

```json
{
    "name": "Office365Table1",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "ReceivedDateTime",
                "type": "datetime"
            },
            {
                "name": "SentDateTime",
                "type": "datetime"
            },
            {
                "name": "HasAttachments",
                "type": "boolean"
            },
            {
                "name": "InternetMessageId",
                "type": "string"
            },
            {
                "name": "Subject",
                "type": "string"
            },
            {
                "name": "Importance",
                "type": "string"
            },
            {
                "name": "ParentFolderId",
                "type": "string"
            },
            {
                "name": "Sender",
                "type": "string"
            },
            {
                "name": "From",
                "type": "string"
            },
            {
                "name": "ToRecipients",
                "type": "string"
            },
            {
                "name": "CcRecipients",
                "type": "string"
            },
            {
                "name": "BccRecipients",
                "type": "string"
            },
            {
                "name": "ReplyTo",
                "type": "string"
            },
            {
                "name": "ConversationId",
                "type": "string"
            },
            {
                "name": "UniqueBody",
                "type": "string"
            },
            {
                "name": "IsDeliveryReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsReadReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsRead",
                "type": "boolean"
            },
            {
                "name": "IsDraft",
                "type": "boolean"
            },
            {
                "name": "WebLink",
                "type": "string"
            },
            {
                "name": "CreatedDateTime",
                "type": "datetime"
            },
            {
                "name": "LastModifiedDateTime",
                "type": "datetime"
            },
            {
                "name": "ChangeKey",
                "type": "string"
            },
            {
                "name": "Categories",
                "type": "string"
            },
            {
                "name": "Id",
                "type": "string"
            },
            {
                "name": "Attachments",
                "type": "string"
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Contact_v0",
            "predicate": "CreatedDateTime >= 2018-07-11T16:00:00.000Z AND CreatedDateTime <= 2018-07-18T16:00:00.000Z"
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
