---
title: Określ grupę zasobów dla maszyn wirtualnych w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i określ grupę zasobów dla maszyn wirtualnych w laboratorium Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2019
ms.author: spelluru
ms.openlocfilehash: 574cc0c41ce645c71302178afcf6e7deaec69d8e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476089"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Określ grupę zasobów dla maszyn wirtualnych laboratorium Azure DevTest Labs

Jako właściciel laboratorium możesz skonfigurować maszyny wirtualne laboratorium ma być utworzony w określonej grupie zasobów. Ta funkcja pomaga w następujących scenariuszach:

- Ma mniejszą liczbę grup zasobów utworzonych przez laboratoria w ramach subskrypcji.
- Ma działać w ramach ustalony zestaw elementów grupy zasobów, które można skonfigurować laboratorium.
- Obejścia ograniczeń i zatwierdzeń wymagane do utworzenia grupy zasobów w ramach subskrypcji platformy Azure.
- Konsolidacja wszystkich zasobów laboratorium w ramach pojedynczej grupy zasobów ułatwiają śledzenie tych zasobów i stosowanie [zasady](../governance/policy/overview.md) do zarządzania zasobami na poziomie grupy zasobów.

Dzięki tej funkcji można użyć skryptu do określenia nowej lub istniejącej grupy zasobów w ramach subskrypcji platformy Azure dla wszystkich laboratorium, maszyny wirtualne. Obecnie usługa Azure DevTest Labs obsługuje tę funkcję za pomocą interfejsu API.

> [!NOTE]
> Po utworzeniu laboratoria DevTest Labs, mają zastosowanie wszystkie limity subskrypcji. Laboratorium można traktować jako innych zasobów w ramach subskrypcji. W przypadku grup zasobów wynoszący [980 grup zasobów na subskrypcję](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager). 

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
Wykonaj następujące kroki, aby określić grupę zasobów dla wszystkich maszyn wirtualnych tworzonych w środowisku laboratoryjnym. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** w menu nawigacji po lewej stronie. 
3. Wybierz z listy pozycję **DevTest Labs**.
4. Wybierz z listy labs, Twoje **laboratorium**.  
5. Wybierz **konfiguracji i zasad** w **ustawienia** sekcji, w menu po lewej stronie. 
6. Wybierz **ustawienia Lab** w menu po lewej stronie. 
7. Wybierz **wszystkie maszyny wirtualne w jednej grupie zasobów**. 
8. Wybierz istniejącą grupę zasobów na rozwijanej liście (lub) wybierz **Utwórz nową**, wprowadź **nazwa** dla grupy zasobów, a następnie wybierz **OK**. 

    ![Wybierz grupę zasobów dla wszystkich maszyn wirtualnych w laboratorium](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Korzystanie z programu PowerShell 
Poniższy przykład pokazuje, jak używać skryptu programu PowerShell do tworzenia wszystkich maszyn wirtualnych laboratorium w nowej grupie zasobów.

```powershell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

Wywołania skryptu za pomocą następującego polecenia. ResourceGroup.ps1 to plik, który zawiera powyższy skrypt:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Użyj szablonu usługi Azure Resource Manager
Jeśli używasz szablonu usługi Azure Resource Manager, aby utworzyć laboratorium, należy użyć **vmCreationResourceGroupId** właściwości w sekcji właściwości laboratorium szablonu, jak pokazano w poniższym przykładzie:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>Interfejs API, aby skonfigurować grupę zasobów dla maszyn wirtualnych laboratorium
Masz następujące opcje jako właściciel laboratorium, korzystając z tego interfejsu API:

- Wybierz **grupy zasobów laboratorium** dla wszystkich maszyn wirtualnych.
- Wybierz **istniejącą grupę zasobów** innej niż grupa zasobów laboratorium dla wszystkich maszyn wirtualnych.
- Wprowadź **nową grupę zasobów** nazwy wszystkich maszyn wirtualnych.
- Kontynuuj korzystanie z istniejącego zachowania, w której grupa zasobów jest tworzona dla każdej maszyny Wirtualnej w środowisku laboratoryjnym.
 
To ustawienie dotyczy nowe maszyny wirtualne utworzone w środowisku laboratoryjnym. Starsze maszyny wirtualne w laboratorium, które zostały utworzone w ich własnych grupach zasobów pozostaną niezmienione. Środowisk, które są tworzone w środowisku laboratoryjnym nadal pozostać w ich własnych grupach zasobów.

Jak używać tego interfejsu API:
- Użyj interfejsu API w wersji **2018_10_15_preview**.
- Jeśli określisz nową grupę zasobów, upewnij się, że **uprawnień do zapisu na temat grup zasobów** w ramach subskrypcji. Jeśli nie masz uprawnień do zapisu, tworzenie nowych maszyn wirtualnych w określonej grupie zasobów zakończy się niepowodzeniem.
- Podczas korzystania z interfejsu API, przekazywanie **pełny identyfikator grupy zasobów**. Na przykład: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Upewnij się, że grupa zasobów znajduje się w tej samej subskrypcji co laboratorium. 


## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły: 

- [Ustawianie zasad laboratorium](devtest-lab-get-started-with-lab-policies.md)
- [Często zadawane pytania](devtest-lab-faq.md)
