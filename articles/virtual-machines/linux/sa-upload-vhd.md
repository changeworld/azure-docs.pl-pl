---
title: Przekazywanie niestandardowego dysku systemu Linux przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Tworzenie i przekazywanie wirtualnego dysku twardego (VHD) na platformie Azure przy użyciu modelu wdrażania usługi Resource Manager i interfejsu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: 368fec658dd1f063c45f3d00d42a4549ca9dfd83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771162"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Przekazywanie i tworzenie maszyny Wirtualnej z systemem Linux z niestandardowego dysku przy użyciu wiersza polecenia platformy Azure

W tym artykule pokazano, jak przekazać wirtualny dysk twardy (VHD) do konta usługi Azure storage przy użyciu wiersza polecenia platformy Azure i Tworzenie maszyn wirtualnych systemu Linux z niestandardowego dysku. Ta funkcja pozwala zainstalować i skonfigurować dystrybucja systemu Linux, wymagań, a następnie użyć tego wirtualnego dysku twardego do szybkiego tworzenia maszyn wirtualnych (VM).

Ten temat używa konta magazynu dla końcowego wirtualnych dysków twardych, ale można również wykonać te kroki przy użyciu [usługi managed disks](upload-vhd.md). 

## <a name="quick-commands"></a>Szybkie polecenia
Jeśli chcesz szybko wykonać zadania w poniższej sekcji opisano szczegółowo base polecenia do przekazania dysku VHD na platformie Azure. Więcej szczegółowych informacji i kontekst dla każdego kroku można znaleźć pozostałej części dokumentu, [uruchamianie tutaj](#requirements).

Upewnij się, że masz najnowszy [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowane i zalogować się do konta platformy Azure przy użyciu [az login](/cli/azure/reference-index).

W poniższych przykładach należy zastąpić własnymi wartościami przykładowe nazwy parametru. Przykładowe nazwy parametru uwzględnione `myResourceGroup`, `mystorageaccount`, i `mydisks`.

Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Utwórz konto magazynu do przechowywania wirtualnych dysków z [Tworzenie konta magazynu az](/cli/azure/storage/account). Poniższy przykład tworzy konto magazynu o nazwie `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Utwórz listę kluczy dostępu do konta magazynu przy użyciu [listy kluczy kont magazynu az](/cli/azure/storage/account/keys). Zwróć uwagę na `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Utwórz kontener na koncie magazynu przy użyciu klucza magazynu uzyskanych dzięki [utworzyć kontenera magazynu az](/cli/azure/storage/container). Poniższy przykład tworzy kontener o nazwie `mydisks` przy użyciu wartości klucza magazynu z `key1`:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Na koniec Przekaż wirtualnego dysku twardego do kontenera, który został utworzony z [az storage blob upload](/cli/azure/storage/blob). Określ ścieżkę lokalną do wirtualnego dysku twardego w ramach `/path/to/disk/mydisk.vhd`:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Określ identyfikator URI na dysku (`--image`) za pomocą [tworzenie az vm](/cli/azure/vm). Poniższy przykład tworzy Maszynę wirtualną o nazwie `myVM` przy użyciu wirtualnego dysku został wcześniej przekazany:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

Docelowe konto magazynu musi być taka sama jak którego został przekazany do wirtualnego dysku. Należy także określić lub odpowiedzi, który monituje o podanie dodatkowych parametrów wymaganych przez **tworzenie az vm** polecenia takie jak sieć wirtualną, publiczny adres IP, nazwy użytkownika i kluczy SSH. Przeczytaj więcej o [dostępnych parametrów interfejs wiersza polecenia Menedżera zasobów](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Wymagania
Aby wykonać następujące kroki, potrzebne są:

* **System operacyjny Linux zainstalowane w pliku VHD** -Zainstaluj [dystrybucji systemu Linux z zatwierdzone na platformie Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (lub zobacz [informacje dotyczące niezatwierdzonych dystrybucji](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) do dysku wirtualnego w formacie VHD . Istnieje wiele narzędzi do tworzenia maszyny Wirtualnej i wirtualnego dysku twardego:
  * Instalowanie i konfigurowanie [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) lub [KVM](https://www.linux-kvm.org/page/RunningKVM), zwracając szczególną uwagę na w formacie VHD obrazu. Jeśli to konieczne, możesz [Konwertowanie obrazu](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) przy użyciu `qemu-img convert`.
  * Możesz również użyć funkcji Hyper-V [w systemie Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) lub [w systemie Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Nowszy format VHDX nie jest obsługiwane na platformie Azure. Podczas tworzenia maszyny Wirtualnej należy określić wirtualnego dysku twardego w formacie. Jeśli to konieczne, można przekonwertować dysków VHDX na dysku VHD za pomocą [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) lub [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) polecenia cmdlet programu PowerShell. Ponadto Azure nie obsługuje przekazywania dynamicznych wirtualnych dysków twardych, więc musisz przekonwertować te dyski do statycznego wirtualnych dysków twardych przed przekazaniem. Użyj narzędzi takich jak [narzędzia wirtualnego dysku twardego platformy Azure dla języka GO](https://github.com/Microsoft/azure-vhd-utils-for-go) przekonwertować dysków dynamicznych w procesie przekazywania na platformie Azure.
> 
> 

* Maszyny wirtualne utworzone na podstawie niestandardowego dysku musi znajdować się na tym samym koncie magazynu jako dysku
  * Tworzenie konta magazynu i kontener do przechowywania zarówno niestandardowego dysku, jak i utworzonych maszyn wirtualnych
  * Po utworzeniu wszystkich maszyn wirtualnych, można bezpiecznie usunąć dysku

Upewnij się, że masz najnowszy [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowane i zalogować się do konta platformy Azure przy użyciu [az login](/cli/azure/reference-index).

W poniższych przykładach należy zastąpić własnymi wartościami przykładowe nazwy parametru. Przykładowe nazwy parametru uwzględnione `myResourceGroup`, `mystorageaccount`, i `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Przygotuj dysk do przekazania
Platforma Azure obsługuje różne dystrybucje systemu Linux (zobacz [dystrybucje zatwierdzone dla](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Następujące artykuły przeprowadzą Cię przez proces przygotować różne dystrybucje systemu Linux, które są obsługiwane na platformie Azure:

* **[Dystrybucje systemu centOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Inne — niezatwierdzonych dystrybucji](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Zobacz też **[uwagi dotyczące instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes)** bardziej ogólne porady na temat przygotowywania obrazów systemu Linux na platformie Azure.

> [!NOTE]
> [Platformy Azure, umowa SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) dotyczy maszyn wirtualnych z systemem Linux, tylko wtedy, gdy jeden z zalecanych dystrybucji jest używany przy użyciu szczegółów konfiguracji, jak określono w obsługiwanych wersjach [systemu Linux na zatwierdzone na platformie Azure Dystrybucje](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupy zasobów łączą ze sobą logicznie wszystkich zasobów platformy Azure do obsługi maszyn wirtualnych, takie jak sieci wirtualne i magazyn. Aby uzyskać więcej informacji o grupach zasobów, zobacz [grupy zasobów — omówienie](../../azure-resource-manager/resource-group-overview.md). Przed przekazaniem niestandardowego dysku i Tworzenie maszyn wirtualnych, należy najpierw utworzyć grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group).

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Tworzenie konta magazynu dla niestandardowego dysku i maszyn wirtualnych przy użyciu [Tworzenie konta magazynu az](/cli/azure/storage/account). Wszystkie maszyny wirtualne z dyskami niezarządzanymi, tworzonych za pomocą niestandardowego dysku konieczności znajdować się w tym samym koncie magazynu jako dysku. 

Poniższy przykład tworzy konto magazynu o nazwie `mystorageaccount` w utworzoną wcześniej grupę zasobów:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Wyświetl klucze konta magazynu
Azure generuje dwa klucze dostępu 512-bitowe dla każdego konta magazynu. Klawisze dostępu są używane podczas uwierzytelniania konta magazynu, takich jak przeprowadzanie operacji zapisu. Przeczytaj więcej na temat [zarządzanie dostępem do magazynu w tym miejscu](../../storage/common/storage-account-manage.md#access-keys). Wyświetl klucze dostępu za pomocą [listy kluczy kont magazynu az](/cli/azure/storage/account/keys).

Wyświetl klucze dostępu dla konta magazynu, który został utworzony:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
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
Zwróć uwagę na `key1` jako użyje do interakcji z kontem usługi storage w następnych krokach.

## <a name="create-a-storage-container"></a>Utwórz kontener magazynu
Tak samo jak tworzenie różnych katalogów w celu logicznego uporządkowania lokalnego systemu plików służy do tworzenia kontenerów w ramach konta magazynu, aby organizować dyski. Konto magazynu może zawierać dowolną liczbę kontenerów. Utwórz kontener przy użyciu [utworzyć kontenera magazynu az](/cli/azure/storage/container).

Poniższy przykład tworzy kontener o nazwie `mydisks`:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Przekazywanie wirtualnego dysku twardego
Teraz Przekaż Twojego niestandardowego dysku przy użyciu [az storage blob upload](/cli/azure/storage/blob). Przekazywanie i przechowywanie niestandardowego dysku jako stronicowy obiekt blob.

Określ klucz dostępu do kontenera utworzonego w poprzednim kroku, a następnie ścieżkę do niestandardowego dysku na komputerze lokalnym:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej
Aby utworzyć Maszynę wirtualną z dyskami niezarządzanymi, określ identyfikator URI na dysku (`--image`) za pomocą [tworzenie az vm](/cli/azure/vm). Poniższy przykład tworzy Maszynę wirtualną o nazwie `myVM` przy użyciu wirtualnego dysku został wcześniej przekazany:

Należy określić `--image` parametrem [tworzenie az vm](/cli/azure/vm) by wskazywał dysku niestandardowych. Upewnij się, że `--storage-account` pasuje do konta magazynu, w którym przechowywany jest niestandardowy dysku. Nie trzeba używać tego samego kontenera jako niestandardowego dysku do przechowywania maszyn wirtualnych. Upewnij się utworzyć wszystkie dodatkowe kontenery w taki sam sposób jak w poprzednich krokach przed przekazaniem dysku niestandardowych.

Poniższy przykład tworzy Maszynę wirtualną o nazwie `myVM` z niestandardowego dysku:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Nadal należy określić lub odpowiedzi, który monituje o podanie dodatkowych parametrów wymaganych przez **tworzenie az vm** polecenia takie jak nazwa użytkownika i klucze SSH.


## <a name="resource-manager-template"></a>Szablon usługi Resource Manager
Szablony usługi Azure Resource Manager są plikami JavaScript Object Notation (JSON), które definiują środowisko, które chcesz utworzyć. Szablony są podzielone na inny zasób dostawców, takich jak obliczenia lub sieci. Możesz użyć istniejących szablonów lub napisać własny. Przeczytaj więcej na temat [przy użyciu usługi Resource Manager i szablonów](../../azure-resource-manager/resource-group-overview.md).

W ramach `Microsoft.Compute/virtualMachines` dostawcy szablonu, masz `storageProfile` węzeł, który zawiera szczegóły konfiguracji dla maszyny Wirtualnej. Są dwa parametry główne, aby edytować `image` i `vhd` identyfikatory URI wskazujące niestandardowego dysku oraz dysk wirtualny w nowej maszyny Wirtualnej. Poniżej przedstawiono przykładowy kod JSON przy użyciu niestandardowego dysku:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Możesz użyć [tego istniejącego szablonu, aby utworzyć Maszynę wirtualną na podstawie niestandardowego obrazu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) ani czytać [tworzenia szablonów usługi Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 

Po utworzeniu szablonu, który został skonfigurowany, użyj [Utwórz wdrożenie grupy az](/cli/azure/group/deployment) do tworzenia maszyn wirtualnych. Określ identyfikator URI szablonu JSON za pomocą `--template-uri` parametru:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Jeśli masz plik JSON przechowywane lokalnie na komputerze, możesz użyć `--template-file` parametru zamiast tego:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Kolejne kroki
Po przygotować i przekazać niestandardowe wirtualnego dysku, możesz przeczytać więcej na temat [przy użyciu usługi Resource Manager i szablonów](../../azure-resource-manager/resource-group-overview.md). Możesz również chcieć [Dodaj dysk danych](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) do nowych maszyn wirtualnych. Jeśli masz aplikacje uruchomione na maszynach wirtualnych, które chcesz uzyskać dostęp, należy koniecznie [otworzyć porty i punkty końcowe](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

