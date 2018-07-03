---
title: Podniesienie poziomu dostępu administratora globalnego usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Opisuje sposób podniesienie poziomu dostępu administratora globalnego usługi Azure Active Directory przy użyciu witryny Azure portal lub interfejsu API REST.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/29/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0004390264ed0c22adbdc0de5e20150c7946dc4b
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37347110"
---
# <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Podniesienie poziomu dostępu administratora globalnego usługi Azure Active Directory

Jeśli jesteś [administratora globalnego](../active-directory/active-directory-assign-admin-roles-azure-portal.md#company-administrator) w usłudze Azure Active Directory (Azure AD), może być konieczny do następujących czynności:

- Odzyskać dostęp do subskrypcji platformy Azure, gdy użytkownik utracił dostęp
- Udziel innego użytkownika lub sobie dostęp do subskrypcji platformy Azure
- Zobacz wszystkie subskrypcje systemu Azure w organizacji
- Zezwól aplikacji automatyzacji (na przykład aplikacja fakturowania lub inspekcji) na dostęp do wszystkich subskrypcji platformy Azure

Domyślnie role administratora usługi Azure AD i Azure opartej na rolach kontrolę dostępu ról (RBAC) nie zakresu usługi Azure AD i Azure. Jednakże jeśli jesteś administratorem globalnym w usłudze Azure AD, możesz podnieść poziom konta dostępu do zarządzania subskrypcjami platformy Azure i grup zarządzania. Gdy możesz podnieść poziom dostępu, zostanie nadany [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) roli (rola RBAC) na wszystkie subskrypcje dla określonej dzierżawy. Rola Administrator dostępu użytkowników umożliwia przyznawanie innym użytkownikom dostęp do zasobów platformy Azure w zakresie głównym (`/`).

Podniesienie uprawnień powinna być tymczasowe i tylko wykonywane, kiedy potrzebne.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="elevate-access-for-a-global-administrator-using-the-azure-portal"></a>Podniesienie poziomu dostępu administratora globalnego przy użyciu witryny Azure portal

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) lub [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com).

1. Na liście w okienku nawigacji kliknij **usługi Azure Active Directory** a następnie kliknij przycisk **właściwości**.

   ![Właściwości usługi AD platformy Azure — zrzut ekranu](./media/elevate-access-global-admin/aad-properties.png)

1. W obszarze **Administrator globalny może zarządzać subskrypcjami platformy Azure i grup zarządzania**, ustaw przełącznik na **tak**.

   ![Administrator globalny może zarządzać subskrypcjami platformy Azure i zarządzania grupy — zrzut ekranu](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Po ustawieniu przełącznika na **tak**, konta administratora globalnego (aktualnie zalogowany użytkownik) zostanie dodany do roli Administrator dostępu użytkowników w usłudze Azure RBAC w zakresie głównym (`/`), która przyznaje dostęp do widoku i raportowanie na wszystkie subskrypcje systemu Azure skojarzone z dzierżawą usługi Azure AD.

   Po ustawieniu przełącznika na **nie**, konta administratora globalnego (aktualnie zalogowany użytkownik) zostanie usunięty z roli Administrator dostępu użytkowników w RBAC platformy Azure. Nie można wyświetlić wszystkie subskrypcje systemu Azure, które są skojarzone z dzierżawą usługi Azure AD i można wyświetlać i zarządzać nimi tylko subskrypcji platformy Azure do których użytkownik ma dostęp.

1. Kliknij przycisk **Zapisz** można zapisać ustawień użytkownika.

   To ustawienie nie jest właściwością globalną i ma zastosowanie tylko do aktualnie zalogowanego użytkownika.

1. Wykonaj zadania, które należy wprowadzić w podwyższonego poziomu dostępu. Gdy wszystko będzie gotowe, ustaw przełącznik na **nie**.

## <a name="list-role-assignment-at-the-root-scope--using-powershell"></a>Przypisanie roli listy w zakresie głównym (/), przy użyciu programu PowerShell

Aby wyświetlić listę przypisania roli Administrator dostępu użytkowników dla użytkownika w zakresie głównym (`/`), użyj [polecenia Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) polecenia.

```azurepowershell
Get-AzureRmRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
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
```

## <a name="remove-a-role-assignment-at-the-root-scope--using-powershell"></a>Usuń przypisanie roli w zakresie głównym (/), przy użyciu programu PowerShell

Aby usunąć przypisania roli Administrator dostępu użytkowników dla użytkownika w zakresie głównym (`/`), użyj [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) polecenia.

```azurepowershell
Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
  -RoleDefinitionName "User Access Administrator" -Scope "/"
```

## <a name="elevate-access-for-a-global-administrator-using-the-rest-api"></a>Podniesienie poziomu dostępu administratora globalnego przy użyciu interfejsu API REST

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

## <a name="list-role-assignments-at-the-root-scope--using-the-rest-api"></a>Lista przypisań ról w zakresie głównym (/), za pomocą interfejsu API REST

Możesz wyświetlić listę wszystkich przypisań ról użytkownika w zakresie głównym (`/`).

- Wywołaj [tym GET](/rest/api/authorization/roleassignments/listforscope) gdzie `{objectIdOfUser}` jest identyfikator obiektu użytkownika, którego przypisania ról, które mają zostać pobrane.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

## <a name="remove-elevated-access-using-the-rest-api"></a>Usuń podwyższonego poziomu dostępu przy użyciu interfejsu API REST

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

2. Należy również listy przypisania roli administrator dzierżawy w zakresie dzierżawy. Lista wszystkich przypisań w zakresie dzierżawy dla `principalId` administratora dzierżawy, który wprowadzone podniesienie uprawnień dostępu, wywołania. Spowoduje to wyświetlenie listy wszystkich przypisań w dzierżawie, aby uzyskać identyfikator obiektu.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Administrator dzierżawy nie powinny mieć wielu przypisań, jeśli poprzednie zapytanie zwraca zbyt wiele przypisania, można także badać dla wszystkich przypisań tylko na poziomie zakresu dzierżawy, a następnie filtrować wyniki: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. Poprzednie wywołania zwracają listę przypisań ról. Znajdź przypisania roli, której zakres jest `"/"` i `roleDefinitionId` kończy się na identyfikator nazwy roli w kroku 1 i `principalId` odpowiada objectId administratora dzierżawy. 
    
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

    3. Na koniec użyj identyfikator przypisania roli, aby usunąć przypisanie dodane przez `elevateAccess`:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Następne kroki

- [Kontrola dostępu oparta na rolach, z użyciem usług REST](role-assignments-rest.md)
- [Zarządzanie dostępem do zasobów platformy Azure Privileged Identity Management](pim-azure-resource.md)
- [Zarządzanie dostępem do zarządzania platformy Azure przy użyciu dostępu warunkowego](conditional-access-azure-management.md)
