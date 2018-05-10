---
title: Jak skonfigurować MSI w maszynie Wirtualnej platformy Azure przy użyciu szablonu
description: Instrukcje krok po kroku dotyczące konfigurowania zarządzane tożsamości usługi (MSI) na maszynie Wirtualnej platformy Azure, przy użyciu szablonu usługi Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 8c955e6ad9d47c6963a1c136600761fddee03835
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Konfigurowanie tożsamości usługi maszyn wirtualnych zarządzanych za pomocą szablonu

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej zapewnia usług platformy Azure przy użyciu tożsamości automatycznie zarządzane w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule Dowiedz się jak wykonać następujące operacje zarządzane tożsamości usługi na maszynie Wirtualnej platformy Azure, przy użyciu szablonu wdrożenia usługi Azure Resource Manager:

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli znasz zarządzane tożsamość usługi, zapoznaj się [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między systemu przypisane i przypisać tożsamość użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Podobnie jak w przypadku platformy Azure, portalu i skryptów, [usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) Szablony zapewniają możliwość wdrażania nowych lub zmodyfikowanych zasobów zdefiniowany przez grupę zasobów platformy Azure. Kilka opcje są dostępne do edycji szablonu i wdrażania, zarówno lokalnych, jak i oparte na portalu, w tym:

   - Przy użyciu [niestandardowego szablonu z portalu Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), co pozwala na tworzyć szablon od podstaw albo oparte na istniejących typowe lub [szablon szybkiego startu](https://azure.microsoft.com/documentation/templates/).
   - Wyprowadzanie z istniejącej grupy zasobów przez wyeksportowanie szablonu z dowolnej [oryginalnego wdrożenia](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), lub z [bieżący stan wdrożenia](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Przy użyciu lokalnego [edytora JSON (na przykład w kodzie VS)](../../azure-resource-manager/resource-manager-create-first-template.md), a następnie przekazywanie i wdrażanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
   - Za pomocą programu Visual Studio [projektu grupy zasobów platformy Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) do tworzenia i wdrażania szablonu.  

Niezależnie od wybranej opcji składni szablonu jest taka sama podczas pierwszego wdrożenia i ponownego wdrażania. Włączanie system lub użytkownik, któremu przypisano tożsamości w nowej lub istniejącej maszyny Wirtualnej odbywa się w taki sam sposób. Ponadto domyślnie program Azure Resource Manager jest [aktualizacji przyrostowej](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) do wdrożenia.

## <a name="system-assigned-identity"></a>System przypisane tożsamości

W tej sekcji zostanie włączony i Wyłącz system przypisane tożsamość za pomocą szablonu usługi Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Włącz system przypisane tożsamości podczas tworzenia maszyny Wirtualnej platformy Azure, lub z istniejącej maszyny Wirtualnej

1. Logowanie do platformy Azure lokalnie lub za pośrednictwem portalu Azure, użyj konta, które jest skojarzone z subskrypcją platformy Azure, który zawiera maszyny Wirtualnej. Upewnij się również, że używane konto należy do roli, która daje uprawnienia do zapisu na maszynie Wirtualnej (na przykład roli "Współautor maszyny wirtualnej").

2. Po załadowaniu szablonu do edytora, zlokalizuj `Microsoft.Compute/virtualMachines` zasobów odsetek w `resources` sekcji. Należy do Ciebie może wyglądać nieco inaczej niż poniższy zrzut ekranu, w zależności od edytor, którego używasz, i czy edytujesz szablon do wdrożenia nowego lub istniejącego.

   >[!NOTE] 
   > W tym przykładzie założono zmienne `vmName`, `storageAccountName`, i `nicName` zostały zdefiniowane w szablonie.
   >

   ![Zrzut ekranu przedstawiający szablonu — zlokalizować maszyny Wirtualnej](../media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Aby włączyć system przypisane tożsamości, Dodaj `"identity"` właściwości na tym samym poziomie jako `"type": "Microsoft.Compute/virtualMachines"` właściwości. Należy użyć następującej składni:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. (Opcjonalnie) Dodaj rozszerzenie pliku MSI maszyny Wirtualnej jako `resources` elementu. Ten krok jest opcjonalny, jako punkt końcowy tożsamości Azure wystąpienie metadanych usługi (IMDS), umożliwia pobranie również tokenów.  Należy użyć następującej składni:

   >[!NOTE] 
   > W poniższym przykładzie założono rozszerzenia maszyny Wirtualnej systemu Windows (`ManagedIdentityExtensionForWindows`) jest wdrażany. Można również skonfigurować dla systemu Linux przy użyciu `ManagedIdentityExtensionForLinux` zamiast tego dla `"name"` i `"type"` elementy.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2016-03-30",
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

5. Gdy wszystko będzie gotowe, szablon powinien wyglądać podobnie do następującego:

   ![Zrzut ekranu przedstawiający szablonu po aktualizacji](../media/msi-qs-configure-template-windows-vm/template-file-after.png)

### <a name="disable-a-system-assigned-identity-from-an-azure-vm"></a>Wyłącz system przypisany tożsamości z maszyny Wirtualnej platformy Azure

> [!NOTE]
> Wyłączanie zarządzane tożsamość usługi z maszyny wirtualnej nie jest obecnie obsługiwane. Tymczasem można przełączać się między przypisane systemu i przypisane tożsamości użytkowników.

Jeśli masz maszynę Wirtualną, która nie będzie już potrzebował tożsamości zarządzanych usług:

1. Logowanie do platformy Azure lokalnie lub za pośrednictwem portalu Azure, użyj konta, które jest skojarzone z subskrypcją platformy Azure, który zawiera maszyny Wirtualnej. Upewnij się również, że używane konto należy do roli, która daje uprawnienia do zapisu na maszynie Wirtualnej (na przykład roli "Współautor maszyny wirtualnej").

2. Zmień typ tożsamości do `UserAssigned`.

## <a name="user-assigned-identity"></a>Użytkownik, któremu przypisano tożsamości

W tej sekcji należy przypisać tożsamość użytkownika z przypisanym do maszyny Wirtualnej platformy Azure przy użyciu szablonu usługi Azure Resource Manager.

> [!Note]
> Aby tworzyć tożsamość użytkownika z przypisanym przy użyciu szablonu usługi Azure Resource Manager, zobacz [utworzenia tożsamości użytkownika z przypisanym](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

 ### <a name="assign-a-user-assigned-identity-to-an-azure-vm"></a>Przypisać użytkownicy przypisani tożsamości do maszyny Wirtualnej platformy Azure

1. W obszarze `resources` elementu, Dodaj następujący wpis, aby przypisać tożsamość użytkownika z przypisanym do maszyny Wirtualnej.  Pamiętaj zastąpić `<USERASSIGNEDIDENTITY>` o nazwie tożsamość użytkownika z przypisanym został utworzony.
    ```json
    {
        "apiVersion": "2017-12-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/<USERASSIGNEDIDENTITYNAME>)']"
            ]
        },
    ```
    
2. (Opcjonalnie) Następnie w obszarze `resources` elementu, Dodaj następujący wpis, aby przypisać rozszerzenie tożsamości zarządzanych do maszyny Wirtualnej. Ten krok jest opcjonalny, jako punkt końcowy tożsamości Azure wystąpienie metadanych usługi (IMDS), umożliwia pobranie również tokenów. Należy użyć następującej składni:
    ```json
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
    ```
    
3.  Gdy wszystko będzie gotowe, szablon powinien wyglądać podobnie do następującego:

      ![Zrzut ekranu przedstawiający tożsamości przypisane przez użytkownika](./media/qs-configure-template-windows-vm/qs-configure-template-windows-vm-ua-final.PNG)


## <a name="related-content"></a>Zawartość pokrewna

- Dla szerszego perspektywy o MSI, przeczytaj [omówienie zarządzane tożsamość usługi](overview.md).

