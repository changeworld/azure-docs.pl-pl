---
title: Samouczek — instalowanie aplikacji w zestawie skalowania za pomocą programu Azure PowerShell | Microsoft Docs
description: Dowiedz się, jak za pomocą programu Azure PowerShell instalować aplikacje w zestawach skalowania maszyn wirtualnych, korzystając z rozszerzenia niestandardowego skryptu
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
ms.openlocfilehash: 4f162dcf58316e6d9f39b71be37abf5626e93c75
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38295828"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-azure-powershell"></a>Samouczek: instalowanie aplikacji w zestawach skalowania maszyn wirtualnych za pomocą programu Azure PowerShell
Aby uruchamiać aplikacje na wystąpieniach maszyn wirtualnych w zestawie skalowania, musisz najpierw zainstalować składniki aplikacji i wymagane pliki. W poprzednim samouczku omówiono tworzenie niestandardowego obrazu maszyny wirtualnej i wdrażanie własnych wystąpień maszyn wirtualnych. Niestandardowy obraz zawierał ręczne instalacje i konfiguracje aplikacji. Można również zautomatyzować instalację aplikacji w zestawie skalowania po wdrożeniu poszczególnych wystąpień maszyn wirtualnych lub zaktualizować już uruchomioną aplikację. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Automatyczne instalowanie aplikacji w zestawie skalowania
> * Używanie rozszerzenia niestandardowego skryptu platformy Azure
> * Aktualizowanie uruchomionej aplikacji w zestawie skalowania

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek będzie wymagał modułu programu Azure PowerShell w wersji 6.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. 


## <a name="what-is-the-azure-custom-script-extension"></a>Co to jest rozszerzenie niestandardowego skryptu platformy Azure?
Rozszerzenie niestandardowego skryptu pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalowania oprogramowania lub każdego innego zadania związanego z konfiguracją lub zarządzaniem. Skrypty można pobrać z usługi Azure Storage lub GitHub bądź można je dostarczyć do witryny Azure Portal w czasie wykonywania rozszerzenia.

Rozszerzenie niestandardowego skryptu można zintegrować z szablonami usługi Azure Resource Manager, a także je uruchamiać przy użyciu interfejsu wiersza polecenia platformy Azure 2.0, programu Azure PowerShell, witryny Azure Portal lub interfejsu API REST. Aby uzyskać więcej informacji, zobacz [Omówienie niestandardowego rozszerzenia skryptu](../virtual-machines/windows/extensions-customscript.md).

Aby zobaczyć, jak działa rozszerzenie niestandardowego skryptu, utworzymy zestaw skalowania, który instaluje serwer internetowy IIS i zwraca nazwę hosta wystąpienia maszyny wirtualnej w zestawie skalowania. Definicja rozszerzenia niestandardowego skryptu pobiera przykładowy skrypt z witryny GitHub, instaluje wymagane pakiety, a następnie zapisuje nazwę hosta wystąpienia maszyny wirtualnej na prostej stronie HTML.


## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania
Teraz utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Musisz również utworzyć moduł równoważenia obciążenia, który umożliwia kierowanie ruchu do poszczególnych wystąpień maszyn wirtualnych. Moduł równoważenia obciążenia zawiera reguły, które pozwalają kierować ruchem na porcie TCP 80 oraz korzystać z ruchu pulpitu zdalnego na porcie TCP 3389 i komunikacji zdalnej programu PowerShell na porcie TCP 5985. Po wyświetleniu monitu podaj własne odpowiednie poświadczenia administracyjne dla wystąpień maszyn wirtualnych w zestawie skalowania:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.


## <a name="create-custom-script-extension-definition"></a>Tworzenie definicji rozszerzenia niestandardowego skryptu
Program Azure PowerShell używa tablicy skrótów do przechowywania pliku do pobrania i polecenia do wykonania. W poniższym przykładzie jest używany przykładowy skrypt z usługi GitHub. Najpierw utwórz obiekt konfiguracji w następujący sposób:

```azurepowershell-interactive
$customConfig = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}
```

