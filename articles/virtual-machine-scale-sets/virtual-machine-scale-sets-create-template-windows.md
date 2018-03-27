---
title: "Tworzenie zestawu skalowania maszyn wirtualnych z systemem Windows przy użyciu szablonu platformy Azure | Microsoft Docs"
description: "Dowiedz się, jak szybko utworzyć skalę maszyny wirtualnej z systemem Windows za pomocą szablonu usługi Azure Resource Manager, który wdraża przykładową aplikację i konfiguruje reguły automatycznego skalowania"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 1632411b0cfc2f8fa59f323436ee386e763a1ae0
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-windows-virtual-machine-scale-set-with-an-azure-template"></a>Tworzenie zestawu skalowania maszyn wirtualnych z systemem Windows przy użyciu szablonu platformy Azure
Zestaw skalowania maszyn wirtualnych umożliwia wdrożenie zestawu identycznych, automatycznie skalowanych maszyn wirtualnych, oraz zarządzanie nimi. Maszyny wirtualne w zestawie skalowania można skalować ręcznie lub można zdefiniować reguły skalowania automatycznego na podstawie użycia zasobów, takich jak procesor CPU, zapotrzebowanie na pamięć lub ruch sieciowy. W tym artykule wprowadzającym opisano tworzenie zestawu skalowania maszyn wirtualnych za pomocą szablonu usługi Azure Resource Manager. Zestaw skalowania można również utworzyć przy użyciu [interfejsu wiersza polecenia platformy Azure w wersji 2.0](virtual-machine-scale-sets-create-cli.md), [programu Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) lub [witryny Azure Portal](virtual-machine-scale-sets-create-portal.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 4.4.1 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.


## <a name="define-a-scale-set-in-a-template"></a>Definiowanie zestawu skalowania w szablonie
Szablony usługi Azure Resource Manager umożliwiają wdrażanie grup powiązanych zasobów. Szablony są pisane w formacie JavaScript Object Notation (JSON) i określają środowisko całej infrastruktury platformy Azure dla aplikacji. W jednym szablonie można utworzyć zestaw skalowania maszyn wirtualnych, zainstalować aplikacje i skonfigurować reguły automatycznego skalowania. Korzystając ze zmiennych i parametrów, można ponownie użyć tego szablonu, aby zaktualizować istniejące zestawy skalowania lub utworzyć dodatkowe. Szablony można wdrażać za pośrednictwem witryny Azure Portal, interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub programu Azure PowerShell bądź z poziomu potoków ciągłej integracji / ciągłego dostarczania (CI/CD).

Więcej informacji dotyczących szablonów można znaleźć w temacie [Omówienie usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment).

Szablon definiuje konfigurację dla każdego typu zasobu. Typ zasobu zestawu skalowania maszyn wirtualnych jest podobny do typu zasobu pojedynczej maszyny wirtualnej. Podstawowe elementy typu zasobu zestawu skalowania maszyn wirtualnych są następujące:

| Właściwość                     | Opis właściwości                                  | Przykładowa wartość szablonu                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | Typ zasobu platformy Azure do utworzenia                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | Nazwa zestawu skalowania                                       | myScaleSet                                |
| location                     | Lokalizacja utworzenia zestawu skalowania                     | Wschodnie stany USA                                   |
| sku.name                     | Rozmiar maszyny wirtualnej dla każdego wystąpienia zestawu skalowania                  | Standardowa_A1                               |
| sku.capacity                 | Liczba wystąpień maszyn wirtualnych do początkowego utworzenia           | 2                                         |
| upgradePolicy.mode           | Tryb uaktualniania wystąpienia maszyny wirtualnej w przypadku wprowadzenia zmian              | Automatyczny                                 |
| imageReference               | Platforma lub obraz niestandardowy do użycia na potrzeby wystąpień maszyn wirtualnych | Microsoft Windows Server 2016 Datacenter  |
| osProfile.computerNamePrefix | Prefiks nazwy dla każdego wystąpienia maszyny wirtualnej                     | myvmss                                    |
| osProfile.adminUsername      | Nazwa użytkownika dla każdego wystąpienia maszyny wirtualnej                        | użytkownik_azure                                 |
| osProfile.adminPassword      | Hasło dla każdego wystąpienia maszyny wirtualnej                        | P@ssw0rd!                                 |

 W poniższym przykładzie przedstawiono definicję podstawowych zasobów zestawu skalowania. Aby dostosować szablon zestawu skalowania, można zmienić rozmiar maszyny wirtualnej lub pojemność początkową bądź użyć innej platformy albo obrazu niestandardowego.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

 W celu skrócenia przykładu nie pokazano w nim konfiguracji wirtualnej karty sieciowej. Dodatkowe składniki, takie jak moduł równoważenia obciążenia, także nie są widoczne. Pełny szablon zestawu skalowania znajduje się [na końcu tego artykułu](#deploy-the-template).


## <a name="install-an-application"></a>Instalowanie aplikacji
Gdy wdrażasz zestaw skalowania, rozszerzenia maszyn wirtualnych mogą dostarczać konfigurację po wdrożeniu oraz zadania automatyzacji, takie jak instalowanie aplikacji. Skrypty można pobrać z usługi Azure Storage lub GitHub bądź można je dostarczyć do witryny Azure Portal w czasie wykonywania rozszerzenia. Aby zastosować rozszerzenie do zestawu skalowania, dodaj sekcję *extensionProfile* do przedstawionego wcześniej przykładu zasobów. Profil rozszerzenia zwykle definiuje następujące właściwości:

- Typ rozszerzenia
- Wydawca rozszerzenia
- Wersja rozszerzenia
- Lokalizacja konfiguracji lub skryptów instalacji
- Polecenia do wykonania na wystąpieniach maszyn wirtualnych

Przykładowy szablon [aplikacji ASP.NET w systemie Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) używa rozszerzenia DSC programu PowerShell w celu zainstalowania aplikacji ASP.NET MVC, która działa w usługach IIS. 

Skrypt instalacji jest pobierany z usługi GitHub, jak zdefiniowano we właściwości *url*. Następnie rozszerzenie uruchamia polecenie *InstallIIS* ze skryptu *IISInstall.ps1*, jak zdefiniowano we właściwościach *function* i *Script*. Sama aplikacja ASP.NET jest dostarczana jako pakiet Web Deploy, który także jest pobierany z usługi GitHub, jak zdefiniowano we właściwości *WebDeployPackagePath*:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "Microsoft.Powershell.DSC",
      "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.9",
        "autoUpgradeMinorVersion": true,
        "forceUpdateTag": "1.0",
        "settings": {
          "configuration": {
            "url": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/DSC/IISInstall.ps1.zip",
            "script": "IISInstall.ps1",
            "function": "InstallIIS"
          },
          "configurationArguments": {
            "nodeName": "localhost",
            "WebDeployPackagePath": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/WebDeploy/DefaultASPWebApp.v1.0.zip"
          }
        }
      }
    }
  ]
}
```

## <a name="deploy-the-template"></a>Wdrożenie szablonu
Szablon [aplikacji ASP.NET MVC w systemie Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) można wdrożyć za pomocą następującego przycisku **Wdróż na platformie Azure**. Ten przycisk otwiera witrynę Azure Portal, ładuje pełny szablon i wyświetla monit o podanie kilku parametrów, takich jak nazwa zestawu skalowania, liczba wystąpień i poświadczenia administratora.

[![Wdrażanie szablonu na platformie Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

Aby zainstalować aplikację ASP.NET w systemie Windows za pomocą polecenia [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) można także użyć programu Azure PowerShell:

```azurepowershell-interactive
# Create a resource group
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateFile https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzureRmVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

