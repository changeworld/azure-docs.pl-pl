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
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: d09d2bf7d14508b0b8eac955eb2589c0e7384903
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39506514"
---
# <a name="setup-dpdk-in-a-linux-virtual-machine"></a>Instalator DPDK na maszynie wirtualnej systemu Linux

Danych płaszczyzna Development Kit (DPDK) na platformie Azure oferuje szybsze użytkownika miejsca pakietów platforma przetwarzania dla aplikacji intensywnie korzystających z wydajności, które obchodzą stos sieciowy jądra maszyny wirtualnej.

Typowe pakietów przetwarzania za pomocą stosu sieciowego jądra jest przerwania opartych na. Każdorazowo interfejs sieciowy odbiera pakietów przychodzących jest przerwanie jądra, do przetwarzania pakietów i kontekst przełącznika z obszaru jądra przestrzeni użytkownika. DPDK eliminuje przełączania kontekstu i przerwania opartych na metodę na rzecz implementacji miejsca na użytkownika, za pomocą sterowników trybu sondowania dla przetwarzania pakietów szybkie.

DPDK zawiera zbiór bibliotek miejsca na użytkownika, zapewnianie dostępu do zasobów, takich jak sprzętu, rdzenie logiczne, zarządzanie pamięcią niższego poziomu, a następnie wykonać sondowanie sterowniki trybu dla kart interfejsu sieciowego.

DPDK można uruchomić w maszynach wirtualnych platformy Azure, obsługa wielu dystrybucji systemu operacyjnego. DPDK oferuje odmienny wydajności opracowuje funkcję sieciową implementacji wirtualizacji, w postaci wirtualne urządzenia sieciowe (WUS) takich jak wirtualne routera, zapory, sieci VPN, moduł równoważenia obciążenia, core wydzielonego pakietów i typu "odmowa usługi" ( Aplikacje przed atakami DDoS).

## <a name="benefit"></a>Korzyść

**Wyższe pakietów na sekundę (PPS)**: pomijanie jądra i biorąc kontroli pakietów w obszarze użytkownika powoduje zmniejszenie liczby cykli, eliminując przełączenie kontekstu i zwiększa szybkość pakietów przetwarzanych na sekundę na maszynach wirtualnych z systemem Linux platformy Azure.


## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Obsługiwane są poniższe dystrybucje w galerii platformy Azure:

| System operacyjny Linux     | Wersja jądra        |
|--------------|----------------       |
| Ubuntu 16.04 | 4.15.0-1015-Azure     |
| Ubuntu 18.04 | 4.15.0-1015-Azure     |
| SLES 15      | 4.12.14-5.5-Azure     |
| RHEL W WERSJI 7.5     | 3.10.0-862.9.1.el7    |
| CentOS w wersji 7.5   | 3.10.0-862.3.3.el7    |

**Obsługa niestandardowych jądra**

