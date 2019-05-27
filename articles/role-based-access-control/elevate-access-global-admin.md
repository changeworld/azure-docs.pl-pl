---
title: Podniesienie poziomu dostępu, aby zarządzać wszystkimi subskrypcjami platformy Azure i grup zarządzania | Dokumentacja firmy Microsoft
description: Opisuje sposób podniesienie poziomu dostępu administratora globalnego zarządzać wszystkimi subskrypcji i grup zarządzania w usłudze Azure Active Directory przy użyciu witryny Azure portal lub interfejsu API REST.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ede7037aabc85739ee47636f1390c15e0b0d1639
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66158419"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Podniesienie poziomu dostępu, aby zarządzać wszystkimi subskrypcjami platformy Azure i grup zarządzania

Jako Administrator globalny usługi Azure Active Directory (Azure AD) możesz utracić dostęp do wszystkich subskrypcji i grup zarządzania w Twoim katalogu. W tym artykule opisano sposób, że możesz podnieść poziom konta dostępu do wszystkich subskrypcji i grup zarządzania.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Dlaczego potrzebować podniesienie poziomu dostępu?

Jeśli jesteś administratorem globalnym, może być konieczny wykonać następujące czynności:

- Odzyskać dostęp do platformy Azure subskrypcję lub grupę zarządzania, gdy użytkownik utracił dostęp
- Udziel innego użytkownika lub sobie dostęp do platformy Azure subskrypcję lub grupę zarządzania
- Zobacz wszystkie subskrypcje platformy Azure lub grup zarządzania w organizacji
- Zezwalaj aplikacji automatyzacji (na przykład fakturowania lub inspekcji aplikacji), dostęp do wszystkich subskrypcji platformy Azure lub grupy zarządzania

## <a name="how-does-elevate-access-work"></a>Jak podnieść dostęp?

Usługa Azure AD i zasobów platformy Azure są zabezpieczone niezależnie od siebie nawzajem. Oznacza to przypisania roli usługi Azure AD nie może udzielać dostępu do zasobów platformy Azure i przypisań ról platformy Azure nie może udzielać dostępu do usługi Azure AD. Jednak jeśli [administratora globalnego](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) w usłudze Azure AD można przypisać sobie dostęp do wszystkich subskrypcji platformy Azure i grup zarządzania w Twoim katalogu. Użyj tej funkcji, jeśli nie masz dostępu do zasobów subskrypcji platformy Azure, takich jak maszyny wirtualne lub kont magazynu, i chcesz używać z uprawnieniami administratora globalnego w celu uzyskania dostępu do tych zasobów.

Po użytkownik podniesienie poziomu dostępu, użytkownik zostanie przypisany [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) roli na platformie Azure na zakres głównego (`/`). Dzięki temu można wyświetlić wszystkie zasoby i przypisać dostęp w dowolnej subskrypcji lub grupy zarządzania w katalogu. Można usunąć przypisania roli Administrator dostępu użytkowników przy użyciu programu PowerShell.

Po wprowadzeniu zmiany, które należy wprowadzić w zakresie głównym, należy usunąć ten podwyższonego poziomu dostępu.

![Podniesienie poziomu dostępu](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure Portal

Wykonaj następujące kroki w celu podniesienie poziomu dostępu administratora globalnego przy użyciu witryny Azure portal.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) lub [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com) jako Administrator globalny.

1. Na liście w okienku nawigacji kliknij **usługi Azure Active Directory** a następnie kliknij przycisk **właściwości**.

   ![Właściwości usługi AD platformy Azure — zrzut ekranu](./media/elevate-access-global-admin/aad-properties.png)

