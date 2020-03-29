---
title: Dostrajanie wydajności protokołu TCP/IP dla maszyn wirtualnych platformy Azure | Dokumenty firmy Microsoft
description: Poznaj różne typowe techniki dostrajania wydajności TCP/IP i ich relacje z maszynami wirtualnymi platformy Azure.
services: virtual-network
documentationcenter: na
author: rimayber
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: rimayber
ms.reviewer: dgoddard, stegag, steveesp, minale, btalb, prachank
ms.openlocfilehash: bb23484903ac3ce129c6e7a7a27e0765c227fb1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68297783"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>Dostosowywanie wydajności protokołu TCP/IP dla maszyn wirtualnych platformy Azure

W tym artykule omówiono typowe techniki dostrajania wydajności protokołu TCP/IP i niektóre kwestie, które należy wziąć pod uwagę podczas używania ich do maszyn wirtualnych uruchomionych na platformie Azure. Zapewni podstawowy przegląd technik i zbada, w jaki sposób można je dostroić.

## <a name="common-tcpip-tuning-techniques"></a>Typowe techniki strojenia TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload"></a>Jednostki MTU, fragmentacja i duże odciążanie wysyłania

#### <a name="mtu"></a>Mtu

Maksymalna jednostka transmisji (MTU) to największa ramka (pakiet), określona w bajtach, która może być wysyłana za pośrednictwem interfejsu sieciowego. Mtu jest konfigurowalne ustawienie. Domyślna jednostka MTU używana na maszynach wirtualnych platformy Azure i domyślne ustawienie na większości urządzeń sieciowych globalnie wynosi 1500 bajtów.

#### <a name="fragmentation"></a>Fragmentacji

Fragmentacja występuje, gdy pakiet jest wysyłany, który przekracza jednostkę MTU interfejsu sieciowego. Stos TCP/IP podzieli pakiet na mniejsze kawałki (fragmenty), które są zgodne z jednostką MTU interfejsu. Fragmentacja występuje w warstwie IP i jest niezależna od protokołu źródłowego (takiego jak TCP). Gdy pakiet 2000 bajtów jest wysyłany za pośrednictwem interfejsu sieciowego o jednostce MTU 1500, pakiet zostanie podzielony na jeden pakiet 1500-bajtowy i jeden pakiet 500-bajtowy.

Urządzenia sieciowe w ścieżce między źródłem a miejscem docelowym mogą upuszczać pakiety, które przekraczają jednostkę MTU, lub fragmentować pakiet na mniejsze kawałki.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>Bit Nie fragmentuj w pakiecie IP

Bit Nie należy fragmentować (DF) jest flagą w nagłówku protokołu IP. Bit DF wskazuje, że urządzenia sieciowe na ścieżce między nadawcą a odbiorcą nie mogą fragmentaryzować pakietu. Ten bit może być ustawiony z wielu powodów. (Zobacz sekcję "Odnajdowanie jednostki MTU ścieżki" w tym artykule, aby uzyskać jeden przykład). Gdy urządzenie sieciowe odbiera pakiet z zestawem bitów Nie fragmentuj, a ten pakiet przekracza jednostkę MTU interfejsu urządzenia, standardowe zachowanie jest dla urządzenia, aby upuścić pakiet. Urządzenie wysyła komunikat fragmentacji ICMP potrzebne do oryginalnego źródła pakietu.

#### <a name="performance-implications-of-fragmentation"></a>Wpływ fragmentacji na wydajność

Fragmentacja może mieć negatywny wpływ na wydajność. Jednym z głównych powodów wpływu na wydajność jest wpływ cpu/pamięci fragmentacji i ponownego montażu pakietów. Gdy urządzenie sieciowe musi fragmentować pakiet, będzie musiało przydzielić zasoby procesora CPU/pamięci do wykonania fragmentacji.

To samo dzieje się, gdy pakiet jest ponownie zmontowany. Urządzenie sieciowe musi przechowywać wszystkie fragmenty, dopóki nie zostanie odebrane, aby można je ponownie złożyć w oryginalnym pakiecie. Ten proces fragmentacji i ponownego montażu może również powodować opóźnienie.

Inne możliwe negatywne skutki fragmentacji wydajności jest, że pofragmentowane pakiety mogą pochodzić poza kolejnością. Gdy pakiety są odbierane poza kolejnością, niektóre typy urządzeń sieciowych mogą je upuścić. W takim przypadku cały pakiet musi zostać przekazany do ponownego przesłania.

Fragmenty są zazwyczaj usuwane przez urządzenia zabezpieczające, takie jak zapory sieciowe lub gdy bufory odbierania urządzenia sieciowego są wyczerpane. Po wyczerpaniu buforów odbierania urządzenia sieciowego urządzenie sieciowe próbuje ponownie zarysować pofragmentowany pakiet, ale nie ma zasobów do przechowywania i ponownego tworzenia pakietu.

Fragmentacja może być postrzegana jako operacja ujemna, ale obsługa fragmentacji jest konieczna, gdy łączysz różne sieci przez Internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Korzyści i konsekwencje modyfikacji mtu