Zapoznaj się [poprawki do tworzenia dostosowanych Azure jądra systemu Linux](https://github.com/microsoft/azure-linux-kernel) dowolna wersja jądra systemu Linux nie są wyświetlane lub w celu uzyskania dalszych informacji, skontaktuj się z pomocą [ azuredpdk@microsoft.com ](mailto:azuredpdk@microsoft.com). 

## <a name="region-support"></a>Obsługa regionu

Wszystkie regiony platformy Azure obsługują DPDK.

## <a name="prerequisites"></a>Wymagania wstępne

Przyspieszona sieć musi być włączona na maszynie wirtualnej systemu Linux. Maszyna wirtualna powinna mieć co najmniej dwa interfejsy sieciowe, za pomocą jednego interfejsu do zarządzania. Dowiedz się, jak [Utwórz maszynę wirtualną systemu Linux z włączoną obsługą przyspieszonej sieci](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>Zainstaluj DPDK zależności

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev
```

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev
```

### <a name="rhel75centos-75"></a>RHEL7.5/CentOS w wersji 7.5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel
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

## <a name="setup-virtual-machine-environment-once"></a>Konfigurowanie środowiska maszyny wirtualnej (raz)

1. [Pobierz najnowsze DPDK](https://core.dpdk.org/download). Wersja 18.02 lub nowszy jest wymagany dla platformy Azure.
2. Zainstaluj *libnuma dev* pakietu z `sudo apt-get install libnuma-dev`.
3. Najpierw utworzyć konfigurację domyślną, za pomocą `make config T=x86_64-native-linuxapp-gcc`.
4. Włącz Mellanox PMDs w wygenerowanym pliku config przy użyciu `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`.
5. Kompiluj przy użyciu `make`.
6. Instalowanie przy użyciu `make install DESTDIR=<output folder>`.

# <a name="configure-runtime-environment"></a>Konfigurowanie środowiska uruchomieniowego

Uruchom następujące polecenia jeden raz, po ponownym uruchomieniu:

1. Hugepages

   * Skonfiguruj hugepage, uruchamiając następujące polecenie, jeden raz dla wszystkich numanodes:

     ```bash
     echo 1024 | sudo tee
     /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   *  Utwórz katalog dla instalowania za pomocą `mkdir /mnt/huge`.
   *  Hugepages instalacji za pomocą `mount -t hugetlbfs nodev /mnt/huge`.
   *  Hugepages wyboru są zarezerwowane przez `grep Huge /proc/meminfo`.

     > [!NOTE]
     > Istnieje sposób do modyfikowania pliku chodników, tak aby ogromna strony są zarezerwowane podczas rozruchu, postępując zgodnie z [instrukcje](http://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) dla DPDK. Instrukcja znajduje się w dolnej części strony. Podczas uruchamiania na maszynie wirtualnej z systemem Linux platformy Azure, należy zamiast tego należy zmodyfikować plików w obszarze /etc/config/grub.d zarezerwować hugepages między ponownymi uruchomieniami.

2. Adresy MAC i IP: Użyj `ifconfig –a` Aby wyświetlić adres IP i MAC interfejsów sieciowych. *VF* interfejsu sieciowego i *NETVSC* interfejsu sieciowego mają ten sam adres MAC, ale tylko wtedy, *NETVSC* interfejs sieciowy ma adres IP.

3. Adresy PCI

   * Dowiedz się, który adres PCI na potrzeby *VF* z `ethtool -i <vf interface name>`.
   * Upewnij się, że testpmd przypadkowo nie przejąć urządzeń ze standardami pci VF dla *eth0*, jeśli *eth0* ma accelerated networking włączone. Jeśli aplikacja DPDK przypadkowo przejmuje interfejs sieci zarządzania i spowoduje utratę połączenia SSH, należy użyć konsoli szeregowej kill DPDK aplikacji, lub do zatrzymywania lub uruchamiania maszyny wirtualnej.

4. Obciążenia *ibuverbs* podczas każdego ponownego uruchamiania komputera za pomocą `modprobe -a ib_uverbs`. Tylko 15 SLES obciążenia *mlx4_ib* za pomocą "modprobe - mlx4_ib".

## <a name="failsafe-pmd"></a>Przed uszkodzeniami PMD

Aplikacje DPDK należy uruchomić przed uszkodzeniami PMD, która jest widoczna na platformie Azure. Jeśli aplikacja zostanie uruchomiona bezpośrednio nad VF PMD, nie będzie odbierać **wszystkich** pakiety przeznaczone do maszyny Wirtualnej, ponieważ niektóre pakiety pojawią się nad syntetycznego interfejsu. Uruchomiony za pośrednictwem przed uszkodzeniami, który PMD gwarantuje, że aplikacja odbiera wszystkie pakiety przeznaczone do niego i gwarantuje również, aplikacja będzie działać w trybie DPDK nawet wtedy, gdy VF jest odwoływane, gdy host jest obsługiwany. Aby uzyskać więcej informacji na temat przed uszkodzeniami PMD, zobacz [biblioteki sterownika trybu awaryjnego sondowania](http://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Uruchom testpmd

Użyj `sudo` przed *testpmd* polecenie do uruchomienia w trybie głównym.

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Podstawowe: Sprawdź poprawnością, przed uszkodzeniami Inicjalizacja adaptera

1. Uruchom następujące polecenia, aby uruchomić aplikację jednego portu:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -i \
     --port-topology=chained
    ```

2. Uruchom następujące polecenia, aby uruchomić aplikację podwójny port:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -i
   ```

   Jeśli więcej niż 2 karty sieciowe, `--vdev` argumentu następuje tego wzorca: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`.

3.  Uruchom po rozpoczęciu `show port info all` Aby sprawdzić informacje o porcie. Powinien zostać wyświetlony co najmniej dwóch DPDK porty net_failsafe (nie *net_mlx4*).
4.  Użyj `start <port> /stop <port>` można uruchomić ruchu.

Poprzednie polecenia start *testpmd* w trybie interaktywnym, który zaleca się, aby wypróbować niektóre polecenia testpmd.

### <a name="basic-single-sendersingle-receiver"></a>Basic: Jednego nadawcy/jednego odbiornika

Następujące polecenia drukowania okresowo pakietów Statystyka na sekundę:

1. Na stronie TX uruchom następujące polecenie:

   ```bash
   Testpmd \
     –l <core-mask> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev=”net_vdev_netvsc<id>,iface=<the iface to attach to>” \
     --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     –eth-peer=<port id>,<peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. Po stronie ODBIERANIA uruchom następujące polecenie:

   ```bash
   Testpmd \
     –l <core-mask> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev net_vdev_netvsc<id>,iface=<the iface to attach to>” \
     --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     –eth-peer=<port id>,<peer MAC address> \
     --stats-period <display interval in seconds>
   ```

Podczas uruchamiania poprzednich poleceń na maszynie wirtualnej, należy zmienić *IP_SRC_ADDR* i *IP_DST_ADDR* w `app/test-pmd/txonly.c` dopasować rzeczywistego adresu IP maszyn wirtualnych, zanim kompilacja. W przeciwnym razie pakiety są porzucane przed osiągnięciem odbiornika.

### <a name="advanced-single-sendersingle-forwarder"></a>Zaawansowane: Usługa przesyłania dalej jednego nadawcy/pojedynczego
Następujące polecenia drukowania okresowo pakietów Statystyka na sekundę:

1. Na stronie TX uruchom następujące polecenie:

   ```bash
   Testpmd \
     –l <core-mask> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev net_vdev_netvsc<id>,iface=<the iface to attach to>” \
     --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     –eth-peer=<port id>,<peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. Na stronie FWD uruchom następujące polecenie:

   ```bash
   Testpmd \
     –l <core-mask> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev=”net_vdev_netvsc<id>,iface=<the iface to attach to>” \
     --vdev=” net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>” (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     –eth-peer=<recv port id>,<peer MAC address> \
     --stats-period <display interval in seconds>
    ```

Podczas uruchamiania poprzednich poleceń na maszynie wirtualnej, należy zmienić *IP_SRC_ADDR* i *IP_DST_ADDR* w `app/test-pmd/txonly.c` dopasować rzeczywistego adresu IP maszyn wirtualnych, zanim kompilacja. W przeciwnym razie pakiety są porzucane przed osiągnięciem usługi przesyłania dalej. Nie można mieć trzecim komputerze odbierać ruch przesłany dalej, ponieważ *testpmd* usługi przesyłania dalej nie modyfikuje adresów warstwy 3, chyba że wprowadzasz pewne zmiany kodu.

## <a name="references"></a>Dokumentacja

* [Opcje EAL](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Polecenia Testpmd](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
