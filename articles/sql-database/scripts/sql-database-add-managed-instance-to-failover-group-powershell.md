---
title: Przykład programu PowerShell — grupa trybu failover — wystąpienie zarządzanego usługi Azure SQL Database
description: Przykładowy skrypt programu Azure PowerShell do utworzenia wystąpienia zarządzanego usługi Azure SQL Database, dodania go do grupy trybu failover i przetestowania trybu failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: e50877f6f3194885b139683fe865144384716b48
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691763"
---
# <a name="use-powershell-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>Dodawanie wystąpienia zarządzanego bazy danych SQL usługi Azure do grupy trybu failover za pomocą programu PowerShell 

W tym przykładowym przykładzie skryptu programu PowerShell utworzy dwa wystąpienia zarządzane, dodaje je do grupy trybu failover, a następnie testuje przegrupowanie w błąd z podstawowego wystąpienia zarządzanego do pomocniczego wystąpienia zarządzanego. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać programu PowerShell lokalnie, ten samouczek wymaga programu AZ PowerShell 1.4.0 lub nowszego. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-scripts"></a>Przykładowe skrypty

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby. Należy usunąć grupę zasobów dwa razy. Usunięcie grupy zasobów po raz pierwszy spowoduje usunięcie wystąpienia zarządzanego i klastrów `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`wirtualnych, ale spowoduje niepowodzenie z komunikatem o błędzie . Uruchom polecenie Usuń-AzResourceGroup po raz drugi, aby usunąć wszystkie zasoby resztkowe, a także grupę zasobów.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów platformy Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Dodaje konfigurację podsieci do sieci wirtualnej. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Pobiera sieć wirtualną w grupie zasobów. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Pobiera podsieci w sieci wirtualnej. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Tworzy sieciową grupę zabezpieczeń. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Tworzy tabelę tras. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualizuje konfigurację podsieci dla sieci wirtualnej.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje sieć wirtualną.  |
| [Grupa bezpieczeństwa Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Pobiera grupę zabezpieczeń sieci. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Dodaje konfigurację reguł zabezpieczeń sieciowych do sieciowej grupy zabezpieczeń. |
| [Grupa bezpieczeństwa Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualizuje sieciową grupę zabezpieczeń.  | 
| [Dodatek AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Dodaje trasę do tabeli tras. |
| [Tabela Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualizuje tabelę tras.  |
| [Nowy-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Tworzy wystąpienie zarządzanej usługi Azure SQL Database.  |
| [Get-AzSqlInstance (Nieumieja)](/powershell/module/az.sql/get-azsqlinstance)| Zwraca informacje o wystąpieniu zarządzanej bazy danych SQL platformy Azure. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Tworzy publiczny adres IP.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Tworzy konfigurację IP dla bramy sieci wirtualnej |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Tworzy bramę sieci wirtualnej |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Tworzy połączenie między dwiema bramami sieci wirtualnej.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Tworzy nową grupę trybu failover wystąpienia zarządzanego usługi Azure SQL Database.  |
| [Grupa Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Pobiera lub wyświetla listy grup trybu failover wystąpienia zarządzanego.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Wykonuje przeżycie awaryjne grupy trybu failover wystąpienia zarządzanego. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów. | 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).
