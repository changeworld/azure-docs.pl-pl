---
title: Sterowanie routingiem w Virtual Network platformy Azure — interfejs wiersza polecenia — klasyczny | Microsoft Docs
description: Dowiedz się, jak kontrolować Routing w programie sieci wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure w klasycznym modelu wdrażania
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 1193145b315175e6394db4caf93ab2e76a942ed9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058785"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Kontrolowanie routingu i używanie urządzeń wirtualnych (klasycznych) przy użyciu interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](tutorial-create-route-table-cli.md)
> * [PowerShell (klasyczny)](virtual-network-create-udr-classic-ps.md)
> * [Interfejs wiersza polecenia (klasyczny)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania. Można również [kontrolować Routing i używać urządzeń wirtualnych w Menedżer zasobów model wdrażania](tutorial-create-route-table-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Przykłady poleceń interfejsu wiersza polecenia platformy Azure poniżej oczekują prostego środowiska już utworzonego na podstawie powyższego scenariusza. Jeśli chcesz uruchomić polecenia w taki sposób, aby były wyświetlane w tym dokumencie, Utwórz środowisko widoczne w temacie [Tworzenie sieci wirtualnej (klasycznej) przy użyciu interfejsu wiersza polecenia platformy Azure](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Utwórz UDR dla podsieci frontonu
Aby utworzyć tabelę tras i trasę wymaganą dla podsieci frontonu na podstawie powyższego scenariusza, wykonaj poniższe kroki.

1. Uruchom następujące polecenie, aby przełączyć się do trybu klasycznego:

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
   
   * **-l (lub --location)** . Region świadczenia usługi Azure, w którym zostanie utworzony nowy sieciowej grupy zabezpieczeń. W naszym scenariuszu *zachodnie*.
   * **-n (lub --name)** . Nazwa nowego sieciowej grupy zabezpieczeń. W naszym scenariuszu *sieciowej grupy zabezpieczeń-frontonu*.
3. Uruchom następujące polecenie, aby utworzyć trasę w tabeli tras, aby wysłać cały ruch przychodzący do podsieci zaplecza (192.168.2.0/24) do maszyny wirtualnej **FW1** (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Dane wyjściowe:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Parametry:
   
   * **-r (lub--Route-Table-Name)** . Nazwa tabeli tras, w której zostanie dodana trasa. W naszym scenariuszu *UDR-frontonu*.
   * **-a (lub --address-prefix)** . Prefiks adresu podsieci, do której są przeznaczone pakiety. W naszym scenariuszu *192.168.2.0/24*.
   * **-t (lub--następny-typ)** . Typ ruchu obiektów zostanie wysłany do. Możliwe wartości to *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*lub *none*.
   * **-p (lub--Next-IP-Address**). Adres IP dla następnego przeskoku. W naszym scenariuszu *192.168.0.4*.
4. Uruchom następujące polecenie, aby skojarzyć tabelę tras utworzoną z podsiecią **frontonu** :

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
   
   * **-t (lub--VNET-Name)** . Nazwa sieci wirtualnej, w której znajduje się podsieć. W naszym scenariuszu jest to *TestVNet*.
   * **-n (lub--nazwa podsieci**. Nazwa podsieci, do której zostanie dodana tabela tras. W naszym scenariuszu jest to *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Utwórz UDR dla podsieci zaplecza
Aby utworzyć tabelę tras i trasę wymaganą dla podsieci zaplecza na podstawie tego scenariusza, wykonaj następujące czynności:

1. Uruchom następujące polecenie, aby utworzyć tabelę tras dla podsieci zaplecza:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Uruchom następujące polecenie, aby utworzyć trasę w tabeli tras, aby wysłać cały ruch przychodzący do podsieci frontonu (192.168.1.0/24) do maszyny wirtualnej **FW1** (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Uruchom następujące polecenie, aby skojarzyć tabelę tras z podsiecią **zaplecza** :

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

