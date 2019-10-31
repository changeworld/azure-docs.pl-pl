---
title: Testowanie opóźnienia sieci maszyn wirtualnych platformy Azure w usłudze Azure Virtual Network | Microsoft Docs
titleSuffix: Azure Virtual Network latency
description: Dowiedz się, jak testować opóźnienie sieci między maszynami wirtualnymi platformy Azure w sieci wirtualnej
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
ms.openlocfilehash: d5efc1b802246597b4ee545b4d805e4f0d10ebb5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166566"
---
# <a name="testing-network-latency"></a>Testowanie opóźnienia sieci

Mierzenie opóźnień sieci za pomocą narzędzia, które jest przeznaczone do zadania, zapewni najdokładniejsze wyniki. Dostępne publicznie narzędzia, takie jak SockPerf (for Linux) i latte (dla systemu Windows), to przykłady narzędzi, które mogą odizolować i zmierzyć opóźnienia sieci przy jednoczesnym wykorzystaniu innych typów opóźnień, takich jak opóźnienie aplikacji. Te narzędzia koncentrują się na rodzaju ruchu sieciowego, który ma wpływ na wydajność aplikacji, czyli protokoły TCP i UDP. Inne popularne narzędzia do łączności, takie jak polecenie ping, mogą czasami służyć do mierzenia opóźnień, ale te wyniki mogą nie być reprezentatywne dla ruchu sieciowego używanego w rzeczywistych obciążeniach. Te&#39;s, ponieważ większość z tych narzędzi korzysta z protokołu ICMP, który może być traktowany inaczej niż ruch aplikacji, a wyniki mogą nie być stosowane do obciążeń, które korzystają z protokołów TCP i UDP. Aby uzyskać dokładne testy opóźnienia sieci w protokołach używanych przez większość aplikacji, SockPerf (dla systemów Linux) i latte (dla systemu Windows) generują najbardziej odpowiednie wyniki. Ten dokument obejmuje oba te narzędzia.

## <a name="overview"></a>Przegląd

Korzystając z dwóch maszyn wirtualnych, jednego jako nadawcy i jednego jako odbiorcy, tworzony jest dwukierunkowy kanał komunikacyjny służący do wysyłania i odbierania pakietów w obu kierunkach, aby mierzyć czas błądzenia (RTT).

Te kroki mogą służyć do mierzenia opóźnień sieci między dwoma Virtual Machines (maszynami wirtualnymi) lub nawet między dwoma komputerami fizycznymi. Pomiary opóźnień mogą być przydatne w następujących scenariuszach:

- Ustanów wzorzec dla opóźnień sieci między wdrożonymi maszynami wirtualnymi
- Porównaj skutki zmian opóźnienia sieci po wprowadzeniu związanych z nimi zmian:
  - Oprogramowanie systemu operacyjnego lub stosu sieciowego, w tym zmiany konfiguracji
  - Metoda wdrożenia maszyny wirtualnej, na przykład wdrożenie do strefy lub PPG
  - Właściwości maszyny wirtualnej, takie jak przyspieszona zmiana sieci lub rozmiar
  - Sieć wirtualna, taka jak Routing lub zmiany filtrowania

### <a name="tools-for-testing"></a>Narzędzia do testowania
Aby mierzyć opóźnienia, mamy dwie różne opcje, jeden dla systemów z systemem Windows i jeden dla systemów z systemem Linux

