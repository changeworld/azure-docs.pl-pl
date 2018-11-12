---
title: Łączność urządzeń Twins cyfrowych platformy Azure i uwierzytelniania | Dokumentacja firmy Microsoft
description: Użyj Twins cyfrowych platformy Azure do nawiązywania połączenia i uwierzytelniania urządzeń
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: 42c1b0fbb6d87e9ed35d4ecce3971d8512eed4d4
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51012466"
---
# <a name="create-and-manage-role-assignments"></a>Tworzenie przypisań ról i zarządzanie nimi

Azure Twins cyfrowego używa kontroli dostępu opartej na rolach ([RBAC](./security-role-based-access-control.md)) do zarządzania dostępem do zasobów.

Obejmuje każdego przypisania roli:

* **Identyfikator obiektu**: identyfikator usługi Azure Active Directory, identyfikator obiektu nazwy głównej usługi lub nazwy domeny
* **Typ identyfikatora obiektu**
* **Identyfikator definicji roli**
* **Ścieżka miejsca**
* **Identyfikator dzierżawy**: W większości przypadków, identyfikator dzierżawy usługi Azure Active Directory

## <a name="role-definition-identifiers"></a>Identyfikatorów definicji roli

W poniższej tabeli przedstawiono, jakie można uzyskać, badając systemu/role interfejsu API.

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
| Urządzenie bramy | d4c69766-e9bd-4e61-BFC1-d8b6e686c7a8 |

## <a name="supported-objectidtypes"></a>Obsługiwane ObjectIdTypes

Obsługiwane `ObjectIdTypes`:

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
| Identyfikator RoleId| Yes |Ciąg | Identyfikator definicji roli. Znajdź definicje ról i ich identyfikatorów, badając interfejsu API systemu. |
| Identyfikator obiektu | Yes |Ciąg | Identyfikator przypisania roli, które muszą być sformatowane zgodnie z jego skojarzony typ obiektu. Aby uzyskać `DomainName` ObjectIdType, identyfikator obiektu musi zaczynać się od `“@”` znaków. |
| objectIdType | Yes |Ciąg | Typ przypisania roli. Musi mieć jedną z następujących wierszy w tej tabeli. |
| Identyfikator dzierżawy | Różna | Ciąg |Identyfikator dzierżawy. Niedozwolone dla `DeviceId` i `TenantId` ObjectIdTypes. Wymagane dla `UserId` i `ServicePrincipalId` ObjectIdTypes. Opcjonalnie na potrzeby ObjectIdType nazwa_domeny. |
| Ścieżka * | Yes | Ciąg |Ścieżka pełny dostęp do `Space` obiektu. Może to być na przykład `/{Guid}/{Guid}`. Jeśli identyfikator wymaga przypisania roli dla całego wykresu, należy określić `"/"`. Ten znak wyznacza katalogu głównego, ale nie zaleca się jej użycie. Zawsze postępuj zgodnie z zasadą najniższych uprawnień. |

## <a name="sample-configuration"></a>Przykładowa konfiguracja

W tym przykładzie użytkownik musi dostęp administracyjny do floor miejsca dzierżawy.

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : " 0fc863bb-eb51-4704-a312-7d635d70e599",
      "ObjectIdType" : "UserId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/ 091e349c-c0ea-43d4-93cf-6b57abd23a44/ d84e82e6-84d5-45a4-bd9d-006a118e3bab"
    }
  ```

W tym przykładzie aplikacja jest uruchamiana scenariuszy testowania pozorowanie urządzenia i czujniki.

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : "cabf7acd-af0b-41c5-959a-ce2f4c26565b",
      "ObjectIdType" : "ServicePrincipalId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/"
    }
  ```

Wszyscy użytkownicy, którzy należą do domeny otrzymują dostęp do odczytu dla miejsca do magazynowania, czujniki i użytkowników. Ten dostęp obejmuje ich odpowiednich powiązanych obiektów.

  ```JSON
    {
      "RoleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
      "ObjectId" : "@microsoft.com",
      "ObjectIdType" : "DomainName",
      "Path": "/091e349c-c0ea-43d4-93cf-6b57abd23a44"
    }
  ```

Pobierz użycie można pobrać przypisania roli.

```plaintext
HTTP GET /api/v1/roleassignments?path={path}
```

| **Nazwa** | **W** | **Wymagane** |    **Typ** |  **Opis** |
| --- | --- | --- | --- | --- |
| Ścieżka | Ścieżka | True | Ciąg | Pełna ścieżka do obszaru |

Usuń umożliwia usunięcie przypisania roli.

```plaintext
HTTP DELETE /api/v1/roleassignments/{id}
```

| **Nazwa** | **W** | **Wymagane** | **Typ** | **Opis** |
| --- | --- | --- | --- | --- |
| ID | Ścieżka | True | Ciąg |   Identyfikator przypisania roli |

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o bezpieczeństwie Twins cyfrowych platformy Azure, przeczytaj [uwierzytelniania interfejsu API](./security-authenticating-apis.md).
