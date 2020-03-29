---
title: Używanie funkcji cloud-init na maszynie wirtualnej z systemem Linux na platformie Azure
description: Jak używać cloud-init do aktualizowania i instalowania pakietów na maszynie Wirtualnej z systemem Linux podczas tworzenia za pomocą interfejsu wiersza polecenia platformy Azure
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/20/2018
ms.author: cynthn
ms.openlocfilehash: 7b7a03572a001fc6d5114635b33510f1a4b1bc70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969155"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Aktualizowanie i instalowanie pakietów na maszynie Wirtualnej z systemem Linux na platformie Azure za pomocą funkcji cloud-init
W tym artykule pokazano, jak używać [cloud-init](https://cloudinit.readthedocs.io) do aktualizowania pakietów na maszynie wirtualnej systemu Linux (VM) lub zestawów skalowania maszyny wirtualnej w czasie inicjowania obsługi administracyjnej na platformie Azure. Te skrypty init w chmurze są uruchamiane przy pierwszym rozruchu po zainicjowaniu zasobów przez platformę Azure. Aby uzyskać więcej informacji na temat działania cloud-init na platformie Azure i obsługiwanych dystrybucji systemu Linux, zobacz [omówienie cloud-init](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Aktualizowanie maszyny Wirtualnej za pomocą initu w chmurze
Ze względów bezpieczeństwa można skonfigurować maszynę wirtualną do stosowania najnowszych aktualizacji przy pierwszym rozruchu. Ponieważ cloud-init działa w różnych dystrybucjach linuksa, nie ma potrzeby określania `apt` ani `yum` menedżera pakietów. Zamiast tego można `package_upgrade` zdefiniować i niech proces cloud-init określić odpowiedni mechanizm dla dystrybucji w użyciu. Ten przepływ pracy umożliwia korzystanie z tych samych skryptów init chmury w dystrybucjach.

Aby wyświetlić proces uaktualniania w działaniu, utwórz plik w bieżącej powłoce o nazwie *cloud_init_upgrade.txt* i wklej następującą konfigurację. W tym przykładzie utwórz plik w usłudze Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud_init_upgrade.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć **edytora nano.** Upewnij się, że cały plik cloud-init jest kopiowany poprawnie, zwłaszcza w pierwszym wierszu.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Przed wdrożeniem tego obrazu należy utworzyć grupę zasobów za pomocą polecenia [tworzenie grupy az.](/cli/azure/group) Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz utwórz maszynę wirtualną z [az vm utworzyć](/cli/azure/vm) `--custom-data cloud_init_upgrade.txt` i określić plik cloud-init w następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH do publicznego adresu IP maszyny Wirtualnej pokazane w danych wyjściowych z poprzedniego polecenia. Wpisz swój własny **publicIpAddress** w następujący sposób:

```bash
ssh <publicIpAddress>
```

Uruchom narzędzie do zarządzania pakietami i sprawdź dostępność aktualizacji.

```bash
sudo yum update
```

Ponieważ chmura init sprawdzone i zainstalowane aktualizacje podczas rozruchu, nie powinno być żadnych dodatkowych aktualizacji do zastosowania.  Widzisz proces aktualizacji, liczbę zmienionych pakietów, a `httpd` także `yum history` instalację przez uruchomienie i przejrzenie danych wyjściowych podobnych do poniższego.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkowe przykłady zmian konfiguracji w chmurze, zobacz następujące elementy:
 
- [Dodawanie dodatkowego użytkownika systemu Linux do maszyny Wirtualnej](cloudinit-add-user.md)
- [Uruchamianie menedżera pakietów w celu zaktualizowania istniejących pakietów przy pierwszym rozruchu](cloudinit-update-vm.md)
- [Zmienianie nazwy hosta lokalnego maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Instalowanie pakietu aplikacji, aktualizowanie plików konfiguracyjnych i wstrzykiwanie kluczy](tutorial-automate-vm-deployment.md)
