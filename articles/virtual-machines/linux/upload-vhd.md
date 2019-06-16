---
title: Przekazywanie lub skopiuj niestandardową maszynę Wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Przekazywanie lub kopiowanie niestandardową maszynę wirtualną przy użyciu modelu wdrażania usługi Resource Manager i interfejsu wiersza polecenia platformy Azure
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
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: 6483fa8737ee3de6a60c4e4646fefec30ae702b6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61473420"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Tworzenie maszyny Wirtualnej z systemem Linux z niestandardowego dysku przy użyciu wiersza polecenia platformy Azure

<!-- rename to create-vm-specialized -->

W tym artykule dowiesz się, jak przekazać niestandardowe wirtualnego dysku twardego (VHD), a także sposób kopiowania istniejącego wirtualnego dysku twardego na platformie Azure. Nowo utworzony wirtualny dysk twardy jest następnie używany do tworzenia maszyn wirtualnych systemu Linux (VM). Można zainstalować i skonfigurować dystrybucja systemu Linux, wymagań i następnie utworzyć nową maszynę wirtualną platformy Azure za pomocą tego wirtualnego dysku twardego.

Aby utworzyć wiele maszyn wirtualnych z dysku dostosowane, należy najpierw utworzyć obraz z maszyny Wirtualnej lub wirtualnego dysku twardego. Aby uzyskać więcej informacji, zobacz [Tworzenie niestandardowego obrazu maszyny wirtualnej portalu Azure przy użyciu interfejsu wiersza polecenia](tutorial-custom-images.md).

Masz dwie opcje w celu utworzenia niestandardowego dysku:
* Przekazywanie wirtualnego dysku twardego
* Kopiowanie istniejących maszyn wirtualnych platformy Azure

## <a name="quick-commands"></a>Szybkie polecenia

