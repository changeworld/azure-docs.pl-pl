---
title: Dodawanie użytkownika do maszyny Wirtualnej z systemem Linux na platformie Azure za pomocą funkcji cloud-init
description: Jak używać cloud-init, aby dodać użytkownika do maszyny Wirtualnej systemu Linux podczas tworzenia za pomocą interfejsu wiersza polecenia platformy Azure
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: f1782bfe0c14e3b44703f89ec7f78590c1bb74c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969236"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Dodawanie użytkownika do maszyny wirtualnej z systemem Linux na platformie Azure za pomocą funkcji cloud-init
W tym artykule pokazano, jak używać [init chmury,](https://cloudinit.readthedocs.io) aby dodać użytkownika na maszynie wirtualnej (VM) lub zestawy skalowania maszyny wirtualnej (VMSS) w czasie inicjowania obsługi administracyjnej na platformie Azure. Ten skrypt init w chmurze jest uruchamiany przy pierwszym rozruchu po zainicjowaniu zasobów przez platformę Azure. Aby uzyskać więcej informacji na temat działania cloud-init natywnie na platformie Azure i obsługiwanych dystrybucji systemu Linux, zobacz [omówienie cloud-init](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Dodawanie użytkownika do maszyny Wirtualnej za pomocą cloud-init
Jednym z pierwszych zadań na każdej nowej maszynie wirtualnej z systemem Linux jest dodanie dodatkowego użytkownika dla siebie, aby uniknąć użycia *roota*. Klucze SSH są najlepszym rozwiązaniem w zakresie bezpieczeństwa i użyteczności. Klucze są dodawane do pliku *~/.ssh/authorized_keys* za pomocą tego skryptu cloud-init.

Aby dodać użytkownika do maszyny Wirtualnej systemu Linux, utwórz plik w bieżącej powłoce o nazwie *cloud_init_add_user.txt* i wklej następującą konfigurację. W tym przykładzie utwórz plik w usłudze Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud_init_add_user.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć **edytora nano.** Upewnij się, że cały plik cloud-init jest kopiowany poprawnie, zwłaszcza w pierwszym wierszu.  Musisz podać własny klucz publiczny (na przykład zawartość *~/.ssh/id_rsa.pub)* dla `ssh-authorized-keys:` wartości - został on skrócony tutaj, aby uprościć przykład.

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
> Plik #cloud-config zawiera dołączony `- default` parametr. Spowoduje to dołączenie użytkownika do istniejącego użytkownika administratora utworzonego podczas inicjowania obsługi administracyjnej. Jeśli utworzysz użytkownika `- default` bez parametru — automatycznie wygenerowany użytkownik administratora utworzony przez platformę Azure zostanie zastąpiony. 

Przed wdrożeniem tego obrazu należy utworzyć grupę zasobów za pomocą polecenia [tworzenie grupy az.](/cli/azure/group) Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz utwórz maszynę wirtualną z [az vm utworzyć](/cli/azure/vm) `--custom-data cloud_init_add_user.txt` i określić plik cloud-init w następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH do publicznego adresu IP maszyny Wirtualnej pokazane w danych wyjściowych z poprzedniego polecenia. Wpisz swój własny **publicIpAddress** w następujący sposób:

```bash
ssh <publicIpAddress>
```

Aby potwierdzić, że użytkownik został dodany do maszyny Wirtualnej i określonych grup, wyświetl zawartość pliku */etc/group* w następujący sposób:

```bash
cat /etc/group
```

Poniższy przykładowy wynik pokazuje, że użytkownik z pliku *cloud_init_add_user.txt* został dodany do maszyny Wirtualnej i odpowiedniej grupy:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkowe przykłady zmian konfiguracji w chmurze, zobacz następujące elementy:
 
- [Dodawanie dodatkowego użytkownika systemu Linux do maszyny Wirtualnej](cloudinit-add-user.md)
- [Uruchamianie menedżera pakietów w celu zaktualizowania istniejących pakietów przy pierwszym rozruchu](cloudinit-update-vm.md)
- [Zmienianie nazwy hosta lokalnego maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Instalowanie pakietu aplikacji, aktualizowanie plików konfiguracyjnych i wstrzykiwanie kluczy](tutorial-automate-vm-deployment.md)
