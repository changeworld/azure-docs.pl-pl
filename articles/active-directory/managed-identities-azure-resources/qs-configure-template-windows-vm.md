---
title: Konfigurowanie tożsamości zarządzanych na maszynie Wirtualnej platformy Azure przy użyciu szablonu — Usługa Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu szablonu usługi Azure Resource Manager.
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
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5540697e8e64586d73e34d253fb95e549fc0301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972144"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-a-templates"></a>Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu szablonów

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie.

W tym artykule przy użyciu szablonu wdrażania usługi Azure Resource Manager, dowiedzieć się, jak wykonać następujące tożsamości zarządzane dla operacji zasobów platformy Azure na maszynie Wirtualnej platformy Azure:

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz szablonu wdrażania usługi Azure Resource Manager, zapoznaj się z [sekcją omówienia](overview.md). **Pamiętaj, aby przejrzeć [różnicę między tożsamością zarządzaną przypisaną do systemu a przypisaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Podobnie jak w witrynie Azure portal i skrypty, szablony [usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md) zapewniają możliwość wdrażania nowych lub zmodyfikowanych zasobów zdefiniowanych przez grupę zasobów platformy Azure. Dostępnych jest kilka opcji edycji i wdrażania szablonów, zarówno lokalnych, jak i opartych na portalu, w tym:

   - Korzystanie [z szablonu niestandardowego z portalu Azure Marketplace,](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)który umożliwia utworzenie szablonu od podstaw lub oparcie go na istniejącym szablonie wspólnym lub [szybki start.](https://azure.microsoft.com/documentation/templates/)
   - Wyprowadzając z istniejącej grupy zasobów, eksportując szablon z [oryginalnego wdrożenia](../../azure-resource-manager/templates/export-template-portal.md)lub z [bieżącego stanu wdrożenia](../../azure-resource-manager/templates/export-template-portal.md).
   - Za pomocą lokalnego [edytora JSON (na przykład VS Code),](../../azure-resource-manager/resource-manager-create-first-template.md)a następnie przekazywania i wdrażania przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
   - Za pomocą [projektu](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) Visual Studio Azure Resource Group zarówno do tworzenia i wdrażania szablonu.  

Niezależnie od opcji, którą wybierzesz, składnia szablonu jest taka sama podczas początkowego wdrażania i ponownego wdrażania. Włączenie tożsamości zarządzanej przypisanej przez system lub użytkownika na nowej lub istniejącej maszynie wirtualnej odbywa się w ten sam sposób. Ponadto domyślnie usługa Azure Resource Manager wykonuje [przyrostową aktualizację](../../azure-resource-manager/templates/deployment-modes.md) do wdrożeń.

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana do systemu

W tej sekcji włączysz i wyłączysz tożsamość zarządzaną przypisaną do systemu przy użyciu szablonu usługi Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Włączanie tożsamości zarządzanej przypisanej do systemu podczas tworzenia maszyny wirtualnej platformy Azure lub na istniejącej maszynie wirtualnej

Aby włączyć tożsamość zarządzaną przypisaną do systemu na maszynie wirtualnej, twoje konto wymaga przypisania roli [współautora maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Niezależnie od tego, czy logujesz się do platformy Azure lokalnie, czy za pośrednictwem witryny Azure portal, użyj konta skojarzonego z subskrypcją platformy Azure zawierającą maszynę wirtualną.

2. Aby włączyć tożsamość zarządzaną przypisaną do systemu, `Microsoft.Compute/virtualMachines` załaduj `resources` szablon do `"identity"` edytora, zlokalizuj zasób zainteresowania w sekcji i dodaj właściwość na tym samym poziomie co `"type": "Microsoft.Compute/virtualMachines"` właściwość. Użyj następującej składni:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   },
   ```



3. Po zakończeniu do `resource` sekcji szablonu należy dodać następujące sekcje i powinny one wyglądać następująco:

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

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Przypisywanie roli tożsamości zarządzanej przypisanej przez system maszyny Wirtualnej

Po włączeniu tożsamości zarządzanej przypisanej przez system na maszynie wirtualnej można udzielić jej roli, takiej jak dostęp **czytelnika** do grupy zasobów, w której została utworzona.

Aby przypisać rolę do tożsamości przypisanej systemowi maszyny Wirtualnej, twoje konto wymaga przypisania roli [Administrator dostępu użytkownika.](/azure/role-based-access-control/built-in-roles#user-access-administrator)

1. Niezależnie od tego, czy logujesz się do platformy Azure lokalnie, czy za pośrednictwem witryny Azure portal, użyj konta skojarzonego z subskrypcją platformy Azure zawierającą maszynę wirtualną.

2. Załaduj szablon do [edytora](#azure-resource-manager-templates) i dodaj następujące informacje, aby dać **programowi** VM Reader dostęp do grupy zasobów, w której został utworzony.  Struktura szablonu może się różnić w zależności od edytora i wybranego modelu wdrażania.

   W `parameters` sekcji dodaj następujące elementy:

    ```JSON
    "builtInRoleType": {
        "type": "string",
        "defaultValue": "Reader"
    },
    "rbacGuid": {
        "type": "string"
    }
    ```

    W `variables` sekcji dodaj następujące elementy:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    W `resources` sekcji dodaj następujące elementy:

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

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Wyłączanie tożsamości zarządzanej przypisanej przez system z maszyny Wirtualnej platformy Azure

Aby usunąć tożsamość zarządzaną przypisaną do systemu z maszyny wirtualnej, twoje konto wymaga przypisania roli [Współautor maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Niezależnie od tego, czy logujesz się do platformy Azure lokalnie, czy za pośrednictwem witryny Azure portal, użyj konta skojarzonego z subskrypcją platformy Azure zawierającą maszynę wirtualną.

2. Załaduj szablon do `Microsoft.Compute/virtualMachines` [edytora](#azure-resource-manager-templates) i `resources` znajdź zasób zainteresowania w sekcji. Jeśli masz maszynę wirtualną, która ma tylko tożsamość zarządzaną przypisaną `None`do systemu, możesz ją wyłączyć, zmieniając typ tożsamości na .  

   **Interfejs API microsoft.compute/virtualMachines w wersji 2018-06-01**

   Jeśli maszyna wirtualna ma zarówno tożsamości zarządzane przypisane `SystemAssigned` przez system, `UserAssigned` jak i `userAssignedIdentities` przez użytkownika, usuń z typu tożsamości i zachowaj wraz z wartościami słownika.

   **Interfejs API microsoft.compute/virtualMachines w wersji 2018-06-01**

   Jeśli `apiVersion` twoja `2017-12-01` maszyna wirtualna ma zarówno tożsamości zarządzane przypisane `SystemAssigned` przez system, jak `UserAssigned` i `identityIds` przez użytkownika, usuń z typu tożsamości i zachowaj wraz z tablicą tożsamości zarządzanych przypisanych przez użytkownika.  

Poniższy przykład pokazuje, jak usunąć tożsamości zarządzanej przypisane przez system z maszyny Wirtualnej bez tożsamości zarządzanych przypisanych przez użytkownika:

 ```JSON
 {
     "apiVersion": "2018-06-01",
     "type": "Microsoft.Compute/virtualMachines",
     "name": "[parameters('vmName')]",
     "location": "[resourceGroup().location]",
     "identity": {
         "type": "None"
     }
 }
 ```

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji można przypisać tożsamość zarządzaną przypisaną przez użytkownika do maszyny Wirtualnej platformy Azure przy użyciu szablonu usługi Azure Resource Manager.

> [!Note]
> Aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika przy użyciu szablonu usługi Azure Resource Manager, zobacz [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do maszyny Wirtualnej platformy Azure

Aby przypisać tożsamość przypisaną przez użytkownika do maszyny Wirtualnej, twoje konto wymaga przypisania roli [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) i [Operator tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. W `resources` obszarze elementu dodaj następujący wpis, aby przypisać tożsamość zarządzaną przypisaną przez użytkownika do maszyny Wirtualnej.  Pamiętaj, aby `<USERASSIGNEDIDENTITY>` zastąpić nazwą utworzonej tożsamości zarządzanej przypisanej przez użytkownika.

   **Interfejs API microsoft.compute/virtualMachines w wersji 2018-06-01**

   Jeśli `apiVersion` `2018-06-01`jest, tożsamości zarządzane przypisane przez użytkownika `userAssignedIdentities` są przechowywane w `<USERASSIGNEDIDENTITYNAME>` formacie słownika, a wartość `variables` musi być przechowywana w zmiennej zdefiniowanej w sekcji szablonu.

   ```JSON
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

   **Interfejs API microsoft.compute/virtualMachines w wersji 2017-12-01**

   Jeśli `apiVersion` `2017-12-01`jest, tożsamości zarządzane przypisane przez użytkownika `identityIds` są przechowywane `<USERASSIGNEDIDENTITYNAME>` w tablicy, a wartość `variables` musi być przechowywana w zmiennej zdefiniowanej w sekcji szablonu.

   ```JSON
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

3. Po zakończeniu do `resource` sekcji szablonu należy dodać następujące sekcje i powinny one wyglądać następująco:

   **Interfejs API microsoft.compute/virtualMachines w wersji 2018-06-01**    

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
   **Interfejs API microsoft.compute/virtualMachines w wersji 2017-12-01**

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z maszyny Wirtualnej platformy Azure

Aby usunąć tożsamość przypisaną przez użytkownika z maszyny Wirtualnej, twoje konto wymaga przypisania roli [Współautor maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Niezależnie od tego, czy logujesz się do platformy Azure lokalnie, czy za pośrednictwem witryny Azure portal, użyj konta skojarzonego z subskrypcją platformy Azure zawierającą maszynę wirtualną.

2. Załaduj szablon do `Microsoft.Compute/virtualMachines` [edytora](#azure-resource-manager-templates) i `resources` znajdź zasób zainteresowania w sekcji. Jeśli masz maszynę wirtualną, która ma tylko przypisaną przez użytkownika tożsamość `None`zarządzaną, możesz ją wyłączyć, zmieniając typ tożsamości na .

   Poniższy przykład pokazuje, jak usunąć wszystkie tożsamości zarządzane przypisane przez użytkownika z maszyny Wirtualnej bez tożsamości zarządzanych przypisanych do systemu:

   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": {
          "type": "None"
          },
    }
   ```

   **Interfejs API microsoft.compute/virtualMachines w wersji 2018-06-01**

   Aby usunąć pojedynczą tożsamość zarządzaną przypisaną przez użytkownika `useraAssignedIdentities` z maszyny Wirtualnej, usuń ją ze słownika.

   Jeśli masz przypisaną do systemu tożsamość zarządzaną, `type` zachowaj `identity` ją w wartości pod wartością.

   **Interfejs API microsoft.compute/virtualMachines w wersji 2017-12-01**

   Aby usunąć pojedynczą tożsamość zarządzaną przypisaną przez użytkownika `identityIds` z maszyny Wirtualnej, usuń ją z tablicy.

   Jeśli masz przypisaną do systemu tożsamość zarządzaną, `type` zachowaj `identity` ją w wartości pod wartością.

## <a name="next-steps"></a>Następne kroki

- [Zarządzane tożsamości dla zasobów platformy Azure omówienie](overview.md).
