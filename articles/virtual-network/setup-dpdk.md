---
title: DPDK na maszynie wirtualnej systemu Azure z systemem Linux | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować dpdk na maszynie wirtualnej systemu Linux.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: c79c1fd687e329b97a854a3ff66a3cf95076b5d6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384232"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>Konfigurowanie dpdk na maszynie wirtualnej systemu Linux

Zestaw DPDK (Data Plane Development Kit) na platformie Azure oferuje szybszą strukturę przetwarzania pakietów przestrzeni użytkownika dla aplikacji wymagających dużej wydajności. Ta struktura pomija stos sieci jądra maszyny wirtualnej.

W typowym przetwarzaniu pakietów, który używa stosu sieci jądra, proces jest oparty na przerwaniu. Gdy interfejs sieciowy odbiera przychodzące pakiety, istnieje przerwanie jądra do przetworzenia pakietu i przełączanie kontekstu z przestrzeni jądra do przestrzeni użytkownika. DPDK eliminuje przełączanie kontekstu i metodę opartą na przerwaniu na rzecz implementacji przestrzeni użytkownika, która używa sterowników trybu sondowania do szybkiego przetwarzania pakietów.

DPDK składa się z zestawów bibliotek przestrzeni użytkownika, które zapewniają dostęp do zasobów niższego poziomu. Zasoby te mogą obejmować sprzęt, rdzenie logiczne, zarządzanie pamięcią i sterowniki trybu sondowania dla kart interfejsu sieciowego.

DPDK można uruchomić na maszynach wirtualnych platformy Azure, które obsługują wiele dystrybucji systemu operacyjnego. DPDK zapewnia kluczowe zróżnicowanie wydajności w implementacji wirtualizacji funkcji sieciowych. Te implementacje mogą przybierać formę sieciowych urządzeń wirtualnych (NVA), takich jak routery wirtualne, zapory, vpny, moduły równoważenia obciążenia, rozwinięte rdzenie pakietów i aplikacje typu "odmowa usługi" (DDoS).

## <a name="benefit"></a>Korzyść

**Wyższe pakiety na sekundę (PPS)**: Pomijanie jądra i przejęcie kontroli nad pakietami w przestrzeni użytkownika zmniejsza liczbę cykli, eliminując przełączniki kontekstowe. Zwiększa również szybkość pakietów, które są przetwarzane na sekundę na maszynach wirtualnych systemu Azure Linux.


## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Obsługiwane są następujące dystrybucje z portalu Azure Marketplace:

| System operacyjny Linux     | Wersja jądra               | 
|--------------|---------------------------   |
| Ubuntu 16.04 | 4.15.0-1014-azure+           | 
| Ubuntu 18.04 | 4.15.0-1014-azure+           |
| SLES 15 SP1  | 4.12.14-8.27-azure+          | 
| RHEL 7.5     | 3.10.0-862.11.6.el7.x86_64+  | 
| CentOS 7.5   | 3.10.0-862.11.6.el7.x86_64+  | 

**Niestandardowa obsługa jądra**

