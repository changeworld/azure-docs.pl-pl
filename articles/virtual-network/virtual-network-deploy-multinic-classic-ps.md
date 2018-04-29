---
title: Tworzenie maszyny Wirtualnej (klasyczne) z wieloma kartami sieciowymi — programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Maszynę wirtualną (klasyczne) z wieloma kartami sieciowymi przy użyciu programu PowerShell.
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
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 09c85a9badaad45816a9160d09cb3c4a726426e8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Tworzenie maszyny Wirtualnej (klasyczne) z wieloma kartami sieciowymi przy użyciu programu PowerShell

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Można tworzyć maszyn wirtualnych (VM) na platformie Azure i dołączyć wiele interfejsów sieciowych (NIC) do wszystkich maszyn wirtualnych. Wiele kart sieciowych włączać rozdzielenie typów ruchu sieciowego między kart sieciowych. Na przykład jednej karcie Sieciowej może komunikować się z Internetem, podczas gdy inny komunikuje się tylko z wewnętrznych zasobów nie jest połączony z Internetem. Do rozdzielania ruchu sieciowego między wiele kart sieciowych jest wymaganych dla wielu urządzeń wirtualnych sieci, takich jak dostarczania aplikacji i rozwiązań Optymalizacja sieci WAN.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dowiedz się, jak wykonać te czynności przy użyciu [modelu wdrażania usługi Resource Manager](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Poniższe kroki Użyj grupy zasobów o nazwie *IaaSStory* dla serwerów sieci WEB i grupy zasobów o nazwie *IaaSStory zaplecza* dla serwerów baz danych.

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem serwerów bazy danych, należy utworzyć *IaaSStory* grupy zasobów z wszystkie niezbędne zasoby dotyczące tego scenariusza. Aby utworzyć tych zasobów, wykonaj kroki, które należy wykonać. Tworzenie sieci wirtualnej, wykonując kroki opisane w [utworzyć sieć wirtualną](virtual-networks-create-vnet-classic-netcfg-ps.md) artykułu.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Tworzenie maszyn wirtualnych zaplecza
Maszyny wirtualne zaplecza są zależne od utworzenie następujących zasobów:

* **Podsieci wewnętrznej bazy danych**. Serwery bazy danych będą częścią osobnej podsieci, aby rozdzielenie ruchu. W poniższym skrypcie oczekuje tej podsieci w sieci wirtualnej o nazwie występują *WTestVnet*.
* **Konto magazynu dla dysków z danymi**. W celu poprawy wydajności dysków danych na serwerach bazy danych będzie używać półprzewodnikowych (SSD) dysku technologii, która wymaga konta magazynu w warstwie premium. Upewnij się, lokalizacja platformy Azure, można wdrożyć na obsługuje usługi premium storage.
* **Zestaw dostępności**. Wszystkie serwery baz danych zostanie dodany do jednej dostępności ustawić, aby upewnić się, że co najmniej jeden z maszynami wirtualnymi i systemem podczas konserwacji.

### <a name="step-1---start-your-script"></a>Krok 1 — Uruchom skrypt
Możesz pobrać pełną skrypt programu PowerShell używane [tutaj](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Wykonaj poniższe kroki, aby zmienić skryptu do pracy w środowisku.

1. Zmienianie wartości zmiennych poniżej oparte na istniejącej grupie zasobów wdrożone powyżej w [wymagania wstępne](#Prerequisites).

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Zmienianie wartości zmiennych poniżej na podstawie wartości, który ma być używany dla danego wdrożenia wewnętrznej bazy danych.

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

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Krok 2 — Tworzenie niezbędne zasoby dla maszyn wirtualnych
Należy utworzyć nową usługę w chmurze i konto magazynu dla dysków z danymi dla wszystkich maszyn wirtualnych. Należy również określić obrazu, a konto administratora lokalnego dla maszyn wirtualnych. Aby utworzyć tych zasobów, wykonaj następujące kroki:

1. Utwórz nową usługę w chmurze.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Utwórz nowe konto magazynu premium.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Ustaw konto magazynu utworzone powyżej bieżącego konta magazynu dla Twojej subskrypcji.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Wybieranie obrazu maszyny wirtualnej.

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

### <a name="step-3---create-vms"></a>Krok 3 — Tworzenie maszyn wirtualnych
Należy użyć pętli można utworzyć dowolną liczbę maszyn wirtualnych, jak i utworzyć niezbędne karty sieciowe i maszyn wirtualnych w pętli. Aby utworzyć karty sieciowe i maszyn wirtualnych, należy wykonać następujące kroki.

1. Uruchom `for` pętli powtórzeń poleceń, aby utworzyć Maszynę wirtualną i dwie karty sieciowe jako tyle razy, ile to konieczne, na podstawie wartości z `$numberOfVMs` zmiennej.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Utwórz `VMConfig` obiekt określający obrazu, rozmiar i zbiór dostępności dla maszyny Wirtualnej.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Maszyna wirtualna wyznaczenie jako maszyny Wirtualnej systemu Windows.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Ustaw domyślny karty Sieciowej i przypisz mu statyczny adres IP.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Dodawanie drugiej karty Sieciowej dla każdej maszyny Wirtualnej.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Utwórz dyski danych dla każdej maszyny Wirtualnej.

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

7. Utwórz każdej maszyny Wirtualnej i zakończyć pętli.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>Krok 4 — Uruchom skrypt
Pobrane i zmienić skryptu na podstawie Twoich potrzeb, runt skrypt do tworzenia bazy danych zaplecza maszyn wirtualnych z wieloma kartami sieciowymi.

1. Zapisz skrypt i uruchom go z **PowerShell** wiersza polecenia lub **PowerShell ISE**. Zobaczysz początkowej danych wyjściowych, jak pokazano poniżej.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Wprowadź informacje wymagane w wierszu poświadczenia i kliknij **OK**. Następujące dane wyjściowe są zwracane.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Krok 5 — Konfigurowanie routingu w ramach systemu operacyjnego maszyny Wirtualnej

Azure DHCP przypisuje do pierwszego interfejsu sieciowego (podstawowe) dołączonych do maszyny wirtualnej bramy domyślnej. Platforma Azure domyślnie nie przypisuje domyślnej bramy do dodatkowych interfejsów sieciowych dołączonych do maszyny wirtualnej. Dlatego domyślnie nie można komunikować się z zasobami poza podsiecią, w której znajduje się dodatkowy interfejs sieciowy. Dodatkowych interfejsów sieciowych można jednak komunikować się z zasobami spoza ich podsieci. Aby skonfigurować routing dla dodatkowych interfejsów sieciowych, zobacz [routingu w systemie operacyjnym maszyny wirtualnej z wieloma interfejsami sieciowymi](virtual-network-network-interface-vm.md).
