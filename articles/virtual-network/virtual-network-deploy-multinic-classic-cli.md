---
title: Tworzenie maszyny Wirtualnej (klasycznej) z wieloma kartami sieciowymi — klasyczny interfejs wiersza polecenia Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć maszynę Wirtualną (wersja klasyczna) z wieloma kartami sieciowymi przy użyciu platformy Azure klasyczny interfejs wiersza polecenia (CLI).
services: virtual-network
documentationcenter: na
author: genlin
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
ms.openlocfilehash: 1e47b1e548516960c6aab3c48d64255370c94a77
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650023"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-classic-cli"></a>Tworzenie maszyny Wirtualnej (klasycznej) z wieloma kartami sieciowymi przy użyciu platformy Azure klasyczny interfejs wiersza polecenia

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Można utworzyć maszyny wirtualne (VM) na platformie Azure i dołączanie wielu interfejsów sieciowych (NIC) do wszystkich maszyn wirtualnych. Wiele kart sieciowych umożliwia rozdzielenie typów ruchu między kartami sieciowymi. Na przykład jedną kartą Sieciową może komunikować się z Internetu, podczas gdy inny komunikuje się tylko z wewnętrznych zasobów, które nie jest połączony z Internetem. Do rozdzielania ruchu sieciowego między wiele kart sieciowych jest wymagany do wielu wirtualnych urządzeń sieciowych, takich jak dostarczanie aplikacji i rozwiązań Optymalizacja sieci WAN.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi:  [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dowiedz się, jak wykonać te kroki przy użyciu [modelu wdrażania usługi Resource Manager](../virtual-machines/linux/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Użyto grupę zasobów o nazwie *IaaSStory* serwerów sieci WEB oraz grupę zasobów o nazwie *zaplecza IaaSStory* dla serwerów bazy danych.

## <a name="prerequisites"></a>Wymagania wstępne
Przed utworzeniem serwerów bazy danych, musisz utworzyć *IaaSStory* grupę zasobów za pomocą wszystkich niezbędnych zasobów dla tego scenariusza. Aby utworzyć te zasoby, wykonaj poniższe kroki. Tworzenie sieci wirtualnej, wykonując kroki opisane w [tworzenie sieci wirtualnej](virtual-networks-create-vnet-classic-cli.md) artykułu.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Wdrażanie maszyn wirtualnych zaplecza
Maszyny wirtualne zaplecza, zależą od utworzenia następujące zasoby:

* **Konto magazynu dla dysków z danymi**. Dla lepszej wydajności dysków z danymi na serwerach bazy danych użyje pełny stan dysku (SSD) technologii, która wymaga konta magazynu premium storage. Upewnij się, lokalizacja platformy Azure, wdrażanie do obsługi magazynu w warstwie premium.
* **Karty sieciowe**. Każda maszyna wirtualna będzie mieć dwie karty sieciowe, jeden dla dostępu do bazy danych, a drugi dla zarządzania.
* **Zestaw dostępności**. Wszystkie serwery baz danych zostaną dodane do pojedynczego dostępności ustawić, aby upewnić się, że co najmniej jedną z maszyn wirtualnych i uruchamianie podczas konserwacji.

### <a name="step-1---start-your-script"></a>Krok 1 — Uruchom skrypt
Możesz pobrać skrypt pełną powłoki bash używane [tutaj](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Wykonaj poniższe kroki, aby zmienić skrypt zadziałał w Twoim środowisku:

1. Zmień wartości zmiennych poniżej istniejącej grupy zasobów wdrożonymi powyżej w oparciu o [wymagania wstępne](#prerequisites).

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Zmień wartości zmiennych poniżej na podstawie wartości, których chcesz używać dla danego wdrożenia wewnętrznej bazy danych.

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

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Krok 2 — Tworzenie zasobów niezbędnych dla maszyn wirtualnych
1. Utwórz nową usługę w chmurze dla wszystkich maszyn wirtualnych zaplecza. Zwróć uwagę na `$backendCSName` zmienną dla nazwy grupy zasobów i `$location` dla regionu platformy Azure.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Utwórz konto magazynu premium storage dla dysków systemu operacyjnego i danych, który będzie używany przez Twoje maszyny wirtualne.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>Krok 3 — Tworzenie maszyn wirtualnych z wieloma kartami sieciowymi
1. Rozpocznij pętli, aby utworzyć wiele maszyn wirtualnych na podstawie `numberOfVMs` zmiennych.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Dla każdej maszyny Wirtualnej Określ nazwę i adres IP każdego z dwiema kartami sieciowymi.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. Tworzenie maszyny Wirtualnej. Zwróć uwagę, użycie `--nic-config` parametr zawierający listę wszystkich kart sieciowych z nazwą, podsieci i adresu IP.

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

### <a name="step-4---run-the-script"></a>Krok 4 — uruchamianie skryptu
Teraz, gdy został pobrany i zmieniony skryptów, w zależności od potrzeb, uruchom skrypt, aby utworzyć wewnętrznej bazy danych, maszyny wirtualne z wieloma kartami sieciowymi.

1. Zapisz skrypt i uruchom go z Twojego **Bash** terminala. Zobaczysz wstępnych danych wyjściowych, jak pokazano poniżej.

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

2. Po kilku minutach zakończy się wykonywanie i pozostałej części danych wyjściowych zostanie wyświetlony, jak pokazano poniżej.

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

Usługa Azure DHCP przypisuje domyślnej bramy do pierwszego interfejsu sieciowego (podstawowy) dołączonych do maszyny wirtualnej. Platforma Azure domyślnie nie przypisuje domyślnej bramy do dodatkowych interfejsów sieciowych dołączonych do maszyny wirtualnej. Dlatego domyślnie nie można komunikować się z zasobami poza podsiecią, w której znajduje się dodatkowy interfejs sieciowy. Dodatkowe interfejsy sieciowe mogą jednak komunikować się z zasobami poza podsiecią. Aby skonfigurować routing dla dodatkowych interfejsów sieciowych, zobacz [routingu w ramach systemu operacyjnego maszyny wirtualnej z wieloma interfejsami sieciowymi](virtual-network-network-interface-vm.md).
