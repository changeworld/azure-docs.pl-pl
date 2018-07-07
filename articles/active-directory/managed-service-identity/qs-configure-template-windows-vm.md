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
ms.openlocfilehash: 30e186c86d9947c5d0ef609a1c447dc6ed938c35
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902415"
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Konfigurowanie tożsamości usługi zarządzanej maszyny Wirtualnej przy użyciu szablonu

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wykonywać następujące operacje tożsamości usługi zarządzanej na Maszynie wirtualnej platformy Azure, przy użyciu szablonu wdrożenia usługi Azure Resource Manager:

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między przypisanej w systemie i tożsamości przypisanych przez użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Podobnie jak w witrynie Azure portal i skryptów, [usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) Szablony zapewniają możliwość wdrażania nowych lub zmodyfikowanych zasobów zdefiniowany przez grupę zasobów platformy Azure. Jest dostępnych kilka opcji edycję szablonu i wdrażania, zarówno lokalnych, jak i oparte na portalu, w tym:

   - Za pomocą [niestandardowego szablonu w portalu Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), co pozwala na Utwórz szablon od podstaw albo oparte na typowych istniejących lub [szablon szybkiego startu](https://azure.microsoft.com/documentation/templates/).
   - Wyprowadzanie z istniejącej grupy zasobów przez wyeksportowanie szablonu z poziomu [oryginalnego wdrożenia](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), lub z [bieżący stan wdrożenia](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Za pomocą lokalnego [edytora JSON (np. programu VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), a następnie przekazywanie i wdrażanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
   - Za pomocą programu Visual Studio [projekt grupy zasobów platformy Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) Aby utworzyć i wdrożyć szablon.  

Niezależnie od wybranej opcji składni szablonu jest taka sama podczas początkowego wdrażania i ponownego wdrażania. Włączanie system lub użytkownik, któremu przypisano tożsamość na nowej lub istniejącej maszyny Wirtualnej z systemem odbywa się w taki sam sposób. Ponadto domyślnie usługi Azure Resource Manager jest [aktualizacji przyrostowej](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) do wdrożeń.

## <a name="system-assigned-identity"></a>Tożsamości przypisanej w systemie

W tej sekcji zostanie włączony i Wyłącz system przypisane do tożsamości przy użyciu szablonu usługi Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Włącz system tożsamości przypisanej podczas tworzenia maszyny wirtualnej portalu Azure lub istniejącej maszyny wirtualnej

1. Czy logowanie do platformy Azure, lokalnie lub w witrynie Azure portal, należy użyć konta skojarzonego z subskrypcją platformy Azure, która zawiera maszyny Wirtualnej. Upewnij się również, że Twoje konto należy do roli, która zapewnia uprawnienia do zapisu na maszynie Wirtualnej (na przykład rola "Współautor maszyny wirtualnej").

2. Po załadowaniu szablonu do edytora, zlokalizuj `Microsoft.Compute/virtualMachines` zasobów zainteresowania w ramach `resources` sekcji. Należy do Ciebie mogą się nieznacznie różnić od Poniższy zrzut ekranu, w zależności od edytor, którego używasz, i czy edytujesz szablon dla nowego wdrożenia lub istniejącą grupę.

   >[!NOTE] 
   > W tym przykładzie założono zmienne `vmName`, `storageAccountName`, i `nicName` zostały zdefiniowane w szablonie.
   >

   ![Zrzut ekranu przedstawiający szablon — Znajdź maszynę Wirtualną](../media/msi-qs-configure-template-windows-vm/template-file-before.png) 

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

   ![Zrzut ekranu przedstawiający szablon po aktualizacji](../media/msi-qs-configure-template-windows-vm/template-file-after.png)

### <a name="disable-a-system-assigned-identity-from-an-azure-vm"></a>Wyłącz system tożsamości przypisanej w Maszynie wirtualnej platformy Azure

> [!NOTE]
> Wyłączanie tożsamości usługi zarządzanej z maszyny wirtualnej nie jest obecnie obsługiwane. W międzyczasie można przełączać się między przypisana przez System i tożsamości przypisanych użytkowników.

Jeśli masz maszynę Wirtualną, która nie wymaga tożsamości usługi zarządzanej:

1. Czy logowanie do platformy Azure, lokalnie lub w witrynie Azure portal, należy użyć konta skojarzonego z subskrypcją platformy Azure, która zawiera maszyny Wirtualnej. Upewnij się również, że Twoje konto należy do roli, która zapewnia uprawnienia do zapisu na maszynie Wirtualnej (na przykład rola "Współautor maszyny wirtualnej").

2. Zmień typ tożsamości na `UserAssigned`.

## <a name="user-assigned-identity"></a>Tożsamości przypisanych przez użytkownika

W tej sekcji należy przypisać tożsamości przypisanych przez użytkownika na Maszynie wirtualnej platformy Azure przy użyciu szablonu usługi Azure Resource Manager.

> [!Note]
> Aby utworzyć tożsamości przypisanych przez użytkownika, przy użyciu szablonu usługi Azure Resource Manager, zobacz [tworzenie tożsamości przypisanych przez użytkownika](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

 ### <a name="assign-a-user-assigned-identity-to-an-azure-vm"></a>Przypisywanie użytkownika tożsamości przypisanej do maszyny Wirtualnej platformy Azure

1. W obszarze `resources` elementu, Dodaj następujący wpis do przypisywania tożsamości przypisanych przez użytkownika do maszyny Wirtualnej.  Koniecznie Zastąp `<USERASSIGNEDIDENTITY>` o nazwie tożsamości przypisanych przez użytkownika został utworzony.
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


## <a name="related-content"></a>Powiązana zawartość

- Dla szerszej perspektywy temat tożsamości usługi Zarządzanej, przeczytaj [Przegląd tożsamości usługi zarządzanej](overview.md).

