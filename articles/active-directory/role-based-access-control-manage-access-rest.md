---
title: "Kontrola dostępu oparta na rolach przy użyciu REST - usługi Azure AD | Dokumentacja firmy Microsoft"
description: "Zarządzanie kontrolą dostępu opartej na rolach przy użyciu interfejsu API REST"
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: 
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: active-directory
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: rolyon
ms.openlocfilehash: ba25340e41cefe2b7847a39a6c9182cd0fc057d3
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2018
---
# <a name="manage-role-based-access-control-with-the-rest-api"></a>Zarządzanie kontrolą dostępu opartej na rolach przy użyciu interfejsu API REST
> [!div class="op_single_selector"]
> * [Program PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](role-based-access-control-manage-access-azure-cli.md)
> * [Interfejs API REST](role-based-access-control-manage-access-rest.md)

Z opartej na rolach kontroli dostępu (RBAC), należy zdefiniować dostęp dla użytkowników, grup i nazwy główne usług poprzez przypisywanie ról w określonym zakresie. W tym artykule opisano, jak zarządzać dostępem przy użyciu interfejsu API REST.

## <a name="list-all-role-assignments"></a>Wyświetl listę wszystkich przypisań ról
Wyświetla listę wszystkich przypisań ról w określonym zakresie i subscopes.

Na liście przypisań ról musi mieć dostęp do `Microsoft.Authorization/roleAssignments/read` operacji w tym zakresie. Wbudowane role mają dostęp do tej operacji. Aby uzyskać więcej informacji na temat przypisania ról i zarządzanie dostęp do zasobów platformy Azure, zobacz [kontroli dostępu](role-based-access-control-configure.md).

### <a name="request"></a>Żądanie
Użyj **UZYSKAĆ** metody za pomocą następującego identyfikatora URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

W identyfikatorze URI wprowadź następujące elementy zastępcze, aby dostosować żądania:

1. Zastąp *{zakresu}* z zakresem, dla którego chcesz wyświetlić listę przypisań ról. Poniższe przykłady przedstawiają sposób określania zakresu dla różnych poziomów:

   * Subskrypcja: /subscriptions/ {identyfikator subskrypcji}  
   * Grupa zasobów: /subscriptions/ {identyfikator subskrypcji} / resourceGroups/myresourcegroup1  
   * Zasób: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Zastąp *{wersja interfejsu api}* z 2015-07-01.
3. Zastąp *{filtru}* warunek, który chcesz zastosować, aby filtrować listę przypisania roli:

   * Wyświetl listę przypisań ról tylko określony zakres, z wyłączeniem przypisania ról na subscopes: `atScope()`    
   * Utwórz listę przypisań ról dla określonego użytkownika, grupy lub aplikacji. `principalId%20eq%20'{objectId of user, group, or service principal}'`  
   * Lista przypisań ról określonego użytkownika, między innymi dziedziczone z grup | `assignedTo('{objectId of user}')`

### <a name="response"></a>Odpowiedź
Kod stanu: 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role-assignment"></a>Pobierz informacje o przypisaniu roli
Pobiera informacje o przypisaniu roli jednej, określonej przez identyfikator przypisania roli.

Aby uzyskać informacje o przypisaniu roli, musi mieć dostęp do `Microsoft.Authorization/roleAssignments/read` operacji. Wbudowane role mają dostęp do tej operacji. Aby uzyskać więcej informacji na temat przypisania ról i zarządzanie dostęp do zasobów platformy Azure, zobacz [kontroli dostępu](role-based-access-control-configure.md).

### <a name="request"></a>Żądanie
Użyj **UZYSKAĆ** metody za pomocą następującego identyfikatora URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

W identyfikatorze URI wprowadź następujące elementy zastępcze, aby dostosować żądania:

1. Zastąp *{zakresu}* z zakresem, dla którego chcesz wyświetlić listę przypisań ról. Poniższe przykłady przedstawiają sposób określania zakresu dla różnych poziomów:

   * Subskrypcja: /subscriptions/ {identyfikator subskrypcji}  
   * Grupa zasobów: /subscriptions/ {identyfikator subskrypcji} / resourceGroups/myresourcegroup1  
   * Zasób: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Zastąp *{— identyfikator przypisania roli-}* o identyfikatorze GUID przypisania roli.
3. Zastąp *{wersja interfejsu api}* z 2015-07-01.

### <a name="response"></a>Odpowiedź
Kod stanu: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## <a name="create-a-role-assignment"></a>Tworzy przypisanie roli
Tworzy przypisanie roli w podanym zakresie dla określonego podmiotu zabezpieczeń udzielanie określonej roli.