1. W obszarze **Access management dla zasobów platformy Azure**, ustaw przełącznik w pozycji **tak**.

   ![Zarządzanie dostępem do zasobów platformy Azure — zrzut ekranu](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Gdy ustaw przełącznik w pozycji **tak**, masz przypisaną rolę administratora dostępu użytkowników w usłudze Azure RBAC w zakresie głównym (/). Przyznaje uprawnienia do przypisywania ról we wszystkich subskrypcjach platformy Azure i skojarzone z tego katalogu usługi Azure AD grupy zarządzania. Ten przełącznik jest dostępna tylko dla użytkowników, którzy mają przypisaną rolę administratora globalnego w usłudze Azure AD.

   Gdy ustaw przełącznik w pozycji **nie**, rolę Administrator dostępu użytkowników w RBAC platformy Azure zostanie usunięty z konta użytkownika. Nie można przypisać ról we wszystkich subskrypcjach platformy Azure i grup zarządzania, które są skojarzone z tego katalogu usługi Azure AD. Można wyświetlać i zarządzać tylko subskrypcji platformy Azure i grup zarządzania, do których użytkownik ma dostęp.

1. Kliknij przycisk **Zapisz** można zapisać ustawień użytkownika.

   To ustawienie nie jest właściwością globalną i ma zastosowanie tylko do aktualnie zalogowanego użytkownika. Nie można podniesienie poziomu dostępu dla wszystkich elementów członkowskich w roli administratora globalnego.

1. Wyloguj się i zalogują się ponownie odświeżyć dostępu.

    Teraz mają dostęp do wszystkich subskrypcji i grup zarządzania w Twoim katalogu. Można zauważyć, że przypisano Ci rolę administratora dostępu użytkowników na zakres głównego.

   ![Subskrypcja przypisań ról z zakresu głównego — zrzut ekranu](./media/elevate-access-global-admin/iam-root.png)

1. Wprowadź zmiany, które należy wprowadzić w podwyższonego poziomu dostępu.

    Aby uzyskać informacje dotyczące przypisywania ról, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](role-assignments-portal.md). Jeśli używasz usługi Azure AD Privileged Identity Management (PIM), zobacz [odnajdywanie zasobów platformy Azure do zarządzania w usłudze PIM](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) lub [Azure przypisać role zasobów w usłudze PIM](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

1. Gdy wszystko będzie gotowe, ustaw **Access management dla zasobów platformy Azure** powrócić do **nie**. Ponieważ jest to ustawienie dla poszczególnych użytkowników, możesz muszą być podpisane jako tego samego użytkownika, która była stosowana do podniesienia uprawnień dostępu.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-the-root-scope-"></a>Przypisanie roli listy w zakresie głównym (/)

Aby wyświetlić listę przypisania roli Administrator dostępu użytkowników dla użytkownika w zakresie głównym (`/`), użyj [Get AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) polecenia.

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-a-role-assignment-at-the-root-scope-"></a>Usuń przypisanie roli w zakresie głównym (/)

Aby usunąć przypisania roli Administrator dostępu użytkowników dla użytkownika w zakresie głównym (`/`), wykonaj następujące kroki.

1. Zaloguj się jako użytkownik, który można usunąć podwyższonego poziomu dostępu. Może to być ten sam użytkownik, który został użyty do podniesienia poziomu dostępu lub innego administratora globalnego z dostępem z podwyższonym poziomem uprawnień w zakresie głównym.


1. Użyj [AzRoleAssignment Usuń](/powershell/module/az.resources/remove-azroleassignment) polecenie, aby usunąć przypisania roli Administrator dostępu użytkowników.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="rest-api"></a>Interfejs API REST

### <a name="elevate-access-for-a-global-administrator"></a>Podniesienie poziomu dostępu administratora globalnego

Poniższe podstawowe kroki umożliwia podniesienie poziomu dostępu administratora globalnego przy użyciu interfejsu API REST.

1. Przy użyciu usługi REST, wywołaj `elevateAccess`, która udziela użytkownikowi rolę administratora dostępu użytkowników w zakresie głównym (`/`).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Tworzenie [przypisania roli](/rest/api/authorization/roleassignments) można przyznawać żadnej roli w dowolnym zakresie. W poniższym przykładzie pokazano właściwości do przypisywania roli {roleDefinitionID} w zakresie głównym (`/`):

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
   }
   ```

1. Podczas Administrator dostępu użytkowników, można również usunąć przypisania roli w zakresie głównym (`/`).

1. Usuń Twoje uprawnienia Administrator dostępu użytkowników, dopóki nie będą one potrzebne ponownie.

### <a name="list-role-assignments-at-the-root-scope-"></a>Lista przypisań ról w zakresie głównym (/)

Możesz wyświetlić listę wszystkich przypisań ról użytkownika w zakresie głównym (`/`).

- Wywołaj [tym GET](/rest/api/authorization/roleassignments/listforscope) gdzie `{objectIdOfUser}` jest identyfikator obiektu użytkownika, którego przypisania ról, które mają zostać pobrane.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-the-root-scope-"></a>Lista Odmów przypisania w zakresie głównym (/)

Możesz wyświetlić listę wszystkich przypisań odmowy dla użytkownika w zakresie głównym (`/`).

- Wywołanie GET denyAssignments gdzie `{objectIdOfUser}` jest identyfikator obiektu użytkownika, którego Odmów przypisania, które mają zostać pobrane.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Usuń podwyższonego poziomu dostępu

Gdy wywołujesz `elevateAccess`, możesz utworzyć przypisania roli dla siebie, tak aby odwołać te uprawnienia należy usunąć przypisanie.

1. Wywołaj [GET roleDefinitions](/rest/api/authorization/roledefinitions/get) gdzie `roleName` jest równe Administrator dostępu użytkowników, aby określić identyfikator nazwy roli Administrator dostępu użytkowników.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
        "roleName": "User Access Administrator",
        "type": "BuiltInRole",
        "description": "Lets you manage user access to Azure resources.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Authorization/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedOn": "2016-05-31T23:14:04.6964687Z",
        "createdBy": null,
        "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Zapisz identyfikator z `name` parametru, w tym przypadku `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