Dla systemu Windows: latte. exe (Windows) [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Dla systemu Linux: SockPerf (Linux) [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

Korzystanie z tych narzędzi gwarantuje, że tylko czasy dostarczania protokołu TCP lub UDP są mierzone, a nie protokołu ICMP (ping) lub innych typów pakietów, które nie są używane przez aplikacje i nie wpływają na ich wydajność.

### <a name="tips-for-an-optimal-vm-configuration"></a>Wskazówki dotyczące optymalnej konfiguracji maszyny wirtualnej:

- Korzystanie z najnowszej wersji systemu Windows lub Linux
- Włączanie przyspieszonej sieci pod kątem najlepszych wyników
- Wdrażanie maszyn wirtualnych przy użyciu [grupy umieszczania usługi Azure zbliżeniowe](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
- Większe maszyny wirtualne zwykle działają lepiej niż w przypadku mniejszych maszyn wirtualnych

### <a name="tips-for-analysis"></a>Wskazówki dotyczące analizy

- Wczesne ustanawianie planu bazowego, gdy tylko wdrożenie, konfiguracja i optymalizacje zostaną zakończone
- Zawsze porównuj nowe wyniki do linii bazowej lub w inny sposób z jednego testu do innego ze zmianami kontrolowanymi
- Powtarzaj testy po każdym zaobserwowaniu lub zaplanowaniu zmian


## <a name="testing-vms-running-windows"></a>Testowanie maszyn wirtualnych z systemem Windows:

## <a name="get-latteexe-onto-the-vms"></a>Pobierz latte. exe na maszynach wirtualnych

Pobierz najnowszą wersję: [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Rozważ umieszczenie pliku latte. exe w osobnym folderze, na przykład c:\Tools

## <a name="allow-latteexe-through-the-windows-firewall"></a>Zezwalaj programowi latte. exe za pomocą zapory systemu Windows

Na ODBIORNIKu Utwórz regułę zezwalania w zaporze systemu Windows, aby zezwolić na odbieranie ruchu latte. exe. Najłatwiej zezwolić na cały program latte. exe, zamiast zezwalać na ruch przychodzący określonych portów TCP.

Zezwalaj na latte. exe za pomocą zapory systemu Windows w następujący sposób:

netsh advfirewall firewall add Rule program =\<PATH\>\\latte. exe Name =&quot;latte&quot; Protocol = any dir = in Action = Allow Enable = Yes profil = ANY


Jeśli na przykład skopiowano plik latte. exe do folderu &quot;c:\\Tools&quot;, będzie to polecenie:

```cmd
netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

## <a name="running-latency-tests"></a>Uruchamianie testów opóźnienia

Uruchom program latte. exe w ODBIORNIKu (Uruchom z polecenia CMD, a nie z programu PowerShell):

latte —&gt;adres IP odbiornika &lt;:&lt;&gt; &lt;

Wszystkie iteracje 65 000 dla są wystarczająco długie, aby zwracały reprezentatywne wyniki.

Wszystkie dostępne numery portów są dokładne.

Jeśli maszyna wirtualna ma adres IP 10.0.0.4, będzie wyglądać następująco:

```cmd
latte -a 10.0.0.4:5005 -i 65100
```

Uruchom program latte. exe na NADAWCy (Uruchom polecenie z polecenia CMD, a nie z programu PowerShell):

latte-c \<-\>adres IP odbiornika:\<\> \<


Wyniki polecenia są takie same jak w odniesieniu do odbiornika, z wyjątkiem dodania &quot;-c&quot;, aby wskazać, że jest to &quot;&quot; klienta lub nadawcy:

```cmd
latte -c -a 10.0.0.4:5005 -i 65100
```

Poczekaj na wyniki. W zależności od tego, jak daleko są maszyny wirtualne, może to potrwać kilka minut. Rozważ rozpoczęcie od mniejszej liczby iteracji do przetestowania pod kątem sukcesu przed uruchomieniem dłuższych testów.



## <a name="testing-vms-running-linux"></a>Testowanie maszyn wirtualnych z systemem Linux

Użyj SockPerf. Jest ona dostępna z [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

### <a name="install-sockperf-on-the-vms"></a>Instalowanie SockPerf na maszynach wirtualnych

Na maszynach wirtualnych z systemem Linux (NADAWCy i odbiornik) Uruchom następujące polecenia, aby przygotować SockPerf na maszynach wirtualnych. Polecenia są dostępne dla głównych dystrybucje.

#### <a name="for-rhel--centos-follow-these-steps"></a>W przypadku RHEL/CentOS wykonaj następujące kroki:
```bash
#CentOS / RHEL - Install GIT and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu-follow-these-steps"></a>W przypadku Ubuntu wykonaj następujące kroki:
```bash
#Ubuntu - Install GIT and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros-copy-compile-and-install-sockperf-according-to-the-following-steps"></a>Dla wszystkich dystrybucje, skopiuj, Kompiluj i zainstaluj SockPerf, zgodnie z poniższymi krokami:
```bash
#Bash - all distros

#From bash command line (assumes git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>Uruchom SockPerf na maszynach wirtualnych

Po zakończeniu instalacji SockPerf maszyny wirtualne są gotowe do uruchomienia testów opóźnienia. 

Najpierw Zacznij SockPerf na ODBIORNIKu.

Wszystkie dostępne numery portów są dokładne. W tym przykładzie używamy portu 12345:
```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```
Teraz, gdy serwer nasłuchuje, klient może rozpocząć wysyłanie pakietów do serwera na porcie, na którym nasłuchuje, 12345 w tym przypadku.

Około 100 sekund jest wystarczająco długi, aby zwracał reprezentatywne wyniki, jak pokazano w następującym przykładzie:
```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Poczekaj na wyniki. W zależności od tego, jak daleko są maszyny wirtualne, liczba iteracji będzie się różnić. Rozważ rozpoczęcie krótszych testów wynoszących około 5 sekund, aby przeprowadzić testy pod kątem sukcesu przed uruchomieniem dłuższych testów.

Ten SockPerf przykład używa rozmiaru komunikatu 350 bajtowego, ponieważ jest to typowy pakiet o średnim rozmiarze. Rozmiar komunikatu można zwiększyć lub zmniejszyć, aby osiągnąć wyniki, które bardziej precyzyjnie reprezentują obciążenie, które będzie działać na maszynach wirtualnych.


## <a name="next-steps"></a>Następne kroki
* Poprawianie opóźnień przy użyciu [grupy rozmieszczenia usługi Azure zbliżeniowe](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
* Dowiedz się więcej na temat [optymalizowania sieci maszyn wirtualnych](../virtual-network/virtual-network-optimize-network-bandwidth.md) w danym scenariuszu.
* Przeczytaj o sposobie [przydzielenia przepustowości do maszyn wirtualnych](../virtual-network/virtual-machine-network-throughput.md)
* Dowiedz się więcej na temat [usługi Azure Virtual Network często zadawanych pytań](../virtual-network/virtual-networks-faq.md)
