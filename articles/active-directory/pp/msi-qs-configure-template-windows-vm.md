---
title: Jak skonfigurować Instalatora MSI przypisane przez użytkownika dla maszyny Wirtualnej platformy Azure przy użyciu szablonu platformy Azure
description: Krok kroku instrukcje dotyczące konfigurowania przypisanych do użytkowników zarządzanych usługi tożsamości (MSI) dla maszyny Wirtualnej platformy Azure, przy użyciu szablonu usługi Azure Resource Manager.
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
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2018
ms.locfileid: "28984043"
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-template"></a>Skonfiguruj przypisany użytkownik zarządzane usługi tożsamości (MSI) dla maszyny Wirtualnej, przy użyciu szablonu platformy Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tożsamość usługi zarządzanej udostępnia usługi Azure za pomocą tożsamości zarządzanych w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule Dowiedz się jak włączyć i usunąć przypisany użytkownik MSI dla maszyny Wirtualnej platformy Azure, przy użyciu szablonu wdrożenia usługi Azure Resource Manager.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Włącz MSI podczas tworzenia maszyny Wirtualnej platformy Azure, lub z istniejącej maszyny Wirtualnej

Zgodnie z platformy Azure portalu i skryptów, szablony usługi Azure Resource Manager zapewniają możliwość wdrażania nowych lub zmodyfikowanych zasobów zdefiniowany przez grupę zasobów platformy Azure. Kilka opcje są dostępne do edycji szablonu i wdrażania, zarówno lokalnych, jak i oparte na portalu, w tym:

   - Przy użyciu [niestandardowego szablonu z portalu Azure Marketplace](~/articles/azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), co pozwala na tworzyć szablon od podstaw albo oparte na istniejących typowe lub [szablon szybkiego startu](https://azure.microsoft.com/documentation/templates/).
   - Wyprowadzanie z istniejącej grupy zasobów przez wyeksportowanie szablonu z dowolnej [oryginalnego wdrożenia](~/articles/azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), lub z [bieżący stan wdrożenia](~/articles/azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Przy użyciu lokalnego [edytora JSON (na przykład w kodzie VS)](~/articles/azure-resource-manager/resource-manager-create-first-template.md), a następnie przekazywanie i wdrażanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
   - Za pomocą programu Visual Studio [projektu grupy zasobów platformy Azure](~/articles/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) do tworzenia i wdrażania szablonu.  

Niezależnie od wybranej opcji składni szablonu jest taka sama podczas pierwszego wdrożenia i ponownego wdrażania. Tworzenie i przypisywanie pliku MSI przypisane przez użytkownika do nowej lub istniejącej maszyny Wirtualnej odbywa się w taki sam sposób. Ponadto domyślnie program Azure Resource Manager jest [aktualizacji przyrostowej](~/articles/azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) do wdrożenia:

1. Logowanie do platformy Azure lokalnie lub za pośrednictwem portalu Azure, użyj konta, które jest skojarzone z subskrypcją platformy Azure zawiera MSI i maszyny Wirtualnej. Upewnij się również, że używane konto należy do roli, która daje uprawnienia do zapisu w subskrypcji lub zasobów (na przykład rola "właściciela").

2. Po załadowaniu szablonu do edytora, zlokalizuj `Microsoft.Compute/virtualMachines` zasobów odsetek w `resources` sekcji. Należy do Ciebie może wyglądać nieco inaczej niż poniższy zrzut ekranu, w zależności od edytor, którego używasz, i czy edytujesz szablon do wdrożenia nowego lub istniejącego.

   >[!NOTE] 
   > W tym przykładzie założono zmienne `vmName`, `storageAccountName`, i `nicName` zostały zdefiniowane w szablonie.
   >

   ![Zrzut ekranu przedstawiający szablonu — zlokalizować maszyny Wirtualnej](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Dodaj `"identity"` właściwości na tym samym poziomie jako `"type": "Microsoft.Compute/virtualMachines"` właściwości. Należy użyć następującej składni:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Następnie dodaj rozszerzenie pliku MSI maszyny Wirtualnej jako `resources` elementu. Należy użyć następującej składni:

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

   ![Zrzut ekranu przedstawiający szablonu po aktualizacji](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Usuń MSI w maszynie Wirtualnej platformy Azure

Jeśli masz maszynę Wirtualną, która nie będzie już potrzebował MSI:

1. Logowanie do platformy Azure lokalnie lub za pośrednictwem portalu Azure, użyj konta, które jest skojarzone z subskrypcją platformy Azure, który zawiera maszyny Wirtualnej. Upewnij się również, że używane konto należy do roli, która daje uprawnienia do zapisu na maszynie Wirtualnej (na przykład roli "Współautor maszyny wirtualnej").

2. Usuń dwa elementy, które zostały dodane w poprzedniej sekcji: maszyny Wirtualnej `"identity"` właściwości i `"Microsoft.Compute/virtualMachines/extensions"` zasobów.

## <a name="related-content"></a>Zawartość pokrewna

- Dla szerszego perspektywy o MSI, przeczytaj [omówienie zarządzane tożsamość usługi](msi-overview.md).

