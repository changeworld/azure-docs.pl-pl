---
title: Jak skonfigurować zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu szablonu
description: Instrukcje krok po kroku dotyczące konfigurowania zarządzania tożsamościami dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure, przy użyciu szablonu usługi Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cb96f4aaef461d049ca496780d542ad7db229e2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60307764"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-a-templates"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu szablonów

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule przy użyciu szablonu wdrożenia usługi Azure Resource Manager, nauczysz się wykonywać następujące zarządzanych tożsamości dla operacji tworzenia zasobów platformy Azure w Maszynie wirtualnej platformy Azure:

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli znasz przy użyciu szablonu wdrożenia usługi Azure Resource Manager, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnicę między przypisana przez system i przypisanych przez użytkownika tożsamości zarządzanej](overview.md#how-does-it-work)** .
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Podobnie jak w witrynie Azure portal i skryptów, [usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) Szablony zapewniają możliwość wdrażania nowych lub zmodyfikowanych zasobów zdefiniowany przez grupę zasobów platformy Azure. Jest dostępnych kilka opcji edycję szablonu i wdrażania, zarówno lokalnych, jak i oparte na portalu, w tym:

   - Za pomocą [niestandardowego szablonu w portalu Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), co pozwala na Utwórz szablon od podstaw albo oparte na typowych istniejących lub [szablon szybkiego startu](https://azure.microsoft.com/documentation/templates/).
   - Wyprowadzanie z istniejącej grupy zasobów przez wyeksportowanie szablonu z poziomu [oryginalnego wdrożenia](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates), lub z [bieżący stan wdrożenia](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).
   - Za pomocą lokalnego [edytora JSON (np. programu VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), a następnie przekazywanie i wdrażanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
   - Za pomocą programu Visual Studio [projekt grupy zasobów platformy Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) Aby utworzyć i wdrożyć szablon.  

Niezależnie od wybranej opcji składni szablonu jest taka sama podczas początkowego wdrażania i ponownego wdrażania. Włączanie system lub użytkownik przypisany tożsamość zarządzaną w nowej lub istniejącej maszyny Wirtualnej z systemem odbywa się w taki sam sposób. Ponadto domyślnie usługi Azure Resource Manager jest [aktualizacji przyrostowej](../../azure-resource-manager/deployment-modes.md) do wdrożeń.

## <a name="system-assigned-managed-identity"></a>Przypisana przez system tożsamości zarządzanej

W tej sekcji można będzie włączyć i wyłączyć przypisany systemowo tożsamości zarządzanej przy użyciu szablonu usługi Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Włącz przypisany systemowo tożsamości zarządzanej podczas tworzenia maszyny Wirtualnej platformy Azure lub z istniejącej maszyny Wirtualnej

Aby włączyć przypisany systemowo tożsamości zarządzanej maszyny wirtualnej, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) przypisania roli.  Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

1. Czy logowanie do platformy Azure, lokalnie lub w witrynie Azure portal, należy użyć konta skojarzonego z subskrypcją platformy Azure, która zawiera maszyny Wirtualnej.

2. Aby włączyć tożsamość zarządzaną przypisana przez system, należy załadować szablon do edytora, odszukaj `Microsoft.Compute/virtualMachines` zasobów zainteresowania w ramach `resources` sekcji i Dodaj `"identity"` właściwości w tym samym poziomie co `"type": "Microsoft.Compute/virtualMachines"` właściwości. Należy użyć następującej składni:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   },
   ```

> [!NOTE]
> Opcjonalnie aprowizujesz zarządzanych tożsamości dla rozszerzenia maszyny Wirtualnej zasoby platformy Azure, określając ją jako `resources` elementu w szablonie. Ten krok jest opcjonalny, zgodnie z punktu końcowego tożsamości Azure wystąpienie metadanych usługi (IMDS), można użyć do pobierania tokenów, jak również.  Aby uzyskać więcej informacji, zobacz [migracja z rozszerzenia maszyny Wirtualnej do IMDS platformy Azure do uwierzytelniania](howto-migrate-vm-extension.md).

3. Gdy wszystko będzie gotowe, sekcje powinna być dodana do `resource` części szablonu i powinny wyglądać podobnie do poniższego:

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
        
            //The following appears only if you provisioned the optional VM extension (to be deprecated)
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
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Przypisywanie roli maszyny Wirtualnej przypisany systemowo tożsamości zarządzanej

Po włączeniu tożsamości zarządzanej przypisana przez system na maszynie Wirtualnej, możesz chcieć przyznania roli takich jak **czytnika** dostęp do grupy zasobów, w której został utworzony.

Aby przypisać rolę do maszyny Wirtualnej tożsamości przypisanych przez system, konto usługi musi [Administrator dostępu użytkowników](/azure/role-based-access-control/built-in-roles#user-access-administrator) przypisania roli.

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

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Wyłącz przypisany systemowo tożsamości zarządzanej maszyny wirtualnej platformy Azure

Aby usunąć przypisany systemowo tożsamości zarządzanej maszyny Wirtualnej, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) przypisania roli.  Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

1. Czy logowanie do platformy Azure, lokalnie lub w witrynie Azure portal, należy użyć konta skojarzonego z subskrypcją platformy Azure, która zawiera maszyny Wirtualnej.

2. Ładowanie szablonu do [edytora](#azure-resource-manager-templates) i Znajdź `Microsoft.Compute/virtualMachines` zasobów zainteresowania w ramach `resources` sekcji. Jeśli masz maszynę Wirtualną, która ma tylko przypisana przez system tożsamości zarządzanej, można ją wyłączyć, zmieniając typ tożsamości do `None`.  
   
   **Microsoft.Compute/virtualMachines interfejsu API w wersji 2018-06-01**

   Jeśli maszyna wirtualna ma system i zarządzanych tożsamości przypisanych przez użytkownika, Usuń `SystemAssigned` z typu tożsamości i zachować `UserAssigned` wraz z `userAssignedIdentities` wartości w słowniku.

   **Microsoft.Compute/virtualMachines interfejsu API w wersji 2018-06-01**
   
   Jeśli Twoje `apiVersion` jest `2017-12-01` i maszyna wirtualna ma systemowych i tożsamości przypisanych przez użytkownika zarządzanego, Usuń `SystemAssigned` z typu tożsamości i zachować `UserAssigned` wraz z `identityIds` tablicę użytkownik przypisany zarządzanych tożsamości.  
   
Poniższy przykład pokazuje, jak usunąć przypisany systemowo tożsamości zarządzanej maszyny wirtualnej za pomocą tożsamości zarządzanych nie przypisanych przez użytkownika:

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

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji należy przypisać tożsamości zarządzanej przypisanych przez użytkownika na Maszynie wirtualnej platformy Azure przy użyciu szablonu usługi Azure Resource Manager.

> [!Note]
> Aby utworzyć przypisanych przez użytkownika tożsamości zarządzanej przy użyciu szablonu usługi Azure Resource Manager, zobacz [tworzenie zarządzanych tożsamości przypisanych przez użytkownika](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Przypisz tożsamości zarządzanej przypisanych przez użytkownika na Maszynie wirtualnej platformy Azure

Aby przypisać tożsamości przypisanych przez użytkownika do maszyny Wirtualnej, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) i [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) przypisań ról. Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

1. W obszarze `resources` elementu, Dodaj następujący wpis do przypisywania tożsamości zarządzanej użytkownik przypisany do maszyny Wirtualnej.  Koniecznie Zastąp `<USERASSIGNEDIDENTITY>` o nazwie użytkownik przypisany zarządzanych tożsamości został utworzony.

   **Microsoft.Compute/virtualMachines interfejsu API w wersji 2018-06-01**

   Jeśli Twoje `apiVersion` jest `2018-06-01`, zarządzanych tożsamości przypisanych przez użytkownika są przechowywane w `userAssignedIdentities` format słownika i `<USERASSIGNEDIDENTITYNAME>` wartość musi być przechowywany w zmiennej zdefiniowanej w `variables` części szablonu.

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
   
   **Microsoft.Compute/virtualMachines interfejsu API w wersji 2017-12-01**
    
   Jeśli Twoje `apiVersion` jest `2017-12-01`, zarządzanych tożsamości przypisanych przez użytkownika są przechowywane w `identityIds` tablicy i `<USERASSIGNEDIDENTITYNAME>` wartość musi być przechowywany w zmiennej zdefiniowanej w `variables` części szablonu.
    
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
        //The following appears only if you provisioned the optional VM extension (to be deprecated)                  
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
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
   **Microsoft.Compute/virtualMachines interfejsu API w wersji 2017-12-01**
   
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
                 
        //The following appears only if you provisioned the optional VM extension (to be deprecated)                   
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Usuń przypisanych przez użytkownika tożsamości zarządzanej maszyny wirtualnej platformy Azure

Aby usunąć tożsamości przypisanych przez użytkownika z maszyny Wirtualnej, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) przypisania roli. Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

1. Czy logowanie do platformy Azure, lokalnie lub w witrynie Azure portal, należy użyć konta skojarzonego z subskrypcją platformy Azure, która zawiera maszyny Wirtualnej.

2. Ładowanie szablonu do [edytora](#azure-resource-manager-templates) i Znajdź `Microsoft.Compute/virtualMachines` zasobów zainteresowania w ramach `resources` sekcji. Jeśli masz maszynę Wirtualną, która zawiera tylko przypisanych przez użytkownika z tożsamości zarządzanej, można ją wyłączyć, zmieniając typ tożsamości do `None`.
 
   Poniższy przykład pokazuje, jak usunąć wszystkich przypisanych do użytkowników zarządzanych tożsamości z maszyny Wirtualnej za pomocą tożsamości zarządzanych nie przypisanych systemu:
   
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
   
   **Microsoft.Compute/virtualMachines interfejsu API w wersji 2018-06-01**
    
   Aby usunąć pojedynczy tożsamość zarządzaną przypisanych przez użytkownika z maszyny Wirtualnej, usuń go z `useraAssignedIdentities` słownika.

   Jeśli masz przypisaną przez system tożsamości zarządzanej, przechowuj je w w `type` wartości w obszarze `identity` wartość.
 
   **Microsoft.Compute/virtualMachines interfejsu API w wersji 2017-12-01**

   Aby usunąć pojedynczy tożsamość zarządzaną przypisanych przez użytkownika z maszyny Wirtualnej, usuń go z `identityIds` tablicy.

   Jeśli masz przypisaną przez system tożsamości zarządzanej, przechowuj je w w `type` wartości w obszarze `identity` wartość.
   
## <a name="next-steps"></a>Kolejne kroki

- [Zarządzane tożsamości dla zasobów platformy Azure — omówienie](overview.md).

