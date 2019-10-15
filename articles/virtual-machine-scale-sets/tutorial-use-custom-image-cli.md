---
title: Samouczek — korzystanie z niestandardowego obrazu maszyny wirtualnej w zestawie skalowania za pośrednictwem interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć niestandardowy obraz maszyny wirtualnej, którego można użyć do wdrożenia zestawu skalowania maszyny wirtualnej
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
ms.openlocfilehash: 6368a36eaa2e6832f22a13f20bc35d66c4425b4b
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311583"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Samouczek: tworzenie niestandardowego obrazu i używanie go dla zestawów skalowania maszyn wirtualnych za pośrednictwem interfejsu wiersza polecenia platformy Azure
Podczas tworzenia zestawu skalowania należy wskazać obraz używany do wdrożenia wystąpień maszyn wirtualnych. Aby zmniejszyć liczbę zadań wykonywanych po wdrożeniu wystąpień maszyn wirtualnych, można użyć niestandardowego obrazu maszyny wirtualnej. Niestandardowy obraz maszyny wirtualnej obejmuje wszystkie wymagane instalacje i konfiguracje aplikacji. Wszystkie wystąpienia maszyn wirtualnych utworzone w zestawie skalowania używają niestandardowego obrazu maszyny wirtualnej i są gotowe do obsługi ruchu aplikacji. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie i dostosowywanie maszyny wirtualnej
> * Anulowanie aprowizacji i uogólnianie maszyny wirtualnej
> * Tworzenie niestandardowego obrazu maszyny wirtualnej
> * Wdrażanie zestawu skalowania, który używa niestandardowego obrazu maszyny wirtualnej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.29 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).


## <a name="create-and-configure-a-source-vm"></a>Tworzenie i konfigurowanie źródłowej maszyny wirtualnej

>[!NOTE]
> Ten samouczek zawiera opis kroków procesu tworzenia i używania obrazu uogólnionej maszyny wirtualnej. Tworzenie zestawu skalowania na podstawie obrazu wyspecjalizowanej maszyny wirtualnej nie jest obsługiwane.

Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group), a następnie utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Ta maszyna wirtualna będzie używana jako źródło dla niestandardowego obrazu maszyny wirtualnej. Poniższy przykład obejmuje tworzenie maszyny wirtualnej *myVM* w grupie zasobów *myResourceGroup*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

Publiczny adres IP maszyny wirtualnej jest widoczny w danych wyjściowych polecenia [az vm create](/cli/azure/vm). Za pośrednictwem protokołu SSH połącz się z publicznym adresem IP maszyny wirtualnej w następujący sposób:

```azurecli-interactive
ssh azureuser@<publicIpAddress>
```

Aby dostosować maszynę wirtualną, należy zainstalować podstawowy serwer internetowy. Dzięki temu wdrożona maszyna wirtualna będzie miała wszystkie pakiety wymagane do uruchamiania aplikacji internetowej. Zainstaluj serwer *NGINX* za pomocą polecenia `apt-get` w następujący sposób:

```bash
sudo apt-get install -y nginx
```

Ostatnim krokiem jest anulowanie aprowizacji maszyny wirtualnej w celu przygotowania jej do użycia jako obraz niestandardowy. Ten krok polega na usunięciu informacji specyficznych dla maszyny, co umożliwia wdrożenie wielu maszyn wirtualnych za pomocą pojedynczego obrazu. Anulowanie aprowizacji maszyny wirtualnej powoduje zresetowanie nazwy hosta do wartości *localhost.localdomain*. Usuwane są również klucze hosta SSH, konfiguracje „nameserver”, hasło administratora (root) i buforowane dzierżawy DHCP.

Aby anulować aprowizację maszyny wirtualnej, użyj agenta maszyny wirtualnej platformy Azure (*waagent*). Agent maszyny wirtualnej platformy Azure jest zainstalowany na każdej maszynie wirtualnej i służy do komunikowania się z platformą Azure. Parametr `-force` nakazuje agentowi akceptowanie poleceń resetowania informacji specyficznych dla maszyny.

```bash
sudo waagent -deprovision+user -force
```

Zamknij połączenie SSH z maszyną wirtualną:

```bash
exit
```


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Tworzenie niestandardowego obrazu maszyny wirtualnej ze źródłowej maszyny wirtualnej
Źródłowa maszyna wirtualna została dostosowana przez zainstalowanie serwera internetowego Nginx. Utworzymy niestandardowy obraz maszyny wirtualnej, który będzie używany z zestawem skalowania.

