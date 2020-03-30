---
title: Zarządzana aplikacja z tożsamością zarządzana
description: Skonfiguruj aplikację zarządzaną przy użyciu tożsamości zarządzanej do łączenia z istniejącymi zasobami, zarządzania zasobami platformy Azure i dostarczania tożsamości operacyjnej dla dziennika aktywności.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: dbf75262440474c5cb50a6d733ac7cba212b5f3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651659"
---
# <a name="azure-managed-application-with-managed-identity"></a>Aplikacja zarządzana platformy Azure z tożsamością zarządzana

> [!NOTE]
> Obsługa tożsamości zarządzanej dla aplikacji zarządzanych jest obecnie w wersji zapoznawczej. Użyj wersji interfejsu API 2018-09-01-preview, aby wykorzystać tożsamość zarządzaną.

Dowiedz się, jak skonfigurować aplikację zarządzaną w celu ograniczenia tożsamości zarządzanej. Tożsamość zarządzana może służyć do umożliwienia klientowi udzielenia dostępu do aplikacji zarządzanej do dodatkowych istniejących zasobów. Tożsamość jest zarządzana przez platformę Azure i nie wymaga aprowidizacji ani obracania żadnych wpisów tajnych. Aby uzyskać więcej informacji o tożsamościach zarządzanych w usłudze Azure Active Directory (AAD), zobacz [Tożsamości zarządzane dla zasobów platformy Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

Wniosek może otrzymać dwa typy tożsamości:

- **Tożsamość przypisana systemowi** jest powiązana z aplikacją i jest usuwana, jeśli aplikacja zostanie usunięta. Aplikacja może mieć tylko jedną tożsamość przypisaną do systemu.
- **Tożsamość przypisana przez użytkownika** to autonomiczny zasób platformy Azure, który można przypisać do aplikacji. Aplikacja może mieć wiele tożsamości przypisanych przez użytkownika.

## <a name="how-to-use-managed-identity"></a>Jak korzystać z tożsamości zarządzanej

Tożsamość zarządzana umożliwia wiele scenariuszy dla aplikacji zarządzanych. Niektóre typowe scenariusze, które można rozwiązać, to:

- Wdrażanie aplikacji zarządzanej połączonej z istniejącymi zasobami platformy Azure. Przykładem jest wdrażanie maszyny wirtualnej platformy Azure (VM) w aplikacji zarządzanej, która jest dołączona do [istniejącego interfejsu sieciowego.](../../virtual-network/virtual-network-network-interface-vm.md)
- Przyznawanie aplikacji zarządzanej i wydawcy dostępu do zasobów platformy Azure poza **zarządzana grupa zasobów**.
- Zapewnienie tożsamości operacyjnej zarządzanych aplikacji dla dziennika aktywności i innych usług na platformie Azure.

## <a name="adding-managed-identity"></a>Dodawanie tożsamości zarządzanej

Tworzenie aplikacji zarządzanej z tożsamością zarządzaną wymaga dodatkowej właściwości, która ma być ustawiona w zasobie platformy Azure. W poniższym przykładzie przedstawiono przykładową właściwość **tożsamości:**

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Istnieją dwa typowe sposoby tworzenia aplikacji zarządzanej z **tożsamością:** [Szablony CreateUIDefinition.json](./create-uidefinition-overview.md) i [Azure Resource Manager](../templates/template-syntax.md). W przypadku prostych scenariuszy pojedynczego tworzenia createu należy użyć createuidefinition, aby włączyć tożsamość zarządzaną, ponieważ zapewnia bogatsze środowisko. Jednak w przypadku systemów zaawansowanych lub złożonych, które wymagają zautomatyzowanych lub wielu wdrożeń aplikacji zarządzanych, można użyć szablonów.

### <a name="using-createuidefinition"></a>Korzystanie z funkcji CreateUIDefinition

Aplikację zarządzaną można skonfigurować za pomocą tożsamości zarządzanej za pośrednictwem pliku [CreateUIDefinition.json](./create-uidefinition-overview.md). W [sekcji dane wyjściowe](./create-uidefinition-overview.md#outputs)klucz `managedIdentity` może służyć do zastąpienia właściwości tożsamości szablonu aplikacji zarządzanej. Przykładowy poniżej włączy tożsamość **przypisaną do systemu** w aplikacji zarządzanej. Bardziej złożone obiekty tożsamości mogą być tworzone przy użyciu CreateUIDefinition elementów, aby poprosić konsumenta o dane wejściowe. Te dane wejściowe mogą służyć do konstruowania aplikacji zarządzanych z **tożsamością przypisaną przez użytkownika.**

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Kiedy używać createuidedefition dla tożsamości zarządzanej

Poniżej znajduje się kilka zaleceń dotyczących tego, kiedy używać CreateUIDefinition do włączania tożsamości zarządzanej w aplikacjach zarządzanych.

- Tworzenie aplikacji zarządzanej przechodzi przez witrynę Azure portal lub marketplace.
- Tożsamość zarządzana wymaga złożonych danych wejściowych konsumenta.
- Tożsamość zarządzana jest potrzebna podczas tworzenia aplikacji zarządzanej.

#### <a name="systemassigned-createuidefinition"></a>Konfiguracja createui przypisana do systemu

Podstawowa funkcja CreateUIDefinition, która umożliwia tożsamość przypisaną przez system dla aplikacji zarządzanej.

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

#### <a name="userassigned-createuidefinition"></a>Identyfikator CreateUIDefinicja przypisana przez użytkownika

Podstawowa funkcja CreateUIDefinition, która przyjmuje zasób **tożsamości przypisany przez użytkownika** jako dane wejściowe i włącza tożsamość przypisaną przez użytkownika dla aplikacji zarządzanej.

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

CreateUIDefinition.json powyżej generuje środowisko użytkownika tworzenia, który ma pole tekstowe dla konsumenta, aby wprowadzić identyfikator zasobu **platformy Azure przypisany przez użytkownika.** Wygenerowane doświadczenie będzie wyglądać następująco:

![Przykładowa tożsamość przypisana przez użytkownika CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Używanie szablonów usługi Azure Resource Manager

> [!NOTE]
> Szablony aplikacji zarządzanych w portalu Marketplace są generowane automatycznie dla klientów korzystających z środowiska tworzenia witryny Azure Portal.
> W tych scenariuszach `managedIdentity` klucz wyjściowy na CreateUIDefinition musi służyć do włączonej tożsamości.

Tożsamość zarządzana można również włączyć za pomocą szablonów usługi Azure Resource Manager. Przykładowy poniżej włączy tożsamość **przypisaną do systemu** w aplikacji zarządzanej. Bardziej złożone obiekty tożsamości mogą być tworzone przy użyciu parametrów szablonu usługi Azure Resource Manager w celu zapewnienia danych wejściowych. Te dane wejściowe mogą służyć do konstruowania aplikacji zarządzanych z **tożsamością przypisaną przez użytkownika.**

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Kiedy używać szablonów usługi Azure Resource Manager dla tożsamości zarządzanej

Poniżej znajduje się kilka zaleceń dotyczących tego, kiedy używać szablonów usługi Azure Resource Manager do włączania tożsamości zarządzanej w aplikacjach zarządzanych.

- Aplikacje zarządzane można wdrożyć programowo na podstawie szablonu.
- Niestandardowe przypisania ról dla tożsamości zarządzanej są potrzebne do aprowizowania aplikacji zarządzanej.
- Aplikacja zarządzana nie wymaga przepływu tworzenia portalu azure i portalu marketplace.

#### <a name="systemassigned-template"></a>Szablon przypisany do systemu

Podstawowy szablon usługi Azure Resource Manager, który wdraża aplikację zarządzaną z **tożsamością przypisaną do systemu.**

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

### <a name="userassigned-template"></a>Szablon przypisany przez użytkownika

Podstawowy szablon usługi Azure Resource Manager, który wdraża aplikację zarządzaną z **tożsamością przypisaną przez użytkownika.**

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

Po przyznaniu tożsamości aplikacji zarządzanej można udzielić jej dostępu do istniejących zasobów platformy Azure. Ten proces można wykonać za pomocą interfejsu kontroli dostępu (IAM) w witrynie Azure portal. Nazwę aplikacji zarządzanej lub **tożsamości przypisanej przez użytkownika** można przeszukać w celu dodania przypisania roli.

![Dodawanie przypisania roli dla aplikacji zarządzanej](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Łączenie istniejących zasobów platformy Azure

> [!NOTE]
> Tożsamość **przypisana przez użytkownika** musi być [skonfigurowana](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) przed wdrożeniem aplikacji zarządzanej. Ponadto połączone wdrażanie zasobów aplikacji zarządzanych jest obsługiwane tylko dla rodzaju **rynku.**

Tożsamość zarządzana może również służyć do wdrażania aplikacji zarządzanej, która wymaga dostępu do istniejących zasobów podczas jej wdrażania. Gdy aplikacja zarządzana jest aprowizowana przez klienta, **tożsamości przypisane przez użytkownika** można dodać w celu zapewnienia dodatkowych autoryzacji do wdrożenia **mainTemplate.**

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Tworzenie createuidefinition z połączonym zasobem

Podczas łączenia wdrożenia aplikacji zarządzanej z istniejącymi zasobami należy podać zarówno istniejący zasób platformy Azure, jak i **tożsamość przypisaną przez użytkownika** z odpowiednim przypisaniem roli dla tego zasobu.

 Przykładowa definicja CreateUIDefinacja, która wymaga dwóch danych wejściowych: identyfikatora zasobu interfejsu sieciowego i identyfikatora zasobu tożsamości przypisanego przez użytkownika.

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

Ten CreateUIDefinition.json generuje środowisko użytkownika tworzenia, który ma dwa pola. Pierwsze pole umożliwia użytkownikowi wprowadzenie identyfikatora zasobu platformy Azure dla zasobu połączonego z wdrożeniem aplikacji zarządzanej. Drugi jest dla konsumenta, aby wprowadzić identyfikator zasobu platformy Azure **przypisany przez użytkownika,** który ma dostęp do połączonego zasobu platformy Azure. Wygenerowane doświadczenie będzie wyglądać następująco:

![Przykładowe tworzenie identyfikatora CreateUI z dwoma wejściami: identyfikator zasobu interfejsu sieciowego i identyfikator zasobu tożsamości przypisany przez użytkownika](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Tworzenie płyty głównej z połączonym zasobem

Oprócz aktualizacji CreateUIDefinition, szablon główny również musi zostać zaktualizowany, aby zaakceptować przekazany w identyfikatorze połączonego zasobu. Główny szablon można zaktualizować, aby zaakceptować nowe dane wyjściowe, dodając nowy parametr. Ponieważ `managedIdentity` dane wyjściowe zastępuje wartość na wygenerowany szablon aplikacji zarządzanej, nie jest przekazywana do szablonu głównego i nie powinny być uwzględniane w sekcji parametry.

Przykładowy szablon główny, który ustawia profil sieciowy na istniejący interfejs sieciowy dostarczony przez CreateUIDefinition.

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

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Korzystanie z aplikacji zarządzanej przy zasób połączony

Po utworzeniu pakietu aplikacji zarządzanej aplikacja zarządzana może być wykorzystana za pośrednictwem witryny Azure portal. Zanim będzie można zużyć, istnieje kilka kroków wstępnych.

- Należy utworzyć wystąpienie wymaganego połączonego zasobu platformy Azure.
- Tożsamość **przypisana przez użytkownika** musi zostać [utworzona i przypisanie roli](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) do połączonego zasobu.
- Istniejący identyfikator połączonego zasobu i identyfikator **tożsamości przypisany przez użytkownika** są dostarczane do CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Uzyskiwanie dostępu do tokenu tożsamości zarządzanej

Token aplikacji zarządzanej można teraz uzyskać `listTokens` za pośrednictwem interfejsu API z dzierżawy wydawcy. Przykładowe żądanie może wyglądać następująco:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

Parametry treści żądania:

Parametr | Wymagany | Opis
---|---|---
autoryzacjaUwaga | *nie* | Identyfikator URI identyfikatora aplikacji zasobu docelowego. Jest to `aud` również (publiczność) roszczenia wydanego tokenu. Wartością domyślnąhttps://management.azure.com/jest " "
użytkownikAssignedIdentities | *nie* | Lista tożsamości zarządzanych przypisanych przez użytkownika do pobrania tokenu. Jeśli nie `listTokens` zostanie określony, zwróci token dla tożsamości zarządzanej przypisanej przez system.


Przykładowa odpowiedź może wyglądać następująco:

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

Odpowiedź będzie zawierać tablicę tokenów `value` pod właściwością:

Parametr | Opis
---|---
access_token | Żądany token dostępu.
expires_in | Liczba sekund tokenu dostępu będzie prawidłowa.
expires_on | Czas po wygaśnięciu tokenu dostępu. Jest to reprezentowane jako liczba sekund od epoki.
not_before | Czas, gdy token dostępu staje się skuteczne. Jest to reprezentowane jako liczba sekund od epoki.
autoryzacjaUwaga | (Odbiorcy) `aud` token dostępu został poproszony o. Jest to taka sama, jak `listTokens` to, co zostało dostarczone w żądaniu.
resourceId | Identyfikator zasobu platformy Azure dla wystawionego tokenu. Jest to identyfikator aplikacji zarządzanej lub identyfikator tożsamości przypisany przez użytkownika.
token_type | Typ tokenu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Jak skonfigurować aplikację zarządzaną za pomocą dostawcy niestandardowego](../custom-providers/overview.md)
