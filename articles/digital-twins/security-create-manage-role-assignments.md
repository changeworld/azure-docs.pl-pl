---
title: Opis bliźniaczych reprezentacji cyfrowych platformy Azure, łączność urządzeń i uwierzytelniania | Dokumentacja firmy Microsoft
description: Za pomocą Twins cyfrowych platformy Azure do nawiązywania połączenia i uwierzytelniania urządzeń
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: adfb4c369ea1b324da8562a5b0b245ebdecff602
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324188"
---
# <a name="create-and-manage-role-assignments"></a>Tworzenie i Zarządzanie przypisaniami ról

Azure Twins cyfrowego używa kontroli dostępu opartej na rolach ([RBAC](./security-role-based-access-control.md)) do zarządzania dostępem do zasobów.

Obejmuje każdego przypisania roli:

* **Identyfikator obiektu** (identyfikator usługi Azure Active Directory, identyfikator obiektu nazwy głównej usługi lub nazwa domeny).
* **Typ identyfikatora obiektu**.
* A **identyfikator definicji roli**.
* A **ścieżkę miejsca**.
* (W większości przypadków) usługi Azure Active Directory **identyfikator dzierżawy**.

## <a name="role-definition-identifiers"></a>Identyfikatorów definicji roli

W poniższej tabeli przedstawiono, jakie można uzyskać, badając interfejsu API systemu/role:

| **Rola** | **Identyfikator** |
| --- | --- |
| Administrator miejsca | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Administrator użytkowników| dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Administrator urządzenia | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Klucz administratora | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Token administratora | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Użytkownik | b1ffdb77-c635-4E7E-ad25-948237d85b30 |
| Specjalista ds. pomocy technicznej | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Instalator urządzenia | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| GatewayDevice | d4c69766-e9bd-4e61-BFC1-d8b6e686c7a8 |

## <a name="supported-objectidtypes"></a>Obsługiwane ObjectIdTypes

Obsługiwane `ObjectIdTypes` są:

* `UserId`
* `DeviceId`
* `DomainName`
* `TenantId`
* `ServicePrincipalId`
* `UserDefinedFunctionId`

## <a name="create-a-role-assignment"></a>Utwórz przypisanie roli

```plaintext
HTTP POST /api/v1.0/roleassignments
```

| **Nazwa** | **Wymagane** | **Typ** | **Opis** |
| --- | --- | --- | --- |
| Identyfikator RoleId| Yes |ciąg | Identyfikator definicji roli. Definicje ról i ich identyfikatorów można znaleźć, badając interfejsu API systemu. |
| Identyfikator obiektu | Yes |ciąg | Identyfikator przypisania roli, które muszą być sformatowane zgodnie z jego skojarzony typ obiektu. Aby uzyskać `DomainName` ObjectIdType, identyfikator obiektu musi zaczynać się od `“@”` znaków. |
| objectIdType | Yes |ciąg | Typ przypisania roli. Musi mieć jedną z następujących wierszy w tej tabeli. |
| Identyfikator dzierżawy | Różna | ciąg |Identyfikator dzierżawy. Niedozwolone dla `DeviceId` i `TenantId` ObjectIdTypes. Wymagane dla `UserId` i `ServicePrincipalId` ObjectIdTypes. Opcjonalnie na potrzeby ObjectIdType nazwa_domeny. |
| Ścieżka * | Yes | ciąg |Ścieżka pełny dostęp do `Space` obiektu. Przykład: `/{Guid}/{Guid}` Jeśli odpowiadającym wymaga przypisania roli dla całego wykresu, należy określić `"/"` (który wyznacza katalog główny). Jednakże, który jest odradzane i **zawsze należy stosować zasadę najmniejszych uprawnień**. |

## <a name="sample-configuration"></a>Przykładowa konfiguracja

Użytkownik wymaga dostępu administracyjnego do floor miejsca dzierżawy:

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : " 0fc863bb-eb51-4704-a312-7d635d70e599",
      "ObjectIdType" : "UserId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/ 091e349c-c0ea-43d4-93cf-6b57abd23a44/ d84e82e6-84d5-45a4-bd9d-006a118e3bab"
    }
  ```

Aplikacja, przebiegów przetestować scenariusze pozorowanie urządzenia i czujniki:

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : "cabf7acd-af0b-41c5-959a-ce2f4c26565b",
      "ObjectIdType" : "ServicePrincipalId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/"
    }
  ```

Do wszystkich użytkowników domeny otrzymają dostęp do odczytu dla miejsca do magazynowania, czujniki i użytkowników, w tym ich odpowiednie powiązane obiekty:

  ```JSON
    {
      "RoleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
      "ObjectId" : "@microsoft.com",
      "ObjectIdType" : "DomainName",
      "Path": "/091e349c-c0ea-43d4-93cf-6b57abd23a44"
    }
  ```

Aby UZYSKAĆ przypisania roli:

```plaintext
HTTP GET /api/v1/roleassignments?path={path}
```

| **Nazwa** | **W** | **Wymagane** |    **Typ** |  **Opis** |
| --- | --- | --- | --- | --- |
| Ścieżka | Ścieżka | True | Ciąg | Pełna ścieżka do obszaru |

Aby usunąć przypisania roli:

```plaintext
HTTP DELETE /api/v1/roleassignments/{id}
```

| **Nazwa** | **W** | **Wymagane** | **Typ** | **Opis** |
| --- | --- | --- | --- | --- |
| ID | Ścieżka | True | Ciąg |   Identyfikator przypisania roli |

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o bezpieczeństwie Twins cyfrowych platformy Azure, przeczytaj [uwierzytelniania interfejsu API](./security-authenticating-apis.md).
