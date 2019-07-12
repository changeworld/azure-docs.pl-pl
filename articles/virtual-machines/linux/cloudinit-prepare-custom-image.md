---
title: Przygotowywanie obrazu maszyny Wirtualnej platformy Azure do użycia z pakietu cloud-init | Dokumentacja firmy Microsoft
description: Jak przygotować istniejącego obrazu maszyny Wirtualnej platformy Azure do wdrożenia przy użyciu pakietu cloud-init
services: virtual-machines-linux
documentationcenter: ''
author: danis
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 06/24/2019
ms.author: danis
ms.openlocfilehash: 1f9f6042b52c722280a8227754960ffb270e94b8
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668246"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Przygotowywanie istniejącego obrazu maszyny Wirtualnej platformy Azure z systemem Linux do użycia z pakietu cloud-init
Ten artykuł pokazuje, jak pobrać istniejącej maszyny wirtualnej platformy Azure i przygotować je do można ponownie wdrożonym i gotowe do użycia pakietu cloud-init. Obraz wynikowy może służyć do wdrażania nowej maszyny wirtualnej lub zestawy skalowania maszyn wirtualnych — które można następnie można dodatkowo dostosowywać przez pakiet cloud-init w czasie wdrażania.  Skrypty te pakietu cloud-init są uruchamiane podczas pierwszego rozruchu po zasoby zostały udostępnione przez platformę Azure. Aby uzyskać więcej informacji o tym, jak pakietu cloud-init działa natywnie na platformie Azure i obsługiwane dystrybucje systemu Linux, zobacz [Omówienie pakietu cloud-init](using-cloud-init.md)

## <a name="prerequisites"></a>Wymagania wstępne
W tym dokumencie przyjęto założenie, że masz już pracę maszyn wirtualnych z obsługiwaną wersją systemu operacyjnego Linux. Zostały już skonfigurowane maszyny do własnych potrzeb, zainstalować wymagane moduły, przetworzone wszystkie wymagane aktualizacje i zostały przetestowane na zapewnienie spełnia Twoje wymagania. 

## <a name="preparing-rhel-76--centos-76"></a>Przygotowywanie RHEL 7.6 / CentOS 7.6
Trzeba SSH z maszyną wirtualną systemu Linux i uruchom następujące polecenia, aby można było zainstalować pakiet cloud-init.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

Aktualizacja `cloud_init_modules` sekcji `/etc/cloud/cloud.cfg` do uwzględnienia w przypadku następujących modułów:
```bash
- disk_setup
- mounts
```

Oto przykład jakie ogólnego przeznaczenia `cloud_init_modules` sekcja wygląda następująco.
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
Liczba zadań związanych z udostępnianiem i obsługa efemeryczne dyski muszą zostać zaktualizowane w `/etc/waagent.conf`. Uruchom następujące polecenia, aby zaktualizować odpowiednie ustawienia. 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cloud-init clean
```

Zezwalaj tylko usługi Azure jako źródła danych dla agenta systemu Linux dla platformy Azure, tworząc nowy plik `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` za pomocą dowolnego edytora o następujący wiersz:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
```

Jeśli Twojego istniejącego obrazu platformy Azure ma skonfigurowane pliku wymiany, i chcesz zmienić konfigurację pliku wymiany nowych obrazów przy użyciu pakietu cloud-init, którą chcesz usunąć istniejący plik wymiany.

Dla firmy Red Hat opartych na obrazach - postępuj zgodnie z instrukcjami następujących firmy Red Hat dokumentu wyjaśniające, jak [Usuń plik wymiany](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

CentOS obrazów za pomocą pliku wymiany jest włączone można uruchom następujące polecenie, aby wyłączyć pliku wymiany:
```bash
sudo swapoff /mnt/resource/swapfile
```

Upewnij się, odwołanie do pliku wymiany jest usuwany z `/etc/fstab` -powinien on wyglądać podobnie jak poniższe dane wyjściowe:
```text
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Aby zaoszczędzić miejsce i usuń plik wymiany można uruchom następujące polecenie:
```bash
rm /mnt/resource/swapfile
```
## <a name="extra-step-for-cloud-init-prepared-image"></a>Dodatkowego kroku przygotowany obraz pakietu cloud-init
> [!NOTE]
> Jeśli obraz został wcześniej **pakietu cloud-init** obrazu przygotowany i jest skonfigurowany, należy wykonać następujące czynności.

Następujące trzy polecenia są używane tylko w przypadku dostosowywania jako nowy obraz źródłowy wyspecjalizowane maszyny Wirtualnej zostało wcześniej przez pakiet cloud-init.  NIE trzeba przeprowadzać te, jeśli obraz został skonfigurowany przy użyciu agenta systemu Linux dla platformy Azure.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Finalizowanie agenta systemu Linux ustawienie 
Wszystkie obrazy platformy Azure ma agenta systemu Linux platformy Azure, które są zainstalowane, niezależnie od tego, jeśli została skonfigurowana przez pakiet cloud-init, czy nie.  Uruchom następujące polecenie, aby zakończyć cofania aprowizacji użytkowników z maszyny z systemem Linux. 

```bash
sudo waagent -deprovision+user -force
```

Aby uzyskać więcej informacji na temat polecenia anulowania aprowizacji agenta systemu Linux platformy Azure, zobacz [agenta systemu Linux platformy Azure](../extensions/agent-linux.md) Aby uzyskać więcej informacji.

Zamknij sesję SSH, a następnie z powłoki bash, uruchom następujące polecenia codziennych cofnięcie przydziału i uogólnianie Utwórz nowy obraz maszyny Wirtualnej platformy Azure.  Zastąp `myResourceGroup` i `sourceVmName` odpowiednimi informacjami odzwierciedlający Twoje sourceVM.

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Kolejne kroki
Przykłady dodatkowe pakietu cloud-init zmian konfiguracji zobacz następujące tematy:
 
- [Dodaj dodatkowe użytkownika w systemie Linux do maszyny Wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety podczas pierwszego rozruchu](cloudinit-update-vm.md)
- [Zmień lokalną nazwą hosta maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizować pliki konfiguracji i wstawić kluczy](tutorial-automate-vm-deployment.md)
