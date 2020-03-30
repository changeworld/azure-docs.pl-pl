---
title: Przechwytywanie obrazu maszyny Wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: Przechwyć obraz maszyny Wirtualnej platformy Azure do użycia w przypadku masowych wdrożeń przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 77f6244651551763f5460432655d66267775a256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250402"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Jak utworzyć obraz maszyny wirtualnej lub wirtualnego dysku twardego

Aby utworzyć wiele kopii maszyny wirtualnej (VM) do użycia na platformie Azure, przechwyć obraz maszyny wirtualnej lub dysku twardego systemu operacyjnego. Aby utworzyć obraz do wdrożenia, musisz usunąć informacje o koncie osobistym. W poniższych krokach można anulować aprowizację istniejącej maszyny Wirtualnej, przydzielić jej i utworzyć obraz. Ten obraz służy do tworzenia maszyn wirtualnych w dowolnej grupie zasobów w ramach subskrypcji.

Aby utworzyć kopię istniejącej maszyny Wirtualnej z systemem Linux do tworzenia kopii zapasowych lub debugowania lub przesłać wyspecjalizowaną dysk VHD systemu Linux z lokalnej maszyny Wirtualnej, zobacz [Przekazywanie i tworzenie maszyny Wirtualnej z systemem Linux z niestandardowego obrazu dysku](upload-vhd.md).  

Za pomocą usługi **Azure VM Image Builder (Public Preview)** można użyć do tworzenia obrazu niestandardowego, nie trzeba uczyć się żadnych narzędzi lub konfiguracji potoków kompilacji, po prostu zapewniając konfigurację obrazu, a Konstruktor obrazów utworzy obraz. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do konstruktora obrazów maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

Przed utworzeniem obrazu potrzebne będą następujące elementy:

* Maszyna wirtualna platformy Azure utworzona w modelu wdrażania Usługi Resource Manager, która używa dysków zarządzanych. Jeśli maszyna wirtualna z systemem Linux nie została jeszcze utworzona, można użyć szablonów [portalu](quick-create-portal.md), [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)lub Menedżera [zasobów.](create-ssh-secured-vm-from-template.md) Skonfiguruj maszynę wirtualną w razie potrzeby. Na przykład [dodaj dyski danych,](add-disk.md)zastosuj aktualizacje i zainstaluj aplikacje. 