Podczas tworzenia nowej maszyny Wirtualnej za pomocą [tworzenie az vm](/cli/azure/vm#az-vm-create) z dostosowany lub wyspecjalizowanego dysku, możesz **dołączyć** dysku (— dołączanie os-disk) zamiast określania obrazu niestandardowego lub portalu marketplace (— obrazu). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* użycie dysku zarządzanego, o nazwie *myManagedDisk* utworzony z dostosowanego wirtualnego dysku twardego:

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>Wymagania
Aby wykonać następujące kroki, będą potrzebne:

* Maszyny wirtualnej systemu Linux, który został przygotowany do użytku na platformie Azure. [Przygotować maszynę Wirtualną](#prepare-the-vm) dalszej części tego artykułu opisano, jak znaleźć informacje specyficzne dla dystrybucji na temat instalowania agenta systemu Linux platformy Azure (waagent), który jest niezbędny do nawiązania połączenia z maszyną wirtualną przy użyciu protokołu SSH.
* Plik wirtualnego dysku twardego z istniejącego [dystrybucji systemu Linux z zatwierdzone na platformie Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (lub zobacz [informacje dotyczące niezatwierdzonych dystrybucji](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) do dysku wirtualnego w formacie VHD. Istnieje wiele narzędzi do tworzenia maszyny Wirtualnej i wirtualnego dysku twardego:
  * Instalowanie i konfigurowanie [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) lub [KVM](https://www.linux-kvm.org/page/RunningKVM), zwracając szczególną uwagę na w formacie VHD obrazu. Jeśli to konieczne, możesz [Konwertowanie obrazu](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) z `qemu-img convert`.
  * Możesz również użyć funkcji Hyper-V [w systemie Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) lub [w systemie Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Nowszy format VHDX nie jest obsługiwane na platformie Azure. Podczas tworzenia maszyny Wirtualnej należy określić wirtualnego dysku twardego w formacie. Jeśli to konieczne, można przekonwertować dysków VHDX na wirtualny dysk twardy z [przekonwertować qemu img](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) lub [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) polecenia cmdlet programu PowerShell. Azure nie obsługuje przekazywania dynamicznych wirtualnych dysków twardych, więc musisz przekonwertować te dyski statyczne wirtualne dyski twarde przed przekazaniem. Użyj narzędzi takich jak [narzędzia wirtualnego dysku twardego platformy Azure dla języka GO](https://github.com/Microsoft/azure-vhd-utils-for-go) przekonwertować dysków dynamicznych w procesie przekazywania ich do platformy Azure.
> 
> 


* Upewnij się, że masz najnowszy [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowane i użytkownik jest zalogowany na koncie platformy Azure za pomocą [az login](/cli/azure/reference-index#az-login).

W poniższych przykładach, Zastąp przykładowe nazwy parametru przy użyciu własnych wartości, takich jak *myResourceGroup*, *mystorageaccount*, i *mydisks*.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej

Platforma Azure obsługuje różne dystrybucje systemu Linux (zobacz [dystrybucje zatwierdzone dla](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Następujące artykuły opisano sposób przygotowania różne dystrybucje systemu Linux, które są obsługiwane na platformie Azure:

* [dystrybucje oparte na systemie CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES i openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Inne: nieobsługiwane dystrybucje](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Zobacz też [uwagi dotyczące instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) bardziej ogólne porady na temat przygotowywania obrazów systemu Linux na platformie Azure.

> [!NOTE]
> [Platformy Azure, umowa SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) ma zastosowanie do maszyn wirtualnych z systemem Linux, tylko wtedy, gdy jeden z zalecanych dystrybucji jest używany przy użyciu szczegółów konfiguracji, jak określono w obszarze "Obsługiwane wersje" w [systemu Linux na zatwierdzone na platformie Azure Dystrybucje](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Opcja 1: Przekazywanie wirtualnego dysku twardego

Możesz przekazać niestandardowe wirtualnego dysku twardego zostały uruchomione na komputerze lokalnym lub który został wyeksportowany z innej chmury. Aby użyć wirtualnego dysku twardego do tworzenia nowej maszyny Wirtualnej platformy Azure, musisz przekazanie dysku VHD do konta magazynu i tworzenie dysku zarządzanego na podstawie wirtualnego dysku twardego. Aby uzyskać więcej informacji, zobacz temat [Omówienie funkcji Dyski zarządzane platformy Azure](../windows/managed-disks-overview.md).

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed przekazaniem niestandardowego dysku i Tworzenie maszyn wirtualnych, musisz utworzyć grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group#az-group-create).

W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Tworzenie konta magazynu dla niestandardowego dysku i maszyn wirtualnych przy użyciu [Tworzenie konta magazynu az](/cli/azure/storage/account). Poniższy przykład tworzy konto magazynu o nazwie *mystorageaccount* w utworzoną wcześniej grupę zasobów:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>Wyświetl klucze konta magazynu
Azure generuje dwa klucze dostępu 512-bitowe dla każdego konta magazynu. Te klucze dostępu są używane podczas uwierzytelniania konta magazynu, takich jak podczas przeprowadzania operacji zapisu. Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem do magazynu](../../storage/common/storage-account-manage.md#access-keys). 

Wyświetl klucze dostępu za pomocą [listy kluczy kont magazynu az](/cli/azure/storage/account/keys#az-storage-account-keys-list). Na przykład aby wyświetlić klawisze dostępu dla magazynu utworzone konto:

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
Zwróć uwagę na **klucz1** jako użyje do interakcji z kontem usługi storage w następnych krokach.

### <a name="create-a-storage-container"></a>Utwórz kontener magazynu
W taki sam sposób utworzyć różnych katalogów w celu logicznego uporządkowania lokalnego systemu plików utworzysz kontenerów na koncie magazynu, aby organizować dyski. Konto magazynu może zawierać wiele kontenerów. Utwórz kontener przy użyciu [utworzyć kontenera magazynu az](/cli/azure/storage/container#az-storage-container-create).

Poniższy przykład tworzy kontener o nazwie *mydisks*:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>Przekazywanie wirtualnego dysku twardego
Przekaż swoje niestandardowego dysku przy użyciu [az storage blob upload](/cli/azure/storage/blob#az-storage-blob-upload). Będziesz przekazywanie i przechowywanie niestandardowego dysku jako stronicowy obiekt blob.

Określ klucz dostępu do kontenera utworzonego w poprzednim kroku, a następnie ścieżkę do niestandardowego dysku na komputerze lokalnym:

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


Tworzenie dysku zarządzanego na podstawie wirtualnego dysku twardego z [tworzenia dysku az](/cli/azure/disk#az-disk-create). Poniższy przykład tworzy dysk zarządzany o nazwie *myManagedDisk* z wirtualnego dysku twardego został przekazany do konta o nazwie magazynu i kontener:

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>Opcja 2: Kopiowanie istniejącej maszyny Wirtualnej

Można również tworzenie dostosowanych maszyn wirtualnych na platformie Azure, a następnie skopiuj dysk systemu operacyjnego i dołączyć go do nowej maszyny Wirtualnej, aby utworzyć kolejną kopię. Jest to poprawne dla testów, ale jeśli chcesz użyć istniejącej maszyny Wirtualnej platformy Azure jako modelu wielu nowych maszyn wirtualnych, tworzenie *obraz* zamiast tego. Aby uzyskać więcej informacji o tworzeniu obrazu z istniejącej maszyny Wirtualnej platformy Azure, zobacz [Tworzenie niestandardowego obrazu maszyny wirtualnej portalu Azure przy użyciu interfejsu wiersza polecenia](tutorial-custom-images.md).

### <a name="create-a-snapshot"></a>Tworzenie migawki

W tym przykładzie tworzy migawkę maszyny Wirtualnej o nazwie *myVM* w grupie zasobów *myResourceGroup* i tworzących migawkę o nazwie *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Tworzenie dysku zarządzanego

Tworzenie nowego dysku zarządzanego z migawki.

Pobierz identyfikator migawki. W tym przykładzie nosi nazwę migawki *osDiskSnapshot* i *myResourceGroup* grupy zasobów.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Tworzenie dysku zarządzanego. W tym przykładzie zostanie utworzony dysk zarządzany o nazwie *myManagedDisk* z naszych migawki, gdy dysk jest w magazynie standard storage i o rozmiarze 128 GB.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę Wirtualną za pomocą [tworzenie az vm](/cli/azure/vm#az-vm-create) i dołączyć (--dołączyć os-disk) dysku zarządzanego jako dysk systemu operacyjnego. Poniższy przykład tworzy Maszynę wirtualną o nazwie *myNewVM* użycie dysku zarządzanego utworzonego na podstawie przekazanych wirtualnego dysku twardego:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Powinno być możliwe do protokołu SSH z maszyną wirtualną przy użyciu poświadczeń ze źródłowej maszyny Wirtualnej. 

## <a name="next-steps"></a>Kolejne kroki
Po przygotować i przekazać niestandardowe wirtualnego dysku, możesz przeczytać więcej na temat [przy użyciu usługi Resource Manager i szablonów](../../azure-resource-manager/resource-group-overview.md). Możesz również chcieć [Dodaj dysk danych](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) do nowych maszyn wirtualnych. Jeśli masz aplikacje uruchomione na maszynach wirtualnych, które chcesz uzyskać dostęp, należy koniecznie [otworzyć porty i punkty końcowe](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
