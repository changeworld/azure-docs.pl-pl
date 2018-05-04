---
title: Przygotowywanie obrazu maszyny Wirtualnej platformy Azure do użycia z chmury init | Dokumentacja firmy Microsoft
description: Jak przygotować istniejącego obrazu maszyny Wirtualnej platformy Azure do wdrożenia z inicjowaniem chmury
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
ms.openlocfilehash: dda444e77f588cd1ba5989b393e9a3987241ef9a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Przygotowanie do użycia z chmury init istniejącego obrazu maszyny Wirtualnej systemu Linux platformy Azure
W tym artykule przedstawiono sposób pobrać istniejącej maszyny wirtualnej platformy Azure i przygotowania go ponownie wdrożonym i gotowe do użycia chmury init. Obraz wynikowy może służyć do wdrażania nowej maszyny wirtualnej lub zestawy skalowania maszyn wirtualnych — w jednej z nich można następnie można dodatkowo dostosowywać przez init chmury w czasie wdrażania.  Skrypty te init chmury są uruchamiane po pierwszym uruchomieniu komputera po zasoby zostały udostępnione przez platformę Azure. Aby uzyskać więcej informacji na temat chmury inicjowania działania natywnie Azure i obsługiwanych dystrybucjach systemu Linux, zobacz [init chmury — omówienie](using-cloud-init.md)

## <a name="prerequisites"></a>Wymagania wstępne
Ten dokument przyjęto założenie, że masz już działa Azure maszyny wirtualnej uruchomiona obsługiwana wersja systemu operacyjnego Linux. Został już skonfigurowany maszyny do własnych potrzeb, zainstalowane wszystkie wymagane moduły, przetwarzane wszystkie wymagane aktualizacje i zostały przetestowane na zapewnienie spełnia on wymagania. 

## <a name="preparing-rhel-74--centos-74"></a>Przygotowywanie RHEL 7.4 / CentOS 7.4
Konieczne SSH do maszyny Wirtualnej systemu Linux i uruchom następujące polecenia, aby można było zainstalować init chmury.

```bash
sudo yum install -y cloud-init gdisk
sudo yum check-update cloud-init -y
sudo yum install cloud-init -y
```

Aktualizacja `cloud_init_modules` sekcji `/etc/cloud/cloud.cfg` uwzględnienie następujących modułów:
```bash
- disk_setup
- mounts
```

Oto przykład jakie ogólnego przeznaczenia `cloud_init_modules` wygląda sekcji.
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
Liczba zadań związanych z udostępnianiem i Obsługa tymczasowych dyski muszą zostać zaktualizowane w `/etc/waagent.conf`. Uruchom następujące polecenia, aby zaktualizować odpowiednie ustawienia. 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
```
Zezwalaj tylko na platformie Azure jako źródła danych dla agenta systemu Linux platformy Azure przez utworzenie nowego pliku `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` za pomocą dowolnego edytora następujące wiersze:

```bash
# This configuration file is provided by the WALinuxAgent package.
datasource_list: [ Azure ]
```

Dodaj konfigurację do adresu usterki rejestracji oczekujących nazwy hosta.
```bash
cat > /etc/cloud/hostnamectl-wrapper.sh <<\EOF
#!/bin/bash -e
if [[ -n $1 ]]; then
  hostnamectl set-hostname $1
else
  hostname
fi
EOF

chmod 0755 /etc/cloud/hostnamectl-wrapper.sh

cat > /etc/cloud/cloud.cfg.d/90-hostnamectl-workaround-azure.cfg <<EOF
# local fix to ensure hostname is registered
datasource:
  Azure:
    hostname_bounce:
      hostname_command: /etc/cloud/hostnamectl-wrapper.sh
EOF
```

Jeśli chcesz zmienić konfigurację pliku wymiany dla nowych obrazów przy użyciu chmury init istniejącego obrazu platformy Azure został skonfigurowany pliku wymiany, musisz usunąć istniejący plik wymiany.

RedHat podstawie obrazów — postępuj zgodnie z instrukcjami następujące RedHat dokument wyjaśniający, jak [Usuń plik wymiany](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/5/html/Deployment_Guide/s2-swap-removing-file.html).

CentOS obrazów z swapfile włączone można uruchomić następujące polecenie, aby wyłączyć swapfile:
```bash
sudo swapoff /mnt/resource/swapfile
```

Upewnij się, swapfile odwołanie zostanie usunięte z `/etc/fstab` -powinien on wyglądać podobnie jak następujące dane wyjściowe:
```text
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Aby zaoszczędzić miejsce, a następnie usuń plik wymiany można uruchom następujące polecenie:
```bash
rm /mnt/resource/swapfile
```
## <a name="extra-step-for-cloud-init-prepared-image"></a>Dodatkowy krok w przypadku inicjowania chmury przygotowany obraz
> [!NOTE]
> Jeśli obraz został wcześniej **init chmury** obrazu przygotowany i jest skonfigurowany, należy wykonać następujące kroki.

Następujące trzy polecenia są używane tylko w przypadku dostosowywania jako nowy obraz źródłowy specjalistyczne maszyny Wirtualnej został wcześniej zainicjowane przez init chmury.  Jest konieczne uruchamianie tych, jeśli obraz został skonfigurowany przy użyciu agenta systemu Linux platformy Azure.

```bash
sudo rm -rf /var/lib/cloud/instances/* 
sudo rm -rf /var/log/cloud-init*
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Finalizowanie agenta systemu Linux ustawienie 
Wszystkie obrazy platformy Azure został zainstalowany, niezależnie od tego, jeśli zostały skonfigurowane przez init chmury, lub nie Agent systemu Linux platformy Azure.  Uruchom następujące polecenie, aby zakończyć anulowania obsługi użytkowników z komputera z systemem Linux. 

```bash
sudo waagent -deprovision+user -force
```

Aby uzyskać więcej informacji o poleceniach deprovision agenta systemu Linux platformy Azure, zobacz [agenta systemu Linux Azure](agent-user-guide.md) więcej szczegółów.

Zakończyć sesję SSH, a następnie z powłoki bash, uruchom następujące polecenia AzureCLI deallocate generalize i utworzyć nowy obraz maszyny Wirtualnej platformy Azure.  Zastąp `myResourceGroup` i `sourceVmName` odpowiednie informacje w czasie wykonywania odbicia sourceVM Twojego.

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Kolejne kroki
Przykłady dodatkowe init chmury zmian konfiguracji zobacz następujące tematy:
 
- [Dodaj dodatkowe użytkownika w systemie Linux na maszynie Wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety po pierwszym uruchomieniu komputera](cloudinit-update-vm.md)
- [Zmień lokalną nazwą hosta maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizuj pliki konfiguracji i wstrzyknąć kluczy](tutorial-automate-vm-deployment.md)
