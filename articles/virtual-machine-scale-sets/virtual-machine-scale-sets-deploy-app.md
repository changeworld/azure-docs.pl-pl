---
title: Wdrażanie aplikacji do zestawu skalowania maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrażać aplikacji dla systemu Linux i Windows wystąpień maszyn wirtualnych w zestawie skalowania
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: iainfou
ms.openlocfilehash: 4f57924bf2197c472d408edc67baf35b18ae45c2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652930"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Wdrażanie aplikacji na zestawy skalowania maszyny wirtualnej
Aby uruchamiać aplikacje na wystąpieniach maszyn wirtualnych w zestawie skalowania, musisz najpierw zainstalować składniki aplikacji i wymagane pliki. W tym artykule przedstawiono sposoby budowania niestandardowego obrazu maszyny Wirtualnej dla wystąpień w skali ustawić lub automatycznego uruchamiania skryptów instalacji na istniejących wystąpień maszyny Wirtualnej. Możesz również sposób zarządzania aplikacji lub aktualizacji systemu operacyjnego przez zestaw skali.


## <a name="build-a-custom-vm-image"></a>Tworzenie niestandardowego obrazu maszyny Wirtualnej
Korzystając z jednego z obrazów platformy Azure można utworzyć wystąpienia w zestawie skali, żadne dodatkowe oprogramowanie jest zainstalowane lub skonfigurowane. Można zautomatyzować instalację tych składników, jednak dodający się czas wymagany do obsługi administracyjnej wystąpień maszyn wirtualnych do sieci zestawy skalowania. Wiele zmian konfiguracji w przypadku zastosowania do wystąpień maszyn wirtualnych, brak zarządzania nakłady pracy związane z tymi skrypty do konfiguracji i zadań.

Aby zmniejszyć Zarządzanie konfiguracją i czas do udostępnienia maszyny Wirtualnej, można utworzyć niestandardowy obraz maszyny Wirtualnej, który jest gotowy do uruchomienia aplikacji natychmiast po udostępnieniu wystąpienia w zestawie skalowania. Aby uzyskać więcej informacji na temat sposobu tworzenia i używania niestandardowego obrazu maszyny Wirtualnej o skali, zobacz następujące samouczki:

- [Interfejs wiersza polecenia platformy Azure 2.0](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="already-provisioned"></a>Instalowanie aplikacji z rozszerzeniem niestandardowego skryptu
Rozszerzenie niestandardowego skryptu pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalowania oprogramowania lub każdego innego zadania związanego z konfiguracją lub zarządzaniem. Skrypty można pobrać z usługi Azure Storage lub GitHub bądź można je dostarczyć do witryny Azure Portal w czasie wykonywania rozszerzenia. Aby uzyskać więcej informacji na temat sposobu tworzenia i używania niestandardowego obrazu maszyny Wirtualnej o skali, zobacz następujące samouczki:

- [Interfejs wiersza polecenia platformy Azure 2.0](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Szablon usługi Azure Resource Manager](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Instalowanie aplikacji do systemu Windows maszyny Wirtualnej przy użyciu programu PowerShell DSC
[Konfiguracji żądanego stanu środowiska PowerShell (DSC)](https://msdn.microsoft.com/powershell/dsc/overview) to platforma zarządzania, aby zdefiniować konfigurację komputerów docelowych. Konfiguracji DSC zdefiniować, co należy zainstalować na maszynie oraz sposób konfigurowania hosta. Aparat lokalnego Configuration Manager (LCM) działa na każdym węźle docelowym, który przetwarza żądane akcje wciśnięcia konfiguracji.

Rozszerzenia DSC środowiska PowerShell umożliwia dostosowanie wystąpień maszyn wirtualnych w skali ustawić przy użyciu programu PowerShell. Poniższy przykład:

- Powoduje, że wystąpień maszyny Wirtualnej, aby pobrać pakiet DSC z serwisu GitHub- *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Ustawia na uruchomienie skryptu install - rozszerzenia `configure-http.ps1`
- Pobiera informacje o skali ustawiony za pomocą [Get AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- Dotyczy rozszerzenia wystąpień maszyn wirtualnych z [AzureRmVmss aktualizacji](/powershell/module/azurerm.compute/update-azurermvmss)

Rozszerzenia usługi Konfiguracja DSC jest stosowany do *myScaleSet* wystąpień maszyn wirtualnych w grupie zasobów o nazwie *myResourceGroup*. Wprowadź własne nazwy w następujący sposób:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

W przypadku zasad uaktualniania na zestawie skali *ręczne*, zaktualizować wystąpień maszyn wirtualnych z [AzureRmVmssInstance aktualizacji](/powershell/module/azurerm.compute/update-azurermvmssinstance). To polecenie cmdlet ma zastosowanie do konfiguracji zestaw skalowania zaktualizowane do wystąpień maszyn wirtualnych i instaluje aplikację.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Instalowanie aplikacji do maszyny Wirtualnej systemu Linux z inicjowaniem chmury
[Cloud-init](https://cloudinit.readthedocs.io/latest/) to powszechnie używana metoda dostosowywania maszyny wirtualnej z systemem Linux podczas jej pierwszego rozruchu. Za pomocą pakietu cloud-init można instalować pakiety i zapisywać pliki lub konfigurować użytkowników i zabezpieczenia. Pakiet cloud-init jest uruchamiany w trakcie początkowego rozruchu, więc do zastosowania konfiguracji nie są wymagane żadne dodatkowe kroki ani agenci.

Pakiet cloud-init działa również w różnych dystrybucjach. Przykładowo nie używa się poleceń **apt-get install** lub **yum install** do zainstalowania pakietu. Zamiast tego możesz zdefiniować listę pakietów do zainstalowania. Pakiet cloud-init automatycznie używa natywnego narzędzia do zarządzania pakietami dla wybranej dystrybucji.

Aby uzyskać więcej informacji, w tym przykładzie *init.txt chmury* plików, zobacz [umożliwia dostosowywanie maszyn wirtualnych platformy Azure w chmurze init](../virtual-machines/linux/using-cloud-init.md).

Aby utworzyć zestaw skali i przy użyciu pliku init chmury, Dodaj `--custom-data` parametr [az vmss utworzyć](/cli/azure/vmss#az_vmss_create) polecenia i określ nazwę pliku init chmury. Poniższy przykład tworzy zestaw o nazwie skalowania *myScaleSet* w *myResourceGroup* i konfiguruje wystąpień maszyn wirtualnych przy użyciu pliku o nazwie *init.txt chmury*. Wprowadź własne nazwy w następujący sposób:

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>Instalowanie aplikacji z aktualizacje systemu operacyjnego
Gdy dostępne są nowe wersje systemu operacyjnego, możesz użyć lub utworzyć obraz niestandardowy i [wdrażanie uaktualnień systemu operacyjnego](virtual-machine-scale-sets-upgrade-scale-set.md) skali ustawić. Każde wystąpienie maszyny Wirtualnej zostanie uaktualniony do najnowszej obrazu, który określisz. Niestandardowy obraz z aplikacją preinstalowanym, niestandardowe rozszerzenie skryptu lub DSC środowiska PowerShell umożliwia ma automatycznie dostępny aplikacji, jak przeprowadzić uaktualnienie. Może być konieczne planowanie konserwacji aplikacji wykonanie tego procesu, aby upewnić się, że nie ma żadnych wersji problemy ze zgodnością.

Jeśli używasz niestandardowego obrazu maszyny Wirtualnej z wcześniej zainstalowaną aplikację może integrować aktualizacje aplikacji z potokiem wdrożenia do tworzenia nowych obrazów i wdrażania uaktualnień systemu operacyjnego na zestawie skali. Takie podejście umożliwia potoku do odebrania najnowszej kompilacji aplikacji, Utwórz i Zweryfikuj obrazu maszyny Wirtualnej, a następnie uaktualnić wystąpień maszyny Wirtualnej w zestawie skalowania. Aby uruchomić potok wdrożenia, który tworzy i wdraża aktualizacje aplikacji w niestandardowych obrazów maszyn wirtualnych, można [utworzyć obraz pakujący i wdrożyć przy użyciu programu Visual Studio Team Services) [/ vsts/potoki/aplikacje/cd/azure/wdrożyć azure-scaleset], lub użyj innej platformy takie jak [Spinnaker](https://www.spinnaker.io/) lub [Wpięć](https://jenkins.io/).


## <a name="next-steps"></a>Kolejne kroki
Jak skompilować i wdrożyć aplikacje na Twoje zestawy skalowania, możesz przejrzeć [Omówienie projektowania zestaw skali](virtual-machine-scale-sets-design-overview.md). Aby uzyskać więcej informacji na temat zarządzania zestaw skalowania, zobacz [Użyj programu PowerShell do zarządzania zestawie skali](virtual-machine-scale-sets-windows-manage.md).
