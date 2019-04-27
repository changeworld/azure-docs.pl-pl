---
title: Użyj pakietu cloud-init do aktualizacji i zainstalować pakiety na maszynie Wirtualnej z systemem Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Jak zaktualizować i instalowanie pakietów na maszynie Wirtualnej z systemem Linux podczas tworzenia przy użyciu wiersza polecenia platformy Azure za pomocą pakietu cloud-init
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
ms.openlocfilehash: d5f4dc7f4abc13f253a206a63e65faf1106f9c7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627745"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Użyj pakietu cloud-init do aktualizacji i zainstalować pakiety na maszynie Wirtualnej z systemem Linux na platformie Azure
W tym artykule dowiesz się, jak używać [pakietu cloud-init](https://cloudinit.readthedocs.io) aktualizację pakietów w systemie Linux maszyny wirtualnej (VM) lub skalowania maszyn wirtualnych ustawia (zestawu skalowania maszyn wirtualnych) w aprowizacji czas na platformie Azure. Skrypty te pakietu cloud-init są uruchamiane podczas pierwszego rozruchu po zasoby zostały udostępnione przez platformę Azure. Aby uzyskać więcej informacji o tym, jak pakietu cloud-init działa natywnie na platformie Azure i obsługiwane dystrybucje systemu Linux, zobacz [Omówienie pakietu cloud-init](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Zaktualizuj Maszynę wirtualną przy użyciu pakietu cloud-init
Ze względów bezpieczeństwa można skonfigurować maszynę Wirtualną, która Zastosuj najnowsze aktualizacje przy pierwszym uruchomieniu. Jak pakiet cloud-init działa między różne dystrybucje systemu Linux, nie ma konieczności określania `apt` lub `yum` dla Menedżera pakietów. Zamiast tego Zdefiniuj `package_upgrade` i pozwolić określić odpowiedni mechanizm dystrybucji używany proces pakietu cloud-init. Ten przepływ pracy umożliwia za pomocą tych samych skryptów pakietu cloud-init w dystrybucjach.

Aby zobaczyć proces uaktualniania w akcji, Utwórz plik w bieżącej powłoce o nazwie *cloud_init_upgrade.txt* i Wklej poniższą konfigurację. W tym przykładzie należy utworzyć plik w usłudze Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud_init_upgrade.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć **nano** edytora. Upewnij się, że poprawnie skopiować pliku całego pakietu cloud-init szczególnie pierwszy wiersz.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Przed wdrożeniem tego obrazu, należy utworzyć grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group) polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz Utwórz maszynę Wirtualną z [tworzenie az vm](/cli/azure/vm) i określ plik cloud-init za pomocą `--custom-data cloud_init_upgrade.txt` w następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

Protokół SSH z publicznego adresu IP maszyny Wirtualnej w danych wyjściowych z poprzedniego polecenia. Wprowadź własne **publicznego adresu IP** w następujący sposób:

```bash
ssh <publicIpAddress>
```

Uruchamianie narzędzia do zarządzania pakietami i sprawdzenie aktualizacji.

```bash
sudo yum update
```

Pakiet cloud-init sprawdzane pod kątem do zainstalowania aktualizacji podczas rozruchu, powinno być nie dodatkowych aktualizacji do zastosowania.  Zobacz proces aktualizacji, liczba zmienionych pakietów, a także instalację `httpd` , uruchamiając `yum history` i przejrzyj dane wyjściowe są podobne do przedstawionego poniżej.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Kolejne kroki
Przykłady dodatkowe pakietu cloud-init zmian konfiguracji zobacz następujące tematy:
 
- [Dodaj dodatkowe użytkownika w systemie Linux do maszyny Wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety podczas pierwszego rozruchu](cloudinit-update-vm.md)
- [Zmień lokalną nazwą hosta maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizować pliki konfiguracji i wstawić kluczy](tutorial-automate-vm-deployment.md)
