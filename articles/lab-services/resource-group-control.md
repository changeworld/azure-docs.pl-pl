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
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: e4e2a01bbac7aebb70852b93c51c32933cc75eec
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652182"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Określ grupę zasobów dla maszyn wirtualnych laboratorium Azure DevTest Labs

Jako właściciel laboratorium możesz skonfigurować maszyny wirtualne laboratorium ma być utworzony w określonej grupie zasobów. Ta funkcja pomaga w następujących scenariuszach:

- Ma mniejszą liczbę grup zasobów utworzonych przez laboratoria w ramach subskrypcji.
- Ma działać w ramach ustalony zestaw elementów grupy zasobów, które można skonfigurować laboratorium.
- Obejścia ograniczeń i zatwierdzeń wymagane do utworzenia grupy zasobów w ramach subskrypcji platformy Azure.
- Konsolidacja wszystkich zasobów laboratorium w ramach pojedynczej grupy zasobów ułatwiają śledzenie tych zasobów i stosowanie [zasady](../governance/policy/overview.md) do zarządzania zasobami na poziomie grupy zasobów.

Dzięki tej funkcji można użyć skryptu do określenia nowej lub istniejącej grupy zasobów w ramach subskrypcji platformy Azure dla wszystkich laboratorium, maszyny wirtualne. Obecnie usługa Azure DevTest Labs obsługuje tę funkcję za pomocą interfejsu API.

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

## <a name="use-powershell"></a>Korzystanie z programu PowerShell 
Poniższy przykład pokazuje, jak używać skryptu programu PowerShell do tworzenia wszystkich maszyn wirtualnych laboratorium w nowej grupie zasobów.

```PowerShell
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

```PowerShell
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


## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły: 

- [Ustawianie zasad laboratorium](devtest-lab-get-started-with-lab-policies.md)
- [Często zadawane pytania](devtest-lab-faq.md)
