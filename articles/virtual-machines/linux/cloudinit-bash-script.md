---
title: Użyj init chmury, aby uruchomić skrypt bash w Maszynę wirtualną systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Jak używać init chmury do uruchomienia skryptu bash w Maszynę wirtualną systemu Linux podczas tworzenia 2.0 interfejsu wiersza polecenia platformy Azure
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
ms.openlocfilehash: 471749563fae5b5de6e98e22ebf2ec5cc9365368
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29123722"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Uruchom skrypt bash w Maszynę wirtualną systemu Linux na platformie Azure za pomocą init chmury
W tym artykule przedstawiono sposób użycia [init chmury](https://cloudinit.readthedocs.io) istniejące bash do uruchamiania skryptu na maszynie wirtualnej systemu Linux (VM) lub zestawach skali maszyn wirtualnych (VMSS) na inicjowanie obsługi administracyjnej czas na platformie Azure. Skrypty te init chmury są uruchamiane po pierwszym uruchomieniu komputera po zasoby zostały udostępnione przez platformę Azure. Aby uzyskać więcej informacji na temat chmury inicjowania działania natywnie Azure i obsługiwanych dystrybucjach systemu Linux, zobacz [init chmury — omówienie](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>Uruchom skrypt bash z inicjowaniem chmury
Nie trzeba przekonwertować istniejące skrypty chmurze config init chmury chmury init akceptuje wiele typów wejściowych, z których jeden jest skryptem bash.

Jeśli do uruchamiania skryptów był używany Linux niestandardowe rozszerzenie Azure skryptu, można migrować je, aby użyć init chmury. Jednak rozszerzenia Azure jest zintegrowany zgłoszenie alertu na awarie skryptu, wdrożenia obrazu init chmury zakończy się niepowodzeniem, jeśli skrypt zakończy się niepowodzeniem.

Aby wyświetlić tę funkcję w akcji, należy utworzyć skrypt bash proste do testowania. Init chmury, takich jak `#cloud-config` pliku tego skryptu musi być lokalny, na którym zostanie uruchomiona polecenia AzureCLI, aby udostępnić maszynie wirtualnej.  Na przykład można utworzyć pliku w powłoce chmury nie na komputerze lokalnym. Można użyć dowolnego edytora, którego chcesz. Wprowadź `sensible-editor simple_bash.sh`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć **nano** edytora. Upewnij się, że poprawnie skopiować pliku całego init chmury szczególnie pierwszego wiersza.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Przed wdrożeniem tego obrazu, należy utworzyć nową grupę zasobów o [Tworzenie grupy az](/cli/azure/group#az_group_create) polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz, utwórz maszynę Wirtualną z [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) i określić plik skryptu bash z `--custom-data simple_bash.sh` w następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Sprawdź bash skryptu zostało uruchomione.
SSH jako publiczny adres IP wyświetlany w danych wyjściowych z poprzedniego polecenia maszyny wirtualnej. Wprowadź własne **publicznego adresu IP** w następujący sposób:

```bash
ssh <publicIpAddress>
```

Zmień na **/tmp** katalogu i sprawdź, czy plik myScript.txt istnieje i ma odpowiedni tekst wewnątrz niej.  Jeśli nie, można sprawdzić **/var/log/cloud-init.log** więcej szczegółów.  Wyszukaj następujący wpis:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Kolejne kroki
Przykłady dodatkowe init chmury zmian konfiguracji zobacz następujące tematy:
 
- [Dodaj dodatkowe użytkownika w systemie Linux na maszynie Wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety po pierwszym uruchomieniu komputera](cloudinit-update-vm.md)
- [Zmień lokalną nazwą hosta maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizuj pliki konfiguracji i wstrzyknąć kluczy](tutorial-automate-vm-deployment.md)