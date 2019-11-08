---
title: Migrowanie do Menedżer zasobów przy użyciu programu PowerShell | Microsoft Docs
description: Ten artykuł zawiera opis obsługiwanej przez platformę migracji zasobów IaaS, takich jak maszyny wirtualne (maszyny wirtualne), sieci wirtualne (sieci wirtualnych) i konta magazynu z klasycznego do Azure Resource Manager (ARM) za pomocą poleceń Azure PowerShell
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
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 01d5670add82291cb91264ab41fcd312a338840c
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749328"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migrowanie zasobów IaaS z klasycznego do Azure Resource Manager przy użyciu Azure PowerShell
W tych krokach pokazano, jak za pomocą poleceń Azure PowerShell przeprowadzić migrację zasobów infrastruktury jako usługi (IaaS) z klasycznego modelu wdrażania do modelu wdrażania Azure Resource Manager.

Jeśli chcesz, możesz także przeprowadzić migrację zasobów przy użyciu [interfejsu wiersza polecenia platformy Azure (Azure CLI)](../linux/migration-classic-resource-manager-cli.md).

* Aby zapoznać się z obsługiwanymi scenariuszami migracji, zobacz [migracja zasobów IaaS z wersji klasycznej do Azure Resource Manager przy użyciu platformy obsługiwanej przez platformę](migration-classic-resource-manager-overview.md).
* Aby zapoznać się ze szczegółowymi wskazówkami i przewodnikiem dotyczącym migracji, zobacz [technicznie szczegółowee na platformie obsługiwana migracja z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md)

<br>
Oto schemat blokowy służący do identyfikowania kolejności, w której należy wykonać kroki w procesie migracji

