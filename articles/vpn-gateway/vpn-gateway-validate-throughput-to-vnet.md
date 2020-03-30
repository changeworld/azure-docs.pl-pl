---
title: Sprawdzanie poprawności przepływności sieci VPN w sieci wirtualnej platformy Microsoft Azure
description: Celem tego dokumentu jest pomoc użytkownikowi w weryfikacji przepływności sieci z ich zasobów lokalnych do maszyny wirtualnej platformy Azure.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 05/29/2019
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: a88e339e82484c2ec1cd2276f6218fa718b990f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75860490"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Jak zweryfikować przepływność sieci VPN do sieci wirtualnej

Połączenie bramy sieci VPN umożliwia ustanowienie bezpiecznej, międzylokajnej łączności między siecią wirtualną na platformie Azure a lokalną infrastrukturą IT.

W tym artykule pokazano, jak sprawdzić poprawność przepływności sieci z zasobów lokalnych do maszyny wirtualnej platformy Azure (VM).

> [!NOTE]
> Ten artykuł ma na celu pomóc zdiagnozować i rozwiązać typowe problemy. Jeśli nie możesz rozwiązać problemu, korzystając z następujących informacji, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="overview"></a>Omówienie

Połączenie bramy sieci VPN obejmuje następujące składniki:

* Lokalne urządzenie sieci VPN (wyświetl listę [zweryfikowanych urządzeń sieci VPN).](vpn-gateway-about-vpn-devices.md#devicetable)
* Publiczny internet
* Brama sieci VPN platformy Azure
* Maszyna wirtualna platformy Azure

Na poniższym diagramie przedstawiono logiczną łączność sieci lokalnej z siecią wirtualną platformy Azure za pośrednictwem sieci VPN.

![Logiczna łączność sieci klienta z siecią MSFT przy użyciu sieci VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Obliczanie maksymalnego oczekiwanego ruchu przychodzącego/wychodzącego

1. Określ wymagania dotyczące przepływności według planu bazowego aplikacji.
1. Określ limity przepływności bramy sieci VPN platformy Azure. Aby uzyskać pomoc, zobacz sekcję "Jednostki SKU bramy" [w temacie Informacje o bramie sieci VPN](vpn-gateway-about-vpngateways.md#gwsku).
1. Określ [wskazówki dotyczące przepływności maszyny Wirtualnej platformy Azure](../virtual-machines/virtual-machines-windows-sizes.md) dla rozmiaru maszyny Wirtualnej.
1. Określ przepustowość usługodawcy internetowego (ISP).
1. Oblicz oczekiwaną przepustowość, biorąc najmniejszą przepustowość maszyny Wirtualnej, bramy sieci VPN lub usługodawcy w sieci. mierzona w Megabitach na sekundę (/) podzielona przez osiem (8).

Jeśli obliczona przepływność nie spełnia wymagań dotyczących przepływności według planu bazowego aplikacji, należy zwiększyć przepustowość zasobu zidentyfikowanego jako wąskie gardło. Aby zmienić rozmiar bramy sieci VPN platformy Azure, zobacz [Zmienianie jednostki SKU bramy](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Aby zmienić rozmiar maszyny wirtualnej, zobacz [Ponowne rozmiary maszyny wirtualnej](../virtual-machines/virtual-machines-windows-resize-vm.md). Jeśli oczekiwana przepustowość Internetu nie jest dostępna, możesz również skontaktować się z usługodawcą internetowym.

> [!NOTE]
> Przepustowość bramy sieci VPN jest agregacja wszystkich lokacji do lokacji\sieci wirtualnej do sieci wirtualnej lub połączenia typu punkt-lokacja.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Sprawdzanie poprawności przepływności sieci przy użyciu narzędzi wydajności

To sprawdzanie poprawności należy wykonać w godzinach poza szczytem, jak nasycenie przepływności tunelu sieci VPN podczas testowania nie daje dokładne wyniki.

Narzędziem, którego używamy do tego testu, jest iPerf, który działa zarówno w systemie Windows, jak i Linux i ma zarówno tryb klienta, jak i serwera. Jest ograniczona do 3Gbps dla maszyn wirtualnych z systemem Windows.

To narzędzie nie wykonuje żadnych operacji odczytu/zapisu na dysku. Produkuje wyłącznie własny generowany ruch TCP z jednego końca na drugi. Generuje statystyki na podstawie eksperymentów, który mierzy przepustowość dostępną między węzłami klienta i serwera. Podczas testowania między dwoma węzłami jeden węzeł działa jako serwer, a drugi węzeł działa jako klient. Po zakończeniu tego testu zaleca się odwrócenie ról węzłów, aby przetestować przepływność przekazywania i pobierania w obu węzłach.

### <a name="download-iperf"></a>Pobierz iPerf

Pobierz [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Aby uzyskać szczegółowe informacje, zobacz [dokumentację iPerf](https://iperf.fr/iperf-doc.php).

 > [!NOTE]
 > Produkty innych firm omówione w tym artykule są produkowane przez firmy, które są niezależne od firmy Microsoft. Firma Microsoft nie udziela żadnych gwarancji, dorozumianych ani żadnego innego rodzaju, w odniesieniu do wydajności ani niezawodności tych produktów.

### <a name="run-iperf-iperf3exe"></a>Uruchamianie iPerf (iperf3.exe)

1. Włącz regułę listy NSG/ACL zezwalającą na ruch (do testowania publicznego adresu IP na maszynie Wirtualnej platformy Azure).

1. W obu węzłach włącz wyjątek zapory dla portu 5001.

   **Windows:** Uruchom następujące polecenie jako administrator:

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   Aby usunąć regułę po zakończeniu testowania, uruchom to polecenie:

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **System Azure Linux:** Obrazy systemu Azure Linux mają permisywne zapory. Jeśli istnieje aplikacja nasłuchiwania na porcie, ruch jest dozwolony za pośrednictwem. Obrazy niestandardowe, które są zabezpieczone może wymagać portów otwarte jawnie. Typowe zapory systemu operacyjnego `iptables` `ufw`Linux `firewalld`obejmują , lub .

1. W węźle serwera zmień katalog, w którym wyodrębniany jest plik iperf3.exe. Następnie uruchom iPerf w trybie serwera i ustaw go tak, aby nasłuchiwać na porcie 5001 jako następujące polecenia:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > Port 5001 można dostosować, aby uwzględnić określone ograniczenia zapory w danym środowisku.

1. W węźle klienta zmień katalog, w którym wyodrębniane jest narzędzie iperf, a następnie uruchom następujące polecenie:

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   Klient kieruje trzydzieści sekund ruchu na porcie 5001, do serwera. Flaga '-P ' wskazuje, że nawiązujemy 32 jednoczesne połączenia z węzłem serwera.

   Na poniższym ekranie przedstawiono dane wyjściowe z tego przykładu:

   ![Dane wyjściowe](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. (OPCJONALNIE) Aby zachować wyniki testów, uruchom to polecenie:

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. Po wykonaniu poprzednich kroków wykonaj te same kroki z rolami odwróconymi, dzięki czemu węzeł serwera będzie teraz węzłem klienta i odwrotnie.

> [!Note]
> Iperf nie jest jedynym narzędziem. [NTTTCP jest alternatywnym rozwiązaniem do testowania.](https://docs.microsoft.com/azure/virtual-network/virtual-network-bandwidth-testing)

## <a name="test-vms-running-windows"></a>Testowanie maszyn wirtualnych z systemem Windows

### <a name="load-latteexe-onto-the-vms"></a>Załaduj program Latte.exe na maszyny wirtualne

Pobierz najnowszą wersję [programu Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Rozważ umieszczenie pliku Latte.exe w osobnym folderze, takim jak`c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>Zezwalaj na program Latte.exe przez zaporę systemu Windows

W odbiorniku utwórz regułę Zezwalaj na Zaporze systemu Windows, aby umożliwić przychodzący ruch Latte.exe. Najłatwiej jest zezwolić na cały program Latte.exe według nazwy, a nie zezwolić na przychodzące określone porty TCP.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>Zezwalaj na program Latte.exe przez Zaporę systemu Windows w ten sposób

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

Na przykład, jeśli latte.exe został skopiowany do folderu "c:\tools", będzie to polecenie

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Uruchamianie testów opóźnienia

Uruchom latte.exe na odbiorniku (uruchom z CMD, a nie z programu PowerShell):

`latte -a <Receiver IP address>:<port> -i <iterations>`

Około 65k iteracji jest wystarczająco długi, aby powrócić reprezentatywne wyniki.

Każdy dostępny numer portu jest w porządku.

Jeśli maszyna wirtualna ma adres IP 10.0.0.4, będzie wyglądać tak

`latte -c -a 10.0.0.4:5005 -i 65100`

Uruchom latte.exe na nadawcy (uruchom z CMD, a nie z programu PowerShell)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

Wynikowe polecenie jest takie samo jak w odbiorniku, z wyjątkiem dodatku "-c", aby wskazać, że jest to "klient" lub nadawca

`latte -c -a 10.0.0.4:5005 -i 65100`

Poczekaj na wyniki. W zależności od tego, jak daleko od siebie są maszyny wirtualne, może upłynąć kilka minut, aby zakończyć. Należy rozważyć rozpoczęcie z mniejszą liczbą iteracji, aby przetestować pod kątem powodzenia przed uruchomieniem dłuższych testów.

## <a name="test-vms-running-linux"></a>Testowanie maszyn wirtualnych z systemem Linux

Użyj [SockPerf](https://github.com/mellanox/sockperf) do testowania maszyn wirtualnych.

### <a name="install-sockperf-on-the-vms"></a>Instalowanie aplikacji SockPerf na maszynach wirtualnych

Na maszynach wirtualnych z systemem Linux (zarówno nadawca, jak i odbiorca) uruchom następujące polecenia, aby przygotować SockPerf na maszynach wirtualnych:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS / RHEL - Zainstaluj GIT i inne pomocne narzędzia

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu - Zainstaluj GIT i inne pomocne narzędzia

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash - wszystkie

Z wiersza polecenia bash (zakłada git jest zainstalowany)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

Make jest wolniejszy, może potrwać kilka minut

`make`

Spraw, aby instalacja była szybka

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>Uruchamianie sockperf na maszynach wirtualnych

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>Przykładowe polecenia po instalacji. Serwer/odbiornik - zakłada, że adres IP serwera wynosi 10.0.0.4

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>Klient - zakłada, że adres IP serwera to 10.0.0.4

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> Upewnij się, że nie ma żadnych przeskoków pośrednich (np. urządzenia wirtualnego) podczas testowania przepływności między maszyną wirtualną a bramą.
> Jeśli istnieją słabe wyniki (pod względem ogólnej przepustowości) pochodzące z testów iPERF/NTTTCP powyżej, zapoznaj się z następującym artykułem, aby zrozumieć kluczowe czynniki stojące za możliwymi przyczynami problemu:https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

W szczególności analiza śladów przechwytywania pakietów (Wireshark/Network Monitor) zebranych równolegle z klienta i serwera podczas tych testów pomoże w ocenie złej wydajności. Te ślady mogą obejmować utratę pakietów, duże opóźnienie, rozmiar jednostki MTU. fragmentacja, okno TCP 0, fragmenty poza kolejnością i tak dalej.

## <a name="address-slow-file-copy-issues"></a>Rozwiązywanie problemów z powolnym kopiowaniem plików

Nawet jeśli ogólna przepływność oceniana przy użyciu poprzednich kroków (iPERF/NTTTCP/etc.. była dobra), podczas korzystania z Eksploratora Windows lub przeciągania i upuszczania przez sesję RDP może wystąpić powolne radzenie sobie z plikami. Ten problem jest zwykle ze względu na jeden lub oba z następujących czynników:

* Aplikacje do kopiowania plików, takie jak Eksplorator Windows i RDP, nie używają wielu wątków podczas kopiowania plików. Aby uzyskać lepszą wydajność, użyj aplikacji do kopiowania plików wielowątkowych, takich jak [Richcopy,](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) aby skopiować pliki przy użyciu 16 lub 32 wątków. Aby zmienić numer wątku dla kopiowania **Action** > plików w richkopii, kliknij pozycję**Opcje** > kopiowania akcji Kopia**pliku**.

   ![Powolne problemy z kopiowaniem plików](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > Nie wszystkie aplikacje działają tak samo, a nie wszystkie aplikacje/proces wykorzystuje wszystkie wątki. Po uruchomieniu testu, można zobaczyć niektóre wątki są puste i nie zapewni dokładne wyniki przepływności.
   > Aby sprawdzić wydajność transferu plików aplikacji, należy użyć wielowych, zwiększając numer wątku po kolei lub zmniejszyć, aby znaleźć optymalną przepustowość transferu aplikacji lub pliku.

* Niewystarczająca szybkość odczytu/zapisu dysku maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z usługą Azure Storage](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Interfejs zewnętrzny urządzenia lokalnego

Wspomniano o podsieciach zakresów lokalnych, do których platforma Azure ma dotrzeć za pośrednictwem sieci VPN w bramie sieci lokalnej. Jednocześnie zdefiniuj przestrzeń adresową sieci wirtualnej na platformie Azure na urządzeniu lokalnym.

* **Brama oparta na trasach:** Zasady lub selektor ruchu dla sieci VPN opartych na trasach są skonfigurowane jako karty typu any-to-any (lub symboli wieloznacznych).

* **Brama oparta na zasadach:** Sieci VPN oparte na zasadach szyfrują i kierują pakiety za pośrednictwem tuneli IPsec na podstawie kombinacji prefiksów adresów między siecią lokalną a siecią wirtualną platformy Azure. Zasady (lub selektor ruchu) są zazwyczaj zdefiniowane jako lista dostępu w konfiguracji VPN.

* **Użyj PołączeniaPolcyBasedTrafficSelector:** ("UsePolicyBasedTrafficSelectors", aby $True na połączenie skonfiguruje bramę sieci VPN platformy Azure, aby łączyć się z zaporą sieci VPN opartą na zasadach w środowisku lokalnym. Jeśli włączysz policybasedtrafficSelectors, musisz upewnić się, że urządzenie SIECI VPN ma pasujące selektory ruchu zdefiniowane ze wszystkimi kombinacjami prefiksów sieci lokalnej (bramy sieci lokalnej) do i z prefiksów sieci wirtualnej platformy Azure, zamiast w dowolnym momencie.

Niewłaściwa konfiguracja może prowadzić do częstych rozłączeń w tunelu, spadków pakietów, złej przepływności i opóźnienia.

## <a name="check-latency"></a>Sprawdź opóźnienie

Opóźnienie można sprawdzić za pomocą następujących narzędzi:

* WinMTR (właśc.
* Protokół TCPTraceroute
* `ping`i `psping` (Narzędzia te mogą zapewnić dobre oszacowanie RTT, ale nie mogą być używane we wszystkich przypadkach.)

![Sprawdź opóźnienie](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

Jeśli zauważysz skok dużych opóźnień w dowolnym z przeskoków przed wejściem w szkielet sieci MS Network, możesz kontynuować dochodzenia z dostawcą usług internetowych.

Jeśli duży, nietypowy skok opóźnienia zostanie zauważony z przeskoków w ramach "msn.net", skontaktuj się z pomocą techniczną MS w celu przeprowadzenia dalszych badań.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji lub pomoc, zapoznaj się z poniższym linkiem:

* [Pomoc techniczna firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
