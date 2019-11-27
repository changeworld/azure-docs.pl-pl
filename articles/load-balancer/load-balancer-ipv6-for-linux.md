---
title: Konfigurowanie protokołu DHCPv6 dla maszyn wirtualnych systemu Linux
titleSuffix: Azure Load Balancer
description: W tym artykule dowiesz się, jak skonfigurować protokół DHCPv6 dla maszyn wirtualnych z systemem Linux.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: Protokół IPv6, usługa azure load balancer, podwójnego stosu, publiczny adres ip, natywnego protokołu ipv6, aplikację mobilną, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: 6ea215b6aa826231e940f88c3687bb65591303f2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225318"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Konfigurowanie protokołu DHCPv6 dla maszyn wirtualnych systemu Linux


Niektóre z obrazów maszyn wirtualnych systemu Linux w witrynie Azure Marketplace nie mają Dynamic Host Configuration Protocol w wersji 6 (DHCPv6) domyślnie konfigurowana. Aby zapewnić obsługę protokołu IPv6, DHCPv6 musi być skonfigurowany w dystrybucji systemu operacyjnego Linux, którego używasz. Różne dystrybucje systemu Linux skonfiguruj DHCPv6 różne sposoby, ponieważ używają one różnych pakietach.

> [!NOTE]
> Najnowsze obrazy systemu Linux SUSE i CoreOS w witrynie Azure Marketplace zostały wstępnie skonfigurowane przy użyciu protokołu DHCPv6. Żadne dodatkowe zmiany są wymagane, gdy używasz tych obrazów.

Ten dokument opisuje sposób włączania protokołu DHCPv6, tak aby maszyny wirtualnej systemu Linux uzyskuje adres IPv6.

> [!WARNING]
> Nieprawidłowo Edycja plików konfiguracji sieci, można utratę dostępu do sieci dla maszyny wirtualnej. Zaleca się przetestowanie zmiany konfiguracji w systemach nieprodukcyjnych. Instrukcje w tym artykule zostały przetestowane w najnowszych wersjach obrazów systemu Linux w witrynie Azure Marketplace. Aby uzyskać bardziej szczegółowe instrukcje zajrzyj do dokumentacji własnej wersji systemu Linux.

## <a name="ubuntu"></a>Ubuntu

1. Edytuj plik */etc/DHCP/dhclient6.conf* i Dodaj następujący wiersz:

        timeout 10;

2. Edytuj konfigurację sieci dla interfejsu eth0 o następującej konfiguracji:

   * W systemach **Ubuntu 12,04 i 14,04**Edytuj plik */etc/network/interfaces.d/eth0.cfg* . 
   * W witrynie **Ubuntu 16,04**Edytuj plik */etc/network/interfaces.d/50-Cloud-init.cfg* .

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Odnowienia adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```
Począwszy od Ubuntu 17,10, domyślnym mechanizmem konfiguracji sieci jest [Plan]( https://netplan.io).  W czasie instalacji/tworzenia wystąpienia, PLAN sieciowy odczytuje konfigurację sieci z plików konfiguracji YAML w tej lokalizacji:/{lib, itp., uruchom}/netplan/*. YAML.

Dołącz instrukcję *dhcp6: true* dla każdego interfejsu Ethernet w konfiguracji.  Na przykład:
  
        network:
          version: 2
          ethernets:
            eno1:
              dhcp6: true

Podczas wczesnego rozruchu, plan sieci "moduł renderowania sieciowego" zapisuje konfigurację, aby można było usunąć kontrolę nad urządzeniami do określonego demona sieci, aby uzyskać informacje referencyjne na temat planu sieciowego, zobacz https://netplan.io/reference.
 
## <a name="debian"></a>Debian

1. Edytuj plik */etc/DHCP/dhclient6.conf* i Dodaj następujący wiersz:

        timeout 10;

2. Edytuj plik */etc/network/interfaces* i Dodaj następującą konfigurację:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Odnowienia adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS i Oracle Linux

1. Edytuj plik */etc/sysconfig/Network* i Dodaj następujący parametr:

        NETWORKING_IPV6=yes

2. Edytuj plik */etc/sysconfig/Network-scripts/ifcfg-eth0* i Dodaj następujące dwa parametry:

        IPV6INIT=yes
        DHCPV6C=yes

3. Odnowienia adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 i openSUSE 13

Ostatnie SUSE Linux Enterprise Server (SLES) i openSUSE obrazów na platformie Azure zostały wstępnie skonfigurowane przy użyciu protokołu DHCPv6. Żadne dodatkowe zmiany są wymagane, gdy używasz tych obrazów. Jeśli masz maszynę Wirtualną, która jest oparta na starszej lub niestandardowy obraz SUSE, wykonaj następujące czynności:

1. Zainstaluj pakiet `dhcp-client`, w razie konieczności:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Edytuj plik */etc/sysconfig/Network/ifcfg-eth0* i Dodaj następujący parametr:

        DHCLIENT6_MODE='managed'

3. Odnowienia adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 i openSUSE przestępnym

Ostatnie SLES i openSUSE obrazów na platformie Azure zostały wstępnie skonfigurowane przy użyciu protokołu DHCPv6. Żadne dodatkowe zmiany są wymagane, gdy używasz tych obrazów. Jeśli masz maszynę Wirtualną, która jest oparta na starszej lub niestandardowy obraz SUSE, wykonaj następujące czynności:

1. Edytuj plik */etc/sysconfig/Network/ifcfg-eth0* i Zastąp parametr `#BOOTPROTO='dhcp4'` następującą wartością:

        BOOTPROTO='dhcp'

2. Do pliku */etc/sysconfig/Network/ifcfg-eth0* Dodaj następujący parametr:

        DHCLIENT6_MODE='managed'

3. Odnowienia adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Najnowsze obrazy systemu CoreOS na platformie Azure zostały wstępnie skonfigurowane przy użyciu protokołu DHCPv6. Żadne dodatkowe zmiany są wymagane, gdy używasz tych obrazów. Jeśli masz Maszynę wirtualną na podstawie obrazu CoreOS starszej lub niestandardowego, wykonaj następujące czynności:

1. Edytuj plik */etc/systemd/network/10_dhcp. Network* :

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Odnowienia adresu IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
