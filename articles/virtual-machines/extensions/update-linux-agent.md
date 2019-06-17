---
title: Zaktualizuj agenta systemu Linux dla platformy Azure z repozytorium GitHub | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zaktualizować agenta systemu Linux platformy Azure dla maszyny Wirtualnej systemu Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
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
ms.author: roiyz
ms.openlocfilehash: 5d53f34ea6b0983d0687cdaf6ec6271c703bb055
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60799759"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Jak zaktualizować agenta systemu Linux dla platformy Azure na maszynie Wirtualnej

Aby zaktualizować swoje [agenta systemu Linux platformy Azure](https://github.com/Azure/WALinuxAgent) na maszynie Wirtualnej systemu Linux na platformie Azure, musi już mieć:

- Uruchamianie maszyny Wirtualnej systemu Linux na platformie Azure.
- Połączenie do tej maszyny Wirtualnej z systemem Linux przy użyciu protokołu SSH.

Należy zawsze sprawdzić pakietu w repozytorium dystrybucja systemu Linux najpierw. Istnieje możliwość, że najnowszą wersję, jednak włączenie funkcji Aktualizacje automatyczne będą upewnij się, że Agent systemu Linux będą zawsze Pobierz najnowszą aktualizację może nie być dostępny pakiet. Powinna mieć problemy z instalacją z menedżerów pakietów, należy zwrócić się pomocy technicznej przez dostawcę dystrybucji.

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Obsługa agenta minimalne maszyn wirtualnych na platformie Azure
Sprawdź [minimalna obsługiwana wersja dla agentów maszyny wirtualnej na platformie Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) przed kontynuowaniem.

## <a name="updating-the-azure-linux-agent"></a>Aktualizowanie agenta systemu Linux dla platformy Azure

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Zaktualizuj pamięć podręczną pakietów

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że aktualizacje automatyczne są włączone

Najpierw sprawdź, czy jest włączony:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli widzisz te dane wyjściowe, jest włączona:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć przebiegu:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Uruchom ponownie usługę waagent

#### <a name="restart-agent-for-1404"></a>Uruchom ponownie agenta na 14.04

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Uruchom ponownie agenta na 16.04 / 17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-wheezy"></a>Debian 7 "Wheezy"

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Zaktualizuj pamięć podręczną pakietów

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Włączanie automatycznej aktualizacji agenta
Ta wersja Debian nie ma wersji > = 2.0.16, dlatego aktualizacje automatyczne nie jest dla niej. Dane wyjściowe z powyższego polecenia pokazuje, czy pakiet jest aktualny.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie" / Debian 9 "rozproszonego"

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>Zaktualizuj pamięć podręczną pakietów

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo apt-get install waagent
```
#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że aktualizacje automatyczne są włączone 

Najpierw sprawdź, czy jest włączony:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli widzisz te dane wyjściowe, jest włączona:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć przebiegu:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Uruchom ponownie usługę waagent

```
sudo systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Sprawdź dostępne aktualizacje

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że aktualizacje automatyczne są włączone 

Najpierw sprawdź, czy jest włączony:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli widzisz te dane wyjściowe, jest włączona:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć przebiegu:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Uruchom ponownie usługę waagent

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Sprawdź dostępne aktualizacje

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że aktualizacje automatyczne są włączone 

Najpierw sprawdź, czy jest włączony:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli widzisz te dane wyjściowe, jest włączona:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć przebiegu:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Uruchom ponownie usługę waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Sprawdź dostępne aktualizacje

Powyższe dane wyjściowe pokazuje, czy pakiet jest aktualny.

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że aktualizacje automatyczne są włączone 

Najpierw sprawdź, czy jest włączony:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli widzisz te dane wyjściowe, jest włączona:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć przebiegu:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Uruchom ponownie usługę waagent

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>W SYSTEMIE SUSE SLES 12 Z DODATKIEM SP2

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Sprawdź dostępne aktualizacje

W danych wyjściowych powyżej spowoduje to wyświetlenie czy pakiet jest aktualny.

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że aktualizacje automatyczne są włączone 

Najpierw sprawdź, czy jest włączony:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli widzisz te dane wyjściowe, jest włączona:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć przebiegu:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Uruchom ponownie usługę waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-6-and-7"></a>Oracle 6 i 7

Oracle Linux, upewnij się, że `Addons` repozytorium jest włączona. Wybierz edytować plik `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) lub `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) i zmień wiersz `enabled=0` do `enabled=1` w obszarze **[ol6_addons]** lub **[ol7_addons]** w tym plik.

Następnie Aby zainstalować najnowszą wersję agenta systemu Linux dla platformy Azure, wpisz:

```bash
sudo yum install WALinuxAgent
```

Jeśli nie możesz znaleźć repozytorium dodatku możesz po prostu dodaj następujące wiersze na końcu pliku .repo zgodnie z danej wersji systemu Oracle Linux:

W przypadku maszyn wirtualnych Oracle Linux 6:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

W przypadku maszyn wirtualnych Oracle Linux 7:

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

Zazwyczaj jest to wszystko, czego potrzebujesz, ale jeśli jakiegoś powodu, musisz zainstalować go z https://github.com bezpośrednio, wykonaj następujące kroki.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Aktualizacja agenta systemu Linux, gdy pakiet agenta, nie istnieje dla dystrybucji

Zainstaluj wget (ma niektórych dystrybucjach, który nie należy instalować go domyślnie, takie jak wersje Red Hat, CentOS i Oracle Linux 6.4 i 6.5), wpisując `sudo yum install wget` w wierszu polecenia.

### <a name="1-download-the-latest-version"></a>1. Pobierz najnowszą wersję
Otwórz [wersję agenta systemu Linux platformy Azure w usłudze GitHub](https://github.com/Azure/WALinuxAgent/releases) strony sieci web i Dowiedz się, numer najnowszej wersji. (Możesz znaleźć bieżącą wersję, wpisując `waagent --version`.)

#### <a name="for-version-22x-or-later-type"></a>Dla wersji 2.2.x lub nowszej, wpisz:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

Następujący wiersz używa wersji 2.2.0, na przykład:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Zainstaluj agenta systemu Linux dla platformy Azure

#### <a name="for-version-22x-use"></a>Dla wersji 2.2.x, użyj:
Może być konieczne zainstalowanie pakietu `setuptools` najpierw — zobacz [tutaj](https://pypi.python.org/pypi/setuptools). Następnie uruchom polecenie:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że aktualizacje automatyczne są włączone

Najpierw sprawdź, czy jest włączony:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli widzisz te dane wyjściowe, jest włączona:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć przebiegu:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Uruchom ponownie usługę waagent
Dla większości dystrybucje systemu Linux:

```bash
sudo service waagent restart
```

W przypadku systemu Ubuntu użyj polecenia:

```bash
sudo service walinuxagent restart
```

Aby uzyskać CoreOS należy użyć:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Sprawdzić, która wersja agenta systemu Linux platformy Azure
    
```bash
waagent -version
```

Aby uzyskać CoreOS powyższego polecenia mogą nie działać.

Zobaczysz, że wersja agenta systemu Linux platformy Azure został zaktualizowany do nowej wersji.

Aby uzyskać więcej informacji na temat agenta systemu Linux dla platformy Azure, zobacz [Azure Linux Agent README](https://github.com/Azure/WALinuxAgent).
