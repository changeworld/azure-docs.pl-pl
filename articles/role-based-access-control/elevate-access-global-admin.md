---
title: Podniesienie uprawnień dostępu administratora globalnego w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Opisuje sposób podniesienie uprawnień dostępu administratora globalnego w usłudze Azure Active Directory przy użyciu portalu Azure lub interfejsu API REST.
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
ms.date: 05/11/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e1e46d5fb786b09a4c006b61f52b3ac99aafd555
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266509"
---
# <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Podniesienie uprawnień dostępu administratora globalnego w usłudze Azure Active Directory

Jeśli jesteś [administratora globalnego](../active-directory/active-directory-assign-admin-roles-azure-portal.md#global-administrator) w usłudze Azure Active Directory (Azure AD), może być czas, kiedy zachodzi potrzeba wykonaj następujące czynności:

- Odzyskać dostęp do subskrypcji platformy Azure, gdy użytkownik utracił dostęp
- Udziel innego użytkownika lub samodzielnie dostęp do subskrypcji platformy Azure
- Zobacz wszystkie subskrypcje platformy Azure w organizacji
- Zezwalaj aplikacji automatyzacji (na przykład fakturowania lub inspekcji aplikacji) do wszystkich subskrypcji platformy Azure

Domyślnie role administratora usługi Azure AD dostępu opartej na rolach na platformie Azure kontrolę i ról (RBAC) nie zakresu usługi Azure AD i Azure. Jednak jeśli jesteś administratorem globalnym w usłudze Azure AD, możesz podnieść poziom dostępu użytkownika do zarządzania subskrypcjami platformy Azure i grup zarządzania. Gdy użytkownik podniesienia uprawnień dostępu, zostanie nadany [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) roli (rola RBAC) na wszystkie subskrypcje dla konkretnego dzierżawcy. Rola Administrator dostępu użytkowników umożliwia udzielić innym użytkownikom dostęp do zasobów platformy Azure w zakresie głównym (`/`).

Podniesienie uprawnień powinna być tymczasowe i tylko wykonywane w razie potrzeby.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="elevate-access-for-a-global-administrator-using-the-azure-portal"></a>Podniesienie uprawnień dostępu administratora globalnego przy użyciu portalu Azure

1. Zaloguj się do [portalu Azure](https://portal.azure.com) lub [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com).

1. Na liście nawigacji kliknij **usługi Azure Active Directory** , a następnie kliknij przycisk **właściwości**.

   ![Azure AD właściwości — zrzut ekranu](./media/elevate-access-global-admin/aad-properties.png)

1. W obszarze **administratora globalnego mogą zarządzać subskrypcji platformy Azure i grup zarządzania**, ustawiona przełącznik na **tak**.

   ![Administrator globalny można zarządzać subskrypcjami platformy Azure i grup zarządzania — zrzut ekranu](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Jeśli zostanie ustawiona przełącznik **tak**, konta administratora globalnego (aktualnie zalogowany użytkownik) jest dodawany do roli Administrator dostępu użytkowników, w Azure RBAC w zakresie głównym (`/`), która udziela dostępu do widoku i tworzenia raportów w wszystkie subskrypcje platformy Azure skojarzony z dzierżawą usługi Azure AD.

   Jeśli zostanie ustawiona przełącznik **nr**, konta administratora globalnego (aktualnie zalogowany użytkownik) zostanie usunięty z roli Administrator dostępu użytkowników w programie Azure RBAC. Nie można wyświetlić wszystkie subskrypcje platformy Azure, które są skojarzone z dzierżawy usługi Azure AD i można wyświetlać i zarządzać nimi tylko subskrypcji platformy Azure do których przyznano Ci dostęp.

1. Kliknij przycisk **zapisać** można zapisać ustawienia.

   To ustawienie nie jest właściwością globalną i ma zastosowanie tylko do aktualnie zalogowanego użytkownika.

1. Wykonaj zadania, które należy upewnić się na podwyższonego poziomu dostępu. Gdy wszystko będzie gotowe, ustawiona przełącznik do **nr**.

## <a name="list-role-assignment-at-the-root-scope--using-powershell"></a>Lista przypisanie roli w zakresie głównym (/), za pomocą programu PowerShell

Aby wyświetlić listę przypisania roli użytkownika Administrator dostępu dla użytkownika w zakresie głównym (`/`), użyj [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) polecenia.

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

## <a name="remove-a-role-assignment-at-the-root-scope--using-powershell"></a>Usuń przypisanie roli w zakresie głównym (/), za pomocą programu PowerShell

Aby usunąć przypisanie roli użytkownika Administrator dostępu dla użytkownika w zakresie głównym (`/`), użyj [AzureRmRoleAssignment Usuń](/powershell/module/azurerm.resources/remove-azurermroleassignment) polecenia.

```azurepowershell
Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
  -RoleDefinitionName "User Access Administrator" -Scope "/"
```

## <a name="elevate-access-for-a-global-administrator-using-the-rest-api"></a>Podniesienie uprawnień dostępu administratora globalnego przy użyciu interfejsu API REST

Poniższe podstawowe kroki umożliwiają podniesienie uprawnień dostępu administratora globalnego przy użyciu interfejsu API REST.

1. Za pomocą usługi REST, wywołaj `elevateAccess`, który przyznaje Ci roli Administrator dostępu użytkowników w zakresie głównym (`/`).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Utwórz [przypisania roli](/rest/api/authorization/roleassignments) można przypisać żadnej roli w dowolnym zakresie. W poniższym przykładzie przedstawiono właściwości przypisywanie roli {roleDefinitionID} w zakresie głównym (`/`):

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

1. Usuń Twoje uprawnienia Administrator dostępu użytkowników, dopóki nie jest ponownie potrzebny.

## <a name="list-role-assignments-at-the-root-scope--using-the-rest-api"></a>Wyświetl listę przypisań ról w zakresie głównym (/), za pomocą interfejsu API REST

Można wyświetlić listę wszystkich przypisań ról użytkownika w zakresie głównym (`/`).

- Wywołanie [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope) gdzie `{objectIdOfUser}` jest Identyfikatorem obiektu użytkownika, którego przypisania ról do pobrania.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

## <a name="remove-elevated-access-using-the-rest-api"></a>Usuń z podwyższonym poziomem uprawnień dostępu przy użyciu interfejsu API REST

Podczas wywoływania `elevateAccess`, możesz utworzyć przypisania roli dla siebie, tak aby odwołać tych uprawnień należy usunąć przypisanie.

1. Wywołanie [GET roleDefinitions](/rest/api/authorization/roledefinitions/get) gdzie `roleName` jest równe Administrator dostępu użytkowników w celu określenia Identyfikatora nazwy roli użytkownika Administrator dostępu.

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

2. Należy również listy przypisania roli administrator dzierżawy w zakresie dzierżawy. Wyświetl listę wszystkich przypisań w zakresie dzierżawy dla `principalId` z administratora dzierżawy, który wykonał wywołania dostępu podniesienie uprawnień. Wyświetl listę wszystkich przypisań w dzierżawie dla objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Administrator dzierżawy nie powinny mieć wielu przypisań, jeśli poprzednie zapytanie zwraca za dużo przypisania, możesz także zbadać dla wszystkich przypisań tylko na poziomie zakresu dzierżawy, a następnie filtrować wyniki: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. Poprzednich wywołań zwraca listę przypisań ról. Znajdź przypisania roli, której zakresem jest `"/"` i `roleDefinitionId` kończy Identyfikatora nazwy roli można znaleźć w kroku 1 i `principalId` odpowiada objectId administratora dzierżawy. 
    
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
        
    Ponownie, Zapisz identyfikator z `name` parametru, w tym przypadku e7dd75bc-06f6-4e71-9014-ee96a929d099.

    3. Na koniec użyj identyfikator przypisania roli, aby usunąć przypisanie dodane przez `elevateAccess`:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Kolejne kroki

- [Kontrola dostępu oparta na rolach przy użyciu REST](role-assignments-rest.md)
- [Zarządzanie przypisaniami dostępu](role-assignments-users.md)
