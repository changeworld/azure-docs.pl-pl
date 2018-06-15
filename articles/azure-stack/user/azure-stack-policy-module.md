---
title: Użycie modułu zasad stosu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ograniczyć subskrypcji platformy Azure, aby działały jak subskrypcji platformy Azure stosu
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: mabrigg
ms.openlocfilehash: 538cf0eb0f9f2351f7a71a1dd24aab05938963c5
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259087"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Zarządzanie zasadami Azure za pomocą modułu zasad stosu Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Moduł zasad stosu Azure umożliwia skonfigurowanie subskrypcji platformy Azure przy użyciu tej samej wersji i dostępności usługi jako stosu Azure.  Moduł używa **AzureRMPolicyAssignment nowy** polecenia cmdlet, aby utworzyć zasady Azure, co ogranicza typów zasobów i usług dostępnych w ramach subskrypcji.  Po skonfigurowaniu zasad, subskrypcji platformy Azure służy do opracowywania aplikacji przeznaczony dla platformy Azure stosu.

## <a name="install-the-module"></a>Zainstaluj moduł

1. Zainstaluj wymaganą wersję AzureRM modułu programu PowerShell, zgodnie z opisem w krok 1 z [Zainstaluj program PowerShell Azure stosu](azure-stack-powershell-install.md).
2. [Pobieranie narzędzia Azure stosu z usługi GitHub](azure-stack-powershell-download.md)
3. [Configure PowerShell for use with Azure Stack](azure-stack-powershell-configure-user.md) (Konfigurowanie programu PowerShell do używania z usługą Azure Stack)

4. Zaimportuj moduł AzureStack.Policy.psm1:

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Stosowania zasad dla subskrypcji platformy Azure

Następujące polecenie służy do zastosowania domyślne zasady usługi Azure stosu dla Twojej subskrypcji platformy Azure. Przed uruchomieniem tego polecenia należy zastąpić *Nazwa subskrypcji Azure* z subskrypcją platformy Azure.

```PowerShell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>Zastosuj zasady grupy zasobów

Można stosować zasady, które są bardziej szczegółowego. Na przykład może mieć inne zasoby w tej samej subskrypcji. Można określić zakres stosowania zasad do określonej grupy zasobów, co umożliwia testowanie aplikacji stosu Azure przy użyciu zasobów platformy Azure. Przed uruchomieniem następujące polecenie, Zastąp *Nazwa subskrypcji Azure* nazwą Twojej subskrypcji platformy Azure.

```PowerShell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>Zasady działania

Po wdrożeniu zasad platformy Azure, wystąpi błąd podczas próby wdrożenia z zasobem, który jest zabronione przez zasady.

![Wynik Niepowodzenie wdrażania zasobów z powodu ograniczenia zasad](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie szablonów za pomocą programu PowerShell](azure-stack-deploy-template-powershell.md)
* [Wdrażanie szablonów z wiersza polecenia platformy Azure](azure-stack-deploy-template-command-line.md)
* [Wdrażanie szablonów z programem Visual Studio](azure-stack-deploy-template-visual-studio.md)
