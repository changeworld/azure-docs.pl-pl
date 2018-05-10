---
title: Połączeń wychodzących na platformie Azure (klasyczne) | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak Azure umożliwia usługi do komunikacji z publicznego internetowych usług w chmurze.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: kumud
ms.openlocfilehash: f6452d8f88b91fe0cbf144ce951b84ba4cec0047
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="outbound-connections-classic"></a>Połączenia wychodzące (klasyczne)

Platforma Azure udostępnia łączność wychodząca wdrożeń klienta za pomocą kilku różnych mechanizmów. W tym artykule opisano scenariusze są, gdy mają one zastosowanie, jak działają i jak nimi zarządzać.

>[!NOTE]
>W tym artykule omówiono Classic tylko w przypadku wdrożeń.  Przegląd [połączeń wychodzących](load-balancer-outbound-connections.md) we wszystkich scenariuszach wdrażania Menedżera zasobów na platformie Azure.

Wdrażanie na platformie Azure mogą komunikować się z punktami końcowymi poza Azure w ramach publicznej przestrzeni adresów IP. Gdy wystąpienie inicjuje przepływu wychodzącego do miejsca docelowego w publicznej przestrzeni adresów IP, Azure dynamicznie mapuje prywatnego adresu IP do publicznego adresu IP. Po utworzeniu tego mapowania zwracany ruchu dla tego ruchu wychodzącego przepływu zdalnych zapewnia także łączność prywatnego adresu IP pochodzenie przepływu.