Ogólnie rzecz biorąc, można utworzyć bardziej wydajną sieć, zwiększając jednostkę MTU. Każdy pakiet, który jest przesyłany ma informacje nagłówka, który jest dodawany do oryginalnego pakietu. Gdy fragmentacja tworzy więcej pakietów, jest więcej narzutów nagłówka, co sprawia, że sieć mniej wydajne.

Oto przykład. Rozmiar nagłówka Ethernet wynosi 14 bajtów plus sekwencja sprawdzania ramki 4-bajtowej, aby zapewnić spójność ramki. Jeśli wysyłany jest jeden pakiet 2000 bajtów, w sieci jest dodawanych 18 bajtów narzutów ethernetowych. Jeśli pakiet jest podzielony na pakiet 1500-bajtowy i pakiet 500-bajtowy, każdy pakiet będzie miał 18 bajtów nagłówka Ethernet, w sumie 36 bajtów.

Należy pamiętać, że zwiększenie jednostki MTU niekoniecznie spowoduje utworzenie bardziej wydajnej sieci. Jeśli aplikacja wysyła tylko pakiety 500 bajtów, będzie istniało to samo obciążenie nagłówka, niezależnie od tego, czy jednostka MTU ma 1500 bajtów, czy 9000 bajtów. Sieć stanie się bardziej wydajna tylko wtedy, gdy używa większych rozmiarów pakietów, których dotyczy mtu.

#### <a name="azure-and-vm-mtu"></a>Platforma Azure i maszyna wirtualna MTU

Domyślna jednostka MTU dla maszyn wirtualnych platformy Azure wynosi 1500 bajtów. Stos sieci wirtualnej platformy Azure spróbuje fragmentować pakiet na 1400 bajtów.

Należy zauważyć, że stos sieci wirtualnej nie jest z natury nieefektywne, ponieważ fragmentuje pakiety na 1400 bajtów, mimo że maszyny wirtualne mają jednostkę MTU 1500. Duży odsetek pakietów sieciowych jest znacznie mniejszy niż 1400 lub 1500 bajtów.

#### <a name="azure-and-fragmentation"></a>Platforma Azure i fragmentacja

Stos sieci wirtualnej jest skonfigurowany do upuszczania "fragmentów kolejności", czyli pofragmentowanych pakietów, które nie docierają do ich oryginalnej kolejności pofragmentowanych. Pakiety te są usuwane głównie z powodu luki w zabezpieczeniach sieci ogłoszonej w listopadzie 2018 r. o nazwie FragmentSmack.

FragmentSmack jest wadą sposobu, w jaki jądro Linuksa obsługiwało ponowny zmontowane pakiety IPv4 i IPv6. Osoba atakująca zdalnie może użyć tej luki do wyzwalania kosztownych operacji ponownego montażu fragmentów, co może prowadzić do zwiększenia procesora CPU i odmowy usługi w systemie docelowym.

#### <a name="tune-the-mtu"></a>Dostrajanie jednostki MTU

Można skonfigurować jednostkę MTU maszyny Wirtualnej platformy Azure, tak jak w każdym innym systemie operacyjnym. Należy jednak wziąć pod uwagę fragmentacji, która występuje na platformie Azure, opisane powyżej, podczas konfigurowania jednostki MTU.

Nie zachęcamy klientów do zwiększania jednostek MTU maszyn wirtualnych. Ta dyskusja ma na celu wyjaśnienie szczegółów, jak platforma Azure implementuje jednostkę MTU i wykonuje fragmentację.

> [!IMPORTANT]
>Zwiększenie MTU nie jest znany w celu zwiększenia wydajności i może mieć negatywny wpływ na wydajność aplikacji.
>
>

#### <a name="large-send-offload"></a>Duże odciążanie wysyłania

