---
title: Używanie modułu zasad usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ograniczyć subskrypcji platformy Azure, aby działały jak subskrypcją usługi Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: sethm
ms.openlocfilehash: b0236a790200feec7f1d16724f351882056b2cd5
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52678529"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Zarządzanie zasadami platformy Azure przy użyciu modułu zasad usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Moduł usługi Azure Stack zasad umożliwia skonfigurowanie subskrypcji platformy Azure przy użyciu tej samej wersji i dostępności usługi jako usługi Azure Stack. Moduł używa [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) polecenia cmdlet, aby utworzyć zasady platformy Azure, co ogranicza typy zasobów i usług dostępnych w ramach subskrypcji. Następnie utworzysz przypisanie zasad w ramach wybranego zakresu przy użyciu [New-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) polecenia cmdlet. Po skonfigurowaniu zasad, można użyć subskrypcji platformy Azure, tworzyć aplikacje przeznaczone dla usługi Azure Stack.

## <a name="install-the-module"></a>Zainstaluj moduł

1. Zainstaluj wymaganą wersję modułu AzureRM PowerShell, zgodnie z opisem w kroku 1 [Instalowanie programu PowerShell dla usługi Azure Stack](azure-stack-powershell-install.md).
2. [Pobierz narzędzia usługi Azure Stack z serwisu GitHub](azure-stack-powershell-download.md).
3. [Konfigurowanie programu PowerShell do użycia z usługą Azure Stack](azure-stack-powershell-configure-user.md).
4. Zaimportuj moduł AzureStack.Policy.psm1:

    ```PowerShell
    Import-Module .\Policy\AzureStack.Policy.psm1
    ```

## <a name="apply-policy-to-azure-subscription"></a>Zastosuj zasady do subskrypcji platformy Azure

Aby zastosować domyślne zasady usługi Azure Stack względem Twojej subskrypcji platformy Azure, można użyć następującego polecenia. Przed uruchomieniem tego polecenia, należy zastąpić `Azure Subscription Name` nazwą subskrypcji platformy Azure.

```PowerShell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure Subscription Name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>Zastosuj zasady do grupy zasobów

Można stosować zasady, które są bardziej szczegółowe. Na przykład może mieć inne zasoby działające na tej samej subskrypcji. Możesz ograniczyć stosowanie zasad do konkretnej grupy zasobów, które umożliwia testowanie aplikacji dla usługi Azure Stack przy użyciu zasobów platformy Azure. Przed uruchomieniem następującego polecenia, należy zastąpić `Azure Subscription Name` nazwą subskrypcji platformy Azure.

```PowerShell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure Subscription Name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>Zasady działania

Po wdrożeniu usługi Azure policy, wystąpi błąd podczas próby wdrożenia z zasobem, który jest zabronione przez zasady.

![Wynikiem Niepowodzenie wdrażania zasobów ze względu na ograniczenia zasad](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie szablonów za pomocą programu PowerShell](azure-stack-deploy-template-powershell.md)
* [Wdrażanie szablonów za pomocą wiersza polecenia platformy Azure](azure-stack-deploy-template-command-line.md)
* [Wdrażanie szablonów za pomocą programu Visual Studio](azure-stack-deploy-template-visual-studio.md)
