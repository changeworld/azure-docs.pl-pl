---
title: Skonfiguruj MSI skali maszyny wirtualnej platformy Azure, ustawić przy użyciu szablonu
description: Instrukcje krok po kroku dotyczące konfigurowania zarządzane tożsamości usługi (MSI) na VMSS Azure, przy użyciu szablonu usługi Azure Resource Manager.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 64fe217cf3d845e6a09fe67d03648e79e8a4cadd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="configure-a-vmss-managed-service-identity-by-using-a-template"></a>Konfigurowanie tożsamości usługi VMSS zarządzane przy użyciu szablonu

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej zapewnia usług platformy Azure przy użyciu tożsamości automatycznie zarządzane w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule Dowiedz się jak wykonać następujące operacje zarządzane tożsamości usługi na VMSS Azure, przy użyciu szablonu wdrożenia usługi Azure Resource Manager:
- Włączanie i wyłączanie tożsamość na VMSS Azure przypisanego przez system
- Dodawanie i usuwanie użytkownika przypisanego tożsamość na VMSS Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli znasz zarządzane tożsamość usługi, zapoznaj się [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między systemu przypisane i przypisać tożsamość użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Zgodnie z platformy Azure portalu i skryptów, szablony usługi Azure Resource Manager zapewniają możliwość wdrażania nowych lub zmodyfikowanych zasobów zdefiniowany przez grupę zasobów platformy Azure. Kilka opcje są dostępne do edycji szablonu i wdrażania, zarówno lokalnych, jak i oparte na portalu, w tym:

   - Przy użyciu [niestandardowego szablonu z portalu Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), co pozwala na tworzyć szablon od podstaw albo oparte na istniejących typowe lub [szablon szybkiego startu](https://azure.microsoft.com/documentation/templates/).
   - Wyprowadzanie z istniejącej grupy zasobów przez wyeksportowanie szablonu z dowolnej [oryginalnego wdrożenia](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), lub z [bieżący stan wdrożenia](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Przy użyciu lokalnego [edytora JSON (na przykład w kodzie VS)](../../azure-resource-manager/resource-manager-create-first-template.md), a następnie przekazywanie i wdrażanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
   - Za pomocą programu Visual Studio [projektu grupy zasobów platformy Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) do tworzenia i wdrażania szablonu.  

Niezależnie od wybranej opcji składni szablonu jest taka sama podczas pierwszego wdrożenia i ponownego wdrażania. Włączanie MSI w maszynie Wirtualnej nowego lub istniejącego odbywa się w taki sam sposób. Ponadto domyślnie program Azure Resource Manager jest [aktualizacji przyrostowej](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) do wdrożenia.

## <a name="system-assigned-identity"></a>System przypisane tożsamości

W tej sekcji zostanie włączony i Wyłącz system przypisane tożsamość za pomocą szablonu usługi Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss"></a>Włącz system przypisane tożsamości podczas tworzenia VMSS Azure lub istniejące VMSS Azure

1. Czy logowania się do usługi Azure lokalnie lub za pośrednictwem portalu Azure, użyj konta, które jest skojarzone z subskrypcją platformy Azure, która zawiera zestaw skali maszyny wirtualnej.

2. Po załadowaniu szablonu do edytora, zlokalizuj `Microsoft.Compute/virtualMachineScaleSets` zasobów odsetek w `resources` sekcji. Należy do Ciebie może wyglądać nieco inaczej niż poniższy zrzut ekranu, w zależności od edytor, którego używasz, i czy edytujesz szablon do wdrożenia nowego lub istniejącego.
   
   ![Zrzut ekranu przedstawiający szablonu — zlokalizować maszyny Wirtualnej](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

3. Aby włączyć tożsamości systemu przypisane, Dodaj `"identity"` właściwości na tym samym poziomie jako `"type": "Microsoft.Compute/virtualMachineScaleSets"` właściwości. Należy użyć następującej składni:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. (Opcjonalnie) Dodaj rozszerzenie pliku MSI jako zestawu skalowania maszyn wirtualnych `extensionsProfile` elementu. Ten krok jest opcjonalny, jako tożsamości Azure wystąpienie metadanych usługi (IMDS) umożliwia pobranie również tokenów.  Należy użyć następującej składni:

   >[!NOTE] 
   > W poniższym przykładzie założono rozszerzenia zestawu skalowania maszyn wirtualnych systemu Windows (`ManagedIdentityExtensionForWindows`) jest wdrażany. Można również skonfigurować dla systemu Linux przy użyciu `ManagedIdentityExtensionForLinux` zamiast tego dla `"name"` i `"type"` elementy.
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

5. Gdy wszystko będzie gotowe, szablon powinien wyglądać podobnie do następującego:

   ![Zrzut ekranu przedstawiający szablonu po aktualizacji](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłącz tożsamości systemu przypisane, z zestawu skalowania maszyny wirtualnej platformy Azure

> [!NOTE]
> Wyłączanie zarządzane tożsamość usługi z maszyny wirtualnej nie jest obecnie obsługiwane. Tymczasem można przełączać się między przypisane systemu i przypisane tożsamości użytkowników.

Jeśli masz już zestaw skali maszyny wirtualnej należy przypisać tożsamość systemu, ale nadal należy przypisać tożsamość użytkownika:

1. Czy logowania się do usługi Azure lokalnie lub za pośrednictwem portalu Azure, użyj konta, które jest skojarzone z subskrypcją platformy Azure, która zawiera zestaw skali maszyny wirtualnej.

2. Zmień typ tożsamości do `'UserAssigned'`

## <a name="user-assigned-identity"></a>Użytkownik, któremu przypisano tożsamości

W tej sekcji utworzysz użytkownik, któremu przypisano tożsamości i VMSS Azure przy użyciu szablonu usługi Azure Resource Manager.

### <a name="create-and-assign-a-user-assigned-identity-to-an-azure-vmss"></a>Utwórz i przypisz użytkownika przypisanego tożsamości do VMSS Azure

1. Wykonaj pierwszy krok w sekcji [włączyć tożsamość systemu przypisany podczas tworzenia VMSS Azure, na istniejących VMSS](qs-configure-template-windows-vmss.md#enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss).

2. W sekcji zmiennych zawierający zmienne konfiguracji dla Twojego VMSS Azure Dodaj wpis dla nazwy przypisanej tożsamości użytkownika podobny do następującego.  To przechowuje wartość przypisane w trakcie procesu tworzenia Azure VMSS tożsamości użytkownika:
    
    > [!IMPORTANT]
    > Tworzenie użytkownika z przypisanym tożsamości z znaki specjalne (np. podkreślenie) w nazwie nie jest obecnie obsługiwane. Użyj znaków alfanumerycznych. Sprawdzanie dostępności aktualizacji.  Aby uzyskać więcej informacji, zobacz [— często zadawane pytania i znane problemy](known-issues.md)

    ```json
    "variables": {
        "vmssPrefix": "vmss",
        "vmssName": "[concat(variables('vmssPrefix'), uniquestring(resourceGroup().id,deployment().name))]",
        //other vm configuration variables...
        "identityName": "[concat(variables('vmssName'), 'id')]"
    ```
3. W obszarze `resources` element Dodaj następujący wpis do utworzenia tożsamości użytkownika z przypisanym:

    ```json
    {
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
        "name": "[variables('identityName')]",
        "apiVersion": "2015-08-31-PREVIEW",
        "location": "[resourceGroup().location]"
    },
    ```
4. Następnie w obszarze `resources` element Dodaj następujący wpis do przypisania tożsamości użytkownika przypisane do użytkownika VMSS:

    ```json
    {
        "name": "[variables('vmssName')]",
        "apiVersion": "2017-03-30",
        "location": "[parameters(Location')]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/, variables('identityName'))]"
            ]
        }

    }
    ```
5. (Opcjonalnie) Dodaj następujący wpis w obszarze `extensionProfile` elementu, aby przypisać rozszerzenie tożsamości zarządzanych do Twojej VMSS. Ten krok jest opcjonalny, jako punkt końcowy tożsamości Azure wystąpienie metadanych usługi (IMDS), umożliwia pobranie również tokenów. Należy użyć następującej składni:
   
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
6.  Gdy wszystko będzie gotowe, szablon powinien wyglądać podobnie do następującego:
    > [!NOTE]
    > Szablon nie ma wszystkie zmienne niezbędne do utworzenia sieci VMSS.  `//other configuration variables...` Służy do miejsca wszystkie zmienne konfiguracji niezbędne w celu skrócenia.

      ![Zrzut ekranu przedstawiający tożsamości przypisane przez użytkownika](../media/msi-qs-configure-template-windows-vmss/template-vmss-user-assigned-identity.png)

## <a name="next-steps"></a>Kolejne kroki

- Dla szerszego perspektywy o MSI, przeczytaj [omówienie zarządzane tożsamość usługi](overview.md).

