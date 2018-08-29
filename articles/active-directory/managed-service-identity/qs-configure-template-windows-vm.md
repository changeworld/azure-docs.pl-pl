---
title: Konfigurowanie tożsamości usługi zarządzanej maszyny wirtualnej platformy Azure przy użyciu szablonu
description: Szczegółowe instrukcje dotyczące konfigurowania tożsamości usługi zarządzanej na Maszynie wirtualnej platformy Azure, przy użyciu szablonu usługi Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 6a5f8fc126f9c94ce139b99c94936e01da8b4099
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126425"
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Konfigurowanie tożsamości usługi zarządzanej maszyny Wirtualnej przy użyciu szablonu

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wykonywać następujące operacje tożsamości usługi zarządzanej na Maszynie wirtualnej platformy Azure, przy użyciu szablonu wdrożenia usługi Azure Resource Manager:

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między przypisanej w systemie i tożsamości przypisanych przez użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujących przypisań ról:
    - [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) do tworzenia maszyny Wirtualnej i Włącz oraz usuwać systemowych i/lub użytkownik przypisany tożsamości zarządzanej maszyny wirtualnej platformy Azure.
    - [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roli do utworzenia tożsamości przypisanych przez użytkownika.
    - [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli przypisywania i usuwania tożsamości przypisanych przez użytkownika z i do maszyny Wirtualnej.

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Podobnie jak w witrynie Azure portal i skryptów, [usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) Szablony zapewniają możliwość wdrażania nowych lub zmodyfikowanych zasobów zdefiniowany przez grupę zasobów platformy Azure. Jest dostępnych kilka opcji edycję szablonu i wdrażania, zarówno lokalnych, jak i oparte na portalu, w tym:

   - Za pomocą [niestandardowego szablonu w portalu Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), co pozwala na Utwórz szablon od podstaw albo oparte na typowych istniejących lub [szablon szybkiego startu](https://azure.microsoft.com/documentation/templates/).
   - Wyprowadzanie z istniejącej grupy zasobów przez wyeksportowanie szablonu z poziomu [oryginalnego wdrożenia](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), lub z [bieżący stan wdrożenia](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Za pomocą lokalnego [edytora JSON (np. programu VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), a następnie przekazywanie i wdrażanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
   - Za pomocą programu Visual Studio [projekt grupy zasobów platformy Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) Aby utworzyć i wdrożyć szablon.  

Niezależnie od wybranej opcji składni szablonu jest taka sama podczas początkowego wdrażania i ponownego wdrażania. Włączanie system lub użytkownik, któremu przypisano tożsamość na nowej lub istniejącej maszyny Wirtualnej z systemem odbywa się w taki sam sposób. Ponadto domyślnie usługi Azure Resource Manager jest [aktualizacji przyrostowej](../../azure-resource-manager/deployment-modes.md) do wdrożeń.

## <a name="system-assigned-identity"></a>Tożsamości przypisanej w systemie

W tej sekcji zostanie włączony i Wyłącz system przypisane do tożsamości przy użyciu szablonu usługi Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Włącz system tożsamości przypisanej podczas tworzenia maszyny Wirtualnej platformy Azure lub z istniejącej maszyny Wirtualnej

1. Czy logowanie do platformy Azure, lokalnie lub w witrynie Azure portal, należy użyć konta skojarzonego z subskrypcją platformy Azure, która zawiera maszyny Wirtualnej.

2. Aby włączyć system tożsamości przypisanej, załadować szablon do edytora, odszukaj `Microsoft.Compute/virtualMachines` zasobów zainteresowania w ramach `resources` sekcji i Dodaj `"identity"` właściwości w tym samym poziomie co `"type": "Microsoft.Compute/virtualMachines"` właściwości. Należy użyć następującej składni:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   },
   ```

3. (Opcjonalnie) Dodaj rozszerzenie tożsamości usługi zarządzanej maszyny Wirtualnej jako `resources` elementu. Ten krok jest opcjonalny, zgodnie z punktu końcowego tożsamości Azure wystąpienie metadanych usługi (IMDS), można użyć do pobierania tokenów, jak również.  Należy użyć następującej składni:

   >[!NOTE] 
   > W poniższym przykładzie założono rozszerzenie maszyny Wirtualnej Windows (`ManagedIdentityExtensionForWindows`) jest wdrażany. Można również skonfigurować dla systemu Linux przy użyciu `ManagedIdentityExtensionForLinux` katalog wirtualny wskazywał na `"name"` i `"type"` elementów.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2018-06-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.ManagedIdentity",
           "type": "ManagedIdentityExtensionForWindows",
           "typeHandlerVersion": "1.0",
           "autoUpgradeMinorVersion": true,
           "settings": {
               "port": 50342
           },
           "protectedSettings": {}
       }
   }
   ```

4. Gdy wszystko będzie gotowe, sekcje powinna być dodana do `resource` części szablonu i powinny wyglądać podobnie do poniższego:

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                },
            },
            {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForLinux')]",
            "apiVersion": "2018-06-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
        }
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-identity"></a>Przypisywanie roli maszyny Wirtualnej tożsamości przypisanej w systemie

Po włączeniu tożsamości przypisanej w systemie na maszynie Wirtualnej, można przyznać jej rolę takich jak **czytnika** dostęp do grupy zasobów, w której został utworzony.

1. Czy logowanie do platformy Azure, lokalnie lub w witrynie Azure portal, należy użyć konta skojarzonego z subskrypcją platformy Azure, która zawiera maszyny Wirtualnej.
 
2. Załaduj szablon do [edytora](#azure-resource-manager-templates) i dodaj następujące informacje, aby nadać maszyny Wirtualnej **czytnika** dostęp do grupy zasobów, w której został utworzony.  Strukturę szablon będzie zależeć od edytora i modelu wdrażania, które wybierzesz.
   
   W obszarze `parameters` sekcji Dodaj następujący kod:

    ```JSON
    "builtInRoleType": {
          "type": "string",
          "defaultValue": "Reader"
        },
        "rbacGuid": {
          "type": "string"
        }
    ```

    W obszarze `variables` sekcji Dodaj następujący kod:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    W obszarze `resources` sekcji Dodaj następujący kod:

    ```JSON
    {
        "apiVersion": "2017-09-01",
         "type": "Microsoft.Authorization/roleAssignments",
         "name": "[parameters('rbacGuid')]",
         "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
                "scope": "[resourceGroup().id]"
          },
          "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ]
    }
    ```

### <a name="disable-a-system-assigned-identity-from-an-azure-vm"></a>Wyłącz system tożsamości przypisanej w Maszynie wirtualnej platformy Azure

Jeśli masz maszynę Wirtualną, która nie wymaga tożsamości usługi zarządzanej:

1. Czy logowanie do platformy Azure, lokalnie lub w witrynie Azure portal, należy użyć konta skojarzonego z subskrypcją platformy Azure, która zawiera maszyny Wirtualnej.

2. Ładowanie szablonu do [edytora](#azure-resource-manager-templates) i Znajdź `Microsoft.Compute/virtualMachines` zasobów zainteresowania w ramach `resources` sekcji. Jeśli masz maszynę Wirtualną, która zawiera tylko tożsamości przypisanej w systemie, można ją wyłączyć, zmieniając typ tożsamości do `None`.  
   
   **Microsoft.Compute/virtualMachines interfejsu API w wersji 2018-06-01**

   Jeśli maszyna wirtualna ma systemowych i tożsamości przypisanych przez użytkownika, Usuń `SystemAssigned` z typu tożsamości i zachować `UserAssigned` wraz z `userAssignedIdentities` wartości w słowniku.

   **Microsoft.Compute/virtualMachines interfejsu API w wersji 2018-06-01 i starszych wersji**
   
   Jeśli Twoje `apiVersion` jest `2017-12-01` i maszyna wirtualna ma systemowych i tożsamości przypisanych przez użytkownika, Usuń `SystemAssigned` z typu tożsamości i zachować `UserAssigned` wraz z `identityIds` Tablica tożsamości przypisanych przez użytkownika.  
   
Poniższy przykład pokazuje, jak usunąć tożsamości przypisanej z maszyny Wirtualnej bez użytkownika tożsamości przypisanych przez system:

```JSON
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[parameters('vmName')]",
    "location": "[resourceGroup().location]",
    "identity": { 
        "type": "None"
}
```

## <a name="user-assigned-identity"></a>Tożsamości przypisanych przez użytkownika

W tej sekcji należy przypisać tożsamości przypisanych przez użytkownika na Maszynie wirtualnej platformy Azure przy użyciu szablonu usługi Azure Resource Manager.

> [!Note]
> Aby utworzyć tożsamości przypisanych przez użytkownika, przy użyciu szablonu usługi Azure Resource Manager, zobacz [tworzenie tożsamości przypisanych przez użytkownika](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

 ### <a name="assign-a-user-assigned-identity-to-an-azure-vm"></a>Przypisywanie użytkownika tożsamości przypisanej do maszyny Wirtualnej platformy Azure

1. W obszarze `resources` elementu, Dodaj następujący wpis do przypisywania tożsamości przypisanych przez użytkownika do maszyny Wirtualnej.  Koniecznie Zastąp `<USERASSIGNEDIDENTITY>` o nazwie tożsamości przypisanych przez użytkownika został utworzony.

   **Microsoft.Compute/virtualMachines interfejsu API w wersji 2018-06-01**

   Jeśli Twoje `apiVersion` jest `2018-06-01`, tożsamości przypisanych przez użytkownika są przechowywane w `userAssignedIdentities` format słownika i `<USERASSIGNEDIDENTITYNAME>` wartość musi być przechowywany w zmiennej zdefiniowanej w `variables` części szablonu.

   ```json
   {
       "apiVersion": "2018-06-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
        }
   }
   ```
   
   **Microsoft.Compute/virtualMachines interfejsu API w wersji 2017-12-01 i starszych wersji**
    
   Jeśli Twoje `apiVersion` jest `2017-12-01`, tożsamości przypisanych przez użytkownika są przechowywane w `identityIds` tablicy i `<USERASSIGNEDIDENTITYNAME>` wartość musi być przechowywany w zmiennej zdefiniowanej w `variables` części szablonu.
    
   ```json
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```
       

2. (Opcjonalnie) Następnie w obszarze `resources` elementu, Dodaj następujący wpis, aby przypisać rozszerzenie tożsamości zarządzanej dla maszyny wirtualnej. Ten krok jest opcjonalny, zgodnie z punktu końcowego tożsamości Azure wystąpienie metadanych usługi (IMDS), można użyć do pobierania tokenów, jak również. Należy użyć następującej składni:
    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
3. Gdy wszystko będzie gotowe, sekcje powinna być dodana do `resource` części szablonu i powinny wyglądać podobnie do poniższego:
   
   **Microsoft.Compute/virtualMachines interfejsu API w wersji 2018-06-01**    

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForLinux')]",
            "apiVersion": "2018-06-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
            }
        }
       }
    ]
   ```
   **Microsoft.Compute/virtualMachines interfejsu API w wersji 2017-12-01 i starszych wersji**
   
   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForLinux')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
            }
        }
       }
    ]
   ```
    

### <a name="remove-user-assigned-identity-from-an-azure-vm"></a>Usuwanie tożsamości przypisanych przez użytkownika na Maszynie wirtualnej platformy Azure

Jeśli masz maszynę Wirtualną, która nie wymaga tożsamości usługi zarządzanej:

1. Czy logowanie do platformy Azure, lokalnie lub w witrynie Azure portal, należy użyć konta skojarzonego z subskrypcją platformy Azure, która zawiera maszyny Wirtualnej.

2. Ładowanie szablonu do [edytora](#azure-resource-manager-templates) i Znajdź `Microsoft.Compute/virtualMachines` zasobów zainteresowania w ramach `resources` sekcji. Jeśli masz maszynę Wirtualną, która zawiera tylko tożsamości przypisanych przez użytkownika, można ją wyłączyć, zmieniając typ tożsamości do `None`.
 
   Poniższy przykład pokazuje, jak usunąć przypisane tożsamości z maszyny Wirtualnej przy użyciu tożsamości przypisanych przez system, nie do wszystkich użytkowników:
   
   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
    }
   ```
   
   **Microsoft.Compute/virtualMachines interfejsu API w wersji 2018-06-01 i starszych wersji**
    
   Aby usunąć pojedynczej przypisane tożsamości użytkownika z maszyny Wirtualnej, usuń go z `useraAssignedIdentities` słownika.

   Jeśli masz tożsamości przypisanej w systemie, przechowuj je w w `type` wartości w obszarze `identity` wartość.
 
   **Microsoft.Compute/virtualMachines interfejsu API w wersji 2017-12-01**

   Aby usunąć pojedynczej przypisane tożsamości użytkownika z maszyny Wirtualnej, usuń go z `identityIds` tablicy.

   Jeśli masz tożsamości przypisanej w systemie, przechowuj je w w `type` wartości w obszarze `identity` wartość.
   
## <a name="related-content"></a>Powiązana zawartość

- Dla szerszej perspektywy o tożsamości usługi zarządzanej, przeczytaj [Przegląd tożsamości usługi zarządzanej](overview.md).

