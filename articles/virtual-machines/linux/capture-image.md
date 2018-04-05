---
title: Przechwyć obraz maszyny wirtualnej systemu Linux na platformie Azure przy użyciu interfejsu wiersza polecenia 2.0 | Dokumentacja firmy Microsoft
description: Przechwyć obraz maszyny Wirtualnej platformy Azure na potrzeby wdrożeń masowej przy użyciu 2.0 interfejsu wiersza polecenia platformy Azure.
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
ms.date: 08/22/2018
ms.author: cynthn
ms.openlocfilehash: e83319b8b4ace495a4301dd850cea27633115426
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Jak utworzyć obraz maszyny wirtualnej lub wirtualnego dysku twardego

<!-- generalize, image - extended version of the tutorial-->

Aby utworzyć wiele kopii maszyny wirtualnej (VM) do użycia na platformie Azure, przechwytywania obrazu maszyny Wirtualnej lub wirtualnego dysku twardego systemu operacyjnego. Do utworzenia obrazu, należy usunąć informacje osobiste konto, które sprawia, że bezpieczniejsze wdrożyć wiele razy. W poniższych krokach anulowanie zastrzeżenia istniejącej maszyny Wirtualnej, deallocate i tworzenie obrazu. Ten obraz służy do tworzenia maszyn wirtualnych w dowolnej grupie zasobów w ramach subskrypcji.

Jeśli chcesz utworzyć kopię istniejącej maszyny Wirtualnej systemu Linux tworzenia kopii zapasowej i debugowanie, lub Przekaż specjalne VHD Linux z lokalnej maszyny Wirtualnej, zobacz [przekazywanie i Utwórz Maszynę wirtualną systemu Linux na podstawie obrazu niestandardowego dysku](upload-vhd.md).  

Można również użyć **pakujący** do utworzenia konfiguracji niestandardowej. Aby uzyskać więcej informacji na temat używania pakujący, zobacz [tworzenie obrazów maszyny wirtualnej systemu Linux na platformie Azure przy użyciu pakujący](build-image-with-packer.md).


## <a name="before-you-begin"></a>Przed rozpoczęciem
Upewnij się, że zostały spełnione następujące wymagania wstępne:

* Należy maszyny Wirtualnej platformy Azure utworzonych w modelu wdrażania usługi Resource Manager za pomocą dysków zarządzanych. Jeśli nie utworzono Maszynę wirtualną systemu Linux, możesz użyć [portal](quick-create-portal.md), [interfejsu wiersza polecenia Azure](quick-create-cli.md), lub [szablonów Resource Manager](create-ssh-secured-vm-from-template.md). Konfigurowanie maszyny Wirtualnej, zgodnie z potrzebami. Na przykład [Dodaj dyski danych](add-disk.md), stosowania aktualizacji i zainstalować aplikacje. 

* Musisz również mieć najnowszej [Azure CLI 2.0](/cli/azure/install-az-cli2) zainstalowane i zalogować się do konta platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login).

## <a name="quick-commands"></a>Szybkie polecenia

