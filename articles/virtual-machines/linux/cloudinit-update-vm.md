---
title: Zaktualizować i zainstaluj pakiety w Maszynę wirtualną systemu Linux na platformie Azure za pomocą init chmurze | Dokumentacja firmy Microsoft
description: Jak używać init chmury do aktualizacji, a następnie zainstaluj pakiety w Maszynę wirtualną systemu Linux podczas tworzenia 2.0 interfejsu wiersza polecenia platformy Azure
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
ms.date: 04/20/2018
ms.author: rclaus
ms.openlocfilehash: 8d5835b5d1b0c2f77bdf5e1a2b808478b8f4de22
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186158"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Zaktualizować i zainstaluj pakiety w Maszynę wirtualną systemu Linux na platformie Azure za pomocą init chmury
W tym artykule przedstawiono sposób użycia [init chmury](https://cloudinit.readthedocs.io) aktualizację pakietów w systemie Linux maszyny wirtualnej (VM) lub skalowania maszyny wirtualnej ustawia (VMSS) na inicjowanie obsługi administracyjnej czas na platformie Azure. Skrypty te init chmury są uruchamiane po pierwszym uruchomieniu komputera po zasoby zostały udostępnione przez platformę Azure. Aby uzyskać więcej informacji na temat chmury inicjowania działania natywnie Azure i obsługiwanych dystrybucjach systemu Linux, zobacz [init chmury — omówienie](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Aktualizacja maszyny Wirtualnej z inicjowaniem chmury
Ze względów bezpieczeństwa można skonfigurować Maszynę wirtualną do zastosowania najnowszych aktualizacji po pierwszym uruchomieniu komputera. Jak chmury init działa za pośrednictwem różnych dystrybucjach systemu Linux, nie istnieje potrzeba do określenia `apt` lub `yum` Menedżera pakietów. Zamiast tego należy zdefiniować `package_upgrade` i pozwolić, aby ustalić odpowiedni mechanizm distro używany proces init chmury. Ten przepływ pracy umożliwia przy użyciu tego samego skrypty chmurze init między dystrybucjach.

Aby wyświetlić procesu uaktualniania w akcji, należy utworzyć plik w bieżącym powłoki o nazwie *cloud_init_upgrade.txt* i wklej następującą konfigurację. Na przykład można utworzyć pliku w powłoce chmury nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud_init_upgrade.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć **nano** edytora. Upewnij się, że poprawnie skopiować pliku całego init chmury szczególnie pierwszego wiersza.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Przed wdrożeniem tego obrazu, należy utworzyć nową grupę zasobów o [Tworzenie grupy az](/cli/azure/group#az_group_create) polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz, utwórz maszynę Wirtualną z [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) i określ plik init chmury z `--custom-data cloud_init_upgrade.txt` w następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH jako publiczny adres IP wyświetlany w danych wyjściowych z poprzedniego polecenia maszyny wirtualnej. Wprowadź własne **publicznego adresu IP** w następujący sposób:

```bash
ssh <publicIpAddress>
```

Uruchom pakiet narzędzia do zarządzania i sprawdzają aktualizacje.

```bash
sudo yum update
```

Ponieważ init chmury sprawdzane pod kątem i zainstalować aktualizacje na rozruchu, nie powinno być żadnych dodatkowych aktualizacji do zastosowania.  Zobacz proces aktualizacji, liczbę pakietów zmieniony, a także instalacji `httpd` uruchamiając `yum history` i przejrzyj dane wyjściowe podobne do przedstawionego poniżej.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Kolejne kroki
Przykłady dodatkowe init chmury zmian konfiguracji zobacz następujące tematy:
 
- [Dodaj dodatkowe użytkownika w systemie Linux na maszynie Wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety po pierwszym uruchomieniu komputera](cloudinit-update-vm.md)
- [Zmień lokalną nazwą hosta maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizuj pliki konfiguracji i wstrzyknąć kluczy](tutorial-automate-vm-deployment.md)