* Zainstalowano najnowszą [platformę Azure CLI](/cli/azure/install-az-cli2) i zalogowano się do konta platformy Azure przy [logowaniu az.](/cli/azure/reference-index#az-login)

## <a name="prefer-a-tutorial-instead"></a>Wolisz tutorial zamiast?

Aby uzyskać uproszczoną wersję tego artykułu oraz do testowania, oceny lub uczenia się o maszynach wirtualnych na platformie Azure, zobacz [Tworzenie niestandardowego obrazu maszyny wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia.](tutorial-custom-images.md)  W przeciwnym razie czytaj tutaj, aby uzyskać pełny obraz.


## <a name="step-1-deprovision-the-vm"></a>Krok 1: Anulowanie obsługi administracyjnej maszyny wirtualnej
Najpierw będzie deprovision maszyny Wirtualnej przy użyciu agenta maszyny Wirtualnej platformy Azure do usuwania plików specyficznych dla komputera i danych. Użyj `waagent` polecenia z `-deprovision+user` parametrem na źródłowej maszynie wirtualnej systemu Linux. Aby uzyskać więcej informacji, zobacz [Przewodnik użytkownika Agenta platformy Azure dla systemu Linux](../extensions/agent-linux.md).

1. Połącz się z maszyną wirtualną z systemem Linux za pomocą klienta SSH.
2. W oknie SSH wprowadź następujące polecenie:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Uruchom to polecenie tylko na maszynie Wirtualnej, która zostanie przechwyci jako obraz. To polecenie nie gwarantuje, że obraz jest wyczyszczony ze wszystkich poufnych informacji lub nadaje się do redystrybucji. Parametr `+user` usuwa również ostatnie konto użytkownika aprowizowanego. Aby zachować poświadczenia konta użytkownika na `-deprovision`maszynie Wirtualnej, należy używać tylko .
 
3. Wprowadź **y,** aby kontynuować. Można dodać `-force` parametr, aby uniknąć tego kroku potwierdzenia.
4. Po zakończeniu polecenia wprowadź **exit,** aby zamknąć klienta SSH.  Maszyna wirtualna będzie nadal uruchomiona w tym momencie.

## <a name="step-2-create-vm-image"></a>Krok 2: Tworzenie obrazu maszyny Wirtualnej
Użyj interfejsu wiersza polecenia platformy Azure, aby oznaczyć maszynę wirtualną jako uogólnioną i przechwyć obraz. W poniższych przykładach zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów to *myResourceGroup*, *myVnet*i *myVM*.

1. Zdemionelokuj maszynę wirtualną, która została wyrejestrowana za pomocą [az vm deallocate](/cli/azure/vm). W poniższym przykładzie wiele osób przydziela maszynę wirtualną o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*.  
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```
    
    Poczekaj, aż maszyna wirtualna całkowicie cofnięto alokację przed przejściem dalej. Może to potrwać kilka minut.  Maszyna wirtualna jest zamykana podczas alokacji transakcji.

2. Oznacz maszynę wirtualną jako uogólnioną [za pomocą az vm uogólniać](/cli/azure/vm). W poniższym przykładzie znak maszynowy o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup* jako uogólniony.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

    Nie można już ponownie uruchomić maszyny wirtualnej, która została uogólniona.

3. Utwórz obraz zasobu maszyny Wirtualnej z [obrazem az .](/cli/azure/image#az-image-create) Poniższy przykład tworzy obraz o nazwie *myImage* w grupie zasobów o nazwie *myResourceGroup* przy użyciu zasobu maszyny Wirtualnej o nazwie *myVM*.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > Obraz jest tworzony w tej samej grupie zasobów co źródłowa maszyna wirtualna. Maszyny wirtualne można tworzyć w dowolnej grupie zasobów w ramach subskrypcji z tego obrazu. Z punktu widzenia zarządzania można utworzyć określoną grupę zasobów dla zasobów i obrazów maszyny Wirtualnej.
   >
   > Jeśli chcesz przechowywać obraz w magazynie odpornym na strefy, musisz utworzyć go w regionie, który obsługuje [strefy dostępności](../../availability-zones/az-overview.md) i zawiera `--zone-resilient true` parametr.
   
To polecenie zwraca JSON, który opisuje obraz maszyny Wirtualnej. Zapisz to dane wyjściowe do późniejszego odwołania.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Krok 3: Tworzenie maszyny Wirtualnej na podstawie przechwyconego obrazu
Utwórz maszynę wirtualną przy użyciu obrazu utworzonego za [pomocą programu az vm create](/cli/azure/vm). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVMDeployed* z obrazu o nazwie *myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Tworzenie maszyny Wirtualnej w innej grupie zasobów 

Maszyny wirtualne można tworzyć z obrazu w dowolnej grupie zasobów w ramach subskrypcji. Aby utworzyć maszynę wirtualną w innej grupie zasobów niż obraz, określ pełny identyfikator zasobu do obrazu. Użyj [listy obrazów az,](/cli/azure/image#az-image-list) aby wyświetlić listę obrazów. Dane wyjściowe będą podobne do poniższego przykładu.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

W poniższym przykładzie użyto [az vm create](/cli/azure/vm#az-vm-create) do utworzenia maszyny Wirtualnej w grupie zasobów innej niż obraz źródłowy, określając identyfikator zasobu obrazu.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Krok 4: Sprawdź wdrożenie

SSH do maszyny wirtualnej utworzonej w celu sprawdzenia wdrożenia i rozpoczęcia korzystania z nowej maszyny wirtualnej. Aby połączyć się za pośrednictwem protokołu SSH, znajdź adres IP lub numer FQDN maszyny Wirtualnej z [az vm show](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Następne kroki
Można utworzyć wiele maszyn wirtualnych ze źródłowego obrazu maszyny Wirtualnej. Aby wprowadzić zmiany w obrazie: 

- Utwórz maszynę wirtualną na podstawie obrazu.
- Wprowadzanie jakichkolwiek aktualizacji lub zmian konfiguracji.
- Wykonaj ponownie kroki, aby anulować aprowizację, cofnięto alokację, uogólnić i utworzyć obraz.
- Użyj tego nowego obrazu dla przyszłych wdrożeń. Możesz usunąć oryginalny obraz.

Aby uzyskać więcej informacji na temat zarządzania maszynami wirtualnymi za pomocą interfejsu wiersza polecenia, zobacz [interfejsu wiersza polecenia platformy Azure](/cli/azure).
