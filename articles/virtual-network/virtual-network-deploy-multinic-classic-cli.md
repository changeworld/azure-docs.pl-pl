---
title: Tworzenie maszyny Wirtualnej (klasyczne) z wieloma kartami sieciowymi — 1.0 interfejsu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Maszynę wirtualną (klasyczne) z wieloma kartami sieciowymi przy użyciu interfejsu wiersza polecenia platformy Azure (CLI) 1.0.
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
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
ms.openlocfilehash: d2631e531368df4f00d7ffd8fef585038658797b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-cli-10"></a>Tworzenie maszyny Wirtualnej (klasyczne) z wieloma kartami sieciowymi przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Można tworzyć maszyn wirtualnych (VM) na platformie Azure i dołączyć wiele interfejsów sieciowych (NIC) do wszystkich maszyn wirtualnych. Wiele kart sieciowych włączać rozdzielenie typów ruchu sieciowego między kart sieciowych. Na przykład jednej karcie Sieciowej może komunikować się z Internetem, podczas gdy inny komunikuje się tylko z wewnętrznych zasobów nie jest połączony z Internetem. Do rozdzielania ruchu sieciowego między wiele kart sieciowych jest wymaganych dla wielu urządzeń wirtualnych sieci, takich jak dostarczania aplikacji i rozwiązań Optymalizacja sieci WAN.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dowiedz się, jak wykonać te czynności przy użyciu [modelu wdrażania usługi Resource Manager](../virtual-machines/linux/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Poniższe kroki Użyj grupy zasobów o nazwie *IaaSStory* dla serwerów sieci WEB i grupy zasobów o nazwie *IaaSStory zaplecza* dla serwerów baz danych.

## <a name="prerequisites"></a>Wymagania wstępne
Przed utworzeniem serwerów bazy danych, należy utworzyć *IaaSStory* grupy zasobów z wszystkie niezbędne zasoby dotyczące tego scenariusza. Aby utworzyć tych zasobów, wykonaj kroki, które należy wykonać. Tworzenie sieci wirtualnej, wykonując kroki opisane w [utworzyć sieć wirtualną](virtual-networks-create-vnet-classic-cli.md) artykułu.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Wdrażanie maszyn wirtualnych zaplecza
Maszyny wirtualne zaplecza są zależne od utworzenie następujących zasobów:

* **Konto magazynu dla dysków z danymi**. W celu poprawy wydajności dysków danych na serwerach bazy danych będzie używać półprzewodnikowych (SSD) dysku technologii, która wymaga konta magazynu w warstwie premium. Upewnij się, lokalizacja platformy Azure, można wdrożyć na obsługuje usługi premium storage.
* **Karty sieciowe**. Każda maszyna wirtualna będzie mieć dwie karty sieciowe, jeden dla dostępu do bazy danych, a drugi do zarządzania.
* **Zestaw dostępności**. Wszystkie serwery baz danych zostanie dodany do jednej dostępności ustawić, aby upewnić się, że co najmniej jeden z maszynami wirtualnymi i systemem podczas konserwacji.

### <a name="step-1---start-your-script"></a>Krok 1 — Uruchom skrypt
Możesz pobrać skrypt pełna bash używany [tutaj](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Wykonaj poniższe kroki, aby zmienić skryptu do pracy w środowisku:

1. Zmienianie wartości zmiennych poniżej oparte na istniejącej grupie zasobów wdrożone powyżej w [wymagania wstępne](#Prerequisites).

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Zmienianie wartości zmiennych poniżej na podstawie wartości, który ma być używany dla danego wdrożenia wewnętrznej bazy danych.

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

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Krok 2 — Tworzenie niezbędne zasoby dla maszyn wirtualnych
1. Utwórz nową usługę chmury dla wszystkich maszyn wirtualnych wewnętrznej bazy danych. Zwróć uwagę na `$backendCSName` zmiennej dla nazwy grupy zasobów i `$location` dla regionu platformy Azure.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Utwórz konto magazynu premium dysków systemu operacyjnego i danych ma być używany przez Twoje maszyn wirtualnych.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>Krok 3 — Tworzenie maszyn wirtualnych z wieloma kartami sieciowymi
1. Uruchom pętli do utworzenia wielu maszyn wirtualnych, na podstawie `numberOfVMs` zmiennych.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Dla każdej maszyny Wirtualnej Określ nazwę i adres IP każdego dwie karty sieciowe.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. Tworzenie maszyny Wirtualnej. Zwróć uwagę, użycie `--nic-config` parametr zawierający listę wszystkich kart sieciowych podsieci, adresów IP i nazwy.

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

4. Dla każdej maszyny Wirtualnej należy utworzyć dwa dyski danych.

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>Krok 4 — Uruchom skrypt
Pobrane i zmienić skryptu na podstawie Twoich potrzeb, należy uruchomić skrypt w celu utworzenia wewnętrznej bazy danych maszyn wirtualnych z wieloma kartami sieciowymi.

1. Zapisz skrypt i uruchom go z Twojego **Bash** terminala. Zobaczysz początkowej danych wyjściowych, jak pokazano poniżej.

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

2. Po kilku minutach zakończy wykonywanie i będzie zobaczyć pozostałą część danych wyjściowych, jak pokazano poniżej.

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

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Krok 5 — Konfigurowanie routingu w ramach systemu operacyjnego maszyny Wirtualnej

Azure DHCP przypisuje do pierwszego interfejsu sieciowego (podstawowe) dołączonych do maszyny wirtualnej bramy domyślnej. Platforma Azure domyślnie nie przypisuje domyślnej bramy do dodatkowych interfejsów sieciowych dołączonych do maszyny wirtualnej. Dlatego domyślnie nie można komunikować się z zasobami poza podsiecią, w której znajduje się dodatkowy interfejs sieciowy. Dodatkowych interfejsów sieciowych można jednak komunikować się z zasobami spoza ich podsieci. Aby skonfigurować routing dla dodatkowych interfejsów sieciowych, zobacz [routingu w systemie operacyjnym maszyny wirtualnej z wieloma interfejsami sieciowymi](virtual-network-network-interface-vm.md).
