---
title: Przekazywanie lub skopiować niestandardowe maszyny Wirtualnej systemu Linux 2.0 interfejsu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Przekazywanie lub skopiowanie dostosowane maszyny wirtualnej przy użyciu modelu wdrażania usługi Resource Manager i 2.0 interfejsu wiersza polecenia platformy Azure
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
ms.date: 07/06/2017
ms.author: cynthn
ms.openlocfilehash: fc3d72ace6398b69a5efa5543c590bba166baaf0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Utwórz Maszynę wirtualną systemu Linux na podstawie niestandardowych dysku 2.0 interfejsu wiersza polecenia platformy Azure

<!-- rename to create-vm-specialized -->

W tym artykule przedstawiono sposób przekazywanie dostosowanych wirtualnego dysku twardego (VHD) lub kopii istniejącego dysku VHD na platformie Azure i utworzyć nowe maszyny wirtualne systemu Linux (VM) na dysku niestandardowych. Można zainstalować i skonfigurować Linux distro do własnych potrzeb i następnie użyć tego wirtualnego dysku twardego do szybkiego tworzenia nowej maszyny wirtualnej platformy Azure.

Jeśli chcesz utworzyć wiele maszyn wirtualnych z dysku dostosowane, należy utworzyć obraz z maszyny Wirtualnej lub wirtualnego dysku twardego. Aby uzyskać więcej informacji, zobacz [utworzyć niestandardowy obraz maszyny Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia](tutorial-custom-images.md).

