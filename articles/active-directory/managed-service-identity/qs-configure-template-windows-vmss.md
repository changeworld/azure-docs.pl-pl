---
title: Konfigurowanie pliku MSI na Azure zestawu skalowania maszyn wirtualnych przy użyciu szablonu
description: Szczegółowe instrukcje dotyczące konfigurowania tożsamość usługi zarządzanej (MSI) na platformie Azure zestawu skalowania maszyn wirtualnych, przy użyciu szablonu usługi Azure Resource Manager.
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
ms.openlocfilehash: ab3982c85cfb008bde08495f8cb8aa86d066d8c0
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114858"
---
# <a name="configure-a-vmss-managed-service-identity-by-using-a-template"></a>Konfigurowanie tożsamości usługi zarządzanej zestawu skalowania maszyn wirtualnych przy użyciu szablonu

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wykonywać następujące operacje tożsamości usługi zarządzanej w usłudze VMSS platformy Azure, przy użyciu szablonu wdrożenia usługi Azure Resource Manager:
- Włączanie i wyłączanie systemu przypisane tożsamość zestawu skalowania maszyn wirtualnych platformy Azure
- Dodawanie i usuwanie użytkownik, któremu przypisano tożsamość zestawu skalowania maszyn wirtualnych platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między przypisanej w systemie i tożsamości przypisanych przez użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Podobnie jak w witrynie Azure portal i skryptów, [usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) Szablony zapewniają możliwość wdrażania nowych lub zmodyfikowanych zasobów zdefiniowany przez grupę zasobów platformy Azure. Jest dostępnych kilka opcji edycję szablonu i wdrażania, zarówno lokalnych, jak i oparte na portalu, w tym:

   - Za pomocą [niestandardowego szablonu w portalu Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), co pozwala na Utwórz szablon od podstaw albo oparte na typowych istniejących lub [szablon szybkiego startu](https://azure.microsoft.com/documentation/templates/).
   - Wyprowadzanie z istniejącej grupy zasobów przez wyeksportowanie szablonu z poziomu [oryginalnego wdrożenia](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), lub z [bieżący stan wdrożenia](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Za pomocą lokalnego [edytora JSON (np. programu VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), a następnie przekazywanie i wdrażanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
   - Za pomocą programu Visual Studio [projekt grupy zasobów platformy Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) Aby utworzyć i wdrożyć szablon.  

Niezależnie od wybranej opcji składni szablonu jest taka sama podczas początkowego wdrażania i ponownego wdrażania. Włączanie tożsamości usługi Zarządzanej w nowej lub istniejącej maszyny Wirtualnej z systemem odbywa się w taki sam sposób. Ponadto domyślnie usługi Azure Resource Manager jest [aktualizacji przyrostowej](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) do wdrożeń.

## <a name="system-assigned-identity"></a>Tożsamości przypisanej w systemie

W tej sekcji zostanie włączony i Wyłącz system przypisane do tożsamości przy użyciu szablonu usługi Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss"></a>Włącz system tożsamości przypisanej podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure lub istniejącego zestawu skalowania maszyn wirtualnych platformy Azure

1. Załadować szablon do edytora, odszukaj `Microsoft.Compute/virtualMachineScaleSets` zasobów zainteresowania w ramach `resources` sekcji. Należy do Ciebie mogą się nieznacznie różnić od Poniższy zrzut ekranu, w zależności od edytor, którego używasz, i czy edytujesz szablon dla nowego wdrożenia lub istniejącą grupę.
   
   ![Zrzut ekranu przedstawiający szablon — Znajdź maszynę Wirtualną](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. Aby umożliwić tożsamości przypisanej w systemie, Dodaj `"identity"` właściwości w tym samym poziomie co `"type": "Microsoft.Compute/virtualMachineScaleSets"` właściwości. Należy użyć następującej składni:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. (Opcjonalnie) Dodaj rozszerzenie pliku MSI jako zestawu skalowania maszyn wirtualnych `extensionsProfile` elementu. Ten krok jest opcjonalny, zgodnie z tożsamości usługi Azure wystąpienie metadanych usługi (IMDS), można użyć do pobierania tokenów, jak również.  Należy użyć następującej składni:

   >[!NOTE] 
   > W poniższym przykładzie założono, rozszerzenie zestawu skalowania maszyn wirtualnych z systemem Windows (`ManagedIdentityExtensionForWindows`) jest wdrażany. Można również skonfigurować dla systemu Linux przy użyciu `ManagedIdentityExtensionForLinux` katalog wirtualny wskazywał na `"name"` i `"type"` elementów.
   >

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

4. Gdy wszystko będzie gotowe, szablon powinien wyglądać podobnie do poniższej:

   ![Zrzut ekranu przedstawiający szablon po aktualizacji](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłączanie tożsamości przypisanej w systemie, z zestawu skalowania maszyn wirtualnych platformy Azure

> [!NOTE]
> Wyłączanie tożsamości usługi zarządzanej z maszyny wirtualnej nie jest obecnie obsługiwane. W międzyczasie można przełączać się między przypisana przez System i tożsamości przypisanych użytkowników.

Jeśli masz już zestaw skalowania maszyn wirtualnych wymaga systemu tożsamości przypisanej, ale nadal wymaga tożsamości przypisanych przez użytkownika:

- Załaduj szablon do edytora, a następnie zmień typ tożsamości do `'UserAssigned'`

## <a name="user-assigned-identity"></a>Tożsamości przypisanych przez użytkownika

W tej sekcji należy przypisać tożsamości przypisanych przez użytkownika do zestawu skalowania maszyn wirtualnych platformy Azure przy użyciu szablonu usługi Azure Resource Manager.

> [!Note]
> Aby utworzyć tożsamości przypisanych przez użytkownika, przy użyciu szablonu usługi Azure Resource Manager, zobacz [tworzenie tożsamości przypisanych przez użytkownika](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Przypisywanie użytkownika tożsamości przypisanej do zestawu skalowania maszyn wirtualnych platformy Azure

1. W obszarze `resources` elementu, Dodaj następujący wpis do przypisywania tożsamości przypisanych przez użytkownika do Twojego zestawu skalowania maszyn wirtualnych.  Koniecznie Zastąp `<USERASSIGNEDIDENTITY>` o nazwie tożsamości przypisanych przez użytkownika został utworzony.

   > [!Important]
   > `<USERASSIGNEDIDENTITYNAME>` Pokazano w poniższym przykładzie wartość musi być przechowywany w zmiennej.  Ponadto obecnie obsługiwane wykonania przypisywania tożsamości przypisanych przez użytkownika do maszyny wirtualnej w szablonie usługi Resource Manager, wersja interfejsu api musi odpowiadać wersji w następującym przykładzie. 

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
3.  Gdy wszystko będzie gotowe, szablon powinien wyglądać podobnie do poniższej:
   
      ![Zrzut ekranu przedstawiający tożsamości przypisanych przez użytkownika](./media/qs-configure-template-windows-vmss/qs-configure-template-windows-final.PNG)

## <a name="next-steps"></a>Kolejne kroki

- Dla szerszej perspektywy temat tożsamości usługi Zarządzanej, przeczytaj [Przegląd tożsamości usługi zarządzanej](overview.md).

