---
title: Jak skonfigurować MSI na Maszynie wirtualnej platformy Azure przy użyciu szablonu
description: Szczegółowe instrukcje dotyczące konfigurowania tożsamość usługi zarządzanej (MSI) na Maszynie wirtualnej platformy Azure, przy użyciu szablonu usługi Azure Resource Manager.
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
ms.openlocfilehash: 703595bbc13fb859f406e7c9fa422a9c573957ab
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237251"
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

Niezależnie od wybranej opcji składni szablonu jest taka sama podczas początkowego wdrażania i ponownego wdrażania. Włączanie system lub użytkownik, któremu przypisano tożsamość na nowej lub istniejącej maszyny Wirtualnej z systemem odbywa się w taki sam sposób. Ponadto domyślnie usługi Azure Resource Manager jest [aktualizacji przyrostowej](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) do wdrożeń.

## <a name="system-assigned-identity"></a>Tożsamości przypisanej w systemie

W tej sekcji zostanie włączony i Wyłącz system przypisane do tożsamości przy użyciu szablonu usługi Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Włącz system tożsamości przypisanej podczas tworzenia maszyny Wirtualnej platformy Azure lub z istniejącej maszyny Wirtualnej

1. Czy logowanie do platformy Azure, lokalnie lub w witrynie Azure portal, należy użyć konta skojarzonego z subskrypcją platformy Azure, która zawiera maszyny Wirtualnej.

2. Po załadowaniu szablonu do edytora, zlokalizuj `Microsoft.Compute/virtualMachines` zasobów zainteresowania w ramach `resources` sekcji. Należy do Ciebie mogą się nieznacznie różnić od Poniższy zrzut ekranu, w zależności od edytor, którego używasz, i czy edytujesz szablon dla nowego wdrożenia lub istniejącą grupę.

   >[!NOTE] 
   > W tym przykładzie założono zmienne `vmName`, `storageAccountName`, i `nicName` zostały zdefiniowane w szablonie.
   >

   ![Zrzut ekranu przedstawiający szablon — Znajdź maszynę Wirtualną](../managed-service-identity/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Aby włączyć system tożsamości przypisanej, Dodaj `"identity"` właściwości w tym samym poziomie co `"type": "Microsoft.Compute/virtualMachines"` właściwości. Należy użyć następującej składni:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. (Opcjonalnie) Dodaj rozszerzenie Zarządzanej maszyny Wirtualnej jako `resources` elementu. Ten krok jest opcjonalny, zgodnie z punktu końcowego tożsamości Azure wystąpienie metadanych usługi (IMDS), można użyć do pobierania tokenów, jak również.  Należy użyć następującej składni:

   >[!NOTE] 
   > W poniższym przykładzie założono rozszerzenie maszyny Wirtualnej Windows (`ManagedIdentityExtensionForWindows`) jest wdrażany. Można również skonfigurować dla systemu Linux przy użyciu `ManagedIdentityExtensionForLinux` katalog wirtualny wskazywał na `"name"` i `"type"` elementów.
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

5. Gdy wszystko będzie gotowe, szablon powinien wyglądać podobnie do poniższej:

   ![Zrzut ekranu przedstawiający szablon po aktualizacji](../managed-service-identity/media/msi-qs-configure-template-windows-vm/template-file-after.png)

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

2. Ładowanie szablonu do [edytora](#azure-resource-manager-templates) i Znajdź `Microsoft.Compute/virtualMachines` zasobów zainteresowania w ramach `resources` sekcji. Jeśli masz maszynę Wirtualną, która zawiera tylko tożsamości przypisanej w systemie, można ją wyłączyć, zmieniając typ tożsamości do `None`.  Jeśli maszyna wirtualna ma systemowych i tożsamości przypisanych przez użytkownika, Usuń `SystemAssigned` z typu tożsamości i zachować `UserAssigned` wraz z `identityIds` Tablica tożsamości przypisanych przez użytkownika.  Poniższy przykład pokazuje, jak usunąć tożsamości przypisanej z maszyny Wirtualnej bez użytkownika tożsamości przypisanych przez system:
   
   ```JSON
    {
      "apiVersion": "2017-12-01",
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
   
   > [!Important]
   > `<USERASSIGNEDIDENTITYNAME>` Pokazano w poniższym przykładzie wartość musi być przechowywany w zmiennej.  Ponadto obecnie obsługiwane wykonania przypisywania tożsamości przypisanych przez użytkownika do maszyny wirtualnej w szablonie usługi Resource Manager, wersja interfejsu api musi odpowiadać wersji w następującym przykładzie.
    
    ```json
    {
        "apiVersion": "2017-12-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
            ]
        },
    ```
    
2. (Opcjonalnie) Następnie w obszarze `resources` elementu, Dodaj następujący wpis, aby przypisać rozszerzenie tożsamości zarządzanej dla maszyny wirtualnej. Ten krok jest opcjonalny, zgodnie z punktu końcowego tożsamości Azure wystąpienie metadanych usługi (IMDS), można użyć do pobierania tokenów, jak również. Należy użyć następującej składni:
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
    
3.  Gdy wszystko będzie gotowe, szablon powinien wyglądać podobnie do poniższej:

      ![Zrzut ekranu przedstawiający tożsamości przypisanych przez użytkownika](./media/qs-configure-template-windows-vm/qs-configure-template-windows-vm-ua-final.PNG)

### <a name="remove-user-assigned-identity-from-an-azure-vm"></a>Usuwanie tożsamości przypisanych przez użytkownika na Maszynie wirtualnej platformy Azure

Jeśli masz maszynę Wirtualną, która nie wymaga tożsamości usługi zarządzanej:

1. Czy logowanie do platformy Azure, lokalnie lub w witrynie Azure portal, należy użyć konta skojarzonego z subskrypcją platformy Azure, która zawiera maszyny Wirtualnej.

2. Ładowanie szablonu do [edytora](#azure-resource-manager-templates) i Znajdź `Microsoft.Compute/virtualMachines` zasobów zainteresowania w ramach `resources` sekcji. Jeśli masz maszynę Wirtualną, która zawiera tylko tożsamości przypisanych przez użytkownika, można ją wyłączyć, zmieniając typ tożsamości do `None`.  Jeśli maszyna wirtualna ma systemowych i tożsamości przypisanych przez użytkownika, a chcesz zachować tożsamości przypisanej w systemie, Usuń `UserAssigned` z typu tożsamości, wraz z `identityIds` Tablica tożsamości przypisanych przez użytkownika.
    
   Aby usunąć pojedynczej przypisane tożsamości użytkownika z maszyny Wirtualnej, usuń go z `identityIds` tablicy.
   
   Poniższy przykład pokazuje, jak usunąć przypisane tożsamości z maszyny Wirtualnej przy użyciu tożsamości przypisanych przez system, nie do wszystkich użytkowników:
   
   ```JSON
    {
      "apiVersion": "2017-12-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
    }
   ```

## <a name="related-content"></a>Powiązana zawartość

- Dla szerszej perspektywy o tożsamości usługi zarządzanej, przeczytaj [Przegląd tożsamości usługi zarządzanej](overview.md).

