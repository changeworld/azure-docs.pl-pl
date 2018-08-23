---
title: Konfigurowanie tożsamości usługi zarządzanej na Azure zestawu skalowania maszyn wirtualnych przy użyciu szablonu
description: Szczegółowe instrukcje dotyczące konfigurowania tożsamości usługi zarządzanej w usłudze VMSS platformy Azure, przy użyciu szablonu usługi Azure Resource Manager.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 68304b3e5eea50aba28f46344abcbd7ad060c5c8
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055198"
---
# <a name="configure-managed-service-identity-on-virtual-machine-scale-using-a-template"></a>Konfigurowanie tożsamości usługi zarządzanej w skali maszyny wirtualnej przy użyciu szablonu

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wykonywać następujące operacje tożsamości usługi zarządzanej w zestawie skalowania maszyn wirtualnych platformy Azure przy użyciu szablonu wdrożenia usługi Azure Resource Manager:
- Włączanie i wyłączanie tożsamości przypisanej w systemie w zestawie skalowania maszyn wirtualnych platformy Azure
- Dodawanie i usuwanie tożsamości przypisanych przez użytkownika w zestawie skalowania maszyn wirtualnych platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między przypisanej w systemie i tożsamości przypisanych przez użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujących przypisań ról:
    - [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) Aby utworzyć zestaw skalowania maszyn wirtualnych zestawu i Włącz i Usuń systemu i/lub użytkownik przypisany tożsamość zarządzaną z zestawu skalowania maszyn wirtualnych.
    - [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roli do utworzenia tożsamości przypisanych przez użytkownika.
    - [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli przypisywania i usuwania tożsamości przypisanych przez użytkownika z i do zestawu skalowania maszyn wirtualnych.

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Podobnie jak w witrynie Azure portal i skryptów, [usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) Szablony zapewniają możliwość wdrażania nowych lub zmodyfikowanych zasobów zdefiniowany przez grupę zasobów platformy Azure. Jest dostępnych kilka opcji edycję szablonu i wdrażania, zarówno lokalnych, jak i oparte na portalu, w tym:

   - Za pomocą [niestandardowego szablonu w portalu Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), co pozwala na Utwórz szablon od podstaw albo oparte na typowych istniejących lub [szablon szybkiego startu](https://azure.microsoft.com/documentation/templates/).
   - Wyprowadzanie z istniejącej grupy zasobów przez wyeksportowanie szablonu z poziomu [oryginalnego wdrożenia](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), lub z [bieżący stan wdrożenia](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Za pomocą lokalnego [edytora JSON (np. programu VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), a następnie przekazywanie i wdrażanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
   - Za pomocą programu Visual Studio [projekt grupy zasobów platformy Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) Aby utworzyć i wdrożyć szablon.  

Niezależnie od wybranej opcji składni szablonu jest taka sama podczas początkowego wdrażania i ponownego wdrażania. Włączanie tożsamości usługi zarządzanej w nowej lub istniejącej maszyny Wirtualnej z systemem odbywa się w taki sam sposób. Ponadto domyślnie usługi Azure Resource Manager jest [aktualizacji przyrostowej](../../azure-resource-manager/deployment-modes.md) do wdrożeń.

## <a name="system-assigned-identity"></a>Tożsamości przypisanej w systemie

W tej sekcji zostanie włączony i Wyłącz system przypisane do tożsamości przy użyciu szablonu usługi Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-a-existing-virtual-machine-scale-set"></a>Włączanie tożsamości przypisanej w systemie podczas tworzenia tworzenia zestawu skalowania maszyn wirtualnych lub istniejącego zestawu skalowania maszyn wirtualnych

1. Czy możesz się zarejestrować na platformę Azure lokalnie lub w witrynie Azure portal, należy użyć konta skojarzonego z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych.
   
2. Aby włączyć tożsamości przypisanej w systemie, załadować szablon do edytora, zlokalizuj `Microsoft.Compute/virtualMachinesScaleSets` zasobów zainteresowania w ramach zasobów sekcji i Dodaj `identity` właściwości w tym samym poziomie co `"type": "Microsoft.Compute/virtualMachines"` właściwości. Należy użyć następującej składni:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   }
   ```

3. (Opcjonalnie) Dodaj rozszerzenie tożsamości usługi zarządzanej jako zestawu skalowania maszyn wirtualnych `extensionsProfile` elementu. Ten krok jest opcjonalny, zgodnie z tożsamości usługi Azure wystąpienie metadanych usługi (IMDS), można użyć do pobierania tokenów, jak również.  Należy użyć następującej składni:

   >[!NOTE] 
   > W poniższym przykładzie założono, rozszerzenie zestawu skalowania maszyn wirtualnych z systemem Windows (`ManagedIdentityExtensionForWindows`) jest wdrażany. Można również skonfigurować dla systemu Linux przy użyciu `ManagedIdentityExtensionForLinux` katalog wirtualny wskazywał na `"name"` i `"type"` elementów.
   >

   ```json
   "extensionProfile": {
        "extensions": [
            {
                "name": "MSIWindowsExtension",
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

4. Gdy wszystko będzie gotowe, w poniższych sekcjach należy dodawać do sekcji zasobów szablonu i powinien wyglądać w następujący sposób:

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

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłączanie tożsamości przypisanej w systemie, z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli masz już zestaw skalowania maszyn wirtualnych wymaga tożsamości usługi zarządzanej:

1. Czy możesz się zarejestrować na platformę Azure lokalnie lub w witrynie Azure portal, należy użyć konta skojarzonego z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych.

2. Ładowanie szablonu do [edytora](#azure-resource-manager-templates) i Znajdź `Microsoft.Compute/virtualMachineScaleSets` zasobów zainteresowania w ramach `resources` sekcji. Jeśli masz maszynę Wirtualną, która zawiera tylko tożsamości przypisanej w systemie, można ją wyłączyć, zmieniając typ tożsamości do `None`.

   **Microsoft.Compute/virtualMachineScaleSets interfejsu API w wersji 2018-06-01**

   Jeśli Twoja wersja interfejsu API jest `2018-06-01` i maszyna wirtualna ma systemowych i tożsamości przypisanych przez użytkownika, Usuń `SystemAssigned` z typu tożsamości i zachować `UserAssigned` wraz z wartościami słownika userAssignedIdentities.

   **Microsoft.Compute/virtualMachineScaleSets interfejsu API w wersji 2018-06-01 i starszych wersji**

   Jeśli Twoja wersja interfejsu API jest `2017-12-01` i zestawu skalowania maszyn wirtualnych ma systemowych i tożsamości przypisanych przez użytkownika, Usuń `SystemAssigned` z typu tożsamości i zachować `UserAssigned` wraz z `identityIds` Tablica tożsamości przypisanych przez użytkownika. 
   
    

   Poniższy przykład pokazuje, jak usunąć system tożsamości przypisanej z maszyny wirtualnej zestawu skalowania przy użyciu tożsamości przypisanych przez żaden użytkownik nie:
   
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

## <a name="user-assigned-identity"></a>Tożsamości przypisanych przez użytkownika

W tej sekcji możesz przypisać użytkownika tożsamości przypisanej do maszyny wirtualnej zestawu skalowania przy użyciu szablonu usługi Azure Resource Manager.

> [!Note]
> Aby utworzyć tożsamości przypisanych przez użytkownika, przy użyciu szablonu usługi Azure Resource Manager, zobacz [tworzenie tożsamości przypisanych przez użytkownika](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Przypisywanie użytkownika tożsamości przypisanej do zestawu skalowania maszyn wirtualnych platformy Azure

1. W obszarze `resources` elementu, Dodaj następujący wpis do przypisywania tożsamości przypisanych przez użytkownika do zestawu skalowania maszyn wirtualnych.  Koniecznie Zastąp `<USERASSIGNEDIDENTITY>` o nazwie tożsamości przypisanych przez użytkownika został utworzony.
   
   **Microsoft.Compute/virtualMachineScaleSets interfejsu API w wersji 2018-06-01**

   Jeśli Twoja wersja interfejsu API jest `2018-06-01`, tożsamości przypisanych przez użytkownika są przechowywane w `userAssignedIdentities` format słownika i `<USERASSIGNEDIDENTITYNAME>` wartość musi być przechowywany w zmiennej zdefiniowanej w `variables` części szablonu.

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

   **Microsoft.Compute/virtualMachineScaleSets interfejsu API w wersji 2017-12-01**
    
   Jeśli Twoje `apiVersion` jest `2017-12-01` lub starszych tożsamości przypisanych przez użytkownika są przechowywane w `identityIds` tablicy i `<USERASSIGNEDIDENTITYNAME>` wartość musi być przechowywany w zmiennej zdefiniowanej w sekcji zmiennych szablonu.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ``` 

2. (Opcjonalnie) Dodaj następujący wpis w obszarze `extensionProfile` element, aby przypisać rozszerzenie tożsamość zarządzaną do Twojego zestawu skalowania maszyn wirtualnych. Ten krok jest opcjonalny, zgodnie z punktu końcowego tożsamości Azure wystąpienie metadanych usługi (IMDS), można użyć do pobierania tokenów, jak również. Należy użyć następującej składni:
   
    ```JSON
       "extensionProfile": {
            "extensions": [
                {
                    "name": "MSIWindowsExtension",
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

3. Gdy wszystko będzie gotowe, szablon powinien wyglądać podobnie do poniższej:
   
   **Microsoft.Compute/virtualMachineScaleSets interfejsu API w wersji 2018-06-01**   

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

   **Microsoft.Compute/virtualMachines interfejsu API w wersji 2017-12-01 wcześniej eand**

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
### <a name="remove-user-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Usuwanie tożsamości przypisanych przez użytkownika z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli masz już zestaw skalowania maszyn wirtualnych wymaga tożsamości usługi zarządzanej:

1. Czy możesz się zarejestrować na platformę Azure lokalnie lub w witrynie Azure portal, należy użyć konta skojarzonego z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych.

2. Ładowanie szablonu do [edytora](#azure-resource-manager-templates) i Znajdź `Microsoft.Compute/virtualMachineScaleSets` zasobów zainteresowania w ramach `resources` sekcji. Jeśli masz zestaw skalowania maszyn wirtualnych, który zawiera tylko tożsamości przypisanych przez użytkownika, można ją wyłączyć, zmieniając typ tożsamości do `None`.

   Poniższy przykład pokazuje, jak usunąć przypisane tożsamości z maszyny Wirtualnej przy użyciu tożsamości przypisanych przez system, nie do wszystkich użytkowników:

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
   
   **Microsoft.Compute/virtualMachineScaleSets interfejsu API w wersji 2018-06-01**
    
   Aby usunąć pojedynczej przypisane tożsamości użytkownika z zestawu skalowania maszyn wirtualnych, usuń go z `userAssignedIdentities` słownika.

   Jeśli masz tożsamości przypisanej w systemie, przechowuj je w w `type` wartości w obszarze `identity` wartość.

   **Microsoft.Compute/virtualMachineScaleSets interfejsu API w wersji 2017-12-01**

   Aby usunąć pojedynczej przypisane tożsamości użytkownika z zestawu skalowania maszyn wirtualnych, usuń go z `identityIds` tablicy.

   Jeśli masz tożsamości przypisanej w systemie, przechowuj je w w `type` wartości w obszarze `identity` wartość.
   
## <a name="next-steps"></a>Kolejne kroki

- Dla szerszej perspektywy o tożsamości usługi zarządzanej, przeczytaj [Przegląd tożsamości usługi zarządzanej](overview.md).

