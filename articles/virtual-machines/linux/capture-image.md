---
title: Przechwytywanie obrazu maszyny Wirtualnej z systemem Linux na platformie Azure przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Przechwytywanie obrazu maszyny Wirtualnej platformy Azure na potrzeby wdrożeń pamięci masowej przy użyciu wiersza polecenia platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 5022d765b5dfa4f1f973b7fb4370d5314bb887b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542875"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Jak utworzyć obraz maszyny wirtualnej lub wirtualnego dysku twardego

<!-- generalize, image - extended version of the tutorial-->

Aby utworzyć wiele kopii maszyny wirtualnej (VM) do użytku na platformie Azure, Przechwytywanie obrazu maszyny wirtualnej lub wirtualnego dysku twardego systemu operacyjnego. Aby utworzyć obraz do wdrożenia, należy usunąć konto osobiste informacje. W poniższych krokach anulować aprowizację istniejącej maszyny Wirtualnej, Cofnij jej przydział i Utwórz obraz. Ten obraz umożliwia tworzenie maszyn wirtualnych w dowolnej grupie zasobów w ramach Twojej subskrypcji.

Aby utworzyć kopię istniejącej maszyny Wirtualnej systemu Linux w kopii zapasowej lub debugowania lub przekazywanie wyspecjalizowanego wirtualnego dysku twardego systemu Linux z lokalnej maszyny Wirtualnej, zobacz [przekazywanie i tworzenie maszyny Wirtualnej z systemem Linux przy użyciu niestandardowego obrazu dysku](upload-vhd.md).  

Można również użyć **Packer** umożliwia utworzenie konfiguracji niestandardowej. Aby uzyskać więcej informacji, zobacz [jak tworzenie obrazów maszyn wirtualnych z systemem Linux na platformie Azure za pomocą narzędzia Packer](build-image-with-packer.md).

Przed utworzeniem obrazu będą potrzebne następujące elementy:

* Maszynę wirtualną platformy Azure utworzone w modelu wdrażania usługi Resource Manager używa dysków zarządzanych. Jeśli jeszcze nie utworzono Maszynę wirtualną systemu Linux, możesz użyć [portal](quick-create-portal.md), [wiersza polecenia platformy Azure](quick-create-cli.md), lub [szablonów usługi Resource Manager](create-ssh-secured-vm-from-template.md). Konfigurowanie maszyny Wirtualnej, zgodnie z potrzebami. Na przykład [dodawanie dysków danych](add-disk.md), stosowania aktualizacji i zainstalować aplikacje. 