Teraz zastosuj rozszerzenie niestandardowego skryptu za pomocą polecenia [Add-AzureRmVmssExtension](/powershell/module/AzureRM.Compute/Add-AzureRmVmssExtension). Rozszerzenie przyjmuje wcześniej zdefiniowany obiekt konfiguracji. Zaktualizuj i uruchom rozszerzenie na wystąpieniach maszyn wirtualnych za pomocą polecenia [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss).

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzureRmVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
  -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.9 `
  -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Każde wystąpienie maszyny wirtualnej w zestawie skalowania pobiera i uruchamia skrypt z usługi GitHub. Bardziej złożony przykład może obejmować instalację wielu składników aplikacji i plików. W przypadku skalowania w górę nowe wystąpienia maszyn wirtualnych automatycznie stosują tę samą definicję rozszerzenia niestandardowego skryptu i instalują wymaganą aplikację.


## <a name="test-your-scale-set"></a>Testowanie zestawu skalowania
Aby zobaczyć, jak działa serwer internetowy, uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). W poniższym przykładzie pokazano uzyskiwanie adres IP utworzonego w grupie zasobów *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Wprowadź publiczny adres IP modułu równoważenia obciążenia w przeglądarce internetowej. Moduł równoważenia obciążenia kieruje ruch do jednego z wystąpień maszyn wirtualnych, jak pokazano w poniższym przykładzie:

![Podstawowa strona internetowa na serwerze IIS](media/tutorial-install-apps-powershell/running-iis.png)

Nie zamykaj przeglądarki internetowej, aby w następnym kroku można było zobaczyć zaktualizowaną wersję.


## <a name="update-app-deployment"></a>Aktualizowanie wdrożenia aplikacji
W całym cyklu życia zestawu skalowania konieczne może być wdrożenie zaktualizowanej wersji aplikacji. Rozszerzenie niestandardowego skryptu umożliwia odwołanie się do zaktualizowanego skryptu wdrażania i ponowne zastosowanie rozszerzenia do zestawu skalowania. Podczas tworzenia zestawu skalowania w poprzednim kroku parametr `-UpgradePolicyMode` ustawiono na wartość *Automatic*. To ustawienie pozwala wystąpieniom maszyn wirtualnych w zestawie skalowania automatycznie aktualizować aplikację i stosować jej najnowszą wersję.

Utwórz nową definicję konfiguracji o nazwie *customConfigv2*. Ta definicja umożliwia uruchomienie zaktualizowanej wersji *v2* skryptu instalacji aplikacji:

```azurepowershell-interactive
$customConfigv2 = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis-v2.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis-v2.ps1"
}
```

Za pomocą polecenia [Add-AzureRmVmssExtension](/powershell/module/AzureRM.Compute/Add-AzureRmVmssExtension) ponownie zastosuj konfigurację rozszerzenia niestandardowego skryptu do wystąpień maszyn wirtualnych w zestawie skalowania. Definicja *customConfigv2* umożliwia zastosowanie zaktualizowanej wersji aplikacji:

```azurepowershell-interactive
# Reapply the Custom Script Extension to install the updated website
$vmss = Add-AzureRmVmssExtension `
  -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.9 `
  -Setting $customConfigv2

# Update the scale set and reapply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Wszystkie wystąpienia maszyn wirtualnych w zestawie skalowania są automatycznie aktualizowane do najnowszej wersji przykładowej strony internetowej. Aby wyświetlić zaktualizowaną wersję, odśwież witrynę internetową w przeglądarce:

![Zaktualizowana strona internetowa na serwerze IIS](media/tutorial-install-apps-powershell/running-iis-updated.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Aby pozbyć się zestawu skalowania i dodatkowych zasobów, usuń grupę zasobów wraz z całą zawartością za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Parametr `-Force` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu. Parametr `-AsJob` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono automatyczne instalowanie i aktualizowanie aplikacji w zestawie skalowania za pomocą programu Azure PowerShell:

> [!div class="checklist"]
> * Automatyczne instalowanie aplikacji w zestawie skalowania
> * Używanie rozszerzenia niestandardowego skryptu platformy Azure
> * Aktualizowanie uruchomionej aplikacji w zestawie skalowania

Przejdź do następnego samouczka, aby dowiedzieć się, jak automatycznie skalować zestaw skalowania.

> [!div class="nextstepaction"]
> [Automatically scale your scale sets (Automatyczne skalowanie zestawów skalowania)](tutorial-autoscale-powershell.md)
