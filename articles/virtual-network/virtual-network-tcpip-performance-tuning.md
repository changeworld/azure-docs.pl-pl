---
title: Dostrajanie wydajności protokołu TCP/IP dla maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, różnych typowych TCP/IP wydajności dostrajania technik i ich związek z maszyn wirtualnych platformy Azure.
services: virtual-network
documentationcenter: na
author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
ms.openlocfilehash: 1e8605a41cbe610c971b891309b2149d221b8b27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032455"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>Wydajność protokołu TCP/IP automatycznego dostrajania dla maszyn wirtualnych platformy Azure

W tym artykule omówiono typowe metody dostrajania wydajności TCP/IP i niektóre kwestie, należy wziąć pod uwagę podczas korzystania z nich dla maszyn wirtualnych działających na platformie Azure. Wówczas zapewniają ogólne omówienie techniki i Poznaj, jak mogą być dostosowane.

## <a name="common-tcpip-tuning-techniques"></a>Typowe metody dostosowywania TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload"></a>Rozmiar jednostki MTU fragmentacji oraz Odciążanie wysyłania dużej ilości danych

#### <a name="mtu"></a>ROZMIAR JEDNOSTKI MTU

Maksymalna jednostka transmisji (MTU) jest największy ramki rozmiaru (pakiet) podany w bajtach, które mogą być wysyłane za pośrednictwem interfejsu sieciowego. Rozmiar jednostki MTU to ustawienie można skonfigurować. Domyślny rozmiar jednostki MTU używane na maszynach wirtualnych platformy Azure, a domyślne ustawienie na większości urządzeń sieciowych jest globalnie, 1500 bajtów.

#### <a name="fragmentation"></a>Fragmentation

Fragmentacja występuje, gdy pakiet jest wysyłany, która przekracza rozmiar jednostki MTU interfejsu sieciowego. Stos TCP/IP spowoduje uszkodzenie pakietu na mniejsze części (fragmentów), które są zgodne z rozmiar jednostki MTU interfejsu. Fragmentacja występuje w warstwie IP i jest niezależna od podstawowej protokołu (takich jak TCP). Gdy 2000-bajtowe są wysyłane za pośrednictwem karty sieciowej z rozmiar jednostki MTU 1500, pakiet zostanie podzielony na jeden pakiet 1500 bajtów i jeden pakiet 500-bajtowy.

Urządzenia sieciowe w ścieżce między źródłową i docelową można albo pakietów docelowej przekracza rozmiar jednostki MTU lub fragmenty pakietu na mniejsze części.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>Fragment nie bit w pakiecie protokołu IP

Bit nie fragmentu (DF) ma wartość flagi w nagłówku protokołu IP. DF bit wskazuje, że urządzenia sieciowe na ścieżce między nadawcą i odbiorcą nie musi fragmenty pakietu. Można ustawić tego bitu wielu powodów. (Zobacz sekcję "Path MTU Discovery" tego artykułu dla jednego przykładu). Gdy urządzenie sieciowe odbiera pakiet z bitem Fragment nie pakietu przekracza interfejsu urządzenia rozmiar jednostki MTU, standardowe zachowanie jest dla urządzenia, które można usunąć pakiet. Urządzenie wysyła komunikat ICMP fragmentacja potrzebny do oryginalnego źródła pakietu.

#### <a name="performance-implications-of-fragmentation"></a>Wpływ na wydajność fragmentacji

Fragmentacja może mieć wpływ na wydajność ujemna. Jest jednym z głównych powodów wpływ na wydajność Procesora/pamięci wpływ fragmentacji i ponowne składanie pakietów. Gdy urządzenie sieciowe musi fragmentu pakietu, będą musieli przydzielania zasobów Procesora i pamięci do wykonania fragmentacji.

Tak samo występuje, gdy pakiet jest ponownie. Urządzenie sieciowe musi przechowywać wszystkie fragmenty, dopóki nie otrzymali więc je można ponownie połączyć je do oryginalnego pakietu. Ten proces fragmentacji i ponownego łączenia także może spowodować opóźnienia.

Inne możliwe słabą wydajność domniemanie fragmentacji to, że pofragmentowane pakiety, może pojawić się poza kolejnością. Gdy pakiety są odebrane poza kolejnością, niektóre typy urządzeń sieciowych można upuścić je. Jeśli tak się stanie, cały pakiet musi zostać ponownie wysłane.

Fragmenty zwykle są odrzucane przez urządzenia zabezpieczeń, takiej jak zapory, sieci lub po otrzymaniu przez urządzenia sieciowego wyczerpania buforów. Po otrzymaniu przez urządzenia sieciowego wyczerpania buforów, urządzenie sieciowe próbuje ponownie połączyć podzielonej zawartości pakietu, ale nie ma zasobów do przechowywania i reassume pakiet.

Fragmentacja może być traktowany jako działania negatywne, ale pomocy technicznej, fragmentacji jest niezbędne podczas łączenia różnych sieci za pośrednictwem Internetu.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Korzyści z i skutków modyfikowanie rozmiar jednostki MTU

Ogólnie rzecz biorąc można utworzyć bardziej wydajne sieci, zwiększając rozmiar jednostki MTU. Każdy pakiet, który jest przekazywany ma informacje nagłówka, który jest dodawany do oryginalnego pakietu. Gdy fragmentacji tworzy więcej pakietów, ma więcej nagłówka obciążenie i sprawia to, że sieć mniej wydajne rozwiązanie.

