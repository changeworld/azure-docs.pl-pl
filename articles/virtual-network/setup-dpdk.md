---
title: DPDK w maszynie Wirtualnej systemu Linux platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować DPDK na maszynie wirtualnej systemu Linux.
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
ms.openlocfilehash: c5cb840035c5d0d5694982324c7237c58001e689
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731604"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>Konfigurowanie DPDK na maszynie wirtualnej systemu Linux

Danych płaszczyzna Development Kit (DPDK) na platformie Azure oferuje szybsze framework przetwarzania pakietów przestrzeń użytkownika dla aplikacji intensywnie korzystających z wydajnością. Ta struktura pomija stos sieciowy jądra maszyny wirtualnej.

W przetwarzania typowych pakietów, które używa stosu sieciowego jądra, proces jest oparte na przerwania. Gdy interfejs sieciowy odbiera pakiety przychodzące, ma przerwania jądra, do procesu, który pakiet i kontekst przełączyć obszaru jądra na przestrzeni użytkownika. DPDK eliminuje przełączania kontekstu i metody opartej na przerwania na rzecz implementacji przestrzeń użytkownika, że używa sondowania sterowniki trybu przetwarzania szybkie pakietów.

DPDK składa się z zestawów bibliotek przestrzeń użytkownika, które umożliwiają dostęp do zasobów niższego poziomu. Te zasoby mogą obejmować sprzętu, rdzenie logiczne, zarządzanie pamięcią i sterowniki trybu sondowania dla kart interfejsu sieciowego.

DPDK można uruchamiać na maszynach wirtualnych platformy Azure, które obsługują wielu dystrybucji systemu operacyjnego. DPDK zawiera rozróżnienie wydajności w kształtowaniu implementacje Wirtualizacja funkcji sieci. Tych implementacji może mieć postać wirtualnych urządzeń sieciowych (urządzeń WUS), takie jak wirtualne routery, zapory, sieci VPN, usługi równoważenia obciążenia, rdzeni wydzielonego pakietów i aplikacji denial of service (DDoS).

## <a name="benefit"></a>Korzyść

**Wyższe pakietów na sekundę (PPS)**: Pomijanie jądra i kontroli pobieranie pakietów w obszarze użytkownika zmniejsza liczbę cyklu przez wyeliminowanie przełączeń kontekstu. Zwiększa to liczba pakietów, które są przetwarzane na sekundę na maszynach wirtualnych z systemem Linux platformy Azure.


## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Obsługiwane są poniższe dystrybucje w galerii platformy Azure:

| System operacyjny Linux     | Wersja jądra        |
|--------------|----------------       |
| Ubuntu 16.04 | 4.15.0-1015-azure     |
| Ubuntu 18.04 | 4.15.0-1015-azure     |
| SLES 15      | 4.12.14-5.5-azure     |
| RHEL W WERSJI 7.5     | 3.10.0-862.9.1.el7    |
| CentOS w wersji 7.5   | 3.10.0-862.3.3.el7    |

**Obsługa niestandardowych jądra**

