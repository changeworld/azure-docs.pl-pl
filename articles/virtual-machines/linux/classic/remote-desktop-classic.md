---
title: Pulpit zdalny dla maszyny Wirtualnej z systemem Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować i skonfigurować Pulpit zdalny, aby nawiązać połączenie z Microsoft Azure Maszynę wirtualną systemu Linux dla klasycznego modelu wdrażania
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 5e68774c3edb7d82fef388c593a6b96c52857be6
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927744"
---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>Korzystanie z Pulpitu zdalnego do łączenia się z maszyną wirtualną systemu Linux na platformie Microsoft Azure
> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Aby uzyskać zaktualizowaną wersję usługi Resource Manager w tym artykule, zobacz [tutaj](../use-remote-desktop.md).

## <a name="overview"></a>Przegląd
RDP (Remote Desktop Protocol) jest protokołem używany dla Windows. Jak możemy użyć protokołu RDP na zdalne łączenie z maszyną wirtualną systemu Linux (maszyna wirtualna)

Wskazówki te zapewniają odpowiedź na pytanie! Ułatwi to umożliwia zainstalowanie i xrdp konfiguracji usługi Microsoft systemu Linux maszyny wirtualnej platformy Azure, która pozwala połączyć się z nim pulpitu zdalnego na komputerze Windows. Firma Microsoft użyje systemem Ubuntu lub OpenSUSE jako przykład w tych wskazówkach maszyny Wirtualnej systemu Linux.

Narzędzie xrdp jest serwera RDP "open source", która umożliwia łączenie z serwerem systemu Linux przy użyciu pulpitu zdalnego na komputerze Windows. Protokół RDP ma lepszą wydajność niż VNC (przetwarzanie wirtualnych sieci). VNC renderowany przy użyciu JPEG jakości grafika i może działać powoli, protokołu RDP jest szybkie i krystalicznie czyste.

> [!NOTE]
> Musi już mieć Maszynie wirtualnej platformy Azure firmy Microsoft z systemem Linux. Aby utworzyć i skonfigurować Maszynę wirtualną systemu Linux, zobacz [maszyny Wirtualnej systemu Linux platformy Azure w ramach samouczka](createportal-classic.md).
> 
> 

## <a name="create-an-endpoint-for-remote-desktop"></a>Utworzenie punktu końcowego za pomocą pulpitu zdalnego
Firma Microsoft użyje domyślnego punktu końcowego 3389 dla pulpitu zdalnego w tym dokumencie. Konfigurowanie 3389 punktu końcowego jako `Remote Desktop` do maszyny Wirtualnej systemu Linux takie jak poniżej:

![image](./media/remote-desktop/endpoint-for-linux-server.png)

Jeśli nie wiesz, jak skonfigurować punkt końcowy dla maszyny Wirtualnej, zobacz [Niniejsze wskazówki](setup-endpoints.md).

## <a name="install-gnome-desktop"></a>Zainstaluj pulpit Gnome
Łączenie z maszyną wirtualną systemu Linux, za pośrednictwem `putty`i zainstaluj `Gnome Desktop`.

W przypadku systemu Ubuntu użyj polecenia:

```bash
sudo apt-get update
sudo apt-get install ubuntu-desktop
```

Aby uzyskać OpenSUSE należy użyć:

```bash
sudo zypper install gnome-session
```

## <a name="install-xrdp"></a>Zainstaluj xrdp
W przypadku systemu Ubuntu użyj polecenia:

```bash
sudo apt-get install xrdp
```

Aby uzyskać OpenSUSE należy użyć:

> [!NOTE]
> Zaktualizuj wersja oprogramowania OpenSUSE przy użyciu wersji, którego używasz w następującym poleceniu. W poniższym przykładzie jest `OpenSUSE 13.2`.
> 
> 

```bash
sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc
```

## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>Uruchom xrdp i skonfiguruj xdrp podczas rozruchu
Aby uzyskać OpenSUSE należy użyć:

```bash
sudo systemctl start xrdp
sudo systemctl enable xrdp
```

Ubuntu xrdp zostanie uruchomiona i włączone podczas rozruchu automatycznie po zakończeniu instalacji.

## <a name="using-xfce-if-you-are-using-an-ubuntu-version-later-than-ubuntu-1204lts"></a>Jeśli używasz wersji systemu Ubuntu później niż Ubuntu 12.04LTS, przy użyciu xfce
Ponieważ bieżąca wersja xrdp nie obsługuje pulpit Gnome dla wersji systemu Ubuntu później niż Ubuntu 12.04LTS, użyjemy `xfce` pulpitu zamiast tego.

Aby zainstalować `xfce`, użyj tego polecenia:

```bash
sudo apt-get install xubuntu-desktop
```

Następnie Włącz `xfce` za pomocą tego polecenia:

```bash
echo xfce4-session >~/.xsession
```

Edytuj plik konfiguracji `/etc/xrdp/startwm.sh`:

```bash
sudo vi /etc/xrdp/startwm.sh   
```

Dodaj wiersz `xfce4-session` przed wierszem `/etc/X11/Xsession`.

Aby ponownie uruchomić usługę xrdp, użyć następującego polecenia:

```bash
sudo service xrdp restart
```

## <a name="connect-your-linux-vm-from-a-windows-machine"></a>Łączenie z maszyną Wirtualną z systemem Linux na komputerze Windows
Na maszynie Windows uruchom klienta pulpitu zdalnego, a następnie wprowadź nazwę DNS maszyny Wirtualnej systemu Linux. Przejdź do pulpitu nawigacyjnego maszyny wirtualnej w witrynie Azure portal i kliknij przycisk `Connect` nawiązywania połączenia z maszyną Wirtualną systemu Linux. W takim przypadku zobaczysz okno logowania:

![image](./media/remote-desktop/no2.png)

Zaloguj się przy użyciu nazwy użytkownika i hasło maszyny wirtualnej systemu Linux.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat używania xrdp zobacz [ http://www.xrdp.org/ ](http://www.xrdp.org/).
