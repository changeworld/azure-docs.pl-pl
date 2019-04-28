---
title: Kontrolowanie routingu w sieci wirtualnej platformy Azure - CLI - Classic | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kontrolować routingu w sieci wirtualnej w klasycznym modelu wdrażania przy użyciu wiersza polecenia platformy Azure
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
ms.openlocfilehash: e1b8bb3544a08b60564ceb5bd7e1666214059e09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743925"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Kontrolowanie, routingu i używaniu urządzeń wirtualnych (wersja klasyczna), przy użyciu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Program PowerShell](tutorial-create-route-table-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](tutorial-create-route-table-cli.md)
> * [PowerShell (klasyczny)](virtual-network-create-udr-classic-ps.md)
> * [Interfejs wiersza polecenia (klasyczny)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania. Możesz również [sterować routingiem i używaniu urządzeń wirtualnych w modelu wdrażania usługi Resource Manager](tutorial-create-route-table-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Poniższe przykładowe polecenia wiersza polecenia platformy Azure oczekują proste środowisko już utworzone w oparciu o powyższy scenariusz. Jeśli chcesz uruchamiać polecenia, ponieważ są one wyświetlane w tym dokumencie, należy utworzyć w środowisku pokazanym w [tworzenie sieci wirtualnej (klasycznej) przy użyciu wiersza polecenia platformy Azure](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Utwórz trasę zdefiniowaną przez użytkownika dla podsieci frontonu
Aby utworzyć tabelę tras i trasy służące do podsieci frontonu, w oparciu o powyższy scenariusz, wykonaj następujące czynności.

1. Uruchom następujące polecenie, aby przełączyć tryb na klasyczny:

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
   
   * **-l (lub --location)**. Region platformy Azure, w którym zostanie utworzona nowa sieciowa grupa zabezpieczeń. W naszym scenariuszu *westus*.
   * **-n (lub --name)**. Nazwa nowej sieciowej grupy zabezpieczeń. W naszym scenariuszu *sieciowa grupa zabezpieczeń frontonu*.
3. Uruchom następujące polecenie, aby tworzyć trasy w tabeli tras, aby wysłać cały ruch kierowany do podsieci zaplecza (192.168.2.0/24), aby **FW1** maszyny Wirtualnej (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Dane wyjściowe:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Parametry:
   
   * **-r (lub--nazwa tabeli tras)**. Nazwa tabeli tras, w której zostaną dodane trasy. W naszym scenariuszu *trasy zdefiniowanej przez użytkownika frontonu*.
   * **-a (lub --address-prefix)**. Prefiks adresu podsieci, w których pakietów są przeznaczone do. W naszym scenariuszu *192.168.2.0/24*.
   * **-t (lub--następnego przeskoku typu)**. Typ ruchu obiektu będą wysyłane do. Możliwe wartości to *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*, lub *Brak*.
   * **-p (lub--ip adres, następnego przeskoku w-**). Adres IP następnego przeskoku. W naszym scenariuszu *192.168.0.4*.
4. Uruchom następujące polecenie, aby skojarzyć utworzony za pomocą tabeli tras **frontonu** podsieci:

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
   
   * **-t (lub--vnet-name)**. Nazwa sieci wirtualnej, w którym znajduje się podsieć. W naszym scenariuszu jest to *TestVNet*.
   * **-n (lub--subnet-name**. Nazwa tabeli tras podsieci zostanie dodany do. W naszym scenariuszu jest to *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Utwórz trasę zdefiniowaną przez użytkownika dla podsieci zaplecza
Aby utworzyć tabelę tras i trasy służące do podsieci zaplecza na podstawie scenariusza, wykonaj następujące czynności:

1. Uruchom następujące polecenie, aby utworzyć tabelę tras dla podsieci zaplecza:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Uruchom następujące polecenie, aby tworzyć trasy w tabeli tras, aby wysłać cały ruch kierowany do podsieci frontonu (. 192.168.1.0/24), aby **FW1** maszyny Wirtualnej (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Uruchom następujące polecenie, aby skojarzyć tabeli trasy z **zaplecza** podsieci:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

