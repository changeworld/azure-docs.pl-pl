---
title: Migrowanie do Menedżera zasobów przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono w trakcie migracji obsługiwane przez platformę IaaS zasobów, takich jak maszynach wirtualnych (VM), sieci wirtualnych (sieci wirtualne) oraz kont magazynu ze środowiska klasycznego do usługi Azure Resource Manager (ARM) za pomocą poleceń programu PowerShell systemu Azure
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: a57337acadafe40839e16d6a31861ff7c892c071
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31602505"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migracja zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager przy użyciu programu Azure PowerShell
Te kroki pokazują, jak używać poleceń programu Azure PowerShell do migracji infrastruktury jako zasoby usługi (IaaS) z klasycznym modelu wdrażania modelu wdrażania usługi Azure Resource Manager.

Należy również przeprowadzić migrację zasobów za pomocą [interfejsu wiersza polecenia platformy Azure (Azure CLI)](../linux/migration-classic-resource-manager-cli.md).

* Aby uzyskać podstawowe informacje dotyczące obsługiwanych scenariuszy migracji, zobacz [obsługiwane platformy migracji zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Aby uzyskać szczegółowe wskazówki i Przewodnik migracji, zobacz [techniczne szczegółowe informacje na temat obsługiwanych platform migracji ze środowiska klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md)

<br>
Oto schemat blokowy, aby określić kolejność kroków muszą być wykonywane podczas procesu migracji

![Zrzut ekranu przedstawiający kroki migracji](media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-plan-for-migration"></a>Krok 1: Planowanie migracji
Poniżej przedstawiono kilka najlepsze rozwiązania, które są zalecane jako ocenić Migrowanie zasobów IaaS z klasycznego do Menedżera zasobów:

* Zapoznaj się z artykułem [obsługiwane i nieobsługiwane funkcje i konfiguracje](migration-classic-resource-manager-overview.md). Jeśli masz maszyn wirtualnych, które używają nieobsługiwane konfiguracje i funkcje, zaleca się poczekać do obsługi funkcji/konfiguracji zostaną ogłoszone. Jeśli zgodnie z potrzebami, Usuń tej funkcji lub wychodzenia takiej konfiguracji, aby umożliwić migrację.
* Jeśli mają automatyczny skrypty, które obecnie wdrażanie infrastruktury i aplikacji, spróbuj utworzyć podobnych konfiguracji testu przy użyciu tych skryptów do migracji. Można też skonfigurować próbkę środowiskami przy użyciu portalu Azure.

> [!IMPORTANT]
> Bramy aplikacji nie są obecnie obsługiwane dla migracji ze środowiska klasycznego do Menedżera zasobów. Aby przeprowadzić migrację z bramy aplikacji klasycznej sieci wirtualnej, należy usunąć bramę, przed uruchomieniem operacji Prepare, aby przenieść sieć. Po zakończeniu migracji należy ponownie podłączyć bramy w usłudze Azure Resource Manager.
>
>Bram usługi ExpressRoute nawiązywania obwody usługi ExpressRoute w innej subskrypcji nie może być migrowane automatycznie. W takiej sytuacji należy usunąć bramę usługi ExpressRoute, migrację sieci wirtualnej i Utwórz ponownie bramę. Zobacz [ExpressRoute migracji obwody i skojarzone sieci wirtualne z klasycznego modelu wdrażania usługi Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md) Aby uzyskać więcej informacji.
>
>

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Krok 2: Zainstaluj najnowszą wersję programu Azure PowerShell
Istnieją dwie główne opcje instalacji programu Azure PowerShell: [galerii programu PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) lub [Instalatora platformy sieci Web (WebPI)](http://aka.ms/webpi-azps). WebPI odbiera comiesięcznych aktualizacji. Galerii programu PowerShell odbiera aktualizacje w sposób ciągły. W tym artykule jest oparta na program Azure PowerShell w wersji 2.1.0.

Aby uzyskać instrukcje instalacji, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>Krok 3: Upewnij się, że jesteś administratorem subskrypcji w portalu Azure
Do przeprowadzania migracji, możesz muszą zostać dodane jako współadministrator subskrypcji w [portalu Azure](https://portal.azure.com).

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
2. W menu Centrum wybierz **subskrypcji**. Jeśli nie widzisz, wybierz **wszystkie usługi**.
3. Znajdź pozycję odpowiednie subskrypcji, a następnie przyjrzeć się **Moje roli** pola. Dla administratora współpracującego, wartość powinna być _administrator konta_.

Jeśli nie jest możliwe dodać administratora współpracującego, następnie skontaktuj się z administratorem usługi ani współadministratorem subskrypcji można pobrać samodzielnie dodane.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Krok 4: Ustaw subskrypcji i zaloguj do migracji
Najpierw należy uruchomić wiersz programu PowerShell. W przypadku migracji należy skonfigurować środowisko dla obu classic i Menedżera zasobów.

Zaloguj się do swojego konta dla modelu Resource Manager.

```powershell
    Connect-AzureRmAccount
```

Uzyskiwanie dostępnych subskrypcji przy użyciu następującego polecenia:

```powershell
    Get-AzureRMSubscription | Sort Name | Select Name
```

Ustaw subskrypcji platformy Azure dla bieżącej sesji. W tym przykładzie ustawia domyślną nazwę subskrypcji **moją subskrypcję Azure**. Przykładowa nazwa subskrypcji należy zastąpić własnymi.

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Rejestracja jest jednorazowe krok, ale należy to robić raz przed próbą wykonania migracji. Bez rejestrowania, zobacz się następujący komunikat o błędzie:
>
> *Element BadRequest: Subskrypcja nie jest zarejestrowana do migracji.*
>
>

Rejestracja dostawcy zasobów migracji za pomocą następującego polecenia:

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Poczekaj pięć minut dla rejestracji zakończyć. Można sprawdzić stan zatwierdzenia za pomocą następującego polecenia:

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Upewnij się, że jest RegistrationState `Registered` przed kontynuowaniem.

Teraz logować się do swojego konta w przypadku modelu klasycznego.

```powershell
    Add-AzureAccount
```

Uzyskiwanie dostępnych subskrypcji przy użyciu następującego polecenia:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Ustaw subskrypcji platformy Azure dla bieżącej sesji. W tym przykładzie subskrypcji domyślne **moją subskrypcję Azure**. Przykładowa nazwa subskrypcji należy zastąpić własnymi.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Krok 5: Upewnij się, że masz wystarczająco dużo Vcpu maszyny wirtualnej Azure Resource Manager w regionie Azure bieżącego wdrożenia lub sieci Wirtualnej
Aby sprawdzić bieżącą liczbę Vcpu, do których masz usługi Azure Resource Manager służy następującego polecenia programu PowerShell. Aby dowiedzieć się więcej na temat przydziały vCPU, zobacz [limity i usługi Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

W tym przykładzie sprawdza dostępność **zachodnie stany USA** regionu. Przykładowa nazwa regionu zastąpić własnym.

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Krok 6: Uruchamianie poleceń, aby przeprowadzić migrację zasobów IaaS
> [!NOTE]
> Wszystkie czynności opisane w tym miejscu są idempotentności. Jeśli masz problem niż nieobsługiwanej funkcji lub błąd konfiguracji, zalecamy możesz ponowić próbę o przygotowaniu przerwania lub przekazania operacji. Platforma próbuje wykonać akcję ponownie.
>
>

## <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Krok 6.1: Opcja 1 — migracji maszyn wirtualnych w usłudze w chmurze (nie w sieci wirtualnej)
Pobierz listę usług w chmurze za pomocą następującego polecenia, a następnie wybierz usługę w chmurze, które chcesz migrować. Jeśli w sieci wirtualnej maszyn wirtualnych w usłudze w chmurze lub mają one role sieci web lub procesu roboczego, polecenie zwróci komunikat o błędzie.

```powershell
    Get-AzureService | ft Servicename
```

Pobierz nazwę wdrożenia usługi w chmurze. W tym przykładzie nazwa usługi jest **Moje usługi**. Przykładowa nazwa usługi należy zastąpić nazwę usługi.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Przygotuj maszyny wirtualne w usłudze w chmurze do migracji. Dostępne są dwie opcje do wyboru.

* **Opcja 1. Migrowanie maszyn wirtualnych do sieci wirtualnej utworzone platformy**

    Po pierwsze sprawdzanie poprawności w przypadku migrowania usługi w chmurze przy użyciu następujących poleceń:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Poprzednie polecenie wyświetla wszystkie ostrzeżenia i błędy, które blokują migracji. Jeśli weryfikacja zakończy się pomyślnie, a następnie można przenieść na **Prepare** krok:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Opcja 2. Migrowanie do istniejącej sieci wirtualnej w modelu wdrażania usługi Resource Manager**

    W tym przykładzie nazwa grupy zasobów **myResourceGroup**, nazwa sieci wirtualnej do **myVirtualNetwork** i nazwy podsieci **mySubNet**. Zamień nazwy w tym przykładzie nazwy własnych zasobów.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Po pierwsze sprawdzanie poprawności w przypadku migrowania sieci wirtualnej przy użyciu następującego polecenia:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Poprzednie polecenie wyświetla wszystkie ostrzeżenia i błędy, które blokują migracji. Jeśli weryfikacja zakończy się pomyślnie, można przejść z następujący krok przygotowania:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Po operacji Prepare powiedzie się przy użyciu jednej z tych opcji, sprawdzania stanu migracji maszyn wirtualnych. Upewnij się, że są one `Prepared` stanu.

W tym przykładzie nazwa maszyny Wirtualnej **myVM**. Przykładowa nazwa należy zastąpić nazwę maszyny Wirtualnej.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Sprawdź konfigurację dla zasobów przygotowanego przy użyciu programu PowerShell lub w portalu Azure. Jeśli chcesz powrócić do poprzedni stan nie jest jeszcze gotowa do migracji, użyj następującego polecenia:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Jeśli przygotowane Konfiguracja wygląda dobrze, możesz przejść do przodu i zatwierdzić zasobów za pomocą następującego polecenia:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

## <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Krok 6.1: Opcja 2 — migracji maszyn wirtualnych w sieci wirtualnej

Aby dokonać migracji maszyn wirtualnych w sieci wirtualnej, należy przeprowadzić migrację sieci wirtualnej. Maszyny wirtualne są automatycznie migracji z siecią wirtualną. Wybierz sieć wirtualną, która mają zostać zmigrowane.
> [!NOTE]
> [Przeprowadź migrację jednej maszyny wirtualnej klasycznego](migrate-single-classic-to-resource-manager.md) przez utworzenie nowej maszyny wirtualnej usługi Resource Manager z dyskami zarządzane przy użyciu plików wirtualnego dysku twardego (systemu operacyjnego i danych) maszyny wirtualnej.
<br>

> [!NOTE]
> Nazwa sieci wirtualnej mogą się różnić od przedstawionego w nowego portalu. Nowy Portal Azure zawiera nazwę jako `[vnet-name]` , ale nazwa rzeczywista sieci wirtualnej jest typu `Group [resource-group-name] [vnet-name]`. Przed migracją, wyszukiwanie nazwy sieci wirtualnych za pomocą polecenia `Get-AzureVnetSite | Select -Property Name` lub wyświetlić w portalu Azure stary. 

W tym przykładzie nazwa sieci wirtualnej **myVnet**. Przykładowa nazwa sieci wirtualnej należy zastąpić własnymi.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Jeśli sieć wirtualna zawiera sieci web lub roli proces roboczy lub maszyn wirtualnych z nieobsługiwaną konfiguracją, otrzymasz komunikat o błędzie weryfikacji.
>
>

Po pierwsze sprawdzanie poprawności w przypadku migrowania sieci wirtualnej przy użyciu następującego polecenia:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Poprzednie polecenie wyświetla wszystkie ostrzeżenia i błędy, które blokują migracji. Jeśli weryfikacja zakończy się pomyślnie, można przejść z następujący krok przygotowania:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Sprawdź konfigurację przygotować maszyn wirtualnych przy użyciu programu Azure PowerShell lub w portalu Azure. Jeśli chcesz powrócić do poprzedni stan nie jest jeszcze gotowa do migracji, użyj następującego polecenia:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Jeśli przygotowane Konfiguracja wygląda dobrze, możesz przejść do przodu i zatwierdzić zasobów za pomocą następującego polecenia:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

## <a name="step-62-migrate-a-storage-account"></a>Krok 6.2 migracji konta magazynu
Po zakończeniu migracji maszyn wirtualnych, firma Microsoft zaleca migracji kont magazynu.

Przed przeprowadzeniem migracji konta magazynu, należy wykonać przed wymagań wstępnych:

* **Migrowanie klasycznych maszyn wirtualnych, których dyski są przechowywane na koncie magazynu**

    Poprzedzających polecenie zwraca RoleName i DiskName właściwości wszystkich klasycznego dysków maszyny Wirtualnej na koncie magazynu. RoleName jest nazwa maszyny wirtualnej, do której jest dołączona dysku. Jeśli poprzedzających polecenie zwraca dysków następnie upewnij się, że maszyn wirtualnych, do których są dołączone dyski te są migrowane przed przeprowadzeniem migracji konta magazynu.
    ```powershell
     $storageAccountName = 'yourStorageAccountName'
      Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
      DiskName | Format-List -Property RoleName, DiskName

    ```
* **Usuń niedołączonej klasycznego dyski maszyn wirtualnych przechowywanych w ramach konta magazynu**

    Find niedołączonej klasycznego dysków maszyny Wirtualnej w magazynie konta przy użyciu następujących poleceń:

    ```powershell
        $storageAccountName = 'yourStorageAccountName'
        Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

    ```
    Jeśli powyżej polecenie zwraca dysków Usuń te dyski za pomocą następujących poleceń:

    ```powershell
       Remove-AzureDisk -DiskName 'yourDiskName'
    ```
* **Usuwanie obrazów maszyn wirtualnych przechowywanych w ramach konta magazynu**

    Poprzedzających polecenie zwraca wszystkie obrazy maszyny Wirtualnej z dyskiem systemu operacyjnego przechowywane na koncie magazynu.
     ```powershell
        Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                } | Select-Object -Property ImageName, ImageLabel
     ```
     Poprzedzających polecenie zwraca wszystkie obrazy maszyny Wirtualnej z dyskami danych przechowywanych w ramach konta magazynu.
     ```powershell

        Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                         -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                        } | Select-Object -Property ImageName, ImageLabel
     ```
    Usuń wszystkie obrazy maszyny Wirtualnej zwrócony przez powyżej poleceń przy użyciu poprzedniego polecenia:
    ```powershell
    Remove-AzureVMImage -ImageName 'yourImageName'
    ```

Sprawdzanie poprawności każdego konta magazynu, migracji za pomocą następującego polecenia. W tym przykładzie nazwa konta magazynu jest **Mojekontomagazynu**. Przykładowa nazwa należy zastąpić nazwę konta magazynu.

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
```

Następnym krokiem jest przygotowania do migracji konta magazynu

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
```

Sprawdź konfigurację dla konta magazynu przygotowanego przy użyciu programu Azure PowerShell lub w portalu Azure. Jeśli chcesz powrócić do poprzedni stan nie jest jeszcze gotowa do migracji, użyj następującego polecenia:

```powershell
    Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
```

Jeśli przygotowane Konfiguracja wygląda dobrze, możesz przejść do przodu i zatwierdzić zasobów za pomocą następującego polecenia:

```powershell
    Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
```

## <a name="next-steps"></a>Kolejne kroki
* [Omówienie migracji zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager obsługiwane platformy](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Techniczne szczegółowe informacje na temat obsługiwanych platform migracji ze środowiska klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migracja zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager za pomocą interfejsu wiersza polecenia](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Narzędzia społeczności z migracji zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przejrzyj najczęściej zadawane pytania dotyczące migrowania zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