Oto przykład. Rozmiar nagłówka Ethernet jest 14 bajtów plus sekwencję wyboru ramka 4-bajtowych w celu zapewnienia spójności ramki. Jeśli jeden 2000 bajtowy pakiet jest wysyłany, 18 bajtów obciążenie sieci Ethernet jest dodawany do sieci. Jeśli pakiet jest pofragmentowana. pakiet 1500 bajtów i pakietów 500-bajtowych, każdy pakiet ma 18 bajtów nagłówek Ethernet, daje w sumie 36 bajtów.

Należy pamiętać, który zwiększa rozmiar jednostki MTU niekoniecznie będzie oznaczało tworzenia wydajniejsze sieci. Jeśli aplikacja wysyła tylko pakiety 500-bajtowych, obciążenie tego samego nagłówka będzie istnieć, czy rozmiar jednostki MTU jest 1500 lub 9000 bajtów. Staną się bardziej efektywne tylko wtedy, gdy używa ona większe rozmiary pakietów, które ma wpływ rozmiar jednostki MTU sieci.

#### <a name="azure-and-vm-mtu"></a>Platforma Azure i rozmiar jednostki MTU maszyny Wirtualnej

Domyślny rozmiar jednostki MTU maszyn wirtualnych platformy Azure to 1500 bajtów. Stos sieci wirtualnej platformy Azure będzie podejmować próby fragmentu pakietów na 1400 bajtów. Lecz stos sieci wirtualnej będzie zezwalać na pakiety do 2,006 bajtów podczas bit Fragment nie jest ustawiony w nagłówku protokołu IP.

Należy pamiętać, że stos sieci wirtualnej jest natury nieefektywne, ponieważ fragmenty pakietów na 1400 bajtów, nawet jeśli maszyny wirtualne mają rozmiar jednostki MTU 1500. Znaczną część pakietów sieciowych są znacznie mniejsze niż 1400 lub 1500 bajtów.

#### <a name="azure-and-fragmentation"></a>Platforma Azure i fragmentacji

Stos sieci wirtualnej skonfigurowano pomijać "poza kolejnością fragmentów," oznacza to, że pofragmentowane pakiety, które nie pojawić się w ich oryginalnej kolejności pofragmentowany. Te pakiety są porzucane przede wszystkim z powodu ogłoszeniem z listopada 2018 o nazwie FragmentSmack luki w zabezpieczeniach sieci.

FragmentSmack jest usterka w sposób jądra systemu Linux obsługiwane ponowne składanie pofragmentowane pakiety protokołów IPv4 i IPv6. Osoba atakująca może użyć tej wady do wyzwalacza kosztowne fragmentu ponownego asemblowania operacje, które może prowadzić do zwiększenia procesora CPU i typu "odmowa usługi" w systemie docelowym.

#### <a name="tune-the-mtu"></a>Dostosuj rozmiar jednostki MTU

Można skonfigurować MTU dla maszyny Wirtualnej platformy Azure, jak w przypadku dowolnego systemu operacyjnego. Należy jednak rozważyć fragmentacji, występujący na platformie Azure opisano powyżej, gdy konfigurujesz rozmiar jednostki MTU.

Firma Microsoft nie Zachęć klientów do zwiększenia MTU maszyny Wirtualnej. Oznacza, że tej dyskusji, wyjaśniono, jak Azure implementuje rozmiar jednostki MTU i wykonuje fragmentacji.

> [!IMPORTANT]
>Zwiększa rozmiar jednostki MTU nie jest znana w celu zwiększenia wydajności i może mieć negatywny wpływ na wydajność aplikacji.
>
>

#### <a name="large-send-offload"></a>Odciążanie wysyłania dużej ilości danych

