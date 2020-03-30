---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 05794a046fdcb15a91145a75717a6a454d15a8da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72511505"
---
Szyfrowanie dysków platformy Azure można włączyć i zarządzać za pośrednictwem [interfejsu wiersza polecenia platformy Azure](/cli/azure) i programu Azure [PowerShell.](/powershell/azure/new-azureps-module-az) Aby to zrobić, należy zainstalować narzędzia lokalnie i połączyć się z subskrypcją platformy Azure.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Narzędzie [cli platformy Azure 2.0](/cli/azure) to narzędzie wiersza polecenia do zarządzania zasobami platformy Azure. Cli jest przeznaczony do elastycznego wyszukiwania danych, obsługi długotrwałych operacji jako procesów nieblokujących i ułatwić wykonywanie skryptów. Można go zainstalować lokalnie, wykonując kroki opisane w [install the Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Aby [zalogować się do konta platformy Azure za pomocą interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure,](/cli/azure/authenticate-azure-cli)użyj polecenia [logowania az.](/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli
az login
```

Jeśli chcesz wybrać dzierżawcę, aby zalogować się w obszarze, użyj:
    
```azurecli
az login --tenant <tenant>
```

Jeśli masz wiele subskrypcji i chcesz określić konkretną, pobierz listę subskrypcji z [listą kont AZ](/cli/azure/account#az-account-list) i określ z [zestawem kont AZ](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Moduł Azure PowerShell az](/powershell/azure/new-azureps-module-az) zawiera zestaw poleceń cmdlet, które używają modelu [usługi Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) do zarządzania zasobami platformy Azure. Można go używać w przeglądarce za pomocą [usługi Azure Cloud Shell](/azure/cloud-shell/overview)lub można go zainstalować na komputerze lokalnym, korzystając z instrukcji w module Azure [PowerShell](/powershell/azure/install-az-ps). 

Jeśli masz już zainstalowany lokalnie, upewnij się, że używasz najnowszej wersji sdk azure powershell do konfigurowania szyfrowania dysku Azure. Pobierz najnowszą wersję [programu Azure PowerShell.](https://github.com/Azure/azure-powershell/releases)

Aby [zalogować się do konta platformy Azure za pomocą programu Azure PowerShell,](/powershell/azure/authenticate-azureps?view=azps-2.5.0)użyj polecenia cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```powershell
Connect-AzAccount
```

Jeśli masz wiele subskrypcji i chcesz je określić, użyj polecenia cmdlet [Get-AzSubscription,](/powershell/module/Az.Accounts/Get-AzSubscription) aby je wyświetlić, a następnie polecenie cmdlet [Set-AzContext:](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Uruchomienie polecenia cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) spowoduje sprawdzenie, czy wybrano poprawną subskrypcję.

Aby potwierdzić, że są zainstalowane polecenia cmdlet szyfrowania dysku Azure, użyj polecenia cmdlet get:To confirm the Azure Disk Encryption cmdlets are installed, use the [Get-command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) commanddlet:
     
```powershell
Get-command *diskencryption*
```
Aby uzyskać więcej informacji, zobacz [Wprowadzenie do programu Azure PowerShell](/powershell/azure/get-started-azureps). 