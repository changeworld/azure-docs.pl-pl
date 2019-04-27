---
title: Konfigurowanie pliku wymiany na Maszynę wirtualną systemu Linux za pomocą pakietu cloud-init | Dokumentacja firmy Microsoft
description: Jak konfigurowanie pliku wymiany w maszynie Wirtualnej systemu Linux podczas tworzenia przy użyciu wiersza polecenia platformy Azure za pomocą pakietu cloud-init
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 626fd4739daf2506854c42f16ac986a361ebab38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729821"
---
# <a name="use-cloud-init-to-configure-a-swapfile-on-a-linux-vm"></a>Konfigurowanie pliku wymiany na Maszynę wirtualną systemu Linux za pomocą pakietu cloud-init
W tym artykule dowiesz się, jak używać [pakietu cloud-init](https://cloudinit.readthedocs.io) na konfigurowanie pliku wymiany w różnych dystrybucjach systemu Linux. Swapfile tradycyjnie zostało skonfigurowane przez Linux Agent (WALA) oparte na które dystrybucji wymagany jeden.  W tym dokumencie opisują proces tworzenia pliku wymiany na żądanie w czasie inicjowania obsługi administracyjnej za pomocą pakietu cloud-init.  Aby uzyskać więcej informacji o tym, jak pakietu cloud-init działa natywnie na platformie Azure i obsługiwane dystrybucje systemu Linux, zobacz [Omówienie pakietu cloud-init](using-cloud-init.md)

## <a name="create-swapfile-for-ubuntu-based-images"></a>Tworzenie pliku wymiany obrazów oparty na systemie Ubuntu
Domyślnie na platformie Azure Ubuntu galerii obrazów nie należy tworzyć pliki wymiany. Aby umożliwić zamiany pliku konfiguracji w czasie za pomocą pakietu cloud-init aprowizacji maszyny Wirtualnej — można znaleźć [dokumentu AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions) w witrynie typu wiki Ubuntu.

## <a name="create-swapfile-for-red-hat-and-centos-based-images"></a>Tworzenie pliku wymiany, Red Hat i CentOS na podstawie obrazów

Utwórz plik w bieżącej powłoce o nazwie *cloud_init_swapfile.txt* i Wklej poniższą konfigurację. W tym przykładzie należy utworzyć plik w usłudze Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud_init_swapfile.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć **nano** edytora. Upewnij się, że poprawnie skopiować pliku całego pakietu cloud-init szczególnie pierwszy wiersz.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Przed wdrożeniem tego obrazu, należy utworzyć grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group) polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz Utwórz maszynę Wirtualną z [tworzenie az vm](/cli/azure/vm) i określ plik cloud-init za pomocą `--custom-data cloud_init_swapfile.txt` w następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swapfile.txt \
  --generate-ssh-keys 
```

## <a name="verify-swapfile-was-created"></a>Upewnij się, że utworzono pliku wymiany
Protokół SSH z publicznego adresu IP maszyny Wirtualnej w danych wyjściowych z poprzedniego polecenia. Wprowadź własne **publicznego adresu IP** w następujący sposób:

```bash
ssh <publicIpAddress>
```

Po utworzeniu SSH'ed do maszyny wirtualnej, sprawdź, czy został utworzony pliku wymiany

```bash
swapon -s
```

Dane wyjściowe tego polecenia powinien wyglądać następująco:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Jeśli masz istniejącego obrazu platformy Azure, który ma skonfigurowane pliku wymiany, i chcesz zmienić konfigurację pliku wymiany nowych obrazów, należy usunąć istniejący plik wymiany. Zobacz "Dostosowywanie obrazy do przepisu pakietu cloud-init" dokumentu, aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki
Przykłady dodatkowe pakietu cloud-init zmian konfiguracji zobacz następujące tematy:
 
- [Dodaj dodatkowe użytkownika w systemie Linux do maszyny Wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety podczas pierwszego rozruchu](cloudinit-update-vm.md)
- [Zmień lokalną nazwą hosta maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizować pliki konfiguracji i wstawić kluczy](tutorial-automate-vm-deployment.md)
