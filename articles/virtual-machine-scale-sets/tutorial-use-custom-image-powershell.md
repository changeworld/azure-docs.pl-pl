---
title: Samouczek — korzystanie z niestandardowego obrazu maszyny wirtualnej w zestawie skalowania za pośrednictwem programu Azure PowerShell | Microsoft Docs
description: Dowiedz się, jak za pomocą programu Azure PowerShell utworzyć niestandardowy obraz maszyny wirtualnej, którego można użyć do wdrożenia zestawu skalowania maszyny wirtualnej
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: bca92b5079b5ef21c954b46bfbeab9b973828fc8
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427444"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Samouczek: tworzenie niestandardowego obrazu i używanie go dla zestawów skalowania maszyn wirtualnych za pośrednictwem programu Azure PowerShell
Podczas tworzenia zestawu skalowania należy wskazać obraz używany do wdrożenia wystąpień maszyn wirtualnych. Aby zmniejszyć liczbę zadań wykonywanych po wdrożeniu wystąpień maszyn wirtualnych, można użyć niestandardowego obrazu maszyny wirtualnej. Niestandardowy obraz maszyny wirtualnej obejmuje wszystkie wymagane instalacje i konfiguracje aplikacji. Wszystkie wystąpienia maszyn wirtualnych utworzone w zestawie skalowania używają niestandardowego obrazu maszyny wirtualnej i są gotowe do obsługi ruchu aplikacji. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie i dostosowywanie maszyny wirtualnej
> * Anulowanie aprowizacji i uogólnianie maszyny wirtualnej
> * Tworzenie niestandardowego obrazu maszyny wirtualnej ze źródłowej maszyny wirtualnej
> * Wdrażanie zestawu skalowania, który używa niestandardowego obrazu maszyny wirtualnej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek będzie wymagał modułu programu Azure PowerShell w wersji 6.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. 


## <a name="create-and-configure-a-source-vm"></a>Tworzenie i konfigurowanie źródłowej maszyny wirtualnej

>[!NOTE]
> Ten samouczek zawiera opis kroków procesu tworzenia i używania obrazu uogólnionej maszyny wirtualnej. Tworzenie zestawu skalowania na podstawie wyspecjalizowanego wirtualnego dysku twardego nie jest obsługiwane.

