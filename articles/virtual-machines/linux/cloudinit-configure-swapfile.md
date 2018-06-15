---
title: Konfigurowanie swapfile na Maszynę wirtualną systemu Linux za pomocą init chmurze | Dokumentacja firmy Microsoft
description: Jak skonfigurować swapfile na maszynie wirtualnej systemu Linux podczas tworzenia 2.0 interfejsu wiersza polecenia platformy Azure przy użyciu inicjowania chmury
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
ms.openlocfilehash: 88a141922f113caf7ad67c89de48f84a821f7ba3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
ms.locfileid: "29952602"
---
# <a name="use-cloud-init-to-configure-a-swapfile-on-a-linux-vm"></a>Konfigurowanie swapfile na Maszynę wirtualną systemu Linux za pomocą init chmury
W tym artykule przedstawiono sposób użycia [init chmury](https://cloudinit.readthedocs.io) skonfigurować swapfile w różnych dystrybucje systemu Linux. Swapfile tradycyjnie została skonfigurowana przez Linux Agent (WALA) oparte na które dystrybucji wymagany jeden.  Ten dokument zostanie wchodzą w skład procesu tworzenia swapfile na żądanie w czasie inicjowania obsługi administracyjnej za pomocą init chmury.  Aby uzyskać więcej informacji na temat chmury inicjowania działania natywnie Azure i obsługiwanych dystrybucjach systemu Linux, zobacz [init chmury — omówienie](using-cloud-init.md)

## <a name="create-swapfile-for-ubuntu-based-images"></a>Utwórz swapfile Ubuntu na podstawie obrazów
Domyślnie na platformie Azure Ubuntu obrazów Galeria nie należy tworzyć pliki wymiany. Można włączyć konfiguracji pliku wymiany podczas obsługi logowanie przy użyciu inicjowania chmury maszyny Wirtualnej — można znaleźć pod adresem [dokumentu AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions) w witrynie typu wiki Ubuntu.

## <a name="create-swapfile-for-redhat-and-centos-based-images"></a>Utwórz swapfile RedHat i CentOS na podstawie obrazów

Utwórz plik w bieżącym powłoki o nazwie *cloud_init_swapfile.txt* i wklej następującą konfigurację. Na przykład można utworzyć pliku w powłoce chmury nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud_init_swapfile.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć **nano** edytora. Upewnij się, że poprawnie skopiować pliku całego init chmury szczególnie pierwszego wiersza.  

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

Przed wdrożeniem tego obrazu, należy utworzyć nową grupę zasobów o [Tworzenie grupy az](/cli/azure/group#az_group_create) polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz, utwórz maszynę Wirtualną z [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) i określ plik init chmury z `--custom-data cloud_init_swapfile.txt` w następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swapfile.txt \
  --generate-ssh-keys 
```

## <a name="verify-swapfile-was-created"></a>Sprawdź, czy utworzono swapfile
SSH jako publiczny adres IP wyświetlany w danych wyjściowych z poprzedniego polecenia maszyny wirtualnej. Wprowadź własne **publicznego adresu IP** w następujący sposób:

```bash
ssh <publicIpAddress>
```

Po utworzeniu SSH'ed do maszyny wirtualnej, sprawdź, czy utworzono swapfile

```bash
swapon -s
```

Dane wyjściowe tego polecenia powinien wyglądać następująco:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Jeśli masz istniejący obraz Azure z pliku wymiany skonfigurowane i chcesz zmienić konfigurację pliku wymiany dla nowych obrazów, należy usunąć istniejący plik wymiany. Zobacz "Dostosowywanie obrazów udostępniania przez init chmury" dokumentu, aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki
Przykłady dodatkowe init chmury zmian konfiguracji zobacz następujące tematy:
 
- [Dodaj dodatkowe użytkownika w systemie Linux na maszynie Wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety po pierwszym uruchomieniu komputera](cloudinit-update-vm.md)
- [Zmień lokalną nazwą hosta maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizuj pliki konfiguracji i wstrzyknąć kluczy](tutorial-automate-vm-deployment.md)
