---
title: Przykładowy kod JSON dla usługi Azure Policy — inicjatywa zasad tagów rozliczeń | Microsoft Docs
description: Ten przykładowy kod JSON zestawu zasad wymaga określonych wartości tagów centrum kosztu i nazwy produktu.
services: azure-policy
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-policy
ms.devlang: ''
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 10/30/2017
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 45dea9bb8ed6826d0c95a8a89e4270b82503a262
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="billing-tags-policy-initiative"></a>Inicjatywa zasad tagów rozliczeń

Ten zestaw zasad wymaga określonych wartości tagów centrum kosztu i nazwy produktu. Przy użyciu wbudowanych zasad określa wymagane tagi i wymusza ich stosowanie. Wymagane wartości tagów są określane przez Ciebie.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Przykładowy szablon

[!code-json[main](../../../policy-templates/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.json "Billing Tags Policy Initiative")]

Można wdrożyć ten szablon przy użyciu [witryny Azure Portal](#deploy-with-the-portal) lub [programu PowerShell](#deploy-with-powershell).

## <a name="deploy-with-the-portal"></a>Wdrażanie przy użyciu portalu

[![Wdrażanie na platformie Azure](http://azuredeploy.net/deploybutton.png)](https://aka.ms/getpolicy)

## <a name="deploy-with-powershell"></a>Wdrażanie przy użyciu programu PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$policydefinitions = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.definitions.json"
$policysetparameters = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.parameters.json"

$policyset= New-AzureRmPolicySetDefinition -Name "multiple-billing-tags" -DisplayName "Billing Tags Policy Initiative" -Description "Specify cost Center tag and product name tag" -PolicyDefinition $policydefinitions -Parameter $policysetparameters

New-AzureRmPolicyAssignment -PolicySetDefinition $policyset -Name <assignmentname> -Scope <scope>  -costCenterValue <required value for Cost Center tag> -productNameValue <required value for product Name tag>  -Sku @{"Name"="A1";"Tier"="Standard"}
```

### <a name="clean-up-powershell-deployment"></a>Czyszczenie po wdrożeniu przy użyciu PowerShell

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="apply-tags-to-existing-resources"></a>Stosowanie tagów do istniejących zasobów

Po przypisaniu zasad możesz uruchomić aktualizację wszystkich istniejących zasobów, aby wymusić stosowanie dodanych zasad tagów. Poniższy skrypt zachowuje wszelkie wcześniej istniejące tagi zasobów:

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($r.Tags -eq $NULL) { @{}} else {$r.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```


## <a name="next-steps"></a>Następne kroki

- Dodatkowe przykłady szablonów usługi Azure Policy można znaleźć na stronie [Templates for Azure Policy (Szablony dla usługi Azure Policy)](../json-samples.md).
