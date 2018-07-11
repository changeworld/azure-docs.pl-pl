---
title: Tworzenie i przekazywanie wirtualnego dysku twardego systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Tworzenie i przekazywanie Azure wirtualnego dysku twardego (VHD) z systemem operacyjnym Linux przy użyciu klasycznego modelu wdrażania
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 8058ff98-db03-4309-9bf4-69842bd64dd4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: cynthn
ms.openlocfilehash: cdbe6aa5683ecf9d8bdaf6bbf9503ddc455f03ee
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928271"
---
# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Tworzenie i przekazywanie wirtualnego dysku twardego zawierającego system operacyjny Linux
> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Możesz również [przekazywanie obrazu niestandardowego dysku przy użyciu usługi Azure Resource Manager](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Ten artykuł pokazuje, jak tworzenie i przekazywanie wirtualnego dysku twardego (VHD), aby można było używać go jako swój własny obraz do tworzenia maszyn wirtualnych na platformie Azure. Dowiedz się, jak przygotować system operacyjny, aby można było używać do tworzenia wielu maszyn wirtualnych na podstawie tego obrazu. 


## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz następujące elementy:

* **System operacyjny Linux zainstalowane w pliku VHD** — zainstalowano [dystrybucji systemu Linux z zatwierdzone na platformie Azure](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (lub zobacz [informacje dotyczące niezatwierdzonych dystrybucji](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) do dysku wirtualnego w Format wirtualnego dysku twardego. Istnieje wiele narzędzi do tworzenia maszyny Wirtualnej i wirtualnego dysku twardego:
  * Instalowanie i konfigurowanie [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) lub [KVM](http://www.linux-kvm.org/page/RunningKVM), zwracając szczególną uwagę na w formacie VHD obrazu. Jeśli to konieczne, możesz [Konwertowanie obrazu](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) przy użyciu `qemu-img convert`.
  * Możesz również użyć funkcji Hyper-V [w systemie Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) lub [w systemie Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Nowszy format VHDX nie jest obsługiwane na platformie Azure. Podczas tworzenia maszyny Wirtualnej należy określić wirtualnego dysku twardego w formacie. Jeśli to konieczne, można przekonwertować dysków VHDX na dysku VHD za pomocą [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) lub [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) polecenia cmdlet programu PowerShell. Ponadto Azure nie obsługuje przekazywania dynamicznych wirtualnych dysków twardych, więc musisz przekonwertować te dyski do statycznego wirtualnych dysków twardych przed przekazaniem. Użyj narzędzi takich jak [narzędzia wirtualnego dysku twardego platformy Azure dla języka GO](https://github.com/Microsoft/azure-vhd-utils-for-go) przekonwertować dysków dynamicznych w procesie przekazywania na platformie Azure.

* **Interfejs wiersza polecenia platformy Azure** -zainstaluj najnowszą wersję [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) do przekazania dysku VHD.

<a id="prepimage"> </a>

## <a name="step-1-prepare-the-image-to-be-uploaded"></a>Krok 1: Przygotowanie obrazu do przekazania
Platforma Azure obsługuje różne dystrybucje systemu Linux (zobacz [dystrybucje zatwierdzone dla](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Następujące artykuły przeprowadzą Cię przez proces przygotować różne dystrybucje systemu Linux, które są obsługiwane na platformie Azure. Po wykonaniu kroków w następujących przewodnikach tu wrócić po utworzeniu pliku wirtualnego dysku twardego, który jest gotowy do przekazania na platformę Azure:

* **[Dystrybucje systemu centOS](../create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian systemu Linux](../debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](../oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](../redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](../suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](../create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Inne — niezatwierdzonych dystrybucji](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

> [!NOTE]
> Platforma Azure, stosowana jest umowa SLA do maszyn wirtualnych z systemem operacyjnym Linux tylko wtedy, gdy jeden z zalecanych dystrybucji jest używany z konfiguracją szczegóły określone w obsługiwanych wersjach w [systemu Linux w Dystrybucjach Azure-Endorsed](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Wszystkie dystrybucje systemu Linux w galerii obrazów systemu Azure są zalecanych dystrybucjach za pomocą wymaganej konfiguracji.
> 
> 

Zobacz też **[uwagi dotyczące instalacji systemu Linux](../create-upload-generic.md#general-linux-installation-notes)** bardziej ogólne porady na temat przygotowywania obrazów systemu Linux na platformie Azure.

<a id="connect"> </a>

## <a name="step-2-prepare-the-connection-to-azure"></a>Krok 2: Przygotowanie połączenie z platformą Azure
Upewnij się, czy przy użyciu wiersza polecenia platformy Azure w klasycznym modelu wdrażania (`azure config mode asm`), następnie zaloguj się do swojego konta:

```azurecli
azure login
```


<a id="upload"> </a>

## <a name="step-3-upload-the-image-to-azure"></a>Krok 3: Przekaż obraz na platformie Azure
Wymagane jest konto magazynu, aby przesłać plik VHD do. Możesz wybrać istniejące konto magazynu lub [Utwórz nową](../../../storage/common/storage-create-storage-account.md).

Użyj wiersza polecenia platformy Azure, aby przekazać obraz przy użyciu następującego polecenia:

```azurecli
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

W poprzednim przykładzie:

* **BlobStorageURL** jest adres URL dla konta magazynu ma być używany
* **YourImagesFolder** jest kontenerem w ramach magazynu obiektów blob, którym chcesz przechowywać swoje obrazy
* **VHDName** jest etykietę, która będzie wyświetlana w portalu, aby zidentyfikować wirtualnego dysku twardego.
* **PathToVHDFile** jest pełną ścieżkę i nazwę pliku VHD na komputerze.

Następujące polecenie wyświetla pełny przykład:

```azurecli
azure vm image create myImage `
    --blob-url https://mystorage.blob.core.windows.net/vhds/myimage.vhd `
    --os Linux /home/ahmet/myimage.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>Krok 4: Tworzenie maszyny Wirtualnej z obrazu
Możesz utworzyć maszynę Wirtualną przy użyciu `azure vm create` w taki sam sposób, jak regularne maszyny Wirtualnej. Określ nazwę nadaną obrazu w poprzednim kroku. W poniższym przykładzie użyto **myImage** nazwę obrazu, podany w poprzednim kroku:

```azurecli
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "myDeployedVM" myImage
```

Aby utworzyć własne maszyny wirtualne, podaj własne nazwy użytkownika + hasła, lokalizację, nazwę DNS i nazwę obrazu.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, zobacz [odwołanie do wiersza polecenia platformy Azure dla klasycznego modelu wdrażania platformy](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

[Step 1: Prepare the image to be uploaded]:#prepimage
[Step 2: Prepare the connection to Azure]:#connect
[Step 3: Upload the image to Azure]:#upload
