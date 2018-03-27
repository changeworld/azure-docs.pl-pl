---
title: "Tworzenie zestawu skalowania maszyn wirtualnych z systemem Linux przy użyciu szablonu platformy Azure | Microsoft Docs"
description: "Dowiedz się, jak szybko utworzyć skalę maszyny wirtualnej z systemem Linux za pomocą szablonu usługi Azure Resource Manager, który wdraża przykładową aplikację i konfiguruje reguły automatycznego skalowania"
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
ms.openlocfilehash: 16e9c0b30710d711ef2789f7781b17e72889d4da
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-linux-virtual-machine-scale-set-with-an-azure-template"></a>Tworzenie zestawu skalowania maszyn wirtualnych z systemem Linux przy użyciu szablonu platformy Azure
Zestaw skalowania maszyn wirtualnych umożliwia wdrożenie zestawu identycznych, automatycznie skalowanych maszyn wirtualnych, oraz zarządzanie nimi. Maszyny wirtualne w zestawie skalowania można skalować ręcznie lub można zdefiniować reguły skalowania automatycznego na podstawie użycia zasobów, takich jak procesor CPU, zapotrzebowanie na pamięć lub ruch sieciowy. W tym artykule wprowadzającym opisano tworzenie zestawu skalowania maszyn wirtualnych z systemem Linux za pomocą szablonu usługi Azure Resource Manager. Zestaw skalowania można również utworzyć przy użyciu [interfejsu wiersza polecenia platformy Azure w wersji 2.0](virtual-machine-scale-sets-create-cli.md), [programu Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) lub [witryny Azure Portal](virtual-machine-scale-sets-create-portal.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.20 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 


## <a name="define-a-scale-set-in-a-template"></a>Definiowanie zestawu skalowania w szablonie
Szablony usługi Azure Resource Manager umożliwiają wdrażanie grup powiązanych zasobów. Szablony są pisane w formacie JavaScript Object Notation (JSON) i określają środowisko całej infrastruktury platformy Azure dla aplikacji. W jednym szablonie można utworzyć zestaw skalowania maszyn wirtualnych, zainstalować aplikacje i skonfigurować reguły automatycznego skalowania. Korzystając ze zmiennych i parametrów, można ponownie użyć tego szablonu, aby zaktualizować istniejące zestawy skalowania lub utworzyć dodatkowe. Szablony można wdrażać za pośrednictwem witryny Azure Portal, interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub programu Azure PowerShell bądź z poziomu potoków ciągłej integracji / ciągłego dostarczania (CI/CD).

Więcej informacji dotyczących szablonów można znaleźć w temacie [Omówienie usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment).

Aby utworzyć skalę przy użyciu szablonu, należy zdefiniować odpowiednie zasoby. Podstawowe elementy typu zasobu zestawu skalowania maszyn wirtualnych są następujące:

| Właściwość                     | Opis właściwości                                  | Przykładowa wartość szablonu                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | Typ zasobu platformy Azure do utworzenia                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | Nazwa zestawu skalowania                                       | myScaleSet                                |
| location                     | Lokalizacja utworzenia zestawu skalowania                     | Wschodnie stany USA                                   |
| sku.name                     | Rozmiar maszyny wirtualnej dla każdego wystąpienia zestawu skalowania                  | Standardowa_A1                               |
| sku.capacity                 | Liczba wystąpień maszyn wirtualnych do początkowego utworzenia           | 2                                         |
| upgradePolicy.mode           | Tryb uaktualniania wystąpienia maszyny wirtualnej w przypadku wprowadzenia zmian              | Automatyczny                                 |
| imageReference               | Platforma lub obraz niestandardowy do użycia na potrzeby wystąpień maszyn wirtualnych | Canonical Ubuntu Server 16.04-LTS         |
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
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
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

Szablon [serwera HTTP Python w systemie Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) używa niestandardowego rozszerzenia skryptu w celu zainstalowania rozwiązania [Bottle](http://bottlepy.org/docs/dev/) — platformy internetowej języka Python — i prostego serwera HTTP. 

Dwa skrypty zostały zdefiniowane w plikach **fileUris** - *installserver.sh* i *workserver.py*. Te pliki są pobierane z usługi GitHub, a następnie w sekcji *commandToExecute* jest uruchamiane polecenie `bash installserver.sh` w celu zainstalowania i skonfigurowania aplikacji:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
          ],
          "commandToExecute": "bash installserver.sh"
        }
      }
    }
  ]
}
```


## <a name="deploy-the-template"></a>Wdrożenie szablonu
Szablon [serwera HTTP Python w systemie Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) można wdrożyć za pomocą następującego przycisku **Wdróż na platformie Azure**. Ten przycisk otwiera witrynę Azure Portal, ładuje pełny szablon i wyświetla monit o podanie kilku parametrów, takich jak nazwa zestawu skalowania, liczba wystąpień i poświadczenia administratora.

[![Wdrażanie szablonu na platformie Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

Możesz także użyć interfejsu wiersza polecenia platformy Azure w wersji 2.0, aby zainstalować serwer HTTP Python w systemie Linux za pomocą polecenia [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create) w następujący sposób:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Wprowadź dane w monitach, aby podać nazwę zestawu skalowania, liczbę wystąpień i poświadczenia administratora dla wystąpień maszyn wirtualnych. Utworzenie zestawu skalowania i zasobów pomocniczych trwa kilka minut.


## <a name="test-your-sample-application"></a>Testowanie aplikacji przykładowej
Aby zobaczyć, jak działa aplikacja, uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_show) w następujący sposób:

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Wprowadź publiczny adres IP modułu równoważenia obciążenia w przeglądarce internetowej w formacie *http://publicznyAdresIP:9000/do_work*. Moduł równoważenia obciążenia kieruje ruch do jednego z wystąpień maszyn wirtualnych, jak pokazano w poniższym przykładzie:

![Domyślna strona internetowa na serwerze NGINX](media/virtual-machine-scale-sets-create-template/running-python-app.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy grupa zasobów, zestaw skalowania i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az_group_delete) w następujący sposób:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Następne kroki
W tym artykule wprowadzającym utworzyliśmy zestaw skalowania systemu Linux za pomocą szablonu platformy Azure i użyliśmy niestandardowego rozszerzenia skryptu w celu zainstalowania podstawowego serwera internetowego Python na wystąpieniach maszyn wirtualnych. W celu osiągnięcia większej skalowalności i automatyzacji rozszerz swój zestaw skalowania, korzystając z następujących artykułów z instrukcjami:

- [Wdrażanie aplikacji na zestawach skalowania maszyn wirtualnych](virtual-machine-scale-sets-deploy-app.md)
- Skalowanie automatyczne za pomocą [interfejsu wiersza polecenia platformy Azure](virtual-machine-scale-sets-autoscale-cli.md), [programu Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md), lub [witryny Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Używanie automatycznych uaktualnień systemu operacyjnego dla wystąpień maszyn wirtualnych zestawu skalowania](virtual-machine-scale-sets-automatic-upgrade.md)