Odciążanie wysyłania dużej ilości danych (LSO) może poprawić wydajność sieci, przenosząc segmentacji pakietów do karty Ethernet. Po włączeniu LSO stos TCP/IP tworzy duży pakiet TCP i wysyła je do karty Ethernet na segmentacji przed przesłaniem. Zaletą LSO jest bezpłatnie Procesora z segmentacji pakietów do rozmiarów, które są zgodne ze rozmiar jednostki MTU i odciążania przetworzenia do interfejsu Ethernet, w której jest przeprowadzane w sprzęt. Aby dowiedzieć się więcej na temat zalet LSO, zobacz [Odciążanie wysyłania dużej obsługi](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Po włączeniu LSO Azure klienci mogą widzieć rozmiary duże ramki, gdy wykonują przechwytywania pakietów. Te rozmiary duże ramki może prowadzić niektórzy klienci myśl, że występuje fragmentacji lub że duży rozmiar jednostki MTU jest używana, gdy nie jest. Za pomocą LSO karta Ethernet można anonsować większy maksymalny rozmiar segmentu (MSS) na stos TCP/IP, aby utworzyć większy pakiet TCP. To całe ramki niepodzielony jest następnie przekazywany do karty Ethernet i będzie widoczny w przechwytywania pakietów, wykonywane na maszynie Wirtualnej. Ale pakiet zostanie podzielony na wiele mniejszych ramki przez kartę sieciową Ethernet, zgodnie z karty Ethernet rozmiar jednostki MTU.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>Skalowanie okna TCP MSS i pmtud, PATH

#### <a name="tcp-maximum-segment-size"></a>TCP maksymalny rozmiar segmentu

TCP maksymalny rozmiar segmentu (MSS) to ustawienie ogranicza rozmiar segmentów TCP, co pozwala uniknąć fragmentacji pakietów protokołu TCP. Systemy operacyjne zazwyczaj będzie używana następująca formuła można ustawić MSS:

`MSS = MTU - (IP header size + TCP header size)`

Nagłówek IP i nagłówek TCP to 20 bajtów lub łącznie 40 bajtów. Dlatego interfejsu o rozmiar jednostki MTU 1500 mają MSS 1,460. Ale MSS można konfigurować.

To ustawienie jest uzgodniona trójstopniowego TCP podczas konfigurowania sesji TCP między źródłem i miejscem docelowym. Obie strony wysyłanie wartości MSS, a dolna dwa służy do połączenia TCP.

Należy pamiętać, że MTU elementu źródłowego i docelowego nie są tylko czynniki, które określają wartości MSS. Urządzenia sieciowe pośrednie, takie jak bramy sieci VPN, w tym usługi Azure VPN Gateway, można dostosować rozmiar jednostki MTU niezależnie od źródłowego i docelowego zapewniające sieci optymalną wydajność.

#### <a name="path-mtu-discovery"></a>Path MTU Discovery

Jest negocjowane MSS, ale może wskazać MSS rzeczywistego, który może służyć. Jest to spowodowane innych urządzeń sieciowych w ścieżce między źródłową i docelową może mieć niższą wartość rozmiar jednostki MTU niż źródłowe i docelowe. W takim przypadku urządzenie, którego rozmiar jednostki MTU jest mniejszy niż pakietu będzie się zmniejszać pakiet. Urządzenie ponownie wyśle komunikat ICMP fragmentacja potrzebny (typ 3, 4 kodu) zawierający jego rozmiar jednostki MTU. Ten komunikat ICMP umożliwia hosta źródłowego zmniejszyć jego MTU ścieżki odpowiednio. Proces jest nazywany odnajdywania rozmiar jednostki MTU ścieżki (PMTUD).

Proces PMTUD jest nieefektywne i wpływa na wydajność sieci. Jeśli pakiety są wysyłane, że przekracza rozmiar jednostki MTU ścieżki sieciowej, pakiety muszą zostać ponownie wysłane z niższym MSS. Jeśli nadawca nie odbierze komunikatu protokołu ICMP fragmentacji potrzebne może być z powodu zapory sieciowej w ścieżce (często nazywany *należący PMTUD*), nadawca nie wie, należy zmniejszyć rozmiar MSS i będzie w sposób ciągły ponowne przesłanie pakietu. Jest to, dlatego nie zaleca się zwiększenie MTU maszyny Wirtualnej platformy Azure.

#### <a name="vpn-and-mtu"></a>Sieci VPN i rozmiar jednostki MTU

Jeśli używasz maszyn wirtualnych, które wykonują hermetyzacji (np. protokołu IPsec VPN), istnieją pewne dodatkowe uwagi dotyczące pakietu, rozmiar i rozmiar jednostki MTU. Sieci VPN dodać więcej nagłówków do pakietów, która zwiększa rozmiar pakietu i wymaga mniejszych MSS.

Dla platformy Azure firma Microsoft zaleca ustawione TCP MSS obcinanie równy 1350 bajtów, a następnie tunelowania interfejs rozmiar jednostki MTU 1400. Aby uzyskać więcej informacji, zobacz [sieci VPN urządzenia i strona parametrów protokołu IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Czas oczekiwania, czas błądzenia i skalowania okna TCP

#### <a name="latency-and-round-trip-time"></a>Czas opóźnienia i przesyłania danych

Opóźnienie sieci podlega prędkość światła za pośrednictwem sieci światłowodowe włókien. Przepustowość sieci dla protokołu TCP jest również skutecznie regulowane przez czas Rundy (RTT) między dwoma urządzeniami sieciowymi.

| | | | |
|-|-|-|-|
|**trasy**|**odległość**|**Czas jednokierunkowe**|**RTT**|
|Nowy Jork, aby San Francisco|4,148 km|21 ms|42 ms|
|Nowy Jork do Londynu|5,585 km|28 ms|56 ms|
|Nowy Jork, aby Sydney|15,993 km|80 ms|160 ms|

W poniższej tabeli przedstawiono liniową odległość między dwiema lokalizacjami. W sieci jest zwykle dłuższe niż liniową odległości. Oto prostej formuły można obliczyć RTT minimalne zgodnie z zasadami prędkość światła:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

Można użyć 200 dla szybkości propagacji. To jest odległość, w metrach i że światło przybliżone ilości tych danych w 1 milisekundy.

Jako przykład Weźmy Nowy Jork do sieci San Francisco. Liniowy jest 4,148 km. Podłączenie tę wartość do równania, uzyskujemy, następujące czynności:

`Minimum RTT = 2 * (4,148 / 200)`

Dane wyjściowe równania są w milisekundach.

Jeśli chcesz uzyskać najlepszą wydajność sieci, opcja logicznych jest wybierz miejsc docelowych o najmniejszej między nimi. Należy również projektować sieci wirtualnej do optymalizacji ścieżki ruchu i zmniejszyć opóźnienie. Aby uzyskać więcej informacji zobacz sekcję "Uwagi dotyczące projektowania sieci" w tym artykule.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Efekty czas opóźnienia i Rundy protokołu TCP

Czas błądzenia ma bezpośredni wpływ na maksymalną przepływność TCP. W protokole TCP *rozmiar okna* jest maksymalną ilość ruchu, które mogą być wysyłane za pośrednictwem połączenia protokołu TCP, zanim nadawca należy do otrzymania potwierdzenia od odbiornika. Jeśli TCP MSS jest ustawiona na 1,460 i rozmiar okna TCP jest ustawiony do 65 535 działań, nadawca wysłać 45 pakiety przed posiada do otrzymania potwierdzenia od odbiornika. Nadawca nie uzyskać potwierdzenie, będzie ponowne przesłanie danych. Oto formuła:

`TCP window size / TCP MSS = packets sent`

W tym przykładzie 65 535 / 1,460 jest zaokrąglana do 45.

Ten stan "Oczekiwanie na potwierdzenie", mechanizm, aby zapewnić niezawodne dostarczanie danych, jest to, co powoduje, że RTT wpływ na przepustowość TCP. Im dłużej nadawcy czeka na potwierdzenie, tym dłużej musi odczekać przez przesyłają więcej danych.

Oto formuła do obliczenia maksymalną przepływność pojedynczego połączenia TCP:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

W poniższej tabeli przedstawiono maksymalną (MB) / zaufanie na drugim przepływność pojedynczego połączenia protokołu TCP. (Aby zwiększyć czytelność, jednostki miary jest używany w megabajtach).

| | | | |
|-|-|-|-|
|**Rozmiar okna TCP (w bajtach)**|**RTT opóźnienie (ms)**|**Maksymalna MB/s przepływności**|**Maksymalna megabitów/s przepływności**|
|65,535|1|65.54|524.29|
|65,535|30|2.18|17.48|
|65,535|60|1.09|8.74|
|65,535|90|.73|5.83|
|65,535|120|.55|4.37|

W przypadku utraty pakietów maksymalną przepływność połączenia protokołu TCP zmniejszy się podczas nadawcę ponownie wysyła dane, które już zostało wysłane.

#### <a name="tcp-window-scaling"></a>Skalowanie okna TCP

Skalowania okna protokołu TCP jest techniką, która dynamicznie zwiększa rozmiar okna TCP, aby umożliwić większej ilości danych do wysłania, zanim wymagane jest potwierdzenie. W poprzednim przykładzie 45 pakiety będą wysyłane przed potwierdzeniem była wymagana. Jeśli zwiększysz liczbę pakietów, które mogą być wysyłane przed potwierdzenia jest potrzebny jest zmniejszenie liczby przypadków, gdy nadawcy czeka na potwierdzenie, co zwiększa maksymalną przepustowość TCP.

W tej tabeli przedstawiono te relacje:

| | | | |
|-|-|-|-|
|**Rozmiar okna TCP (w bajtach)**|**RTT opóźnienie (ms)**|**Maksymalna MB/s przepływności**|**Maksymalna megabitów/s przepływności**|
|65,535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Ale wartość nagłówka TCP rozmiar okna TCP jest tylko 2 bajtów, co oznacza, że maksymalna wartość dla okna odbierania, wynosi 65 535. Aby zwiększyć maksymalny rozmiar okna, wprowadzono współczynnik skali okna TCP.

Współczynnik skali jest również ustawienie, które można skonfigurować w systemie operacyjnym. Oto formuła do obliczenia rozmiaru okna przy użyciu skale:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Poniżej przedstawiono obliczenia współczynnik skali okna 3 i rozmiar okna 65 535:

`65,535 \* (2^3) = 262,140 bytes`

Współczynnik skali 14 powoduje powstanie rozmiar okna TCP 14 (maksymalne dozwolone przesunięcie). Rozmiar okna TCP będzie 1,073,725,440 bajtów (8,5 GB).

#### <a name="support-for-tcp-window-scaling"></a>Obsługa skalowania okna TCP

Windows można ustawić różne czynniki skalowania dla różnych typów połączeń. (Klasy połączeń obejmuje centrum danych, internetowe i tak dalej). Możesz użyć `Get-NetTCPConnection` polecenie programu PowerShell, aby wyświetlić okno skalowanie typ połączenia:

```powershell
Get-NetTCPConnection
```

Możesz użyć `Get-NetTCPSetting` polecenia programu PowerShell, aby wyświetlić wartości każdej klasy:

```powershell
Get-NetTCPSetting
```

W Windows można ustawić początkowy rozmiar okna TCP i TCP współczynnik skalowania przy użyciu `Set-NetTCPSetting` polecenia programu PowerShell. Aby uzyskać więcej informacji, zobacz [NetTCPSetting zestaw](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Są to efektywne ustawienia protokołu TCP dla `AutoTuningLevel`:

| | | | |
|-|-|-|-|
|**AutoTuningLevel**|**Współczynnik skalowania**|**Mnożnik skalowania**|**Formułę<br/>Oblicz maksymalny rozmiar okna**|
|Disabled (Wyłączony)|Brak|Brak|Rozmiar okna|
|Ograniczenia|4|2^4|Rozmiar okna * (2 ^ 4)|
|Bardzo ograniczona|2|2^2|Rozmiar okna * (2 ^ 2)|
|Normalna|8|2^8|Rozmiar okna * (2 ^ 8)|
|Eksperymentalne|14|2^14|Rozmiar okna * (2 ^ 14)|

Te ustawienia są najprawdopodobniej mają wpływ na wydajność protokołu TCP, ale należy pamiętać, że wiele czynników w Internecie, niezależnych od platformy Azure, może również wpływać na wydajność protokołu TCP.

#### <a name="increase-mtu-size"></a>Zwiększ rozmiar jednostki MTU

Ponieważ większy rozmiar jednostki MTU oznacza większe MSS, być może zastanawiasz się czy zwiększyć rozmiar jednostki MTU może zwiększyć wydajność protokołu TCP. Prawdopodobnie nie. Istnieją zalety i wady, aby rozmiar pakietu przekracza tylko ruch TCP. Zgodnie z wcześniejszym opisem najważniejszych czynników wpływających na wydajność przepustowości TCP są rozmiar okna TCP, utraty pakietów i RTT.

> [!IMPORTANT]
> Nie zaleca się że klientów platformy Azure, zmienić domyślną wartość rozmiar jednostki MTU na maszynach wirtualnych.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Przyspieszoną sieć i skalowanie po stronie odbierania

#### <a name="accelerated-networking"></a>Wydajniejsze sieci

Funkcje sieci maszyny wirtualnej w przeszłości były intensywnie z zarówno na maszynie Wirtualnej gościa i funkcji hypervisor/hosta Procesora. Każdy pakiet tranzytu za pośrednictwem hosta są przetwarzane w oprogramowaniu przez hosta procesora CPU, łącznie z wszystkich sieci wirtualnej hermetyzacji i dehermetyzacji. Dlatego więcej ruchu, który przechodzi przez hosta, załaduj większe zużycie Procesora. I Procesora hosta jest zajęty z innymi operacjami, który będzie również mieć wpływ na przepustowość sieci i czas oczekiwania. Azure rozwiązuje ten problem z przyspieszoną siecią.

Przyspieszona sieć zapewnia opóźnienia spójnej ultralow sieci za pomocą wewnętrznych sprzętu programowalny platformy Azure i technologii, takich jak SR-IOV. Przyspieszona sieć przesuwa większość Azure zdefiniowanego programowo stosu sieciowego off procesorów CPU oraz SmartNICs na układach FPGA. Ta zmiana umożliwia aplikacji użytkownika końcowego w celu odzyskania cykli obliczeniowych, która umieszcza mniejszym obciążeniem na maszynie Wirtualnej zmniejszenie opóźnienia zakłócenia i niespójności. Innymi słowy wydajność może być bardziej.

Przyspieszona sieć zwiększa wydajność, umożliwiając gościa maszyny Wirtualnej, aby pominąć hosta i określać ścieżki danych bezpośrednio z SmartNIC hosta. Poniżej przedstawiono niektóre zalety przyspieszonej łączności sieciowej:

- **Zmniejszyć czas oczekiwania wyższe pakietów na sekundę (pps)**: Usunięcie przełącznika wirtualnego z ścieżki danych eliminuje razem pakietów, który możesz wydać na hoście dla przetwarzania zasad i zwiększa się liczba pakietów, które mogą być przetwarzane w maszynie Wirtualnej.

- **Zmniejszona zakłócenia**: Przełącznik wirtualny przetwarzania zależy od tego, liczbę zasad, która musi zostać zastosowana i obciążenie procesora CPU, który wykonuje przetwarzanie. Odciążanie wymuszania zasad do sprzętu usuwa tego zmienność, zapewniając pakietów bezpośrednio do maszyny Wirtualnej, eliminując łączności hosta do maszyny Wirtualnej i wszystkie przerwań oprogramowania oraz przełączeń kontekstu.

- **Zmniejszyć użycie procesora CPU**: Przełącznik wirtualny na hoście z pominięciem prowadzi do mniejsze wykorzystanie procesora CPU do przetwarzania ruchu sieciowego.

Aby korzystać z przyspieszonej łączności sieciowej, musisz jawnie włączyć na każdym odpowiednią maszynę Wirtualną. Zobacz [Utwórz maszynę wirtualną systemu Linux przy użyciu Accelerated Networking](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) instrukcje.

#### <a name="receive-side-scaling"></a>Skalowanie po stronie odbierania

Odbieranie po stronie odbierającej (RSS) to technologia sterownika sieci bardziej efektywnie dystrybuuje odbieranie ruchu w sieci przez dystrybucję odbierać przetwarzania między wiele procesorów w systemie wieloprocesorowym. W prostych słowach RSS umożliwia systemowi przetwarzanie więcej odebrany ruch, ponieważ używa ona wszystkie dostępne procesory CPU zamiast jednego. Techniczne omówienie funkcji RSS, zobacz [wprowadzenie do skalowania po stronie odbierania](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

Aby uzyskać najlepszą wydajność, gdy przyspieszona sieć jest włączona na maszynie Wirtualnej, należy włączyć funkcję RSS. Funkcja RSS oferuje również, że korzyści na maszynach wirtualnych, które nie używają przyspieszoną sieć. Aby uzyskać omówienie sposobu ustalenia, czy jest włączona funkcja RSS i jak go włączyć, zobacz [optymalizacji przepływności sieci dla maszyn wirtualnych platformy Azure](http://aka.ms/FastVM).

### <a name="tcp-timewait-and-timewait-assassination"></a>Assassination TCP TIME_WAIT i TIME_WAIT

TCP TIME_WAIT jest inne typowe ustawienie, który wpływa na wydajność sieci i aplikacji. Na obciążonych maszyn wirtualnych, których otwieranie i zamykanie wiele gniazd jako klientów lub serwerów (Port IP:Source źródłowy + Port IP:Destination docelowy), podczas normalnego działania protokołu TCP danego gniazda mogą znaleźć się w stanu TIME_WAIT przez długi czas. Stan TIME_WAIT jest przeznaczony do Zezwalaj na wszelkie dodatkowe dane mają zostać dostarczone na gniazdo przed jego zamknięciem. Dlatego stosów protokołu TCP/IP ogólnie Zapobiegaj ponownemu gniazdo upuszczając dyskretnie pakietu TCP SYN klienta.

Ilość czasu, w której TIME_WAIT gniazdo jest konfigurowany. 240 sekund może obejmować zarówno 30 sekund. Gniazda są ograniczone zasobów i konfiguruje się liczbę gniazd, które mogą być używane w danym momencie. (Liczba dostępnych gniazd jest zwykle około 30 000). Jeśli dostępnych gniazd są używane, lub klienci i serwery mają niezgodne ustawienia TIME_WAIT i maszyny Wirtualnej spróbuje ponownie użyć gniazdo w stanie TIME_WAIT, nowe połączenia zakończy się niepowodzeniem, jak TCP SYN pakiety dyskretnie są porzucane.

Wartość dla zakresu portów dla ruchu wychodzącego gniazd jest zazwyczaj można skonfigurować w ramach stos TCP/IP systemu operacyjnego. To samo dotyczy ustawień TCP TIME_WAIT i ponowne używanie gniazd. Zmiana tych numerów potencjalnie może zwiększyć skalowalność. Jednak w zależności od sytuacji, tych zmian może spowodować problemy ze współdziałaniem. Należy zachować ostrożność, jeśli zmienisz tych wartości.

Aby rozwiązać ten limit skalowania, można użyć TIME_WAIT assassination. TIME_WAIT assassination umożliwia ponowne użycie w niektórych sytuacjach, takich jak numer sekwencyjny w pakiecie protokołu IP nowego połączenia przekroczy sekwencji liczbę ostatnich pakiet z poprzedniego połączenia gniazda. W takim przypadku system operacyjny umożliwi nowe połączenie nawiązane (będzie akceptować nowych SYN/potwierdzenia) i Wymuś zamknięcie poprzednie połączenie, który był w stanie TIME_WAIT. Ta funkcja jest obsługiwana na maszynach wirtualnych z Windows na platformie Azure. Aby dowiedzieć się więcej na temat pomocy technicznej na innych maszynach wirtualnych, skontaktuj się z dostawcą systemu operacyjnego.

Aby dowiedzieć się więcej na temat konfigurowania ustawień TCP TIME_WAIT i zakres portów źródłowych, zobacz [ustawienia, które mogą być modyfikowane w celu zwiększenia wydajności sieci](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Sieć wirtualna czynników, które mogą wpłynąć na wydajność

### <a name="vm-maximum-outbound-throughput"></a>Maksymalna przepustowość ruchu wychodzącego maszyn wirtualnych

System Azure oferuje szereg rozmiarów maszyn wirtualnych i typy, każdy z różne kombinacje wydajności. Jedną z tych funkcji jest sieci przepływności (lub przepustowości), który jest wyrażona w megabitach na sekundę (MB/s). Ponieważ maszyny wirtualne są hostowane na sprzęcie współużytkowanym, pojemność sieci musi być dość współużytkowane przez maszyny wirtualne, używając tego samego sprzętu. Większe maszyny wirtualne są przydzielane większej przepustowości niż mniejsze maszyny wirtualne.

Przepustowość sieci, przydzielone do każdej maszyny wirtualnej jest mierzone w (ruch wychodzący) ruch wychodzący z maszyny wirtualnej. Cały ruch sieciowy, pozostawiając maszyny wirtualnej jest liczony limitem przydzielone, niezależnie od docelowego. Na przykład jeśli maszyna wirtualna ma limit 1000 MB/s, ten limit dotyczy zarówno ruch wychodzący jest przeznaczony dla innej maszyny wirtualnej w tej samej sieci wirtualnej lub w jednym spoza platformy Azure.

Ruch przychodzący jest mierzone lub nie ograniczony bezpośrednio. Jednak istnieją inne czynniki, takie jak limity procesora CPU i pamięci masowej, które mogą wpłynąć na możliwość przetwarzania przychodzących danych maszyny wirtualnej.

Przyspieszona sieć jest przeznaczony do poprawiania wydajności sieci, w tym czas oczekiwania, przepływność i użycie procesora CPU. Przyspieszona sieć może zwiększyć przepływność maszynę wirtualną, ale może to zrobić tylko do maszyny wirtualnej przydzielonej przepustowości.

Usługa Azure virtual machines ma co najmniej jeden interfejs sieciowy dołączony do nich. Mogą mieć kilka. Przepustowość przydzielona do maszyny wirtualnej jest sumą cały ruch wychodzący we wszystkich interfejsów sieciowych dołączonych do maszyny. Innymi słowy przepustowość jest przydzielany na podstawie na wirtualnej maszynie, niezależnie od tego, jak wiele interfejsów sieciowych dołączonych do maszyny.

Oczekiwanej przepływności wychodzące i liczbę interfejsów sieciowych obsługiwanych przez każdego rozmiaru maszyny Wirtualnej są wyszczególnione w [rozmiary dla Windows maszyn wirtualnych na platformie Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Aby wyświetlić maksymalną przepustowość, wybierz typ, takie jak **ogólnego przeznaczenia**, a następnie znajdź sekcję dotyczącą serii rozmiar na wynikowej stronie (na przykład "serii Dv2"). Dla każdej serii jest tabela, która zapewnia sieci specyfikacji w ostatniej kolumnie, który jest zatytułowana "Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s)."

Limit przepływności stosuje się do maszyny wirtualnej. Przepływność nie ma wpływ następujące czynniki:

- **Liczba interfejsów sieciowych**: Limit przepustowości dotyczy sumę cały ruch wychodzący z maszyny wirtualnej.

- **Przyspieszona sieć**: Chociaż ta funkcja może być pomocny w osiągnięcia limitu opublikowane, go nie zmienia limit.

- **Miejsce docelowe ruchu**: Wszystkie miejsca docelowe są wliczane do limitu ruchu wychodzącego.

- **Protokół**: Cały ruch wychodzący za pośrednictwem wszystkich protokołów, liczy się do limitu.

Aby uzyskać więcej informacji, zobacz [przepustowość sieci maszyny wirtualnej](http://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Zagadnienia dotyczące wydajności w Internecie

Zgodnie z opisem w tym artykule, czynniki w Internecie i na zewnątrz kontrolki, platformy Azure może wpłynąć na wydajność sieci. Oto niektóre z tych czynników:

- **Opóźnienie**: Może mieć wpływ na czas błądzenia między dwoma miejscami docelowymi, problemami w sieciach pośredniego, ruch, który nie przyjmuje "najkrótsze" ścieżki odległości i nieoptymalne ścieżki komunikacji równorzędnej.

- **Utrata pakietów**: Utrata pakietów może być spowodowany przeciążenie sieci, ścieżkę fizyczną problemów i urządzeń sieciowych o niskiej efektywności.

- **Rozmiar jednostki MTU rozmiar/fragmentacji**: Fragmentacja wzdłuż ścieżki może prowadzić do opóźnienia w przybycia danych lub pakiety przychodzące poza kolejnością, co może wpłynąć na dostarczanie pakietów.

Polecenie traceroute jest dobrym narzędziem do pomiaru sieci charakterystyki (np. utraty pakietów i opóźnienia) co ścieżce sieciowej między urządzeniem źródła i urządzenie docelowe.

### <a name="network-design-considerations"></a>Zagadnienia dotyczące projektowania sieci

Z uwzględnieniem zagadnień omówionych wcześniej w tym artykule topologię sieci wirtualnej może mieć wpływ na wydajność sieci. Na przykład projekt Gwiazda backhauls ruch globalny do sieci wirtualnej w jednym centrum wprowadzi opóźnienia sieci, która wpłynie na ogólną wydajność sieci.

Liczba urządzeń sieciowych, które przekazuje ruch sieciowy może również wpływać na ogólny czas oczekiwania. Na przykład w projekcie Gwiazda — Jeśli ruch przechodzi przez wirtualne urządzenie sieciowe gwiazdy i urządzenia wirtualnego Centrum przed użyciem przejścia do Internetu, wirtualne urządzenia sieciowe może wprowadzić opóźnienie.

### <a name="azure-regions-virtual-networks-and-latency"></a>Regiony platformy Azure, sieci wirtualnych i czas oczekiwania

Regiony platformy Azure składają się z wielu centrów danych, który istnieje w ramach obszaru geograficznego. Te centra danych może nie być fizycznie obok siebie. W niektórych przypadkach są rozdzielone przez miarę 10 km. Sieć wirtualna jest logiczną nakładki na podstawie sieci fizycznych centrów danych platformy Azure. Sieć wirtualna nie pociąga za sobą topologii dowolnej określonej sieci w centrum danych.

Na przykład dwie maszyny wirtualne, które znajdują się w tej samej sieci wirtualnej i podsieci może być w różnych stojakach, wiersze lub nawet centrów danych. Może być rozdzielone przez stopy kabel światłowodowy lub kilometrów kabel światłowodowy. Ta zmiana może wprowadzić zmienne opóźnienie (kilku milisekund różnicę) między różnych maszyn wirtualnych.

Geograficzne rozmieszczenie maszyn wirtualnych i potencjalne wynikłe opóźnienie między dwiema maszynami wirtualnymi, mogą być zależne od konfiguracji zestawy dostępności i strefy dostępności. Ale odległość między centrami danych w regionie jest specyficzne dla regionu i przede wszystkim ich według topologii centrum danych w regionie.

### <a name="source-nat-port-exhaustion"></a>Wyczerpanie portów translatora adresów Sieciowych źródła

Wdrożenie na platformie Azure mogą komunikować się z punktami końcowymi spoza platformy Azure w publicznej sieci internet i/lub publiczną przestrzeń adresów IP. Gdy wystąpienie inicjuje połączenia wychodzącego, Azure dynamicznie mapuje prywatny adres IP na publiczny adres IP. Po utworzeniu Azure to mapowanie, ruch powrotny dla ruchu wychodzącego przepływu pochodzącej może także łączyć prywatny adres IP skąd pochodzi przepływ.

Dla każdego połączenia wychodzącego modułu równoważenia obciążenia platformy Azure musi stosować to mapowanie przez pewien czas. Wielodostępne naturze platformy Azure utrzymywanie to mapowanie dla każdego przepływu wychodzącego dla każdej maszyny Wirtualnej może stanowić dużej ilości zasobów. Dlatego istnieją ograniczenia, które są ustawiane i na podstawie konfiguracji sieci wirtualnej platformy Azure. Lub oznacza, że bardziej precyzyjnie, Maszynie wirtualnej platformy Azure można wprowadzać tylko liczbę połączeń wychodzących w danym momencie. Po osiągnięciu limitów maszyna wirtualna nie będzie możliwe zapewnienie bardziej wychodzące połączenia.

Ale to zachowanie można konfigurować. Aby uzyskać więcej informacji na temat SNAT i użyć funkcji SNAT względem portu wyczerpania, zobacz [w tym artykule](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Zmierzyć wydajność sieci na platformie Azure

Liczba maksymalne wartości wydajności, w tym artykule są związane z opóźnieniem sieci / obustronne czasu (RTT) między dwiema maszynami wirtualnymi. Ta sekcja zawiera sugestie dotyczące sposobu testowania opóźnienia/RTT oraz testowania wydajności protokołu TCP i wydajność sieci maszyny Wirtualnej. Można dostrajanie i wydajność testowania wartości TCP/IP i sieci, omówionych wcześniej, korzystając z metod opisanych w tej sekcji. Można podłączyć opóźnienia, rozmiar jednostki MTU, MSS i okno wartości rozmiarów do obliczenia podany wcześniej i porównać teoretycznych maksymalne wartości z rzeczywistymi wartościami, których stosuje się podczas testowania.

### <a name="measure-round-trip-time-and-packet-loss"></a>Czas błądzenia miary i utraty pakietów

Wydajność protokołu TCP intensywnie korzysta RTT i utraty pakietów. Najprostszym sposobem mierzyć utratę RTT i pakietów zapewnia narzędzie PING dostępne w Windows i Linux. Dane wyjściowe polecenia PING pokaże minimalna/maksymalna/średnia opóźnienia między źródłową i docelową. Można tam również sprawdzić utraty pakietów. Domyślnie polecenie PING używa protokołu ICMP. Narzędzie PsPing służy do testowania TCP RTT. Aby uzyskać więcej informacji, zobacz [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Rzeczywista przepływność miary połączenia protokołu TCP

NTttcp to narzędzie do testowania wydajności protokołu TCP, systemu Linux lub Windows maszyny Wirtualnej. Można zmienić różne ustawienia protokołu TCP i ponownie przetestujesz korzyści za pomocą NTttcp. Aby uzyskać więcej informacji zobacz następujące zasoby:

- [Przepustowość/testowania (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [NTttcp Utility](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Miary rzeczywistej przepustowość maszyny wirtualnej

Testowanie wydajności różnych typach maszyn wirtualnych, accelerated networking i tak dalej, korzystając z narzędzia o nazwie dotyczące programu Iperf;. iPerf jest również dostępna w systemie Linux i Windows. dotyczące programu Iperf; służy do testowania ogólną przepustowość sieci TCP lub UDP. testy przepływności TCP dotyczące programu Iperf; wpływało czynników omówionych w tym artykule (np. czas oczekiwania i RTT). Może to prowadzić to UDP w poszukiwaniu lepszych wyników, po prostu chcesz przetestować maksymalną przepustowość.

Więcej informacji można znaleźć w tych artykułach:

- [Rozwiązywanie problemów z wydajnością sieci usługi Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Jak zweryfikować przepływność sieci VPN do sieci wirtualnej](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Wykrywać nieefektywne zachowań protokołu TCP

Przechwytywanie pakietów klienci platformy Azure może zawierać pakiety TCP przy użyciu flag TCP (WOREK, potwierdzenia Zduplikowanej, RETRANSMISJI i szybkie ponowne przesłanie), które mogą wskazywać problemy z wydajnością sieci. Te pakiety wskazują specjalnie nieefektywności sieci, wynikających z utraty pakietów. Ale utraty pakietów niekoniecznie nie jest spowodowany przez problemy z wydajnością usługi Azure. Problemy z wydajnością może wynikać z problemy z aplikacjami, problemy z systemu operacyjnego lub innych problemów, które nie może być bezpośrednio związana z platformą Azure.

Ponadto należy pamiętać o tym, że niektóre retransmisji i zduplikowane potwierdzenia jest to normalne zachowanie w sieci. Protokoły TCP zostały zbudowane wiarygodne. Dowód tych pakietów protokołu TCP w przechwytywania pakietów nie musi oznaczać problem z siecią systemowych, chyba że są one nadmierne.

Jednak te typy pakietów są wskazania, że przepustowość TCP nie osiąga zwiększyć wydajność, przyczyn omówionych w innych częściach tego artykułu.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz już o dostrajanie wydajności protokołu TCP/IP dla maszyn wirtualnych platformy Azure, warto przeczytać o inne uwagi dotyczące [Planowanie sieci wirtualnej](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-vnet-plan-design-arm) lub [Dowiedz się więcej na temat łączenia i konfigurowanie sieci wirtualnych ](https://docs.microsoft.com/en-us/azure/virtual-network/).