Dostępne są dwie opcje:
* [Przekazywanie wirtualnego dysku twardego](#option-1-upload-a-specialized-vhd)
* [Skopiuj istniejącej maszyny Wirtualnej Azure](#option-2-copy-an-existing-azure-vm)

## <a name="quick-commands"></a>Szybkie polecenia

Podczas tworzenia nowej maszyny Wirtualnej przy użyciu [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) z dysku dostosowane lub specjalne możesz **dołączyć** dysku (— attach-os-disk) zamiast określania obraz niestandardowy lub witryny marketplace (— obrazu). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* przy użyciu dysków zarządzanych o nazwie *myManagedDisk* utworzone na podstawie niestandardowych dysk VHD:

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>Wymagania
Aby wykonać następujące kroki, potrzebne są:

* Maszyny wirtualnej systemu Linux, który został przygotowany do użycia na platformie Azure. [Przygotowanie wirtualna](#prepare-the-vm) części w tym artykule opisano sposób znajdowania distro określonych informacji na temat instalowania agenta systemu Linux platformy Azure (agenta waagent), który jest wymagany dla maszyny Wirtualnej, aby działać poprawnie na platformie Azure i musisz mieć możliwość nawiązania połączenia przy użyciu protokołu SSH.
* Plik wirtualnego dysku twardego z istniejącego [dystrybucji zatwierdzone na platformie Azure Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (lub zobacz [informacje dotyczące niezatwierdzonych dystrybucji](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) do wirtualnego dysku w formacie VHD. Istnieje wiele narzędzi do tworzenia maszyny Wirtualnej i wirtualnego dysku twardego:
  * Instalowanie i konfigurowanie [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) lub [KVM](http://www.linux-kvm.org/page/RunningKVM), zwracając szczególną uwagę na w formacie VHD obrazu. W razie potrzeby można [Konwertuj obraz](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) przy użyciu **przekonwertować qemu img**.
  * Można także użyć funkcji Hyper-V [w systemie Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) lub [w systemie Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Nowszy format VHDX nie jest obsługiwane na platformie Azure. Podczas tworzenia maszyny Wirtualnej, określ dysk VHD jak format. W razie potrzeby możesz przekonwertować dysków VHDX na dysku VHD za pomocą [przekonwertować qemu img](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) lub [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) polecenia cmdlet programu PowerShell. Ponadto Azure nie obsługuje przekazywania dynamicznych wirtualnych dysków twardych, dlatego należy przekonwertować takie dyski wirtualne dyski twarde statycznej przed przekazaniem. Można użyć narzędzia, takie jak [Azure dysk VHD narzędzia dla Przejdź](https://github.com/Microsoft/azure-vhd-utils-for-go) przekonwertować dyski dynamiczne podczas procesu przekazywania na platformie Azure.
> 
> 


* Upewnij się, że masz najnowszą [Azure CLI 2.0](/cli/azure/install-az-cli2) zainstalowane i zalogowany do konta platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login).

W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametrów uwzględnione *myResourceGroup*, *mojekontomagazynu*, i *mydisks*.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej

Azure obsługuje różne dystrybucje systemu Linux (zobacz [dystrybucje zatwierdzone](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Następujące artykuły przedstawiono sposób przygotowania różnych dystrybucje systemu Linux, które są obsługiwane na platformie Azure:

* [Na podstawie centOS dystrybucji](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian systemu Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Inne — niezatwierdzonych dystrybucji](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Zobacz też [informacje o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) bardziej ogólne porady dotyczące przygotowywania obrazów systemu Linux na platformie Azure.

> [!NOTE]
> [Umowy SLA platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) ma zastosowanie do maszyn wirtualnych z systemem Linux tylko wtedy, gdy jeden z potwierdzony dystrybucji jest używany z szczegóły konfiguracji określone w obsługiwanych wersjach w [systemu Linux na dystrybucje Azure-Endorsed](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Opcja 1: Przekazywanie wirtualnego dysku twardego

Możesz przekazać dostosowane wirtualnego dysku twardego uruchomione na komputerze lokalnym lub wyeksportowany z innej chmury. Aby użyć wirtualnego dysku twardego do tworzenia nowej maszyny Wirtualnej Azure, należy przekazanie dysku VHD na konto magazynu i Utwórz dysków zarządzanych z dysku VHD. 

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed przekazaniem dysku niestandardowych i Tworzenie maszyn wirtualnych, należy najpierw utworzyć nową grupę zasobów o [Tworzenie grupy az](/cli/azure/group#az_group_create).

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *eastus* lokalizacji: [dysków zarządzanych Azure — omówienie](../windows/managed-disks-overview.md)
```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz konto magazynu dla niestandardowego dysku i maszyn wirtualnych o [Tworzenie konta magazynu az](/cli/azure/storage/account#az_storage_account_create). 

Poniższy przykład tworzy konto magazynu o nazwie *mojekontomagazynu* w utworzoną wcześniej grupę zasobów:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>Wyświetl klucze konta magazynu
Platforma Azure generuje dwa klucze dostępu 512-bitowe dla każdego konta magazynu. Klawisze dostępu są używane podczas uwierzytelniania na konto magazynu, takich jak przeprowadzanie operacji zapisu. Przeczytaj więcej na temat [zarządzanie dostępem do magazynu w tym miejscu](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Wyświetlanie klawiszy dostępu za pomocą [listy kluczy konta magazynu az](/cli/azure/storage/account/keys#az_storage_account_keys_list).

Wyświetl klucze dostępu dla konta magazynu utworzone:

```azurecli
az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount
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
Zanotuj **klucz1** jako użyje do interakcji z konta magazynu w następnych krokach.

### <a name="create-a-storage-container"></a>Tworzenie kontenera magazynu
W taki sam sposób jak utworzyć różne katalogów w celu logicznego uporządkowania lokalnego systemu plików możesz utworzyć kontenery w ramach konta magazynu, aby organizować dyski. Konto magazynu może zawierać dowolną liczbę kontenerów. Tworzenie kontenera z [utworzyć kontenera magazynu az](/cli/azure/storage/container#az_storage_container_create).

Poniższy przykład tworzy kontener o nazwie *mydisks*:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>Przekazywanie wirtualnego dysku twardego
Teraz Przekaż dysku niestandardowych z [az magazynu obiektów blob przekazywania](/cli/azure/storage/blob#az_storage_blob_upload). Przekazywanie i przechowywać dysku niestandardowych jako stronicowy obiekt blob.

Określ klucz dostępu do kontenera, utworzony w poprzednim kroku, a następnie ścieżkę do niestandardowego dysku na komputerze lokalnym:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 \
    --container-name mydisks \
    --type page \
    --file /path/to/disk/mydisk.vhd \
    --name myDisk.vhd
```
Przekazywanie wirtualnego dysku twardego może chwilę potrwać.

### <a name="create-a-managed-disk"></a>Tworzenie dysku zarządzanego


Tworzenie dysku zarządzanego z wirtualnego dysku twardego przy użyciu [Tworzenie dysku az](/cli/azure/disk#az_disk_create). Poniższy przykład tworzy zarządzanych dysk o nazwie *myManagedDisk* z wirtualnego dysku twardego przekazany do konta magazynu o nazwie i kontener:

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>Opcja 2: Kopiowanie istniejącej maszyny Wirtualnej

Można również utworzyć niestandardowe maszynę Wirtualną na platformie Azure, a następnie skopiować dysk systemu operacyjnego i dołączenie go do nowej maszyny Wirtualnej, aby utworzyć kolejną kopię. Jest to testowania poprawnie, ale jeśli chcesz użyć istniejącej maszyny Wirtualnej platformy Azure jako model dla wielu maszyn wirtualnych, naprawdę należy utworzyć **obrazu** zamiast tego. Aby uzyskać więcej informacji o tworzeniu obrazu z istniejącej maszyny Wirtualnej Azure, zobacz [utworzyć niestandardowy obraz maszyny Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia](tutorial-custom-images.md)

### <a name="create-a-snapshot"></a>Utwórz migawkę

W tym przykładzie tworzy migawkę maszyny wirtualnej o nazwie *myVM* w grupie zasobów *myResourceGroup* i tworzy migawkę o nazwie *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Tworzenie dysków zarządzanych

Utwórz nowy dysk zarządzanego z migawki.

Pobierz identyfikator migawki. W tym przykładzie migawki o nazwie *osDiskSnapshot* i znajduje się w *myResourceGroup* grupy zasobów.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Tworzenie dysku zarządzanego. W tym przykładzie utworzysz zarządzanych dysk o nazwie *myManagedDisk* z naszych migawki to 128 GB w rozmiarze w magazynu w warstwie standardowa.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Teraz utworzyć maszyny Wirtualnej z [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) i Dołącz (--attach-os-disk) dysków zarządzanych jako dysk systemu operacyjnego. Poniższy przykład tworzy Maszynę wirtualną o nazwie *myNewVM* przy użyciu dysków zarządzanych utworzone na podstawie przekazanego dysk VHD:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Można na SSH do maszyny Wirtualnej ze źródłowej maszyny Wirtualnej przy użyciu poświadczeń. 

## <a name="next-steps"></a>Kolejne kroki
Po przygotowane i przekazać niestandardowe dysku wirtualnego, można uzyskać więcej informacji [przy użyciu usługi Resource Manager i szablonów](../../azure-resource-manager/resource-group-overview.md). Warto również [Dodaj dysk danych](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) do nowych maszyn wirtualnych. Jeśli masz aplikacje działające na które należy uzyskać dostęp do maszyn wirtualnych, należy koniecznie [Otwórz porty i punkty końcowe](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

