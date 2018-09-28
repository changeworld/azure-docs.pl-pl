---
title: Przykładowy skrypt programu Azure PowerShell — tworzenie środowiska testowego usługi Azure Firewall
description: Przykładowy skrypt programu Azure PowerShell — tworzenie środowiska testowego usługi Azure Firewall.
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 8/13/2018
ms.author: victorh
ms.openlocfilehash: 23f10280cd34927e2e74cb7c5001850bedc6dd35
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967544"
---
# <a name="create-an-azure-firewall-test-environment"></a>Tworzenie środowiska testowego usługi Azure Firewall

Ten przykładowy skrypt tworzy zaporę i testowe środowisko sieciowe. Sieć ma jedną sieć wirtualną z trzema podsieciami: *AzureFirewallSubnet*, *ServersSubnet* i *JumpboxSubnet*. Podsieci ServersSubnet i JumpboxSubnet obejmują po jednej 2-rdzeniowej maszynie z systemem Windows Server.

Zapora znajduje się w podsieci AzureFirewallSubnet i jest w niej skonfigurowana kolekcja reguł aplikacji z pojedynczą regułą zezwalającą na dostęp do witryny www.microsoft.com.

Tworzona jest trasa zdefiniowana przez użytkownika, która kieruje ruch sieciowy z podsieci ServersSubnet przez zaporę, gdzie są stosowane reguły zapory.

Skrypt można uruchomić z poziomu usługi Azure [Cloud Shell](https://shell.azure.com/powershell) lub z lokalnej instalacji programu PowerShell. 

Jeśli uruchamiasz program PowerShell lokalnie, ten skrypt wymaga najnowszej wersji modułu AzureRM PowerShell. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `Get-Module -ListAvailable AzureRM`. 

Jeśli konieczne jest uaktualnienie, możesz użyć modułu `PowerShellGet`, który jest wbudowany w system Windows 10 i Windows Server 2016.

> [!NOTE]
>Inne wersje systemu Windows wymagają zainstalowania modułu `PowerShellGet`, zanim będzie można go użyć. Aby określić, czy ten moduł jest zainstalowany w Twoim systemie, możesz uruchomić polecenie `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path`. Jeśli dane wyjściowe są puste, musisz zainstalować najnowszą wersję pakietu [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616).

Aby uzyskać więcej informacji, zobacz [Instalowanie programu Azure PowerShell w systemie Windows za pomocą modułu PowerShellGet](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0)

Dowolna istniejąca instalacja programu Azure PowerShell zainstalowana za pomocą Instalatora platformy internetowej będzie powodować konflikt z instalacją modułu PowerShellGet i musi zostać usunięta.

Pamiętaj, że jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby:

```powershell
Remove-AzureRmResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, sieci wirtualnej i sieciowych grup zabezpieczeń. Każde polecenie w poniższej tabeli stanowi link do dokumentacji polecenia:

| Polecenie | Uwagi |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Tworzy obiekt konfiguracji podsieci. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Tworzy sieć wirtualną i podsieć frontonu platformy Azure. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Tworzy reguły zabezpieczeń do przypisania do sieciowej grupy zabezpieczeń. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) |Tworzy reguły sieciowych grup zabezpieczeń, które zezwalają na użycie określonych portów w wybranych sieciach lub blokują te porty. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | Tworzy skojarzenia sieciowych grup zabezpieczeń z podsieciami. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Tworzy publiczny adres IP umożliwiający uzyskiwanie dostępu do maszyny wirtualnej z Internetu. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Tworzy interfejsy sieci wirtualnej i dołącza je do podsieci frontonu i zaplecza sieci wirtualnej. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Tworzy konfigurację maszyny wirtualnej. Ta konfiguracja zawiera informacje, takie jak nazwa maszyny wirtualnej, system operacyjny i poświadczenia administracyjne. Jest ona używana podczas tworzenia maszyny wirtualnej. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Tworzy maszynę wirtualną. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte. |
|[New-AzureRmFirewall](https://github.com/Azure/azure-powershell/blob/Networking-AzureFirewall/src/ResourceManager/Network/Commands.Network/help/New-AzureRmFirewall.md)| Tworzy nową usługę Azure Firewall.|
|[Get-AzureRmFirewall](https://github.com/Azure/azure-powershell/blob/Networking-AzureFirewall/src/ResourceManager/Network/Commands.Network/help/Get-AzureRmFirewall.md)|Pobiera obiekt usługi Azure Firewall.|
|[New-AzureRmFirewallApplicationRule](https://github.com/Azure/azure-powershell/blob/Networking-AzureFirewall/src/ResourceManager/Network/Commands.Network/help/New-AzureRmFirewallApplicationRule.md)|Tworzy nową regułę aplikacji usługi Azure Firewall.|
|[Set-AzureRmFirewall](https://github.com/Azure/azure-powershell/blob/Networking-AzureFirewall/src/ResourceManager/Network/Commands.Network/help/Set-AzureRmFirewall.md)|Zatwierdza zmiany w obiekcie usługi Azure Firewall.|


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

