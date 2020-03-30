---
title: Określanie grupy zasobów dla maszyn wirtualnych w laboratoriach usługi Azure DevTest | Dokumenty firmy Microsoft
description: Dowiedz się, jak określić grupę zasobów dla maszyn wirtualnych w laboratorium w laboratoriach DevTest.
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
ms.openlocfilehash: 29816d158cf1428727b7ff17bcc2c347f402dedf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77134527"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Określanie grupy zasobów dla maszyn wirtualnych w laboratorium w laboratoriach devtest azure

Jako właściciel laboratorium można skonfigurować maszyny wirtualne laboratorium do utworzenia w określonej grupie zasobów. Ta funkcja pomaga w następujących scenariuszach:

- Mają mniej grup zasobów utworzonych przez laboratoria w ramach subskrypcji.
- Niech twoje laboratoria działają w ramach stałego zestawu grup zasobów, które można skonfigurować.
- Obejśj ograniczenia i zatwierdzenia wymagane do tworzenia grup zasobów w ramach subskrypcji platformy Azure.
- Skonsoliduj wszystkie zasoby laboratorium w ramach jednej grupy zasobów, aby uprościć śledzenie tych zasobów i stosowanie [zasad](../governance/policy/overview.md) do zarządzania zasobami na poziomie grupy zasobów.

Za pomocą tej funkcji można użyć skryptu, aby określić nową lub istniejącą grupę zasobów w ramach subskrypcji platformy Azure dla wszystkich maszyn wirtualnych w laboratorium. Obecnie usługa Azure DevTest Labs obsługuje tę funkcję za pośrednictwem interfejsu API.

> [!NOTE]
> Wszystkie limity subskrypcji mają zastosowanie podczas tworzenia laboratoriów w DevTest Labs. Pomyśl o laboratorium jak o każdym innym zasobie w ramach subskrypcji. W przypadku grup zasobów limit wynosi [980 grup zasobów na subskrypcję](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). 

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
Wykonaj następujące kroki, aby określić grupę zasobów dla wszystkich maszyn wirtualnych utworzonych w laboratorium. 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz **pozycję Wszystkie usługi** w lewym menu nawigacyjnym. 
3. Wybierz z listy pozycję **DevTest Labs**.
4. Z listy laboratoriów wybierz **laboratorium**.  
5. Wybierz **pozycję Konfiguracja i zasady** w sekcji **Ustawienia** w menu po lewej stronie. 
6. Wybierz **ustawienia laboratorium** w menu po lewej stronie. 
7. Wybierz **wszystkie maszyny wirtualne w jednej grupie zasobów**. 
8. Wybierz istniejącą grupę zasobów na liście rozwijanej (lub) wybierz **pozycję Utwórz nowy**, wprowadź **nazwę** grupy zasobów i wybierz **przycisk OK**. 

    ![Wybierz grupę zasobów dla wszystkich maszyn wirtualnych w laboratorium](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Korzystanie z programu PowerShell 
W poniższym przykładzie pokazano, jak używać skryptu programu PowerShell do tworzenia wszystkich maszyn wirtualnych w laboratorium w nowej grupie zasobów.

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

Wywołać skrypt za pomocą następującego polecenia. ResourceGroup.ps1 jest plikiem zawierającym poprzedni skrypt:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Używanie szablonu usługi Azure Resource Manager
Jeśli używasz szablonu Usługi Azure Resource Manager do utworzenia laboratorium, użyj **właściwości vmCreationResourceGroupId** w sekcji właściwości laboratorium szablonu, jak pokazano w poniższym przykładzie:

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


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>Interfejs API do konfigurowania grupy zasobów dla maszyn wirtualnych w laboratorium
Masz następujące opcje jako właściciel laboratorium podczas korzystania z tego interfejsu API:

- Wybierz **grupę zasobów laboratorium** dla wszystkich maszyn wirtualnych.
- Wybierz **istniejącą grupę zasobów** inną niż grupa zasobów laboratorium dla wszystkich maszyn wirtualnych.
- Wprowadź **nową** nazwę grupy zasobów dla wszystkich maszyn wirtualnych.
- Kontynuuj korzystanie z istniejącego zachowania, w którym grupa zasobów jest tworzona dla każdej maszyny Wirtualnej w laboratorium.
 
To ustawienie dotyczy nowych maszyn wirtualnych utworzonych w laboratorium. Starsze maszyny wirtualne w laboratorium, które zostały utworzone w ich własnych grup zasobów pozostają nienaruszone. Środowiska, które są tworzone w laboratorium nadal pozostają w swoich grupach zasobów.

Jak korzystać z tego interfejsu API:
- Użyj wersji interfejsu API **2018_10_15_preview**.
- Jeśli określisz nową grupę zasobów, upewnij się, że masz **uprawnienia do zapisu w grupach zasobów** w ramach subskrypcji. Jeśli nie masz uprawnień do zapisu, tworzenie nowych maszyn wirtualnych w określonej grupie zasobów zakończy się niepowodzeniem.
- Podczas korzystania z interfejsu API należy przekazać **pełny identyfikator grupy zasobów**. Na przykład: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Upewnij się, że grupa zasobów jest w tej samej subskrypcji co laboratorium. 


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły: 

- [Ustawianie zasad dla laboratorium](devtest-lab-get-started-with-lab-policies.md)
- [Często zadawane pytania](devtest-lab-faq.md)
