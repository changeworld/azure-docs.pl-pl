---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: e81cdb478a63e1e584aef2c32754bd321d245365
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440344"
---
## <a name="start-your-powershell-session"></a>Uruchamianie sesji programu PowerShell
Najpierw musisz mieć najnowsze [programu Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) zainstalowana i uruchomiona. Aby uzyskać szczegółowe informacje, zobacz artykuł [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Instalowanie i konfigurowanie programu Azure PowerShell).

> [!NOTE]
> Przykłady w tym temacie [modelu wdrażania usługi Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md), dlatego w przykładach użyto [poleceń cmdlet usługi Azure Resource Manager](https://msdn.microsoft.com/library/azure/mt125356.aspx). 
> 
> 

Uruchom [ **Connect-AzureRmAccount** ](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) polecenia cmdlet zostanie wyświetlony ekran logowania, w którym należy wprowadzić poświadczenia. Użyj tych samych poświadczeń, których używasz do logowania w witrynie Azure Portal.

    Connect-AzureRmAccount

Jeśli masz wiele subskrypcji, użyj polecenia cmdlet [**Set-AzureRmContext**](https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext) i wybierz subskrypcję, która ma być użyta w sesji programu PowerShell. Aby sprawdzić, która subskrypcja jest używana w bieżącej sesji programu PowerShell, uruchom polecenie [**Get-AzureRmContext**](https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermcontext). Aby wyświetlić wszystkie subskrypcje, uruchom polecenie [**Get-AzureRmSubscription**](https://docs.microsoft.com/powershell/module/servicemanagement/azurerm.profile/get-azurermsubscription).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