Azure używa translatora adresów sieciowych źródła (SNAT) do tej funkcji. W przypadku wielu prywatnych adresów IP są maskaradę za jeden publiczny adres IP, korzysta z usługi Azure [portu translacji adresów (PAWEŁ)](#pat) do zamaskowane pod prywatnych adresów IP. Porty efemeryczne są używane do PAWEŁ i są [wstępnie przydzielonych](#preallocatedports) na podstawie rozmiaru puli.

Dostępnych jest wiele [scenariusze wychodzących](#scenarios). W razie potrzeby można połączyć tych scenariuszy. Przeglądać je uważnie, aby zrozumieć możliwości, ograniczenia i wzorce, ponieważ mają one zastosowanie do modelu wdrażania oraz scenariusz aplikacji. Przejrzyj wskazówki dotyczące [Zarządzanie tych scenariuszy](#snatexhaust).

## <a name="scenarios"></a>Omówienie scenariusza

Platforma Azure udostępnia trzy różnych metod na osiągnięcie wdrożenia klasycznego łączność wychodząca.  Nie wszystkie wdrożenia klasycznego zawierają wszystkie trzy scenariusze dostępne do nich:

| Scenariusz | Metoda | Protokoły IP | Opis | Rola procesu roboczego sieci Web | IaaS | 
| --- | --- | --- | --- | --- | --- |
| [1. Maszyna wirtualna o wystąpieniu poziomu publicznego adresu IP](#ilpip) | SNAT, port maskaradę nieużywane. | TCP, UDP, ICMP, ESP | Azure używa publicznego adresu IP, przypisane maszyny wirtualnej. Wystąpienie ma wszystkie dostępne porty efemeryczne. | Nie | Yes |
| [2. publiczny punkt końcowy równoważeniem obciążenia](#publiclbendpoint) | SNAT z portu maskaradę (PAT) do publicznego punktu końcowego | TCP, UDP | Azure udostępnia wiele punktów końcowych prywatnej publicznego endpoint publicznego adresu IP. PAWEŁ Azure używa porty efemeryczne publicznego punktu końcowego. | Yes | Yes |
| [3. Autonomiczne maszyny Wirtualnej ](#defaultsnat) | SNAT z portu maskaradę (PAT) | TCP, UDP | Azure automatycznie wyznacza publicznego adresu IP dla SNAT współużytkuje ten publiczny adres IP z całego wdrożenia i używa PAWEŁ porty efemeryczne publiczny punkt końcowy adres IP. Jest to rezerwowy scenariusz dla powyższych scenariuszy. Firma Microsoft nie zaleca, aby uzyskać wgląd i większą kontrolę. | Yes | Yes |

Jest to podzbiór wychodzące połączenie funkcje dostępne w przypadku wdrożeń usługi Resource Manager na platformie Azure.  

Różnych wdrożeń w klasycznym mają różne funkcje:

| Wdrożenie klasyczne | Funkcje dostępne | 
| --- | --- |
| Maszyna wirtualna | Scenariusz [1](#ilpip), [2](#publiclbendpoint), lub [3](#defaultsnat) |
| Rola procesu roboczego sieci Web | tylko scenariusz [2](#publiclbendpoint), [3](#defaultsnat) | 

[Środki zaradcze strategii](#snatexhaust) również mieć tego samego różnice.

[Algorytm używany do przydzielone porty efemeryczne](#ephemeralports) PAWEŁ w przypadku wdrożeń klasycznych jest taka sama jak w przypadku wdrożenia zasobów usługi Azure Resource Manager.

### <a name="ilpip"></a>Scenariusz 1: Maszyna wirtualna adres publiczny adres IP na poziomie wystąpienia

W tym scenariuszu maszyna wirtualna ma wystąpienie poziomu publicznego adresu IP (ILPIP) przypisane do niej. Jeśli chodzi o połączeń wychodzących, nie ma znaczenia, czy maszyna wirtualna ma punkt końcowy ze zrównoważonym obciążeniem, czy nie. Ten scenariusz ma pierwszeństwo przed jej innym osobom. W przypadku ILPIP maszyna wirtualna używa ILPIP dla wszystkich przepływów wychodzących.  

Publiczny IP przypisane do maszyny Wirtualnej jest 1:1 relacji (zamiast 1: duży zakres) oraz implementowany jako bezstanowych translatora adresów sieciowych. 1:1  Port zamaskowana (PAWEŁ) nie jest używany, a maszyna wirtualna ma wszystkie porty efemeryczne dostępne do użycia.

Aplikacja inicjuje wiele przepływów wychodzących i występują wyczerpania portu SNAT, warto rozważyć przypisywanie [ILPIP złagodzić ograniczenia SNAT](#assignilpip). Przegląd [wyczerpania Zarządzanie SNAT](#snatexhaust) w całości.

### <a name="publiclbendpoint"></a>Scenariusz 2: Publicznego równoważeniem obciążenia punktu końcowego

W tym scenariuszu jest skojarzony z publicznego adresu IP za pośrednictwem punktu końcowego równoważeniem obciążenia maszyny Wirtualnej lub roli procesu roboczego sieci Web. Maszyna wirtualna nie ma publicznego adresu IP przypisane do niej. 

Gdy maszyna wirtualna z równoważeniem obciążenia tworzy przepływu wychodzącego, Azure tłumaczy prywatnej źródłowy adres IP przepływu wychodzącego do publicznego adresu IP publiczny punkt końcowy z równoważeniem obciążenia. Platforma Azure korzysta SNAT tej funkcji. Używa również Azure [PAT](#pat) do zamaskowane pod wiele prywatnych adresów IP za pomocą publicznego adresu IP. 

Porty efemeryczne publicznego frontonu adresu IP usługi równoważenia obciążenia są używane do rozróżniania poszczególnych przepływów pochodzi przez maszynę Wirtualną. Dynamicznie używa SNAT [wstępnie przydzielonych portów tymczasowych](#preallocatedports) utworzenia przepływów wychodzących. W tym kontekście tymczasowych portów używanych do SNAT są nazywane SNAT portów.

Są wstępnie przydzielonych portów SNAT, zgodnie z opisem w [SNAT zrozumienia i PAWEŁ](#snat) sekcji. Są one ograniczone zasób, który może być wyczerpany. Ważne jest, aby zrozumieć, jak są one [używane](#pat). Aby zrozumieć sposób projektowania dla tego zużycia i ograniczyć odpowiednio do potrzeb, przejrzyj [wyczerpania Zarządzanie SNAT](#snatexhaust).

Gdy [wiele publicznych równoważeniem obciążenia punktów końcowych](load-balancer-multivip.md) istnieje, te publiczne adresy IP są [kandydatem do przepływów wychodzących](#multivipsnat), i jeden losowo wybrany.  

### <a name="defaultsnat"></a>Scenariusz 3: Nie publicznego adresu IP skojarzonego

W tym scenariuszu maszyny Wirtualnej lub roli procesu roboczego sieci Web nie jest częścią publiczny punkt końcowy o zrównoważonym obciążeniu.  A w przypadku maszyny Wirtualnej, nie ma przypisanego adresu ILPIP. Gdy maszyna wirtualna tworzy przepływu wychodzącego, Azure tłumaczy prywatnej źródłowy adres IP przepływu wychodzącego do źródłowego publicznego adresu IP. Publiczny adres IP używany dla tego przepływu ruchu wychodzącego nie można skonfigurować, nie będą uwzględniane względem subskrypcji publicznego adresu IP limit zasobów.  Azure automatycznie przydzieli ten adres.

Platforma Azure korzysta SNAT z portu maskaradę ([PAT](#pat)) do tej funkcji. Ten scenariusz jest podobny do [Scenariusz 2](#lb), z wyjątkiem jest nie kontroluje adres IP używany. Jest to rezerwowy scenariusz dla gdy nie istnieją scenariusze 1 i 2. Jeśli chcesz, aby kontrolować wychodzący adres nie zaleca tego scenariusza. W przypadku połączeń wychodzących krytyczną częścią aplikacji została wybrana opcja inny scenariusz.

Są wstępnie przydzielonych portów SNAT, zgodnie z opisem w [SNAT zrozumienia i PAWEŁ](#snat) sekcji.  Liczba maszyn wirtualnych lub ról procesów roboczych Web udostępnianie publicznego adresu IP określa liczbę przydzielony wstępnie porty efemeryczne.   Ważne jest, aby zrozumieć, jak są one [używane](#pat). Aby zrozumieć sposób projektowania dla tego zużycia i ograniczyć odpowiednio do potrzeb, przejrzyj [wyczerpania Zarządzanie SNAT](#snatexhaust).

## <a name="snat"></a>Opis SNAT i Marta

### <a name="pat"></a>Port zamaskowana SNAT (PAWEŁ)

Po wdrożeniu połączeń wychodzących, każde źródło połączenia wychodzącego jest napisany od nowa. Źródło jest napisany od nowa z prywatnej przestrzeń adresów IP do publicznego adresu IP, skojarzone z wdrożeniem (na podstawie opisanych powyżej scenariuszy). W publicznej przestrzeni adresów IP 5-elementowej przepływu (źródłowy adres IP, port źródłowy, protokół transportu IP, docelowy adres IP, port docelowy) muszą być unikatowe.  

Tymczasowych porty (SNAT) są używane do osiągnięcia tego celu po ponowne zapisywanie prywatnej źródłowy adres IP, ponieważ wiele przepływów pochodzi z jednego publicznego adresu IP. 

Jeden port SNAT jest używany na przepływ pojedynczego adresu IP, portu i protokołu. Wiele przepływów do docelowego adresu IP, portu i protokołu zajmują jednego portu SNAT każdego przepływu. Dzięki temu strumienie są unikatowe, gdy pochodziły z tej samej publiczny adres IP i przejdź do tego samego adresu IP, portu i protokołu. 

Wiele przepływów, każdy z nich do innego adresu IP, port i protokół, współużytkować jeden port SNAT. Docelowy adres IP, port oraz protokół zapewnić przepływów unikatowy bez konieczności stosowania dodatkowych źródła porty, aby odróżnić przepływów w publicznej przestrzeni adresów IP.

Wyczerpania zasobów portu SNAT przepływów wychodzących zakończyć się niepowodzeniem aż do istniejących przepływów zwolnienia SNAT portów. Moduł równoważenia obciążenia zwraca SNAT portów, gdy przepływ zamyka i używa [limit czasu bezczynności 4-minutowy](#idletimeout) dla odzyskiwania porty SNAT z bezczynności przepływów.

Wzorce ograniczyć warunki, które często doprowadzić do wyczerpania portu SNAT, można przejrzeć [Zarządzanie SNAT](#snatexhaust) sekcji.

### <a name="preallocatedports"></a>Wstępne przydzielanie portów efemerycznych dla portu maskaradę SNAT (PAWEŁ)

Azure używa algorytmu, aby określić liczbę SNAT wstępnie przydzielonych portów dostępnych na podstawie rozmiaru puli wewnętrznej bazy danych przy użyciu portu zamaskowana SNAT ([PAT](#pat)). Porty SNAT są porty efemeryczne, które są dostępne dla określonego adresu źródłowego publicznego adresu IP.

Azure preallocates SNAT portów gdy wystąpienie jest wdrażany w oparciu o liczbę wystąpień maszyny Wirtualnej lub roli procesu roboczego sieci Web udostępnianie danego publicznego adresu IP.  Po utworzeniu przepływów wychodzących [PAT](#pat) dynamicznie zużywa (w granicach przydzielonych wstępnie) i zwalnia te porty, po zamknięciu przepływ lub [limitów czasu bezczynności](#ideltimeout) stanie.

W poniższej tabeli przedstawiono preallocations portu SNAT warstw rozmiary puli wewnętrznej bazy danych:

| Wystąpienia | Przydzielony wstępnie SNAT portów dla każdego wystąpienia |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |

Należy pamiętać, że liczba dostępnych portów SNAT nie przekłada się bezpośrednio do liczby przepływów. Jeden port SNAT mogą być ponownie używane dla wielu celów unikatowy. Porty są używane tylko wtedy, gdy jest to niezbędne do zapewnienia przepływów unikatowy. Aby uzyskać wskazówki dotyczące projektowania i środki zaradcze, można znaleźć w sekcji o [jak zarządzać zasobem wyczerpującymi](#snatexhaust) i sekcji opisano [PAT](#pat).

Zmiana rozmiaru wdrożenia może mieć wpływ na niektóre przepływów ustalonych. Jeśli rozmiar puli wewnętrznej bazy danych zwiększa się i przechodzi do następnej warstwy, połowy Twojej przydzielony wstępnie porty SNAT odzyskane podczas przejścia do następnej warstwy większych puli wewnętrznej bazy danych. Przepływów, które są skojarzone z portem SNAT regeneracji limit czasu i należy ustanowić. Próbie nowy przepływ przepływ powiedzie się natychmiast tak długo, jak są dostępne porty przydzielony wstępnie.

Jeśli zmniejsza rozmiar wdrożenia i zwiększyć liczbę dostępnych portów SNAT przejścia do dolnej warstwy. W takim przypadku istniejące przydzielonych SNAT portów i nie ma wpływu na ich odpowiednich przepływów.

SNAT porty są protokół transportu IP określonego (TCP i UDP są przechowywane osobno) i są wydawane w następujących warunkach:

### <a name="tcp-snat-port-release"></a>Wersja port TCP SNAT

- Jeśli zarówno klient/serwer wysyła potwierdzenia/w wynikach wyszukiwania, SNAT port zostaną wydane 240 sekund.
- Jeśli występuje RST, SNAT port zostaną wydane po 15 sekund.
- Osiągnięto limit czasu bezczynności

### <a name="udp-snat-port-release"></a>Wersja portu UDP SNAT

- Osiągnięto limit czasu bezczynności

## <a name="problemsolving"></a> Rozwiązywanie problemów 

Ta sekcja jest przeznaczona do zmniejszenia wyczerpania SNAT i innych scenariuszy, które mogą wystąpić w przypadku połączeń wychodzących na platformie Azure.

### <a name="snatexhaust"></a> Zarządzanie wyczerpania portu SNAT (PAWEŁ)
[Porty efemeryczne](#preallocatedports) używany do [PAT](#pat) są zużywalnymi zasobami, zgodnie z opisem w [nie publicznego adresu IP skojarzonego](#defaultsnat) i [publiczny punkt końcowy równoważeniem obciążenia](#publiclbendpoint).

Jeśli wiadomo, że jest inicjowanie liczbę wychodzących połączeń TCP lub UDP do tego samego adresu IP i portu i obserwować niepowodzeniem połączenia wychodzące lub zalecana przy pomocy technicznej jest bardzo wyczerpuje porty SNAT (wstępnie przydzielonych [tymczasowych porty](#preallocatedports) używane przez [PAT](#pat)), masz kilka opcji ogólnych środki zaradcze. Przejrzyj te opcje i zdecyduj, jaki jest dostępny i najlepsze w przypadku danego scenariusza. Istnieje możliwość, że co najmniej jeden ułatwiają zarządzanie tego scenariusza.

Jeśli problemy z zrozumienie zachowania połączeń wychodzących można użyć adresu IP stosu statystyk (netstat). Lub może być przydatne do przestrzegania zachowania połączeń przy użyciu przechwytywania pakietów.

#### <a name="connectionreuse"></a>Modyfikowanie aplikacji do ponownego użycia połączenia 
Można zmniejszyć zapotrzebowanie na porty efemeryczne, używanych do SNAT przez ponowne użycie połączenia w aplikacji. Jest to szczególnie istotne dla protokołów, takich jak HTTP/1.1, gdzie ponownemu połączenia jest ustawieniem domyślnym. I innych protokołów, które zgodnie z ich transportu (na przykład REST) mogą korzystać z kolei za pomocą protokołu HTTP. 

Ponowne użycie zawsze jest lepszym rozwiązaniem niż poszczególnych, atomic połączeń TCP dla każdego żądania. Ponowne użycie powoduje więcej wydajności, bardzo wydajny transakcji protokołu TCP.

#### <a name="connection pooling"></a>Modyfikowanie aplikacji do korzystania z puli połączeń
Można wdrożyć połączenia buforowanie schemat w aplikacji, które żądania są dystrybuowane wewnętrznie przez ustalony zbiór połączeń (każdego ponowne użycie w miarę możliwości). Ten schemat ogranicza liczbę portów tymczasowych w użyciu i tworzy środowisku bardziej przewidywalne. Ten schemat także może zwiększyć przepływność żądań zezwalając wiele równoczesnych operacji, gdy blokuje pojedynczego połączenia w przypadku odpowiedzi operacji.  

Pula połączeń może już istnieć w ramach używanego do tworzenia aplikacji lub ustawienia konfiguracji dla aplikacji. Można połączyć z połączenia ponownemu puli połączeń. Wiele żądań mogły używać stały, przewidywalnych liczba portów do tego samego adresu IP i portu. Żądania również korzystać z efektywne wykorzystanie transakcji TCP zmniejsza opóźnienia i użycia zasobów. Transakcje protokołu UDP mogą również skorzystać, ponieważ zarządzanie liczba przepływów UDP mogą z kolei uniknąć warunków spalin i zarządzanie SNAT użycie portu.

#### <a name="retry logic"></a>Modyfikowanie aplikacji na używanie mniej agresywne Logika ponawiania
Gdy [wstępnie przydzielonych portów tymczasowych](#preallocatedports) używany do [PAT](#pat) są wyczerpane lub aplikacji występują błędy, życie aktywnego lub atak metodą prób bez zanikania i wycofywania logiki spowodować wyczerpanie wystąpienia lub utrwalić. Można zmniejszyć zapotrzebowanie na porty efemeryczne przy użyciu mniej agresywne logiki ponawiania próby. 

Porty efemeryczne mieć 4-minutowy limit czasu bezczynności (nie można dostosować). Jeśli ponowne próby są zbyt agresywnie, wyczerpanie nie ma możliwość wyczyszczone samodzielnie. W związku z tym biorąc pod uwagę, jak--i jak często--aplikacji ponowi próbę transakcji jest krytyczną częścią projektu.

#### <a name="assignilpip"></a>Przypisz wystąpienie poziomu publicznego adresu IP do każdej maszyny Wirtualnej
Przypisywanie ILPIP zmiany Twojego scenariusza [wystąpienia poziomu publicznego adresu IP do maszyny Wirtualnej](#ilpip). Wszystkie porty efemeryczne publicznego adresu IP, które są używane dla każdej maszyny Wirtualnej są dostępne dla maszyny Wirtualnej. (W przeciwieństwie do scenariuszy gdzie porty efemeryczne publicznego adresu IP są udostępniane wszystkie skojarzone z wdrożeniem odpowiednich maszyn wirtualnych). Brak możliwości wziąć pod uwagę, takie jak potencjalny wpływ listę dozwolonych podobnej dużą liczbę pojedyncze adresy IP.

>[!NOTE] 
>Ta opcja nie jest dostępna dla roli proces roboczy sieci web.

### <a name="idletimeout"></a>Użyj keepalives, aby zresetować wychodzącego limit czasu bezczynności

Połączenia wychodzące mieć 4-minutowy limit czasu bezczynności. Tego limitu czasu nie jest zmieniane. Jednak aby odświeżyć przepływu bezczynności i zresetować ten limit czasu bezczynności, w razie potrzeby mogą używać transportu (na przykład keepalives TCP) lub keepalives warstwy aplikacji.  Skontaktuj się z od dostawcy oprogramowania spakowanych Określa, czy jest to obsługiwane lub jak je włączyć.  Zwykle tylko po jednej stronie ma generować keepalives zresetować limit czasu bezczynności. 

## <a name="discoveroutbound"></a>Odnajdywanie publicznego adresu IP, który korzysta z maszyny Wirtualnej
Istnieje wiele sposobów, aby określić źródłowy publiczny adres IP połączeń wychodzących. OpenDNS zapewnia usługę, które mogą być prezentowane z publicznego adresu IP maszyny Wirtualnej. 

Za pomocą polecenia nslookup, możesz wysłać zapytanie DNS dla nazwy myip.opendns.com OpenDNS program rozpoznawania nazw. Usługa zwraca źródłowy adres IP użyty do wysłania zapytania. Po uruchomieniu następującego zapytania z maszyny Wirtualnej, odpowiedź jest publiczny adres IP używany dla tej maszyny Wirtualnej:

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [modułu równoważenia obciążenia](load-balancer-overview.md) używane w przypadku wdrożeń usługi Resource Manager.
- Więcej informacji na temat trybu [wychodzące połączenie](load-balancer-outbound-connections.md) scenariusze dostępne w przypadku wdrożeń usługi Resource Manager.
