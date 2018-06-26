---
title: Przekazanie niestandardowego dysku Linux 2.0 interfejsu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Tworzenie i przekazywanie wirtualnego dysku twardego (VHD) na platformie Azure przy użyciu modelu wdrażania usługi Resource Manager i 2.0 interfejsu wiersza polecenia platformy Azure
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
ms.openlocfilehash: ccdeb2e57d8e2f8fc7ad2fa5e76726b90c0c8a50
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936823"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Przekazywanie i Utwórz Maszynę wirtualną systemu Linux na podstawie niestandardowych dysku 2.0 interfejsu wiersza polecenia platformy Azure
W tym artykule przedstawiono sposób przekazywania wirtualnego dysku twardego (VHD) do konta magazynu Azure, Azure CLI 2.0 i Tworzenie maszyn wirtualnych systemu Linux z tego dysku niestandardowych. Ta funkcja pozwala zainstalować i skonfigurować distro Linux do własnych potrzeb, a następnie użyć tego wirtualnego dysku twardego do szybkiego tworzenia maszyn wirtualnych platformy Azure (maszyny wirtualne).

W tym temacie używa konta magazynu dla końcowego wirtualne dyski twarde, ale można też wykonać te czynności przy użyciu [dyskach zarządzanych](upload-vhd.md). 

## <a name="quick-commands"></a>Szybkie polecenia
Jeśli chcesz szybko wykonać zadanie, następujące szczegóły sekcji bazie polecenia do przekazania dysku VHD na platformę Azure. Bardziej szczegółowe informacje i kontekst dla każdego kroku można znaleźć pozostałej części dokumentu, [uruchamiania tutaj](#requirements).

Upewnij się, że masz najnowszą [Azure CLI 2.0](/cli/azure/install-az-cli2) zainstalowane i zalogowany do konta platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login).

W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametrów uwzględnione `myResourceGroup`, `mystorageaccount`, i `mydisks`.

Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Poniższy przykład tworzy grupę zasobów o nazwie `myResourceGroup` w `WestUs` lokalizacji:

```azurecli
az group create --name myResourceGroup --location westus
```