Dla dowolnej wersji jądra systemu Linux, który nie ma na liście, zobacz [poprawki do tworzenia dostosowanych Azure jądra systemu Linux](https://github.com/microsoft/azure-linux-kernel). Aby uzyskać więcej informacji, możesz również skontaktować się ze [ azuredpdk@microsoft.com ](mailto:azuredpdk@microsoft.com). 

## <a name="region-support"></a>Obsługa regionu

Wszystkie regiony platformy Azure obsługują DPDK.

## <a name="prerequisites"></a>Wymagania wstępne

Przyspieszona sieć musi być włączona na maszynie wirtualnej systemu Linux. Maszyna wirtualna powinna mieć co najmniej dwa interfejsy sieciowe, za pomocą jednego interfejsu do zarządzania. Dowiedz się, jak [Utwórz maszynę wirtualną systemu Linux z włączoną obsługą przyspieszonej sieci](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>Zainstaluj DPDK zależności

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL7.5/CentOS 7.5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15"></a>SLES 15

**Jądra systemu Azure**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Domyślne jądra**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="set-up-the-virtual-machine-environment-once"></a>Konfigurowanie środowiska maszyny wirtualnej (raz)

1. [Pobierz najnowsze DPDK](https://core.dpdk.org/download). Wersja 18.02 lub nowszy jest wymagany dla platformy Azure.
2. Tworzenie konfiguracji domyślnej z `make config T=x86_64-native-linuxapp-gcc`.
3. Włącz Mellanox PMDs w wygenerowanym pliku config przy użyciu `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`.
4. Kompiluj przy użyciu `make`.
5. Instalowanie przy użyciu `make install DESTDIR=<output folder>`.

## <a name="configure-the-runtime-environment"></a>Konfigurowanie środowiska wykonawczego

Po ponownym uruchomieniu komputera, uruchom następujące polecenia jeden raz:

1. Hugepages

   * Skonfiguruj hugepage, uruchamiając następujące polecenie, jeden raz dla wszystkich numanodes:

     ```bash
     echo 1024 | sudo tee
     /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * Utwórz katalog dla instalowania za pomocą `mkdir /mnt/huge`.
   * Hugepages instalacji za pomocą `mount -t hugetlbfs nodev /mnt/huge`.
   * Sprawdź, czy hugepages są zarezerwowane przez `grep Huge /proc/meminfo`.

     > [!NOTE]
     > Istnieje sposób do modyfikowania pliku chodników, tak aby hugepages są zarezerwowane podczas rozruchu, postępując zgodnie z [instrukcje](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) dla DPDK. Instrukcje są w dolnej części strony. Podczas korzystania z maszyny wirtualnej systemu Linux platformy Azure, należy zmodyfikować plików w obszarze **/etc/config/grub.d** zamiast tego należy zarezerwować hugepages między ponownymi uruchomieniami.

2. Adresy MAC i IP: Użyj `ifconfig –a` Aby wyświetlić adres IP i MAC interfejsów sieciowych. *VF* interfejsu sieciowego i *NETVSC* interfejsu sieciowego mają ten sam adres MAC, ale tylko wtedy, *NETVSC* interfejs sieciowy ma adres IP. Interfejsy funkcji Wirtualnej są uruchomione jako nadrzędne interfejsy NETVSC interfejsów.

3. Adresy PCI

   * Użyj `ethtool -i <vf interface name>` Aby dowiedzieć się, który adres PCI na potrzeby *VF*.
   * Jeśli *eth0* ma accelerated networking włączona, upewnij się, że testpmd przypadkowo nie przejąć urządzeń ze standardami pci VF dla *eth0*. Jeśli aplikacja DPDK przypadkowo przejmuje interfejs sieci zarządzania i spowoduje utratę połączenia SSH, użyj konsoli szeregowej, aby zatrzymać aplikację DPDK. Można również użyć konsoli szeregowej zatrzymywania lub uruchamiania maszyny wirtualnej.

4. Obciążenia *ibuverbs* podczas każdego ponownego uruchamiania komputera za pomocą `modprobe -a ib_uverbs`. Tylko 15 SLES także załadować *mlx4_ib* z `modprobe -a mlx4_ib`.

## <a name="failsafe-pmd"></a>Przed uszkodzeniami PMD

Aplikacje DPDK należy uruchomić przed uszkodzeniami PMD, która jest widoczna na platformie Azure. Jeśli aplikacja zostanie uruchomiona bezpośrednio nad VF PMD, nie odbiera **wszystkich** pakiety przeznaczone do maszyny Wirtualnej, ponieważ niektóre pakiety wyświetlane nad syntetycznego interfejsu. 

Po uruchomieniu aplikacji DPDK za pośrednictwem przed uszkodzeniami PMD gwarantuje to, że aplikacja odbiera wszystkie pakiety, które są przeznaczone do niego. Zapewnia także się upewnić, że aplikacja będzie kontynuować działanie w trybie DPDK nawet wtedy, gdy VF jest odwoływane, gdy host jest obsługiwany. Aby uzyskać więcej informacji na temat przed uszkodzeniami PMD zobacz [biblioteki sterownika trybu awaryjnego sondowania](https://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Uruchom testpmd

Aby uruchomić testpmd w trybie głównym, użyj `sudo` przed *testpmd* polecenia.

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Tryb podstawowy: Inicjalizacja adaptera przed uszkodzeniami poprawnością kontroli

1. Uruchom następujące polecenia, aby uruchomić aplikację testpmd jednego portu:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Uruchom następujące polecenia, aby uruchomić aplikację testpmd podwójny port:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   W przypadku korzystania z więcej niż dwie karty sieciowe, testpmd `--vdev` argumentu następuje tego wzorca: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`.

3.  Po jej ponownym uruchomieniu, uruchom `show port info all` Aby sprawdzić informacje o porcie. Powinien zostać wyświetlony co najmniej dwóch DPDK porty net_failsafe (nie *net_mlx4*).
4.  Użyj `start <port> /stop <port>` można uruchomić ruchu.

Poprzednie polecenia start *testpmd* w trybie interaktywnym, który jest zalecane w przypadku wypróbowanie testpmd poleceń.

### <a name="basic-single-sendersingle-receiver"></a>Tryb podstawowy: Jednego nadawcy/jednego odbiornika

Następujące polecenia drukowania okresowo pakietów Statystyka na sekundę:

1. Na stronie TX uruchom następujące polecenie:

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

2. Po stronie ODBIERANIA uruchom następujące polecenie:

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

Po uruchomieniu powyższych poleceń na maszynie wirtualnej, należy zmienić *IP_SRC_ADDR* i *IP_DST_ADDR* w `app/test-pmd/txonly.c` dopasować rzeczywistego adresu IP maszyn wirtualnych, zanim kompilacja. W przeciwnym razie pakiety są porzucane przed osiągnięciem odbiornika.

### <a name="advanced-single-sendersingle-forwarder"></a>Zaawansowane: Jednego nadawcy/pojedynczej usługi przesyłania dalej
Następujące polecenia drukowania okresowo pakietów Statystyka na sekundę:

1. Na stronie TX uruchom następujące polecenie:

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

2. Na stronie FWD uruchom następujące polecenie:

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

Po uruchomieniu powyższych poleceń na maszynie wirtualnej, należy zmienić *IP_SRC_ADDR* i *IP_DST_ADDR* w `app/test-pmd/txonly.c` dopasować rzeczywistego adresu IP maszyn wirtualnych, zanim kompilacja. W przeciwnym razie pakiety są porzucane przed osiągnięciem usługi przesyłania dalej. Nie można mieć trzecim komputerze odbierać ruch przesłany dalej, ponieważ *testpmd* usługi przesyłania dalej nie modyfikuje adresów warstwy 3, chyba że wprowadzasz pewne zmiany kodu.

## <a name="references"></a>Dokumentacja

* [Opcje EAL](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Polecenia Testpmd](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