![Zrzut ekranu przedstawiający kroki migracji](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>Krok 1. Planowanie migracji
Poniżej przedstawiono kilka najlepszych rozwiązań, które zalecamy podczas szacowania migracji zasobów IaaS z klasycznej do Menedżer zasobów:

* Przeczytaj [obsługiwane i nieobsługiwane funkcje i konfiguracje](migration-classic-resource-manager-overview.md). Jeśli masz maszyny wirtualne korzystające z nieobsługiwanych konfiguracji lub funkcji, zalecamy zaczekanie na anonsowanie obsługi konfiguracji/funkcji. Alternatywnie, jeśli odpowiada Twoim potrzebom, Usuń tę funkcję lub wykorzystaj ją z tej konfiguracji w celu włączenia migracji.
* W przypadku zautomatyzowanych skryptów, które obecnie wdrażają infrastrukturę i aplikacje, spróbuj utworzyć podobną konfigurację testu przy użyciu tych skryptów do migracji. Alternatywnie można skonfigurować przykładowe środowiska przy użyciu Azure Portal.

> [!IMPORTANT]
> Bramy aplikacji nie są obecnie obsługiwane w przypadku migracji z wersji klasycznej do Menedżer zasobów. Aby przeprowadzić migrację klasycznej sieci wirtualnej z bramą aplikacji, Usuń bramę przed uruchomieniem operacji przygotowywania, aby przenieść sieć. Po zakończeniu migracji Połącz się ponownie z bramą w Azure Resource Manager.
>
>Nie można automatycznie migrować bram ExpressRoute łączących się ze obwodami usługi ExpressRoute w innej subskrypcji. W takich przypadkach Usuń bramę ExpressRoute, Przeprowadź migrację sieci wirtualnej i Utwórz ponownie bramę. Aby uzyskać więcej informacji, zobacz [Migrowanie obwodów usługi ExpressRoute i skojarzonych sieci wirtualnych z klasycznej do modelu wdrażania Menedżer zasobów](../../expressroute/expressroute-migration-classic-resource-manager.md) .

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Krok 2. Instalacja najnowszej wersji programu Azure PowerShell
Dostępne są dwie główne opcje instalacji Azure PowerShell: [Galeria programu PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) lub [Instalatora platformy sieci Web (Instalatora WebPI)](https://aka.ms/webpi-azps). Instalatora WebPI otrzymuje comiesięczne aktualizacje. Galeria programu PowerShell otrzymuje aktualizacje w sposób ciągły. Ten artykuł jest oparty na Azure PowerShell wersji 2.1.0.

Instrukcje instalacji znajdują się w temacie [How to Install and configure Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>Krok 3. Upewnij się, że jesteś administratorem subskrypcji w Azure Portal
Aby przeprowadzić migrację, należy dodać jako współadministratora dla subskrypcji w [Azure Portal](https://portal.azure.com).

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
2. W menu centrum wybierz pozycję **subskrypcja**. Jeśli go nie widzisz, wybierz pozycję **wszystkie usługi**.
3. Znajdź odpowiedni wpis subskrypcji, a następnie zapoznaj się z polem **My role** . Dla współadministratora wartość powinna być _administratorem konta_.

Jeśli nie możesz dodać współadministratora, skontaktuj się z administratorem usługi lub współadministratorem, aby uzyskać subskrypcję, która ma zostać dodana.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Krok 4. Ustawianie subskrypcji i rejestrowanie się w celu migracji
Najpierw Uruchom wiersz polecenia programu PowerShell. W przypadku migracji należy skonfigurować środowisko dla środowiska klasycznego i Menedżer zasobów.

Zaloguj się do swojego konta, aby uzyskać Menedżer zasobów model.

```powershell
    Connect-AzAccount
```

Uzyskaj dostępne subskrypcje przy użyciu następującego polecenia:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Ustaw subskrypcję platformy Azure dla bieżącej sesji. Ten przykład ustawia domyślną nazwę subskrypcji na **moją subskrypcję platformy Azure**. Zastąp własną przykładową nazwę subskrypcji.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Rejestracja to jednorazowy krok, ale należy wykonać go raz przed podjęciem próby migracji. Bez rejestrowania zostanie wyświetlony następujący komunikat o błędzie:
>
> *Nieprawidłowego żądania: subskrypcja nie jest zarejestrowana na potrzeby migracji.*

Zarejestruj się w dostawcy zasobów migracji przy użyciu następującego polecenia:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Zaczekaj pięć minut na zakończenie rejestracji. Stan zatwierdzenia można sprawdzić za pomocą następującego polecenia:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Przed kontynuowaniem upewnij się, że RegistrationState jest `Registered`.

Teraz Zaloguj się do swojego konta dla modelu klasycznego.

```powershell
    Add-AzureAccount
```

Uzyskaj dostępne subskrypcje przy użyciu następującego polecenia:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Ustaw subskrypcję platformy Azure dla bieżącej sesji. W tym przykładzie ustawiono domyślną subskrypcję dla **mojej subskrypcji platformy Azure**. Zastąp własną przykładową nazwę subskrypcji.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Krok 5. Upewnij się, że masz wystarczającą Azure Resource Manager procesorów wirtualnych vCPU maszyny wirtualnej w regionie świadczenia usługi Azure bieżącego wdrożenia lub sieci wirtualnej
Aby sprawdzić bieżącą liczbę procesorów wirtualnych vCPU w Azure Resource Manager, można użyć następującego polecenia programu PowerShell. Aby dowiedzieć się więcej o przydziałach vCPU, zobacz [limity i Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-azure-resource-manager).

Ten przykład sprawdza dostępność w regionie **zachodnie stany USA** . Zamień nazwę przykładowego regionu na własny.

```powershell
Get-AzVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Krok 6. Uruchamianie poleceń w celu migrowania zasobów IaaS
* [Migrowanie maszyn wirtualnych w usłudze w chmurze (nie w sieci wirtualnej)](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrowanie maszyn wirtualnych w sieci wirtualnej](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrowanie konta magazynu](#step-62-migrate-a-storage-account)

> [!NOTE]
> Wszystkie operacje opisane w tym miejscu to idempotentne. Jeśli wystąpi problem inny niż nieobsługiwana funkcja lub błąd konfiguracji, zalecamy ponowną próbę wykonania operacji przygotowywania, przerwania lub zatwierdzania. Następnie platforma próbuje wykonać akcję ponownie.


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Krok 6,1: Opcja 1 — Migrowanie maszyn wirtualnych w usłudze w chmurze (poza siecią wirtualną)
Pobierz listę usług w chmurze przy użyciu następującego polecenia, a następnie wybierz usługę w chmurze, którą chcesz zmigrować. Jeśli maszyny wirtualne w usłudze w chmurze znajdują się w sieci wirtualnej lub mają role sieci Web lub proces roboczy, polecenie zwróci komunikat o błędzie.

```powershell
    Get-AzureService | ft Servicename
```

Pobierz nazwę wdrożenia dla usługi w chmurze. W tym przykładzie nazwa usługi to **Moja usługa**. Zastąp przykładową nazwę usługi własną nazwą usługi.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Przygotuj maszyny wirtualne w usłudze w chmurze na potrzeby migracji. Dostępne są dwie opcje do wyboru.

* **Opcja 1. Migrowanie maszyn wirtualnych do sieci wirtualnej utworzonej na platformie**

    Najpierw sprawdź, czy można przeprowadzić migrację usługi w chmurze przy użyciu następujących poleceń:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Następujące polecenie wyświetla ostrzeżenia i błędy, które blokują migrację. Jeśli walidacja zakończyła się pomyślnie, możesz przejść do kroku **Prepare** :

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Opcja 2. Migrowanie do istniejącej sieci wirtualnej w modelu wdrażania Menedżer zasobów**

    Ten przykład służy do ustawiania nazwy grupy zasobów dla **zasobu, nazwy sieci wirtualnej do** **myVirtualNetwork** i nazwy **podsieci.** Zastąp nazwy w przykładzie nazwami własnych zasobów.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Najpierw sprawdź, czy można przeprowadzić migrację sieci wirtualnej za pomocą następującego polecenia:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Następujące polecenie wyświetla ostrzeżenia i błędy, które blokują migrację. Jeśli walidacja zakończyła się pomyślnie, możesz przejść do następujących kroków:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Po pomyślnym zakończeniu operacji przygotowania z jedną z powyższych opcji wykonaj zapytanie o stan migracji maszyn wirtualnych. Upewnij się, że są one w stanie `Prepared`.

W tym przykładzie nazwa maszyny wirtualnej jest ustawiana na **myVM**. Zastąp przykładową nazwę własną nazwą maszyny wirtualnej.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Sprawdź konfigurację przygotowanych zasobów przy użyciu programu PowerShell lub Azure Portal. Jeśli nie jesteś gotowy do migracji i chcesz wrócić do starego stanu, użyj następującego polecenia:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Jeśli gotowa konfiguracja wygląda dobrze, można przenieść do przodu i zatwierdzić zasoby przy użyciu następującego polecenia:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Krok 6,1: Opcja 2 — Migrowanie maszyn wirtualnych w sieci wirtualnej

Aby przeprowadzić migrację maszyn wirtualnych w sieci wirtualnej, należy przeprowadzić migrację sieci wirtualnej. Maszyny wirtualne są automatycznie migrowane z siecią wirtualną. Wybierz sieć wirtualną, która ma zostać poddana migracji.
> [!NOTE]
> [Przeprowadź migrację pojedynczej klasycznej maszyny wirtualnej](migrate-single-classic-to-resource-manager.md) , tworząc nową Menedżer zasobów maszynę wirtualną z Managed disks przy użyciu plików VHD (systemu operacyjnego i danych) maszyny wirtualnej.
<br>

> [!NOTE]
> Nazwa sieci wirtualnej może się różnić od tego, co jest widoczne w nowym portalu. Nowa witryna Azure portal wyświetla nazwę jako `[vnet-name]` ale rzeczywista nazwa sieci wirtualnej jest typu `Group [resource-group-name] [vnet-name]`. Przed przeprowadzeniem migracji Wyszukaj rzeczywistą nazwę sieci wirtualnej przy użyciu polecenia `Get-AzureVnetSite | Select -Property Name` lub Wyświetl je w starej witrynie Azure Portal. 

Ten przykład ustawia nazwę sieci wirtualnej na **myVnet**. Zamień nazwę przykładowej sieci wirtualnej na własną.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Jeśli sieć wirtualna zawiera role sieci Web lub procesu roboczego lub maszyny wirtualne z nieobsługiwanymi konfiguracjami, zostanie wyświetlony komunikat o błędzie walidacji.

Najpierw sprawdź, czy można przeprowadzić migrację sieci wirtualnej za pomocą następującego polecenia:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Następujące polecenie wyświetla ostrzeżenia i błędy, które blokują migrację. Jeśli walidacja zakończyła się pomyślnie, możesz przejść do następujących kroków:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Sprawdź konfigurację przygotowanych maszyn wirtualnych przy użyciu Azure PowerShell lub Azure Portal. Jeśli nie jesteś gotowy do migracji i chcesz wrócić do starego stanu, użyj następującego polecenia:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Jeśli gotowa konfiguracja wygląda dobrze, można przenieść do przodu i zatwierdzić zasoby przy użyciu następującego polecenia:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-62-migrate-a-storage-account"></a>Krok 6,2 Migrowanie konta magazynu
Po zakończeniu migracji maszyn wirtualnych zalecamy wykonanie poniższych testów wymagań wstępnych przed przeprowadzeniem migracji kont magazynu.

> [!NOTE]
> Jeśli konto magazynu nie ma skojarzonych dysków lub danych maszyny wirtualnej, możesz przejść bezpośrednio do sekcji **Weryfikuj konto magazynu i Rozpocznij migrację** .

* **Sprawdzanie wymagań wstępnych w przypadku migrowania dowolnych maszyn wirtualnych lub konta magazynu ma zasoby dyskowe**
    * **Migrowanie klasycznych maszyn wirtualnych, których dyski są przechowywane na koncie magazynu**

        Następujące polecenie zwraca właściwości rolename i diskname wszystkich klasycznych dysków maszyny wirtualnej na koncie magazynu. Rolename to nazwa maszyny wirtualnej, do której jest dołączony dysk. Jeśli to polecenie zwraca dyski, upewnij się, że maszyny wirtualne, do których te dyski są dołączone, są migrowane przed migracją konta magazynu.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * **Usuń niedołączone klasyczne dyski maszyny wirtualnej przechowywane na koncie magazynu**

        Znajdź niedołączone klasyczne dyski maszyny wirtualnej na koncie magazynu przy użyciu następującego polecenia:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Jeśli powyższe polecenie zwraca dyski, usuń te dyski przy użyciu następującego polecenia:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * **Usuń obrazy maszyn wirtualnych przechowywane na koncie magazynu**

        Następujące polecenie zwraca wszystkie obrazy maszyn wirtualnych z dyskiem systemu operacyjnego przechowywanego na koncie magazynu.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         Następujące polecenie zwraca wszystkie obrazy maszyn wirtualnych z dyskami danych przechowywanymi na koncie magazynu.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Usuń wszystkie obrazy maszyn wirtualnych zwrócone przez powyższe polecenia za pomocą tego polecenia:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* **Sprawdź poprawność konta magazynu i Rozpocznij migrację**

    Sprawdź poprawność każdego konta magazynu do migracji przy użyciu następującego polecenia. W tym przykładzie nazwa konta magazynu to **mojekontomagazynu**. Zastąp przykładową nazwę nazwą własnego konta magazynu.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    Następnym krokiem jest przygotowanie konta magazynu do migracji

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Sprawdź konfigurację przygotowanego konta magazynu przy użyciu Azure PowerShell lub Azure Portal. Jeśli nie jesteś gotowy do migracji i chcesz wrócić do starego stanu, użyj następującego polecenia:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Jeśli gotowa konfiguracja wygląda dobrze, można przenieść do przodu i zatwierdzić zasoby przy użyciu następującego polecenia:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Następne kroki
* [Przegląd obsługiwanej przez platformę migracji zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Użyj interfejsu wiersza polecenia do migrowania zasobów IaaS z klasycznego do Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Narzędzia społecznościowe ułatwiające migrację zasobów IaaS z klasycznej do Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Zapoznaj się z najczęściej zadawanymi pytaniami dotyczącymi migrowania zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
