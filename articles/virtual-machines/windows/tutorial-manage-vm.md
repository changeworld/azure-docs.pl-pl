---
title: Tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi za pomocą modułu Azure PowerShell | Microsoft Docs
description: Samouczek — tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi za pomocą modułu Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/23/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: cce3fd003888c76490cb402b658f5c3aa76ab11e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="create-and-manage-windows-vms-with-the-azure-powershell-module"></a>Tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi za pomocą modułu Azure PowerShell

Maszyny wirtualne platformy Azure oferują w pełni konfigurowalne i elastyczne środowiska obliczeniowe. W tym samouczku opisano podstawowe elementy wdrożenia maszyny wirtualnej platformy Azure, takie jak wybieranie rozmiaru i obrazu maszyny wirtualnej oraz wdrażanie maszyny wirtualnej. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej i łączenie z nią
> * Wybieranie i używanie obrazów maszyn wirtualnych
> * Wyświetlanie i używanie określonych rozmiarów maszyn wirtualnych
> * Zmienianie rozmiaru maszyny wirtualnej
> * Wyświetlanie stanu maszyny wirtualnej i jego omówienie


[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli chcesz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 5.3 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. 

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów na pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Grupę zasobów należy utworzyć przed maszyną wirtualną. W poniższym przykładzie grupa zasobów o nazwie *myResourceGroupVM* zostanie utworzona w regionie *EastUS*:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

Grupa zasobów jest określana podczas tworzenia lub modyfikowania maszyn wirtualnych, co zostało przedstawione w tym samouczku.

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Podczas tworzenia maszyny wirtualnej masz dostęp do kilku opcji, takich jak obraz systemu operacyjnego, konfiguracja sieci i poświadczenia administracyjne. W tym przykładzie zostanie utworzona maszyna wirtualna o nazwie *myVM* z domyślnie uruchomioną najnowszą wersją systemu Windows Server 2016 Datacenter.

Ustaw nazwę użytkownika i hasło potrzebne dla konta administratora na maszynie wirtualnej przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Utwórz maszynę wirtualną za pomocą polecenia [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>Łączenie z maszyną wirtualną

Po zakończeniu wdrożenia utwórz połączenie pulpitu zdalnego z maszyną wirtualną.

Uruchom następujące polecenia, aby zwrócić publiczny adres IP maszyny wirtualnej. Zapisz ten adres IP, aby połączyć się z nim w przeglądarce w celu przetestowania połączenia z siecią Web w przyszłym kroku.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

Użyj następującego polecenia na swojej maszynie lokalnej, aby utworzyć sesję usług pulpitu zdalnego z maszyną wirtualną. Zamień adres IP na *publiczny adres IP* Twojej maszyny wirtualnej. Po wyświetleniu monitu wprowadź poświadczenia używane podczas tworzenia maszyny wirtualnej.

```powershell
mstsc /v:<publicIpAddress>
```

W oknie **Zabezpieczenia systemu Windows** wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. Wpisz nazwę użytkownika i hasło utworzone dla maszyny wirtualnej, a następnie kliknij przycisk **OK**.

## <a name="understand-vm-images"></a>Omówienie obrazów maszyny wirtualnej

Witryna Azure Marketplace udostępnia wiele obrazów maszyn wirtualnych, które mogą służyć do tworzenia nowej maszyny wirtualnej. W poprzednich krokach utworzono maszynę wirtualną przy użyciu obrazu systemu Windows Server 2016 Datacenter. W tym kroku moduł PowerShell jest używany do wyszukiwania w witrynie Marketplace innych obrazów systemu Windows, które mogą być również używane jako podstawa nowych maszyn wirtualnych. Proces ten składa się z wyszukiwania wydawcy, oferty, jednostki SKU i opcjonalnie numeru wersji w celu [zidentyfikowania](cli-ps-findimage.md#terminology) obrazu. 

Użyj polecenia [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), aby uzyskać listę wydawców obrazów:

```powersehll
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Użyj polecenia [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer), aby uzyskać listę ofert obrazów. To polecenie umożliwia filtrowanie zwracanej listy według określonego wydawcy:

```azurepowershell-interactive
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

```azurepowershell-interactive
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer EastUS 
WindowsServer     MicrosoftWindowsServer EastUS   
WindowsServer-HUB MicrosoftWindowsServer EastUS   
```

Polecenie [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) spowoduje następnie odfiltrowanie według nazwy wydawcy i oferty w celu uzyskania listy nazw obrazów.

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```azurepowershell-interactive
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

Te informacje mogą być używane na potrzeby wdrażania maszyny wirtualnej za pomocą określonego obrazu. W tym przykładzie wdrażasz maszynę wirtualną przy użyciu obrazu systemu Windows Server 2016 z kontenerami.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

Parametr `-AsJob` umożliwia tworzenie maszyny wirtualnej w tle, co powoduje powrót do wiersza polecenia programu PowerShell. Możesz wyświetlić szczegóły zadań w tle, używając polecenia cmdlet `Job`.


## <a name="understand-vm-sizes"></a>Omówienie rozmiarów maszyn wirtualnych

Rozmiar maszyny wirtualnej określa ilość zasobów obliczeniowych, takich jak procesor CPU, procesor GPU i pamięć, które są dostępne dla maszyny wirtualnej. Tworzone maszyny wirtualne powinny mieć rozmiar odpowiadający oczekiwanemu obciążeniu. Jeśli obciążenie zwiększy się, można zmienić rozmiar istniejącej maszyny wirtualnej.

### <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych

W poniższej tabeli przedstawiono kategorie rozmiarów podzielone według przypadków użycia.  
| Typ                     | Typowe rozmiary           |    Opis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Zastosowania ogólne](sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0–7| Zrównoważona moc procesora CPU w stosunku do pamięci. Opcja idealna w przypadku tworzenia i testowania, małych i średnich aplikacji oraz rozwiązań dotyczących danych.  |
| [Optymalizacja pod kątem obliczeń](sizes-compute.md)   | Fs, F             | Duża moc procesora CPU w stosunku do pamięci. Opcja dobra w przypadku aplikacji o średnim ruchu, urządzeń sieciowych i procesów wsadowych.        |
| [Optymalizacja pod kątem pamięci](sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Duża ilość pamięci na rdzeń. Opcja bardzo dobra w przypadku relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizowania w pamięci.                 |
| [Optymalizacja pod kątem magazynu](sizes-storage.md)      | Ls                | Wysoka przepływność dysku i duża liczba operacji we/wy. Opcja idealna w przypadku danych big data oraz baz danych SQL i NoSQL.                                                         |
| [Procesor GPU](sizes-gpu.md)          | NV, NC            | Maszyny wirtualne wyspecjalizowane pod kątem intensywnego renderowania grafiki i edytowania materiałów wideo.       |
| [Wysoka wydajność](sizes-hpc.md) | H, A8-11          | Maszyny wirtualne z najbardziej wydajnymi procesorami CPU oraz, opcjonalnie, interfejsami sieciowymi zapewniającymi wysoką przepływność (RDMA). 


### <a name="find-available-vm-sizes"></a>Wyszukiwanie dostępnych rozmiarów maszyn wirtualnych

Aby wyświetlić listę dostępnych rozmiarów maszyn wirtualnych w danym regionie, użyj polecenia [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize).

```azurepowershell-interactive
Get-AzureRmVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>Zmienianie rozmiaru maszyny wirtualnej

Po wdrożeniu maszyny wirtualnej można zmienić jej rozmiar w celu zwiększenia lub zmniejszenia alokacji zasobów.

Przed zmianą rozmiaru maszyny wirtualnej sprawdź, czy żądany rozmiar jest dostępny w bieżącym klastrze maszyny wirtualnej. Polecenie [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) zwraca listę rozmiarów. 

```azurepowershell-interactive
Get-AzureRmVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

Jeśli żądany rozmiar maszyny wirtualnej jest dostępny, można go zmienić dla włączonej maszyny, ale zostanie ona ponownie uruchomiona w trakcie tej operacji.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
```

Jeśli żądany rozmiar jest niedostępny w bieżącym klastrze, przed rozpoczęciem operacji zmiany rozmiaru należy cofnąć przydział maszyny wirtualnej. Pamiętaj, że gdy maszyna wirtualna zostanie ponownie włączona, wszystkie dane na dysku tymczasowym zostaną usunięte, a publiczny adres IP zmieni się, chyba że używany jest statyczny adres IP. 

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -Name $vm.name
```

## <a name="vm-power-states"></a>Stany zasilania maszyny wirtualnej

Maszyna wirtualna platformy Azure może znajdować się w jednym z wielu stanów zasilania. Ten stan reprezentuje bieżący stan maszyny wirtualnej z punktu widzenia funkcji hypervisor. 

### <a name="power-states"></a>Stany zasilania

| Stan zasilania | Opis
|----|----|
| Uruchamianie | Wskazuje, że maszyna wirtualna jest uruchamiana. |
| Działanie | Wskazuje, że maszyna wirtualna działa. |
| Zatrzymywanie | Wskazuje, że maszyna wirtualna jest zatrzymywana. | 
| Zatrzymano | Wskazuje, że maszyna wirtualna została zatrzymana. Pamiętaj, że opłaty za operacje obliczeniowe są także naliczane w przypadku maszyn wirtualnych w stanie Zatrzymano.  |
| Cofanie przydziału | Wskazuje, że przydział maszyny wirtualnej jest cofany. |
| Cofnięto przydział | Wskazuje, że maszyna wirtualna została całkowicie usunięta z funkcji hypervisor, ale jest nadal dostępna na płaszczyźnie kontroli. Opłaty za operacje obliczeniowe nie są naliczane w przypadku maszyn wirtualnych w stanie Cofnięto przydział. |
| - | Wskazuje, że stan zasilania maszyny wirtualnej jest nieznany. |

### <a name="find-power-state"></a>Znajdowanie stanu zasilania

Aby pobrać stan określonej maszyny wirtualnej, użyj polecenia [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm). Pamiętaj, aby określić prawidłową nazwę maszyny wirtualnej i grupy zasobów. 

```azurepowershell-interactive
Get-AzureRmVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Dane wyjściowe:

```azurepowershell-interactive
Status
------
PowerState/running
```

## <a name="management-tasks"></a>Zadania zarządzania

W trakcie cyklu życia maszyny wirtualnej można uruchamiać zadania zarządzania, takie jak uruchamianie, zatrzymywanie lub usuwanie maszyny wirtualnej. Ponadto można tworzyć skrypty służące do automatyzowania zadań powtarzających się lub złożonych. Dzięki użyciu programu Azure PowerShell wiele typowych zadań zarządzania można uruchamiać z poziomu wiersza polecenia lub w skryptach.

### <a name="stop-virtual-machine"></a>Zatrzymywanie maszyny wirtualnej

Do zatrzymywania maszyny wirtualnej i cofania jej przydziału służy polecenie [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm):

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
```

Jeśli maszyna wirtualna ma zachować stan Aprowizowano, użyj parametru -StayProvisioned.

### <a name="start-virtual-machine"></a>Uruchamianie maszyny wirtualnej

```azurepowershell-interactive
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM"
```

### <a name="delete-resource-group"></a>Usuwanie grupy zasobów

Usunięcie grupy zasobów spowoduje również usunięcie wszystkich znajdujących się w niej zasobów.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroupVM" -Force
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje dotyczące podstawowych zadań tworzenia maszyny wirtualnej i zarządzania nią, takie jak:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej i łączenie z nią
> * Wybieranie i używanie obrazów maszyn wirtualnych
> * Wyświetlanie i używanie określonych rozmiarów maszyn wirtualnych
> * Zmienianie rozmiaru maszyny wirtualnej
> * Wyświetlanie stanu maszyny wirtualnej i jego omówienie

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat dysków maszyn wirtualnych.  

> [!div class="nextstepaction"]
> [Tworzenie dysków maszyn wirtualnych i zarządzanie nimi](./tutorial-manage-data-disk.md)