Aby utworzyć przypisanie roli, musi mieć dostęp do `Microsoft.Authorization/roleAssignments/write` operacji. Wbudowanych ról, tylko *właściciela* i *Administrator dostępu użytkowników* udzielany jest dostęp do tej operacji. Aby uzyskać więcej informacji na temat przypisania ról i zarządzanie dostęp do zasobów platformy Azure, zobacz [kontroli dostępu](role-based-access-control-configure.md).

### <a name="request"></a>Żądanie
Użyj **PUT** metody za pomocą następującego identyfikatora URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

W identyfikatorze URI wprowadź następujące elementy zastępcze, aby dostosować żądania:

1. Zastąp *{zakresu}* w zakresie, w którym chcesz utworzyć przypisań ról. Po utworzeniu przypisanie roli w zakresie nadrzędnym, wszystkie zakresy podrzędne dziedziczą tego samego przypisania roli. Poniższe przykłady przedstawiają sposób określania zakresu dla różnych poziomów:

   * Subskrypcja: /subscriptions/ {identyfikator subskrypcji}  
   * Grupa zasobów: /subscriptions/ {identyfikator subskrypcji} / resourceGroups/myresourcegroup1   
   * Zasób: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Zastąp *{— identyfikator przypisania roli-}* za pomocą nowego identyfikatora GUID, który staje się identyfikator GUID nowe przypisanie roli.
3. Zastąp *{wersja interfejsu api}* z 2015-07-01.

Treść żądania Podaj wartości w następującym formacie:

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Nazwa elementu | Wymagane | Typ | Opis |
| --- | --- | --- | --- |
| wartość roleDefinitionId |Yes |Ciąg |Identyfikator roli. Format identyfikatora jest: `{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId |Yes |Ciąg |Identyfikator obiektu głównego usługi Azure AD (użytkownika, grupy lub nazwy głównej usługi), do której przypisano rolę. |

### <a name="response"></a>Odpowiedź
Kod stanu: 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## <a name="delete-a-role-assignment"></a>Usuwa przypisanie roli
Usuwa przypisanie roli w podanym zakresie.

Aby usunąć przypisanie roli, musi mieć dostęp do `Microsoft.Authorization/roleAssignments/delete` operacji. Wbudowanych ról, tylko *właściciela* i *Administrator dostępu użytkowników* udzielany jest dostęp do tej operacji. Aby uzyskać więcej informacji na temat przypisania ról i zarządzanie dostęp do zasobów platformy Azure, zobacz [kontroli dostępu](role-based-access-control-configure.md).

### <a name="request"></a>Żądanie
Użyj **usunąć** metody za pomocą następującego identyfikatora URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

W identyfikatorze URI wprowadź następujące elementy zastępcze, aby dostosować żądania:

1. Zastąp *{zakresu}* w zakresie, w którym chcesz utworzyć przypisań ról. Poniższe przykłady przedstawiają sposób określania zakresu dla różnych poziomów:

   * Subskrypcja: /subscriptions/ {identyfikator subskrypcji}  
   * Grupa zasobów: /subscriptions/ {identyfikator subskrypcji} / resourceGroups/myresourcegroup1  
   * Zasób: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Zastąp *{— identyfikator przypisania roli-}* o identyfikatorze GUID przypisania roli.
3. Zastąp *{wersja interfejsu api}* z 2015-07-01.

### <a name="response"></a>Odpowiedź
Kod stanu: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## <a name="list-all-roles"></a>Wyświetl listę wszystkich ról
Wyświetla wszystkie role, które są dostępne do przypisania w podanym zakresie.

Do listy ról musi mieć dostęp do `Microsoft.Authorization/roleDefinitions/read` operacji w tym zakresie. Wbudowane role mają dostęp do tej operacji. Aby uzyskać więcej informacji na temat przypisania ról i zarządzanie dostęp do zasobów platformy Azure, zobacz [kontroli dostępu](role-based-access-control-configure.md).

### <a name="request"></a>Żądanie
Użyj **UZYSKAĆ** metody za pomocą następującego identyfikatora URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

W identyfikatorze URI wprowadź następujące elementy zastępcze, aby dostosować żądania:

1. Zastąp *{zakresu}* z zakresem, dla którego chcesz wyświetlić listę ról. Poniższe przykłady przedstawiają sposób określania zakresu dla różnych poziomów:

   * Subskrypcja: /subscriptions/ {identyfikator subskrypcji}  
   * Grupa zasobów: /subscriptions/ {identyfikator subskrypcji} / resourceGroups/myresourcegroup1  
   * Resource /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Zastąp *{wersja interfejsu api}* z 2015-07-01.
3. Zastąp *{filtru}* warunek, który chcesz zastosować, aby filtrować listę ról:

   * Lista ról dostępne do przypisania w określonym zakresie i w żadnym z jego zakresy podrzędne: `atScopeAndBelow()`
   * Wyszukaj rolę przy użyciu nazwy wyświetlanej dokładne: `roleName%20eq%20'{role-display-name}'`. Formularz zakodowane w adresie URL nazwę wyświetlaną dokładne roli. Na przykład `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="response"></a>Odpowiedź
