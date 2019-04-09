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
ms.openlocfilehash: 6d53b8fe46997f6b4f915bfb14a64d69b22cc5d4
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057833"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>Wydajność protokołu TCP/IP automatycznego dostrajania dla maszyn wirtualnych platformy Azure

Ten artykuł ma na celu omówienia typowych technik dostrajania wydajności TCP/IP i ich uwagi dotyczące maszyn wirtualnych działających w systemie Microsoft Azure. Warto najpierw mieć podstawową wiedzę na temat pojęć i następnie omówiono, jak mogą być dostosowane.

## <a name="common-tcpip-tuning-techniques"></a>Typowe metody dostosowywania TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload-lso"></a>Rozmiar jednostki MTU, fragmentacji i dużych Send Offload (LSO)

#### <a name="explanation-of-mtu"></a>Wyjaśnienie, rozmiar jednostki MTU

Maksymalna jednostka transmisji (MTU) jest największy ramki rozmiaru (pakiet) podany w bajtach, które mogą być wysyłane za pośrednictwem interfejsu sieciowego. Rozmiar jednostki MTU jest konfigurowalne ustawienia i domyślny rozmiar jednostki MTU używane na maszynach wirtualnych platformy Azure, a domyślne ustawienie na większości urządzeń sieciowych jest globalnie, 1500 bajtów.

#### <a name="explanation-of-fragmentation"></a>Wyjaśnienie fragmentacji

Fragmentacja występuje, gdy pakiet jest wysyłany, która przekracza rozmiar jednostki MTU interfejsu sieciowego. Stos TCP/IP spowoduje uszkodzenie pakietu na mniejsze części (fragmentów), które są zgodne z interfejsów, rozmiar jednostki MTU. Fragmentacja występuje w warstwie IP i jest niezależna od podstawowej protokołu (takich jak TCP). Gdy 2000-bajtowe są wysyłane za pośrednictwem karty sieciowej z rozmiar jednostki MTU 1500, następnie go będą można podzielić na jeden pakiet 1500 bajtów i jeden pakiet 500-bajtowy.

Urządzenia sieciowe w ścieżce między źródłowym i docelowym mają opcję, aby usunąć pakiety, które przekracza rozmiar jednostki MTU lub fragmenty pakietu na mniejsze części.

#### <a name="the-dont-fragment-df-bit-in-an-ip-packet"></a>Bit "nie fragmentu (DF)" w pakiecie protokołu IP

Bit Fragment nie ma flagi w nagłówku protokołu IP. W przypadku ustawiony DF bit, oznacza to, że urządzenia sieciowe pośrednie w ścieżce między nadawcą i odbiorcą nie musi fragmenty pakietu. Istnieje wiele powodów dlaczego ten bit może być ustawiona (zobacz poniższą sekcją ścieżki odnajdywania dla jednego przykładu). Gdy urządzenie sieciowe odbiera pakiet z bitem Fragment nie pakietu przekracza rozmiar jednostki MTU interfejsu urządzenia, a następnie standardowe zachowanie dotyczy urządzenia usunąć pakiet i wysłanie pakietu "ICMP fragmentacja potrzebny" powrót do oryginalnego źródła pakiet.

#### <a name="performance-implications-of-fragmentation"></a>Wpływ na wydajność fragmentacji

Fragmentacja może mieć wpływ na wydajność ujemna. Jest jednym z głównych powodów negatywny wpływ na wydajność Procesora/pamięci wpływ fragmentacji i ponowne składanie pakietów. Gdy urządzenie sieciowe musi fragmentu pakietu, będą musieli przydzielania zasobów Procesora i pamięci do wykonania fragmentacji. Taki sam musi się zdarzyć, gdy pakiet jest ponownie. Urządzenie sieciowe muszą być przechowywane wszystkie fragmenty, dopóki nie otrzymali więc je można ponownie połączyć je do oryginalnego pakietu. Ten proces fragmentacji/ponowne złożenie także może spowodować opóźnienia z powodu proces fragmentacji/ponownego składania.

Inne możliwe słabą wydajność domniemanie fragmentacji to, że pofragmentowane pakiety może pojawić się poza kolejnością. Poza kolejnością pakietów może spowodować niektóre typy urządzeń sieciowych, można usunąć pakietów poza kolejnością — które następnie będzie wymagać całego pakietu, aby zostać ponownie wysłane. Typowe scenariusze dotyczące upuszczanie fragmentów dołączania urządzeń zabezpieczających, takich jak zapory sieciowe lub po otrzymaniu przez urządzenia sieciowego wyczerpania buforów. Po otrzymaniu przez urządzenia sieciowego wyczerpania buforów, urządzenie sieciowe próbuje ponownie połączyć podzielonej zawartości pakietu, ale nie ma zasobów do przechowywania i reassume pakiet.

Fragmentacja mogą być uważane za działania negatywne, ale Obsługa fragmentacji jest niezbędne do łączenia różnych sieci za pośrednictwem Internetu.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Korzyści z i skutków modyfikowanie rozmiar jednostki MTU

Jako ogólnego zestawienia zwiększyć rozmiar jednostki MTU tworzyć bardziej wydajne sieci. Każdy pakiet, który jest przekazywany ma informacje dodatkowe nagłówka, który jest dodawany do oryginalnego pakietu. Więcej pakietów oznacza, że obciążenie więcej nagłówka, a sieć jest mniej wydajna w wyniku.

