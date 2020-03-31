---
title: Tworzenie przypisań ról i zarządzanie nimi — azure digital twins | Dokumenty firmy Microsoft
description: Dowiedz się więcej o tworzeniu przypisań ról i zarządzaniu nimi w ramach usługi Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1c83ca0abfd17db873bec62f0a0d052703862a45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110409"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Tworzenie przypisań ról i zarządzanie nimi w usłudze Azure Digital Twins

Usługa Azure Digital Twins używa kontroli dostępu opartej na rolach[(RBAC)](./security-role-based-access-control.md)do zarządzania dostępem do zasobów.

## <a name="role-assignments-overview"></a>Omówienie przypisań ról

Każde przypisanie roli jest zgodne z następującą definicją:

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

| Atrybut | Nazwa | Wymagany | Typ | Opis |
| --- | --- | --- | --- | --- |
| ida roli | Identyfikator definicji roli | Tak | Ciąg | Unikatowy identyfikator żądanego przypisania roli. Znajdź definicje ról i ich identyfikator, odpytując systemowy interfejs API lub przeglądając poniższą tabelę. |
| Objectid | Identyfikator obiektu | Tak | Ciąg | Identyfikator usługi Azure Active Directory, identyfikator obiektu jednostki usługi lub nazwa domeny. Do czego lub do kogo przypisane jest przypisanie roli. Przypisanie roli musi być sformatowane zgodnie z skojarzonym typem. Dla `DomainName` objectIdType objectId musi zaczynać się od `“@”` znaku. |
| objectIdType (Identyfikator obiektu) | Typ identyfikatora obiektu | Tak | Ciąg | Rodzaj identyfikatora obiektu używane. Zobacz **obsługiwane ObjectIdTypes** poniżej. |
| ścieżka | Ścieżka przestrzeni | Tak | Ciąg | Pełna ścieżka dostępu `Space` do obiektu. Może to być na przykład `/{Guid}/{Guid}`. Jeśli identyfikator wymaga przypisania roli dla całego `"/"`wykresu, określ . Ten znak wyznacza katalog główny, ale jego użycie jest odradzane. Zawsze postępuj zgodnie z zasadą najmniejszych przywilejów. |
| identyfikator dzierżawy | Identyfikator dzierżawy | Różna | Ciąg | W większości przypadków identyfikator dzierżawy usługi Azure Active Directory. Niedozwolone dla `DeviceId` i `TenantId` ObjectIdTypes. Wymagane dla `UserId` `ServicePrincipalId` i ObjectIdTypes. Opcjonalnie dla identyfikatora objectidtype nazwy domeny. |

### <a name="supported-role-definition-identifiers"></a>Obsługiwane identyfikatory definicji roli

Każde przypisanie roli kojarzy definicję roli z jednostką w środowisku usługi Azure Digital Twins.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Obsługiwane typy identyfikatorów obiektów

Wcześniej wprowadzono atrybut **objectIdType.**

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Operacje przypisywania ról

Usługa Azure Digital Twins obsługuje pełne operacje *CREATE*, *READ*i *DELETE* dla przypisań ról. Operacje *UPDATE* są obsługiwane przez dodawanie przypisań ról, usuwanie przypisań ról lub modyfikowanie węzłów [wykresu analizy przestrzennej,](./concepts-objectmodel-spatialgraph.md) do których przypisania ról dają dostęp.