Aby można było utworzyć obraz, należy cofnąć przydział maszyny wirtualnej. Cofnij przydział maszyny wirtualnej przy użyciu polecenia [az vm deallocate](/cli//azure/vm). Następnie ustaw dla maszyny wirtualnej stan uogólniony za pomocą polecenia [az vm generalize](/cli//azure/vm), aby poinformować platformę Azure, że maszyna wirtualna jest gotowa do użycia jako obraz niestandardowy. Obraz można utworzyć tylko za pomocą uogólnionej maszyny wirtualnej:


```azurecli-interactive
az vm deallocate --resource-group myResourceGroup --name myVM
az vm generalize --resource-group myResourceGroup --name myVM
```

Cofanie przydziału i uogólnianie maszyny wirtualnej może potrwać kilka minut.

Teraz utwórz obraz maszyny wirtualnej za pomocą polecenia [az image create](/cli//azure/image). W poniższym przykładzie utworzono obraz o nazwie *myImage* za pomocą maszyny wirtualnej:

> KORYGUJĄC Jeśli grupa zasobów i lokalizacja maszyny wirtualnej są różne, można dodać parametr `--location` do poniższych poleceń w celu wybrania lokalizacji źródłowej maszyny wirtualnej użytej do utworzenia obrazu. 

```azurecli-interactive
az image create \
  --resource-group myResourceGroup \
  --name myImage \
  --source myVM
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Tworzenie zestawu skalowania z niestandardowego obrazu maszyny wirtualnej
Utwórz zestaw skalowania za pomocą polecenia [az vmss create](/cli/azure/vmss#az-vmss-create). Zamiast obrazu platformy, takiego jak *UbuntuLTS* lub *CentOS*, podaj nazwę niestandardowego obrazu maszyny wirtualnej. W poniższym przykładzie pokazano tworzenie zestawu skalowania o nazwie *myScaleSet*, używającego niestandardowego obrazu o nazwie *myImage* utworzonego w poprzednim kroku:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image myImage \
  --admin-username azureuser \
  --generate-ssh-keys
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.


## <a name="test-your-scale-set"></a>Testowanie zestawu skalowania
Aby zezwolić na ruch internetowy w zestawie skalowania oraz upewnić się, że serwer internetowy działa prawidłowo, utwórz regułę modułu równoważenia obciążenia za pomocą polecenia [az network lb rule create](/cli/azure/network/lb/rule). W poniższym przykładzie pokazano tworzenie reguły o nazwie *myLoadBalancerRuleWeb*, która zezwala na ruch na porcie *TCP* *80*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Aby zobaczyć, jak działa zestaw skalowania, uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [az network public-ip show](/cli/azure/network/public-ip). W poniższym przykładzie pokazano uzyskiwanie adresu IP dla modułu *myScaleSetLBPublicIP* utworzonego w ramach zestawu skalowania:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Wprowadź publiczny adres IP w przeglądarce internetowej. Zostanie wyświetlona domyślna strona serwera NGINX, jak pokazano w poniższym przykładzie:

![Serwer Nginx uruchomiony za pomocą niestandardowego obrazu maszyny wirtualnej](media/tutorial-use-custom-image-cli/default-nginx-website.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Aby pozbyć się zestawu skalowania i dodatkowych zasobów, usuń grupę zasobów wraz z całą zawartością za pomocą polecenia [az group delete](/cli/azure/group). Parametr `--no-wait` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji. Parametr `--yes` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Następne kroki
W tym samouczku omówiono tworzenie niestandardowego obrazu maszyny wirtualnej i używanie go z zestawami skalowania za pośrednictwem interfejsu wiersza polecenia platformy Azure:

> [!div class="checklist"]
> * Tworzenie i dostosowywanie maszyny wirtualnej
> * Anulowanie aprowizacji i uogólnianie maszyny wirtualnej
> * Tworzenie niestandardowego obrazu maszyny wirtualnej
> * Wdrażanie zestawu skalowania, który używa niestandardowego obrazu maszyny wirtualnej

Przejdź do następnego samouczka, aby dowiedzieć się, jak wdrożyć aplikacje w zestawie skalowania.

> [!div class="nextstepaction"]
> [Deploy applications to your scale sets (Wdrażanie aplikacji w zestawach skalowania)](tutorial-install-apps-cli.md)