2. Należy również listy przypisania roli administrator katalogu, w zakresie katalogu. Lista wszystkich przypisań w zakresie katalogu dla `principalId` administratora katalogu, który wprowadzone podniesienie uprawnień dostępu, wywołania. Spowoduje to wyświetlenie listy wszystkich przypisań w bieżącym katalogu dla objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Administrator katalogu nie powinny mieć wielu przypisań, jeśli poprzednie zapytanie zwraca zbyt wiele przypisania, można także badać dla wszystkich przypisań tylko na poziomie zakresu w katalogu, a następnie filtrować wyniki: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
   1. Poprzednie wywołania zwracają listę przypisań ról. Znajdź przypisania roli, której zakres jest `"/"` i `roleDefinitionId` kończy się na identyfikator nazwy roli w kroku 1 i `principalId` odpowiada objectId administratorem katalogu. 
    
      Przykład przypisania roli:

       ```json
       {
         "value": [
           {
             "properties": {
               "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
               "principalId": "{objectID}",
               "scope": "/",
               "createdOn": "2016-08-17T19:21:16.3422480Z",
               "updatedOn": "2016-08-17T19:21:16.3422480Z",
               "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
               "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
             },
             "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
             "type": "Microsoft.Authorization/roleAssignments",
             "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
           }
         ],
         "nextLink": null
       }
       ```
        
      Ponownie zapisz identyfikator `name` parametru, w tym przypadku e7dd75bc-06f6-4e71-9014-ee96a929d099.

   1. Na koniec użyj identyfikator przypisania roli, aby usunąć przypisanie dodane przez `elevateAccess`:

      ```http
      DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
      ```

## <a name="next-steps"></a>Kolejne kroki

- [Understand the different roles in Azure (Omówienie ról na platformie Azure)](rbac-and-directory-admin-roles.md)
- [Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i interfejsu API REST](role-assignments-rest.md)