Kod stanu: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role"></a>Uzyskiwanie informacji o roli
Pobiera informacje o pojedynczej roli określonej przez identyfikator definicji roli. Aby uzyskać informacje o pojedynczej roli przy użyciu nazwy wyświetlanej, zobacz [listy wszystkich ról](role-based-access-control-manage-access-rest.md#list-all-roles).

Aby uzyskać informacje o roli, musi mieć dostęp do `Microsoft.Authorization/roleDefinitions/read` operacji. Wbudowane role mają dostęp do tej operacji. Aby uzyskać więcej informacji na temat przypisania ról i zarządzanie dostęp do zasobów platformy Azure, zobacz [kontroli dostępu](role-based-access-control-configure.md).

### <a name="request"></a>Żądanie
Użyj **UZYSKAĆ** metody za pomocą następującego identyfikatora URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

W identyfikatorze URI wprowadź następujące elementy zastępcze, aby dostosować żądania:

1. Zastąp *{zakresu}* z zakresem, dla którego chcesz wyświetlić listę przypisań ról. Poniższe przykłady przedstawiają sposób określania zakresu dla różnych poziomów:

   * Subskrypcja: /subscriptions/ {identyfikator subskrypcji}  
   * Grupa zasobów: /subscriptions/ {identyfikator subskrypcji} / resourceGroups/myresourcegroup1  
   * Zasób: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Zastąp *{— identyfikator definicji roli-}* o identyfikatorze GUID definicji roli.
3. Zastąp *{wersja interfejsu api}* z 2015-07-01.

### <a name="response"></a>Odpowiedź
Kod stanu: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="create-a-custom-role"></a>Tworzenie niestandardowej roli zabezpieczeń
Tworzenie niestandardowej roli zabezpieczeń.

Aby utworzyć niestandardową rolę, musi mieć dostęp do `Microsoft.Authorization/roleDefinitions/write` operację na wszystkich `AssignableScopes`. Wbudowanych ról, tylko *właściciela* i *Administrator dostępu użytkowników* udzielany jest dostęp do tej operacji. Aby uzyskać więcej informacji na temat przypisania ról i zarządzanie dostęp do zasobów platformy Azure, zobacz [kontroli dostępu](role-based-access-control-configure.md).

### <a name="request"></a>Żądanie
Użyj **PUT** metody za pomocą następującego identyfikatora URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

W identyfikatorze URI wprowadź następujące elementy zastępcze, aby dostosować żądania:

1. Zastąp *{zakresu}* przy pierwszym *AssignableScope* niestandardowej roli. Poniższe przykłady przedstawiają sposób określania zakresu dla różnych poziomów.

   * Subskrypcja: /subscriptions/ {identyfikator subskrypcji}  
   * Grupa zasobów: /subscriptions/ {identyfikator subskrypcji} / resourceGroups/myresourcegroup1  
   * Zasób: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Zastąp *{— identyfikator definicji roli-}* za pomocą nowego identyfikatora GUID, który staje się identyfikator GUID nowej niestandardowej roli.
3. Zastąp *{wersja interfejsu api}* z 2015-07-01.

Treść żądania Podaj wartości w następującym formacie:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nazwa elementu | Wymagane | Typ | Opis |
| --- | --- | --- | --- |
| name |Yes |Ciąg |Identyfikator GUID tworzona rola niestandardowa. |
| properties.roleName |Yes |Ciąg |Tworzona rola niestandardowa nazwa wyświetlana. Maksymalny rozmiar 128 znaków. |
| Properties.Description |Nie |Ciąg |Opis roli niestandardowej. Maksymalny rozmiar 1024 znaki. |
| Properties.Type |Yes |Ciąg |Wartość "CustomRole." |
| Properties.permissions.Actions |Yes |String[] |Tablica ciągów akcji określenie operacji przyznanych przez rolę niestandardowych. |
| properties.permissions.notActions |Nie |String[] |Tablica ciągów akcji określenie operacji do wykluczenia z operacji przyznanych przez rolę niestandardowych. |
| properties.assignableScopes |Yes |String[] |Tablica zakresów, w których można użyć niestandardowej roli. |

### <a name="response"></a>Odpowiedź
Kod stanu: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="update-a-custom-role"></a>Aktualizacja niestandardowej roli zabezpieczeń
Zmodyfikuj niestandardowej roli zabezpieczeń.

Aby zmodyfikować niestandardową rolę, musi mieć dostęp do `Microsoft.Authorization/roleDefinitions/write` operację na wszystkich `AssignableScopes`. Wbudowanych ról, tylko *właściciela* i *Administrator dostępu użytkowników* udzielany jest dostęp do tej operacji. Aby uzyskać więcej informacji na temat przypisania ról i zarządzanie dostęp do zasobów platformy Azure, zobacz [kontroli dostępu](role-based-access-control-configure.md).

### <a name="request"></a>Żądanie
Użyj **PUT** metody za pomocą następującego identyfikatora URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

W identyfikatorze URI wprowadź następujące elementy zastępcze, aby dostosować żądania:

1. Zastąp *{zakresu}* przy pierwszym *AssignableScope* niestandardowej roli. Poniższe przykłady przedstawiają sposób określania zakresu dla różnych poziomów:

   * Subskrypcja: /subscriptions/ {identyfikator subskrypcji}  
   * Grupa zasobów: /subscriptions/ {identyfikator subskrypcji} / resourceGroups/myresourcegroup1  
   * Zasób: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Zastąp *{— identyfikator definicji roli-}* o identyfikatorze GUID niestandardowej roli.
3. Zastąp *{wersja interfejsu api}* z 2015-07-01.

Treść żądania Podaj wartości w następującym formacie:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nazwa elementu | Wymagane | Typ | Opis |
| --- | --- | --- | --- |
| name |Yes |Ciąg |Identyfikator GUID tworzona rola niestandardowa. |
| properties.roleName |Yes |Ciąg |Nazwa wyświetlana zaktualizowane rola niestandardowa. |
| Properties.Description |Nie |Ciąg |Opis roli niestandardowej zaktualizowane. |
| Properties.Type |Yes |Ciąg |Wartość "CustomRole." |
| Properties.permissions.Actions |Yes |String[] |Tablica ciągów akcji Określanie operacje, do których tworzona rola niestandardowa zaktualizowane udziela dostępu. |
| properties.permissions.notActions |Nie |String[] |Tablica ciągów akcji określenie operacji do wykluczenia z działań, które zaktualizowane niestandardowe rola przyznaje. |
| properties.assignableScopes |Yes |String[] |Tablica zakresów, w których można użyć roli zaktualizowane niestandardowe. |

### <a name="response"></a>Odpowiedź
Kod stanu: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="delete-a-custom-role"></a>Usunięcia niestandardowej roli zabezpieczeń
Usunięcia niestandardowej roli zabezpieczeń.

Aby usunąć rolę niestandardową, należy mieć dostęp do `Microsoft.Authorization/roleDefinitions/delete` operację na wszystkich `AssignableScopes`. Wbudowanych ról, tylko *właściciela* i *Administrator dostępu użytkowników* udzielany jest dostęp do tej operacji. Aby uzyskać więcej informacji na temat przypisania ról i zarządzanie dostęp do zasobów platformy Azure, zobacz [kontroli dostępu](role-based-access-control-configure.md).

### <a name="request"></a>Żądanie
Użyj **usunąć** metody za pomocą następującego identyfikatora URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

W identyfikatorze URI wprowadź następujące elementy zastępcze, aby dostosować żądania:

1. Zastąp *{zakresu}* w zakresie, w którym chcesz usunąć definicji roli. Poniższe przykłady przedstawiają sposób określania zakresu dla różnych poziomów:

   * Subskrypcja: /subscriptions/ {identyfikator subskrypcji}  
   * Grupa zasobów: /subscriptions/ {identyfikator subskrypcji} / resourceGroups/myresourcegroup1  
   * Zasób: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Zastąp *{— identyfikator definicji roli-}* z identyfikatorem GUID identyfikator definicji roli niestandardowej roli.
3. Zastąp *{wersja interfejsu api}* z 2015-07-01.

### <a name="response"></a>Odpowiedź
Kod stanu: 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```

## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
