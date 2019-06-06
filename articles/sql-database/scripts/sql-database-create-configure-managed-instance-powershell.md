---
title: Przykład programu PowerShell — Tworzenie wystąpienia zarządzanego usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Przykładowy skrypt Azure PowerShell do tworzenia wystąpienia zarządzanego usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 929ab995ea76fa0d1d5227e3a53c2b50bc43fdc0
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729375"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Użyj programu PowerShell do utworzenia usługi Azure SQL Database, wystąpienia zarządzanego

Ten przykładowy skrypt programu PowerShell tworzy wystąpienia zarządzanego usługi Azure SQL Database w dedykowanej podsieci sieci wirtualnej. Konfiguruje również tabelę tras i sieciowej grupy zabezpieczeń dla sieci wirtualnej. Po pomyślnym uruchomieniu skryptu wystąpienia zarządzanego jest możliwy z w ramach sieci wirtualnej lub ze środowiska lokalnego. Zobacz [Konfigurowanie maszyny Wirtualnej platformy Azure nawiązać wystąpienia usługi Azure SQL Database Managed](../sql-database-managed-instance-configure-vm.md) i [Konfigurowanie połączenia typu punkt lokacja do wystąpienia usługi Azure SQL Database Managed ze środowiska lokalnego](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Aby uzyskać informacje o ograniczeniach, zobacz [obsługiwane regiony](../sql-database-managed-instance-resource-limits.md#supported-regions) i [obsługiwane typy subskrypcji](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i korzystać z programu PowerShell lokalnie, ten samouczek wymaga programu PowerShell AZ 1.4.0 lub nowszej. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Dodaje konfigurację podsieci w sieci wirtualnej |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Pobiera z sieci wirtualnej w grupie zasobów |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Ustawia stan docelowy dla sieci wirtualnej |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Pobiera podsieci w sieci wirtualnej |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Konfiguruje stan docelowy dla konfiguracji podsieci w sieci wirtualnej |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Tworzy tabelę tras |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Pobiera tabele tras |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Ustawia stan docelowy dla tabeli tras |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Tworzy wystąpienie zarządzane usługi Azure SQL Database |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).
