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
ms.openlocfilehash: 749cc5a5e5b8417abe602b7e37c103a26cc4dc03
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Tworzenie klasycznej sieci wirtualnej przy użyciu wiersza polecenia platformy Azure
Interfejsu wiersza polecenia platformy Azure można użyć do zarządzania zasobami Azure z poziomu wiersza polecenia dowolnego komputera z systemem Windows, Linux lub OS X.

1. Jeśli po raz pierwszy używasz interfejsu wiersza polecenia Azure, zobacz artykuł [Instalowanie i konfigurowania interfejsu wiersza polecenia Azure](../articles/cli-install-nodejs.md) i postępuj zgodnie z instrukcjami aż do punktu, w którym należy wybrać konto platformy Azure i subskrypcję.
2. Aby utworzyć sieć wirtualną i podsieć, uruchom **Utwórz sieć wirtualną sieć platformy azure** polecenia:
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    Oczekiwane dane wyjściowe:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**. Nazwa sieci wirtualnej, która zostanie utworzona. W scenariuszu *TestVNet*
   * **-e (lub--przestrzeni adresowej)**. Przestrzeń adresowa sieci wirtualnej. W scenariuszu *192.168.0.0*
   * **-i (lub - cidr)**. Maska sieci w formacie CIDR. W scenariuszu *16*.
   * **-n (lub--nazwy podsieci**). Nazwa pierwszej podsieci. W scenariuszu *frontonu*.
   * **-p (lub--podsieci start-ip)**. Początkowy adres IP dla podsieci lub przestrzeni adresowej podsieci. W scenariuszu *192.168.1.0*.
   * **-r (lub--podsieci cidr)**. Maska sieci w formacie CIDR podsieci. W scenariuszu *24*.
   * **-l (lub --location)**. Region platformy Azure, w którym utworzona sieć wirtualna. W scenariuszu *środkowe stany USA*.
3. Aby utworzyć podsieć, uruchom **Utwórz podsieć sieci wirtualnej platformy azure sieci** polecenia:
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    Oczekiwane dane wyjściowe poprzedniego polecenia:
   
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
   * **-n (lub --name)**. Nazwa nowej podsieci. W scenariuszu *zaplecza*.
   * **-a (lub --address-prefix)**. Blok CIDR podsieci. W scenariuszu *192.168.2.0/24*.
4. Aby wyświetlić właściwości nowej sieci wirtualnej, należy uruchomić **Pokaż sieci wirtualnej sieci platformy azure** polecenia:
   
            azure network vnet show
   
    Oczekiwane dane wyjściowe poprzedniego polecenia:
   
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

