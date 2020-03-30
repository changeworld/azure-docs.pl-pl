---
title: Przygotowywanie obrazu maszyny Wirtualnej platformy Azure do użycia z obiektami w chmurze
description: Jak przygotować istniejący obraz maszyny Wirtualnej platformy Azure do wdrożenia za pomocą cloud-init
author: danis
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: danis
ms.openlocfilehash: fef41f4dc90c03e3efbe4c8a75e495c26eec64b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066816"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Przygotowywanie istniejącego obrazu maszyny Wirtualnej platformy Azure systemu Linux do użytku z obiektami w chmurze
W tym artykule pokazano, jak wziąć istniejącą maszynę wirtualną platformy Azure i przygotować ją do ponownego wdrożenia i gotowości do użycia init w chmurze. Wynikowy obraz może służyć do wdrażania nowej maszyny wirtualnej lub zestawy skalowania maszyny wirtualnej — z których każdy może być następnie dodatkowo dostosowane przez cloud-init w czasie wdrażania.  Te skrypty init w chmurze są uruchamiane przy pierwszym rozruchu po zainicjowaniu zasobów przez platformę Azure. Aby uzyskać więcej informacji na temat działania cloud-init na platformie Azure i obsługiwanych dystrybucji systemu Linux, zobacz [omówienie cloud-init](using-cloud-init.md)

## <a name="prerequisites"></a>Wymagania wstępne
W tym dokumencie przyjęto założenie, że masz już uruchomionej maszyny wirtualnej platformy Azure z obsługiwaną wersją systemu operacyjnego Linux. Urządzenie zostało już skonfigurowane zgodnie z potrzebami, zainstalowałeś wszystkie wymagane moduły, przetworzyłeś wszystkie wymagane aktualizacje i przetestowałeś ją, aby upewnić się, że spełnia Twoje wymagania. 

## <a name="preparing-rhel-76--centos-76"></a>Przygotowanie RHEL 7.6 / CentOS 7.6
Musisz SSH do maszyny Wirtualnej systemu Linux i uruchomić następujące polecenia w celu zainstalowania cloud-init.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

Zaktualizuj sekcję, `cloud_init_modules` `/etc/cloud/cloud.cfg` aby uwzględnić następujące moduły:

```bash
- disk_setup
- mounts
```

Oto przykład tego, jak wygląda `cloud_init_modules` sekcja ogólnego przeznaczenia.

```bash
cloud_init_modules:
 - migrator
 - bootcmd
 - write-files
 - growpart
 - resizefs
 - disk_setup
 - mounts
 - set_hostname
 - update_hostname
 - update_etc_hosts
 - rsyslog
 - users-groups
 - ssh
```

W obszarze `/etc/waagent.conf`. Uruchom następujące polecenia, aby zaktualizować odpowiednie ustawienia.

```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cloud-init clean
```

Zezwalaj tylko na platformę Azure jako źródło danych `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` dla agenta systemu Azure Linux, tworząc nowy plik przy użyciu wybranego edytora z następującym wierszem:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
```

Jeśli istniejący obraz platformy Azure ma skonfigurowany plik wymiany i chcesz zmienić konfigurację pliku wymiany dla nowych obrazów przy użyciu cloud-init, należy usunąć istniejący plik wymiany.

W przypadku obrazów opartych na red hat - postępuj zgodnie z instrukcjami w poniższym dokumencie Red Hat wyjaśniającym, jak [usunąć plik wymiany](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

W przypadku obrazów CentOS z włączonym plikiem wymiany można uruchomić następujące polecenie, aby wyłączyć plik wymiany:

```bash
sudo swapoff /mnt/resource/swapfile
```

Upewnij się, że odwołanie `/etc/fstab` do pliku swap jest usuwany z - powinien wyglądać mniej więcej następująco:

```output
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Aby zaoszczędzić miejsce i usunąć plik wymiany, można uruchomić następujące polecenie:

```bash
rm /mnt/resource/swapfile
```

## <a name="extra-step-for-cloud-init-prepared-image"></a>Dodatkowy krok dla przygotowanego obrazu w chmurze
> [!NOTE]
> Jeśli obraz był wcześniej obraz przygotowany i skonfigurowany **w chmurze,** należy wykonać następujące kroki.

Następujące trzy polecenia są używane tylko wtedy, gdy maszyna wirtualna, którą dostosowujesz do nowego obrazu źródłowego specjalistyczne wcześniej aprowizował przez cloud-init.  NIE trzeba ich uruchamiać, jeśli obraz został skonfigurowany przy użyciu agenta systemu Azure Linux.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Finalizowanie ustawienia agenta systemu Linux 
Wszystkie obrazy platformy Azure platformy mają zainstalowany agent systemu Azure Linux, niezależnie od tego, czy został skonfigurowany przez cloud-init, czy nie.  Uruchom następujące polecenie, aby zakończyć anulowanie obsługi administracyjnej użytkownika z komputera z systemem Linux. 

```bash
sudo waagent -deprovision+user -force
```

Aby uzyskać więcej informacji na temat poleceń deprovision agenta systemu Azure Linux, zobacz [Agent systemu Azure Linux, aby](../extensions/agent-linux.md) uzyskać więcej informacji.

Zamknij sesję SSH, a następnie z powłoki bash, uruchom następujące polecenia AzureCLI, aby zdeterminować, uogólnić i utworzyć nowy obraz maszyny wirtualnej platformy Azure.  Wymień `myResourceGroup` i `sourceVmName` odpowiednie informacje odzwierciedlające źródłoVM.

```azurecli
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkowe przykłady zmian konfiguracji w chmurze, zobacz następujące elementy:
 
- [Dodawanie dodatkowego użytkownika systemu Linux do maszyny Wirtualnej](cloudinit-add-user.md)
- [Uruchamianie menedżera pakietów w celu zaktualizowania istniejących pakietów przy pierwszym rozruchu](cloudinit-update-vm.md)
- [Zmienianie nazwy hosta lokalnego maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Instalowanie pakietu aplikacji, aktualizowanie plików konfiguracyjnych i wstrzykiwanie kluczy](tutorial-automate-vm-deployment.md)
