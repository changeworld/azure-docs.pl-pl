---
title: Konfigurowanie protokołu DHCPv6 dla maszyn wirtualnych systemu Linux
titlesuffix: Azure Load Balancer
description: Jak skonfigurować protokołu DHCPv6 dla maszyn wirtualnych systemu Linux.
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
ms.openlocfilehash: 3e987b6718ead6b7014ec302d1a186dabef11126
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274927"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Konfigurowanie protokołu DHCPv6 dla maszyn wirtualnych systemu Linux


Niektóre z obrazów maszyn wirtualnych systemu Linux w witrynie Azure Marketplace nie mają Dynamic Host Configuration Protocol w wersji 6 (DHCPv6) domyślnie konfigurowana. Aby zapewnić obsługę protokołu IPv6, DHCPv6 musi być skonfigurowany w dystrybucji systemu operacyjnego Linux, którego używasz. Różne dystrybucje systemu Linux skonfiguruj DHCPv6 różne sposoby, ponieważ używają one różnych pakietach.

> [!NOTE]
> Najnowsze obrazy systemu Linux SUSE i CoreOS w witrynie Azure Marketplace zostały wstępnie skonfigurowane przy użyciu protokołu DHCPv6. Żadne dodatkowe zmiany są wymagane, gdy używasz tych obrazów.

Ten dokument opisuje sposób włączania protokołu DHCPv6, tak aby maszyny wirtualnej systemu Linux uzyskuje adres IPv6.

> [!WARNING]
> Nieprawidłowo Edycja plików konfiguracji sieci, można utratę dostępu do sieci dla maszyny wirtualnej. Zaleca się przetestowanie zmiany konfiguracji w systemach nieprodukcyjnych. Instrukcje w tym artykule zostały przetestowane w najnowszych wersjach obrazów systemu Linux w witrynie Azure Marketplace. Aby uzyskać bardziej szczegółowe instrukcje zajrzyj do dokumentacji własnej wersji systemu Linux.

## <a name="ubuntu"></a>Ubuntu

1. Edytuj */etc/dhcp/dhclient6.conf* pliku i Dodaj następujący wiersz:

        timeout 10;

2. Edytuj konfigurację sieci dla interfejsu eth0 o następującej konfiguracji:

   * Na **Ubuntu 12.04 i 14.04**, Edytuj */etc/network/interfaces.d/eth0.cfg* pliku. 
   * Na **Ubuntu 16.04**, Edytuj */etc/network/interfaces.d/50-cloud-init.cfg* pliku.

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Odnowienia adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```
Począwszy od Ubuntu 17,10, domyślnym mechanizmem konfiguracji sieci jest [Plan]( https://netplan.io).  W czasie instalacji/tworzenia wystąpienia, PLAN sieciowy odczytuje konfigurację sieci z plików konfiguracji YAML w tej lokalizacji:/{lib, itp., uruchom}/netplan/*. YAML.

Dołącz instrukcję *dhcp6: true* dla każdego interfejsu Ethernet w konfiguracji.  Przykład:
  
        network:
          version: 2
          ethernets:
            eno1:
              dhcp6: true

Podczas wczesnego rozruchu, plan sieci "moduł renderowania sieciowego" zapisuje konfigurację, aby można było usunąć kontrolę nad urządzeniami do określonego demona sieci, aby uzyskać informacje referencyjne na https://netplan.io/reference temat planu, zobacz.
 
## <a name="debian"></a>Debian

1. Edytuj */etc/dhcp/dhclient6.conf* pliku i Dodaj następujący wiersz:

        timeout 10;

2. Edytuj */etc/network/interfaces* pliku i dodaj następującą konfigurację:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Odnowienia adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS i Oracle Linux

1. Edytuj */etc/sysconfig/network* pliku i Dodaj następujący parametr:

        NETWORKING_IPV6=yes

2. Edytuj */etc/sysconfig/network-scripts/ifcfg-eth0* pliku i dodaj następujące dwa parametry:

        IPV6INIT=yes
        DHCPV6C=yes

3. Odnowienia adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 i openSUSE 13

Ostatnie SUSE Linux Enterprise Server (SLES) i openSUSE obrazów na platformie Azure zostały wstępnie skonfigurowane przy użyciu protokołu DHCPv6. Żadne dodatkowe zmiany są wymagane, gdy używasz tych obrazów. Jeśli masz maszynę Wirtualną, która jest oparta na starszej lub niestandardowy obraz SUSE, wykonaj następujące czynności:

1. Zainstaluj `dhcp-client` pakietu, jeśli to konieczne:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Edytuj */etc/sysconfig/network/ifcfg-eth0* pliku i Dodaj następujący parametr:

        DHCLIENT6_MODE='managed'

3. Odnowienia adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 i openSUSE przestępnym

Ostatnie SLES i openSUSE obrazów na platformie Azure zostały wstępnie skonfigurowane przy użyciu protokołu DHCPv6. Żadne dodatkowe zmiany są wymagane, gdy używasz tych obrazów. Jeśli masz maszynę Wirtualną, która jest oparta na starszej lub niestandardowy obraz SUSE, wykonaj następujące czynności:

1. Edytuj */etc/sysconfig/network/ifcfg-eth0* plik i zastąpić `#BOOTPROTO='dhcp4'` parametru z następujących wartości:

        BOOTPROTO='dhcp'

2. Aby */etc/sysconfig/network/ifcfg-eth0* plików, Dodaj następujący parametr:

        DHCLIENT6_MODE='managed'

3. Odnowienia adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Najnowsze obrazy systemu CoreOS na platformie Azure zostały wstępnie skonfigurowane przy użyciu protokołu DHCPv6. Żadne dodatkowe zmiany są wymagane, gdy używasz tych obrazów. Jeśli masz Maszynę wirtualną na podstawie obrazu CoreOS starszej lub niestandardowego, wykonaj następujące czynności:

1. Edytuj */etc/systemd/network/10_dhcp.network* pliku:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Odnowienia adresu IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
