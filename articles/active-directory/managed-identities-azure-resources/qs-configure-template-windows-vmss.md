---
title: Konfigurowanie szablonu do używania tożsamości zarządzanych w zestawach skalowania maszyny wirtualnej — Usługa Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych dla zasobów platformy Azure w zestawie skalowania maszyny wirtualnej przy użyciu szablonu usługi Azure Resource Manager.
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
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d5e324ea20b2ea82fac5b5132893d3558bd3b41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425565"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure w skali maszyny wirtualnej platformy Azure przy użyciu szablonu

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie.

W tym artykule dowiesz się, jak wykonać następujące tożsamości zarządzane dla operacji zasobów platformy Azure na zestawie skalowania maszyny wirtualnej platformy Azure przy użyciu szablonu wdrożenia usługi Azure Resource Manager:
- Włączanie i wyłączanie tożsamości zarządzanej przypisanej przez system w zestawie skalowania maszyny wirtualnej platformy Azure
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika w zestawie skalowania maszyny wirtualnej platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md). **Pamiętaj, aby przejrzeć [różnicę między tożsamością zarządzaną przypisaną do systemu a przypisaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby wykonać operacje zarządzania w tym artykule, twoje konto wymaga następujących przypisań kontroli dostępu opartych na rolach platformy Azure:

    > [!NOTE]
    > Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

    - [Współautor maszyny wirtualnej,](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) aby utworzyć zestaw skalowania maszyny wirtualnej i włączyć i usunąć system i/lub przypisaną przez użytkownika tożsamość zarządzaną z zestawu skalowania maszyny wirtualnej.
    - [Rola współautora tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) w celu utworzenia tożsamości zarządzanej przypisanej przez użytkownika.
    - [Rola zarządzanego operatora tożsamości](/azure/role-based-access-control/built-in-roles#managed-identity-operator) do przypisywania i usuwania tożsamości zarządzanej przypisanej przez użytkownika z i do zestawu skalowania maszyny wirtualnej.

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Podobnie jak w witrynie Azure portal i skrypty, szablony [usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md) zapewniają możliwość wdrażania nowych lub zmodyfikowanych zasobów zdefiniowanych przez grupę zasobów platformy Azure. Dostępnych jest kilka opcji edycji i wdrażania szablonów, zarówno lokalnych, jak i opartych na portalu, w tym:

   - Korzystanie [z szablonu niestandardowego z portalu Azure Marketplace,](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)który umożliwia utworzenie szablonu od podstaw lub oparcie go na istniejącym szablonie wspólnym lub [szybki start.](https://azure.microsoft.com/documentation/templates/)
   - Wyprowadzając z istniejącej grupy zasobów, eksportując szablon z [oryginalnego wdrożenia](../../azure-resource-manager/templates/export-template-portal.md)lub z [bieżącego stanu wdrożenia](../../azure-resource-manager/templates/export-template-portal.md).
   - Za pomocą lokalnego [edytora JSON (na przykład VS Code),](../../azure-resource-manager/resource-manager-create-first-template.md)a następnie przekazywania i wdrażania przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
   - Za pomocą [projektu](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) Visual Studio Azure Resource Group zarówno do tworzenia i wdrażania szablonu.  

Niezależnie od opcji, którą wybierzesz, składnia szablonu jest taka sama podczas początkowego wdrażania i ponownego wdrażania. Włączanie tożsamości zarządzanych dla zasobów platformy Azure na nowej lub istniejącej maszynie wirtualnej odbywa się w ten sam sposób. Ponadto domyślnie usługa Azure Resource Manager wykonuje [przyrostową aktualizację](../../azure-resource-manager/templates/deployment-modes.md) do wdrożeń.

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana do systemu

W tej sekcji włączysz i wyłączysz przypisaną do systemu tożsamość zarządzaną przy użyciu szablonu usługi Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>Włączanie tożsamości zarządzanej przypisanej przez system podczas tworzenia zestawu skalowania maszyn wirtualnych lub istniejącego zestawu skalowania maszyny wirtualnej

1. Niezależnie od tego, czy logujesz się do platformy Azure lokalnie, czy za pośrednictwem witryny Azure Portal, użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skalowania maszyny wirtualnej.
2. Aby włączyć tożsamość zarządzaną przypisaną do systemu, należy `Microsoft.Compute/virtualMachinesScaleSets` załadować szablon do edytora, zlokalizować zasób zainteresowania w sekcji zasobów i dodać `identity` właściwość na tym samym poziomie co `"type": "Microsoft.Compute/virtualMachinesScaleSets"` właściwość. Użyj następującej składni:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> Opcjonalnie można aprowizować tożsamości zarządzane dla zasobów platformy Azure rozszerzenie `extensionProfile` zestawu skalowania maszyny wirtualnej, określając go w elemencie szablonu. Ten krok jest opcjonalny, ponieważ można użyć punktu końcowego tożsamości usługi imds (Azure Instance Metadata Service), aby pobrać tokeny.  Aby uzyskać więcej informacji, zobacz [Migrowanie z rozszerzenia maszyny Wirtualnej do usługi Azure IMDS w celu uwierzytelnienia](howto-migrate-vm-extension.md).


4. Po zakończeniu następujące sekcje powinny zostać dodane do sekcji zasobów szablonu i powinny wyglądać następująco:

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłączanie tożsamości zarządzanej przypisanej przez system z zestawu skalowania maszyny wirtualnej platformy Azure

Jeśli masz zestaw skalowania maszyny wirtualnej, który nie wymaga już tożsamości zarządzanej przypisanej do systemu:

1. Niezależnie od tego, czy logujesz się do platformy Azure lokalnie, czy za pośrednictwem witryny Azure Portal, użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skalowania maszyny wirtualnej.

2. Załaduj szablon do `Microsoft.Compute/virtualMachineScaleSets` [edytora](#azure-resource-manager-templates) i `resources` znajdź zasób zainteresowania w sekcji. Jeśli masz maszynę wirtualną, która ma tylko tożsamość zarządzaną przypisaną `None`do systemu, możesz ją wyłączyć, zmieniając typ tożsamości na .

   **Interfejs API Microsoft.Compute/virtualMachineScaleSets w wersji 2018-06-01**

   Jeśli apiVersion `2018-06-01` jest i maszyna wirtualna ma zarówno system, jak `SystemAssigned` i tożsamości zarządzane `UserAssigned` przypisane przez użytkownika, usuń z typu tożsamości i zachowaj wraz z wartościami słownika userAssignedIdentities.

   **Interfejs API Microsoft.Compute/virtualMachineScaleSets w wersji 2018-06-01**

   Jeśli apiVersion `2017-12-01` jest i zestaw skalowania maszyny wirtualnej ma zarówno system, jak i tożsamości zarządzane przypisane przez użytkownika, usuń `SystemAssigned` z typu tożsamości i zachowaj `UserAssigned` wraz z `identityIds` tablicą tożsamości zarządzanych przypisanych przez użytkownika.



   W poniższym przykładzie pokazano, jak usunąć tożsamość zarządzaną przypisaną do systemu z zestawu skalowania maszyny wirtualnej bez tożsamości zarządzanych przypisanych przez użytkownika:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji przypisano tożsamość zarządzaną przypisaną przez użytkownika do zestawu skalowania maszyny wirtualnej przy użyciu szablonu usługi Azure Resource Manager.

> [!Note]
> Aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika przy użyciu szablonu usługi Azure Resource Manager, zobacz [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do zestawu skalowania maszyny wirtualnej

1. W `resources` obszarze elementu dodaj następujący wpis, aby przypisać tożsamość zarządzaną przypisaną przez użytkownika do zestawu skalowania maszyny wirtualnej.  Pamiętaj, aby `<USERASSIGNEDIDENTITY>` zastąpić nazwą utworzonej tożsamości zarządzanej przypisanej przez użytkownika.

   **Interfejs API Microsoft.Compute/virtualMachineScaleSets w wersji 2018-06-01**

   Jeśli apiVersion `2018-06-01`jest , tożsamości zarządzane przypisane przez `userAssignedIdentities` użytkownika są przechowywane `<USERASSIGNEDIDENTITYNAME>` w formacie słownika i `variables` wartość musi być przechowywana w zmiennej zdefiniowanej w sekcji szablonu.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }

   }
   ```   

   **Interfejs API Microsoft.Compute/virtualMachineScaleSets w wersji 2017-12-01**

   Jeśli `apiVersion` jest `2017-12-01` lub wcześniej, tożsamości zarządzane przypisane przez `identityIds` użytkownika są `<USERASSIGNEDIDENTITYNAME>` przechowywane w tablicy, a wartość musi być przechowywana w zmiennej zdefiniowanej w sekcji zmiennych szablonu.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ```
> [!NOTE]
> Opcjonalnie można aprowizować tożsamości zarządzane dla zasobów platformy Azure rozszerzenie `extensionProfile` zestawu skalowania maszyny wirtualnej, określając go w elemencie szablonu. Ten krok jest opcjonalny, ponieważ można użyć punktu końcowego tożsamości usługi imds (Azure Instance Metadata Service), aby pobrać tokeny.  Aby uzyskać więcej informacji, zobacz [Migrowanie z rozszerzenia maszyny Wirtualnej do usługi Azure IMDS w celu uwierzytelnienia](howto-migrate-vm-extension.md).

3. Po zakończeniu szablon powinien wyglądać podobnie do następującego:

   **Interfejs API Microsoft.Compute/virtualMachineScaleSets w wersji 2018-06-01**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```

   **Interfejs API microsoft.compute/virtualMachines w wersji 2017-12-01**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)    
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z zestawu skalowania maszyny wirtualnej platformy Azure

Jeśli masz zestaw skalowania maszyny wirtualnej, który nie wymaga już tożsamości zarządzanej przypisanej przez użytkownika:

1. Niezależnie od tego, czy logujesz się do platformy Azure lokalnie, czy za pośrednictwem witryny Azure Portal, użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skalowania maszyny wirtualnej.

2. Załaduj szablon do `Microsoft.Compute/virtualMachineScaleSets` [edytora](#azure-resource-manager-templates) i `resources` znajdź zasób zainteresowania w sekcji. Jeśli masz zestaw skalowania maszyny wirtualnej, który ma tylko przypisaną przez użytkownika `None`tożsamość zarządzaną, możesz ją wyłączyć, zmieniając typ tożsamości na .

   Poniższy przykład pokazuje, jak usunąć wszystkie tożsamości zarządzane przypisane przez użytkownika z maszyny Wirtualnej bez tożsamości zarządzanych przypisanych do systemu:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```

   **Interfejs API Microsoft.Compute/virtualMachineScaleSets w wersji 2018-06-01**

   Aby usunąć pojedynczą tożsamość zarządzaną przypisaną przez użytkownika z `userAssignedIdentities` zestawu skalowania maszyny wirtualnej, usuń ją ze słownika.

   Jeśli masz tożsamość przypisaną do systemu, zachowaj ją w `type` wartości pod wartością. `identity`

   **Interfejs API Microsoft.Compute/virtualMachineScaleSets w wersji 2017-12-01**

   Aby usunąć pojedynczą tożsamość zarządzaną przypisaną przez użytkownika z `identityIds` zestawu skalowania maszyny wirtualnej, usuń ją z tablicy.

   Jeśli masz przypisaną do systemu tożsamość zarządzaną, `type` zachowaj `identity` ją w wartości pod wartością.

## <a name="next-steps"></a>Następne kroki

- [Zarządzane tożsamości dla zasobów platformy Azure omówienie](overview.md).
