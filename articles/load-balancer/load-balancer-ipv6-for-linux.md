---
title: Konfigurowanie protokołu DHCPv6 dla maszyn wirtualnych systemu Linux | Dokumentacja firmy Microsoft
description: Jak skonfigurować DHCPv6 dla maszyn wirtualnych systemu Linux.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
keywords: Protokół IPv6, usługi równoważenia obciążenia azure, podwójnego stosu, publiczny adres ip, natywnego protokołu ipv6, mobile, iot
ms.assetid: b32719b6-00e8-4cd0-ba7f-e60e8146084b
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 6248ed2f55fb5bbcc2061af6ce1dedf2bd31ccad
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
ms.locfileid: "30261851"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Konfigurowanie protokołu DHCPv6 dla maszyn wirtualnych systemu Linux


Niektóre obrazy maszyn wirtualnych systemu Linux w portalu Azure Marketplace nie mają Dynamic Host Configuration Protocol w wersji 6 (DHCPv6) konfiguracja domyślna. Aby obsługiwać protokół IPv6, DHCPv6 musi być skonfigurowany w dystrybucji systemu operacyjnego Linux, którego używasz. Różne dystrybucje systemu Linux skonfigurować DHCPv6 na różne sposoby, ponieważ korzystają z różnych pakietów.

> [!NOTE]
> Ostatnie SUSE Linux i CoreOS obrazów w portalu Azure Marketplace zostały wstępnie skonfigurowany z użyciem protokołu DHCPv6. Korzystając z tych obrazów są wymagane nie dodatkowe zmiany.

Ten dokument zawiera opis sposobu zapewniają DHCPv6, dzięki czemu maszyny wirtualnej systemu Linux uzyskuje adres IPv6.

> [!WARNING]
> Nieprawidłowo edycję plików konfiguracyjnych sieci, można utratę dostępu do sieci do maszyny Wirtualnej. Zaleca się przetestowanie zmiany konfiguracji w systemach nieprodukcyjnych. Instrukcje w tym artykule zostały przetestowane na najnowsze wersje obrazów systemu Linux w portalu Azure Marketplace. Aby uzyskać bardziej szczegółowe instrukcje zajrzyj do dokumentacji dla wersji systemu Linux.

## <a name="ubuntu"></a>Ubuntu

1. Edytuj */etc/dhcp/dhclient6.conf* pliku i Dodaj następujący wiersz:

        timeout 10;

2. Zmień konfigurację sieci dla interfejsu eth0 przy użyciu następującej konfiguracji:

   * Na **Ubuntu 12.04 i 14.04**, Edytuj */etc/network/interfaces.d/eth0.cfg* pliku. 
   * Na **Ubuntu 16.04**, Edytuj */etc/network/interfaces.d/50-cloud-init.cfg* pliku.

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Odnów adres IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Edytuj */etc/dhcp/dhclient6.conf* pliku i Dodaj następujący wiersz:

        timeout 10;

2. Edytuj */etc/network/interfaces* pliku i dodaj następującą konfigurację:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Odnów adres IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS i Oracle Linux

1. Edytuj */etc/sysconfig/network* pliku i Dodaj następujący parametr:

        NETWORKING_IPV6=yes

2. Edytuj */etc/sysconfig/network-scripts/ifcfg-eth0* pliku, a następnie dodaj następujące dwa parametry:

        IPV6INIT=yes
        DHCPV6C=yes

3. Odnów adres IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 i openSUSE 13

Ostatnie SUSE Linux Enterprise Server (SLES) i openSUSE obrazów na platformie Azure zostały wstępnie skonfigurowany z użyciem protokołu DHCPv6. Korzystając z tych obrazów są wymagane nie dodatkowe zmiany. Jeśli masz maszynę Wirtualną, która jest oparta na starszej lub niestandardowy obraz SUSE, wykonaj następujące czynności:

1. Zainstaluj `dhcp-client` pakietu, w razie potrzeby:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Edytuj */etc/sysconfig/network/ifcfg-eth0* pliku i Dodaj następujący parametr:

        DHCLIENT6_MODE='managed'

3. Odnów adres IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 i openSUSE przestępnego

Ostatnie SLES i openSUSE obrazów na platformie Azure zostały wstępnie skonfigurowany z użyciem protokołu DHCPv6. Korzystając z tych obrazów są wymagane nie dodatkowe zmiany. Jeśli masz maszynę Wirtualną, która jest oparta na starszej lub niestandardowy obraz SUSE, wykonaj następujące czynności:

1. Edytuj */etc/sysconfig/network/ifcfg-eth0* plików i Zastąp `#BOOTPROTO='dhcp4'` parametr przy użyciu następującej wartości:

        BOOTPROTO='dhcp'

2. Aby */etc/sysconfig/network/ifcfg-eth0* pliku, Dodaj następujący parametr:

        DHCLIENT6_MODE='managed'

3. Odnów adres IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Ostatnie obrazy CoreOS na platformie Azure zostały wstępnie skonfigurowany z użyciem protokołu DHCPv6. Korzystając z tych obrazów są wymagane nie dodatkowe zmiany. Jeśli masz maszyny Wirtualnej na podstawie obrazu CoreOS starszych lub niestandardowego, wykonaj następujące czynności:

1. Edytuj */etc/systemd/network/10_dhcp.network* pliku:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Odnów adres IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
