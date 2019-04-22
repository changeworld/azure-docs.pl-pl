---
title: Tworzenie i Zarządzanie przypisaniami ról w reprezentacji urządzeń cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Tworzenie i Zarządzanie przypisaniami ról w reprezentacji urządzeń cyfrowych platformy Azure.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: lyrana
ms.custom: seodec18
ms.openlocfilehash: 72155799971760e9ddc93746dceafb1ea554d88b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58905311"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Tworzenie i Zarządzanie przypisaniami ról w reprezentacji urządzeń cyfrowych platformy Azure

Azure Twins cyfrowego używa kontroli dostępu opartej na rolach ([RBAC](./security-role-based-access-control.md)) do zarządzania dostępem do zasobów.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="role-assignments-overview"></a>Omówienie przypisania roli

Każdy przypisania roli są zgodne z następującą definicję:

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

W poniższej tabeli opisano każdy atrybut:

| Atrybut | Name (Nazwa) | Wymagane | Typ | Opis |
| --- | --- | --- | --- | --- |
| roleId | Identyfikator definicji roli | Yes | String | Unikatowy identyfikator przypisania roli żądaną. Aby znaleźć definicji ról i ich identyfikatora, wykonywania zapytań interfejsu API systemu lub przeglądania w poniższej tabeli. |
| Identyfikator obiektu | Identyfikator obiektu | Yes | String | Identyfikator usługi Azure Active Directory, identyfikator obiektu nazwy głównej usługi lub nazwy domeny. Co lub których przypisanie roli jest przypisany do. Przypisanie roli musi być sformatowany zgodnie z jego skojarzonego typu. Aby uzyskać `DomainName` objectIdType, identyfikator obiektu musi zaczynać się od `“@”` znaków. |
| objectIdType | Typ identyfikatora obiektu | Yes | String | Typ identyfikatora obiektu używany. Zobacz **obsługiwane ObjectIdTypes** poniżej. |
| ścieżka | Ścieżka miejsca | Yes | String | Ścieżka pełny dostęp do `Space` obiektu. Może to być na przykład `/{Guid}/{Guid}`. Jeśli identyfikator wymaga przypisania roli dla całego wykresu, należy określić `"/"`. Ten znak wyznacza katalogu głównego, ale nie zaleca się jej użycie. Zawsze postępuj zgodnie z zasadą najniższych uprawnień. |
| tenantId | Identyfikator dzierżawy | Różna | String | W większości przypadków identyfikator dzierżawy usługi Azure Active Directory. Niedozwolone dla `DeviceId` i `TenantId` ObjectIdTypes. Wymagane dla `UserId` i `ServicePrincipalId` ObjectIdTypes. Opcjonalnie na potrzeby ObjectIdType nazwa_domeny. |

### <a name="supported-role-definition-identifiers"></a>Identyfikatorów definicji roli obsługiwanej

Każdego przypisania roli kojarzy definicję roli z jednostką w danym środowisku Twins cyfrowych platformy Azure.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Typy identyfikatorów obsługiwany obiekt

Wcześniej **objectIdType** atrybut został wprowadzony.

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Operacji przypisania roli

Twins cyfrowych platformy Azure obsługuje pełne *Utwórz*, *odczytu*, i *Usuń* operacji przypisania ról. *Aktualizacja* operacje są obsługiwane przez dodawanie przypisania roli, usuwanie przypisań ról lub modyfikowanie [przestrzenne wykres analizy](./concepts-objectmodel-spatialgraph.md) węzły, które zapewniają przypisania roli dostęp do.

![Punkty końcowe przypisania roli][1]

Podany dokumentację referencyjną programu Swagger dalsze zawiera informacje o wszystkich dostępnych interfejsu API punktów końcowych, operacje dotyczące żądania i definicji.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

<div id="grant"></div>

### <a name="grant-permissions-to-your-service-principal"></a>Udzielanie uprawnień do jednostki usługi

Udzielanie uprawnień do jednostki usługi często jest jednym z pierwszych kroków, które należy wykonać podczas pracy za pomocą Twins cyfrowych platformy Azure. Pociąga za sobą:

1. Rejestrowanie ramach wystąpienia platformy Azure za pomocą programu PowerShell.
1. Uzyskiwanie informacji o jednostce Twojej usługi.
1. Przypisywanie odpowiednią rolę do nazwy głównej usługi.

Twoja nazwa aplikacji jest dostarczany do Ciebie w usłudze Azure Active Directory. Aby dowiedzieć się więcej na temat konfigurowania i aprowizacji Twins cyfrowych platformy Azure, w usłudze Active Directory, zapoznaj się z artykułem [Szybki Start](./quickstart-view-occupancy-dotnet.md).

Po utworzeniu Identyfikatora aplikacji, wykonaj następujące polecenia środowiska PowerShell:

```shell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId  <ApplicationId>
```

Użytkownik mający **administratora** roli można następnie przypisać rolę administratora miejsca dla użytkownika, wprowadzając uwierzytelnionego żądania HTTP POST na adres URL:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Za pomocą następujących treść kodu JSON:

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

<div id="all"></div>

### <a name="retrieve-all-roles"></a>Pobieranie wszystkich ról

![Role systemu][2]

Aby wyświetlić listę wszystkich dostępnych ról (definicje ról), należy uwierzytelnionego żądania HTTP GET do:

