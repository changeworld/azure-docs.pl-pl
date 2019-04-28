---
title: Kontrolowanie routingu w usłudze Azure Virtual Network - PowerShell — Model Klasyczny | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kontrolować routingu w sieci wirtualnej przy użyciu programu PowerShell | Model Klasyczny
services: virtual-network
documentationcenter: na
author: genlin
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
ms.openlocfilehash: 1441ee9a3d4a563ab35cd9b01e8347d8f51b827a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743411"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Formant routingu i użyj urządzenia wirtualne (wersja klasyczna) przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Program PowerShell](tutorial-create-route-table-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](tutorial-create-route-table-cli.md)
> * [PowerShell (klasyczny)](virtual-network-create-udr-classic-ps.md)
> * [Interfejs wiersza polecenia (klasyczny)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Przed rozpoczęciem pracy z zasobami platformy Azure, ważne jest zrozumienie, że platforma Azure ma obecnie dwa modele wdrażania: Usługa Azure Resource Manager i model klasyczny. Przed rozpoczęciem pracy z dowolnym zasobem Azure należy zapoznać się z [modelami i narzędziami wdrażania](../azure-resource-manager/resource-manager-deployment-model.md). Możesz wyświetlić dokumentację dotyczącą różnych narzędzi, wybierając odpowiednią opcję w górnej części tego artykułu. W tym artykule opisano klasyczny model wdrażania.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Przykład programu Azure PowerShell poniższe polecenia oczekiwać proste środowisko, utworzonym na podstawie scenariusza powyżej. Jeśli chcesz uruchamiać polecenia, ponieważ są one wyświetlane w tym dokumencie, należy utworzyć w środowisku pokazanym w [tworzenie sieci wirtualnej (klasycznej) przy użyciu programu PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Utwórz trasę zdefiniowaną przez użytkownika dla podsieci frontonu
Aby utworzyć tabelę tras i trasy służące do podsieci frontonu, w oparciu o powyższy scenariusz, wykonaj następujące czynności.

1. Uruchom następujące polecenie, aby utworzyć tabelę tras dla podsieci frontonu:

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. Uruchom następujące polecenie, aby tworzyć trasy w tabeli tras, aby wysłać cały ruch kierowany do podsieci zaplecza (192.168.2.0/24), aby **FW1** maszyny Wirtualnej (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Uruchom następujące polecenie, aby skojarzyć tabeli trasy z **frontonu** podsieci:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>Utwórz trasę zdefiniowaną przez użytkownika dla podsieci zaplecza
Aby utworzyć tabelę tras i trasy służące do podsieci zaplecza na podstawie scenariusza, wykonaj następujące czynności:

1. Uruchom następujące polecenie, aby utworzyć tabelę tras dla podsieci zaplecza:

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. Uruchom następujące polecenie, aby tworzyć trasy w tabeli tras, aby wysłać cały ruch kierowany do podsieci frontonu (. 192.168.1.0/24), aby **FW1** maszyny Wirtualnej (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Uruchom następujące polecenie, aby skojarzyć tabeli trasy z **zaplecza** podsieci:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Włączanie przekazywania adresów IP na maszynie Wirtualnej FW1

Aby włączyć przekazywania na maszynie Wirtualnej FW1 adresu IP, wykonaj następujące czynności:

1. Uruchom następujące polecenie, aby sprawdzić stan przesyłania dalej IP:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. Uruchom następujące polecenie, aby włączyć przekazywanie IP *FW1* maszyny Wirtualnej:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```