Utwórz konto magazynu do przechowywania dysków wirtualnych z [Tworzenie konta magazynu az](/cli/azure/storage/account#az_storage_account_create). Poniższy przykład tworzy konto magazynu o nazwie `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Utwórz listę kluczy dostępu dla konta magazynu z [listy kluczy konta magazynu az](/cli/azure/storage/account/keys#az_storage_account_keys_list). Zanotuj `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Utwórz kontener w ramach konta magazynu przy użyciu klucza magazynu uzyskanych z [utworzyć kontenera magazynu az](/cli/azure/storage/container#az_storage_container_create). Poniższy przykład tworzy kontener o nazwie `mydisks` przy użyciu magazynu kluczy wartości `key1`:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Na koniec, Przekaż dysk VHD do kontenera zostały utworzone z [az magazynu obiektów blob przekazywania](/cli/azure/storage/blob#az_storage_blob_upload). Określ ścieżkę lokalną na dysk VHD w obszarze `/path/to/disk/mydisk.vhd`:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Określ identyfikator URI do dysku (`--image`) z [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create). Poniższy przykład tworzy Maszynę wirtualną o nazwie `myVM` przy użyciu dysku wirtualnego wcześniej przekazany:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

Konto magazynu docelowego musi być taka sama jak gdzie przekazany wirtualny dysk. Należy również określić lub odpowiedzi, który wyświetla monit o dodatkowe parametry wymagane przez **tworzenia maszyny wirtualnej az** polecenia, takich jak sieć wirtualną, publiczny adres IP, nazwę użytkownika i kluczy SSH. Możesz przeczytać dodatkowe informacje [dostępne parametry interfejsu wiersza polecenia Menedżera zasobów](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Wymagania
Aby wykonać następujące kroki, potrzebne są:

* **Linux zainstalowanego systemu operacyjnego w pliku VHD** -Zainstaluj [dystrybucji zatwierdzone na platformie Azure Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (lub zobacz [informacje dotyczące niezatwierdzonych dystrybucji](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) do dysku wirtualnego w formacie VHD . Istnieje wiele narzędzi do tworzenia maszyny Wirtualnej i wirtualnego dysku twardego:
  * Instalowanie i konfigurowanie [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) lub [KVM](http://www.linux-kvm.org/page/RunningKVM), zwracając szczególną uwagę na w formacie VHD obrazu. W razie potrzeby można [Konwertuj obraz](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) przy użyciu `qemu-img convert`.
  * Można także użyć funkcji Hyper-V [w systemie Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) lub [w systemie Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Nowszy format VHDX nie jest obsługiwane na platformie Azure. Podczas tworzenia maszyny Wirtualnej, określ dysk VHD jak format. W razie potrzeby możesz przekonwertować dysków VHDX na dysku VHD za pomocą [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) lub [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) polecenia cmdlet programu PowerShell. Ponadto Azure nie obsługuje przekazywania dynamicznych wirtualnych dysków twardych, dlatego należy przekonwertować takie dyski wirtualne dyski twarde statycznej przed przekazaniem. Można użyć narzędzia, takie jak [Azure dysk VHD narzędzia dla Przejdź](https://github.com/Microsoft/azure-vhd-utils-for-go) przekonwertować dyski dynamiczne podczas procesu przekazywania na platformie Azure.
> 
> 

* Maszyny wirtualne utworzone na podstawie niestandardowych dysku musi znajdować się w tym samym kontem magazynu samego dysku
  * Tworzenie konta magazynu i kontener do przechowywania zarówno niestandardowego dysku, jak i maszyny wirtualne utworzone
  * Po utworzeniu wszystkich maszyn wirtualnych, można bezpiecznie usunąć dysku

Upewnij się, że masz najnowszą [Azure CLI 2.0](/cli/azure/install-az-cli2) zainstalowane i zalogowany do konta platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login).

W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametrów uwzględnione `myResourceGroup`, `mystorageaccount`, i `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Przygotowywanie dysku do przekazania
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
> [Umowy SLA platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) ma zastosowanie do maszyn wirtualnych z systemem Linux tylko wtedy, gdy jeden z potwierdzony dystrybucji jest używany z szczegóły konfiguracji określone w obsługiwanych wersjach w [systemu Linux na zatwierdzone na platformie Azure Dystrybucje](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupy zasobów logicznie zebranie wszystkich zasobów platformy Azure do obsługi maszyn wirtualnych, takie jak sieci wirtualnych i magazynu. Dla grup zasobów więcej informacji, zobacz [omówienie grupy zasobów](../../azure-resource-manager/resource-group-overview.md). Przed przekazaniem dysku niestandardowych i Tworzenie maszyn wirtualnych, należy najpierw utworzyć nową grupę zasobów o [Tworzenie grupy az](/cli/azure/group#az_group_create).

Poniższy przykład tworzy grupę zasobów o nazwie `myResourceGroup` w `westus` lokalizacji:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz konto magazynu dla niestandardowego dysku i maszyn wirtualnych o [Tworzenie konta magazynu az](/cli/azure/storage/account#az_storage_account_create). Wszystkie maszyny wirtualne z dyskami niezarządzane, tworzonych z Twojego dysku niestandardowych muszą być w tym samym kontem magazynu tego dysku. 

Poniższy przykład tworzy konto magazynu o nazwie `mystorageaccount` w utworzoną wcześniej grupę zasobów:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Wyświetl klucze konta magazynu
Platforma Azure generuje dwa klucze dostępu 512-bitowe dla każdego konta magazynu. Klawisze dostępu są używane podczas uwierzytelniania na konto magazynu, takich jak do przeprowadzania operacji zapisu. Przeczytaj więcej na temat [zarządzanie dostępem do magazynu w tym miejscu](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Wyświetlanie klawiszy dostępu za pomocą [listy kluczy konta magazynu az](/cli/azure/storage/account/keys#az_storage_account_keys_list).

Wyświetl klucze dostępu dla konta magazynu utworzone:

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
Zanotuj `key1` jako użyje do interakcji z konta magazynu w następnych krokach.

## <a name="create-a-storage-container"></a>Tworzenie kontenera magazynu
W taki sam sposób jak utworzyć różne katalogów w celu logicznego uporządkowania lokalnego systemu plików możesz utworzyć kontenery w ramach konta magazynu, aby organizować dyski. Konto magazynu może zawierać dowolną liczbę kontenerów. Tworzenie kontenera z [utworzyć kontenera magazynu az](/cli/azure/storage/container#az_storage_container_create).

Poniższy przykład tworzy kontener o nazwie `mydisks`:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Przekazywanie wirtualnego dysku twardego
Teraz Przekaż dysku niestandardowych z [az magazynu obiektów blob przekazywania](/cli/azure/storage/blob#az_storage_blob_upload). Przekazywanie i przechowywać dysku niestandardowych jako stronicowy obiekt blob.

Określ klucz dostępu do kontenera, utworzony w poprzednim kroku, a następnie ścieżkę do niestandardowego dysku na komputerze lokalnym:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej
Aby utworzyć Maszynę wirtualną z dyskami niezarządzane, określ identyfikator URI do dysku (`--image`) z [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create). Poniższy przykład tworzy Maszynę wirtualną o nazwie `myVM` przy użyciu dysku wirtualnego wcześniej przekazany:

Należy określić `--image` parametr o [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) wskaż dysku niestandardowych. Upewnij się, że `--storage-account` zgodne z kontem magazynu przechowywania niestandardowych dysku. Nie trzeba używać tego samego kontenera jako niestandardowego dysku do przechowywania maszyn wirtualnych. Upewnij się, że tworzenie żadnych dodatkowych kontenerów w taki sam sposób jak w poprzednich krokach przed przekazaniem dysku niestandardowych.

Poniższy przykład tworzy Maszynę wirtualną o nazwie `myVM` z dysku niestandardowych:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Należy określić nadal lub odpowiedzi, który wyświetla monit o dodatkowe parametry wymagane przez **tworzenia maszyny wirtualnej az** polecenia takie jak nazwa użytkownika i kluczy SSH.


## <a name="resource-manager-template"></a>Szablon usługi Resource Manager
Szablony usługi Azure Resource Manager są plików JavaScript Object Notation (JSON), które definiują środowiska, które chcesz skompilować. Szablony są dzielone w dół dostawców różnych zasobów, takich jak obliczeń lub sieci. Możesz użyć istniejących szablonów lub napisać własny. Przeczytaj więcej na temat [przy użyciu usługi Resource Manager i szablonów](../../azure-resource-manager/resource-group-overview.md).

W ramach `Microsoft.Compute/virtualMachines` dostawcy szablonu, masz `storageProfile` węzła, który zawiera szczegóły konfiguracji dla maszyny Wirtualnej. Są dwa parametry głównym, aby edytować `image` i `vhd` identyfikatorów URI wskaż niestandardowego dysku i dysk wirtualny nowej maszyny Wirtualnej. Poniżej przedstawiono przykład JSON dla przy użyciu niestandardowego dysku:

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

Można użyć [tego istniejącego szablonu, aby utworzyć Maszynę wirtualną z obrazu niestandardowego](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) lub przeczytaj o [Tworzenie szablonów usługi Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 

Po utworzeniu szablonu skonfigurowanego użyj [Utwórz wdrożenie grupy az](/cli/azure/group/deployment#az_group_deployment_create) do tworzenia maszyn wirtualnych. Określ identyfikator URI szablonu JSON z `--template-uri` parametru:

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
Po przygotowane i przekazać niestandardowe dysku wirtualnego, można uzyskać więcej informacji [przy użyciu usługi Resource Manager i szablonów](../../azure-resource-manager/resource-group-overview.md). Warto również [Dodaj dysk danych](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) do nowych maszyn wirtualnych. Jeśli masz aplikacje działające na które należy uzyskać dostęp do maszyn wirtualnych, należy koniecznie [Otwórz porty i punkty końcowe](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

