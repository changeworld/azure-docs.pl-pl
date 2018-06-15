---
title: Kontrolowanie routingu w klasycznym Azure Virtual Network - CLI - | Dokumentacja firmy Microsoft
description: Informacje o sposobie kontrolowania routingu w sieci wirtualnych w klasycznym modelu wdrażania przy użyciu wiersza polecenia platformy Azure
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: ca2b4638-8777-4d30-b972-eb790a7c804f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 0b6c8da03c4a67aadb38280ba958a9b0feb88d1f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793889"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Kontrolowanie routingu i używaniu urządzeń wirtualnych (klasyczne) przy użyciu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Program PowerShell](tutorial-create-route-table-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](tutorial-create-route-table-cli.md)
> * [PowerShell (klasyczny)](virtual-network-create-udr-classic-ps.md)
> * [Interfejs wiersza polecenia (klasyczny)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania. Możesz również [kontrolować routingu i używaniu urządzeń wirtualnych w modelu wdrażania usługi Resource Manager](tutorial-create-route-table-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Poniższe przykładowe polecenia interfejsu wiersza polecenia Azure oczekiwać środowisku niezłożonym już utworzone w zależności od scenariusza powyżej. Jeśli chcesz uruchomić polecenia wyświetlaną w tym dokumencie, należy utworzyć w środowisku pokazanym w [Utwórz sieć wirtualną (klasyczne) przy użyciu interfejsu wiersza polecenia Azure](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Utwórz przez podsieci frontonu
Aby utworzyć tabelę tras i trasy wymagane do podsieci frontonu oparte na powyższym scenariuszu, wykonaj poniższe kroki.

1. Uruchom następujące polecenie, aby przełączyć do trybu klasycznego:

    ```azurecli
    azure config mode asm
    ```

    Dane wyjściowe:

        info:    New mode is asm

2. Uruchom następujące polecenie, aby utworzyć tabelę tras dla podsieci frontonu:

    ```azurecli
    azure network route-table create -n UDR-FrontEnd -l uswest
    ```
   
    Dane wyjściowe:
   
        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK
   
    Parametry:
   
   * **-l (lub --location)**. Region platformy Azure, w którym zostanie utworzona nowa grupa NSG. W naszym scenariuszu *westus*.
   * **-n (lub --name)**. Nazwa nowej grupy NSG. W naszym scenariuszu *frontonu NSG*.
3. Uruchom następujące polecenie, aby utworzyć trasę w tabeli tras do wysyłania całego ruchu kierowanego do podsieci zaplecza (192.168.2.0/24), aby **FW1** maszyny Wirtualnej (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Dane wyjściowe:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Parametry:
   
   * **-r (lub--nazwa tabeli tras)**. Nazwa tabeli tras, w którym można dodać trasy. W naszym scenariuszu *frontonu przez*.
   * **-a (lub --address-prefix)**. Prefiks adresu podsieci, gdy pakiety są przeznaczone do. W naszym scenariuszu *192.168.2.0/24*.
   * **-t (lub--następnego przeskoku typu)**. Typ obiektu ruchu zostaną wysłane do. Możliwe wartości to *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*, lub *Brak*.
   * **-p (lub--dalej przeskoku — adres ip**). Adres IP następnego przeskoku. W naszym scenariuszu *192.168.0.4*.
4. Uruchom następujące polecenie, aby skojarzyć utworzone za pomocą tabeli tras **frontonu** podsieci:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Dane wyjściowe:
   
        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK    
   
    Parametry:
   
   * **-t (lub--vnet-name)**. Nazwa sieci wirtualnej, w którym znajduje się podsieci. W naszym scenariuszu jest to *TestVNet*.
   * **-n (lub--nazwy podsieci**. Nazwa tabeli tras zostanie dodany do podsieci. W naszym scenariuszu jest to *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Utwórz przez podsieci wewnętrznej
Aby utworzyć tabelę tras i trasy wymagane do podsieci zaplecza opartą na tym scenariuszu, wykonaj następujące kroki:

1. Uruchom następujące polecenie, aby utworzyć tabelę tras dla podsieci wewnętrznej:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Uruchom następujące polecenie, aby utworzyć trasę w tabeli tras do wysyłania całego ruchu kierowanego do podsieci frontonu (192.168.1.0/24), aby **FW1** maszyny Wirtualnej (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Uruchom następujące polecenie, aby skojarzyć tabela tras o **zaplecza** podsieci:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