Na przykład rozmiar nagłówka Ethernet jest 14 bajtów plus 4-bajtowych ramki Sprawdź sekwencji FCS w celu zapewnienia spójności ramki. Jeśli jeden 2000-bajtowy pakiet jest wysyłany, 18 bajtów obciążenie sieci Ethernet jest dodawany do sieci. Jeśli pakiet jest pofragmentowana. pakiet 1500 bajtów i pakietów 500-bajtowych, każdy pakiet będzie mieć 18 nagłówka Ethernet — lub 36 bajtów. Pojedynczy pakiet 2000-bajtowych miałyby tylko nagłówek Ethernet, 18 bajtów.

Należy zauważyć, że zwiększyć rozmiar jednostki MTU w sobie nie zawsze utworzy wydajniejsze sieci. Jeśli aplikacja wysyła tylko pakiety 500-bajtowych, ten sam nagłówek obciążenie będzie istnieć, czy rozmiar jednostki MTU jest 1500 lub 9000 bajtów. Aby sieci bardziej wydajne, następnie go należy również użyć większe rozmiary pakietów, które są względne wobec rozmiar jednostki MTU.

#### <a name="azure-and-vm-mtu"></a>Platforma Azure i rozmiar jednostki MTU maszyny Wirtualnej

Domyślny rozmiar jednostki MTU maszyn wirtualnych platformy Azure to 1500 bajtów. Stos sieci wirtualnej platformy Azure będzie podejmować próby fragmentu pakietów na 1400 bajtów. Stos sieci wirtualnej platformy Azure będą jednak umożliwiały pakietów do 2006 bajtów. Jeśli ustawiono bit "Fragment nie" w nagłówku protokołu IP.

Należy zauważyć, że ten fragmentacji nie sugeruje się udzielenia stosu usługi Azure Virtual Network jest natury nieefektywne, ponieważ fragmenty pakietów na 1400 bajtów, gdy maszyny wirtualne mają rozmiar jednostki MTU 1500. Rzeczywistość jest znacznie mniejszy niż 1400 lub 1500 bajtów znaczną część pakietów sieciowych.

#### <a name="azure-and-fragmentation"></a>Platforma Azure i fragmentacji

Stos sieci wirtualnej platformy Azure już dziś skonfigurowano porzucić "Out of kolejności fragmenty" — oznacza pofragmentowane pakiety, które nie pojawić się w ich oryginalnej kolejności pofragmentowany. Te pakiety są porzucane przede wszystkim z powodu ogłoszeniem z listopada 2018 o nazwie FragmentStack luki w zabezpieczeniach sieci.

FragmentSmack jest usterka w sposób jądra systemu Linux obsługiwane ponowne składanie pofragmentowane pakiety protokołów IPv4 i IPv6. Osoba atakująca może użyć tej wady do wyzwalacza kosztowne fragmentu ponownego asemblowania operacji, które prowadzą do zwiększenia procesora CPU i typu "odmowa usługi" w systemie docelowym.

#### <a name="tune-the-mtu"></a>Dostosuj rozmiar jednostki MTU

Maszyny wirtualne platformy Azure obsługuje można skonfigurować rozmiar jednostki MTU, podobnie jak każdego innego systemu operacyjnego. Jednak fragmentacji, występuje w obrębie platformy Azure, która została szczegółowo opisana powyżej, należy rozważyć podczas konfigurowania rozmiar jednostki MTU.

Azure nie Zachęć klientów do zwiększenia ich maszyn wirtualnych, rozmiar jednostki MTU. Tej dyskusji, jest przeznaczona do wyjaśniono szczegółowo, jak Azure implementuje rozmiar jednostki MTU i wykonuje już dziś fragmentacja.

> [!IMPORTANT]
>Zwiększa rozmiar jednostki MTU nie pokazuje, aby zwiększyć wydajność i może mieć negatywny wpływ na wydajność aplikacji.
>
>

#### <a name="large-send-offload-lso"></a>Odciążanie wysyłania dużej ilości danych (LSO)

