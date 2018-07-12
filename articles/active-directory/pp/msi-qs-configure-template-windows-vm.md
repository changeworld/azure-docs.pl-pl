---
title: Jak skonfigurować z przypisanego przez użytkownika tożsamości usługi Zarządzanej dla maszyny Wirtualnej platformy Azure przy użyciu szablonu platformy Azure
description: Krok po kroku instrukcje dotyczące konfigurowania użytkownik przypisany tożsamość usługi zarządzanej (MSI) na Maszynie wirtualnej platformy Azure, przy użyciu szablonu usługi Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e01e4c397e0d0a19280a32fc1e8341b57b47e4eb
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38610387"
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-template"></a>Konfigurowanie użytkownik przypisany tożsamość usługi zarządzanej (MSI) dla maszyny Wirtualnej przy użyciu szablonu platformy Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure za pomocą tożsamości zarządzanej w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD bez konieczności używania poświadczeń w kodzie. 

W tym artykule dowiesz się, jak włączyć i usuń plik MSI przypisanych przez użytkownika na Maszynie wirtualnej platformy Azure, przy użyciu szablonu wdrożenia usługi Azure Resource Manager.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Włączanie tożsamości usługi Zarządzanej podczas tworzenia maszyny wirtualnej portalu Azure lub istniejącej maszyny wirtualnej

Zgodnie z platformą Azure portal i skryptów, szablony usługi Azure Resource Manager umożliwiają wdrażać nowe lub zmodyfikowane zasoby zdefiniowane przez grupę zasobów platformy Azure. Jest dostępnych kilka opcji edycję szablonu i wdrażania, zarówno lokalnych, jak i oparte na portalu, w tym:

   - Za pomocą [niestandardowego szablonu w portalu Azure Marketplace](~/articles/azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), co pozwala na Utwórz szablon od podstaw albo oparte na typowych istniejących lub [szablon szybkiego startu](https://azure.microsoft.com/documentation/templates/).
   - Wyprowadzanie z istniejącej grupy zasobów przez wyeksportowanie szablonu z poziomu [oryginalnego wdrożenia](~/articles/azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), lub z [bieżący stan wdrożenia](~/articles/azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Za pomocą lokalnego [edytora JSON (np. programu VS Code)](~/articles/azure-resource-manager/resource-manager-create-first-template.md), a następnie przekazywanie i wdrażanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
   - Za pomocą programu Visual Studio [projekt grupy zasobów platformy Azure](~/articles/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) Aby utworzyć i wdrożyć szablon.  

Niezależnie od wybranej opcji składni szablonu jest taka sama podczas początkowego wdrażania i ponownego wdrażania. Tworzenie i przypisywanie Instalatora MSI użytkownik przypisany do nowej lub istniejącej maszyny Wirtualnej z systemem odbywa się w taki sam sposób. Ponadto domyślnie usługi Azure Resource Manager jest [aktualizacji przyrostowej](~/articles/azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) wdrożeń:

1. Czy logowanie do platformy Azure, lokalnie lub w witrynie Azure portal, należy użyć konta skojarzonego z subskrypcją platformy Azure zawierające MSI i maszyny Wirtualnej. Upewnij się również, że Twoje konto należy do roli, która zapewnia uprawnienia do zapisu dla subskrypcji lub zasoby (na przykład rola "właściciel").

2. Po załadowaniu szablonu do edytora, zlokalizuj `Microsoft.Compute/virtualMachines` zasobów zainteresowania w ramach `resources` sekcji. Należy do Ciebie mogą się nieznacznie różnić od Poniższy zrzut ekranu, w zależności od edytor, którego używasz, i czy edytujesz szablon dla nowego wdrożenia lub istniejącą grupę.

   >[!NOTE] 
   > W tym przykładzie założono zmienne `vmName`, `storageAccountName`, i `nicName` zostały zdefiniowane w szablonie.
   >

   ![Zrzut ekranu przedstawiający szablon — Znajdź maszynę Wirtualną](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Dodaj `"identity"` właściwości w tym samym poziomie co `"type": "Microsoft.Compute/virtualMachines"` właściwości. Należy użyć następującej składni:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Następnie dodaj rozszerzenie Zarządzanej maszyny Wirtualnej jako `resources` elementu. Należy użyć następującej składni:

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

   ![Zrzut ekranu przedstawiający szablon po aktualizacji](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Usuń plik MSI z maszyny Wirtualnej platformy Azure

Jeśli masz maszynę Wirtualną, która nie wymaga Instalatora MSI:

1. Czy logowanie do platformy Azure, lokalnie lub w witrynie Azure portal, należy użyć konta skojarzonego z subskrypcją platformy Azure, która zawiera maszyny Wirtualnej. Upewnij się również, że Twoje konto należy do roli, która zapewnia uprawnienia do zapisu na maszynie Wirtualnej (na przykład rola "Współautor maszyny wirtualnej").

2. Usuń dwa elementy, które zostały dodane w poprzedniej sekcji: maszyny Wirtualnej `"identity"` właściwości i `"Microsoft.Compute/virtualMachines/extensions"` zasobów.

## <a name="related-content"></a>Powiązana zawartość

- Dla szerszej perspektywy temat tożsamości usługi Zarządzanej, przeczytaj [Przegląd tożsamości usługi zarządzanej](msi-overview.md).

