---
title: Tworzenie maszyny wirtualnej (klasycznej) z wieloma kartami sieciowymi — klasyczny interfejs wiersza polecenia platformy Azure | Microsoft Docs
description: Dowiedz się, jak utworzyć maszynę wirtualną (klasyczną) z wieloma kartami sieciowymi przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: b436e41e-866c-439f-a7c7-7b4b041725ef
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dc437b15f73866f76361da529690eac7a10af1a
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058760"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-classic-cli"></a>Tworzenie maszyny wirtualnej (klasycznej) z wieloma kartami sieciowymi przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Na platformie Azure można tworzyć maszyny wirtualne i dołączać wiele interfejsów sieciowych (nic) do każdej z maszyn wirtualnych. Wiele kart sieciowych umożliwia rozdzielenie typów ruchu między kartami sieciowymi. Na przykład jedna karta sieciowa może komunikować się z Internetem, podczas gdy druga komunikuje się tylko z zasobami wewnętrznymi, które nie są połączone z Internetem. Możliwość rozdzielenia ruchu sieciowego przez wiele kart sieciowych jest wymagana w przypadku wielu sieciowych urządzeń wirtualnych, takich jak dostarczanie aplikacji i rozwiązania do optymalizacji sieci WAN.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi:  [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dowiedz się, jak wykonać te kroki przy użyciu [modelu wdrażania Menedżer zasobów](../virtual-machines/linux/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Poniższe kroki używają grupy zasobów o nazwie *IaaSStory* dla serwerów sieci Web i grupy zasobów o nazwie *IaaSStory-zaplecza* dla serwerów bazy danych.

## <a name="prerequisites"></a>Wymagania wstępne
Przed utworzeniem serwerów bazy danych należy utworzyć grupę zasobów *IaaSStory* ze wszystkimi zasobami wymaganymi w tym scenariuszu. Aby utworzyć te zasoby, wykonaj poniższe czynności. Utwórz sieć wirtualną, wykonując czynności opisane w artykule [Tworzenie sieci wirtualnej](virtual-networks-create-vnet-classic-cli.md) .

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Wdrażanie maszyn wirtualnych zaplecza
Maszyny wirtualne zaplecza zależą od tworzenia następujących zasobów:

* **Konto magazynu dla dysków danych**. W celu zapewnienia lepszej wydajności dyski danych na serwerach baz danych będą korzystać z technologii dysków półprzewodnikowych (SSD), co wymaga konta magazynu w warstwie Premium. Upewnij się, że wdrożona lokalizacja platformy Azure obsługuje usługę Premium Storage.
* **Karty sieciowe**. Każda maszyna wirtualna będzie miała dwie karty sieciowe, jedną na potrzeby dostępu do bazy danych i jedną do zarządzania.
* **Zestaw dostępności**. Wszystkie serwery baz danych zostaną dodane do jednego zestawu dostępności, aby upewnić się, że co najmniej jedna z maszyn wirtualnych będzie działać w trakcie konserwacji.

### <a name="step-1---start-your-script"></a>Krok 1. Uruchamianie skryptu
W [tym miejscu](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh)możesz pobrać pełny skrypt bash. Wykonaj następujące kroki, aby zmienić skrypt do działania w środowisku:

1. Zmień wartości zmiennych poniżej w zależności od istniejącej grupy zasobów wdrożonej powyżej w [wymaganiach wstępnych](#prerequisites).

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Zmień wartości zmiennych poniżej w zależności od wartości, których chcesz użyć do wdrożenia wewnętrznej bazy danych.

    ```azurecli
    backendCSName="IaaSStory-Backend"
    prmStorageAccountName="iaasstoryprmstorage"
    image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
    avSetName="ASDB"
    vmSize="Standard_DS3"
    diskSize=127
    vmNamePrefix="DB"
    osDiskName="osdiskdb"
    dataDiskPrefix="db"
    dataDiskName="datadisk"
    ipAddressPrefix="192.168.2."
    username='adminuser'
    password='adminP@ssw0rd'
    numberOfVMs=2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Krok 2. Tworzenie niezbędnych zasobów dla maszyn wirtualnych
1. Utwórz nową usługę w chmurze dla wszystkich maszyn wirtualnych zaplecza. Zwróć uwagę na użycie `$backendCSName` zmiennej nazwy grupy zasobów i `$location` dla regionu platformy Azure.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Utwórz konto magazynu w warstwie Premium dla systemu operacyjnego i dysków danych, które będą używane przez maszyny wirtualne.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>Krok 3. Tworzenie maszyn wirtualnych z wieloma kartami sieciowymi
1. Rozpocznij pętlę, aby utworzyć wiele maszyn wirtualnych na podstawie `numberOfVMs` zmiennych.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Dla każdej maszyny wirtualnej Określ nazwę i adres IP każdej z tych dwóch kart sieciowych.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. Utwórz maszynę wirtualną. Zwróć uwagę na użycie `--nic-config` parametru zawierającego listę wszystkich kart sieciowych z nazwą, podsiecią i adresem IP.

    ```azurecli
    azure vm create $backendCSName $image $username $password \
        --connect $backendCSName \
        --vm-name $vmNamePrefix$suffixNumber \
        --vm-size $vmSize \
        --availability-set $avSetName \
        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
        --virtual-network-name $vnetName \
        --subnet-names $backendSubnetName \
        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::
    ```

4. Dla każdej maszyny wirtualnej Utwórz dwa dyski danych.

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>Krok 4. Uruchamianie skryptu
Po pobraniu i zmianie skryptu w zależności od potrzeb Uruchom skrypt, aby utworzyć maszyny wirtualne bazy danych zaplecza z wieloma kartami sieciowymi.

1. Zapisz skrypt i uruchom go z poziomu terminalu **bash** . Pojawią się początkowe dane wyjściowe, jak pokazano poniżej.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Po kilku minutach wykonywanie zakończy się i zobaczysz resztę danych wyjściowych, jak pokazano poniżej.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Krok 5 — Konfigurowanie routingu w ramach systemu operacyjnego maszyny wirtualnej

Usługa DHCP platformy Azure przypisuje bramę domyślną do pierwszego (podstawowego) interfejsu sieciowego dołączonego do maszyny wirtualnej. Platforma Azure domyślnie nie przypisuje domyślnej bramy do dodatkowych interfejsów sieciowych dołączonych do maszyny wirtualnej. Dlatego domyślnie nie można komunikować się z zasobami poza podsiecią, w której znajduje się dodatkowy interfejs sieciowy. Dodatkowe interfejsy sieciowe mogą jednak komunikować się z zasobami poza podsiecią. Aby skonfigurować Routing dla dodatkowych interfejsów sieciowych, zobacz [Routing w ramach systemu operacyjnego maszyny wirtualnej z wieloma interfejsami sieciowymi](virtual-network-network-interface-vm.md).