Dla każdej wersji jądra Linuksa, która nie jest wymieniona, zobacz [Poprawki do tworzenia jądra Linuksa dostrojonego do platformy Azure](https://github.com/microsoft/azure-linux-kernel). Aby uzyskać więcej informacji, [azuredpdk@microsoft.com](mailto:azuredpdk@microsoft.com)można również skontaktować się z . 

## <a name="region-support"></a>Wsparcie regionu

Wszystkie regiony platformy Azure obsługują dpdk.

## <a name="prerequisites"></a>Wymagania wstępne

Przyspieszona sieć musi być włączona na maszynie wirtualnej systemu Linux. Maszyna wirtualna powinna mieć co najmniej dwa interfejsy sieciowe z jednym interfejsem do zarządzania. Dowiedz się, jak [utworzyć maszynę wirtualną systemu Linux z włączoną obsługą przyspieszonej sieci.](create-vm-accelerated-networking-cli.md)

## <a name="install-dpdk-dependencies"></a>Instalowanie zależności DPDK

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL7,5/CentOS 7,5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15-sp1"></a>SLES 15 SP1

**Jądro platformy Azure**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Domyślne jądro**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="set-up-the-virtual-machine-environment-once"></a>Konfigurowanie środowiska maszyny wirtualnej (raz)

1. [Pobierz najnowsze DPDK](https://core.dpdk.org/download). Wersja 18.11 LTS lub 19.11 LTS jest wymagana dla platformy Azure.
2. Zbuduj domyślny config z . `make config T=x86_64-native-linuxapp-gcc`
3. Włącz mellanox PMD w wygenerowanej `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`konfiguracji z .
4. Skompiluj z `make`.
5. Zainstaluj `make install DESTDIR=<output folder>`z .

## <a name="configure-the-runtime-environment"></a>Konfigurowanie środowiska wykonawczego

Po ponownym uruchomieniu uruchom jednocześnie następujące polecenia:

1. Ogromne strony

   * Skonfiguruj hugepage, uruchamiając następujące polecenie, raz dla każdego węzła numa:

     ```bash
     echo 1024 | sudo tee /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * Utwórz katalog do `mkdir /mnt/huge`montażu za pomocą pliku .
   * Zamontuj `mount -t hugetlbfs nodev /mnt/huge`hugepages z .
   * Sprawdź, czy hugepages `grep Huge /proc/meminfo`są zarezerwowane z .

     > [UWAGA] Istnieje sposób, aby zmodyfikować plik grub tak, że hugepages są zarezerwowane podczas rozruchu, postępując zgodnie z [instrukcjami](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) dla DPDK. Instrukcje znajdują się na dole strony. Gdy używasz maszyny wirtualnej systemu Azure Linux, zmodyfikuj pliki w **obszarze /etc/config/grub.d,** aby zarezerwować hugepages podczas ponownego uruchamiania.

2. Adresy IP mac &: `ifconfig –a` służy do wyświetlania adresu MAC i IP interfejsów sieciowych. Interfejs *sieciowy VF* i interfejs *sieciowy NETVSC* mają ten sam adres MAC, ale tylko interfejs *sieciowy NETVSC* ma adres IP. Interfejsy *VF* działają jako podrzędne interfejsy interfejsów *NETVSC.*

3. Adresy PCI

   * Użyj, `ethtool -i <vf interface name>` aby dowiedzieć się, który adres PCI do użycia dla *VF*.
   * Jeśli *eth0* ma przyspieszone sieci włączone, upewnij się, że testpmd nie przypadkowo przejąć urządzenie *PCI VF* dla *eth0*. Jeśli aplikacja DPDK przypadkowo przejmuje interfejs sieci zarządzania i powoduje utratę połączenia SSH, użyj konsoli szeregowej, aby zatrzymać aplikację DPDK. Można również użyć konsoli szeregowej, aby zatrzymać lub uruchomić maszynę wirtualną.

4. Załaduj *ibuverbs* przy każdym restarcie z `modprobe -a ib_uverbs`. Tylko dla SLES 15 *mlx4_ib* ładuj `modprobe -a mlx4_ib`mlx4_ib z .

## <a name="failsafe-pmd"></a>Failsafe PMD

Aplikacje DPDK muszą działać za pomocą awaryjnego pmd, który jest narażony na platformie Azure. Jeśli aplikacja działa bezpośrednio za przez *VF* PMD, nie odbiera **wszystkie** pakiety, które są przeznaczone do maszyny Wirtualnej, ponieważ niektóre pakiety są wyświetlane za jego syntetyczny interfejs. 

Jeśli uruchomisz aplikację DPDK za pomocą awaryjnego pmd, gwarantuje, że aplikacja odbiera wszystkie pakiety, które są do niej przeznaczone. Upewnia się również, że aplikacja działa w trybie DPDK, nawet jeśli VF jest odwołany, gdy host jest obsługiwany. Aby uzyskać więcej informacji na temat failsafe PMD, zobacz [Biblioteka sterowników trybu sondowania awaryjnego](https://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Uruchom testpmd

Aby uruchomić testpmd w `sudo` trybie głównym, należy użyć przed *testpmd* polecenia.

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Podstawowe: Sprawdzanie poczytalności, inicjowanie karty failsafe

1. Uruchom następujące polecenia, aby uruchomić pojedynczą aplikację testpmd portu:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Uruchom następujące polecenia, aby uruchomić aplikację testpmd z dwoma portami:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Jeśli korzystasz z testpmd z więcej niż `--vdev` dwoma kartami `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`sieciowym, argument jest zgodny z tym wzorcem: .

3.  Po jego uruchomieniu uruchom, `show port info all` aby sprawdzić informacje o porcie. Powinien zostać wyświetlony jeden lub dwa porty DPDK, które są net_failsafe (nie *net_mlx4*).
4.  Służy `start <port> /stop <port>` do uruchamiania ruchu.

Poprzednie polecenia rozpoczynają *testpmd* w trybie interaktywnym, co jest zalecane do wypróbowania poleceń testpmd.

### <a name="basic-single-sendersingle-receiver"></a>Podstawowe: Pojedynczy nadawca/pojedynczy odbiornik

Następujące polecenia okresowo drukują statystyki pakietów na sekundę:

1. Po stronie TX uruchom następujące polecenie:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. Po stronie RX uruchom następujące polecenie:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

Podczas uruchamiania poprzednich poleceń na maszynie wirtualnej, należy zmienić `app/test-pmd/txonly.c` *IP_SRC_ADDR* i *IP_DST_ADDR,* aby dopasować rzeczywisty adres IP maszyn wirtualnych przed kompilacją. W przeciwnym razie pakiety są odrzucane przed dotarciem do odbiornika.

### <a name="advanced-single-sendersingle-forwarder"></a>Zaawansowane: Pojedynczy nadawca/pojedynczy spedyt
Następujące polecenia okresowo drukują statystyki pakietów na sekundę:

1. Po stronie TX uruchom następujące polecenie:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. Po stronie FWD uruchom następujące polecenie:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

Podczas uruchamiania poprzednich poleceń na maszynie wirtualnej, należy zmienić `app/test-pmd/txonly.c` *IP_SRC_ADDR* i *IP_DST_ADDR,* aby dopasować rzeczywisty adres IP maszyn wirtualnych przed kompilacją. W przeciwnym razie pakiety są upuszczane przed dotarciem do usługi przesyłania dalej. Nie będzie można mieć trzeciego komputera odbierać ruch przesyłany dalej, ponieważ *testpmd* usługi przesyłania dalej nie modyfikuje adresy warstwy 3, chyba że wprowadzono pewne zmiany kodu.

## <a name="references"></a>Dokumentacja

* [Opcje EAL](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Polecenia Testpmd](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
