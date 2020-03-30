---
title: Konfigurowanie maszyn wirtualnych DHCPv6 dla systemu Linux
titleSuffix: Azure Load Balancer
description: W tym artykule dowiesz się, jak skonfigurować maszyny wirtualne DHCPv6 dla systemu Linux.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, lazurowy moduł równoważenia obciążenia, podwójny stos, publiczny adres IP, natywny ipv6, mobilny, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: 6ea215b6aa826231e940f88c3687bb65591303f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225318"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Konfigurowanie maszyn wirtualnych DHCPv6 dla systemu Linux


Niektóre obrazy maszyn wirtualnych systemu Linux w portalu Azure Marketplace nie mają domyślnie skonfigurowanego protokołu konfiguracji hosta dynamicznego w wersji 6 (DHCPv6). Aby obsługiwać protokół IPv6, dhcpv6 musi być skonfigurowany w używanej dystrybucji systemu operacyjnego Linux. Różne dystrybucje Linuksa konfigurują DHCPv6 na różne sposoby, ponieważ używają różnych pakietów.

> [!NOTE]
> Najnowsze obrazy SUSE Linux i CoreOS w portalu Azure Marketplace zostały wstępnie skonfigurowane za pomocą usługi DHCPv6. Podczas korzystania z tych obrazów nie są wymagane żadne dodatkowe zmiany.

W tym dokumencie opisano sposób włączania protokołu DHCPv6, aby maszyna wirtualna systemu Linux uzyskała adres IPv6.

> [!WARNING]
> Nieprawidłowo edytując pliki konfiguracyjne sieci, można utracić dostęp do sieci maszyny Wirtualnej. Zaleca się przetestowanie zmian konfiguracji w systemach nieprodukcyjnych. Instrukcje w tym artykule zostały przetestowane w najnowszych wersjach obrazów systemu Linux w portalu Azure Marketplace. Aby uzyskać bardziej szczegółowe instrukcje, zapoznaj się z dokumentacją dla własnej wersji systemu Linux.

## <a name="ubuntu"></a>Ubuntu

1. Edytuj plik */etc/dhcp/dhclient6.conf* i dodaj następujący wiersz:

        timeout 10;

2. Edytuj konfigurację sieci dla interfejsu eth0 z następującą konfiguracją:

   * Na **Ubuntu 12.04 i 14.04,** edytuj plik */etc/network/interfaces.d/eth0.cfg.* 
   * Na **Ubuntu 16.04,** edytuj plik */etc/network/interfaces.d/50-cloud-init.cfg.*

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Odnów adres IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```
Począwszy od Ubuntu 17.10, domyślnym mechanizmem konfiguracji sieci jest [NETPLAN]( https://netplan.io).  W czasie instalacji/wystąpienia netplan odczytuje konfigurację sieci z plików konfiguracyjnych YAML w tej lokalizacji: /{lib itp., uruchom}/netplan/*.yaml.

Dołącz *instrukcję dhcp6:true* dla każdego interfejsu Ethernet w konfiguracji.  Przykład:
  
        network:
          version: 2
          ethernets:
            eno1:
              dhcp6: true

Podczas wczesnego rozruchu netplan "renderer sieciowy" zapisuje konfigurację do /run, aby przekazać kontrolę nad urządzeniami do określonego demona sieciowego Aby uzyskać informacje referencyjne na temat NETPLAN, patrz https://netplan.io/reference.
 
## <a name="debian"></a>Debian

1. Edytuj plik */etc/dhcp/dhclient6.conf* i dodaj następujący wiersz:

        timeout 10;

2. Edytuj plik */etc/network/interfaces* i dodaj następującą konfigurację:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Odnów adres IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS i Oracle Linux

1. Edytuj plik */etc/sysconfig/network* i dodaj następujący parametr:

        NETWORKING_IPV6=yes

2. Edytuj plik */etc/sysconfig/network-scripts/ifcfg-eth0* i dodaj następujące dwa parametry:

        IPV6INIT=yes
        DHCPV6C=yes

3. Odnów adres IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 i openSUSE 13

Najnowsze obrazy SUSE Linux Enterprise Server (SLES) i openSUSE na platformie Azure zostały wstępnie skonfigurowane za pomocą protokołu DHCPv6. Podczas korzystania z tych obrazów nie są wymagane żadne dodatkowe zmiany. Jeśli masz maszynę wirtualną opartą na starszym lub niestandardowym obrazie SUSE, wykonaj następujące czynności:

1. Zainstaluj `dhcp-client` pakiet, jeśli to konieczne:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Edytuj plik */etc/sysconfig/network/ifcfg-eth0* i dodaj następujący parametr:

        DHCLIENT6_MODE='managed'

3. Odnów adres IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 i openSUSE Leap

Najnowsze obrazy SLES i openSUSE na platformie Azure zostały wstępnie skonfigurowane za pomocą dhcpv6. Podczas korzystania z tych obrazów nie są wymagane żadne dodatkowe zmiany. Jeśli masz maszynę wirtualną opartą na starszym lub niestandardowym obrazie SUSE, wykonaj następujące czynności:

1. Edytuj plik */etc/sysconfig/network/ifcfg-eth0* i `#BOOTPROTO='dhcp4'` zastąp parametr następującą wartością:

        BOOTPROTO='dhcp'

2. Do pliku */etc/sysconfig/network/ifcfg-eth0* dodaj następujący parametr:

        DHCLIENT6_MODE='managed'

3. Odnów adres IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Najnowsze obrazy CoreOS na platformie Azure zostały wstępnie skonfigurowane za pomocą dhcpv6. Podczas korzystania z tych obrazów nie są wymagane żadne dodatkowe zmiany. Jeśli masz maszynę wirtualną opartą na starszym lub niestandardowym obrazie CoreOS, wykonaj następujące czynności:

1. Edytuj plik */etc/systemd/network/10_dhcp.network:*

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Odnów adres IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
