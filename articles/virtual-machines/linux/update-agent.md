---
title: Zaktualizuj agenta systemu Linux platformy Azure z usługi GitHub | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zaktualizować agenta systemu Linux platformy Azure dla maszyny Wirtualnej systemu Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: danis
ms.openlocfilehash: 21165621478cc413b34a81345d6d3945f78148c0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31424888"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Jak zaktualizować agenta systemu Linux platformy Azure na maszynie Wirtualnej

Aby zaktualizować Twojej [agenta systemu Linux Azure](https://github.com/Azure/WALinuxAgent) na Maszynę wirtualną systemu Linux na platformie Azure, trzeba już mieć:

- Uruchomionej maszyny Wirtualnej systemu Linux na platformie Azure.
- Połączenie z tą maszyną Wirtualną z systemem Linux przy użyciu protokołu SSH.

Należy zawsze sprawdzić, czy pakiet w repozytorium distro Linux najpierw. Możliwe jest najnowszą wersję, jednak włączenie funkcji Aktualizacje automatyczne będą upewnij się, że Agent systemu Linux będzie zawsze Pobierz najnowszą aktualizację nie mogą być dostępne pakietu. Czy ma problemy z instalacją menedżerów pakietu, należy zwrócić pomocy technicznej od dostawcy distro.

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Obsługa agenta minimalna maszyn wirtualnych na platformie Azure
Sprawdź [obsługę agentów maszyny wirtualnej na platformie Azure w wersji co najmniej](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) przed kontynuowaniem.

## <a name="updating-the-azure-linux-agent"></a>Aktualizowanie Azure agenta systemu Linux

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Pamięć podręczną pakietów aktualizacji

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że jest włączone automatyczne aktualizacje

Najpierw sprawdź, czy jest włączony:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli to pole wyboru jest wyświetlane, jest włączona:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć uruchamianie:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Uruchom ponownie usługę agenta waagent

#### <a name="restart-agent-for-1404"></a>Uruchom ponownie agenta dla 14.04

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Uruchom ponownie agenta dla 16.04 / 17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-wheezy"></a>Debian 7 "Wheezy"

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Pamięć podręczną pakietów aktualizacji

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Włącz Aktualizacje automatyczne agenta
Ta wersja Debian nie ma wersji > = 2.0.16, dlatego aktualizacje automatyczne jest niedostępna dla niego. Dane wyjściowe z powyższego polecenia wyświetli, jeśli pakiet jest aktualny.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Joasia" / Debian 9 "Stretch"

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>Pamięć podręczną pakietów aktualizacji

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo apt-get install waagent
```
#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że jest włączone automatyczne aktualizacje 

Najpierw sprawdź, czy jest włączony:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli to pole wyboru jest wyświetlane, jest włączona:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć uruchamianie:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Uruchom ponownie usługę agenta waagent

```
sudo systemctl restart walinuxagent.service
```

## <a name="redhat--centos"></a>RedHat / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Sprawdzanie dostępności aktualizacji

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że jest włączone automatyczne aktualizacje 

Najpierw sprawdź, czy jest włączony:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli to pole wyboru jest wyświetlane, jest włączona:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć uruchamianie:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Uruchom ponownie usługę agenta waagent

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Sprawdzanie dostępności aktualizacji

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że jest włączone automatyczne aktualizacje 

Najpierw sprawdź, czy jest włączony:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli to pole wyboru jest wyświetlane, jest włączona:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć uruchamianie:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Uruchom ponownie usługę agenta waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 Z DODATKIEM SP4

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Sprawdzanie dostępności aktualizacji

Powyższe dane wyjściowe wyświetli, jeśli pakiet jest aktualny.

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że jest włączone automatyczne aktualizacje 

Najpierw sprawdź, czy jest włączony:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli to pole wyboru jest wyświetlane, jest włączona:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć uruchamianie:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Uruchom ponownie usługę agenta waagent

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 Z DODATKIEM SP2

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Sprawdzanie dostępności aktualizacji

W danych wyjściowych z powyższych spowoduje to wyświetlenie Jeśli pakiet jest maksymalnie daty.

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że jest włączone automatyczne aktualizacje 

Najpierw sprawdź, czy jest włączony:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli to pole wyboru jest wyświetlane, jest włączona:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć uruchamianie:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Uruchom ponownie usługę agenta waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-6-and-7"></a>Oracle 6 i 7

Upewnij się, że dla Oracle Linux `Addons` repozytorium jest włączona. Wybierz edytować plik `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) lub `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) i zmień wiersz `enabled=0` do `enabled=1` w obszarze **[ol6_addons]** lub **[ol7_addons]** w tym plik.

Następnie Aby zainstalować najnowszą wersję agenta systemu Linux platformy Azure, wpisz:

```bash
sudo yum install WALinuxAgent
```

Jeśli nie znajdziesz repozytorium dodatek można było łatwo dodać te wiersze na końcu pliku .repo zgodnie z publikowania Oracle Linux:

W przypadku maszyn wirtualnych Oracle Linux 6:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Oracle Linux 7 maszyn wirtualnych:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```

Następnie wpisz:

```bash
sudo yum update WALinuxAgent
```

Zazwyczaj jest to wszystko, czego potrzebujesz, ale jeśli niektóre przyczyny, należy zainstalować go z https://github.com bezpośrednio, wykonaj następujące kroki.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Zaktualizuj agenta systemu Linux, gdy pakiet agenta, nie istnieje dla dystrybucji

Zainstaluj wget (istnieją pewne dystrybucjach, który nie należy instalować go domyślnie, takie jak wersje Redhat, CentOS i Oracle Linux 6.4 i 6.5) przez wpisanie `sudo yum install wget` w wierszu polecenia.

### <a name="1-download-the-latest-version"></a>1. Pobierz najnowszą wersję
Otwórz [wersji agenta systemu Linux platformy Azure w serwisie GitHub](https://github.com/Azure/WALinuxAgent/releases) strony sieci web, i sprawdź numer najnowszej wersji. (Można znaleźć wersji bieżącej, wpisując `waagent --version`.)

#### <a name="for-version-22x-or-later-type"></a>Dla wersji 2.2.x lub później, wpisz:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

Następujący wiersz jest używana wersja 2.2.0 na przykład:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Zainstaluj agenta systemu Linux platformy Azure

#### <a name="for-version-22x-use"></a>Dla wersji 2.2.x, użyj:
Może być konieczne zainstalowanie pakietu `setuptools` najpierw — zobacz [tutaj](https://pypi.python.org/pypi/setuptools). Następnie uruchom polecenie:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że jest włączone automatyczne aktualizacje

Najpierw sprawdź, czy jest włączony:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli to pole wyboru jest wyświetlane, jest włączona:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć uruchamianie:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Uruchom ponownie usługę agenta waagent
Dla większości dystrybucjach systemu Linux:

```bash
sudo service waagent restart
```

Ubuntu użyć:

```bash
sudo service walinuxagent restart
```

Dla CoreOS użyj polecenia:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Potwierdzenie wersji agenta systemu Linux platformy Azure
    
```bash
waagent -version
```

Dla CoreOS powyższe polecenie może nie działać.

Zobaczysz, że wersja agenta systemu Linux platformy Azure został zaktualizowany do nowej wersji.

Aby uzyskać więcej informacji na temat agenta systemu Linux platformy Azure, zobacz [README agenta systemu Linux Azure](https://github.com/Azure/WALinuxAgent).
