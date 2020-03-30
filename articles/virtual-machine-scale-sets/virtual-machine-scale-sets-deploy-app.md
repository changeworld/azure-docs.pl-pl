---
title: Wdrażanie aplikacji w zestawie skalowania maszyny wirtualnej platformy Azure
description: Dowiedz się, jak wdrażać aplikacje w wystąpieniach maszyn wirtualnych systemu Linux i Windows w zestawie skalowania
author: cynthn
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 6bc319ea50da4ff6a654b2c9ab09bbe218695533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278099"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Wdrażanie aplikacji na zestawach skalowania maszyn wirtualnych

Aby uruchamiać aplikacje na wystąpieniach maszyn wirtualnych w zestawie skalowania, musisz najpierw zainstalować składniki aplikacji i wymagane pliki. W tym artykule przedstawiono sposoby tworzenia niestandardowego obrazu maszyny Wirtualnej dla wystąpień w zestawie skalowania lub automatycznego uruchamiania skryptów instalacji w istniejących wystąpieniach maszyn wirtualnych. Dowiesz się również, jak zarządzać aktualizacjami aplikacji lub systemu operacyjnego w zestawie skalowania.


## <a name="build-a-custom-vm-image"></a>Tworzenie niestandardowego obrazu maszyny Wirtualnej
Korzystając z jednego z obrazów platformy Azure do tworzenia wystąpień w zestawie skalowania, żadne dodatkowe oprogramowanie nie jest instalowane ani konfigurowane. Można zautomatyzować instalację tych składników, jednak, który dodaje do czasu potrzebny do aprowizowania wystąpień maszyn wirtualnych do zestawów skalowania. Jeśli zastosujesz wiele zmian konfiguracji do wystąpień maszyn wirtualnych, istnieje obciążenie związane z zarządzaniem tymi skryptami konfiguracji i zadaniami.

Aby skrócić zarządzanie konfiguracją i czas aprowizowania maszyny Wirtualnej, można utworzyć niestandardowy obraz maszyny Wirtualnej, który jest gotowy do uruchomienia aplikacji, gdy tylko wystąpienie zostanie aprowizowana w zestawie skalowania. Aby uzyskać więcej informacji na temat tworzenia i używania niestandardowego obrazu maszyny Wirtualnej z zestawem skalowania, zobacz następujące samouczki:

- [Interfejs wiersza polecenia platformy Azure](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="install-an-app-with-the-custom-script-extension"></a><a name="already-provisioned"></a>Instalowanie aplikacji z rozszerzeniem skryptu niestandardowego
Rozszerzenie niestandardowego skryptu pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalowania oprogramowania lub każdego innego zadania związanego z konfiguracją lub zarządzaniem. Skrypty można pobrać z usługi Azure Storage lub GitHub bądź można je dostarczyć do witryny Azure Portal w czasie wykonywania rozszerzenia. Aby uzyskać więcej informacji na temat instalowania aplikacji z rozszerzeniem skryptu niestandardowego, zobacz następujące samouczki:

- [Interfejs wiersza polecenia platformy Azure](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Szablon usługi Azure Resource Manager](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Instalowanie aplikacji na maszynie wirtualnej systemu Windows za pomocą usługi PowerShell DSC
[PowerShell żądana konfiguracja stanu (DSC)](/powershell/scripting/dsc/overview/overview) jest platformą zarządzania do definiowania konfiguracji maszyn docelowych. Konfiguracje DSC określają, co należy zainstalować na komputerze i jak skonfigurować hosta. Aparat lokalnego programu Menedżer konfiguracji (LCM) działa na każdym węźle docelowym, który przetwarza żądane akcje na podstawie wypychanych konfiguracji.

Rozszerzenie DSC programu PowerShell umożliwia dostosowanie wystąpień maszyn wirtualnych w skali ustawionej za pomocą programu PowerShell. Poniższy przykład:

- Nakazuje wystąpieniom maszyn wirtualnych pobranie pakietu DSC z usługi GitHub -*https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Ustawia rozszerzenie do uruchomienia skryptu instalacji -`configure-http.ps1`
- Pobiera informacje o zestawie skalowania za pomocą [get-AzVmss](/powershell/module/az.compute/get-azvmss)
- Stosuje rozszerzenie do wystąpień maszyny Wirtualnej z [Update-AzVmss](/powershell/module/az.compute/update-azvmss)

Rozszerzenie DSC jest stosowane do wystąpień maszyn wirtualnych *myScaleSet* w grupie zasobów o nazwie *myResourceGroup*. Wprowadź swoje własne nazwy w następujący sposób:

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

Jeśli zasady uaktualniania w zestawie skalowania są *ręczne,* zaktualizuj wystąpienia maszyn wirtualnych za pomocą [funkcji Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance). To polecenie cmdlet stosuje zaktualizowaną konfigurację zestawu skalowania do wystąpień maszyn wirtualnych i instaluje aplikację.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Instalowanie aplikacji na maszynie wirtualnej z systemem Linux za pomocą cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) to powszechnie używana metoda dostosowywania maszyny wirtualnej z systemem Linux podczas jej pierwszego rozruchu. Za pomocą pakietu cloud-init można instalować pakiety i zapisywać pliki lub konfigurować użytkowników i zabezpieczenia. Pakiet cloud-init jest uruchamiany w trakcie początkowego rozruchu, więc do zastosowania konfiguracji nie są wymagane żadne dodatkowe kroki ani agenci.

Pakiet cloud-init działa również w różnych dystrybucjach. Przykładowo nie używa się poleceń **apt-get install** lub **yum install** do zainstalowania pakietu. Zamiast tego możesz zdefiniować listę pakietów do zainstalowania. Pakiet cloud-init automatycznie używa natywnego narzędzia do zarządzania pakietami dla wybranej dystrybucji.

Aby uzyskać więcej informacji, w tym przykładowy plik *cloud-init.txt,* zobacz [Dostosowywanie maszyn wirtualnych platformy Azure za pomocą init w chmurze.](../virtual-machines/linux/using-cloud-init.md)

Aby utworzyć zestaw skalowania i użyć pliku init w chmurze, dodaj `--custom-data` ten parametr do polecenia [az vmss create](/cli/azure/vmss) i określ nazwę pliku init w chmurze. Poniższy przykład tworzy zestaw skalowania o nazwie *myScaleSet* w *myResourceGroup* i konfiguruje wystąpienia maszyn wirtualnych za pomocą pliku o nazwie *cloud-init.txt*. Wprowadź swoje własne nazwy w następujący sposób:

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


### <a name="install-applications-with-os-updates"></a>Instalowanie aplikacji z aktualizacjami systemu operacyjnego
Gdy dostępne są nowe wersje systemu operacyjnego, można użyć lub utworzyć nowy obraz niestandardowy i [wdrożyć uaktualnienia systemu operacyjnego](virtual-machine-scale-sets-upgrade-scale-set.md) do zestawu skalowania. Każde wystąpienie maszyny Wirtualnej jest uaktualniane do najnowszego obrazu, który określisz. Można użyć obrazu niestandardowego z preinstalowana aplikacja, Rozszerzenie skryptów niestandardowych lub PowerShell DSC, aby aplikacja była automatycznie dostępna podczas wykonywania uaktualnienia. Może być konieczne zaplanowanie konserwacji aplikacji podczas wykonywania tego procesu, aby upewnić się, że nie ma problemów ze zgodnością wersji.

Jeśli używasz niestandardowego obrazu maszyny Wirtualnej z wstępnie zainstalowaną aplikacją, można zintegrować aktualizacje aplikacji z potokiem wdrażania, aby utworzyć nowe obrazy i wdrożyć uaktualnienia systemu operacyjnego w zestawie skalowania. Takie podejście umożliwia potoku, aby odebrać najnowsze kompilacje aplikacji, utworzyć i sprawdzić poprawność obrazu maszyny Wirtualnej, a następnie uaktualnić wystąpienia maszyny Wirtualnej w zestawie skalowania. Aby uruchomić potok wdrażania, który tworzy i wdraża aktualizacje aplikacji w niestandardowych obrazach maszyn wirtualnych, można [utworzyć obraz programu Packer i wdrożyć za pomocą usług Azure DevOps](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)lub użyć innej platformy, takiej jak [Spinnaker](https://www.spinnaker.io/) lub [Jenkins.](https://jenkins.io/)


## <a name="next-steps"></a>Następne kroki
Podczas tworzenia i wdrażania aplikacji w zestawach skalowania można przejrzeć [omówienie projektu zestawu skalowania](virtual-machine-scale-sets-design-overview.md). Aby uzyskać więcej informacji na temat zarządzania zestawem skalowania, zobacz [Zarządzanie zestawem skalowania za pomocą programu PowerShell](virtual-machine-scale-sets-windows-manage.md).
