---
title: Szybki start — tworzenie zestawu skalowania maszyny wirtualnej systemu Linux za pomocą szablonu usługi Azure Resource Manager
description: Dowiedz się, jak szybko utworzyć skalę maszyny wirtualnej z systemem Linux za pomocą szablonu usługi Azure Resource Manager, który wdraża przykładową aplikację i konfiguruje reguły automatycznego skalowania
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 03/27/2020
ms.author: jushiman
ms.openlocfilehash: 4c0bac943be996c02436824334bd79a270f9a2e2
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010464"
---
# <a name="quickstart-create-a-linux-virtual-machine-scale-set-with-an-azure-resource-manager-template"></a>Szybki start: tworzenie zestawu skalowania maszyny wirtualnej systemu Linux za pomocą szablonu usługi Azure Resource Manager

Zestaw skalowania maszyn wirtualnych umożliwia wdrożenie zestawu identycznych, automatycznie skalowanych maszyn wirtualnych, oraz zarządzanie nimi. Maszyny wirtualne w zestawie skalowania możesz skalować ręcznie lub możesz zdefiniować reguły skalowania automatycznego na podstawie użycia takich zasobów jak procesor CPU, zapotrzebowanie na pamięć lub ruch sieciowy. Moduł równoważenia obciążenia platformy Azure następnie dystrybuuje ruch do wystąpień maszyn wirtualnych w zestawie skalowania. W tym przewodniku Szybki start utworzysz zestaw skalowania maszyn wirtualnych i wdrożysz przykładową aplikację przy użyciu szablonu usługi Azure Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Brak.

## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania

Szablony usługi Azure Resource Manager umożliwiają wdrażanie grup powiązanych zasobów. W jednym szablonie można utworzyć zestaw skalowania maszyn wirtualnych, zainstalować aplikacje i skonfigurować reguły automatycznego skalowania. Korzystając ze zmiennych i parametrów, można ponownie użyć tego szablonu, aby zaktualizować istniejące zestawy skalowania lub utworzyć dodatkowe. Szablony można wdrażać za pośrednictwem witryny Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell bądź z poziomu potoków ciągłej integracji/ciągłego dostarczania (CI/CD).

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://azure.microsoft.com/resources/templates/201-vmss-bottle-autoscale/).

:::code language="json" source="~/quickstart-templates/201-vmss-bottle-autoscale/azuredeploy.json" range="1-330" highlight="176-264":::

Te zasoby są zdefiniowane w szablonie:

- [**Sieć Microsoft.Network/virtual Sieci**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft.Insights/skalowanie automatyczneStawienia**](/azure/templates/microsoft.insights/autoscalesettings)

#### <a name="define-a-scale-set"></a>Definiowanie zestawu skalowania

Wyróżniona część jest definicją zasobu zestawu skalowania. Aby utworzyć skalę przy użyciu szablonu, należy zdefiniować odpowiednie zasoby. Podstawowe elementy typu zasobu zestawu skalowania maszyn wirtualnych są następujące:

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

Aby dostosować szablon zestawu skalowania, można zmienić rozmiar maszyny Wirtualnej lub pojemność początkową. Inną opcją jest użycie innej platformy lub niestandardowego obrazu.

#### <a name="add-a-sample-application"></a>Dodawanie przykładowej aplikacji

Aby przetestować zestaw skalowania, należy zainstalować podstawową aplikację internetową. Gdy wdrażasz zestaw skalowania, rozszerzenia maszyn wirtualnych mogą dostarczać konfigurację po wdrożeniu oraz zadania automatyzacji, takie jak instalowanie aplikacji. Skrypty można pobrać z usługi Azure Storage lub GitHub bądź można je dostarczyć do witryny Azure Portal w czasie wykonywania rozszerzenia. Aby zastosować rozszerzenie do zestawu skalowania, dodaj sekcję *extensionProfile* do przedstawionego wcześniej przykładu zasobów. Profil rozszerzenia zwykle definiuje następujące właściwości:

- Typ rozszerzenia
- Wydawca rozszerzenia
- Wersja rozszerzenia
- Lokalizacja konfiguracji lub skryptów instalacji
- Polecenia do wykonania na wystąpieniach maszyn wirtualnych

Szablon używa niestandardowego rozszerzenia skryptu do [zainstalowania bottle,](https://bottlepy.org/docs/dev/)struktury sieci Web Języka Python i prostego serwera HTTP.

Dwa skrypty są zdefiniowane w*installserver.sh* **fileUris** - i *workserver.py*. Pliki te są pobierane z GitHub, a `bash installserver.sh` następnie *commandToExecute* działa, aby zainstalować i skonfigurować aplikację.

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Szablon można wdrożyć, wybierając następujący przycisk **Wdrażanie na platformie Azure.** Ten przycisk otwiera witrynę Azure Portal, ładuje pełny szablon i wyświetla monit o podanie kilku parametrów, takich jak nazwa zestawu skalowania, liczba wystąpień i poświadczenia administratora.

[![Wdrażanie szablonu na platformie Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

Można również wdrożyć szablon Usługi Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Wprowadź dane w monitach, aby podać nazwę zestawu skalowania, liczbę wystąpień i poświadczenia administratora dla wystąpień maszyn wirtualnych. Utworzenie zestawu skalowania i zasobów pomocniczych trwa kilka minut.

## <a name="test-the-deployment"></a>Testowanie wdrożenia

Aby zapoznać się z działaniem zestawu skalowania, uzyskaj dostęp do przykładowej aplikacji internetowej w przeglądarce internetowej. Uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [az network public-ip list](/cli/azure/network/public-ip) w następujący sposób:

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Wprowadź publiczny adres IP modułu równoważenia obciążenia w przeglądarce internetowej w formacie *\/http: /publicIpAddress:9000/do_work*. Moduł równoważenia obciążenia kieruje ruch do jednego z wystąpień maszyn wirtualnych, jak pokazano w poniższym przykładzie:

![Domyślna strona internetowa na serwerze NGINX](media/virtual-machine-scale-sets-create-template/running-python-app.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, zestaw skalowania i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group) w następujący sposób. Parametr `--no-wait` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji. Parametr `--yes` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

W tym podręczniku Szybki start utworzyliśmy zestaw skalowania systemu Linux za pomocą szablonu platformy Azure i użyliśmy niestandardowego rozszerzenia skryptu w celu zainstalowania podstawowego serwera internetowego Python na wystąpieniach maszyn wirtualnych. Aby dowiedzieć się więcej, przejdź do samouczka dotyczącego sposobu tworzenia zestawów skalowania maszyn wirtualnych platformy Azure i zarządzania nimi.

> [!div class="nextstepaction"]
> [Tworzenie zestawów skalowania maszyn wirtualnych platformy Azure i zarządzanie nimi](tutorial-create-and-manage-cli.md)
