---
title: Tworzenie maszyny Wirtualnej (model klasyczny) z wieloma kartami sieciowymi — Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć maszynę Wirtualną (wersja klasyczna) z wieloma kartami sieciowymi przy użyciu programu PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
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
ms.openlocfilehash: 087b52bd603e8aed6078ab340e84c1f6bd0e8082
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652199"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Tworzenie maszyny Wirtualnej (klasycznej) z wieloma kartami sieciowymi przy użyciu programu PowerShell

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Można utworzyć maszyny wirtualne (VM) na platformie Azure i dołączanie wielu interfejsów sieciowych (NIC) do wszystkich maszyn wirtualnych. Wiele kart sieciowych umożliwia rozdzielenie typów ruchu między kartami sieciowymi. Na przykład jedną kartą Sieciową może komunikować się z Internetu, podczas gdy inny komunikuje się tylko z wewnętrznych zasobów, które nie jest połączony z Internetem. Do rozdzielania ruchu sieciowego między wiele kart sieciowych jest wymagany do wielu wirtualnych urządzeń sieciowych, takich jak dostarczanie aplikacji i rozwiązań Optymalizacja sieci WAN.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi:  [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dowiedz się, jak wykonać te kroki przy użyciu [modelu wdrażania usługi Resource Manager](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Użyto grupę zasobów o nazwie *IaaSStory* serwerów sieci WEB oraz grupę zasobów o nazwie *zaplecza IaaSStory* dla serwerów bazy danych.

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem serwerów bazy danych, musisz utworzyć *IaaSStory* grupę zasobów za pomocą wszystkich niezbędnych zasobów dla tego scenariusza. Aby utworzyć te zasoby, wykonaj poniższe kroki. Tworzenie sieci wirtualnej, wykonując kroki opisane w [tworzenie sieci wirtualnej](virtual-networks-create-vnet-classic-netcfg-ps.md) artykułu.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Tworzenie maszyn wirtualnych zaplecza
Maszyny wirtualne zaplecza, zależą od utworzenia następujące zasoby:

* **Podsieć zaplecza**. Serwery bazy danych będą częścią osobnej podsieci rozdzielenie ruchu. Poniższy skrypt oczekuje tej podsieci w sieci wirtualnej o nazwie występują *WTestVnet*.
* **Konto magazynu dla dysków z danymi**. Dla lepszej wydajności dysków z danymi na serwerach bazy danych użyje pełny stan dysku (SSD) technologii, która wymaga konta magazynu premium storage. Upewnij się, lokalizacja platformy Azure, wdrażanie do obsługi magazynu w warstwie premium.
* **Zestaw dostępności**. Wszystkie serwery baz danych zostaną dodane do pojedynczego dostępności ustawić, aby upewnić się, że co najmniej jedną z maszyn wirtualnych i uruchamianie podczas konserwacji.

### <a name="step-1---start-your-script"></a>Krok 1 — Uruchom skrypt
Możesz pobrać pełną skrypt programu PowerShell, używane [tutaj](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Wykonaj poniższe kroki, aby zmienić skrypt zadziałał w Twoim środowisku.

1. Zmień wartości zmiennych poniżej istniejącej grupy zasobów wdrożonymi powyżej w oparciu o [wymagania wstępne](#prerequisites).

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Zmień wartości zmiennych poniżej na podstawie wartości, których chcesz używać dla danego wdrożenia wewnętrznej bazy danych.

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

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Krok 2 — Tworzenie zasobów niezbędnych dla maszyn wirtualnych
Należy utworzyć nową usługę w chmurze i konto magazynu dla dysków z danymi dla wszystkich maszyn wirtualnych. Należy również określić obrazu i konta administratora lokalnego dla maszyn wirtualnych. Aby utworzyć te zasoby, wykonaj następujące czynności:

1. Utwórz nową usługę w chmurze.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Utwórz nowe konto magazynu premium storage.

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

4. Wybierz obraz maszyny Wirtualnej.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. Ustaw administratora lokalnego poświadczenia konta.

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>Krok 3 — Tworzenie maszyn wirtualnych
Należy użyć pętli, aby utworzyć dowolną liczbę maszyn wirtualnych, jak i utworzyć niezbędne kartami sieciowymi i maszyny wirtualne w ramach pętli. Aby utworzyć kartami sieciowymi i maszyny wirtualne, wykonaj następujące czynności.

1. Rozpocznij `for` pętli do powtarzania poleceń, aby utworzyć Maszynę wirtualną i dwie karty sieciowe tyle razy, zgodnie z potrzebami, na podstawie wartości z `$numberOfVMs` zmiennej.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Utwórz `VMConfig` obiekt określający obrazu, rozmiar i zestaw dostępności dla maszyny Wirtualnej.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Aprowizuj maszynę Wirtualną jako Windows maszyny Wirtualnej.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Ustaw domyślną kartę Sieciową, a następnie przypisać ją statyczny adres IP.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Dodaj drugi interfejs Sieciowy dla każdej maszyny Wirtualnej.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Utwórz do dysków z danymi dla każdej maszyny Wirtualnej.

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

7. Tworzenia poszczególnych maszyn wirtualnych, a kończy pętli.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>Krok 4 — uruchamianie skryptu
Teraz, gdy został pobrany i zmienić skryptu na podstawie własnych potrzeb, runt skrypt do utworzenia wewnętrznej bazy danych maszyn wirtualnych z wieloma kartami sieciowymi.

1. Zapisz skrypt i uruchom go z **PowerShell** wiersza polecenia lub **PowerShell ISE**. Zobaczysz wstępnych danych wyjściowych, jak pokazano poniżej.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Wypełnij informacje wymagane w wierszu poświadczenia i kliknij **OK**. Następujące dane wyjściowe są zwracane.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Krok 5 — Konfigurowanie routingu w ramach systemu operacyjnego maszyny Wirtualnej

Usługa Azure DHCP przypisuje domyślnej bramy do pierwszego interfejsu sieciowego (podstawowy) dołączonych do maszyny wirtualnej. Platforma Azure domyślnie nie przypisuje domyślnej bramy do dodatkowych interfejsów sieciowych dołączonych do maszyny wirtualnej. Dlatego domyślnie nie można komunikować się z zasobami poza podsiecią, w której znajduje się dodatkowy interfejs sieciowy. Dodatkowe interfejsy sieciowe mogą jednak komunikować się z zasobami poza podsiecią. Aby skonfigurować routing dla dodatkowych interfejsów sieciowych, zobacz następujące artykuły:

- [Konfigurowanie maszyny Wirtualnej z systemem Windows dla wielu kart sieciowych](../virtual-machines/windows/multiple-nics.md#configure-guest-os-for-multiple-nics
)

- [Konfigurowanie maszyny Wirtualnej systemu Linux dla wielu kart sieciowych](../virtual-machines/linux/multiple-nics.md#configure-guest-os-for-multiple-nics
)