Duże odciążanie wysyłania (LSO) może poprawić wydajność sieci, odciążając segmentację pakietów do karty Ethernet. Gdy LSO jest włączona, stos TCP/IP tworzy duży pakiet TCP i wysyła go do karty Ethernet w celu segmentacji przed przekazaniem go. Zaletą LSO jest to, że może zwolnić procesor z segmentacji pakietów na rozmiary, które są zgodne z MTU i odciążyć to przetwarzanie do interfejsu Ethernet, gdzie jest wykonywane w sprzęcie. Aby dowiedzieć się więcej o zaletach LSO, zobacz [Obsługa dużego odciążania wysyłania](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Gdy LSO jest włączona, klienci platformy Azure mogą zobaczyć duże rozmiary ramek podczas wykonywania przechwytywania pakietów. Te duże rozmiary ramek może prowadzić niektórych klientów do myślenia fragmentacji występuje lub że duże MTU jest używany, gdy nie jest. W przypadku LSO karta Ethernet może anonsować większy maksymalny rozmiar segmentu (MSS) do stosu TCP/IP, aby utworzyć większy pakiet TCP. Ta cała klatka nieegzesorowana jest następnie przekazywaną do karty Ethernet i będzie widoczna w przechwytywaniu pakietów wykonywanym na maszynie Wirtualnej. Ale pakiet zostanie podzielony na wiele mniejszych ramek przez adapter Ethernet, zgodnie z MTU karty Ethernet.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>Skalowanie okna protokołu TCP MSS i pmtud

#### <a name="tcp-maximum-segment-size"></a>Maksymalny rozmiar segmentu TCP

Maksymalny rozmiar segmentu TCP (MSS) to ustawienie, które ogranicza rozmiar segmentów TCP, co pozwala uniknąć fragmentacji pakietów TCP. Systemy operacyjne zazwyczaj używają tej formuły do ustawiania MSS:

`MSS = MTU - (IP header size + TCP header size)`

Nagłówek IP i nagłówek TCP mają po 20 bajtów lub łącznie 40 bajtów. Tak więc interfejs z MTU 1500 będzie miał MSS 1460. Ale MSS jest konfigurowalny.

To ustawienie jest uzgodnione w trójkierunkowym uzgadnianiu TCP, gdy sesja TCP jest konfiguracja między źródłem a miejscem docelowym. Obie strony wysyłają wartość MSS, a dolna z nich jest używana dla połączenia TCP.

Należy pamiętać, że jednostki MTU źródła i miejsca docelowego nie są jedynymi czynnikami, które określają wartość MSS. Pośredniczące urządzenia sieciowe, takie jak bramy sieci VPN, w tym brama sieci VPN platformy Azure, mogą dostosowywać jednostkę MTU niezależnie od źródła i miejsca docelowego, aby zapewnić optymalną wydajność sieci.

#### <a name="path-mtu-discovery"></a>Odnajdowanie jednostki MTU ścieżki

MSS jest negocjowany, ale może nie wskazywać rzeczywiste MSS, które mogą być używane. Dzieje się tak, ponieważ inne urządzenia sieciowe w ścieżce między źródłem a miejscem docelowym mogą mieć niższą wartość JEDNOSTKI MTU niż źródło i miejsce docelowe. W takim przypadku urządzenie, którego mtu jest mniejsza niż pakiet upuści pakiet. Urządzenie wyśle z powrotem wymagany komunikat fragmentacji ICMP (typ 3, kod 4), który zawiera jego jednostkę MTU. Ten komunikat ICMP umożliwia hostowi źródłowe odpowiednie zmniejszenie jednostki MTU ścieżki. Proces ten nosi nazwę odnajdowania jednostek MTU (PMTUD).

Proces PMTUD jest nieefektywny i wpływa na wydajność sieci. Gdy wysyłane są pakiety, które przekraczają jednostkę MTU ścieżki sieciowej, pakiety muszą być ponownie przesłane przy niższym MSS. Jeśli nadawca nie otrzyma komunikatu ICMP Fragmentation Needed, być może z powodu zapory sieciowej w ścieżce (powszechnie określanej jako *czarna dziura PMTUD),* nadawca nie wie, że musi obniżyć MSS i będzie stale retransmisjać pakiet. Dlatego nie zaleca się zwiększania jednostki MTU platformy Azure.

#### <a name="vpn-and-mtu"></a>VPN i mtu

Jeśli używasz maszyn wirtualnych, które wykonują hermetyzację (jak sieci VPN IPsec), istnieją pewne dodatkowe zagadnienia dotyczące rozmiaru pakietu i mtu. Sieci VPN dodają więcej nagłówków do pakietów, co zwiększa rozmiar pakietu i wymaga mniejszego MSS.

W przypadku platformy Azure zaleca się ustawienie mocowania protokołu TCP MSS na 1350 bajtów i jednostki MTU interfejsu tunelu na 1400. Aby uzyskać więcej informacji, zobacz [stronę urządzeń sieci VPN i parametrów IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Opóźnienie, czas podróży w obie strony i skalowanie okna TCP

#### <a name="latency-and-round-trip-time"></a>Opóźnienie i czas podróży w obie strony

Opóźnienie sieci zależy od szybkości światła w sieci światłowodowej. Przepustowość sieci protokołu TCP jest również skutecznie regulowana przez czas podróży w obie strony (RTT) między dwoma urządzeniami sieciowymi.

| | | | |
|-|-|-|-|
|**Trasa**|**Odległość**|**Czas w jedną stronę**|**RTT**|
|Nowy Jork do San Francisco|4 148 km|21 ms|42 ms.|
|Nowy Jork do Londyn|5 585 km|28 ms|56 ms.|
|Nowy Jork do Sydney|15 993 km|80 ms|160 ms|

W tej tabeli przedstawiono odległość linii prostej między dwiema lokalizacjami. W sieciach odległość jest zazwyczaj dłuższa niż odległość linii prostej. Oto prosta formuła do obliczania minimalnego RTT zgodnie z prędkością światła:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

Można użyć 200 dla szybkości propagacji. Jest to odległość, w metrach, że światło podróżuje w 1 milisekundy.

Weźmy nowy Jork do San Francisco jako przykład. Odległość linii prostej wynosi 4148 km. Podłączając tę wartość do równania, otrzymujemy następujące czynności:

`Minimum RTT = 2 * (4,148 / 200)`

Dane wyjściowe równania są w milisekundach.

Jeśli chcesz uzyskać najlepszą wydajność sieci, logiczną opcją jest wybranie miejsc docelowych z najkrótszą odległością między nimi. Należy również zaprojektować sieć wirtualną, aby zoptymalizować ścieżkę ruchu i zmniejszyć opóźnienia. Aby uzyskać więcej informacji, zobacz sekcję "Zagadnienia dotyczące projektowania sieci" tego artykułu.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Wpływ opóźnienia i czasu podróży w obie strony na TCP

Czas podróży w obie strony ma bezpośredni wpływ na maksymalną przepustowość TCP. W protokole TCP *rozmiar okna* to maksymalna ilość ruchu, który może być wysyłany za połączenie TCP, zanim nadawca musi otrzymać potwierdzenie od odbiorcy. Jeśli protokół TCP MSS jest ustawiony na 1460, a rozmiar okna TCP jest ustawiony na 65 535, nadawca może wysłać 45 pakietów, zanim będzie musiał otrzymać potwierdzenie od odbiorcy. Jeśli nadawca nie otrzyma potwierdzenia, ponownie przetransmisji dane. Oto formuła:

`TCP window size / TCP MSS = packets sent`

W tym przykładzie 65 535 / 1460 jest zaokrąglane do 45.

Ten stan "oczekiwania na potwierdzenie", mechanizm zapewniający niezawodne dostarczanie danych, powoduje, że RTT wpływa na przepustowość TCP. Im dłużej nadawca czeka na potwierdzenie, tym dłużej musi czekać przed wysłaniem większej ilości danych.

Oto formuła obliczania maksymalnej przepływności pojedynczego połączenia TCP:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

W tej tabeli przedstawiono maksymalną przepustowość megabajtów na sekundę pojedynczego połączenia TCP. (Dla czytelności megabajty są używane dla jednostki miary).

| | | | |
|-|-|-|-|
|**Rozmiar okna TCP (bajty)**|**Opóźnienie RTT (ms)**|**Maksymalna przepustowość megabajtów/sekundę**|**Maksymalna przepustowość megabitów/sekundy**|
|65,535|1|65.54|524.29|
|65,535|30|2.18|17.48|
|65,535|60|1.09|8.74|
|65,535|90|.73|5.83|
|65,535|120|.55|4.37|

Jeśli pakiety zostaną utracone, maksymalna przepustowość połączenia TCP zostanie zmniejszona, podczas gdy nadawca ponownie przesyła dane, które już wysłał.

#### <a name="tcp-window-scaling"></a>Skalowanie okna TCP

Skalowanie okna TCP to technika, która dynamicznie zwiększa rozmiar okna TCP, aby umożliwić wysłanie większej ilości danych przed wymaganiem potwierdzenia. W poprzednim przykładzie 45 pakietów zostanie wysłanych przed wymaganiem potwierdzenia. Jeśli zwiększysz liczbę pakietów, które mogą być wysyłane przed potwierdzeniem jest potrzebne, zmniejszasz liczbę razy, w których nadawca oczekuje na potwierdzenie, co zwiększa maksymalną przepustowość protokołu TCP.

W tej tabeli przedstawiono te relacje:

| | | | |
|-|-|-|-|
|**Rozmiar okna TCP (bajty)**|**Opóźnienie RTT (ms)**|**Maksymalna przepustowość megabajtów/sekundę**|**Maksymalna przepustowość megabitów/sekundy**|
|65,535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Ale wartość nagłówka TCP dla rozmiaru okna TCP jest tylko 2 bajty długości, co oznacza, że maksymalna wartość dla okna odbioru jest 65,535. Aby zwiększyć maksymalny rozmiar okna, wprowadzono współczynnik skali okna TCP.

Współczynnik skali jest również ustawieniem, które można skonfigurować w systemie operacyjnym. Oto formuła obliczania rozmiaru okna TCP przy użyciu współczynników skali:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Oto obliczenia dla współczynnika skali okna 3 i rozmiaru okna 65 535:

`65,535 \* (2^3) = 262,140 bytes`

Współczynnik skali 14 powoduje rozmiar okna TCP 14 (maksymalne dopuszczalne przesunięcie). Rozmiar okna TCP będzie 1,073,725,440 bajtów (8.5 gigabitów).

#### <a name="support-for-tcp-window-scaling"></a>Obsługa skalowania okna TCP

System Windows może ustawić różne współczynniki skalowania dla różnych typów połączeń. (Klasy połączeń obejmują centrum danych, Internet itd.) Polecenie `Get-NetTCPConnection` Programu PowerShell służy do wyświetlania typu połączenia skalowania okna:

```powershell
Get-NetTCPConnection
```

Za pomocą `Get-NetTCPSetting` polecenia programu PowerShell można wyświetlić wartości każdej klasy:

```powershell
Get-NetTCPSetting
```

Za pomocą polecenia `Set-NetTCPSetting` PowerShell można ustawić początkowy rozmiar okna TCP i współczynnik skalowania TCP w systemie Windows. Aby uzyskać więcej informacji, zobacz [Set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Są to skuteczne ustawienia `AutoTuningLevel`TCP dla:

| | | | |
|-|-|-|-|
|**Autotuninglevel**|**Współczynnik skalowania**|**Mnożnik skalowania**|**Formuła<br/>do obliczania maksymalnego rozmiaru okna**|
|Disabled (Wyłączony)|Brak|Brak|Rozmiar okna|
|Z ograniczeniami|4|2^4|Rozmiar okna * (2^4)|
|Bardzo ograniczona|2|2^2|Rozmiar okna * (2^2)|
|Normalne|8|2^8|Rozmiar okna * (2^8)|
|Eksperymentalne|14|2^14|Rozmiar okna * (2^14)|

Te ustawienia są najbardziej narażone na wydajność protokołu TCP, ale należy pamiętać, że wiele innych czynników w Internecie, poza kontrolą platformy Azure, może również mieć wpływ na wydajność protokołu TCP.

#### <a name="increase-mtu-size"></a>Zwiększ rozmiar jednostki MTU

Ponieważ większa jednostka MTU oznacza większy MSS, można się zastanawiać, czy zwiększenie jednostki MTU może zwiększyć wydajność TCP. Prawdopodobnie nie. Istnieją plusy i minusy rozmiaru pakietów poza tylko ruchem TCP. Jak wspomniano wcześniej, najważniejszymi czynnikami wpływającymi na wydajność przepływności TCP są rozmiar okna TCP, utrata pakietów i RTT.

> [!IMPORTANT]
> Nie zaleca się, aby klienci platformy Azure zmieniali domyślną wartość jednostki MTU na maszynach wirtualnych.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Przyspieszone tworzenie sieci i skalowanie po stronie odbierania

#### <a name="accelerated-networking"></a>Wydajniejsze sieci

Funkcje sieci maszyny wirtualnej były w przeszłości intensywnie obciążane procesorem zarówno na maszynie wirtualnej gościa, jak i na hipernadzorcy/hosta. Każdy pakiet przesyłany przez hosta jest przetwarzany w oprogramowaniu przez procesor hosta, w tym wszystkie hermetyzację i dekapsowanie sieci wirtualnej. Im większy ruch przechodzi przez hosta, tym większe obciążenie procesora. A jeśli procesor hosta jest zajęty innymi operacjami, wpłynie to również na przepustowość i opóźnienie sieci. Platforma Azure rozwiązuje ten problem z przyspieszoną siecią.

Przyspieszona sieć zapewnia spójne ultraniskie opóźnienie sieci za pośrednictwem wanego programowalnej platformy Azure i technologii, takich jak SR-IOV. Przyspieszona sieć przenosi większość stosu sieci definiowanego programowo platformy Azure z procesorów i do smartnic opartych na FPGA. Ta zmiana umożliwia aplikacjom użytkowników końcowych odzyskiwanie cykli obliczeniowych, co zmniejsza obciążenie maszyny Wirtualnej, zmniejszając jitter i niespójność w opóźnieniu. Innymi słowy, wydajność może być bardziej deterministyczne.

Przyspieszona sieć zwiększa wydajność, umożliwiając maszynie Wirtualnej gościa ominięcie hosta i ustanowienie ścieżki danych bezpośrednio za pomocą smartnic hosta. Oto kilka zalet przyspieszonej sieci:

- **Mniejsze opóźnienia / wyższe pakiety na sekundę (pps)**: Usunięcie przełącznika wirtualnego ze ścieżki danych eliminuje czas spędzony przez pakiety na hoście na przetwarzanie zasad i zwiększa liczbę pakietów, które mogą być przetwarzane na maszynie Wirtualnej.

- **Zmniejszone jitter:** Przetwarzanie przełącznika wirtualnego zależy od ilości zasad, które muszą być stosowane i obciążenia procesora CPU, który wykonuje przetwarzania. Odciążanie wymuszania zasad do sprzętu usuwa tę zmienność, dostarczając pakiety bezpośrednio do maszyny Wirtualnej, eliminując komunikację między hostami i wszystkie przerwania oprogramowania i przełączniki kontekstu.

- **Zmniejszone wykorzystanie procesora:** Pomijanie przełącznika wirtualnego na hoście prowadzi do mniejszego wykorzystania procesora CPU do przetwarzania ruchu sieciowego.

Aby korzystać z przyspieszonej sieci, należy jawnie włączyć go na każdej odpowiedniej maszynie wirtualnej. Aby uzyskać instrukcje, zobacz [Tworzenie maszyny wirtualnej systemu Linux z przyspieszoną siecią.](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

#### <a name="receive-side-scaling"></a>Odbieranie skalowania bocznego

Skalowanie po stronie odbierania (RSS) to technologia sterownika sieciowego, która bardziej efektywnie dystrybuuje odbieranie ruchu sieciowego, rozprowadzając przetwarzanie odbierania na wielu procesorach w systemie wieloprocesorowym. W prostych słowach RSS umożliwia systemowi przetwarzanie większej liczby odebranych ruchu, ponieważ używa wszystkich dostępnych procesorów zamiast tylko jednego. Aby uzyskać bardziej techniczną dyskusję na temat RSS, zobacz [Wprowadzenie do odbierania skalowania bocznego](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

Aby uzyskać najlepszą wydajność, gdy przyspieszona sieć jest włączona na maszynie wirtualnej, należy włączyć RSS. RSS może również zapewnić korzyści na maszynach wirtualnych, które nie korzystają z przyspieszonej sieci. Aby uzyskać omówienie sposobu określania, czy usługa RSS jest włączona i jak ją włączyć, zobacz [Optymalizowanie przepływności sieci dla maszyn wirtualnych platformy Azure.](https://aka.ms/FastVM)

### <a name="tcp-time_wait-and-time_wait-assassination"></a>Zabójstwo TIME_WAIT TCP i TIME_WAIT

TCP TIME_WAIT jest kolejnym typowym ustawieniem, które wpływa na wydajność sieci i aplikacji. Na zajętych maszyn wirtualnych, które otwierają i zamykają wiele gniazd, jako klienci lub jako serwery (Źródło IP:Source Port + Port docelowy IP:Port docelowy), podczas normalnego działania protokołu TCP, dane gniazdo może skończyć się w stanie TIME_WAIT przez długi czas. Stan TIME_WAIT ma na celu umożliwienie wszelkich dodatkowych danych, które mają być dostarczane na gnieździe przed zamknięciem go. Tak więc stosy TCP/IP zazwyczaj uniemożliwiają ponowne użycie gniazda przez dyskretne upuszczanie pakietu TCP SYN klienta.

Czas, przez który gniazdo znajduje się w TIME_WAIT jest konfigurowalny. Może wynosić od 30 sekund do 240 sekund. Gniazda są zasobem skończonego, a liczba gniazd, które mogą być używane w danym momencie, jest konfigurowana. (Liczba dostępnych gniazd wynosi zazwyczaj około 30 000). Jeśli dostępne gniazda są używane lub jeśli klienci i serwery mają niedopasowane ustawienia TIME_WAIT, a maszyna wirtualna próbuje ponownie użyć gniazda w stanie TIME_WAIT, nowe połączenia nie powiedzie się, ponieważ pakiety TCP SYN są po cichu odrzucane.

Wartość zakresu portów dla gniazd wychodzących jest zwykle konfigurowana w stosie TCP/IP systemu operacyjnego. To samo dotyczy ustawień TIME_WAIT TCP i ponownego użycia gniazda. Zmiana tych liczb może potencjalnie poprawić skalowalność. Jednak w zależności od sytuacji zmiany te mogą powodować problemy z interoperacyjnością. Należy zachować ostrożność, jeśli zmienisz te wartości.

Aby rozwiązać ten problem ograniczenia skalowania, można użyć TIME_WAIT zabójstwa. TIME_WAIT zabójstwie pozwala na ponowne użytie gniazda w pewnych sytuacjach, na przykład gdy numer sekwencyjny w pakiecie IP nowego połączenia przekracza numer sekwencyjny ostatniego pakietu z poprzedniego połączenia. W takim przypadku system operacyjny pozwoli na ustanowienie nowego połączenia (zaakceptuje nowy SYN/ACK) i wymusi zamknięcie poprzedniego połączenia, które było w stanie TIME_WAIT. Ta funkcja jest obsługiwana na maszynach wirtualnych z systemem Windows na platformie Azure. Aby dowiedzieć się więcej o obsłudze na innych maszynach wirtualnych, skontaktuj się z dostawcą systemu operacyjnego.

Aby dowiedzieć się więcej o konfigurowaniu ustawień TIME_WAIT TCP i zakresu portów źródłowych, zobacz [Ustawienia, które można zmodyfikować w celu zwiększenia wydajności sieci](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Czynniki sieci wirtualnej, które mogą mieć wpływ na wydajność

### <a name="vm-maximum-outbound-throughput"></a>Maksymalna przepływność ruchu wychodzącego maszyny Wirtualnej

Platforma Azure udostępnia różne rozmiary i typy maszyn wirtualnych, z których każdy ma inną kombinację możliwości wydajności. Jedną z tych możliwości jest przepustowość sieci (lub przepustowość), która jest mierzona w megabitach na sekundę (Mb/s). Ponieważ maszyny wirtualne są hostowane na współużytkowanym sprzęcie, pojemność sieci musi być sprawiedliwie współużytkowana między maszynami wirtualnymi przy użyciu tego samego sprzętu. Większe maszyny wirtualne są przydzielane większą przepustowość niż mniejsze maszyny wirtualne.

Przepustowość sieci przydzielona do każdej maszyny wirtualnej jest mierzona na ruchu wychodzącym (wychodzącym) z maszyny wirtualnej. Cały ruch sieciowy opuszczający maszynę wirtualną jest wliczany do przydzielonego limitu, niezależnie od miejsca docelowego. Na przykład jeśli maszyna wirtualna ma limit 1000 Mbps, ten limit ma zastosowanie, czy ruch wychodzący jest przeznaczony dla innej maszyny wirtualnej w tej samej sieci wirtualnej lub poza platformą Azure.

Ruch przychodzący nie jest mierzony ani ograniczony bezpośrednio. Istnieją jednak inne czynniki, takie jak limity procesora CPU i magazynu, które mogą mieć wpływ na zdolność maszyny wirtualnej do przetwarzania przychodzących danych.

Przyspieszona sieć została zaprojektowana w celu zwiększenia wydajności sieci, w tym opóźnienia, przepływności i wykorzystania procesora CPU. Przyspieszona sieć może poprawić przepływność maszyny wirtualnej, ale może to zrobić tylko do przydzielonej przepustowości maszyny wirtualnej.

Maszyny wirtualne platformy Azure mają co najmniej jeden interfejs sieciowy dołączony do nich. Mogą mieć ich kilka. Przepustowość przydzielona do maszyny wirtualnej jest sumą całego ruchu wychodzącego we wszystkich interfejsach sieciowych dołączonych do komputera. Innymi słowy przepustowość jest przydzielana na podstawie maszyny wirtualnej, niezależnie od tego, ile interfejsów sieciowych jest dołączonych do komputera.

Oczekiwana przepływność wychodząca i liczba interfejsów sieciowych obsługiwanych przez każdy rozmiar maszyny wirtualnej są szczegółowo opisane w [obszarze Rozmiary maszyn wirtualnych systemu Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Aby wyświetlić maksymalną przepustowość, wybierz typ, na **przykład ogólnego przeznaczenia,** a następnie znajdź sekcję dotyczącą serii rozmiarów na wynikowej stronie (na przykład "Seria Dv2"). Dla każdej serii istnieje tabela, która zawiera specyfikacje sieci w ostatniej kolumnie, która nosi tytuł "Maksymalna karta sieciowa / Oczekiwana przepustowość sieci (Mb/s)."

Limit przepływności ma zastosowanie do maszyny wirtualnej. Następujące czynniki nie mają wpływu na przepływność:

- **Liczba interfejsów sieciowych:** Limit przepustowości ma zastosowanie do sumy całego ruchu wychodzącego z maszyny wirtualnej.

- **Przyspieszona sieć:** Chociaż ta funkcja może być pomocna w osiągnięciu opublikowanego limitu, nie zmienia limitu.

- **Miejsce docelowe ruchu:** Wszystkie miejsca docelowe są wliczane do limitu ruchu wychodzącego.

- **Protokół:** Cały ruch wychodzący na wszystkich protokołach jest wliczany do limitu.

Aby uzyskać więcej informacji, zobacz [Przepustowość sieci maszyn wirtualnych](https://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Zagadnienia dotyczące wydajności internetu

Jak omówiono w tym artykule, czynniki w Internecie i poza kontrolą platformy Azure może mieć wpływ na wydajność sieci. Oto niektóre z tych czynników:

- **Opóźnienie:** Na czas podróży w obie strony między dwoma miejscami docelowymi mogą mieć wpływ problemy w sieciach pośrednich, ruch, który nie zajmuje "najkrótszą" ścieżkę odległości, oraz nieoptymalne ścieżki komunikacji równorzędnej.

- **Utrata pakietów:** Utrata pakietów może być spowodowana przeciążeniem sieci, problemami ze ścieżką fizyczną i słabszymi urządzeniami sieciowymi.

- **Rozmiar/fragmentacja jednostek MTU:** Fragmentacja wzdłuż ścieżki może prowadzić do opóźnień w nadejściu danych lub w pakietach wychodzących poza kolejnością, co może mieć wpływ na dostarczanie pakietów.

Traceroute to dobre narzędzie do pomiaru charakterystyki wydajności sieci (takich jak utrata pakietów i opóźnienie) wzdłuż każdej ścieżki sieciowej między urządzeniem źródłowym a urządzeniem docelowym.

### <a name="network-design-considerations"></a>Zagadnienia dotyczące projektowania sieci

Wraz z zagadnienia omówione wcześniej w tym artykule topologii sieci wirtualnej może mieć wpływ na wydajność sieci. Na przykład projekt koncentratora i szprychy, który łączy ruch na całym świecie do sieci wirtualnej z jednym koncentratorem, wprowadzi opóźnienie sieci, co wpłynie na ogólną wydajność sieci.

Liczba urządzeń sieciowych, przez które przechodzi ruch sieciowy, może również mieć wpływ na ogólne opóźnienie. Na przykład w projekcie koncentratora i szprychy, jeśli ruch przechodzi przez urządzenie wirtualne sieci szprychowej i urządzenia wirtualnego koncentratora przed przejściem do Internetu, urządzenia wirtualne sieci mogą wprowadzać opóźnienia.

### <a name="azure-regions-virtual-networks-and-latency"></a>Regiony platformy Azure, sieci wirtualne i opóźnienia

Regiony platformy Azure składa się z wielu centrów danych, które istnieją w obrębie ogólnego obszaru geograficznego. Te centra danych mogą nie być fizycznie obok siebie. W niektórych przypadkach są one oddzielone aż 10 kilometrów. Sieć wirtualna jest logiczną nakładką na sieć fizycznego centrum danych platformy Azure. Sieć wirtualna nie oznacza żadnej specyficznej topologii sieci w centrum danych.

Na przykład dwie maszyny wirtualne, które znajdują się w tej samej sieci wirtualnej i podsieci może znajdować się w różnych stojakach, wierszach, a nawet centrach danych. Mogą być oddzielone stopami kabla światłowodowego lub kilometrami kabla światłowodowego. Ta odmiana może wprowadzić zmienne opóźnienie (różnica kilku milisekund) między różnymi maszynami wirtualnymi.

Geograficzne rozmieszczenie maszyn wirtualnych i potencjalne opóźnienie między dwoma maszynami wirtualnymi mogą mieć wpływ na konfigurację zestawów dostępności i stref dostępności. Jednak odległość między centrami danych w regionie jest specyficzna dla regionu i zależy przede wszystkim od topologii centrum danych w regionie.

### <a name="source-nat-port-exhaustion"></a>Źródło wyczerpanie portu NAT

Wdrożenie na platformie Azure może komunikować się z punktami końcowymi poza platformą Azure w publicznym Internecie i/lub w przestrzeni publicznej adresów IP. Gdy wystąpienie inicjuje połączenie wychodzące, platforma Azure dynamicznie mapuje prywatny adres IP na publiczny adres IP. Po azure tworzy to mapowanie, zwraca ruch dla wychodzącego przepływu pochodzi również dotrzeć do prywatnego adresu IP, gdzie przepływ pochodzi.

Dla każdego połączenia wychodzącego moduł równoważenia obciążenia platformy Azure musi obsługiwać to mapowanie przez pewien okres czasu. Dzięki wielodostępnej naturze platformy Azure utrzymanie tego mapowania dla każdego przepływu wychodzącego dla każdej maszyny Wirtualnej może być intensywnie korzystające z zasobów. Dlatego istnieją limity, które są ustawione i oparte na konfiguracji sieci wirtualnej platformy Azure. Lub, aby powiedzieć, że dokładniej maszyny Wirtualnej platformy Azure można wykonać tylko pewną liczbę połączeń wychodzących w danym czasie. Po osiągnięciu tych limitów maszyna wirtualna nie będzie w stanie nawiązać większej liczby połączeń wychodzących.

Ale to zachowanie jest konfigurowalne. Aby uzyskać więcej informacji na temat wyczerpania portów SNAT i SNAT, zobacz [ten artykuł](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Mierzenie wydajności sieci na platformie Azure

Liczba wartości maksymalnych wydajności w tym artykule są związane z opóźnieniem sieci / czas podróży w obie strony (RTT) między dwoma maszynami wirtualnymi. W tej sekcji przedstawiono sugestie dotyczące sposobu testowania opóźnienia/RTT oraz testowania wydajności sieci TCP i sieci maszyn wirtualnych. Można dostroić i przetestować wydajność TCP/IP i wartości sieci omówione wcześniej przy użyciu technik opisanych w tej sekcji. Wartości rozmiaru okna można podłączyć do wcześniej podanych obliczeń i porównać teoretyczne wartości maksymalne z wartościami rzeczywistymi obserwowanymi podczas testowania.

### <a name="measure-round-trip-time-and-packet-loss"></a>Mierzenie czasu podróży w obie strony i utraty pakietów

Wydajność TCP zależy w dużej mierze od RTT i utraty pakietów. Narzędzie PING dostępne w systemach Windows i Linux zapewnia najprostszy sposób pomiaru RTT i utraty pakietów. Dane wyjściowe PING pokażą opóźnienie minimalne/maksymalne/średnie między źródłem a miejscem docelowym. Pokaże również utratę pakietów. PING domyślnie używa protokołu ICMP. Można użyć PsPing do testowania TCP RTT. Aby uzyskać więcej informacji, zobacz [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Mierzenie rzeczywistej przepustowości połączenia TCP

NTttcp jest narzędziem do testowania wydajności TCP maszyny wirtualnej z systemem Linux lub Windows. Można zmienić różne ustawienia TCP, a następnie przetestować korzyści za pomocą protokołu NTttcp. Aby uzyskać więcej informacji, zobacz następujące zasoby:

- [Testowanie przepustowości/przepływności (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [Narzędzie NTttcp](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Mierzenie rzeczywistej przepustowości maszyny wirtualnej

Można przetestować wydajność różnych typów maszyn wirtualnych, przyspieszonej sieci i tak dalej, za pomocą narzędzia o nazwie iPerf. iPerf jest również dostępny na Linuksie i Windows. iPerf może używać protokołu TCP lub UDP do testowania ogólnej przepływności sieci. Testy przepływności tcp iPerf są pod wpływem czynników omówionych w tym artykule (takich jak opóźnienie i RTT). Więc UDP może przynieść lepsze wyniki, jeśli chcesz tylko przetestować maksymalną przepływność.

Więcej informacji można znaleźć w tych artykułach:

- [Rozwiązywanie problemów z wydajnością sieci Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Jak zweryfikować przepływność sieci VPN do sieci wirtualnej](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Wykrywanie nieefektywnych zachowań TCP

W przechwytywaniach pakietów klienci platformy Azure mogą widzieć pakiety TCP z flagami TCP (SACK, DUP ACK, RETRANSMIT i FAST RETRANSMIT), które mogą wskazywać na problemy z wydajnością sieci. Pakiety te wskazują w szczególności nieefektywność sieci, które wynikają z utraty pakietów. Ale utrata pakietów nie musi być spowodowana problemami z wydajnością platformy Azure. Problemy z wydajnością mogą być wynikiem problemów z aplikacją, problemów z systemem operacyjnym lub innych problemów, które mogą nie być bezpośrednio związane z platformą Azure.

Należy również pamiętać, że niektóre retransmisja i duplikaty AK są normalne w sieci. Protokoły TCP zostały zbudowane tak, aby były niezawodne. Dowody tych pakietów TCP w przechwytywaniu pakietów niekoniecznie wskazują na problem z siecią systemową, chyba że są one nadmierne.

Mimo to te typy pakietów są wskazania, że przepływność TCP nie osiąga maksymalną wydajność, z powodów omówionych w innych sekcjach tego artykułu.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiesz się o dostrajaniu wydajności TCP/IP dla maszyn wirtualnych platformy Azure, możesz przeczytać o innych zagadnieniach [dotyczących planowania sieci wirtualnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) lub [dowiedzieć się więcej o łączeniu i konfigurowaniu sieci wirtualnych.](https://docs.microsoft.com/azure/virtual-network/)