Duże Send Offload (LSO) może poprawić wydajność sieci, przenosząc segmentacji pakietów do karty Ethernet. Za pomocą LSO włączone stos TCP/IP utworzysz duży pakiet TCP i wyśle do adaptera Ethernet do segmentacji przed przesłaniem. Zaletą LSO jest bezpłatnie Procesora z segmentacji pakietów do rozmiaru pakietów, które są zgodne ze rozmiar jednostki MTU i odciążania przetworzenia do interfejsu Ethernet, w której jest przeprowadzane w sprzęt. Więcej informacji na temat korzyści LSO znajdują się w [wydajności w dokumentacji firmy Microsoft, karta sieciowa](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Po włączeniu LSO klientów platformy Azure może zostać wyświetlony rozmiary duże ramki, po przechwyceniu wykonywania pakietów. Te rozmiary duże ramki może prowadzić niektórzy klienci podejrzewać, że fragmentacji lub duże, rozmiar jednostki MTU jest używany, gdy nie jest. Za pomocą LSO karta ethernet można anonsować większych MSS na stos TCP/IP Aby można było utworzyć większy pakiet TCP. To całe ramki niepodzielony jest następnie przekazywany do karty Ethernet i będzie widoczny w przechwytywania pakietów, wykonywane na maszynie Wirtualnej. Jednakże pakiet zostanie podzielony na wiele mniejszych ramki przez kartę sieciową Ethernet, zgodnie z karty Ethernet rozmiar jednostki MTU.

### <a name="tcpmss-window-scaling-and-pmtud"></a>Skalowanie okna TCP/MSS i pmtud, PATH

#### <a name="explanation-of-tcp-mss"></a>Wyjaśnienie TCP MSS

Maksymalny rozmiar segmentu (MSS) TCP jest ustawienie przeznaczonego do ustawiania maksymalnego rozmiaru segmentu TCP, aby uniknąć fragmentacji pakietów protokołu TCP. Systemy operacyjne zwykle ustawi MSS jako MSS = rozmiar jednostki MTU — rozmiar adresu IP na & główek TCP (20 bajtów lub 40 łączna liczba bajtów). Dlatego interfejsu o rozmiar jednostki MTU 1500 mają MSS 1460. Jednak MSS, jest konfigurowany.

To ustawienie jest zawarte w trójstopniowego TCP, podczas konfigurowania sesji TCP między źródłową i docelową. Obie strony wysyłanie wartości MSS i niższym dwa służy do połączenia TCP.

Urządzenia sieciowe pośrednie, takie jak bramy sieci VPN, w tym usługi Azure VPN Gateway, mają możliwość Dopasuj rozmiar jednostki MTU niezależnie od źródłowego i docelowego zapewniające sieci optymalną wydajność. Dlatego należy zauważyć, że rozmiar jednostki MTU źródłowe i docelowe samodzielnie nie jest jedynym czynniki rzeczywistej wartości MSS.

#### <a name="explanation-of-path-mtu-discovery-pmtud"></a>Wyjaśnienie odkrywania MTU ścieżki (pmtud, Path)

Gdy negocjowane jest MSS, może nie wskazywać MSS rzeczywistego, który może służyć jako inne urządzenia sieciowe w ścieżce między źródłowym i docelowym może mieć niższą wartość rozmiar jednostki MTU niż źródłowe i docelowe. W takim przypadku urządzenie, którego rozmiar jednostki MTU jest mniejszy niż pakiet porzucić pakiet i ponownie wysłać wiadomość potrzebna fragmentacja kontroli komunikat ICMP (Internet Protocol) (typ 3, 4 kodu) zawierający jego rozmiar jednostki MTU. Ten komunikat ICMP umożliwia hosta źródłowego zmniejszyć jego MTU ścieżki odpowiednio. Ten proces jest nazywany odnajdywaniem MTU ścieżki.

Proces PMTUD jest z natury nieefektywne i ma wpływ na wydajność sieci. Jeśli pakiety są wysyłane, że przekracza ścieżek sieciowych rozmiar jednostki MTU, pakiety muszą retransmitowane z niższym MSS. Jeśli nadawca nie otrzyma pakiet ICMP fragmentacja potrzebny, prawdopodobnie z powodu zapory sieciowej na ścieżce (zazwyczaj nazywany należący pmtud, Path), nadawca wiedzą, że należy zmniejszyć rozmiar MSS i będzie w sposób ciągły, a następnie ponowne przesłanie pakietu. Z tego powodu nie zaleca się zwiększenie MTU maszyny Wirtualnej platformy Azure.

#### <a name="vpn-considerations-with-mtu"></a>Uwagi dotyczące sieci VPN za pomocą rozmiar jednostki MTU

Klienci, którzy używają maszyn wirtualnych, które wykonują hermetyzacji (takie jak IPSec sieci VPN) może mieć skutki dodatkowych pakietów, rozmiar i rozmiar jednostki MTU. Dodatkowe nagłówki, które zostaną dodane do oryginalnego pakietu, w związku z tym zwiększenie rozmiaru pakietów co wymaga mniejszych MSS dodać sieci VPN.

Bieżący zalecenie dla platformy Azure jest ustalenie, TCP MSS obcinanie 1350 bajtów i interfejsie tunelu rozmiar jednostki MTU 1400. Więcej informacji znajduje się w temacie [sieci VPN urządzenia i strona parametrów protokołu IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Czas oczekiwania, czas błądzenia i skalowania okna TCP

#### <a name="latency-and-round-trip-time"></a>Czas opóźnienia i przesyłania danych

Opóźnienie sieci podlega prędkość światła za pośrednictwem sieci światłowodowe włókien. Rzeczywistość jest przepustowość sieci dla protokołu TCP jest również efektywnie zarządzanych (praktyczne maksymalne wartości) ze względu na czas Rundy (RTT) między dwoma urządzeniami sieciowymi.

| | | | |
|-|-|-|-|
|Trasa|Odległość|Czas jednokierunkowe|Czas obustronnej konwersji (RTT)|
|Nowy Jork, aby San Francisco|4,148 km|21 ms|42 ms|
|Nowy Jork do Londynu|5,585 km|28 ms|56 ms|
|Nowy Jork, aby Sydney|15,993 km|80 ms|160 ms|

W poniższej tabeli przedstawiono liniową odległość między dwiema lokalizacjami, jednak w sieciach, jest zwykle dłuższe niż liniową odległości. Jest prostej formuły można obliczyć RTT minimalne zgodnie z zasadami prędkość światła: minimalne RTT = 2 * (odległości kilometrów / szybkość propagacji).

Standardowa wartość 200 może służyć do szybkości propagacji — wartość jest odległość w świetle liczniki przybliżone ilości tych danych w 1 milisekundy.

W przykładzie w Nowym Jorku celu San Francisco jest odległość liniową 4,148 km. Minimalna RTT = 2 * (4,148 / 200). Dane wyjściowe równanie będzie w milisekundach.

Podobnie jak fizyczna odległość między dwiema lokalizacjami stały mogą stać się rzeczywistością, jeśli wymagana jest maksymalną wydajność sieci, opcja najbardziej logicznym jest wybranie miejsca docelowe z najmniejszą odległość między nimi. W dalszej kolejności decyzje dotyczące projektu w ramach sieci wirtualnej można nawiązać zoptymalizować ścieżki ruchu i zmniejszyć opóźnienie. Te zagadnienia dotyczące sieci wirtualnej są opisane w poniższej sekcji zagadnienia dotyczące projektowania sieci.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Efekty czas opóźnienia i Rundy protokołu TCP

Obiegu (RTT) czasu ma bezpośredni wpływ na maksymalną przepływność TCP. Protokół TCP utworzono według koncepcji rozmiar okna. Wielkość okna jest na maksymalną ilość ruchu, które mogą być wysyłane za pośrednictwem połączenia protokołu TCP, zanim nadawca musi otrzymać bit potwierdzenia od odbiornika. Jeśli TCP MSS jest równa 1460 i rozmiar okna TCP jest ustawiony do 65535 następnie nadawcy wysłać 45 pakiety przed otrzymali potwierdzenia od odbiornika. Jeśli nie otrzymano potwierdzenia będą retransmitować nadawcy. W tym przykładzie rozmiar okna TCP / TCP MSS = pakiety wysyłane. Lub 65535 / 1460 jest zaokrąglana do 45.

Ten stan "Oczekiwanie na potwierdzenie" jako mechanizm do tworzenia niezawodnego dostarczania danych, to co skutecznie powoduje, że RTT wpływ na przepustowość TCP. Im dłużej nadawcy czeka na potwierdzenie, tym dłużej również musi czekać przed wysłaniem większej ilości danych.

Obliczanie maksymalną przepływność pojedynczego połączenia TCP jest następująca: Rozmiar okna / (RTT opóźnienie w milisekundach / 1000) = Maksimum bajtów na sekundę. W poniższej tabeli jest sformatowany w megabajtach, aby zwiększyć czytelność i przedstawia maksymalną megabajt / zaufanie na drugim przepływność pojedynczego połączenia protokołu TCP.

| | | | |
|-|-|-|-|
|Rozmiar okna TCP w bajtach|Opóźnienie RTT<br/>(w milisekundach)|Maksimum<br/>MB / drugi przepływności|Maksimum<br/> Megabita na drugim przepływności|
|65535|1|65.54|524.29|
|65535|30|2.18|17.48|
|65535|60|1.09|8.74|
|65535|90|.73|5.83|
|65535|120|.55|4.37|

W przypadku utraty pakietów, następnie go zmniejszy maksymalną przepływność połączenia protokołu TCP podczas nadawcę ponownie wysyła dane, które już zostało wysłane.

#### <a name="explanation-of-tcp-window-scaling"></a>Opis skalowanie okna TCP

Skalowanie okna TCP jest koncepcji, która dynamicznie zwiększa rozmiar okna TCP umożliwiając większej ilości danych do wysłania, zanim wymagane jest potwierdzenie. W naszym przykładzie poprzedniego 45 pakiety będą wysyłane przed potwierdzeniem była wymagana. Jeśli jest to liczba pakietów, które są wysyłane przed potwierdzenia jest zwiększana, następnie maksymalną przepływność TCP również zwiększa się dzięki zmniejszeniu liczby nadawcy czeka na potwierdzenie.

Przepustowość TCP jest przedstawiona w prostej tabeli poniżej:

| | | | |
|-|-|-|-|
|Rozmiar okna TCP<br/>w bajtach|RTT opóźnienie w milisekundach|Maksimum<br/>MB / drugi przepływności|Maksimum<br/> Megabita na drugim przepływności|
|65535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Wartość nagłówka protokołu TCP dla rozmiaru okna TCP jest jednak tylko 2 bajtów, co oznacza, że maksymalna wartość okna odbioru 65535. Aby zwiększyć maksymalny rozmiar okna, wprowadzono współczynnik skali okna TCP.

Współczynnik skali jest również ustawienie, które można skonfigurować w systemie operacyjnym. Obliczanie rozmiaru okna przy użyciu skale jest następująca: Rozmiar okna TCP = rozmiar okna TCP w bajtach \* (2 ^ współczynnik skalowania). W przypadku 3 i rozmiaru okna 65535 współczynnik skali okno obliczenia jest następujący: 65535 \* (2 ^ 3) = 262,140 bajtów. Współczynnik skali 14 skutkuje rozmiaru okna 14 (maksymalne dozwolone przesunięcie), a następnie rozmiar okna TCP będzie 1,073,725,440 bajtów (8,5 GB).

#### <a name="support-for-tcp-window-scaling"></a>Obsługa skalowania okna TCP

Windows ma możliwość określenia różnych czynników skalowania na na podstawie typu połączenia — istnieje kilka rodzajów połączenia (centrum danych, internetowe i tak dalej). Możesz zobaczyć okno klasyfikacji połączenia skalowania za pomocą polecenia powershell Get-NetTCPConnection.

```powershell
Get-NetTCPConnection
```

Widać wartości poszczególnych klasy za pomocą polecenia powershell Get-NetTCPSetting.

```powershell
Get-NetTCPSetting
```

Początkowy rozmiar okna TCP i współczynnik skalowania protokołu TCP można ustawić w Windows, za pomocą polecenia programu powershell Set-NetTCPSetting. Więcej informacji znajduje się w temacie [NetTCPSetting zestaw stron](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps)

```powershell
Set-NetTCPSetting
```

Efektywne ustawienia TCP AutoTuningLevel są następujące.

| | | | |
|-|-|-|-|
|AutoTuningLevel|Współczynnik skalowania|Mnożnik skalowania|Formuła<br/>Oblicz maksymalny rozmiar okna|
|Disabled (Wyłączony)|Brak|Brak|Rozmiar okna|
|Ograniczenia|4|2^4|Rozmiar okna * (2 ^ 4)|
|Bardzo ograniczona|2|2^2|Rozmiar okna * (2 ^ 2)|
|Normalna|8|2^8|Rozmiar okna * (2 ^ 8)|
|Eksperymentalne|14|2^14|Rozmiar okna * (2 ^ 14)|

Te ustawienia są najprawdopodobniej mają wpływ na wydajność protokołu TCP, należy zauważyć, że wiele czynników w Internecie, niezależnych od platformy Azure, może również wpływać na wydajność protokołu TCP.

#### <a name="increase-mtu-size"></a>Zwiększ rozmiar jednostki MTU

Logiczne pytaniem jest, "może zwiększyć rozmiar jednostki MTU zwiększyć wydajność protokołu TCP jako większy rozmiar jednostki MTU oznacza większe MSS"? Proste odpowiedzi — prawdopodobnie nie jest. Jak wspomniano, istnieje zalet i wad do rozmiaru pakietów, które są stosowane poza tylko ruch TCP. Jak wspomniano powyżej, najważniejszych czynników wpływających na wydajność przepustowości protokołu TCP jest rozmiar okna TCP, utraty pakietów i RTT.

> [!IMPORTANT]
> Azure nie zaleca się, że klienci platformy Azure modyfikować domyślny rozmiar jednostki MTU wartość na maszynach wirtualnych.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Przyspieszoną sieć i skalowanie po stronie odbierania

#### <a name="accelerated-networking"></a>Accelerated Networking

Funkcje sieci maszyny wirtualnej w przeszłości zostały procesor CPU o znacznym wykorzystaniu na funkcji Hypervisor/hosta i gościa maszyny Wirtualnej. Każdy pakiet tranzytu za pośrednictwem hosta są przetwarzane w oprogramowaniu przez procesor CPU — łącznie z wszystkich sieci wirtualnej hermetyzacji/de-capsulation hosta. Dlatego więcej ruchu, przechodzi przez hosta, a następnie wyższe obciążenie procesora CPU. Ponadto Procesora hosta jest zajęty, inne operacje, następnie, będzie również mieć wpływ na przepustowość sieci i opóźnień. Ten problem został rozwiązany za pośrednictwem przyspieszonej sieci.

Przyspieszona sieć zapewnia opóźnienia spójnej bardzo niskimi sieci za pomocą wewnętrznych programowalny sprzętowych platformy Azure i technologii, takich jak SR-IOV. Przenosząc znaczną część stos sieci zdefiniowanych przez oprogramowanie systemu Azure wyłączone procesorów CPU i do opartej na układach FPGA — SmartNICs, obliczeniowe cykle są odzyskiwane przez aplikacje dla użytkowników końcowych, umieszczając mniejszym obciążeniem na maszynie Wirtualnej, zmniejsza opóźnienia zakłócenia i niespójności. Innymi słowy wydajność może być bardziej.

Przyspieszona sieć osiąga ulepszenia wydajności, umożliwiając obejścia hosta i określać ścieżki danych bezpośrednio z SmartNIC hosta maszyny Wirtualnej gościa. Korzyści z przyspieszonej sieci są:

- **Zmniejszyć czas oczekiwania wyższe pakietów na sekundę (pps)**: Usunięcie przełącznika wirtualnego z ścieżki danych spowoduje usunięcie pakietów poświęcić czas na hoście dla przetwarzania zasad i zwiększa się liczba pakietów, które mogą być przetwarzane w ramach maszyny Wirtualnej.

- **Zmniejszona zakłócenia**: Przełącznik wirtualny przetwarzania zależy od tego, liczbę zasad, która musi zostać zastosowana i obciążenie procesora CPU, który wykonuje przetwarzanie. Odciążanie wymuszania zasad na sprzęcie usuwa tego zmienność, zapewniając pakietów bezpośrednio do maszyny Wirtualnej, usunięcie hosta do komunikacji z maszyną Wirtualną i wszystkie przerwań oprogramowania i przełączeń kontekstu.

- **Zmniejszyć użycie procesora CPU**: Przełącznik wirtualny na hoście z pominięciem prowadzi do mniejsze wykorzystanie procesora CPU do przetwarzania ruchu sieciowego.

Przyspieszona sieć musi być jawnie włączone na podstawie poszczególnych maszyn wirtualnych. Instrukcje dotyczące włączania przyspieszonej sieci na maszynie Wirtualnej są dostępne pod adresem [Utwórz maszynę wirtualną systemu Linux ze stroną Accelerated Networking](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).

#### <a name="receive-side-scaling-rss"></a>Skalowanie po stronie odbierającej (RSS)

Skalowanie po stronie odbierania jest technologia sterownika sieci bardziej efektywnie dystrybuuje odbieranie ruchu w sieci przez dystrybucję odbierać przetwarzania między wiele procesorów w systemie wieloprocesorowym. W prostych słowach RSS umożliwia systemowi przetwarzanie większej ilości ruchu odebrane, ponieważ używa ona wszystkie dostępne procesory CPU zamiast jednego. Techniczne omówienie funkcji RSS, można znaleźć w folderze [wprowadzenie do strony po stronie odbierającej](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

Funkcja RSS jest wymagany do osiągnięcia maksymalnej wydajności, gdy przyspieszonej sieci jest włączona na maszynie Wirtualnej. Może istnieć również korzyści przy użyciu danych RSS na maszynach wirtualnych, które nie mają włączoną obsługą przyspieszonej sieci. Omówienie określają, czy zostaje włączone skalowanie RSS i konfiguracja jej włączeniu znajduje się w temacie [Optymalizacja przepływności sieci dla maszyn wirtualnych platformy Azure, strona](http://aka.ms/FastVM).

### <a name="tcp-time-wait-and-time-wait-assassination"></a>TCP czas oczekiwania i czas oczekiwania Assassination

Inny typowy problem, który wpływa na wydajność sieci i aplikacji jest ustawieniem TCP czas oczekiwania. Na obciążonych maszyn wirtualnych, które są otwierający i zamykający wiele gniazd, jako klienta lub serwera (Port IP:Source źródłowy + Port IP:Destination docelowy), podczas normalnego działania protokołu TCP danego gniazda można znajdą się w stanie oczekiwania, czas na znaczną ilość czasu. Oznacza, że ten stan "czas oczekiwania" Zezwalaj na wszelkie dodatkowe dane mają zostać dostarczone w gnieździe przed jego zamknięciem. W związku z tym stosów protokołu TCP/IP powszechnie Zapobiegaj ponownemu gniazda upuszczając dyskretnie pakietu TCP SYN klientów.

Ta ilość czasu, przez gniazdo jest w stan oczekiwania, można skonfigurować, ale może należeć do zakresu od 30 sekund do 240 sekund. Gniazda są ograniczone zasobów i liczbę gniazd, które mogą być używane w dowolnym momencie można skonfigurować (liczba ogólnie jest około 30 000 potencjalnych sockets). Jeżeli wyczerpania tej liczby, czy klienci i serwery miało niedopasowanych czas oczekiwania i maszyny Wirtualnej spróbuje ponownie użyć gniazdo w stanie oczekiwania czasu nowe połączenia będą mogły jak TCP SYN pakiety dyskretnie są porzucane.

Zazwyczaj wartość dla zakresu portów dla ruchu wychodzącego gniazda, a także ustawienia TCP czas oczekiwania i ponowne użycie gniazda są konfigurowane w stos TCP/IP systemu operacyjnego. Zmiana tych numerów potencjalnie może poprawić skalowalność, ale w zależności od scenariusza, może spowodować problemy ze współdziałaniem i należy je zmienić ostrożnie.

Funkcję o nazwie czas oczekiwania Assassination wprowadzono, aby rozwiązać ten skalowanie ograniczenie. Czas oczekiwania Assassination umożliwia ponowne użycie w niektórych scenariuszach, takich jak numer sekwencyjny w pakiecie protokołu IP nowego połączenia przekroczy sekwencji liczbę ostatnich pakiet z poprzedniego połączenia gniazda. W takim przypadku system operacyjny umożliwi nowe połączenie nawiązane (Zaakceptuj nowy potwierdzenia SYN) i Wymuś zamknięcie poprzednie połączenie, który został w czasie stan oczekiwania. Ta funkcja jest obecnie obsługiwane na maszynach wirtualnych Windows, w obrębie platformy Azure i pomocy technicznej w ramach innych maszyn wirtualnych należy zbadać przez klientów platformy Azure przy użyciu odpowiednich dostawców systemu operacyjnego.

Dokumentacja na temat konfigurowania ustawień protokołu TCP czas oczekiwania i zakres portów źródłowych znajduje się w temacie [ustawień, które mogą być modyfikowane do poprawiania wydajności sieci strony](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Wirtualne czynników sieci, które mogą wpływać na wydajność

### <a name="vm-maximum-outbound-throughput"></a>Maksymalna przepustowość ruchu wychodzącego maszyn wirtualnych

Platforma Azure oferuje szereg rozmiarów maszyn wirtualnych i typy, każdy z różne kombinacje wydajności. Jeden taki poziom wydajności jest sieci przepływności (lub przepustowości), wyrażona w megabitach na sekundę (MB/s). Ponieważ maszyny wirtualne są hostowane na sprzęcie współużytkowanym, pojemność sieci musi być udostępniona dość między maszynami wirtualnymi, udostępnianie tego samego sprzętu. Większe maszyny wirtualne są przydzielane stosunkowo większej przepustowości niż mniejsze maszyny wirtualne.

Przepustowość sieci, przydzielone do każdej maszyny wirtualnej jest mierzone w (ruch wychodzący) ruch wychodzący z maszyny wirtualnej. Cały ruch sieciowy, pozostawiając maszyny wirtualnej jest liczony limitem przydzielone, niezależnie od docelowego. Na przykład jeśli maszyna wirtualna ma limit 1000 MB/s, ten limit dotyczy zarówno ruch wychodzący jest przeznaczony dla innej maszyny wirtualnej w tej samej sieci wirtualnej lub poza platformą Azure.
Ruch przychodzący jest mierzone lub nie ograniczony bezpośrednio. Istnieją jednak inne czynniki, takie jak limity procesora CPU i pamięci masowej, które mogą wpłynąć na możliwość przetwarzania przychodzących danych maszyny wirtualnej.

Przyspieszona sieć jest funkcją pozwalające zwiększyć wydajność sieci, w tym czas oczekiwania, przepływność i użycie procesora CPU. Gdy przyspieszonej sieci można zwiększyć przepływność maszynę wirtualną, go to zrobić tylko przepustowość przydzielona do maszyny wirtualnej w górę.

Maszyny wirtualne platformy Azure musi mieć jedną, ale może mieć kilka, dołączonych do nich interfejsy sieciowe. Przepustowość przydzielona do maszyny wirtualnej jest sumą cały ruch wychodzący we wszystkich interfejsów sieciowych dołączonych do maszyny wirtualnej. Innymi słowy przydzielonej przepustowości odbywa się dla maszyny wirtualnej, niezależnie od tego, jak wiele interfejsów sieciowych dołączonych do maszyny wirtualnej.
 
Oczekiwanej przepływności wychodzące i liczbę interfejsów sieciowych obsługiwanych przez każdego rozmiaru maszyny Wirtualnej została szczegółowo opisana w tym miejscu. Aby wyświetlić maksymalną przepustowość, wybierz typu, na przykład ogólnego przeznaczenia, a następnie wybierz rozmiar serii na stronie wynikowe, np. zalecamy używanie serii Dv2. Każda seria ma jedną tabelę z sieci specyfikacji w ostatniej kolumnie pod tytułem, maksymalna liczba kart sieciowych / oczekiwana wydajność sieci (MB/s).

Limit przepływności stosuje się do maszyny wirtualnej. Przepływność jest zależny od następujących czynników:

- **Liczba interfejsów sieciowych**: Limit przepustowości jest zbiorcze z całego ruchu wychodzącego z maszyny wirtualnej.

- **Przyspieszona sieć**: Chociaż ta funkcja może być pomocny w osiągnięcia limitu opublikowane, nie powoduje zmiany limitu.

- **Miejsce docelowe ruchu**: Wszystkie miejsca docelowe są wliczane do limitu ruchu wychodzącego.

- **Protokół**: Cały ruch wychodzący za pośrednictwem wszystkich protokołów, liczy się do limitu.

A [tabeli maksymalną przepustowość dla typu maszyny Wirtualnej można znaleźć, przechodząc na tej stronie](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) i klikając odpowiedni typ maszyny Wirtualnej. Na każdej stronie Typ tabeli pokaże maksymalna kart sieciowych i maksymalnej przepustowości oczekiwane.

Więcej informacji na temat przepustowość sieci maszyny Wirtualnej znajduje się w temacie [przepustowość sieci maszyny wirtualnej](http://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Zagadnienia dotyczące wydajności w Internecie

Zgodnie z opisem w tym artykule, czynniki w Internecie i na zewnątrz kontrolki, platformy Azure może wpłynąć na wydajność sieci. Czynniki te są następujące:

- **Opóźnienie**: Czas błądzenia między dwoma miejscami docelowymi mogą mieć wpływ problemów w sieciach pośredniego, ruch nie podejmuje "najmniejszej" Ścieżka możliwe i nieoptymalne ścieżki komunikacji równorzędnej

- **Utrata pakietów**: Utrata pakietów, może być spowodowany przez przeciążenie sieci, ścieżkę fizyczną problemów i urządzenia sieciowe w ramach wykonywania

- **Rozmiar jednostki MTU rozmiar/fragmentacji**: Fragmentacja wzdłuż ścieżki mogą prowadzić do opóźnienia przybycia danych lub pakiety przychodzące poza kolejnością, które mogą wpływać na dostarczanie pakietów

Polecenie traceroute jest dobrym narzędziem do mierzenia charakterystyki wydajności sieci (na przykład utraty pakietów i opóźnienia) w każdej ścieżce sieciowej między urządzeniem źródłowym i docelowym.

### <a name="network-design-considerations"></a>Zagadnienia dotyczące projektowania sieci

Z uwzględnieniem zagadnień powyżej topologię sieci wirtualnej może mieć wpływ na wydajność sieci wirtualnej. Na przykład gwiazdy projektu, ruchu backhauls globalnie do sieci wirtualnej w jednym Centrum będzie wprowadzenie opóźnienia sieci i dlatego wpływ na ogólną wydajność sieci. Podobnie liczby urządzeń sieciowych, które przekazuje ruch sieciowy może również wpływać na ogólny czas oczekiwania. Na przykład w projekcie gwiazdy, jeśli ruch przechodzi przez szprychy wirtualne urządzenie sieciowe oraz Centrum urządzenia wirtualnego przed użyciem przejścia do Internetu, następnie opóźnienie może wprowadzony przez wirtualnych urządzeń sieciowych.

### <a name="azure-regions-virtual-networks-and-latency"></a>Regiony platformy Azure, sieci wirtualnych i czas oczekiwania

Regiony platformy Azure składają się z wielu centrów danych, które istnieją w jej określenie obszaru geograficznego. Te centra danych może nie być fizycznie obok siebie, a w niektórych przypadkach może być rozdzielony jak 10 km. Sieć wirtualna jest logiczną nakładki na podstawie sieci centrum danych fizycznych platformy Azure i sieć wirtualna nie oznacza topologii dowolnej określonej sieci w centrum danych. Na przykład maszyny Wirtualnej, A i B maszyn wirtualnych znajdują się w tej samej sieci wirtualnej i podsieci, ale mogą znajdować się w różnych stojakach wierszy lub nawet centrów danych. Mogą one być oddzielone stopy kabel światłowodowy lub kilometrów kabel światłowodowy. Ten stan może stanowić zmienne opóźnienie (kilku milisekund różnicę) między różnych maszyn wirtualnych.

Ta położenia geograficznego, a zatem opóźnienia między dwiema maszynami wirtualnymi, może mieć wpływ omawia konfigurację programu zestawy dostępności i strefy dostępności, jednak odległość między centrami danych w regionie głównie ich i specyficzne dla regionu Topologia centrum danych w regionie.

### <a name="source-nat-port-exhaustion"></a>Wyczerpanie portów translatora adresów Sieciowych źródła

Wdrożenie na platformie Azure mogą komunikować się z punktami końcowymi spoza platformy Azure w publicznej sieci Internet i/lub publiczną przestrzeń adresów IP. Gdy wystąpienie inicjuje tego połączenia wychodzącego, Azure dynamicznie mapuje prywatny adres IP na publiczny adres IP. Po utworzeniu tego mapowania ruch powrotny dla tego ruchu wychodzącego przepływu pochodzącej może także łączyć prywatny adres IP skąd pochodzi przepływ.

Dla każdego połączenia wychodzącego modułu równoważenia obciążenia platformy Azure, musisz utrzymywać to mapowanie przez pewien czas. Z wieloma dzierżawami naturze platformy Azure utrzymywanie to mapowanie dla każdego przepływu wychodzącego dla każdej maszyny Wirtualnej może być intensywnie korzystających z zasobów. Dlatego istnieją ograniczenia, które są ustawiane i na podstawie konfiguracji sieci wirtualnej platformy Azure. Lub bardziej precyzyjne - podane Maszynie wirtualnej platformy Azure można wprowadzać tylko liczbę połączeń wychodzących w danym momencie. Po wyczerpaniu tych limitów maszyny Wirtualnej platformy Azure będą mogli wszelkie dalsze wychodzących połączeń.

To zachowanie jest jednak można konfigurować. Aby uzyskać więcej informacji na temat [SNAT SNAT portu i wyczerpania], zobacz [w tym artykule](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Zmierzyć wydajność sieci na platformie Azure

Liczba maksymalne wartości wydajności, w tym artykule są powiązane z opóźnieniem sieci / obustronne czasu (RTT) między dwiema maszynami wirtualnymi. Ta sekcja zawiera sugestie dotyczące sposobu testowania opóźnienia/RTT oraz wydajności protokołu TCP i wydajność sieci maszyny Wirtualnej. Dostosowanie omówione powyżej wartości TCP/IP & sieci i wydajności przetestowany przy użyciu technik opisanych poniżej. Wartości opóźnienia, rozmiar jednostki MTU, MSS i rozmiaru okna może służyć w obliczeniach wymienionych powyżej i teoretycznych maksymalne wartości, można porównać do rzeczywistych wartości przestrzegać podczas testowania.

### <a name="measure-round-trip-time-and-packet-loss"></a>Czas błądzenia miary i utraty pakietów

Wydajność protokołu TCP intensywnie korzysta RTT i utraty pakietów. Najprostszym sposobem, aby zmierzyć RTT i utraty pakietów jest za pomocą narzędzia ping dostępne w Windows i Linux. Dane wyjściowe polecenia ping pokaże minimalny/maksymalny/średni opóźnienia między źródłowy i docelowy oraz utraty pakietów. Domyślnie polecenie ping używa protokołu ICMP. Aby przetestować TCP RTT, wówczas narzędzie PsPing mogą być używane. Więcej informacji na temat PsPing znajduje się w temacie [ten link](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Rzeczywista przepływność miary połączenia protokołu TCP

NTttcp jest narzędziem, które służy do testowania wydajności protokołu TCP systemu Linux lub Windows maszyny Wirtualnej. Różne ustawienia protokołu TCP można tweaked i korzyści przetestowane za pomocą NTttcp. Więcej informacji na temat NTttcp można znaleźć w poniższych łączy.

- [Przepustowość/testowania (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [NTttcp Utility](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Miary rzeczywistej przepustowość maszyny wirtualnej

Testowanie wydajności różnych typach maszyn wirtualnych, przyspieszonej sieci i tak dalej, można przetestować przy użyciu narzędzia dotyczące programu Iperf; również dostępne w systemie Linux i Windows. Dotyczące programu Iperf; służy do testowania ogólną przepustowość sieci TCP lub UDP. TCP przepływności testów za pomocą systemu dotyczące programu Iperf; wpływało czynników omówionych w tym artykule (czas oczekiwania, RTT i tak dalej). Dlatego UDP może przynieść lepszych wyników, po prostu testowania maksymalną przepustowość.

Dodatkowe informacje można znaleźć poniżej:

- [Rozwiązywanie problemów z wydajnością sieci usługi Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Jak zweryfikować przepływność sieci VPN do sieci wirtualnej](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Wykrywać nieefektywne zachowań protokołu TCP

Klienci platformy Azure może zostać wyświetlony pakiety TCP przy użyciu flag TCP (WOREK, potwierdzenia Zduplikowanej, RETRANSMISJI i szybkie ponowne przesłanie) w przechwytywania pakietów, które mogą wskazywać problemy z wydajnością sieci. Te pakiety wskazują specjalnie nieefektywności sieci na skutek utraty pakietów. Utrata pakietów nie jest jednak zawsze ze względu na problemy z wydajnością systemu Azure. Problemy z wydajnością może wynikać z aplikacji, system operacyjny lub inne problemy, które nie może być bezpośrednio związana z platformą Azure. Jest również pamiętać, że niektóre retransmisji lub zduplikowane potwierdzenia w sieci jest normalne — protokołów TCP zostały zbudowane wiarygodne. I dowody tych pakietów protokołu TCP w przechwytywania pakietów nie musi oznaczać problem z siecią systemowych, chyba że są one nadmierne.

Jednakże należy podać wyraźnie czy te typy pakietów oznaczeń przepustowość TCP nie osiągnęła maksymalną wydajność — przyczyn omówionych w innych częściach.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy Wiemy już, dostrajanie wydajności protokołu TCP/IP dla maszyn wirtualnych platformy Azure, kolejne kroki obejmują [planowanie innych sieci wirtualnych](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-vnet-plan-design-arm) uwagi lub [Dowiedz się więcej na temat łączenia i konfigurowanie sieci wirtualnych](https://docs.microsoft.com/en-us/azure/virtual-network/).