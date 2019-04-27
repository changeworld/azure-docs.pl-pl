---
title: Wdrażanie aplikacji w zestawie skalowania maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrażać aplikacji dla systemów Linux i Windows wystąpień maszyn wirtualnych w zestawie skalowania
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 09145612821cb669e26e3ccb8d15611112eca700
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618629"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Wdrażanie aplikacji na zestawach skalowania maszyn wirtualnych

Aby uruchamiać aplikacje na wystąpieniach maszyn wirtualnych w zestawie skalowania, musisz najpierw zainstalować składniki aplikacji i wymagane pliki. W tym artykule przedstawiono sposoby tworzenia niestandardowego obrazu maszyny Wirtualnej dla wystąpień w skalowania zestawu lub skryptów instalacji automatycznie są uruchamiane w istniejących wystąpieniach maszyn wirtualnych. Poznasz również sposób zarządzania aplikacji lub aktualizacji systemu operacyjnego w zestawie skalowania.


## <a name="build-a-custom-vm-image"></a>Tworzenie niestandardowego obrazu maszyny Wirtualnej
Korzystając z jednego z obrazów platformy Azure do tworzenia wystąpień w zestawie skalowania, żadne dodatkowe oprogramowanie jest zainstalowane lub skonfigurowane. Można zautomatyzować instalację tych składników, jednak dodającego do czasu potrzebnego do aprowizacji wystąpienia maszyny Wirtualnej, aby zestawami skalowania. Jeśli zastosujesz wiele zmian konfiguracji do wystąpień maszyn wirtualnych, brak zarządzania obciążenie za pomocą tych konfiguracji skryptów i zadań.

Aby ograniczyć Zarządzanie konfiguracją i raz, aby aprowizować maszynę Wirtualną, możesz utworzyć niestandardowego obrazu maszyny Wirtualnej, który jest gotowy do uruchomienia zaraz zostanie zainicjowane wystąpienie aplikacji w zestawie skalowania. Aby uzyskać więcej informacji na temat tworzenia i używania niestandardowego obrazu maszyny Wirtualnej o skali zestawu, zobacz następujące samouczki:

- [Interfejs wiersza polecenia platformy Azure](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="already-provisioned"></a>Instalowanie aplikacji przy użyciu rozszerzenia niestandardowego skryptu
Rozszerzenie niestandardowego skryptu pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalowania oprogramowania lub każdego innego zadania związanego z konfiguracją lub zarządzaniem. Skrypty można pobrać z usługi Azure Storage lub GitHub bądź można je dostarczyć do witryny Azure Portal w czasie wykonywania rozszerzenia. Aby uzyskać więcej informacji na temat tworzenia i używania niestandardowego obrazu maszyny Wirtualnej o skali zestawu, zobacz następujące samouczki:

- [Interfejs wiersza polecenia platformy Azure](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Szablon usługi Azure Resource Manager](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Instalowanie aplikacji do Windows maszyny Wirtualnej przy użyciu programu PowerShell DSC
[Program PowerShell Desired State Configuration (DSC)](https://msdn.microsoft.com/powershell/dsc/overview) to platforma zarządzania, aby zdefiniować konfigurację komputerów docelowych. Konfiguracje DSC definiują, co należy zainstalować na komputerze oraz sposób konfigurowania hosta. Aparat lokalnego Configuration Manager (LCM) działa w każdym węźle docelowym, która przetwarza żądane akcje na podstawie konfiguracji wypychanie.

Rozszerzenie DSC programu PowerShell umożliwia dostosowanie wystąpień maszyn wirtualnych w zestawie przy użyciu programu PowerShell skalowania. Poniższy przykład:

- Powoduje, że wystąpień maszyn wirtualnych, aby pobrać pakiet DSC z usługi GitHub — *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Ustawia rozszerzenie do uruchomienia skryptu install- `configure-http.ps1`
- Pobiera informacje o zestawu skalowania z [Get AzVmss](/powershell/module/az.compute/get-azvmss)
- Stosuje rozszerzenie do wystąpień maszyn wirtualnych za pomocą [AzVmss aktualizacji](/powershell/module/az.compute/update-azvmss)

Rozszerzenie DSC jest stosowany do *myScaleSet* wystąpień maszyn wirtualnych w grupie zasobów o nazwie *myResourceGroup*. Wprowadź własne nazwy w następujący sposób:

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
$vmss = Get-AzVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Jeśli zasady uaktualniania na zestaw skalowania jest *ręczne*, zaktualizować wystąpień maszyn wirtualnych za pomocą [AzVmssInstance aktualizacji](/powershell/module/az.compute/update-azvmssinstance). To polecenie cmdlet ma zastosowanie do konfiguracji zestawu skalowania zaktualizowane do wystąpień maszyn wirtualnych i instaluje aplikację.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Instalowanie aplikacji na Maszynę wirtualną systemu Linux przy użyciu pakietu cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) to powszechnie używana metoda dostosowywania maszyny wirtualnej z systemem Linux podczas jej pierwszego rozruchu. Za pomocą pakietu cloud-init można instalować pakiety i zapisywać pliki lub konfigurować użytkowników i zabezpieczenia. Pakiet cloud-init jest uruchamiany w trakcie początkowego rozruchu, więc do zastosowania konfiguracji nie są wymagane żadne dodatkowe kroki ani agenci.

Pakiet cloud-init działa również w różnych dystrybucjach. Przykładowo nie używa się poleceń **apt-get install** lub **yum install** do zainstalowania pakietu. Zamiast tego możesz zdefiniować listę pakietów do zainstalowania. Pakiet cloud-init automatycznie używa natywnego narzędzia do zarządzania pakietami dla wybranej dystrybucji.

Aby uzyskać więcej informacji, w tym przykładzie *cloud-init.txt* plików, zobacz [używać pakietu cloud-init do dostosowywania maszyn wirtualnych platformy Azure](../virtual-machines/linux/using-cloud-init.md).

Aby utworzyć zestaw skalowania i przy użyciu pliku cloud-init, Dodaj `--custom-data` parametr [tworzenie az vmss](/cli/azure/vmss) polecenia i podaj nazwę pliku cloud-init. Poniższy przykład tworzy zestaw skalowania o nazwie *myScaleSet* w *myResourceGroup* i konfiguruje wystąpień maszyn wirtualnych przy użyciu pliku o nazwie *cloud-init.txt*. Wprowadź własne nazwy w następujący sposób:

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


### <a name="install-applications-with-os-updates"></a>Instalowanie aplikacji za pomocą aktualizacji systemu operacyjnego
Po udostępnieniu nowej wersji systemu operacyjnego można użyć lub utworzyć obraz niestandardowy i [wdrażania uaktualnień systemu operacyjnego](virtual-machine-scale-sets-upgrade-scale-set.md) skalowania zestawu. Każde wystąpienie maszyny Wirtualnej została uaktualniona do najnowszego obrazu, który określisz. Za pomocą niestandardowego obrazu i aplikacji, wstępnie zainstalowane rozszerzenie niestandardowego skryptu lub programu PowerShell DSC aby aplikacja automatycznie dostępny podczas wykonywania uaktualnienia. Może być konieczne planowanie konserwacji aplikacji podczas wykonywania tego procesu, aby upewnić się, że nie istnieją żadne wersji problemy ze zgodnością.

Jeśli używasz niestandardowego obrazu maszyny Wirtualnej z aplikacją wstępnie zainstalowane aktualizacje aplikacji może być zintegrowany z potoku wdrożenia w celu tworzenia nowych obrazów i wdrażania uaktualnień systemu operacyjnego w zestawie skalowania. Takie podejście umożliwia potoku, aby wczytać najnowsze kompilacje aplikacji, Utwórz i Zweryfikuj obrazu maszyny Wirtualnej, a następnie uaktualnić wystąpienia maszyny Wirtualnej w zestawie skalowania. Aby uruchomić potok wdrażania, który kompiluje i wdraża aktualizacje aplikacji w niestandardowych obrazów maszyn wirtualnych, można wykonać następujące akcje [Tworzenie obrazu usługi Packer i wdrażanie dzięki usługom DevOps platformy Azure](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset), lub użyj innej platformy, takie jak [Spinnaker](https://www.spinnaker.io/) lub [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Kolejne kroki
Podczas tworzenia i wdrażania aplikacji na Twoje zestawy skalowania można przejrzeć [Omówienie projektowania zestawu skalowania](virtual-machine-scale-sets-design-overview.md). Aby uzyskać więcej informacji na temat zarządzania zestawu skalowania, zobacz [Użyj programu PowerShell do zarządzania zestaw skalowania](virtual-machine-scale-sets-windows-manage.md).
