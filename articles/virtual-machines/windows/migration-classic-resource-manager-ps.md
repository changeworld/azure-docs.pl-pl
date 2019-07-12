---
title: Migrowanie do usługi Resource Manager przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Ten artykuł przeprowadzi migracji obsługiwanej przez platformę IaaS zasobów, takich jak maszyny wirtualne (VM), sieci wirtualne (Vnet) i konta magazynu z wersji klasycznej do usługi Azure Resource Manager (ARM) za pomocą poleceń programu Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
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
ms.openlocfilehash: dc88a7936c4ab4994bd9de168a682b1253c34e1f
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720229"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migrację zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager przy użyciu programu Azure PowerShell
Te kroki pokazują, jak używać poleceń programu Azure PowerShell do migracji infrastruktury jako zasoby usługi (IaaS) z klasycznego modelu wdrażania do modelu wdrażania usługi Azure Resource Manager.

Jeśli chcesz, możesz również migrować zasoby przy użyciu [interfejsu wiersza polecenia platformy Azure (Azure CLI)](../linux/migration-classic-resource-manager-cli.md).

* Aby uzyskać podstawowe informacje dotyczące obsługiwanych scenariuszy migracji, zobacz [obsługiwanej przez platformę migracji zasobów rozwiązania IaaS z wersji klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Szczegółowe wskazówki i przewodnik po migracji, zobacz [techniczne szczegółowe informacje na temat migracji obsługiwanej przez platformę z wersji klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md)

<br>
W tym miejscu jest blokowy do identyfikowania zamówienia, w którym kroki należy wykonać podczas procesu migracji

![Zrzut ekranu przedstawiający kroki migracji](media/migration-classic-resource-manager/migration-flow.png)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="step-1-plan-for-migration"></a>Krok 1: Planowanie migracji
Poniżej przedstawiono kilka najlepszych rozwiązań, które są zalecane, ponieważ oceny migracji zasobów IaaS z klasycznego do usługi Resource Manager:

* Zapoznaj się z artykułem [obsługiwanych i nieobsługiwanych funkcjach i konfiguracjach](migration-classic-resource-manager-overview.md). W przypadku maszyn wirtualnych korzystających z nieobsługiwaną konfiguracją lub funkcji, firma Microsoft zaleca, poczekaj obsługi konfiguracji/funkcji zostanie ogłoszona. Jeśli go odpowiada Twoim potrzebom, Usuń tę funkcję lub wychodzenia tę konfigurację, aby umożliwić migrację.
* Jeśli zautomatyzowano skrypty, które już dziś wdrażanie infrastruktury i aplikacji, spróbuj utworzyć podobnej konfiguracji testu za pomocą tych skryptów migracji. Alternatywnie możesz skonfigurować przykładowych środowisk przy użyciu witryny Azure portal.

> [!IMPORTANT]
> Bramy aplikacji nie są obecnie obsługiwane dla migracji z wersji klasycznej do usługi Resource Manager. Aby przeprowadzić migrację klasycznej sieci wirtualnej z bramą aplikacji, należy usunąć bramę przed uruchomieniem operacji przygotowania, aby przenieść sieć. Po zakończeniu migracji należy ponownie nawiąż połączenie bramy usługi Azure Resource Manager.
>
>Nie można zmigrować automatycznie bramy usługi ExpressRoute, nawiązywania połączenia z obwodów usługi ExpressRoute w innej subskrypcji. W takiej sytuacji należy usunąć bramę usługi ExpressRoute, migrację sieci wirtualnej i ponownie utworzyć bramę. Zobacz [migracji usługi ExpressRoute circuits i skojarzonych sieci wirtualnych z klasycznego modelu wdrażania usługi Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md) Aby uzyskać więcej informacji.

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Krok 2: Zainstaluj najnowszą wersję programu Azure PowerShell
Istnieją dwie główne opcje instalacji programu Azure PowerShell: [Galeria programu PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) lub [sieci Web Platform Installer (WebPI)](https://aka.ms/webpi-azps). WebPI odbiera comiesięcznych aktualizacji. Galeria programu PowerShell otrzymywać aktualizacje w sposób ciągły. Ten artykuł jest oparty na Azure PowerShell w wersji 2.1.0.

Aby uzyskać instrukcje dotyczące instalacji, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>Krok 3: Upewnij się, że jesteś administratorem subskrypcji w witrynie Azure portal
Do przeprowadzania migracji, należy muszą zostać dodane jako współadministratora w ramach subskrypcji w [witryny Azure portal](https://portal.azure.com).

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
2. W menu Centrum wybierz **subskrypcji**. Jeśli nie widzisz, wybierz opcję **wszystkich usług**.
3. Znajdź wpis odpowiednią subskrypcję, a następnie Przyjrzyj się **ROLĘ Moje** pola. Dla administratora współpracującego. wartość powinna być _administrator konta_.

Jeśli nie masz możliwość dodawania administratora współpracującego, skontaktuj się z administrator usługi lub administrator współpracujący dla subskrypcji zapoznania dodane.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Krok 4: Ustaw subskrypcję i zarejestrować się do migracji
Po pierwsze uruchom wiersz polecenia programu PowerShell. W przypadku migracji należy ustawić konfigurowania środowiska na potrzeby modelach klasycznym i usługi Resource Manager.

Zaloguj się do swojego konta, dla modelu usługi Resource Manager.

```powershell
    Connect-AzAccount
```

Pobierz dostępne subskrypcje przy użyciu następującego polecenia:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Ustaw subskrypcję platformy Azure dla bieżącej sesji. W tym przykładzie ustawia domyślną nazwę subskrypcji **moją subskrypcję Azure**. Przykładowa nazwa subskrypcji należy zastąpić własnymi.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Rejestracja jest to jednorazowy krok, ale należy je jeden raz przed próbą wykonania migracji. Bez rejestrowania, zobaczysz następujący komunikat o błędzie:
>
> *Element BadRequest: Subskrypcja nie jest zarejestrowana do migracji.*

Zarejestruj u dostawcy zasobów migracji za pomocą następującego polecenia:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Poczekaj pięć minut rejestracji zakończyć. Aby sprawdzić stan zatwierdzenia, za pomocą następującego polecenia:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Upewnij się, że RegistrationState `Registered` przed kontynuowaniem.

Teraz Zaloguj się do swojego konta w przypadku modelu klasycznego.

```powershell
    Add-AzureAccount
```

Pobierz dostępne subskrypcje przy użyciu następującego polecenia:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Ustaw subskrypcję platformy Azure dla bieżącej sesji. W tym przykładzie ustawia domyślną subskrypcję **moją subskrypcję Azure**. Przykładowa nazwa subskrypcji należy zastąpić własnymi.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Krok 5. Upewnij się, że masz wystarczającej liczby procesorów wirtualnych maszyny wirtualnej usługi Resource Manager platformy Azure w regionie platformy Azure w bieżącym wdrożeniu lub sieci Wirtualnej
Można użyć następującego polecenia programu PowerShell, aby sprawdzić bieżącą liczbę procesorów wirtualnych, które masz w usłudze Azure Resource Manager. Aby dowiedzieć się więcej na temat limitów przydziału procesorów wirtualnych, zobacz [limity i usługi Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-azure-resource-manager).

W tym przykładzie, sprawdza dostępność **zachodnie stany USA** regionu. Przykładowa nazwa regionu Zastąp własnymi.

```powershell
Get-AzVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Krok 6: Uruchamianie poleceń, aby przeprowadzić migrację zasobów IaaS
* [Migrowanie maszyn wirtualnych w usłudze w chmurze (a nie w sieci wirtualnej)](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrowanie maszyn wirtualnych w sieci wirtualnej](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrację konta magazynu](#step-62-migrate-a-storage-account)

> [!NOTE]
> Wszystkie operacje, które są opisane w tym miejscu są idempotentne. Jeśli masz problem inny niż nieobsługiwana funkcja lub błąd konfiguracji, firma Microsoft zaleca ponów próbę wykonania przygotowania, przerwania lub zatwierdzenia operacji. Platforma próbuje wykonać akcję ponownie.


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Krok 6.1: Opcja 1 — Migrowanie maszyn wirtualnych w usłudze w chmurze (a nie w sieci wirtualnej)
Pobierz listę usług w chmurze przy użyciu następującego polecenia, a następnie wybierz usługę w chmurze, które mają zostać zmigrowane. Jeśli maszyny wirtualne w usłudze w chmurze znajdują się w sieci wirtualnej lub mają one ról sieć web i proces roboczy, polecenie zwraca komunikat o błędzie.

```powershell
    Get-AzureService | ft Servicename
```

Pobierz nazwę wdrożenia usługi w chmurze. W tym przykładzie nazwa usługi jest **Moja usługa**. Przykładowa nazwa usługi należy zastąpić nazwę usługi.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Przygotowywanie maszyn wirtualnych w usłudze w chmurze dla migracji. Dostępne są dwie opcje do wyboru.

* **Opcja 1. Migrowanie maszyn wirtualnych z siecią wirtualną utworzone przez platformę**

    Najpierw zweryfikuj, jeśli migrujesz usługę w chmurze przy użyciu następujących poleceń:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Następujące polecenie wyświetla wszystkie ostrzeżenia i błędy, które blokują migracji. Jeśli weryfikacja zakończy się pomyślnie, a następnie można przenieść na **przygotowanie** krok:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Opcja 2. Migrowanie do istniejącej sieci wirtualnej w modelu wdrażania usługi Resource Manager**

    W tym przykładzie nazwa grupy zasobów w **myResourceGroup**, nazwa sieci wirtualnej do **myVirtualNetwork** i nazwę podsieci, aby **mySubNet**. Zastąp nazwy w przykładzie nazw własnych zasobów.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Najpierw zweryfikuj, jeśli migrujesz sieć wirtualną przy użyciu następującego polecenia:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Następujące polecenie wyświetla wszystkie ostrzeżenia i błędy, które blokują migracji. Jeśli weryfikacja zakończy się pomyślnie, następnie możesz przejść do następujących kroku przygotowania:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Po pomyślnym operacji przygotowania w ramach każdej z tych opcji, należy zbadać stan migracji maszyn wirtualnych. Upewnij się, że są one w `Prepared` stanu.

W tym przykładzie nazwa maszyny Wirtualnej w **myVM**. Zastąp nazwę przykładzie na własną nazwę maszyny Wirtualnej.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Sprawdź konfigurację dla zasobów przygotowanego przy użyciu programu PowerShell lub witryny Azure portal. Jeśli chcesz wrócić do poprzedni stan nie jest jeszcze gotowa do migracji, użyj następującego polecenia:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Jeśli przygotowany Konfiguracja wygląda dobrze, możesz przejść do przodu i zatwierdzić zasobów za pomocą następującego polecenia:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Krok 6.1: Opcja 2 — Migrowanie maszyn wirtualnych w sieci wirtualnej

Aby przeprowadzić migrację maszyn wirtualnych w sieci wirtualnej, należy przeprowadzić migrację sieci wirtualnej. Maszyny wirtualne będą automatycznie migrować z siecią wirtualną. Wybierz sieć wirtualną, którą chcesz migrować.
> [!NOTE]
> [Migrowanie pojedynczej maszyny wirtualnej klasycznej](migrate-single-classic-to-resource-manager.md) , tworząc nową maszynę wirtualną usługi Resource Manager z usługą Managed Disks przy użyciu plików (systemu operacyjnego i danych) wirtualnego dysku twardego maszyny wirtualnej.
<br>

> [!NOTE]
> Nazwa sieci wirtualnej może się różnić od przedstawionego w nowym portalu. Nowy Portal Azure zawiera nazwę jako `[vnet-name]` , ale jest nazwa rzeczywistej sieci wirtualnej typu `Group [resource-group-name] [vnet-name]`. Przed migracją, wyszukiwanie nazwy rzeczywistych sieci wirtualnej za pomocą polecenia `Get-AzureVnetSite | Select -Property Name` lub wyświetlić ją w starym portalu Azure. 

W tym przykładzie nazwa sieci wirtualnej w **myVnet**. Przykładowa nazwa sieci wirtualnej należy zastąpić własnymi.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Jeśli sieć wirtualna zawiera sieci web lub ról procesów roboczych lub maszyn wirtualnych z nieobsługiwaną konfiguracją, otrzymasz komunikat o błędzie weryfikacji.

Po pierwsze sprawdź poprawność, jeśli migrujesz sieć wirtualną przy użyciu następującego polecenia:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Następujące polecenie wyświetla wszystkie ostrzeżenia i błędy, które blokują migracji. Jeśli weryfikacja zakończy się pomyślnie, następnie możesz przejść do następujących kroku przygotowania:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Sprawdź konfigurację dla przygotowanej maszyn wirtualnych przy użyciu programu Azure PowerShell lub witryny Azure portal. Jeśli chcesz wrócić do poprzedni stan nie jest jeszcze gotowa do migracji, użyj następującego polecenia:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Jeśli przygotowany Konfiguracja wygląda dobrze, możesz przejść do przodu i zatwierdzić zasobów za pomocą następującego polecenia:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-62-migrate-a-storage-account"></a>Krok 6.2 migracji konta magazynu
Po zakończeniu migracji maszyn wirtualnych, zalecane jest wykonanie następujących sprawdzeń wymagań wstępnych przed przeprowadzeniem migracji konta magazynu.

> [!NOTE]
> Jeśli Twoje konto magazynu nie skojarzone dyski lub danych maszyny Wirtualnej, możesz przejść bezpośrednio do **zweryfikować konta magazynu i Rozpocznij migrację** sekcji.

* **Sprawdzanie wymagań wstępnych migracji maszyn wirtualnych, czy Twoje konto magazynu ma zasoby dyskowe**
    * **Migrowanie klasycznych maszyn wirtualnych, których dyski są przechowywane na koncie magazynu**

        Poniższe polecenie zwraca RoleName DiskName właściwości oraz wszystkich klasycznych dysków maszyn wirtualnych na koncie magazynu. RoleName jest nazwa maszyny wirtualnej, do której jest dołączony dysk. Jeśli to polecenie zwróci dysków następnie upewnij się, migracji maszyn wirtualnych, do których te dyski są dołączone przed przeprowadzeniem migracji konta magazynu.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * **Usuń niedołączone dyski klasycznych maszyn wirtualnych przechowywanych na koncie magazynu**

        Dowiedz się, że niedołączone klasycznych dysków maszyny Wirtualnej w magazynie konta przy użyciu następującego polecenia:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Jeśli powyższe polecenie zwraca dysków Usuń te dyski za pomocą następującego polecenia:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * **Usuwanie obrazów maszyn wirtualnych przechowywanych na koncie magazynu**

        Poniższe polecenie zwraca wszystkie obrazy maszyn wirtualnych z dyskiem systemu operacyjnego przechowywane na koncie magazynu.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         Poniższe polecenie zwraca wszystkie obrazy maszyn wirtualnych z dyskami danych przechowywanych na koncie magazynu.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Aby usunąć wszystkie obrazy maszyn wirtualnych, zwracany przez powyższych poleceń, za pomocą tego polecenia:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* **Sprawdzanie poprawności składowania konta, a następnie uruchom migrację**

    Sprawdzanie poprawności każde konto magazynu, migracji za pomocą następującego polecenia. W tym przykładzie nazwa konta magazynu jest **myStorageAccount**. Przykładowa nazwa należy zastąpić nazwę konta magazynu.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    Następnym krokiem jest przygotowanie konta magazynu do migracji

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Sprawdź konfigurację dla konta magazynu przygotowanego przy użyciu programu Azure PowerShell lub witryny Azure portal. Jeśli chcesz wrócić do poprzedni stan nie jest jeszcze gotowa do migracji, użyj następującego polecenia:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Jeśli przygotowany Konfiguracja wygląda dobrze, możesz przejść do przodu i zatwierdzić zasobów za pomocą następującego polecenia:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Następne kroki
* [Omówienie migracji obsługiwanej przez platformę zasobów rozwiązania IaaS z wersji klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Interfejs wiersza polecenia umożliwia migrację zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Narzędzia społeczności do ułatwiających migrację zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przejrzyj często zadawane pytania dotyczące migracji zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