Uproszczona wersja tego tematu, testowania, obliczenia lub informacje o maszynach wirtualnych na platformie Azure, dla [utworzyć niestandardowy obraz maszyny Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>Krok 1: Anulowanie zastrzeżenia maszyny Wirtualnej
Możesz anulowanie zastrzeżenia maszyny Wirtualnej przy użyciu agenta maszyny Wirtualnej platformy Azure, aby usunąć maszynę określonych plików i danych. Użyj `waagent` z *-deprovision + użytkownika* parametru w źródle maszyny Wirtualnej systemu Linux. Aby uzyskać więcej informacji, zobacz [Przewodnik użytkownika Agenta platformy Azure dla systemu Linux](../windows/agent-user-guide.md).

1. Podłącz do sieci maszyny Wirtualnej systemu Linux przy użyciu klienta SSH.
2. W oknie SSH wpisz następujące polecenie:
   
    ```bash
    sudo waagent -deprovision+user
    ```
<br>
   > [!NOTE]
   > To polecenie można uruchamiać tylko na maszynie Wirtualnej, który ma zostać przechwytywanie w formie obrazu. Nie gwarantuje, że obraz jest wyczyszczone wszystkie informacje poufne lub nadaje się do ponownej dystrybucji. *+ Użytkownika* parametru spowoduje również usunięcie ostatniego elastycznie konta. Jeśli chcesz zachować poświadczenia konta w maszynie Wirtualnej, po prostu użyj *-deprovision* pozostawienia konta użytkownika w miejscu.
 
3. Typ **y** aby kontynuować. Możesz dodać **-force** parametr, aby uniknąć tego kroku potwierdzenia.
4. Po zakończeniu wykonywania polecenia, wpisz **zakończyć**. Ten krok powoduje zamknięcie klienta SSH.

## <a name="step-2-create-vm-image"></a>Krok 2: Tworzenie obrazu maszyny Wirtualnej
Azure CLI 2.0 umożliwia zostać oznaczone jako uogólniona maszyna wirtualna i przechwycenie obrazu. W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametru zawierają *myResourceGroup*, *myVnet*, i *myVM*.

1. Cofnięcie przydziału maszyny Wirtualnej, należy cofnąć jej zainicjowania jej z [deallocate wirtualna az](/cli//azure/vm#deallocate). Poniższy przykład cofa alokację maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*:
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. Maszyna wirtualna zostać oznaczone jako uogólniony z [generalize wirtualna az](/cli//azure/vm#generalize). Następujące znaki przykład maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup* jako uogólniony:
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Teraz utworzyć obraz zasobu maszyny Wirtualnej z [tworzenia obrazu az](/cli/azure/image#az_image_create). Poniższy przykład tworzy obraz o nazwie *myImage* w grupie zasobów o nazwie *myResourceGroup* przy użyciu zasobu maszyny Wirtualnej o nazwie *myVM*:
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > Obraz jest tworzony w tej samej grupie zasobów jako źródło maszyny Wirtualnej. Maszyny wirtualne można tworzyć w dowolnej grupie zasobów w ramach subskrypcji z tego obrazu. Z punktu widzenia zarządzania możesz utworzyć grupę zasobów dla określonych zasobów maszyny Wirtualnej i obrazów.
   >
   > Jeśli chcesz przechowywać obrazu w strefie odporność pamięci masowej, należy go utworzyć w regionie, który obsługuje [stref dostępności](../../availability-zones/az-overview.md) i obejmują `--zone-resilient true` parametru.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Krok 3: Tworzenie maszyny Wirtualnej z przechwyconego obrazu
Utwórz maszynę Wirtualną przy użyciu obrazu zostały utworzone z [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVMDeployed* z obrazu o nazwie *myImage*:

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Tworzenie maszyny Wirtualnej w innej grupie zasobów 

Maszyny wirtualne można utworzyć na podstawie obrazu w dowolnej grupie zasobów w ramach subskrypcji. Aby utworzyć Maszynę wirtualną w innej grupie zasobów niż obraz, określ pełny identyfikator zasobu obrazu. Użyj [listy obrazów az](/cli/azure/image#az_image_list) w celu wyświetlenia listy obrazów. Dane wyjściowe są podobne do poniższego przykładu:

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

W poniższym przykładzie użyto [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) Aby utworzyć Maszynę wirtualną w innej grupie zasobów niż obraz źródłowy, określając identyfikator zasobu obrazu:

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Krok 4: Weryfikowanie wdrażania

Teraz SSH dla maszyny wirtualnej utworzonej do weryfikowania wdrożenia i rozpocząć korzystanie z nowej maszyny Wirtualnej. Aby połączyć się za pośrednictwem protokołu SSH, znaleźć adres IP lub nazwa FQDN maszyny wirtualnej z [az maszyny wirtualnej pokazu](/cli/azure/vm#az_vm_show):

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Następne kroki
Możesz utworzyć wiele maszyn wirtualnych z obrazu maszyny Wirtualnej źródłowego. Jeśli chcesz dokonać zmian obrazu: 

- Utwórz maszynę Wirtualną z obrazu.
- Upewnij się, wszystkie aktualizacje i zmiany konfiguracji.
- Wykonaj kroki ponownie anulowanie zastrzeżenia, deallocate generalize i tworzenie obrazu.
- Skorzystaj z tego nowego obrazu dla przyszłych wdrożeń. W razie potrzeby usuń oryginalny obraz.

Aby uzyskać więcej informacji na temat zarządzania maszyn wirtualnych z poziomu interfejsu wiersza polecenia, zobacz [Azure CLI 2.0](/cli/azure).
