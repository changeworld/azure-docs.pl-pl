---
title: Tworzenie maszyn wirtualnych uruchamiających stos SQL&#47;IIS&#47;.NET na platformie Azure| Microsoft Docs
description: Z tego samouczka dowiesz się, jak zainstalować stos Azure SQL, IIS i.NET na maszynie wirtualnej z systemem Windows na platformie Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f9fea933dd664955a0bc6f47db775fbc469fd684
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="tutorial-install-the-sql47iis47net-stack-in-a-windows-vm-with-azure-powershell"></a>Samouczek: Instalowanie stosu SQL&#47;IIS&#47;.NET na maszynie wirtualnej z systemem Windows przy użyciu programu Azure PowerShell

W tym samouczku instalujemy stos SQL&#47;IIS&#47;.NET przy użyciu programu Azure PowerShell. Ten stos składa się z dwóch maszyn wirtualnych z systemem Windows Server 2016 — jednej z usługami IIS i platformą .NET oraz drugiej z programem SQL Server.

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej 
> * Instalacja usługi IIS i zestawu .NET Core SDK na maszynie wirtualnej
> * Tworzenie maszyny wirtualnej z programem SQL Server
> * Instalacja rozszerzenia programu SQL Server

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu AzureRM.Compute w wersji 5.7.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM.Compute`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-iis-vm"></a>Tworzenie maszyny wirtualnej usług IIS 

W tym przykładzie polecenie cmdlet [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) w programie PowerShell w usłudze Cloud Shell jest używane w celu szybkiego utworzenia maszyny wirtualnej systemu Windows Server 2016, a następnie zainstalowania usług IIS i platformy .NET Framework. Maszyny wirtualne usług IIS i programu SQL współużytkują grupę zasobów i sieć wirtualną, dlatego tworzone są zmienne dla tych nazw.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

Zainstaluj usługi IIS i platformę .NET przy użyciu niestandardowego rozszerzenia skryptu.

```azurepowershell-interactive
Set-AzureRmVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>Tworzenie innej podsieci

Utwórz drugą podsieć dla maszyny wirtualnej programu SQL. Pobierz moduł vNet polecenia [Get-AzureRmVirtualNetwork]{/powershell/module/azurerm.network/get-azurermvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzureRmVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Utwórz konfigurację podsieci przy użyciu polecenia [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig).


```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Zaktualizuj moduł vNet przy użyciu informacji o nowej podsieci za pomocą polecenia [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)
   
```azurepowershell-interactive   
$vNet | Set-AzureRmVirtualNetwork
```

## <a name="azure-sql-vm"></a>Maszyna wirtualna Azure SQL

Użyj wstępnie skonfigurowanego obrazu serwera SQL z platformy handlowej Azure, aby utworzyć maszynę wirtualną programu SQL. Należy najpierw utworzyć maszynę wirtualną, a następnie zainstalować rozszerzenie programu SQL Server na maszynie wirtualnej. 


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

Użyj polecenia [Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension), aby dodać [rozszerzenie programu SQL Server](/sql/virtual-machines-windows-sql-server-agent-extension.md) do maszyny wirtualnej SQL.

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku zainstalowano stos SQL&#92;IIS&#92;.NET przy użyciu programu Azure PowerShell. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej 
> * Instalacja usługi IIS i zestawu .NET Core SDK na maszynie wirtualnej
> * Tworzenie maszyny wirtualnej z programem SQL Server
> * Instalacja rozszerzenia programu SQL Server

Przejdź do następnego samouczka, aby dowiedzieć się, jak zabezpieczyć serwer internetowy usług IIS przy użyciu certyfikatów SSL.

> [!div class="nextstepaction"]
> [Zabezpieczanie serwera internetowego usług IIS przy użyciu certyfikatów SSL](tutorial-secure-web-server.md)

