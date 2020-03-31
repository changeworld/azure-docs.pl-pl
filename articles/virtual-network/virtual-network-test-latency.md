---
title: Testowanie opóźnienia sieci maszyny wirtualnej platformy Azure w sieci wirtualnej platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak przetestować opóźnienie sieci między maszynami wirtualnymi platformy Azure w sieci wirtualnej
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 00efc2754948d53d4f80a6261dbd4041b358185b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896357"
---
# <a name="test-vm-network-latency"></a>Testowanie opóźnienia sieci maszyn wirtualnych

Aby uzyskać najdokładniejsze wyniki, zmierz opóźnienie sieci maszyny wirtualnej platformy Azure za pomocą narzędzia, które jest przeznaczone do zadania. Publicznie dostępne narzędzia, takie jak SockPerf (dla systemu Linux) i latte.exe (dla systemu Windows) mogą izolować i mierzyć opóźnienia sieci, wyłączając inne typy opóźnień, takie jak opóźnienie aplikacji. Narzędzia te koncentrują się na rodzaju ruchu sieciowego, który wpływa na wydajność aplikacji (a mianowicie, Transmission Control Protocol [TCP] i User Datagram Protocol [UDP] ruchu). 

Inne typowe narzędzia łączności, takie jak Ping, mogą mierzyć opóźnienia, ale ich wyniki mogą nie reprezentować ruchu sieciowego używanego w rzeczywistych obciążeniach. Dzieje się tak dlatego, że większość z tych narzędzi korzysta z protokołu ICMP (Internet Control Message Protocol), który może być traktowany inaczej niż ruch aplikacji i którego wyniki mogą nie dotyczyć obciążeń korzystających z protokołu TCP i UDP. 

Aby uzyskać dokładne testowanie opóźnień sieci protokołów używanych przez większość aplikacji, SockPerf (dla systemu Linux) i latte.exe (dla systemu Windows) dają najbardziej odpowiednie wyniki. W tym artykule omówiono oba te narzędzia.

## <a name="overview"></a>Omówienie

Za pomocą dwóch maszyn wirtualnych, jeden jako nadawca i jeden jako odbiornik, można utworzyć kanał komunikacji dwukierunkowej. Dzięki takiemu podejściu można wysyłać i odbierać pakiety w obu kierunkach i mierzyć czas podróży w obie strony (RTT).

Za pomocą tego podejścia można zmierzyć opóźnienie sieci między dwoma maszynami wirtualnymi lub nawet między dwoma komputerami fizycznymi. Pomiary opóźnienia mogą być przydatne w następujących scenariuszach:

- Ustanowienie punktu odniesienia dla opóźnienia sieci między wdrożonymi maszynami wirtualnymi.
- Porównaj skutki zmian w opóźnieniu sieci po dokonaniu powiązanych zmian w:
  - System operacyjny (OS) lub oprogramowanie stosu sieciowego, w tym zmiany konfiguracji.
  - Metoda wdrażania maszyny Wirtualnej, na przykład wdrażanie w strefie dostępności lub grupie miejsc docelowych zbliżeniowych (PPG).
  - Właściwości maszyny Wirtualnej, takie jak przyspieszona sieć lub zmiany rozmiaru.
  - Sieć wirtualna, na przykład zmiany routingu lub filtrowania.

### <a name="tools-for-testing"></a>Narzędzia do testowania
Aby zmierzyć opóźnienie, dostępne są dwie różne opcje narzędzia:

* Dla systemów Windows: [latte.exe (Windows)](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)
* Dla systemów opartych na Linuksie: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Korzystając z tych narzędzi, można zapewnić, że mierzone są tylko czasy dostarczania ładunku TCP lub UDP, a nie ICMP (Ping) lub inne typy pakietów, które nie są używane przez aplikacje i nie wpływają na ich wydajność.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>Wskazówki dotyczące tworzenia optymalnej konfiguracji maszyny Wirtualnej

Podczas tworzenia konfiguracji maszyny Wirtualnej należy pamiętać o następujących zaleceniach:
- Użyj najnowszej wersji systemu Windows lub Linux.
- Włącz przyspieszoną sieć, aby uzyskać najlepsze wyniki.
- Wdrażanie maszyn wirtualnych z [grupą miejsc docelowych zbliżeniowych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location).
- Większe maszyny wirtualne zazwyczaj działają lepiej niż mniejsze maszyny wirtualne.

### <a name="tips-for-analysis"></a>Wskazówki dotyczące analizy

Analizując wyniki testów, należy pamiętać o następujących zaleceniach:

- Ustal punkt odniesienia wcześnie, zaraz po zakończeniu wdrażania, konfiguracji i optymalizacji.
- Zawsze porównuj nowe wyniki z linią bazową lub, w przeciwnym razie, z jednego testu do drugiego z kontrolowanymi zmianami.
- Powtarzaj testy za każdym razem, gdy zmiany są obserwowane lub planowane.


