---
title: Uruchamianie skryptu bash na maszynie Wirtualnej systemu Linux na platformie Azure za pomocą funkcji cloud-init
description: Jak używać cloud-init do uruchamiania skryptu bash na maszynie Wirtualnej systemu Linux podczas tworzenia za pomocą interfejsu wiersza polecenia platformy Azure
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: e2f19ceb6c7f19ba749b46a3553036587be6a71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969210"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Uruchamianie skryptu bash na maszynie Wirtualnej systemu Linux na platformie Azure za pomocą funkcji cloud-init
W tym artykule pokazano, jak używać [cloud-init](https://cloudinit.readthedocs.io) do uruchamiania istniejącego skryptu bash na maszynie wirtualnej systemu Linux (VM) lub zestawów skalowania maszyny wirtualnej (VMSS) w czasie inicjowania obsługi administracyjnej na platformie Azure. Te skrypty init w chmurze są uruchamiane przy pierwszym rozruchu po zainicjowaniu zasobów przez platformę Azure. Aby uzyskać więcej informacji na temat działania cloud-init na platformie Azure i obsługiwanych dystrybucji systemu Linux, zobacz [omówienie cloud-init](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>Uruchamianie skryptu bash z cloud-init
Z cloud-init nie trzeba konwertować istniejących skryptów do chmury config, cloud-init akceptuje wiele typów danych wejściowych, z których jeden jest skrypt bash.

Jeśli używasz rozszerzenia Azure skryptu niestandardowego systemu Linux do uruchamiania skryptów, możesz przeprowadzić ich migrację w celu użycia funkcji cloud-init. Jednak rozszerzenia platformy Azure mają zintegrowane raportowanie do alertów o błędach skryptów, wdrożenie obrazu init w chmurze NIE zakończy się niepowodzeniem, jeśli skrypt nie powiedzie się.

Aby zobaczyć tę funkcję w akcji, utwórz prosty skrypt bash do testowania. Podobnie jak plik `#cloud-config` cloud-init, ten skrypt musi być lokalny, gdzie będzie uruchomiony AzureCLI polecenia do aprowizowania maszyny wirtualnej.  W tym przykładzie utwórz plik w usłudze Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor simple_bash.sh`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć **edytora nano.** Upewnij się, że cały plik cloud-init jest kopiowany poprawnie, zwłaszcza w pierwszym wierszu.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Przed wdrożeniem tego obrazu należy utworzyć grupę zasobów za pomocą polecenia [tworzenie grupy az.](/cli/azure/group) Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz utwórz maszynę wirtualną z [az vm](/cli/azure/vm) `--custom-data simple_bash.sh` utworzyć i określić plik skryptu bash w następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Sprawdź, czy skrypt bash został uruchomiony
SSH do publicznego adresu IP maszyny Wirtualnej pokazane w danych wyjściowych z poprzedniego polecenia. Wpisz swój własny **publicIpAddress** w następujący sposób:

```bash
ssh <publicIpAddress>
```

Zmień katalog **/tmp** i sprawdź, czy plik myScript.txt istnieje i zawiera odpowiedni tekst.  Jeśli tak nie jest, można sprawdzić **/var/log/cloud-init.log, aby** uzyskać więcej szczegółów.  Wyszukaj następujący wpis:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkowe przykłady zmian konfiguracji w chmurze, zobacz następujące elementy:
 
- [Dodawanie dodatkowego użytkownika systemu Linux do maszyny Wirtualnej](cloudinit-add-user.md)
- [Uruchamianie menedżera pakietów w celu zaktualizowania istniejących pakietów przy pierwszym rozruchu](cloudinit-update-vm.md)
- [Zmienianie nazwy hosta lokalnego maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Instalowanie pakietu aplikacji, aktualizowanie plików konfiguracyjnych i wstrzykiwanie kluczy](tutorial-automate-vm-deployment.md)
