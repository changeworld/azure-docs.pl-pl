---
title: Użyj init chmury, aby dodać użytkownika do maszyny Wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Jak używać init chmury można dodać użytkownika do maszyny Wirtualnej systemu Linux podczas tworzenia 2.0 interfejsu wiersza polecenia platformy Azure
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
ms.openlocfilehash: ce4421fc8276f215564cb7a171a215cc166c8517
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29123467"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Użyj init chmury, aby dodać użytkownika do maszyny Wirtualnej systemu Linux na platformie Azure
W tym artykule przedstawiono sposób użycia [init chmury](https://cloudinit.readthedocs.io) Aby dodać użytkownika na maszynie wirtualnej lub maszyny wirtualnej (VM) zestawach skali (VMSS) na inicjowanie obsługi administracyjnej czas na platformie Azure. Ten skrypt init chmury uruchomieniu po pierwszym uruchomieniu komputera zasoby zostały udostępnione przez platformę Azure. Aby uzyskać więcej informacji na temat chmury inicjowania działania natywnie Azure i obsługiwanych dystrybucjach systemu Linux, zobacz [omówienie chmury init](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Dodawanie użytkownika do maszyny Wirtualnej z inicjowaniem chmury
Jedno z pierwszym zadań na nowej maszyny Wirtualnej z systemem Linux jest dodanie dodatkowych użytkowników dla siebie należy unikać użycia *głównego*. Najlepszym rozwiązaniem w zakresie bezpieczeństwa i użyteczność są klucze SSH. Klucze są dodawane do *~/.ssh/authorized_keys* plików za pomocą tego skryptu init chmury.

Aby dodać użytkownika do maszyny Wirtualnej systemu Linux, należy utworzyć plik w bieżącym powłoki o nazwie *cloud_init_add_user.txt* i wklej następującą konfigurację. Na przykład można utworzyć pliku w powłoce chmury nie na komputerze lokalnym. Można użyć dowolnego edytora, którego chcesz. Wprowadź `sensible-editor cloud_init_add_user.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć **nano** edytora. Upewnij się, że poprawnie skopiować pliku całego init chmury szczególnie pierwszego wiersza.  Należy podać klucza publicznego (takie jak zawartość *~/.ssh/id_rsa.pub*) dla wartości `ssh-authorized-keys:` -go została skrócona, w tym miejscu można uprościć w przykładzie.

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
> Zawiera plik #cloud-config `- default` parametru. Użytkownik, zostanie dołączona do istniejącego użytkownika admin utworzony podczas inicjowania obsługi. W przypadku utworzenia użytkownika bez `- default` parametru - użytkownika administratora wygenerowane automatycznie utworzone przez platformę Azure będzie zastąpiona. 

Przed wdrożeniem tego obrazu, należy utworzyć nową grupę zasobów o [Tworzenie grupy az](/cli/azure/group#az_group_create) polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz, utwórz maszynę Wirtualną z [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) i określ plik init chmury z `--custom-data cloud_init_add_user.txt` w następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH jako publiczny adres IP wyświetlany w danych wyjściowych z poprzedniego polecenia maszyny wirtualnej. Wprowadź własne **publicznego adresu IP** w następujący sposób:

```bash
ssh <publicIpAddress>
```

Aby upewnić się, że użytkownik został dodany do maszyny Wirtualnej i określonych grup, Wyświetl zawartość */etc/grupy* plików w następujący sposób:

```bash
cat /etc/group
```

Następujące przykładowe dane wyjściowe wyglądają użytkownika z *cloud_init_add_user.txt* plik został dodany do maszyny Wirtualnej i odpowiednie grupy:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Kolejne kroki
Przykłady dodatkowe init chmury zmian konfiguracji zobacz następujące tematy:
 
- [Dodaj dodatkowe użytkownika w systemie Linux na maszynie Wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety po pierwszym uruchomieniu komputera](cloudinit-update-vm.md)
- [Zmień lokalną nazwą hosta maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizuj pliki konfiguracji i wstrzyknąć kluczy](tutorial-automate-vm-deployment.md)