## <a name="test-vms-that-are-running-windows"></a>Testowanie maszyn wirtualnych z systemem Windows

### <a name="get-latteexe-onto-the-vms"></a>Pobierz latte.exe na maszyny wirtualne

Pobierz [najnowszą wersję latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b).

Rozważ umieszczenie pliku latte.exe w osobnym folderze, takim jak *c:\tools*.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Zezwalaj na program latte.exe za pośrednictwem Zapory usługi Windows Defender

W *odbiorniku*utwórz regułę Zezwalaj na Zaporę usługi Windows Defender, aby umożliwić nadejście ruchu latte.exe. Najłatwiej jest zezwolić na cały program latte.exe według nazwy, a nie zezwolić na przychodzące określone porty TCP.

Zezwól na program latte.exe za pośrednictwem Zapory usługi Windows Defender, uruchamiając następujące polecenie:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Na przykład, jeśli latte.exe został skopiowany do folderu *c:\tools,* będzie to polecenie:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Uruchamianie testów opóźnienia

* Na *odbiorniku*, uruchom latte.exe (uruchom go z okna CMD, a nie z programu PowerShell):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    Około 65 000 iteracji jest wystarczająco długich, aby przywrócić reprezentatywne wyniki.

    Każdy dostępny numer portu jest w porządku.

    Jeśli maszyna wirtualna ma adres IP 10.0.0.4, polecenie będzie wyglądać następująco:

    `latte -a 10.0.0.4:5005 -i 65100`

* Na *nadawcy*, uruchom latte.exe (uruchom go z okna CMD, a nie z programu PowerShell):

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    Wynikowe polecenie jest takie samo jak w odbiorniku, z wyjątkiem&nbsp;dodania *-c,* aby wskazać, że jest to *klient*lub *nadawca:*

    `latte -c -a 10.0.0.4:5005 -i 65100`

Poczekaj na wyniki. W zależności od tego, jak daleko od siebie są maszyny wirtualne, test może potrwać kilka minut, aby zakończyć. Należy rozważyć rozpoczęcie z mniejszą liczbą iteracji, aby przetestować pod kątem powodzenia przed uruchomieniem dłuższych testów.

## <a name="test-vms-that-are-running-linux"></a>Testowanie maszyn wirtualnych z systemem Linux

Aby przetestować maszyny wirtualne z systemem Linux, użyj [SockPerf](https://github.com/mellanox/sockperf).

### <a name="install-sockperf-on-the-vms"></a>Instalowanie aplikacji SockPerf na maszynach wirtualnych

Na maszynach wirtualnych z systemem Linux zarówno *nadawcy,* jak i *odbiorcy,* uruchom następujące polecenia, aby przygotować SockPerf na maszynach wirtualnych. Polecenia są dostarczane dla głównych dystrybucji.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>Dla Red Hat Enterprise Linux (RHEL)/CentOS

Uruchom następujące polecenia:

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>Dla Ubuntu

Uruchom następujące polecenia:

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>Dla wszystkich dystrybucji

Kopiowanie, kompilowanie i instalowanie aplikacji SockPerf zgodnie z następującymi krokami:

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>Uruchamianie sockperf na maszynach wirtualnych

Po zakończeniu instalacji SockPerf maszyny wirtualne są gotowe do uruchomienia testów opóźnienia. 

Najpierw uruchom SockPerf na *odbiorniku*.

Każdy dostępny numer portu jest w porządku. W tym przykładzie używamy portu 12345:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Teraz, gdy serwer nasłuchuje, klient może rozpocząć wysyłanie pakietów do serwera na porcie, na którym nasłuchuje (w tym przypadku 12345).

Około 100 sekund jest wystarczająco długi, aby powrócić reprezentatywne wyniki, jak pokazano w poniższym przykładzie:

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Poczekaj na wyniki. W zależności od tego, jak daleko od siebie są maszyny wirtualne, liczba iteracji będzie się różnić. Aby przetestować pod kątem powodzenia przed uruchomieniem dłuższych testów, należy rozważyć rozpoczęcie krótszych testów o około 5 sekund.

W tym przykładzie SockPerf używa rozmiaru wiadomości 350 bajtów, który jest typowy dla przeciętnego pakietu. Można dostosować rozmiar wyższy lub niższy, aby osiągnąć wyniki, które dokładniej reprezentują obciążenie uruchomione na maszynach wirtualnych.


## <a name="next-steps"></a>Następne kroki
* Zwiększ opóźnienie dzięki [grupie miejsc docelowych zbliżeniowych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location).
* Dowiedz się, jak [zoptymalizować sieć dla maszyn wirtualnych](../virtual-network/virtual-network-optimize-network-bandwidth.md) w swoim scenariuszu.
* Przeczytaj o [tym, jak przepustowość jest przydzielana do maszyn wirtualnych](../virtual-network/virtual-machine-network-throughput.md).
* Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-faq.md).
