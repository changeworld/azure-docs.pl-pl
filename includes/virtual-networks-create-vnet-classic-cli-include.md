---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: cd686e1bf62bbd7f37f61ced767e92918edf919c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66116910"
---
## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Jak utworzyć klasyczną sieć wirtualną przy użyciu wiersza polecenia platformy Azure
Interfejsu wiersza polecenia platformy Azure można użyć do zarządzania zasobami Azure z poziomu wiersza polecenia dowolnego komputera z systemem Windows, Linux lub OS X.

1. Jeśli po raz pierwszy używasz interfejsu wiersza polecenia Azure, zobacz artykuł [Instalowanie i konfigurowania interfejsu wiersza polecenia Azure](../articles/cli-install-nodejs.md) i postępuj zgodnie z instrukcjami aż do punktu, w którym należy wybrać konto platformy Azure i subskrypcję.
2. Aby utworzyć sieć wirtualną i podsieć, uruchom **tworzenie sieci wirtualnej sieci platformy azure** polecenia:
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    Oczekiwane dane wyjściowe:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**. Nazwa sieci wirtualnej, która zostanie utworzona. W scenariuszu *TestVNet*
   * **-e (lub--przestrzeni adresowej)** . Przestrzeń adresowa sieci wirtualnej. W scenariuszu *192.168.0.0*
   * **-i (lub - cidr)** . Maska sieci w formacie CIDR. W scenariuszu *16*.
   * **-n (lub--subnet-name**). Nazwa pierwszej podsieci. W scenariuszu *frontonu*.
   * **-p (lub--podsieci start-ip)** . Początkowy adres IP dla podsieci lub przestrzeń adresową podsieci. W scenariuszu *192.168.1.0*.
   * **-r (lub--podsieci cidr)** . Maska sieci w formacie CIDR podsieci. W scenariuszu *24*.
   * **-l (lub --location)** . Region platformy Azure, w którym zostanie utworzona sieć wirtualna. W scenariuszu *środkowe stany USA*.
3. Aby utworzyć podsieć, uruchom **podsieci sieci wirtualnej sieci platformy azure utworzyć** polecenia:
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    Oczekiwane dane wyjściowe dla poprzedniego polecenia:
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t (lub--vnet-name**. Nazwa sieci wirtualnej, w której zostanie utworzona podsieć. W scenariuszu *TestVNet*.
   * **-n (lub --name)** . Nazwa nowej podsieci. W scenariuszu *zaplecza*.
   * **-a (lub --address-prefix)** . Blok CIDR podsieci. W scenariuszu *192.168.2.0/24*.
4. Aby wyświetlić właściwości nowej sieci wirtualnej, należy uruchomić **usługa azure network vnet show** polecenia:
   
            azure network vnet show
   
    Oczekiwane dane wyjściowe dla poprzedniego polecenia:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

