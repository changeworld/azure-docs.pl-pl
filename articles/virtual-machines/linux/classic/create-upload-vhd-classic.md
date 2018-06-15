---
title: Tworzenie i przekazywanie wirtualnego dysku twardego systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Tworzenie i przekazywanie Azure wirtualnego dysku twardego (VHD) z systemem operacyjnym Linux przy użyciu klasycznego modelu wdrożenia
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 1ba568eeaf3bbc3d786cc48e54404aa65a00fecc
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841898"
---
# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Tworzenie i przekazywanie wirtualnego dysku twardego zawierającego system operacyjny Linux
> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Możesz również [Przekaż obraz niestandardowy dysku przy użyciu usługi Azure Resource Manager](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

W tym artykule opisano tworzenie i przekazywanie wirtualnego dysku twardego (VHD), tak jak własny obraz służy do tworzenia maszyn wirtualnych na platformie Azure. Dowiedz się, jak przygotować system operacyjny, więc służy do tworzenia wielu maszyn wirtualnych na podstawie tego obrazu. 


## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że masz następujące elementy:

* **Linux zainstalowanego systemu operacyjnego w pliku VHD** — zainstalowano [dystrybucji zatwierdzone na platformie Azure Linux](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (lub zobacz [informacje dotyczące niezatwierdzonych dystrybucji](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) do dysku wirtualnego w Format wirtualnego dysku twardego. Istnieje wiele narzędzi do tworzenia maszyny Wirtualnej i wirtualnego dysku twardego:
  * Instalowanie i konfigurowanie [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) lub [KVM](http://www.linux-kvm.org/page/RunningKVM), zwracając szczególną uwagę na w formacie VHD obrazu. W razie potrzeby można [Konwertuj obraz](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) przy użyciu `qemu-img convert`.
  * Można także użyć funkcji Hyper-V [w systemie Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) lub [w systemie Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Nowszy format VHDX nie jest obsługiwane na platformie Azure. Podczas tworzenia maszyny Wirtualnej, określ dysk VHD jak format. W razie potrzeby możesz przekonwertować dysków VHDX na dysku VHD za pomocą [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) lub [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) polecenia cmdlet programu PowerShell. Ponadto Azure nie obsługuje przekazywania dynamicznych wirtualnych dysków twardych, dlatego należy przekonwertować takie dyski wirtualne dyski twarde statycznej przed przekazaniem. Można użyć narzędzia, takie jak [Azure dysk VHD narzędzia dla Przejdź](https://github.com/Microsoft/azure-vhd-utils-for-go) przekonwertować dyski dynamiczne podczas procesu przekazywania na platformie Azure.

* **Interfejs wiersza polecenia platformy Azure** -zainstaluj najnowszą [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) na przekazanie dysku VHD.

<a id="prepimage"> </a>

## <a name="step-1-prepare-the-image-to-be-uploaded"></a>Krok 1: Przygotowanie obrazu do załadowania
Azure obsługuje różne dystrybucje systemu Linux (zobacz [dystrybucje zatwierdzone](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Następujące artykuły przedstawiono sposób przygotowania różnych dystrybucje systemu Linux, które są obsługiwane na platformie Azure. Po wykonaniu kroków w następujących przewodnikach potem wróć tutaj po utworzeniu pliku wirtualnego dysku twardego, który jest gotowe do przekazania do platformy Azure:

* **[Na podstawie centOS dystrybucji](../create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian systemu Linux](../debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](../oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](../redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](../suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](../create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Inne — niezatwierdzonych dystrybucji](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

> [!NOTE]
> Platformy Azure umowy SLA stosuje się do maszyn wirtualnych z systemem operacyjnym Linux tylko wtedy, gdy jeden z potwierdzony dystrybucji jest używany z konfiguracją szczegóły określone w obsługiwanych wersjach w [systemu Linux na dystrybucje Azure-Endorsed](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Wszystkie dystrybucje systemu Linux w galerii Azure obrazu są potwierdzony dystrybucji z odpowiednią konfiguracją.
> 
> 

Zobacz też **[informacje o instalacji systemu Linux](../create-upload-generic.md#general-linux-installation-notes)** bardziej ogólne porady dotyczące przygotowywania obrazów systemu Linux na platformie Azure.

<a id="connect"> </a>

## <a name="step-2-prepare-the-connection-to-azure"></a>Krok 2: Przygotowanie połączenia z platformą Azure
Upewnij się, że używasz interfejsu wiersza polecenia Azure w klasycznym modelu wdrażania (`azure config mode asm`), następnie zaloguj się do swojego konta:

```azurecli
azure login
```


<a id="upload"> </a>

## <a name="step-3-upload-the-image-to-azure"></a>Krok 3: Przekaż obraz na platformie Azure
Musisz mieć konto magazynu, aby przesłać plik wirtualnego dysku twardego do. Albo można wybrać istniejące konto magazynu lub [Utwórz nową](../../../storage/common/storage-create-storage-account.md).

Użyj interfejsu wiersza polecenia Azure, aby przekazać obraz za pomocą następującego polecenia:

```azurecli
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

W poprzednim przykładzie:

* **BlobStorageURL** jest adres URL dla konta magazynu ma być używany
* **YourImagesFolder** jest kontenera w magazynie obiektów blob, w którym mają być przechowywane obrazy
* **VHDName** jest etykietę wyświetlaną w portalu, aby zidentyfikować wirtualnego dysku twardego.
* **PathToVHDFile** jest pełna ścieżka i nazwa pliku VHD na tym komputerze.

Polecenie przedstawia pełny przykład:

```azurecli
azure vm image create myImage `
    --blob-url https://mystorage.blob.core.windows.net/vhds/myimage.vhd `
    --os Linux /home/ahmet/myimage.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>Krok 4: Tworzenie maszyny Wirtualnej z obrazu
Możesz utworzyć maszynę Wirtualną przy użyciu `azure vm create` w taki sam sposób jak regularne maszyny Wirtualnej. Określ nazwę nadaną obrazu w poprzednim kroku. W poniższym przykładzie używamy **myImage** nazwa obrazu podany w poprzednim kroku:

```azurecli
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "myDeployedVM" myImage
```

Do tworzenia własnych maszyn wirtualnych, podać własne nazwy użytkownika + hasła, lokalizację, nazwy DNS i nazwa obrazu.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, zobacz [odwołania wiersza polecenia platformy Azure dla platformy Azure klasycznym modelu wdrażania](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

[Step 1: Prepare the image to be uploaded]:#prepimage
[Step 2: Prepare the connection to Azure]:#connect
[Step 3: Upload the image to Azure]:#upload
