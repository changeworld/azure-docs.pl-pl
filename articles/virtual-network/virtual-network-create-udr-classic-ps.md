---
title: Kontrolowanie routingu w sieci wirtualnej Azure - PowerShell — Classic | Dokumentacja firmy Microsoft
description: Informacje o sposobie kontrolowania routingu w sieci wirtualnych za pomocą programu PowerShell | Classic
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
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
ms.openlocfilehash: 870beabc3f238764e362dc99565f350a2329f4ec
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Kontrolowanie routingu i używaniu urządzeń wirtualnych (klasyczne) przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Program PowerShell](tutorial-create-route-table-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](tutorial-create-route-table-cli.md)
> * [Szablon](virtual-network-create-udr-arm-template.md)
> * [PowerShell (klasyczny)](virtual-network-create-udr-classic-ps.md)
> * [Interfejs wiersza polecenia (klasyczny)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Przed rozpoczęciem pracy z zasobami platformy Azure należy pamiętać, że ma ona obecnie dwa modele wdrażania: za pomocą usługi Azure Resource Manager i model klasyczny. Przed rozpoczęciem pracy z dowolnym zasobem Azure należy zapoznać się z [modelami i narzędziami wdrażania](../azure-resource-manager/resource-manager-deployment-model.md). Dokumentację dotyczącą różnych narzędzi można wyświetlić, wybierając odpowiednią opcję w górnej części tego artykułu. W tym artykule opisano klasyczny model wdrażania.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Próbki programu Azure PowerShell poniższe polecenia oczekiwać środowisku niezłożonym już utworzone oparta na scenariuszu powyżej. Jeśli chcesz uruchomić polecenia wyświetlaną w tym dokumencie, należy utworzyć w środowisku pokazanym w [Utwórz sieć wirtualną (klasyczne) przy użyciu programu PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Utwórz przez podsieci frontonu
Aby utworzyć tabelę tras i trasy wymagane do podsieci frontonu oparte na powyższym scenariuszu, wykonaj poniższe kroki.

1. Uruchom następujące polecenie, aby utworzyć tabelę tras dla podsieci frontonu:

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. Uruchom następujące polecenie, aby utworzyć trasę w tabeli tras do wysyłania całego ruchu kierowanego do podsieci zaplecza (192.168.2.0/24), aby **FW1** maszyny Wirtualnej (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Uruchom następujące polecenie, aby skojarzyć tabela tras o **frontonu** podsieci:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>Utwórz przez podsieci wewnętrznej
Aby utworzyć tabelę tras i trasy wymagane do podsieci zaplecza opartą na tym scenariuszu, wykonaj następujące kroki:

1. Uruchom następujące polecenie, aby utworzyć tabelę tras dla podsieci wewnętrznej:

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. Uruchom następujące polecenie, aby utworzyć trasę w tabeli tras do wysyłania całego ruchu kierowanego do podsieci frontonu (192.168.1.0/24), aby **FW1** maszyny Wirtualnej (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Uruchom następujące polecenie, aby skojarzyć tabela tras o **zaplecza** podsieci:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Włącz przesyłanie dalej IP na Maszynie wirtualnej FW1

Aby włączyć funkcji przekazywania w Maszynie wirtualnej FW1 IP, wykonaj następujące kroki:

1. Uruchom następujące polecenie, aby sprawdzić stan przesyłanie dalej IP:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. Uruchom następujące polecenie, aby umożliwić przekazywanie IP *FW1* maszyny Wirtualnej:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```
