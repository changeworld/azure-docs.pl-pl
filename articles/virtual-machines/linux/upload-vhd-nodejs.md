---
title: Przekaż obraz niestandardowy Linux z interfejsu wiersza polecenia platformy Azure w wersji 1.0 | Dokumentacja firmy Microsoft
description: Tworzenie i przekazywanie wirtualnego dysku twardego (VHD) na platformie Azure przy użyciu niestandardowego obrazu systemu Linux przy użyciu modelu wdrażania usługi Resource Manager i interfejsu wiersza polecenia platformy Azure w wersji 1.0.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: iainfou
ms.openlocfilehash: 6eb0cae2b70e0cbb9a4fb5fcab3a58d566d0f4d9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915314"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-image-by-using-the-azure-cli-10"></a>Przekazywanie i Utwórz Maszynę wirtualną systemu Linux na podstawie obrazu niestandardowego dysku przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure
W tym artykule przedstawiono sposób przekazywania wirtualnego dysku twardego (VHD) na platformie Azure przy użyciu modelu wdrażania usługi Resource Manager i Tworzenie maszyn wirtualnych systemu Linux z tego obrazu niestandardowego. Ta funkcja pozwala zainstalować i skonfigurować distro Linux do własnych potrzeb, a następnie użyć tego wirtualnego dysku twardego do szybkiego tworzenia maszyn wirtualnych platformy Azure (maszyny wirtualne).


## <a name="cli-versions-to-complete-the-task"></a>Wersje interfejsu wiersza polecenia umożliwiające wykonanie zadania
Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