Wprowadź dane w monitach, aby podać nazwę zestawu skalowania i poświadczenia administratora dla wystąpień maszyn wirtualnych. Utworzenie zestawu skalowania i zastosowanie rozszerzenia w celu skonfigurowania aplikacji może potrwać 10 – 15 minut.


## <a name="test-your-sample-application"></a>Testowanie aplikacji przykładowej
Aby zobaczyć, jak działa aplikacja, uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) w następujący sposób:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Wprowadź publiczny adres IP modułu równoważenia obciążenia w przeglądarce internetowej w formacie *http://publicznyAdresIP/MyApp*. Moduł równoważenia obciążenia kieruje ruch do jednego z wystąpień maszyn wirtualnych, jak pokazano w poniższym przykładzie:

![Uruchamianie witryny usług IIS](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy grupa zasobów, zestaw skalowania i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) w następujący sposób:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```


## <a name="next-steps"></a>Następne kroki
W tym artykule wprowadzającym utworzyliśmy zestaw skalowania systemu Windows za pomocą szablonu platformy Azure i użyliśmy rozszerzenia DSC programu PowerShell w celu zainstalowania podstawowej aplikacji ASP.NET na wystąpieniach maszyn wirtualnych. W celu osiągnięcia większej skalowalności i automatyzacji rozszerz swój zestaw skalowania, korzystając z następujących artykułów z instrukcjami:

- [Wdrażanie aplikacji na zestawach skalowania maszyn wirtualnych](virtual-machine-scale-sets-deploy-app.md)
- Skalowanie automatyczne za pomocą [interfejsu wiersza polecenia platformy Azure](virtual-machine-scale-sets-autoscale-cli.md), [programu Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md), lub [witryny Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Używanie automatycznych uaktualnień systemu operacyjnego dla wystąpień maszyn wirtualnych zestawu skalowania](virtual-machine-scale-sets-automatic-upgrade.md)
