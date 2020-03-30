---
title: Przypisywanie ról administratora usługi Azure AD za pomocą interfejsu API programu Microsoft Graph | Dokumenty firmy Microsoft
description: Przypisywanie i usuwanie ról administratora usługi Azure AD za pomocą interfejsu Graph API w usłudze Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3632f8a360df8837569104232b7380fdc8383953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559151"
---
# <a name="assign-custom-admin-roles-using-the-microsoft-graph-api-in-azure-active-directory"></a>Przypisywanie niestandardowych ról administratora przy użyciu interfejsu API programu Microsoft Graph w usłudze Azure Active Directory 

Sposób przypisywania ról do kont użytkowników można zautomatyzować przy użyciu interfejsu API programu Microsoft Graph. W tym artykule omówiono operacje POST, GET i DELETE na znakach ról.

## <a name="required-permissions"></a>Wymagane uprawnienia

Połącz się z dzierżawą usługi Azure AD przy użyciu konta administratora globalnego lub administratora tożsamości uprzywilejowanej w celu przypisania lub usunięcia ról.

## <a name="post-operations-on-roleassignment"></a>Operacje POST na znakowanie roleas

Żądanie HTTP, aby utworzyć przypisanie roli między użytkownikiem a definicją roli.

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json
```

Treść

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/"]
}
```

Odpowiedź

``` HTTP
HTTP/1.1 201 Created
```

Żądanie HTTP w celu utworzenia przypisania roli, w przypadku gdy definicja podmiotu lub roli nie istnieje

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Treść

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/"]
}
```

Odpowiedź

``` HTTP
HTTP/1.1 404 Not Found
```

Żądanie HTTP, aby utworzyć przypisanie roli o określonym zakresie zasobu w wbudowanej definicji roli.

> [!NOTE] 
> Wbudowane role mają obecnie ograniczenie, w którym mogą być objęte zakresem tylko do zakresu "/" całej organizacji lub zakresu "/AU/*". Zakres pojedynczych zasobów nie działa dla ról wbudowanych, ale działa dla ról niestandardowych.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Treść

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/ab2e1023-bddc-4038-9ac1-ad4843e7e539"]
}
```

Odpowiedź

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Provided authorization scope is not supported for built-in role definitions."},
            "values":
            [
                {
                    "item":"scope",
                    "value":"/ab2e1023-bddc-4038-9ac1-ad4843e7e539"
                }
            ]
        }
    }
}
```

## <a name="get-operations-on-roleassignment"></a>Operacje GET na znakowanie roleas

Żądanie HTTP, aby uzyskać przypisanie roli dla danego podmiotu zabezpieczeń

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=principalId eq ‘<object-id-of-principal>’
```

Odpowiedź

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":["/"]
} ,
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

Żądanie HTTP, aby uzyskać przypisanie roli dla danej definicji roli.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Odpowiedź

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

Żądanie HTTP, aby uzyskać przypisanie roli przez identyfikator.

GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Odpowiedź

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1",
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":["/"]
}
```

## <a name="delete-operations-on-roleassignment"></a>Usuń operacje na znakowanie roli

Żądanie HTTP, aby usunąć przypisanie roli między użytkownikiem a definicją roli.

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Odpowiedź
``` HTTP
HTTP/1.1 204 No Content
```

Żądanie HTTP, aby usunąć przypisanie roli, które już nie istnieje

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Odpowiedź

``` HTTP
HTTP/1.1 404 Not Found
```

Żądanie HTTP, aby usunąć przypisanie roli między definicją roli własnej i wbudowanej

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Odpowiedź

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Cannot remove self from built-in role definitions."},
            "values":null
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

* Zapraszam do dzielenia się z nami na [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Aby uzyskać więcej informacji o rolach i przypisywaniu ról administratora, zobacz [Przypisywanie ról administratora](directory-assign-admin-roles.md).
* Aby uzyskać domyślne uprawnienia użytkownika, zobacz [porównanie domyślnych uprawnień gościa i użytkownika.](../fundamentals/users-default-permissions.md)
