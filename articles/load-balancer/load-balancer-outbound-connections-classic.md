---
title: Połączenia wychodzące na platformie Azure (wersja klasyczna)
titlesuffix: Azure Load Balancer
description: W tym artykule wyjaśniono, jak platforma Azure zapewnia usługi do komunikacji z publicznych usług internetowych w chmurze.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2018
ms.author: kumud
ms.openlocfilehash: 3267d79387586f5ca8475d7ac0ed0f86d3f64f0d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60595043"
---
# <a name="outbound-connections-classic"></a>Połączenia wychodzące (klasyczne)

Platforma Azure udostępnia połączenia ruchu wychodzącego dla wdrożenia klienta za pomocą kilku różnych mechanizmów. W tym artykule opisano scenariusze są, gdy są spełnione, jak działają i jak nimi zarządzać.

>[!NOTE]
>W tym artykule opisano tylko w przypadku wdrożeń model klasyczny.  Przegląd [połączeń wychodzących](load-balancer-outbound-connections.md) we wszystkich scenariuszach wdrażania usługi Resource Manager na platformie Azure.

Wdrożenie na platformie Azure mogą komunikować się z punktami końcowymi spoza platformy Azure w przestrzeń publicznych adresów IP. Gdy wystąpienie inicjuje przepływ ruchu wychodzącego do miejsca docelowego w przestrzeń publicznych adresów IP, Azure dynamicznie mapuje prywatny adres IP na publiczny adres IP. Po utworzeniu tego mapowania ruch powrotny dla tego ruchu wychodzącego przepływu pochodzącej może także łączyć prywatny adres IP skąd pochodzi przepływ.