* Najnowsze [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowane i zalogować się do konta platformy Azure za pomocą [az login](/cli/azure/reference-index#az-login).

## <a name="quick-commands"></a>Szybkie polecenia

Uproszczoną wersję tego artykułu, a w przypadku testowania oceny lub informacje o maszynach wirtualnych na platformie Azure, zobacz [Tworzenie niestandardowego obrazu maszyny wirtualnej portalu Azure przy użyciu interfejsu wiersza polecenia](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>Krok 1: Anulowanie aprowizacji maszyny wirtualnej
Najpierw będzie anulowanie aprowizacji maszyny Wirtualnej przy użyciu agenta maszyny Wirtualnej platformy Azure, aby usunąć pliki specyficzny dla komputera i danych. Użyj `waagent` polecenia `-deprovision+user` parametr źródłowa maszyna wirtualna systemu Linux. Aby uzyskać więcej informacji, zobacz [Przewodnik użytkownika Agenta platformy Azure dla systemu Linux](../extensions/agent-linux.md).

1. Połącz z maszyną wirtualną systemu Linux, przy użyciu klienta SSH.
2. W oknie SSH wpisz następujące polecenie:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > To polecenie można uruchamiać tylko na maszynie Wirtualnej, która będzie przechwytywanie w formie obrazu. To polecenie nie gwarantuje, że obraz jest wyczyszczone wszystkie informacje poufne lub nadaje się do ponownej dystrybucji. `+user` Parametru spowoduje również usunięcie ostatnie aprowizowane konto użytkownika. Aby zachować poświadczenia konta użytkownika na maszynie wirtualnej, należy użyć tylko `-deprovision`.
 
3. Wprowadź **y** aby kontynuować. Możesz dodać `-force` parametru, aby uniknąć tego kroku potwierdzenia.
4. Po zakończeniu wykonywania polecenia wprowadź **wyjść** zamknąć klienta SSH.

## <a name="step-2-create-vm-image"></a>Krok 2: Tworzenie obrazu maszyny Wirtualnej
Oznaczanie maszyny Wirtualnej jako uogólnionej i przechwycić obraz, należy użyć wiersza polecenia platformy Azure. W poniższych przykładach należy zastąpić własnymi wartościami przykładowe nazwy parametru. Przykładowe nazwy parametru zawierają *myResourceGroup*, *myVnet*, i *myVM*.

1. Cofnij Przydział maszyny Wirtualnej, która zostanie anulowanie aprowizacji przy użyciu [az vm deallocate](/cli/azure/vm). Poniższy przykład powoduje cofnięcie przydziału maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*.
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. Oznaczanie maszyny Wirtualnej uogólniony za pomocą [az vm generalize](/cli/azure/vm). Poniższy przykład oznacza maszynę Wirtualną o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup* jako uogólniona.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Tworzenie obrazu zasobu maszyny Wirtualnej za pomocą [utworzyć obraz az](/cli/azure/image#az-image-create). Poniższy przykład tworzy obraz o nazwie *myImage* w grupie zasobów o nazwie *myResourceGroup* przy użyciu zasób maszynę Wirtualną o nazwie *myVM*.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > Obraz, który jest tworzony w tej samej grupie zasobów co źródłowa maszyna wirtualna. Maszyny wirtualne można utworzyć w dowolnej grupie zasobów, w ramach subskrypcji za pomocą tego obrazu. Z punktu widzenia zarządzania możesz utworzyć grupę zasobów określonych zasobów maszyny Wirtualnej i obrazów.
   >
   > Jeśli chcesz przechowywać obraz w strefie utworzenia niezawodnego magazynu, należy je utworzyć w regionie, który obsługuje [strefy dostępności](../../availability-zones/az-overview.md) i obejmują `--zone-resilient true` parametru.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Krok 3: Utwórz Maszynę wirtualną z przechwyconego obrazu
Tworzenie maszyny Wirtualnej przy użyciu obrazu, który został utworzony z [tworzenie az vm](/cli/azure/vm). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVMDeployed* za pomocą obrazu o nazwie *myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Tworzenie maszyny Wirtualnej w innej grupie zasobów 

W ramach subskrypcji można utworzyć maszyny wirtualne z obrazu w dowolnej grupie zasobów. Aby utworzyć Maszynę wirtualną w innej grupie zasobów niż obraz, należy określić pełny identyfikator zasobu obrazu. Użyj [listy obrazów az](/cli/azure/image#az-image-list) Aby wyświetlić listę obrazów. Dane wyjściowe będą podobne do poniższego przykładu.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

W poniższym przykładzie użyto [tworzenie az vm](/cli/azure/vm#az-vm-create) tworzenie maszyny Wirtualnej w grupie zasobów innej niż obrazu źródłowego, określając identyfikator zasobu obrazu

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Krok 4: Weryfikowanie wdrożenia

Protokół SSH z maszyną wirtualną, utworzonego do weryfikacji wdrożenia i Rozpocznij korzystanie z nowej maszyny Wirtualnej. Aby połączyć się za pośrednictwem protokołu SSH, znaleźć adres IP lub nazwa FQDN maszyny wirtualnej za pomocą [az vm show](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Kolejne kroki
Możesz utworzyć wiele maszyn wirtualnych za pomocą obrazu maszyny Wirtualnej źródłowego. Do wprowadzania zmian w obrazie: 

- Utwórz Maszynę wirtualną z obrazu.
- Upewnij się, wszystkie aktualizacje i zmiany konfiguracji.
- Wykonaj kroki ponownie, aby anulować aprowizację, cofnięcie przydziału, Uogólnij i utworzyć obraz.
- Użyj nowego obrazu do przyszłych wdrożeń. Możesz usunąć oryginalny obraz.

Aby uzyskać więcej informacji na temat zarządzania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia, zobacz [wiersza polecenia platformy Azure](/cli/azure).