Najpierw utwórz grupę zasobów za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup), a następnie utwórz maszynę wirtualną za pomocą polecenia [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Ta maszyna wirtualna będzie używana jako źródło dla niestandardowego obrazu maszyny wirtualnej. Poniższy przykład obejmuje tworzenie maszyny wirtualnej *myCustomVM* w grupie zasobów *myResourceGroup*. Po wyświetleniu monitu wprowadź nazwę użytkownika i hasło, które będą używane jako poświadczenia logowania dla maszyny wirtualnej:

```azurepowershell-interactive
# Create a resource a group
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"

# Create a Windows Server 2016 Datacenter VM
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Name "myCustomVM" `
  -ImageName "Win2016Datacenter" `
  -OpenPorts 3389
```

Aby nawiązać połączenie z maszyną wirtualną, wyświetl publiczny adres IP za pomocą polecenia [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) w następujący sposób:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Utwórz połączenie zdalne z maszyną wirtualną. Jeśli korzystasz z usługi Azure Cloud Shell, wykonaj ten krok przy użyciu wiersza polecenia lokalnego programu PowerShell lub klienta pulpitu zdalnego. Podaj własny adres IP z poprzedniego polecenia. Po wyświetleniu monitu wprowadź poświadczenia użyte podczas tworzenia maszyny wirtualnej w pierwszym kroku:

```powershell
mstsc /v:<IpAddress>
```

Aby dostosować maszynę wirtualną, należy zainstalować podstawowy serwer internetowy. Dzięki temu wdrożona maszyna wirtualna będzie miała wszystkie pakiety wymagane do uruchamiania aplikacji internetowej. Otwórz wiersz polecenia lokalnego programu PowerShell na maszynie wirtualnej i zainstaluj serwer internetowy IIS za pomocą polecenia [Install-WindowsFeature](/powershell/module/servermanager/install-windowsfeature) w następujący sposób:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Ostatnim krokiem jest uogólnienie maszyny wirtualnej w celu przygotowania jej do użycia jako obraz niestandardowy. Program Sysprep usuwa wszystkie informacje i konfiguracje osobiste oraz resetuje maszynę wirtualną do czystego stanu przeznaczonego do przyszłych wdrożeń. Aby uzyskać więcej informacji, zobacz [Używanie programu Sysprep: wprowadzenie](https://technet.microsoft.com/library/bb457073.aspx).

Aby uogólnić maszynę wirtualną, uruchom program Sysprep i ustaw maszynę wirtualną jako gotową do użycia. Po zakończeniu zamknij maszynę wirtualną za pomocą programu Sysprep:

```powershell
C:\Windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown
```

Gdy program Sysprep zakończy proces i zamknie maszynę wirtualną, połączenie zdalne z maszyną wirtualną zostanie zamknięte automatycznie.


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Tworzenie niestandardowego obrazu maszyny wirtualnej ze źródłowej maszyny wirtualnej
Źródłowa maszyna wirtualna została dostosowana przez zainstalowanie serwera internetowego IIS. Utworzymy niestandardowy obraz maszyny wirtualnej, który będzie używany z zestawem skalowania.

Aby można było utworzyć obraz, należy cofnąć przydział maszyny wirtualnej. Cofnij przydział maszyny wirtualnej za pomocą polecenia [Stop-AzureRmVm](/powershell/module/azurerm.compute/stop-azurermvm). Następnie ustaw dla maszyny wirtualnej stan uogólniony za pomocą polecenia [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm), aby poinformować platformę Azure, że maszyna wirtualna jest gotowa do użycia jako obraz niestandardowy. Obraz można utworzyć tylko za pomocą uogólnionej maszyny wirtualnej:

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Force
Set-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Generalized
```

Cofanie przydziału i uogólnianie maszyny wirtualnej może potrwać kilka minut.

Utwórz obraz maszyny wirtualnej za pomocą poleceń [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) i [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). W poniższym przykładzie utworzono obraz o nazwie *myImage* za pomocą maszyny wirtualnej:

```azurepowershell-interactive
# Get VM object
$vm = Get-AzureRmVM -Name "myCustomVM" -ResourceGroupName "myResourceGroup"

# Create the VM image configuration based on the source VM
$image = New-AzureRmImageConfig -Location "EastUS" -SourceVirtualMachineId $vm.ID 

# Create the custom VM image
New-AzureRmImage -Image $image -ImageName "myImage" -ResourceGroupName "myResourceGroup"
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Tworzenie zestawu skalowania z niestandardowego obrazu maszyny wirtualnej
Utwórz zestaw skalowania za pomocą polecenia [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) z parametrem `-ImageName` umożliwiającym zdefiniowanie niestandardowego obrazu maszyny wirtualnej utworzonego w poprzednim kroku. Musisz również utworzyć moduł równoważenia obciążenia, który umożliwia kierowanie ruchu do poszczególnych wystąpień maszyn wirtualnych. Moduł równoważenia obciążenia zawiera reguły, które pozwalają kierować ruchem na porcie TCP 80 oraz korzystać z ruchu pulpitu zdalnego na porcie TCP 3389 i komunikacji zdalnej programu PowerShell na porcie TCP 5985. Po wyświetleniu monitu podaj własne odpowiednie poświadczenia administracyjne dla wystąpień maszyn wirtualnych w zestawie skalowania:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "myImage"
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.


## <a name="test-your-scale-set"></a>Testowanie zestawu skalowania
Aby zobaczyć, jak działa zestaw skalowania, uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress) w następujący sposób:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Name "myPublicIPAddress" | Select IpAddress
```

Wprowadź publiczny adres IP w przeglądarce internetowej. Zostanie wyświetlona domyślna strona serwera IIS, jak pokazano w poniższym przykładzie:

![Serwer IIS uruchomiony za pomocą niestandardowego obrazu maszyny wirtualnej](media/tutorial-use-custom-image-powershell/default-iis-website.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Aby pozbyć się zestawu skalowania i dodatkowych zasobów, usuń grupę zasobów wraz z całą zawartością za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Parametr `-Force` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu. Parametr `-AsJob` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Następne kroki
W tym samouczku omówiono tworzenie niestandardowego obrazu maszyny wirtualnej i używanie go z zestawami skalowania za pośrednictwem programu Azure PowerShell:

> [!div class="checklist"]
> * Tworzenie i dostosowywanie maszyny wirtualnej
> * Anulowanie aprowizacji i uogólnianie maszyny wirtualnej
> * Tworzenie niestandardowego obrazu maszyny wirtualnej
> * Wdrażanie zestawu skalowania, który używa niestandardowego obrazu maszyny wirtualnej

Przejdź do następnego samouczka, aby dowiedzieć się, jak wdrożyć aplikacje w zestawie skalowania.

> [!div class="nextstepaction"]
> [Deploy applications to your scale sets (Wdrażanie aplikacji w zestawach skalowania)](tutorial-install-apps-powershell.md)