Platforma Azure używa translatora adresów sieciowych źródła (SNAT), aby wykonać tę funkcję. W przypadku wielu prywatnych adresów IP są maskaradę za jeden publiczny adres IP, platforma Azure używa [portu translacji adresów (PAT)](#pat) poczynania prywatnych adresów IP. Porty efemeryczne są używane do osobisty token dostępu i są [wstępnie przydzielonych](#preallocatedports) na podstawie rozmiaru puli.

Dostępnych jest wiele [scenariusze ruchu wychodzącego](#scenarios). Można połączyć te scenariusze, zgodnie z potrzebami. Zapoznaj się z nimi, aby zrozumieć możliwości, ograniczenia i wzorce, ponieważ mają one zastosowanie do modelu wdrażania i scenariusz aplikacji. Przejrzyj wskazówki dotyczące [Zarządzanie te scenariusze](#snatexhaust).

## <a name="scenarios"></a>Omówienie scenariusza

Platforma Azure udostępnia trzy różnych metod na osiągnięcie łączności wychodzącej klasycznego wdrożenia.  Nie wszystkie wdrożenia klasycznego mają wszystkie trzy scenariusze dostępne dla nich:

| Scenariusz | Metoda | Protokoły IP | Opis | Rola procesu roboczego w sieci Web | IaaS | 
| --- | --- | --- | --- | --- | --- |
| [1. Maszyny Wirtualnej przy użyciu adresu publicznego adresu IP poziomu wystąpienia](#ilpip) | Nieużywany port maskaradę SNAT, | TCP, UDP, ICMP, ESP | Platforma Azure używa publicznego adresu IP, przypisany do maszyny wirtualnej. Wystąpienie ma wszystkie dostępne porty efemeryczne. | Nie | Tak |
| [2. publicznego punktu końcowego, które są ze zrównoważonym obciążeniem](#publiclbendpoint) | SNAT przy użyciu portu (PAT) maskaradę publicznego punktu końcowego | TCP, UDP | Platforma Azure udostępnia publicznego adresu IP adres punktu końcowego publicznego z wieloma punktami końcowymi prywatnych. Platforma Azure używa portów tymczasowych publicznego punktu końcowego uzyskać osobisty token dostępu. | Tak | Tak |
| [3. Autonomiczna maszyna wirtualna](#defaultsnat) | SNAT przy użyciu portu maskaradę (PAT) | TCP, UDP | Azure automatycznie wyznacza publiczny adres IP dla SNAT, udostępni ten publiczny adres IP całe wdrożenie i wykorzystuje porty efemeryczne punktu końcowego publicznego adresu IP osobisty token dostępu. Jest to rezerwowego scenariusz dla powyższych scenariuszy. Firma Microsoft nie zaleca, aby uzyskać lepszy wgląd i kontrolę. | Tak | Yes |

Jest to podzbiór funkcji połączeń wychodzących dostępnych w przypadku wdrożeń usługi Resource Manager na platformie Azure.  

Różnych wdrożeń w modelu klasycznym mają różne funkcje:

| Wdrożenie klasyczne | Funkcje dostępne | 
| --- | --- |
| Maszyna wirtualna | Scenariusz [1](#ilpip), [2](#publiclbendpoint), lub [3](#defaultsnat) |
| Rola procesu roboczego w sieci Web | tylko scenariusz [2](#publiclbendpoint), [3](#defaultsnat) | 

[Strategii ograniczania ryzyka](#snatexhaust) też różnią się w ten sam sposób.

Algorytm używany do przydzielone portów tymczasowych dla osobisty token dostępu w przypadku wdrożeń klasycznych jest taka sama, jak w przypadku wdrożenia zasobów usługi Azure Resource Manager.

### <a name="ilpip"></a>Scenariusz 1: Maszyny Wirtualnej przy użyciu adresu publicznego adresu IP poziomu wystąpienia

W tym scenariuszu maszyna wirtualna ma wystąpienia poziom publicznego adresu IP (ILPIP) do niej przypisany. Jeśli chodzi o połączeniach wychodzących, nie ma znaczenia, czy maszyna wirtualna ma punkt końcowy z równoważeniem obciążenia, czy nie. Ten scenariusz ma pierwszeństwo przed innymi. Gdy używany jest ILPIP, maszyna wirtualna używa ILPIP wszystkie przepływy ruchu wychodzącego.  

Publiczny adres IP przypisane do maszyny Wirtualnej jest 1:1 relację (zamiast 1: duży zakres) i zaimplementowane jako bezstanowej translatorem adresów sieciowych 1:1  Nie jest używany port zamaskowany (PAT), a maszyna wirtualna ma wszystkie porty efemeryczne dostępne do użycia.

Jeśli wystąpią wyczerpanie portów SNAT aplikacji inicjuje wiele przepływów ruchu wychodzącego, należy wziąć pod uwagę przypisywanie [ILPIP złagodzić ograniczenia SNAT](#assignilpip). Przegląd [wyczerpania Zarządzanie SNAT](#snatexhaust) w całości.

### <a name="publiclbendpoint"></a>Scenariusz 2: Publiczny punkt końcowy z równoważeniem obciążenia

W tym scenariuszu maszyny Wirtualnej lub sieci Web, rola procesu roboczego jest skojarzony z publicznym adresem IP za pośrednictwem punktu końcowego ze zrównoważonym obciążeniem. Maszyna wirtualna nie ma do niej przypisany publiczny adres IP. 

W przypadku maszyn wirtualnych ze zrównoważonym obciążeniem tworzy przepływu wychodzącego, Azure tłumaczy prywatnej źródłowy adres IP przepływu wychodzącego publiczny adres IP ze zrównoważonym obciążeniem punktu końcowego publicznego. Platforma Azure używa SNAT, aby wykonać tę funkcję. Platforma Azure również używa [osobisty token dostępu](#pat) poczynania wielu prywatnych adresów IP za pomocą publicznego adresu IP. 

Efemeryczne porty frontonu adres IP publicznego modułu równoważenia obciążenia są używane w celu odróżnienia poszczególnych przepływy tworzone przez maszynę Wirtualną. Dynamicznie używa SNAT [wstępnie przydzielonych portów tymczasowych](#preallocatedports) utworzenia przepływy wychodzące. W tym kontekście portów tymczasowych używany do SNAT są nazywane SNAT portów.

Są wstępnie przydzielonych portów SNAT, zgodnie z opisem w [SNAT zrozumienie i osobisty token dostępu](#snat) sekcji. Są one ograniczone zasób, który może wyczerpać. Jest ważne zrozumieć, jak są one [używane](#pat). Aby dowiedzieć się, jak zaprojektować za to użycie i ograniczać zgodnie z potrzebami, zapoznaj się z [wyczerpania Zarządzanie SNAT](#snatexhaust).

Gdy [wielu publicznych ze zrównoważonym obciążeniem punktów końcowych](load-balancer-multivip.md) istnieje, te publiczne adresy IP są kandydatem do przepływy wychodzące i jeden losowo wybrany.  

### <a name="defaultsnat"></a>Scenariusz 3: Brak publicznego adresu IP, które są skojarzone

W tym scenariuszu maszyny Wirtualnej lub sieci Web, rola procesu roboczego nie jest częścią publicznego punktu końcowego o zrównoważonym obciążeniu.  A w przypadku maszyn wirtualnych, nie ma przypisanego adresu ILPIP. Podczas tworzenia maszyny Wirtualnej przepływu wychodzącego, Azure tłumaczy prywatnej źródłowy adres IP przepływu wychodzącego do publicznych źródłowego adresu IP. Publiczny adres IP używany dla tego przepływu ruchu wychodzącego nie konfiguruje się i nie wliczają subskrypcji publicznego adresu IP limit zasobów.  Platforma Azure automatycznie przydziela ten adres.

Platforma Azure używa SNAT przy użyciu portu maskaradę ([osobisty token dostępu](#pat)) do wykonywania tej funkcji. Ten scenariusz jest podobny do scenariusza 2, jednak istnieje nie kontroluje adres IP używany. Jest to rezerwowego scenariusz dla sytuacji scenariusze 1 i 2, nie istnieje. Jeśli chcesz mieć kontrolę nad adresów ruchu wychodzącego nie zaleca tego scenariusza. W przypadku połączeń wychodzących krytycznych części aplikacji została wybrana opcja inny scenariusz.

Są wstępnie przydzielonych portów SNAT, zgodnie z opisem w [SNAT zrozumienie i osobisty token dostępu](#snat) sekcji.  Liczba maszyn wirtualnych lub ról procesów roboczych w sieci Web do udostępniania publiczny adres IP określa liczbę przydzielonych wstępnie portów tymczasowych.   Jest ważne zrozumieć, jak są one [używane](#pat). Aby dowiedzieć się, jak zaprojektować za to użycie i ograniczać zgodnie z potrzebami, zapoznaj się z [wyczerpania Zarządzanie SNAT](#snatexhaust).

## <a name="snat"></a>Opis SNAT i osobisty token dostępu

### <a name="pat"></a>Port zamaskowany SNAT (PAT)

Po wdrożeniu sprawia, że połączenia wychodzącego, jest przepisany każde źródło połączeń wychodzących. Źródło jest przepisany z przestrzeń prywatnych adresów IP na publiczny adres IP skojarzonym z wdrożeniem (oparte na scenariuszach opisanych powyżej). W publicznej przestrzeni adresowej IP tuple 5 (źródłowy adres IP, port źródłowy, protokół transportowy IP, docelowy adres IP, port docelowy) przepływu musi być unikatowa.  

Efemeryczne porty (SNAT) są używane do osiągnięcia tego celu po ponownego zapisywania adresów prywatnych źródłowy adres IP, ponieważ wiele przepływów pochodzi z jednego publicznego adresu IP. 

Jednym SNAT port jest używany na przepływ jeden docelowy adres IP, portu i protokołu. Każdy przepływ zajmują jednoportowe SNAT wiele przepływów do docelowego adresu IP, portu i protokołu. Dzięki temu przepływy są unikatowe, gdy pochodziły z tego samego publicznego adresu IP i przejdź do tego samego adresu IP, portu i protokołu. 

Wiele przepływów, do innego adresu IP, portu i protokołu, udostępniać pojedynczy port SNAT. Docelowy adres IP, port i protokół unikatowość przepływy bez konieczności stosowania dodatkowych źródła portów w celu odróżnienia przepływów przestrzeń publicznych adresów IP.

Wyczerpania zasobów portu SNAT, przepływy wychodzące zakończyć się niepowodzeniem aż do istniejących przepływów zwolnienia SNAT portów. Moduł równoważenia obciążenia odzyskuje porty SNAT, gdy przepływ zostanie zamknięty i używa [limitu czasu bezczynności 4-minutowej](#idletimeout) dla odzyskiwanie porty SNAT wychodzących z przepływów bezczynności.

Wzorce i ułatwiają eliminowanie warunki, które często prowadzą do wyczerpanie portów SNAT, można przejrzeć [Zarządzanie SNAT](#snatexhaust) sekcji.

### <a name="preallocatedports"></a>Wstępne portów efemerycznych przydzielanie dla portu maskaradę SNAT (PAT)

Używa platformy Azure, algorytm, aby określić liczbę użyć funkcji SNAT względem wstępnie przydzielonych portów dostępnych na podstawie rozmiaru puli zaplecza przy użyciu portu zamaskowany SNAT ([osobisty token dostępu](#pat)). SNAT porty są dostępne dla określonego publicznego źródłowego adresu IP portów tymczasowych.

Azure preallocates SNAT portów w przypadku wystąpienia jest wdrażany w oparciu o liczbę wystąpień maszyn wirtualnych lub roli procesu roboczego internetowych udostępnianie danego publicznego adresu IP.  Po utworzeniu przepływy wychodzące [osobisty token dostępu](#pat) dynamicznie zużywa (w granicach przydzielony wstępnie) i zwalnia te porty, gdy strumień zostanie zamknięty lub się zdarzyć, limity czasu bezczynności.

W poniższej tabeli przedstawiono preallocations portu SNAT dla warstw rozmiary pul zaplecza:

| Wystąpienia | Przydzielony wstępnie porty SNAT za wystąpienie usługi |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |

Należy pamiętać, że liczba dostępnych portów SNAT nie przekłada się bezpośrednio do liczba przepływów. Pojedynczy port SNAT można ponownie wielu unikatowe miejsca docelowe. Porty są używane tylko wtedy, gdy jest to niezbędne do zapewnienia unikatowych przepływów. Aby uzyskać wskazówki dotyczące projektowania i ograniczania ryzyka, zapoznaj się z sekcją o [sposób zarządzać tym zasobem wyczerpującymi](#snatexhaust) i sekcji, która opisuje [osobisty token dostępu](#pat).

Zmiana rozmiaru wdrożenia mogą mieć wpływ na niektóre z ustanowionym przepływów. Jeśli rozmiar puli wewnętrznej bazy danych zwiększa się i przechodzi do następnej warstwy, połowa swoje przydzielony wstępnie portów SNAT są odzyskiwane podczas przejścia do następnej warstwie większych puli zaplecza. Przepływy, które są skojarzone z portem SNAT odzyskiwanego przekroczy limit czasu i trzeba je ponownie ustanowić. Jeśli nastąpi próba utworzenia nowego przepływu przepływ zakończy się pomyślnie natychmiast tak długo, jak przydzielony wstępnie porty są dostępne.

Jeśli zmniejsza rozmiar wdrożenia, a następnie zwiększa liczbę dostępnych portów SNAT przejścia do niższej warstwy. W tym przypadku SNAT istniejące przydzielonych portów i ich odpowiednich przepływów nie ulegają zmianom.

Jeśli usługa w chmurze jest ponownie wdrażana lub zmienione, infrastruktura tymczasowo mogą zgłaszać puli zaplecza za maksymalnie dwa razy na duży jak rzeczywista i platformy Azure będzie z kolei wstępnie Przydziel mniej SNAT portów dla każdego wystąpienia niż oczekiwano.  To jest tymczasowo zwiększyć prawdopodobieństwo SNAT wyczerpanie portów. Po pewnym czasie rozmiar puli będzie przejście do rzeczywistego rozmiaru i Azure automatycznie zwiększy przydzielony wstępnie portów SNAT oczekiwana liczba zgodnie z powyższej tabeli.  To zachowanie jest celowe i nie jest konfigurowany.

SNAT porty są określonego protokołu transportowego IP (TCP i UDP są obsługiwane osobno) i są wydawane w następujących warunkach:

### <a name="tcp-snat-port-release"></a>Wersja port TCP SNAT

- Jeśli oba serwerze/kliencie wysyła FIN/potwierdzenia, SNAT port zostaną wydane po 240 sekundach.
- Jeśli występuje RST, SNAT port zostaną wydane po 15 sekundach.
- Osiągnięto limit czasu bezczynności

### <a name="udp-snat-port-release"></a>Wersja port UDP SNAT

- Osiągnięto limit czasu bezczynności

## <a name="problemsolving"></a> Rozwiązywanie problemów 

Ta sekcja ma na celu ułatwić uniknięcie wyczerpania SNAT i innych scenariuszy, które mogą wystąpić w przypadku połączeń wychodzących na platformie Azure.

### <a name="snatexhaust"></a> Zarządzanie wyczerpanie portów SNAT (PAT)
[Porty efemeryczne](#preallocatedports) umożliwiający [osobisty token dostępu](#pat) są wyczerpującymi zasobów zgodnie z opisem w [nie publiczny adres IP skojarzony](#defaultsnat) i [publiczny punkt końcowy z równoważeniem obciążenia](#publiclbendpoint).

Jeśli wiesz, że one inicjowanie wielu TCP lub UDP na połączenia wychodzące przez ten sam docelowy adres IP i port, i obserwować niepowodzenie połączenia wychodzące lub doradza przez dział pomocy technicznej już wyczerpuje porty SNAT (wstępnie przydzielonych [efemeryczne porty](#preallocatedports) posługują się [osobisty token dostępu](#pat)), masz kilka opcji ogólnych środki zaradcze. Zapoznaj się z tymi opcjami i określić, jaka jest dostępna i najlepsze w przypadku danego scenariusza. Istnieje możliwość, że co najmniej jeden ułatwiają zarządzanie tego scenariusza.

Jeśli problemy z zrozumienie zachowania połączeń wychodzących można użyć statystyki stos IP (netstat). Lub może być przydatne do przestrzegania zachowania połączenia przy użyciu przechwytywania pakietów.

#### <a name="connectionreuse"></a>Modyfikowanie aplikacji to ponowne użycie połączenia 
Można zmniejszyć zapotrzebowanie na portów tymczasowych, które są używane do SNAT dzięki ponownemu wykorzystaniu połączeń w aplikacji. Jest to szczególnie istotne dla protokołów, takich jak HTTP/1.1, których ponowne użycie połączenia jest ustawieniem domyślnym. I innych protokołów, które korzystają z protokołu HTTP jako transportu (na przykład, interfejs API REST) mogą korzystać z kolei. 

Ponowne użycie zawsze jest lepsza niż osobę, atomic połączeń TCP dla każdego żądania. Ponowne użycie skutkuje wydajniej bardzo wydajny transakcji protokołu TCP.

#### <a name="connection pooling"></a>Modyfikowanie aplikacji do korzystania z puli połączeń
Można używać połączenia, buforowanie schemat w aplikacji, w której żądania wewnętrznie są rozsyłane ustalony zestaw elementów połączenia (każdego ponowne użycie w przypadku, gdy to możliwe). Ten schemat ogranicza liczbę portów tymczasowych w użyciu i tworzy środowisko bardziej przewidywalne. Ten schemat można również zwiększyć przepływność żądań, umożliwiając wiele jednoczesnych operacji, gdy blokuje przez pojedyncze połączenie w przypadku odpowiedzi operacji.  

Pula połączeń może już istnieć w ramach używanej do tworzenia aplikacji lub ustawienia konfiguracji dla aplikacji. Można łączyć, pula połączeń o ponowne użycie połączenia. Wiele żądań następnie zużyć je stałą, przewidywalną liczbę portów, które mają ten sam docelowy adres IP i port. Żądania, która jest również korzystać z efektywne wykorzystanie transakcji TCP zmniejszenie opóźnień i użycia zasobów. Transakcje UDP mogą również zyskać, ponieważ zarządzanie liczba przepływów UDP mogą z kolei uniknąć warunków spalin oraz zarządzać nimi SNAT użycie portu.

#### <a name="retry logic"></a>Modyfikowanie aplikacji do korzystania z łagodniej logikę ponawiania próby
Gdy [wstępnie przydzielonych portów tymczasowych](#preallocatedports) umożliwiający [osobisty token dostępu](#pat) są aplikacji lub został wyczerpany wystąpią błędy, wymuś agresywne lub atak ponawia próbę bez zanikania i wycofywania logiki spowodować wyczerpanie wystąpienia lub zachować. Można zmniejszyć zapotrzebowanie na portów tymczasowych za pomocą łagodniej logikę ponawiania próby. 

Porty efemeryczne ma 4-minutowy limit czasu bezczynności (nie można dostosować). W przypadku zbyt agresywne ponowne próby wyczerpanie ma możliwość wyczyszczenia samodzielnie. W związku z tym biorąc pod uwagę sposób — i jak często — aplikacja ponawia próbę transakcji jest kluczową częścią projektu.

#### <a name="assignilpip"></a>Przypisywanie wystąpienia poziomu publicznego adresu IP do każdej maszyny Wirtualnej
Przypisywanie ILPIP zmienia dany scenariusz [wystąpienia poziom publicznego adresu IP do maszyny Wirtualnej](#ilpip). Wszystkie porty efemeryczne publicznego adresu IP, które są używane dla każdej maszyny Wirtualnej są dostępne dla maszyny Wirtualnej. (W przeciwieństwie do scenariuszy gdzie portów tymczasowych z publicznym adresem IP są udostępniane wszystkie maszyny wirtualne skojarzone z odpowiednim wdrożeniem). Brak kompromisów należy wziąć pod uwagę, takie jak potencjalny wpływ umieszczania na białej liście dużej liczby poszczególnych adresów IP.

>[!NOTE] 
>Ta opcja nie jest dostępne dla ról procesów roboczych w sieci web.

### <a name="idletimeout"></a>Użyj keepalives, aby zresetować wychodzącego limitu czasu bezczynności

Połączenia wychodzące ma 4-minutowy limit czasu bezczynności. Limit czasu nie jest zmieniane. Jednak można użyć transportu (na przykład keepalives TCP) lub warstwy aplikacji keepalives odświeżanie bezczynności przepływu i zresetować ten limit czasu bezczynności, jeśli to konieczne.  Skontaktuj się z dostawcą dowolnego oprogramowania w pakiecie na tego, czy ta funkcja jest obsługiwana lub jak je włączyć.  Zazwyczaj tylko po jednej stronie musi wygenerować keepalives zresetować limitu czasu bezczynności. 

## <a name="discoveroutbound"></a>Odnajdywania publicznego adresu IP, który korzysta z maszyny Wirtualnej
Istnieje wiele sposobów, aby określić źródłowy publiczny adres IP połączenia wychodzącego. OpenDNS zapewnia usługę, który można wyświetlić publiczny adres IP swojej maszyny wirtualnej. 

Za pomocą polecenia nslookup, możesz wysłać zapytanie DNS dotyczące myip.opendns.com nazwa OpenDNS program rozpoznawania nazw. Usługa zwraca źródłowy adres IP, który został użyty do wysłania zapytania. Po uruchomieniu następującego zapytania z maszyny Wirtualnej, odpowiedź jest publiczny adres IP używany dla tej maszyny Wirtualnej:

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [modułu równoważenia obciążenia](load-balancer-overview.md) używane w przypadku wdrożeń usługi Resource Manager.
- Dowiedz się więcej o [połączeń wychodzących](load-balancer-outbound-connections.md) scenariuszy dostępne w przypadku wdrożeń usługi Resource Manager.
