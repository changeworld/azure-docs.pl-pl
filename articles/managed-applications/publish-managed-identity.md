---
title: Aplikacji zarządzanych platformy Azure za pomocą tożsamości zarządzanych
description: Dowiedz się, jak skonfigurować aplikację zarządzane przy użyciu tożsamości usługi zarządzanej. Tożsamość zarządzana może służyć do wdrażania Managed Applications, które są połączone z istniejącymi zasobami udzielić Managed Applications, aby zarządzać zasobami platformy Azure poza zarządzanej grupy zasobów i zapewnić operacyjną tożsamości Managed Applications dla dziennika aktywności i inne usługi w obrębie platformy Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 5ef653e825a5f1eb0f5df52f9c2544a5224b34cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66003449"
---
# <a name="azure-managed-application-with-managed-identity"></a>Aplikacji zarządzanych platformy Azure za pomocą tożsamości zarządzanych

> [!NOTE]
> Obsługa tożsamości zarządzanego Managed Applications jest obecnie w wersji zapoznawczej. Korzystanie z tożsamości zarządzanej, użyj wersji interfejsu api 2018-09-01-preview.

Dowiedz się, jak skonfigurować aplikacjami zarządzanymi, aby zawierała tożsamości usługi zarządzanej. Tożsamość zarządzana może służyć do Zezwalaj na klienta udzielić dostępu aplikacji zarządzanych do dodatkowych zasobów istniejących. Tożsamość jest zarządzana przez platformę Azure i nie wymaga obsługi administracyjnej ani Obróć jakichkolwiek kluczy tajnych. Aby uzyskać więcej informacji o zarządzanych tożsamości w usłudze Azure Active Directory (AAD), zobacz [zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

Aplikacja może otrzymać dwa rodzaje tożsamości:

- A **tożsamości przypisanych przez system** jest powiązany z aplikacją i zostanie usunięty, jeśli aplikacja zostanie usunięta. Aplikacja może mieć tylko jedną tożsamość przypisaną przez system.
- A **tożsamości przypisanych przez użytkownika** jest autonomicznym zasobów platformy Azure, które mogą być przypisane do aplikacji. Aplikacja może mieć wiele tożsamości przypisanych przez użytkownika.

## <a name="how-to-use-managed-identity"></a>Jak korzystać z tożsamości zarządzanej

Tożsamość zarządzana umożliwia wielu scenariuszy dla zarządzanych aplikacji. Kilka typowych scenariuszy, które można rozwiązać, są:

- Wdrażanie aplikacji zarządzanych połączone z istniejącymi zasobami platformy Azure. Przykładem jest wdrożenie maszyny wirtualnej (VM) platformy Azure w ramach zarządzanej aplikacji, który jest dołączony do [istniejący interfejs sieciowy](../virtual-network/virtual-network-network-interface-vm.md).
- Przyznawanie aplikacji zarządzanych i wydawcy dostęp do zasobów platformy Azure poza **zarządzanej grupy zasobów**.
- Zapewnianie operacyjnej tożsamości Managed Applications dla dziennika aktywności i innych usług w ramach platformy Azure.

## <a name="adding-managed-identity"></a>Dodawanie tożsamości zarządzanej

Tworzenie aplikacji zarządzanych przy użyciu tożsamości usługi zarządzanej wymaga dodatkowych właściwości, należy ustawić na zasób platformy Azure. W poniższym przykładzie pokazano próbkę **tożsamości** właściwości:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Istnieją dwa podstawowe sposoby tworzenia aplikacji zarządzanych za pomocą **tożsamości**: [CreateUIDefinition.json](./create-uidefinition-overview.md) i [szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Dla pojedynczego proste tworzenie scenariuszy, CreateUIDefinition powinna służyć do włączyć tożsamości zarządzanej, ponieważ zawiera ono więcej możliwości. Jednak podczas pracy z zaawansowanych lub złożonych zautomatyzowane systemy, które wymagają lub można użyć wielu wdrożeń zarządzanych aplikacji, szablony.

### <a name="using-createuidefinition"></a>Za pomocą CreateUIDefinition

Zarządzanej aplikacji można skonfigurować za pomocą tożsamości zarządzanej za pośrednictwem [CreateUIDefinition.json](./create-uidefinition-overview.md). W [generuje sekcji](./create-uidefinition-overview.md#outputs), klucz `managedIdentity` może służyć do zastępowania właściwość identity obiektu szablonu zarządzanej aplikacji. Dzwonek przykładowe spowoduje włączenie **przypisany systemowo** tożsamości w zarządzanej aplikacji. Bardziej złożone obiekty tożsamości można zostać utworzona przy użyciu elementów CreateUIDefinition poprosić użytkownika o dane wejściowe. Te dane wejściowe może służyć do tworzenia aplikacji zarządzanych za pomocą **tożsamości przypisanych przez użytkownika**.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Kiedy należy używać CreateUIDefinition tożsamości zarządzanej

Poniżej przedstawiono kilka zaleceń, kiedy należy używać CreateUIDefinition włączania tożsamości zarządzanej w zarządzanych aplikacjach.

- Tworzenie aplikacji zarządzanych przechodzi przez witryny Azure portal lub portalu marketplace.
- Tożsamości usługi zarządzanej wymaga wprowadzenia danych przez konsumenta złożone.
- Tożsamości usługi zarządzanej jest wymagana przy tworzeniu aplikacji zarządzanych.

#### <a name="systemassigned-createuidefinition"></a>Wartość SystemAssigned CreateUIDefinition

Podstawowe CreateUIDefinition umożliwiająca tożsamości wartość SystemAssigned dla zarządzanych aplikacji.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>UserAssigned CreateUIDefinition

Podstawowe CreateUIDefinition, która przyjmuje **tożsamości przypisanych przez użytkownika** zasobu jako danych wejściowych i umożliwia tożsamości UserAssigned dla zarządzanych aplikacji.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

CreateUIDefinition.json powyżej generuje Utwórz interfejs użytkownika, który zawiera pole tekstowe dla użytkownika o podanie **tożsamości przypisanych przez użytkownika** identyfikatora zasobu platformy Azure. Wygenerowany środowisko będzie wyglądać:

![Przykładowy tożsamości przypisanych przez użytkownika CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Za pomocą szablonów usługi Azure Resource Manager

> [!NOTE]
> Szablony zarządzanej aplikacji w portalu Marketplace są generowane automatycznie dla klientów, przechodząc w witrynie Azure portal utworzyć środowisko.
> W tych scenariuszach `managedIdentity` klucza danych wyjściowych na CreateUIDefinition może służyć do włączyć tożsamości.

Można również włączyć tożsamości usługi zarządzanej za pomocą szablonów usługi Azure Resource Manager. Dzwonek przykładowe spowoduje włączenie **przypisany systemowo** tożsamości w zarządzanej aplikacji. Bardziej złożone obiekty tożsamości można zostać utworzona za pomocą parametrów szablonu usługi Azure Resource Manager, podaj dane wejściowe. Te dane wejściowe może służyć do tworzenia aplikacji zarządzanych za pomocą **tożsamości przypisanych przez użytkownika**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Kiedy należy używać szablonów usługi Azure Resource Manager tożsamości zarządzanej

Poniżej przedstawiono kilka zaleceń, kiedy należy używać szablonów usługi Azure Resource Manager, włączania tożsamości zarządzanej w zarządzanych aplikacjach.

- Zarządzane aplikacje mogą programowo wdrażane na podstawie szablonu.
- Przypisania ról niestandardowych dla tożsamości zarządzanej są wymagane do obsługi administracyjnej zarządzanych aplikacji.
- Zarządzanej aplikacji platformy Azure portal i portalu marketplace przepływ tworzenia nie jest konieczne.

#### <a name="systemassigned-template"></a>Wartość SystemAssigned szablonu

Podstawowy szablon usługi Azure Resource Manager, który wdraża zarządzanej aplikacji za pomocą **przypisany systemowo** tożsamości.

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>Szablon UserAssigned

Podstawowy szablon usługi Azure Resource Manager, który wdraża zarządzanej aplikacji za pomocą **tożsamości przypisanych przez użytkownika**.

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Udzielanie dostępu do zasobów platformy Azure

Po udzieleniu tożsamości usługi zarządzanej aplikacji go można udzielić dostępu do istniejących zasobów platformy azure. Ten proces może odbywać się za pośrednictwem interfejsu (IAM) kontroli dostępu w witrynie Azure portal. Nazwa aplikacji zarządzanych lub **tożsamości przypisanych przez użytkownika** można przeszukiwać, Dodaj przypisanie roli.

![Dodaj przypisanie roli dla zarządzanych aplikacji](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Łączenie istniejących zasobów platformy Azure

> [!NOTE]
> A **tożsamości przypisanych przez użytkownika** musi być [skonfigurowane](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) przed wdrożeniem aplikacji zarządzanych. Ponadto połączony zasób wdrożenia aplikacji dla zarządzanych jest obsługiwana tylko w przypadku **marketplace** rodzaju.

Tożsamość zarządzana można również wdrożyć zarządzanej aplikacji, która wymaga dostępu do istniejących zasobów podczas jego wdrażania. Po zaaprowizowaniu aplikacji zarządzanych przez klienta i **tożsamości przypisanych przez użytkownika** mogą być dodawane do zapewniają dodatkowe zezwolenia **mainTemplate** wdrożenia.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Tworzenie CreateUIDefinition z zasobu połączonego

Podczas łączenia wdrożenia aplikacji zarządzanych do istniejących zasobów, zarówno istniejącego zasobu platformy Azure i **tożsamości przypisanych przez użytkownika** rolą zastosowanie przypisania na taki zasób musi być podana.

 Przykładowe CreateUIDefinition, która wymaga dwóch danych wejściowych: identyfikator zasobu interfejsu sieciowego i identyfikator zasobu tożsamości przypisanych przez użytkownika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

Ta CreateUIDefinition.json generuje Utwórz środowisko użytkownika, który ma dwa pola. Pierwsze pole umożliwia użytkownikowi wprowadź identyfikator zasobu platformy Azure dla zasobów jest połączona z wdrażanie zarządzanych aplikacji. Druga jest przeznaczona dla klientów wprowadzić **tożsamości przypisanych przez użytkownika** Identyfikatora zasobu platformy Azure, który ma dostęp do połączonych zasobów platformy Azure. Wygenerowany środowisko będzie wyglądać:

![Przykładowy CreateUIDefinition za pomocą dwóch danych wejściowych: interfejs sieciowy, identyfikator zasobu i identyfikator zasobu tożsamości przypisanych przez użytkownika](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Tworzenie mainTemplate z zasobu połączonego

Oprócz aktualizowaniem CreateUIDefinition głównego szablonu również musi zostać zaktualizowany do akceptowania przekazany w identyfikatorze zasobu połączonego. Aby zaakceptować nowe dane wyjściowe przez dodanie nowego parametru można aktualizować głównego szablonu. Ponieważ `managedIdentity` dane wyjściowe zastępuje wartość na wygenerowany szablon zarządzanej aplikacji, nie zostanie przekazany do głównego szablonu, a nie powinny znajdować się w sekcji parametrów.

Przykładowy główny szablon ustawiający profil sieciowy do istniejącego interfejsu sieciowego, które są dostarczane przez CreateUIDefinition.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Korzystanie z aplikacji zarządzanych za pomocą zasobu połączonego

Po utworzeniu pakietu aplikacji zarządzanych zarządzanej aplikacji mogą być używane w witrynie Azure portal. Zanim mogą być używane, istnieje kilka wstępnie wymagane kroki.

- Można utworzyć wystąpienia wymagane połączonego zasobu platformy Azure.
- **Tożsamości przypisanych przez użytkownika** musi być [utworzone i biorąc pod uwagę przypisań ról](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) do zasobu połączonego.
- Istniejące połączone identyfikator zasobu i **tożsamości przypisanych przez użytkownika** identyfikator podano CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Uzyskiwanie dostępu do tokenu tożsamości zarządzanej

Token zarządzanej aplikacji jest teraz dostępna za pośrednictwem `listTokens` interfejsu api w dzierżawie wydawcy. Przykładowe żądanie może wyglądać jak:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}?api-version=2018-09-01-preview HTTP/1.1
```

Przykładowa odpowiedź może wyglądać jak:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Jak skonfigurować zarządzanej aplikacji za pomocą dostawcy niestandardowego](./custom-providers-overview.md)