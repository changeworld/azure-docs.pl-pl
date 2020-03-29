---
title: Konfigurowanie partycji wymiany na maszynie wirtualnej z systemem Linux za pomocą funkcji cloud-init
description: Jak używać cloud-init do konfigurowania partycji wymiany na maszynie Wirtualnej systemu Linux podczas tworzenia za pomocą interfejsu wiersza polecenia platformy Azure
author: rickstercdn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1247652e536042ee249054d86aed3c3f8e7aa7bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969203"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>Konfigurowanie partycji wymiany na maszynie wirtualnej z systemem Linux za pomocą funkcji cloud-init
W tym artykule pokazano, jak używać [cloud-init](https://cloudinit.readthedocs.io) do konfigurowania partycji wymiany w różnych dystrybucjach systemu Linux. Partycja wymiany była tradycyjnie konfigurowana przez agenta Linux (WALA) na podstawie której wymagane są dystrybucje.  Ten dokument będzie opis procesu tworzenia partycji wymiany na żądanie podczas inicjowania obsługi administracyjnej przy użyciu cloud-init.  Aby uzyskać więcej informacji na temat działania cloud-init na platformie Azure i obsługiwanych dystrybucji systemu Linux, zobacz [omówienie cloud-init](using-cloud-init.md)

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Tworzenie partycji wymiany dla obrazów opartych na Ubuntu
Domyślnie na platformie Azure obrazy galerii Ubuntu nie tworzą partycji wymiany. Aby włączyć konfigurację partycji wymiany podczas inicjowania obsługi maszyn wirtualnych przy użyciu cloud-init — zobacz [dokument AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions) na wiki Ubuntu.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Tworzenie partycji wymiany dla obrazów opartych na red hat i centos

Utwórz plik w bieżącej powłoce o nazwie *cloud_init_swappart.txt* i wklej następującą konfigurację. W tym przykładzie utwórz plik w usłudze Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud_init_swappart.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć **edytora nano.** Upewnij się, że cały plik cloud-init jest kopiowany poprawnie, zwłaszcza w pierwszym wierszu.  

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

Przed wdrożeniem tego obrazu należy utworzyć grupę zasobów za pomocą polecenia [tworzenie grupy az.](/cli/azure/group) Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz utwórz maszynę wirtualną z [az vm utworzyć](/cli/azure/vm) `--custom-data cloud_init_swappart.txt` i określić plik cloud-init w następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>Sprawdź partycję wymiany został utworzony
SSH do publicznego adresu IP maszyny Wirtualnej pokazane w danych wyjściowych z poprzedniego polecenia. Wpisz swój własny **publicIpAddress** w następujący sposób:

```bash
ssh <publicIpAddress>
```

Po wprowadzeniu SSH'ed do maszyny wirtualnej sprawdź, czy partycja wymiany została utworzona

```bash
swapon -s
```

Dane wyjściowe z tego polecenia powinny wyglądać następująco:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Jeśli masz istniejący obraz platformy Azure, który ma partycję wymiany skonfigurowane i chcesz zmienić konfigurację partycji wymiany dla nowych obrazów, należy usunąć istniejącą partycję wymiany. Aby uzyskać więcej informacji, zobacz dokument "Dostosowywanie obrazów do udostępniania przez chmurę init".

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkowe przykłady zmian konfiguracji w chmurze, zobacz następujące elementy:
 
- [Dodawanie dodatkowego użytkownika systemu Linux do maszyny Wirtualnej](cloudinit-add-user.md)
- [Uruchamianie menedżera pakietów w celu zaktualizowania istniejących pakietów przy pierwszym rozruchu](cloudinit-update-vm.md)
- [Zmienianie nazwy hosta lokalnego maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Instalowanie pakietu aplikacji, aktualizowanie plików konfiguracyjnych i wstrzykiwanie kluczy](tutorial-automate-vm-deployment.md)
