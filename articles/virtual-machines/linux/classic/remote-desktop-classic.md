---
title: Pulpit zdalny do maszyny Wirtualnej systemu Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak instalowanie i konfigurowanie pulpitu zdalnego, aby nawiązać połączenie Microsoft Azure Maszynę wirtualną systemu Linux dla klasycznego modelu wdrożenia
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: 0e1bfe468e1572ca98be956d39d82df562dce0e6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
ms.locfileid: "30238617"
---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>Korzystanie z Pulpitu zdalnego do łączenia się z maszyną wirtualną systemu Linux na platformie Microsoft Azure
> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Zaktualizowana wersja Menedżera zasobów w tym artykule, zobacz [tutaj](../use-remote-desktop.md).

## <a name="overview"></a>Przegląd
RDP (Remote Desktop Protocol) jest zastrzeżonym protokołem używane dla systemu Windows. Jak możemy użyć protokołu RDP do nawiązania połączenia zdalnego maszyny Wirtualnej systemu Linux (maszyna wirtualna)?

W tych wskazówkach otrzymasz odpowiedź! Aby ułatwić instalacji i konfiguracji xrdp na Microsoft Azure Linux maszyny Wirtualnej, która pozwala się z nim połączyć za pomocą pulpitu zdalnego na komputerze z systemem Windows. Używamy systemem Ubuntu lub OpenSUSE co w przykładzie w tych wskazówkach maszyny Wirtualnej systemu Linux.

Narzędzie xrdp jest serwerem RDP typu open source, który umożliwia łączenie z serwerem systemu Linux przy użyciu pulpitu zdalnego na komputerze z systemem Windows. RDP ma lepszą wydajność niż VNC (wirtualnych sieci przetwarzania danych). VNC renderuje przy użyciu grafiki jakość JPEG i może działać powoli, protokołu RDP jest szybkie i krystalicznie czyste.

> [!NOTE]
> Musi już mieć maszyny Wirtualnej platformy Microsoft Azure systemem Linux. Aby utworzyć i skonfigurować Maszynę wirtualną systemu Linux, zobacz [samouczek maszyny Wirtualnej systemu Linux Azure](createportal-classic.md).
> 
> 

## <a name="create-an-endpoint-for-remote-desktop"></a>Tworzenie punktu końcowego dla pulpitu zdalnego
Firma Microsoft będzie używać domyślnego punktu końcowego 3389 pulpitu zdalnego w tym dokumencie. Konfigurowanie 3389 punktu końcowego jako `Remote Desktop` do maszyny Wirtualnej systemu Linux takie jak poniżej:

![Obraz](./media/remote-desktop/endpoint-for-linux-server.png)

Jeśli nie znasz sposobu konfigurowania punktu końcowego dla maszyny Wirtualnej, zobacz [w tych wskazówkach](setup-endpoints.md).

## <a name="install-gnome-desktop"></a>Zainstaluj Gnome pulpitu
Połącz do maszyny Wirtualnej systemu Linux za pomocą `putty`i zainstaluj `Gnome Desktop`.

Ubuntu użyć:

```bash
sudo apt-get update
sudo apt-get install ubuntu-desktop
```

Dla OpenSUSE użyj polecenia:

```bash
sudo zypper install gnome-session
```

## <a name="install-xrdp"></a>Zainstaluj xrdp
Ubuntu użyć:

```bash
sudo apt-get install xrdp
```

Dla OpenSUSE użyj polecenia:

> [!NOTE]
> Zaktualizuj wersję OpenSUSE z wersją używaną w poniższym poleceniu. W poniższym przykładzie jest przeznaczony dla `OpenSUSE 13.2`.
> 
> 

```bash
sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc
```

## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>Uruchom xrdp i skonfiguruj xdrp w górę rozruchu
Dla OpenSUSE użyj polecenia:

```bash
sudo systemctl start xrdp
sudo systemctl enable xrdp
```

Ubuntu xrdp zostanie uruchomiona i włączone na rozruchowego automatycznie po zakończeniu instalacji.

## <a name="using-xfce-if-you-are-using-an-ubuntu-version-later-than-ubuntu-1204lts"></a>Przy użyciu xfce, jeśli używasz wersji Ubuntu później niż Ubuntu 12.04LTS
Ponieważ bieżąca wersja xrdp nie obsługuje Gnome pulpitu dla wersji Ubuntu później niż Ubuntu 12.04LTS, będziemy używać `xfce` pulpitu zamiast tego.

Aby zainstalować `xfce`, użyj tego polecenia:

```bash
sudo apt-get install xubuntu-desktop
```

Następnie Włącz `xfce` za pomocą tego polecenia:

```bash
echo xfce4-session >~/.xsession
```

Przeprowadź edycję pliku konfiguracji `/etc/xrdp/startwm.sh`:

```bash
sudo vi /etc/xrdp/startwm.sh   
```

Dodaj wiersz `xfce4-session` przed wierszem `/etc/X11/Xsession`.

Aby ponownie uruchomić usługę xrdp, należy użyć to:

```bash
sudo service xrdp restart
```

## <a name="connect-your-linux-vm-from-a-windows-machine"></a>Połączenie z maszyną Wirtualną systemu Linux z komputera z systemem Windows
Na maszynie z systemem Windows uruchom klienta pulpitu zdalnego i wprowadź nazwę DNS maszyny Wirtualnej systemu Linux. Przejdź do pulpitu nawigacyjnego maszyny wirtualnej w portalu Azure i kliknij przycisk `Connect` nawiązywania połączenia z maszyną Wirtualną systemu Linux. W takim przypadku można wyświetlić okno logowania:

![Obraz](./media/remote-desktop/no2.png)

Zaloguj się przy użyciu nazwy użytkownika i hasła maszyny wirtualnej systemu Linux.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat używania xrdp, zobacz [ http://www.xrdp.org/ ](http://www.xrdp.org/).
