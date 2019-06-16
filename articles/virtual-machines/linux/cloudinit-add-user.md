---
title: Użyj pakietu cloud-init, aby dodać użytkownika do maszyny Wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Jak dodać użytkownika do maszyny Wirtualnej z systemem Linux podczas tworzenia przy użyciu wiersza polecenia platformy Azure za pomocą pakietu cloud-init
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
ms.openlocfilehash: 51de92eb64e9879b769baf7e574ee1dca9355040
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60650356"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Użyj pakietu cloud-init, aby dodać użytkownika do maszyny Wirtualnej z systemem Linux na platformie Azure
W tym artykule dowiesz się, jak używać [pakietu cloud-init](https://cloudinit.readthedocs.io) mógł dodać użytkownika na maszynie wirtualnej lub maszyny wirtualnej (VM) zestawów skalowania (zestawu skalowania maszyn wirtualnych) na inicjowanie obsługi administracyjnej czas na platformie Azure. Ten skrypt pakietu cloud-init jest uruchamiany podczas pierwszego rozruchu po zasoby zostały udostępnione przez platformę Azure. Aby uzyskać więcej informacji o tym, jak pakietu cloud-init działa natywnie na platformie Azure i obsługiwane dystrybucje systemu Linux, zobacz [Omówienie pakietu cloud-init](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Dodawanie użytkownika do maszyny Wirtualnej za pomocą pakietu cloud-init
Jedno z zadań pierwszego na nowej maszynie Wirtualnej z systemem Linux jest dodanie dodatkowego użytkownika samodzielnie uniknąć użycia *głównego*. Klucze SSH są najlepszym rozwiązaniem w zakresie bezpieczeństwa i użyteczności. Klucze są dodawane do *~/.ssh/authorized_keys* plików za pomocą tego skryptu pakietu cloud-init.

Aby dodać użytkownika do maszyny Wirtualnej z systemem Linux, Utwórz plik w bieżącej powłoce o nazwie *cloud_init_add_user.txt* i Wklej poniższą konfigurację. W tym przykładzie należy utworzyć plik w usłudze Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud_init_add_user.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć **nano** edytora. Upewnij się, że poprawnie skopiować pliku całego pakietu cloud-init szczególnie pierwszy wiersz.  Należy podać klucz publiczny (np. zawartość *~/.ssh/id_rsa.pub*) dla wartości `ssh-authorized-keys:` — została ona skrócona tutaj aby uprościć przykład.

```yaml
#cloud-config
users:
  - default
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```
> [!NOTE] 
> Plik konfiguracji cloud # zawiera `- default` parametru. Użytkownik zostanie dołączona do istniejącego konta użytkownika administracyjnego utworzone podczas inicjowania obsługi. Jeśli tworzysz użytkownika bez `- default` parametr - automatycznie wygenerowanego administratora utworzone przez platformę Azure czy zostanie zastąpiony. 

Przed wdrożeniem tego obrazu, należy utworzyć grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group) polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz Utwórz maszynę Wirtualną z [tworzenie az vm](/cli/azure/vm) i określ plik cloud-init za pomocą `--custom-data cloud_init_add_user.txt` w następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

Protokół SSH z publicznego adresu IP maszyny Wirtualnej w danych wyjściowych z poprzedniego polecenia. Wprowadź własne **publicznego adresu IP** w następujący sposób:

```bash
ssh <publicIpAddress>
```

Aby upewnić się, użytkownik został dodany do maszyny Wirtualnej i określonych grup, Wyświetl zawartość */etc/grupy* pliku w następujący sposób:

```bash
cat /etc/group
```

Następujące przykładowe dane wyjściowe są widoczne dla użytkownika z *cloud_init_add_user.txt* plik został dodany do maszyny Wirtualnej i odpowiednie grupy:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Kolejne kroki
Przykłady dodatkowe pakietu cloud-init zmian konfiguracji zobacz następujące tematy:
 
- [Dodaj dodatkowe użytkownika w systemie Linux do maszyny Wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety podczas pierwszego rozruchu](cloudinit-update-vm.md)
- [Zmień lokalną nazwą hosta maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizować pliki konfiguracji i wstawić kluczy](tutorial-automate-vm-deployment.md)
