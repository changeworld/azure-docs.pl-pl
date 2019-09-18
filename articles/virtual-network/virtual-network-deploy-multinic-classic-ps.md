---
title: Tworzenie maszyny wirtualnej (klasycznej) z wieloma kartami sieciowymi — Azure PowerShell | Microsoft Docs
description: Dowiedz się, jak utworzyć maszynę wirtualną (klasyczną) z wieloma kartami sieciowymi przy użyciu programu PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: af22bc43a06be74c7a4b6c869725a19fc87a0f3e
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058731"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Tworzenie maszyny wirtualnej (klasycznej) z wieloma kartami sieciowymi przy użyciu programu PowerShell

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Na platformie Azure można tworzyć maszyny wirtualne i dołączać wiele interfejsów sieciowych (nic) do każdej z maszyn wirtualnych. Wiele kart sieciowych umożliwia rozdzielenie typów ruchu między kartami sieciowymi. Na przykład jedna karta sieciowa może komunikować się z Internetem, podczas gdy druga komunikuje się tylko z zasobami wewnętrznymi, które nie są połączone z Internetem. Możliwość rozdzielenia ruchu sieciowego przez wiele kart sieciowych jest wymagana w przypadku wielu sieciowych urządzeń wirtualnych, takich jak dostarczanie aplikacji i rozwiązania do optymalizacji sieci WAN.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi:  [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dowiedz się, jak wykonać te kroki przy użyciu [modelu wdrażania Menedżer zasobów](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Poniższe kroki używają grupy zasobów o nazwie *IaaSStory* dla serwerów sieci Web i grupy zasobów o nazwie *IaaSStory-zaplecza* dla serwerów bazy danych.

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem serwerów bazy danych należy utworzyć grupę zasobów *IaaSStory* ze wszystkimi zasobami wymaganymi w tym scenariuszu. Aby utworzyć te zasoby, wykonaj poniższe czynności. Utwórz sieć wirtualną, wykonując czynności opisane w artykule [Tworzenie sieci wirtualnej](virtual-networks-create-vnet-classic-netcfg-ps.md) .

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Tworzenie maszyn wirtualnych zaplecza
Maszyny wirtualne zaplecza zależą od tworzenia następujących zasobów:

* **Podsieć zaplecza**. Serwery baz danych będą częścią oddzielnej podsieci w celu rozdzielenia ruchu sieciowego. Poniższy skrypt oczekuje, że ta podsieć ma istnieć w sieci wirtualnej o nazwie *WTestVnet*.
* **Konto magazynu dla dysków danych**. W celu zapewnienia lepszej wydajności dyski danych na serwerach baz danych będą korzystać z technologii dysków półprzewodnikowych (SSD), co wymaga konta magazynu w warstwie Premium. Upewnij się, że wdrożona lokalizacja platformy Azure obsługuje usługę Premium Storage.
* **Zestaw dostępności**. Wszystkie serwery baz danych zostaną dodane do jednego zestawu dostępności, aby upewnić się, że co najmniej jedna z maszyn wirtualnych będzie działać w trakcie konserwacji.

### <a name="step-1---start-your-script"></a>Krok 1. Uruchamianie skryptu
W [tym miejscu](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1)możesz pobrać pełny skrypt programu PowerShell. Wykonaj poniższe kroki, aby zmienić skrypt do działania w środowisku.

1. Zmień wartości zmiennych poniżej w zależności od istniejącej grupy zasobów wdrożonej powyżej w [wymaganiach wstępnych](#prerequisites).

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Zmień wartości zmiennych poniżej w zależności od wartości, których chcesz użyć do wdrożenia wewnętrznej bazy danych.

    ```powershell
    $backendCSName         = "IaaSStory-Backend"
    $prmStorageAccountName = "iaasstoryprmstorage"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $diskSize              = 127
    $vmNamePrefix          = "DB"
    $dataDiskSuffix        = "datadisk"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Krok 2. Tworzenie niezbędnych zasobów dla maszyn wirtualnych
Należy utworzyć nową usługę w chmurze i konto magazynu dla dysków danych dla wszystkich maszyn wirtualnych. Należy również określić obraz oraz konto administratora lokalnego dla maszyn wirtualnych. Aby utworzyć te zasoby, wykonaj następujące czynności:

1. Utwórz nową usługę w chmurze.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Utwórz nowe konto magazynu Premium Storage.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Ustaw konto magazynu utworzone powyżej jako bieżące konto magazynu dla Twojej subskrypcji.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Wybierz obraz dla maszyny wirtualnej.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. Ustaw poświadczenia konta administratora lokalnego.

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>Krok 3. Tworzenie maszyn wirtualnych
Musisz użyć pętli, aby utworzyć dowolną liczbę maszyn wirtualnych, i utworzyć niezbędne karty sieciowe i maszyny wirtualne w pętli. Aby utworzyć karty sieciowe i maszyny wirtualne, wykonaj następujące czynności.

1. Rozpocznij pętlę, aby powtórzyć polecenia, aby utworzyć maszynę wirtualną i dwie karty sieciowe tyle razy, ile jest to konieczne, `$numberOfVMs` na podstawie wartości zmiennej. `for`

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. `VMConfig` Utwórz obiekt określający obraz, rozmiar i zestaw dostępności dla maszyny wirtualnej.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Ustanów maszynę wirtualną jako maszynę wirtualną z systemem Windows.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Ustaw domyślną kartę sieciową i przypisz ją do statycznego adresu IP.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Dodaj drugą kartę sieciową dla każdej maszyny wirtualnej.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Utwórz na dyskach danych dla każdej maszyny wirtualnej.

    ```powershell
    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk1Name `
    -LUN 0

    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk2Name `
    -LUN 1
    ```

7. Utwórz każdą maszynę wirtualną, a następnie Zakończ pętlę.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>Krok 4. Uruchamianie skryptu
Po pobraniu i zmianie skryptu w zależności od potrzeb Runt skrypt, aby utworzyć maszyny wirtualne bazy danych zaplecza z wieloma kartami sieciowymi.

1. Zapisz skrypt i uruchom go z poziomu wiersza polecenia **programu PowerShell** lub **programu PowerShell ISE**. Pojawią się początkowe dane wyjściowe, jak pokazano poniżej.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Wprowadź informacje potrzebne w monicie o poświadczenia i kliknij przycisk **OK**. Zwracane są następujące dane wyjściowe.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Krok 5 — Konfigurowanie routingu w ramach systemu operacyjnego maszyny wirtualnej

Usługa DHCP platformy Azure przypisuje bramę domyślną do pierwszego (podstawowego) interfejsu sieciowego dołączonego do maszyny wirtualnej. Platforma Azure domyślnie nie przypisuje domyślnej bramy do dodatkowych interfejsów sieciowych dołączonych do maszyny wirtualnej. Dlatego domyślnie nie można komunikować się z zasobami poza podsiecią, w której znajduje się dodatkowy interfejs sieciowy. Dodatkowe interfejsy sieciowe mogą jednak komunikować się z zasobami poza podsiecią. Aby skonfigurować Routing dla dodatkowych interfejsów sieciowych, zobacz następujące artykuły:

- [Konfigurowanie maszyny wirtualnej z systemem Windows dla wielu kart sieciowych](../virtual-machines/windows/multiple-nics.md#configure-guest-os-for-multiple-nics
)

- [Konfigurowanie maszyny wirtualnej z systemem Linux dla wielu kart sieciowych](../virtual-machines/linux/multiple-nics.md#configure-guest-os-for-multiple-nics
)