```plaintext
YOUR_MANAGEMENT_API_URL/system/roles
```

Żądania zakończonego powodzeniem, zwróci tablica JSON z wpisy dla każdej roli, która może być przypisany:

```JSON
[
    {
        "id": "3cdfde07-bc16-40d9-bed3-66d49a8f52ae",
        "name": "DeviceAdministrator",
        "permissions": [
            {
                "notActions": [],
                "actions": [
                    "Read",
                    "Create",
                    "Update",
                    "Delete"
                ],
                "condition": "@Resource.Type Any_of {'Device', 'DeviceBlobMetadata', 'DeviceExtendedProperty', 'Sensor', 'SensorBlobMetadata', 'SensorExtendedProperty'} || ( @Resource.Type == 'ExtendedType' && (!Exists @Resource.Category || @Resource.Category Any_of { 'DeviceSubtype', 'DeviceType', 'DeviceBlobType', 'DeviceBlobSubtype', 'SensorBlobSubtype', 'SensorBlobType', 'SensorDataSubtype', 'SensorDataType', 'SensorDataUnitType', 'SensorPortType', 'SensorType' } ) )"
            },
            {
                "notActions": [],
                "actions": [
                    "Read"
                ],
                "condition": "@Resource.Type == 'Space' && @Resource.Category == 'WithoutSpecifiedRbacResourceTypes' || @Resource.Type Any_of {'ExtendedPropertyKey', 'SpaceExtendedProperty', 'SpaceBlobMetadata', 'SpaceResource', 'Matcher'}"
            }
        ],
        "accessControlPath": "/system",
        "friendlyPath": "/system",
        "accessControlType": "System"
    }
]
```

<div id="check"></div>

### <a name="check-a-specific-role-assignment"></a>Sprawdź konkretnego przypisania roli

Aby sprawdzić przypisanie określoną rolę, wprowadzić uwierzytelnionego żądania HTTP GET do:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Wartość parametru** | **Wymagane** |  **Typ** |  **Opis** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True | String |   Identyfikator obiektu objectIdType identyfikatora użytkownika. |
| YOUR_PATH | True | String |   Do sprawdzenia dostępu dla wybranej ścieżki. |
| YOUR_ACCESS_TYPE |  True | String |   Typ dostępu do wyszukania. |
| YOUR_RESOURCE_TYPE | True | String |  Zasób do sprawdzenia. |

Żądania zakończonego powodzeniem będzie zwracać wartość logiczną `true` lub `false` do wskazania, czy typ dostępu zostały przypisane do użytkownika dla danej ścieżki i zasobu.

### <a name="get-role-assignments-by-path"></a>Pobieranie przypisań ról przy użyciu ścieżki

Aby uzyskać wszystkie przypisania roli dla ścieżki, należy uwierzytelnionego żądania HTTP GET do:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Wartość | Zamień na |
| --- | --- |
| YOUR_PATH | Pełna ścieżka do obszaru |

Żądania zakończonego powodzeniem, zwróci tablicę JSON z każdym przypisania roli skojarzonych z wybranym **ścieżki** parametru:

```JSON
[
    {
        "id": "0000c484-698e-46fd-a3fd-c12aa11e53a1",
        "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
        "objectId": "0de38846-1aa5-000c-a46d-ea3d8ca8ee5e",
        "objectIdType": "UserId",
        "path": "/"
    }
]
```

### <a name="revoke-a-permission"></a>Odwołaj uprawnienia

Aby odwołać uprawnienia z adresata, Usuń przypisanie roli, wprowadzając uwierzytelnionego żądania HTTP DELETE:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | **Identyfikator** przypisania roli do usunięcia |

Pomyślne żądania usunięcia zwróci stanu 204 odpowiedzi. Usunięcie przypisania roli, można sprawdzić [sprawdzanie](#check) czy nadal utrzymuje przypisania roli.

### <a name="create-a-role-assignment"></a>Utwórz przypisanie roli

Aby utworzyć przypisanie roli, należy uwierzytelnionego żądania HTTP POST na adres URL:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Sprawdź, czy treść kodu JSON jest zgodny ze zgodny z następującym schematem:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

Żądania zakończonego powodzeniem, zwróci stan odpowiedzi 201 wraz z **identyfikator** przypisania do nowo utworzonej roli:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Przykłady konfiguracji

W poniższych przykładach pokazano, jak skonfigurować treść JSON w kilku scenariuszach najczęściej spotykanych przypisania roli.

* **Przykład**: Użytkownik wymaga dostępu administracyjnego do floor miejsca dzierżawy.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Przykład**: Aplikacja jest uruchamiana scenariuszy testowania pozorowanie urządzenia i czujniki.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Przykład**: Wszyscy użytkownicy, którzy należą do domeny otrzymują dostęp do odczytu dla miejsca do magazynowania, czujniki i użytkowników. Ten dostęp obejmuje ich odpowiednich powiązanych obiektów.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Kolejne kroki

- Aby przejrzeć Twins cyfrowego Azure roli —-kontroli dostępu opartej na —, przeczytaj [roli base-access-control](./security-authenticating-apis.md).

- Informacje na temat uwierzytelniania interfejsu API Twins cyfrowych platformy Azure, przeczytaj [uwierzytelniania interfejsu API](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/security-roles/roleassignments.png
[2]: media/security-roles/system.png