- [Azure CLI 1.0](#quick-commands) — nasze interfejsu wiersza polecenia dla klasycznego i zasobów zarządzania wdrażania modeli (w tym artykule)
- [Interfejs wiersza polecenia platformy Azure w wersji 2.0](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — nasz interfejs wiersza polecenia nowej generacji dla modelu wdrażania na potrzeby zarządzania zasobami


## <a name="quick-commands"></a>Szybkie polecenia
Jeśli chcesz szybko wykonać zadanie, następujące szczegóły sekcji bazie polecenia do przekazania Maszynę wirtualną na platformie Azure. Bardziej szczegółowe informacje i kontekst dla każdego kroku można znaleźć pozostałej części dokumentu, [uruchamiania tutaj](#requirements).

Upewnij się, że masz [1.0 interfejsu wiersza polecenia Azure](../../cli-install-nodejs.md) zalogowany i w trybie Menedżera zasobów:

```azurecli
azure config mode arm
```

W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametrów uwzględnione `myResourceGroup`, `mystorageaccount`, i `myimages`.

Najpierw utwórz grupę zasobów. Poniższy przykład tworzy grupę zasobów o nazwie `myResourceGroup` w `WestUs` lokalizacji:

```azurecli
azure group create myResourceGroup --location "WestUS"
```

Utwórz konto magazynu do przechowywania dysków wirtualnych. Poniższy przykład tworzy konto magazynu o nazwie `mystorageaccount`:

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

Wyświetl klucze dostępu dla konta magazynu. Zanotuj `key1`:

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Utwórz kontener w ramach konta magazynu przy użyciu klucza magazynu, który został uzyskany. Poniższy przykład tworzy kontener o nazwie `myimages` przy użyciu magazynu kluczy wartości `key1`:

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

Na koniec należy przekazać dysk VHD do kontenera, który został utworzony. Określ ścieżkę lokalną na dysk VHD w obszarze `/path/to/disk/mydisk.vhd`:

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

Teraz można utworzyć maszyny Wirtualnej z dysku wirtualnego przekazane [przy użyciu szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd). Umożliwia także interfejsu wiersza polecenia, określając identyfikator URI do dysku (`--image-urn`). Poniższy przykład tworzy Maszynę wirtualną o nazwie `myVM` przy użyciu dysku wirtualnego wcześniej przekazany:

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

Konto magazynu docelowego musi być taka sama jak gdzie przekazany wirtualny dysk. Należy również określić lub odpowiedzi, który wyświetla monit o dodatkowe parametry wymagane przez `azure vm create` polecenia, takich jak sieć wirtualną, publiczny adres IP, nazwę użytkownika i kluczy SSH. Możesz przeczytać dodatkowe informacje [dostępne parametry interfejsu wiersza polecenia Menedżera zasobów](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Wymagania
Aby wykonać następujące kroki, potrzebne są:

* **Linux zainstalowanego systemu operacyjnego w pliku VHD** -Zainstaluj [dystrybucji zatwierdzone na platformie Azure Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (lub zobacz [informacje dotyczące niezatwierdzonych dystrybucji](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) do dysku wirtualnego w formacie VHD . Istnieje wiele narzędzi do tworzenia maszyny Wirtualnej i wirtualnego dysku twardego:
  * Instalowanie i konfigurowanie [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) lub [KVM](http://www.linux-kvm.org/page/RunningKVM), zwracając szczególną uwagę na w formacie VHD obrazu. W razie potrzeby można [Konwertuj obraz](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) przy użyciu `qemu-img convert`.
  * Można także użyć funkcji Hyper-V [w systemie Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) lub [w systemie Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Nowszy format VHDX nie jest obsługiwane na platformie Azure. Podczas tworzenia maszyny Wirtualnej, określ dysk VHD jak format. W razie potrzeby możesz przekonwertować dysków VHDX na dysku VHD za pomocą [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) lub [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) polecenia cmdlet programu PowerShell. Ponadto Azure nie obsługuje przekazywania dynamicznych wirtualnych dysków twardych, dlatego należy przekonwertować takie dyski wirtualne dyski twarde statycznej przed przekazaniem. Można użyć narzędzia, takie jak [Azure dysk VHD narzędzia dla Przejdź](https://github.com/Microsoft/azure-vhd-utils-for-go) przekonwertować dyski dynamiczne podczas procesu przekazywania na platformie Azure.
> 
> 

* Maszyny wirtualne utworzone na podstawie niestandardowego obrazu musi znajdować się w tym samym kontem magazynu samego obrazu
  * Tworzenie konta magazynu i kontener do przechowywania obu niestandardowego obrazu i utworzone maszyny wirtualne
  * Po utworzeniu wszystkich maszyn wirtualnych, można bezpiecznie usunąć obrazu

Upewnij się, że masz [1.0 interfejsu wiersza polecenia Azure](../../cli-install-nodejs.md) zalogowany i w trybie Menedżera zasobów:

```azurecli
azure config mode arm
```

W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametrów uwzględnione `myResourceGroup`, `mystorageaccount`, i `myimages`.

<a id="prepimage"> </a>

## <a name="prepare-the-image-to-be-uploaded"></a>Przygotowanie obrazu do załadowania
Azure obsługuje różne dystrybucje systemu Linux (zobacz [dystrybucje zatwierdzone](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Następujące artykuły przedstawiono sposób przygotowania różnych dystrybucje systemu Linux, które są obsługiwane na platformie Azure:

* **[Na podstawie centOS dystrybucji](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian systemu Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Inne — niezatwierdzonych dystrybucji](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Zobacz też **[informacje o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes)** bardziej ogólne porady dotyczące przygotowywania obrazów systemu Linux na platformie Azure.

> [!NOTE]
> [Umowy SLA platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) ma zastosowanie do maszyn wirtualnych z systemem Linux tylko wtedy, gdy jeden z potwierdzony dystrybucji jest używany z szczegóły konfiguracji określone w obsługiwanych wersjach w [systemu Linux na dystrybucje Azure-Endorsed](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupy zasobów logicznie zebranie wszystkich zasobów platformy Azure do obsługi maszyn wirtualnych, takie jak sieci wirtualnych i magazynu. Przeczytaj więcej na temat [grupy zasobów platformy Azure, w tym miejscu](../../azure-resource-manager/resource-group-overview.md). Przed przekazaniem obrazu niestandardowego dysku i Tworzenie maszyn wirtualnych, należy najpierw utworzyć grupę zasobów. 

Poniższy przykład tworzy grupę zasobów o nazwie `myResourceGroup` w `WestUS` lokalizacji:

```azurecli
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
Maszyny wirtualne są przechowywane jako stronicowe obiekty BLOB w ramach konta magazynu. Przeczytaj więcej na temat [magazynu obiektów blob platformy Azure w tym miejscu](../../storage/common/storage-introduction.md#blob-storage). Możesz utworzyć konto magazynu obrazu niestandardowego dysku i maszyn wirtualnych. Wszystkie maszyny wirtualne utworzone z obrazu niestandardowego dysku muszą być tego samego konta magazynu jako obrazu.

Poniższy przykład tworzy konto magazynu o nazwie `mystorageaccount` w utworzoną wcześniej grupę zasobów:

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>Wyświetl klucze konta magazynu
Platforma Azure generuje dwa klucze dostępu 512-bitowe dla każdego konta magazynu. Klawisze dostępu są używane podczas uwierzytelniania na konto magazynu, takich jak do przeprowadzania operacji zapisu. Przeczytaj więcej na temat [zarządzanie dostępem do magazynu w tym miejscu](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Możesz wyświetlić klawisze dostępu z `azure storage account keys list` polecenia.

Wyświetl klucze dostępu dla konta magazynu utworzone:

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Dane wyjściowe są podobne do następujących:

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK

```
Zanotuj `key1` jako użyje do interakcji z konta magazynu w następnych krokach.

## <a name="create-a-storage-container"></a>Tworzenie kontenera magazynu
W taki sam sposób jak utworzyć różne katalogów w celu logicznego uporządkowania lokalnego systemu plików możesz utworzyć kontenery w ramach konta magazynu, aby organizować dyski wirtualne i obrazów. Konto magazynu może zawierać dowolną liczbę kontenerów. 

Poniższy przykład tworzy kontener o nazwie `myimages`, określania klucza dostępu uzyskanych w poprzednim kroku (`key1`):

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>Przekazywanie wirtualnego dysku twardego
Teraz można faktycznie przekazywanie obrazu niestandardowego dysku. Jako wszystkich wirtualnych dysków używanych przez maszyny wirtualne, należy przekazać i przechowywania obrazu niestandardowego dysku jako stronicowy obiekt blob.

Określ klucz dostępu do kontenera, który został utworzony w poprzednim kroku, a następnie ścieżkę do obrazu niestandardowego dysku na komputerze lokalnym:

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>Tworzenie maszyny Wirtualnej z obrazu niestandardowego
Podczas tworzenia maszyn wirtualnych z obrazu niestandardowego dysku, należy określić identyfikator URI do obrazu dysku. Upewnij się, że zgodne konto magazynu docelowego przechowywania obrazu niestandardowego dysku. Można utworzyć maszyny Wirtualnej przy użyciu szablonu Azure CLI lub JSON Menedżera zasobów.

### <a name="create-a-vm-using-the-azure-cli"></a>Utwórz maszynę Wirtualną przy użyciu wiersza polecenia platformy Azure
Należy określić `--image-urn` parametr `azure vm create` polecenie, aby wskazywał obrazu niestandardowego dysku. Upewnij się, że `--storage-account-name` zgodne z kontem magazynu przechowywania obrazu niestandardowego dysku. Nie trzeba używać tego samego kontenera jako obraz niestandardowy dysku do przechowywania maszyn wirtualnych. Upewnij się utworzyć wszelkie dodatkowe kontenery w taki sam sposób jak w poprzednich krokach przed przekazaniem dysku niestandardowych obrazów.

Poniższy przykład tworzy Maszynę wirtualną o nazwie `myVM` z obrazu niestandardowego dysku:

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

Należy określić nadal lub odpowiedzi, który wyświetla monit o dodatkowe parametry wymagane przez `azure vm create` polecenia, takich jak sieć wirtualną, publiczny adres IP, nazwę użytkownika i kluczy SSH. Przeczytaj więcej na temat [dostępne parametry interfejsu wiersza polecenia Menedżera zasobów](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### <a name="create-a-vm-using-a-json-template"></a>Utwórz maszynę Wirtualną przy użyciu szablonu JSON
Szablony usługi Azure Resource Manager są plików JavaScript Object Notation (JSON), które definiują środowiska, które chcesz skompilować. Szablony są dzielone w dół dostawców różnych zasobów, takich jak obliczeń lub sieci. Możesz użyć istniejących szablonów lub napisać własny. Przeczytaj więcej na temat [przy użyciu usługi Resource Manager i szablonów](../../azure-resource-manager/resource-group-overview.md).

W ramach `Microsoft.Compute/virtualMachines` dostawcy szablonu, masz `storageProfile` węzła, który zawiera szczegóły konfiguracji dla maszyny Wirtualnej. Są dwa parametry głównym, aby edytować `image` i `vhd` identyfikatorów URI wskaż obrazu niestandardowego dysku i dysk wirtualny nowej maszyny Wirtualnej. Poniżej przedstawiono przykład JSON dla przy użyciu obrazu niestandardowego dysku:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Można użyć [tego istniejącego szablonu, aby utworzyć Maszynę wirtualną z obrazu niestandardowego](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) lub przeczytaj o [Tworzenie szablonów usługi Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 

Po utworzeniu szablonu skonfigurowanego tworzenia maszyn wirtualnych przy użyciu `azure group deployment create` polecenia. Określ identyfikator URI szablonu JSON z `--template-uri` parametru:

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

Jeśli masz plik JSON przechowywane lokalnie na komputerze, możesz użyć `--template-file` parametru zamiast tego:

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Kolejne kroki
Po przygotowane i przekazać niestandardowe dysku wirtualnego, można uzyskać więcej informacji [przy użyciu usługi Resource Manager i szablonów](../../azure-resource-manager/resource-group-overview.md). Warto również [Dodaj dysk danych](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) do nowych maszyn wirtualnych. Jeśli masz aplikacje działające na które należy uzyskać dostęp do maszyn wirtualnych, należy koniecznie [Otwórz porty i punkty końcowe](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

