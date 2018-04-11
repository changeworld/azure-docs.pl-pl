---
title: "Administrator dzierżawy podniesienie uprawnień dostępu — usługi Azure AD | Dokumentacja firmy Microsoft"
description: "W tym temacie opisano wbudowanych ról dla kontroli dostępu opartej na rolach (RBAC)."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2017
ms.author: rolyon
ms.openlocfilehash: dff3a26201507f974d52de3fe6dcb23945cd900f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2018
---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>Podniesienie uprawnień dostępu jako Administrator dzierżawy przy użyciu kontroli dostępu opartej na rolach

Oparta na rolach kontrola dostępu ułatwia administratorom dzierżawy uzyskiwania wybierającym tymczasowe w programie access, przyznają uprawnienia wyższe niż zwykle. Administratorzy dzierżawy podnieść poziom siebie do roli Administrator dostępu użytkowników w razie potrzeby. Ta rola zapewnia administratorów dzierżawy uprawnień udzielanych siebie lub innych użytkowników, ról w zakresie "/".

Ta funkcja jest ważna, ponieważ zezwala ona na administratora dzierżawy wyświetlić wszystkie subskrypcje, które istnieją w organizacji. Umożliwia on również automatyzacji aplikacji, takich jak fakturowania i przeprowadzanie inspekcji dostępu do wszystkich subskrypcji i podaj dokładnego widoku stanu organizacji rozliczeń lub zarządzania zasobami.  

## <a name="use-elevateaccess-for-tenant-access-with-azure-ad-admin-center"></a>Użyj elevateAccess na potrzeby dostępu dzierżawcy z Centrum administracyjnego usługi Azure AD

1. Przejdź do [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) i zaloguj się przy użyciu poświadczeń.

2. Wybierz **właściwości** z usługi Azure AD w lewo menu.

3. Znajdź **Administrator globalny może zarządzać subskrypcjami Azure**, wybierz **tak**, następnie **zapisać**.
    > [!IMPORTANT] 
    > Po wybraniu **tak**, przypisuje **Administrator dostępu użytkowników** roli w katalogu głównym "/" (główny zakres) dla użytkownika, z którym użytkownik jest aktualnie zalogowany do portalu. **Dzięki temu użytkownikowi na zobaczenie innych subskrypcji platformy Azure.**
    
    > [!NOTE] 
    > Po wybraniu **nr**, usuwa **Administrator dostępu użytkowników** roli w katalogu głównym "/" (główny zakres) dla użytkownika, z którym użytkownik jest aktualnie zalogowany do portalu.

> [!TIP] 
> Wrażenie to, że jest to właściwość globalny usługi Azure Active Directory, jednak działa na podstawie użytkownika dla obecnie zalogowanego użytkownika. Jeśli użytkownik ma uprawnienia administratora globalnego w usłudze Azure Active Directory, można wywołać funkcji elevateAccess dla użytkownika, że użytkownik jest zalogowany do Centrum administracyjnego usługi Azure Active Directory.

![Administrator globalny usługi Azure Centrum administracyjnego usługi AD - właściwości — można zarządzać subskrypcją Azure — zrzut ekranu](./media/role-based-access-control-tenant-admin-access/aad-azure-portal-global-admin-can-manage-azure-subscriptions.png)

## <a name="view-role-assignments-at-the--scope-using-powershell"></a>Przypisania ról widok w zakresie "/" przy użyciu programu PowerShell
Aby wyświetlić **Administrator dostępu użytkowników** przypisanie w  **/**  zakres, należy użyć `Get-AzureRmRoleAssignment` polecenia cmdlet programu PowerShell.
    
```powershell
Get-AzureRmRoleAssignment* | where {$_.RoleDefinitionName -eq "User Access Administrator" -and $_SignInName -eq "<username@somedomain.com>" -and $_.Scope -eq "/"}
```

**Przykładowe dane wyjściowe**:
```
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0    
Scope              : /    
DisplayName        : username    
SignInName         : username@somedomain.com    
RoleDefinitionName : User Access Administrator    
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9    
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc    
ObjectType         : User    
```

## <a name="delete-the-role-assignment-at--scope-using-powershell"></a>Usuwa przypisanie roli w "/" zakres przy użyciu programu Powershell:
Można usunąć przypisania przy użyciu następujące polecenia cmdlet programu PowerShell:

```powershell
Remove-AzureRmRoleAssignment -SignInName <username@somedomain.com> -RoleDefinitionName "User Access Administrator" -Scope "/" 
```

## <a name="use-elevateaccess-to-give-tenant-access-with-the-rest-api"></a>Użyj elevateAccess, aby zapewnić dostęp do dzierżawy przy użyciu interfejsu API REST

Podstawowy proces działa z następujących kroków:

1. Za pomocą usługi REST, wywołaj *elevateAccess*, która udziela roli Administrator dostępu użytkowników na "/" zakres.

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. Utwórz [przypisania roli](/rest/api/authorization/roleassignments) można przypisać żadnej roli w dowolnym zakresie. Poniższy przykład przedstawia właściwości przypisywanie roli czytnika w "/" zakres:

    ```json
    { 
      "properties": {
        "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/acdd72a7338548efbd42f606fba81ae7",
        "principalId": "cbc5e050-d7cd-4310-813b-4870be8ef5bb",
        "scope": "/"
      },
      "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
    }
    ```

3. Podczas administratorem dostępu użytkownika użytkownik może również usunąć przypisania roli w "/" zakres.

4. Odwołaj Twoje uprawnienia administratora dostępu użytkownika, dopóki nie są potrzebne, ponownie.


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>Jak cofnąć elevateAccess przy użyciu interfejsu API REST

Podczas wywoływania *elevateAccess* Tworzenie przypisania roli dla siebie, tak aby można było odwołać uprawnienia te należy usunąć przypisanie.

1.  Wywołanie GET roleDefinitions gdzie roleName = Administrator dostępu użytkowników można określić nazwy roli Administrator dostępu użytkowników identyfikator GUID.
    ```
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User+Access+Administrator
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

    Zapisz identyfikator GUID z *nazwa* parametru, w tym przypadku **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**.

2. Należy również listy przypisania roli Administrator dzierżawy w zakresie dzierżawy. Wyświetl listę wszystkich przypisań w zakresie dzierżawy dla PrincipalId z TenantAdmin, który wykonał wywołania dostępu podniesienie uprawnień. Wyświetl listę wszystkich przypisań w dzierżawie dla ObjectID.

    ```
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Administrator dzierżawy nie powinny mieć wielu przypisań, jeśli poprzednie zapytanie zwraca za dużo przypisania, możesz także zbadać dla wszystkich przypisań tylko na poziomie zakresu dzierżawy, a następnie filtrować wyniki: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
    
        
    2. Poprzednich wywołań zwraca listę przypisań ról. Znajdź przypisania roli, której zakresem jest "/" RoleDefinitionId kończy nazwę roli można znaleźć w kroku 1 identyfikator GUID i PrincipalId odpowiada ObjectId administratora dzierżawy. 
    
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
        
    Ponownie, Zapisz identyfikator GUID z *nazwa* parametru, w tym przypadku **e7dd75bc-06f6-4e71-9014-ee96a929d099**.

    3. Na koniec użyj wyróżnionych **identyfikator RoleAssignment** można usunąć przypisania dodawany przez podniesienia uprawnień dostępu:

    ```
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [zarządzania opartego na rolach kontrola dostępu przy użyciu REST](role-based-access-control-manage-access-rest.md)

- [Zarządzanie przypisaniami dostępu](role-based-access-control-manage-assignments.md) w portalu Azure