[![Punkty końcowe przypisania roli](media/security-roles/role-assignments.png)](media/security-roles/role-assignments.png#lightbox)

Dostarczona dokumentacja referencyjna Swagger zawiera dalsze informacje na temat wszystkich dostępnych punktów końcowych interfejsu API, operacji żądania i definicji.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="grant-permissions-to-your-service-principal"></a>Udziel uprawnień do jednostki usługi

Udzielanie uprawnień do jednostki usługi jest często jednym z pierwszych kroków, które należy podjąć podczas pracy z usługą Azure Digital Twins. Wiąże się to z:

1. Logowanie się do wystąpienia platformy Azure za pośrednictwem [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) lub programu [PowerShell.](https://docs.microsoft.com/powershell/azure/)
1. Uzyskiwanie informacji o zleceniodawcy usługi.
1. Przypisywanie żądanej roli do jednostki usługi.

Identyfikator aplikacji jest dostarczany w usłudze Azure Active Directory. Aby dowiedzieć się więcej na temat konfigurowania i inicjowania obsługi administracyjnej usługi Azure Digital Twins w usłudze Active Directory, przeczytaj [przewodnik Szybki start](./quickstart-view-occupancy-dotnet.md).

Po uzyskaniu identyfikatora aplikacji należy wykonać jedno z następujących poleceń. W usłudze Azure CLI:

```azurecli
az login
az ad sp show --id <ApplicationId>
```

W programie Powershell:

```powershell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId <ApplicationId>
```

Użytkownik z rolą **Administrator** może następnie przypisać do użytkownika rolę Administratora obszaru, wysyłając do adresu URL uwierzytelnione żądanie HTTP POST:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments
```

Z następującym korpusem JSON:

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

### <a name="retrieve-all-roles"></a>Pobieranie wszystkich ról

[![Role systemowe](media/security-roles/system-api.png)](media/security-roles/system-api.png#lightbox)

Aby wyświetlić listę wszystkich dostępnych ról (definicje ról), należy wykonać uwierzytelnione żądanie HTTP GET:

```URL
YOUR_MANAGEMENT_API_URL/system/roles
```

Pomyślne żądanie zwróci tablicę JSON z wpisami dla każdej roli, która może być przypisana:

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

### <a name="check-a-specific-role-assignment"></a>Sprawdzanie przypisania określonej roli

Aby sprawdzić przypisanie określonej roli, należy wykonać uwierzytelnione żądanie HTTP GET na adres:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Wartość parametru** | **Wymagane** |  **Typ** |  **Opis** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True | Ciąg |   Identyfikator obiektu identyfikatora użytkownika. |
| YOUR_PATH | True | Ciąg |   Wybrana ścieżka, aby sprawdzić dostęp. |
| YOUR_ACCESS_TYPE |  True | Ciąg |   *Odczyt*, *Tworzenie*, *Aktualizacja*lub *Usuń* |
| YOUR_RESOURCE_TYPE | True | Ciąg |  *Urządzenie*, *DeviceBlobMetadata*, *DeviceExtendedProperty*, *ExtendedPropertyKey*, *ExtendedType*, *Punkt końcowy*, *KeyStore*, *Matcher*, *Ontologia*, *Raport*, *Funkcja Zdefiniowanie roli*, *Czujnik*, *SensorExtendedProperty*, *Spacja*, *SpaceBlobMetadata*, *SpaceExtendedProperty*, *SpaceResource*, *SpaceRoleAssignment*, *System*, * UerDedefiniowana Funkcje*, *Użytkownik*, *UserBlobMetadata*lub *UserExtendedProperty* |

Pomyślne żądanie zwróci wartość `true` `false` logiczną lub wskaże, czy typ dostępu został przypisany do użytkownika dla danej ścieżki i zasobu.

### <a name="get-role-assignments-by-path"></a>Pobierz przydziały ról według ścieżki

Aby uzyskać wszystkie przypisania ról dla ścieżki, skonsuj uwierzytelnione żądanie HTTP GET do:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Wartość | Zamień na |
| --- | --- |
| YOUR_PATH | Pełna ścieżka do przestrzeni |

Pomyślne żądanie zwróci tablicę JSON z każdym przypisaniem roli skojarzonym z wybranym parametrem **ścieżki:**

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

### <a name="revoke-a-permission"></a>Cofanie uprawnienia

Aby odwołać uprawnienie od adresata, usuń przypisanie roli, wysyłając uwierzytelnione żądanie HTTP DELETE:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | **Identyfikator** przypisania roli do usunięcia |

Pomyślne żądanie DELETE zwróci stan odpowiedzi 204. Sprawdź usunięcie przypisania roli, [sprawdzając,](#check-a-specific-role-assignment) czy przypisanie roli nadal jest w posiadaniu.

### <a name="create-a-role-assignment"></a>Tworzenie przypisania roli

Aby utworzyć przypisanie roli, należy utworzyć uwierzytelnione żądanie HTTP POST w adresie URL:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments
```

Sprawdź, czy treść JSON jest zgodna z następującym schematem:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

Pomyślne żądanie zwróci stan odpowiedzi 201 wraz z **identyfikatorem** nowo utworzonego przypisania roli:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Przykłady konfiguracji

Poniższe przykłady pokazują, jak skonfigurować treść JSON w kilku scenariuszach często spotykanych przypisania ról.

* **Przykład:** Użytkownik potrzebuje dostępu administracyjnego do podłogi przestrzeni dzierżawy.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Przykład:** Aplikacja uruchamia scenariusze testowe szydercze urządzeń i czujników.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Przykład:** Wszyscy użytkownicy należący do domeny otrzymują dostęp do odczytu dla przestrzeni, czujników i użytkowników. Ten dostęp obejmuje ich odpowiednich powiązanych obiektów.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Następne kroki

- Aby przejrzeć kontrolę dostępu opartą na rolach usługi Azure Digital Twins, przeczytaj artykuł Kontrola dostępu bazy roli.To review Azure Digital Twins role-based-access-control, read [Role-base-access-control](./security-authenticating-apis.md).

- Aby dowiedzieć się więcej o uwierzytelnianiu interfejsu API usługi Azure Digital Twins, przeczytaj [artykuł Uwierzytelnianie interfejsu API](./security-authenticating-apis.md).
