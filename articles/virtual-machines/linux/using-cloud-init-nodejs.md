---
title: Dostosowywanie maszyny Wirtualnej systemu Linux podczas tworzenia na platformie Azure za pomocą init chmurze | Dokumentacja firmy Microsoft
description: Jak użyć init chmury w celu dostosowania Maszynę wirtualną systemu Linux podczas tworzenia z interfejsu wiersza polecenia platformy Azure w wersji 1.0
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: v-livech
ms.openlocfilehash: 2e9182a18a2827ed7f54f5fd042e5934b3b1fd5c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30918442"
---
# <a name="use-cloud-init-to-customize-a-linux-vm-during-creation-with-the-azure-cli-10"></a>Użyć init chmury w celu dostosowania Maszynę wirtualną systemu Linux podczas tworzenia z interfejsu wiersza polecenia platformy Azure w wersji 1.0
W tym artykule przedstawiono sposób wprowadzania skryptu init chmury, ustawiania nazwy hosta, zainstalowano aktualizację pakietów i zarządzanie kontami użytkowników.  Skrypty chmurze init wywoływane podczas tworzenia maszyny Wirtualnej z wiersza polecenia platformy Azure.  Wykonanie czynności opisanych w tym artykule wymaga:

* konta platformy Azure ([skorzystaj z bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/));
* dostępu do [interfejsu wiersza polecenia platformy Azure](../../cli-install-nodejs.md) (po zalogowaniu przy użyciu `azure login`).
* Interfejs wiersza polecenia platformy Azure *musi działać w* trybie usługi Azure Resource Manager`azure config mode arm`.

## <a name="cli-versions-to-complete-the-task"></a>Wersje interfejsu wiersza polecenia umożliwiające wykonanie zadania
Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

