---
title: Sterowanie routingiem w sieci wirtualnej platformy Azure — PowerShell — klasyczny
description: Dowiedz się, jak kontrolować Routing w programie sieci wirtualnych przy użyciu programu PowerShell | Motyw
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: d8d07c16-cbe5-4536-acd6-870269346fe3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: 4170418b4f53277a8f4306fe2835286c8ac99dee
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186354"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Kontrolowanie routingu i używanie urządzeń wirtualnych (klasycznych) przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Program PowerShell](tutorial-create-route-table-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](tutorial-create-route-table-cli.md)
> * [PowerShell (klasyczny)](virtual-network-create-udr-classic-ps.md)
> * [Interfejs wiersza polecenia (klasyczny)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Przed rozpoczęciem pracy z zasobami platformy Azure należy pamiętać, że ma ona obecnie dwa modele wdrażania: za pomocą usługi Azure Resource Manager i model klasyczny. Przed rozpoczęciem pracy z dowolnym zasobem Azure należy zapoznać się z [modelami i narzędziami wdrażania](../azure-resource-manager/resource-manager-deployment-model.md). Można wyświetlić dokumentację dla różnych narzędzi, wybierając opcję w górnej części tego artykułu. W tym artykule opisano klasyczny model wdrażania.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Przykładowe polecenia Azure PowerShell oczekiwano, że proste środowisko zostało już utworzone na podstawie powyższego scenariusza. Jeśli chcesz uruchomić polecenia w taki sposób, aby były wyświetlane w tym dokumencie, Utwórz środowisko widoczne w temacie [Tworzenie sieci wirtualnej (klasycznej) przy użyciu programu PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Utwórz UDR dla podsieci frontonu
Aby utworzyć tabelę tras i trasę wymaganą dla podsieci frontonu na podstawie powyższego scenariusza, wykonaj poniższe kroki.

1. Uruchom następujące polecenie, aby utworzyć tabelę tras dla podsieci frontonu:

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. Uruchom następujące polecenie, aby utworzyć trasę w tabeli tras, aby wysłać cały ruch przychodzący do podsieci zaplecza (192.168.2.0/24) do maszyny wirtualnej **FW1** (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Uruchom następujące polecenie, aby skojarzyć tabelę tras z podsiecią **frontonu** :

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>Utwórz UDR dla podsieci zaplecza
Aby utworzyć tabelę tras i trasę wymaganą dla podsieci zaplecza na podstawie tego scenariusza, wykonaj następujące czynności:

1. Uruchom następujące polecenie, aby utworzyć tabelę tras dla podsieci zaplecza:

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. Uruchom następujące polecenie, aby utworzyć trasę w tabeli tras, aby wysłać cały ruch przychodzący do podsieci frontonu (192.168.1.0/24) do maszyny wirtualnej **FW1** (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Uruchom następujące polecenie, aby skojarzyć tabelę tras z podsiecią **zaplecza** :

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Włączanie przekazywania adresów IP na maszynie wirtualnej FW1

Aby włączyć przekazywanie adresów IP na maszynie wirtualnej FW1, wykonaj następujące czynności:

1. Uruchom następujące polecenie, aby sprawdzić stan przekazywania adresów IP:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. Uruchom następujące polecenie, aby włączyć przekazywanie adresów IP dla maszyny wirtualnej *FW1* :

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```