- [Azure CLI 1.0](#quick-commands) — nasze interfejsu wiersza polecenia dla klasycznego i zasobów zarządzania wdrażania modeli (w tym artykule)
- [Interfejs wiersza polecenia platformy Azure w wersji 2.0](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — nasz interfejs wiersza polecenia nowej generacji dla modelu wdrażania na potrzeby zarządzania zasobami

## <a name="quick-commands"></a>Szybkie polecenia
Utwórz skrypt init.txt chmury, który ustawia nazwę hosta, wszystkie pakiety aktualizacji i dodaje użytkownika sudo do systemu Linux.

```sh
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```
Utwórz grupę zasobów do maszyn wirtualnych do uruchomienia.

```azurecli
azure group create myResourceGroup westus
```

Utwórz Maszynę wirtualną systemu Linux przy użyciu chmury init ją skonfigurować podczas rozruchu.

```azurecli
azure vm create \
  -g myResourceGroup \
  -n myVM \
  -l westus \
  -y Linux \
  -f myVMnic \
  -F myVNet \
  -P 10.0.0.0/22 \
  -j mySubnet \
  -k 10.0.0.0/24 \
  -Q canonical:ubuntuserver:14.04.2-LTS:latest \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -C cloud-init.txt
```

## <a name="detailed-walkthrough"></a>Szczegółowy przewodnik
### <a name="introduction"></a>Wprowadzenie
Podczas uruchamiania nowej maszyny Wirtualnej systemu Linux, w przypadku uzyskiwania standard maszyny Wirtualnej systemu Linux niczego nie dostosowane lub gotowy do własnych potrzeb. [Init chmury](https://cloudinit.readthedocs.org) jest standardowym sposobem wstrzyknąć skryptu lub ustawień konfiguracyjnych do tej maszyny Wirtualnej systemu Linux, jak jest uruchamiany dla po raz pierwszy.

Na platformie Azure, aby wprowadzić zmiany na Maszynę wirtualną systemu Linux, ponieważ jest on są trzy różne sposoby wdrażania lub rozruchu.

* Wstaw skrypty przy użyciu inicjowania chmury.
* Wstaw skrypty przy użyciu platformy Azure [rozszerzenia VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Szablonu platformy Azure przy użyciu inicjowania chmury.
* Przy użyciu szablonu Azure [CustomScriptExtention](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Do dodania skryptów w dowolnym momencie po rozruchu:

* SSH do uruchamiania poleceń, bezpośrednio
* Wstaw skrypty przy użyciu platformy Azure [rozszerzenia VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), imperatively lub szablonu platformy Azure
* Narzędzia zarządzania konfiguracją, takie jak Ansible, wartość Zaburzająca, Chef i Puppet.

> [!NOTE]
> : Rozszerzenia VMAccess wykonuje skryptu, tak jak w głównym w taki sam sposób, przy użyciu protokołu SSH.  Jednak przy użyciu rozszerzenia maszyny Wirtualnej umożliwia kilka funkcji tej oferty Azure, które mogą być przydatne, w zależności od danego scenariusza.
> 
> 

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Dostępność init chmury na maszynie Wirtualnej Azure szybkie — tworzenie aliasów obrazu:
| Alias | Wydawca | Oferta | SKU | Wersja | cloud-init |
|:--- |:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |najnowsza |nie |
| CoreOS |CoreOS |CoreOS |Stable |najnowsza |tak |
| Debian |credativ |Debian |8 |najnowsza |nie |
| openSUSE |SUSE |openSUSE |13.2 |najnowsza |nie |
| RHEL |Redhat |RHEL |7.2 |najnowsza |nie |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |najnowsza |tak |

Firma Microsoft współpracuje z partnerami uzyskanie init chmury uwzględnione i Praca w obrazach, zapewniające na platformie Azure.

## <a name="adding-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Dodawanie skryptu init chmury do tworzenia maszyny Wirtualnej z wiersza polecenia platformy Azure
Aby uruchomić skrypt init chmury, podczas tworzenia maszyny Wirtualnej na platformie Azure, określ plik init chmury przy użyciu interfejsu wiersza polecenia Azure `--custom-data` przełącznika.

Utwórz grupę zasobów do maszyn wirtualnych do uruchomienia.

```azurecli
azure group create myResourceGroup westus
```

Utwórz Maszynę wirtualną systemu Linux przy użyciu chmury init ją skonfigurować podczas rozruchu.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubnet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud-init.txt
```

## <a name="creating-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Tworzenie skrypt init chmurze hostname maszyny wirtualnej systemu Linux
Jedno z ustawień najprostszym i najważniejszych żadnej maszyny Wirtualnej systemu Linux jest nazwą hosta. Firma Microsoft łatwo tę można skonfigurować przy użyciu inicjowania chmury za pomocą tego skryptu.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>Przykładowy skrypt init chmury o nazwie `cloud_config_hostname.txt`.
```sh
#cloud-config
hostname: myservername
```

Podczas wstępnego uruchamiania maszyny Wirtualnej, ten skrypt init chmury ustawia nazwę hosta `myservername`.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_hostname.txt
```

Logowania i sprawdź nazwę hosta nowej maszyny Wirtualnej.

```bash
ssh myVM
hostname
myservername
```

## <a name="creating-a-cloud-init-script-to-update-linux"></a>Tworzenie chmury init skryptu aktualizującego systemu Linux
Dla bezpieczeństwa ma maszyny Wirtualnej systemu Ubuntu aktualizacji po pierwszym uruchomieniu komputera.  Przy użyciu chmury init robimy można przy użyciu skryptu wykonaj, w zależności od używanych dystrybucji systemu Linux.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Przykładowy skrypt init chmury `cloud_config_apt_upgrade.txt` Debian rodziny
```sh
#cloud-config
apt_upgrade: true
```

Po uruchomieniu systemu Linux, wszystkie zainstalowane pakiety są aktualizowane za pośrednictwem `apt-get`.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_apt_upgrade.txt
```

Logowania i sprawdź wszystkie pakiety zostały zaktualizowane.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="creating-a-cloud-init-script-to-add-a-user-to-linux"></a>Tworzenie skryptu init chmury, aby dodać użytkownika do systemu Linux
Jedno z pierwszym zadań na nowej maszyny Wirtualnej z systemem Linux jest dodać użytkownika dla siebie lub unikać `root`. SSH klucze są najlepsze rozwiązanie dotyczące zabezpieczeń i użyteczność i są dodawane do `~/.ssh/authorized_keys` plików za pomocą tego skryptu init chmury.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Przykładowy skrypt init chmury `cloud_config_add_users.txt` Debian rodziny
```sh
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Po uruchomieniu systemu Linux, wyświetlani użytkownicy są tworzone i dodawane do grupy sudo.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_add_users.txt
```

Logowania i Sprawdź nowo utworzonego użytkownika.

```bash
ssh myVM
cat /etc/group
```

Dane wyjściowe

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>Następne kroki
Init chmurze staje się coraz standardowy sposób zmodyfikować maszyny Wirtualnej systemu Linux na rozruchu. Platforma Azure ma rozszerzenia maszyny Wirtualnej, które umożliwiają modyfikowanie użytkownika LinuxVM przy rozruchu lub jest uruchomiona. Na przykład można użyć Azure VMAccessExtension można zresetować informacje o użytkowniku lub SSH uruchomionej maszyny Wirtualnej. Z inicjowaniem chmury może być konieczne ponowne uruchomienie w celu zresetowania hasła.

[Temat funkcji i rozszerzeń maszyny wirtualnej](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Zarządzanie użytkownikami, SSH i wyboru lub napraw dyski na maszynach wirtualnych systemu Linux platformy Azure przy użyciu rozszerzenia VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

