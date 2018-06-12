---
title: Omówienie usługi Azure Standard modułu równoważenia obciążenia | Dokumentacja firmy Microsoft
description: Omówienie funkcji Azure standardowe usługi równoważenia obciążenia
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2018
ms.author: kumud
ms.openlocfilehash: 20897137c617ddf9a33a8f4966bcd7e30ac7c60c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261937"
---
# <a name="azure-load-balancer-standard-overview"></a>Omówienie usługi Azure Standard modułu równoważenia obciążenia

Moduł równoważenia obciążenia Azure umożliwia skalowanie aplikacji i utworzyć wysoką dostępność usług. Usługi równoważenia obciążenia można używać dla ruchu przychodzącego, jak również wychodzącego scenariuszy i zapewnia małe opóźnienia, wysokiej przepływności i skalowanie do milionów przepływów dla wszystkich aplikacji TCP i UDP. 

Ten artykuł koncentruje się na standardowy moduł równoważenia obciążenia.  Aby uzyskać bardziej ogólne dla usługi równoważenia obciążenia Azure, zapoznaj się [Omówienie usługi równoważenia obciążenia](load-balancer-overview.md) również.

## <a name="what-is-standard-load-balancer"></a>Co to jest standardowe usługi równoważenia obciążenia?

Standardowa usługa równoważenia obciążenia jest nowym produktem modułu równoważenia obciążenia dla aplikacji, wszystkie protokoły TCP i UDP z funkcji rozszerzonej i bardziej szczegółowego ustawić przez podstawowe usługi równoważenia obciążenia.  Gdy istnieje wiele podobieństw, należy zapoznać się z różnicami w sposób opisany w tym artykule.

Standardowa usługa równoważenia obciążenia można użyć jako publiczny lub wewnętrzny moduł równoważenia obciążenia. I maszyny wirtualnej można podłączyć do publicznego i jednego zasobu wewnętrznego modułu równoważenia obciążenia.

Funkcje zasobów usługi równoważenia obciążenia zawsze są wyrażane jako frontonu, regułę, sondy kondycji i definicji puli wewnętrznej bazy danych.  Zasób może zawierać wiele reguł. Maszyny wirtualne można umieścić w puli zaplecza, określając puli wewnętrznej bazy danych z maszyny wirtualnej karty Sieciowej zasobu.  Ten parametr jest przekazywany przez profil sieci i rozszerzony, gdy za pomocą zestawów skali maszyny wirtualnej.

Jeden z kluczowych aspektów jest zakresem sieci wirtualnej dla zasobu.  Podstawowa usługa równoważenia obciążenia istnieje w zakresie zestawu dostępności, standardowe usługi równoważenia obciążenia jest w pełni zintegrowana z zakresem sieci wirtualnej i zastosować wszystkie koncepcji sieci wirtualnych.

Zasoby usługi równoważenia obciążenia to obiekty w ramach których mogą express, jak program jego wielodostępnej infrastrukturze do osiągnięcia scenariusza, który chcesz utworzyć w usłudze Azure.  Nie ma żadnej bezpośredniej zależności między zasobami usługi równoważenia obciążenia i rzeczywistego infrastruktury. Tworzenie modułu równoważenia obciążenia nie tworzy wystąpienie, jest zawsze dostępna pojemność i istnieją nie uruchamiania lub skalowanie opóźnienia wziąć pod uwagę. 

>[!NOTE]
> Platforma Azure udostępnia mechanizm równoważenia rozwiązań dla scenariuszy pełni zarządzana obciążenia.  Jeśli szukasz dla zakończenia połączenia TLS ("odciążanie protokołu SSL") lub na przetwarzanie warstwy aplikacji żądań HTTP i HTTPS, przejrzyj [brama aplikacji w](../application-gateway/application-gateway-introduction.md).  Jeśli szukasz DNS globalnego równoważenia obciążenia, przejrzyj [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Scenariuszy end-to-end mogą korzystać z łączenia tych rozwiązań, zgodnie z potrzebami.

## <a name="why-use-standard-load-balancer"></a>Dlaczego warto używać usługi równoważenia obciążenia standardowego?

Usługa Load Balancer w warstwie Standardowa pozwala skalować aplikacje i zapewniać wysoką dostępność zarówno wdrożeniom o małej skali, jak i dużym oraz złożonym architekturom obejmującym wiele stref.

Przejrzyj tabelę poniżej, aby zapoznać się z omówieniem różnice między standardowe usługi równoważenia obciążenia i podstawowa usługi równoważenia obciążenia:

>[!NOTE]
> Nowe projekty należy rozważyć użycie standardowego modułu równoważenia obciążenia. 

| | Standardowy SKU | Podstawowy SKU |
| --- | --- | --- |
| Rozmiar puli wewnętrznej bazy danych | maksymalnie 1000 wystąpień | do 100 wystąpień |
| Punkty końcowe puli wewnętrznej bazy danych | Żadnej maszyny wirtualnej w jednej sieci wirtualnej, tym blend maszyn wirtualnych, zestawów dostępności zestawach skali maszyn wirtualnych. | Ustaw maszyn wirtualnych w skali dostępności pojedynczy zestaw lub maszyny wirtualnej |
| Strefy dostępności | Nadmiarowe strefy i zonal frontends dla ruchu przychodzącego i awarie strefy, równoważenie obciążenia sieciowego stref między mapowania przepływów wychodzących, wychodzący | / |
| Diagnostyka | Azure Monitor wielowymiarowych metryk, takich jak bajt i liczniki pakietów, kondycji sondowania stanu, próby połączenia (TCP SYN), kondycja połączenia wychodzącego (SNAT udane i nieudane przepływy), pomiarów płaszczyzny aktywnych danych | Azure Log Analytics publicznego równoważenia obciążenia, alert wyczerpania SNAT, liczba kondycji puli wewnętrznej bazy danych |
| HA portów | Wewnętrzny moduł równoważenia obciążenia | / |
| Bezpieczeństwo domyślne | domyślne zamknięty dla publicznych adresów IP i usługi równoważenia obciążenia punktów końcowych i grupy zabezpieczeń sieci musi być używany do jawnie dozwolone w przypadku ruchu przepływu | domyślny otwarty, grupy zabezpieczeń sieci opcjonalne |
| Połączenia wychodzące | Wiele frontends z każdej reguły zrezygnować. Scenariusz wychodzących _musi_ jawnie można utworzyć maszyny wirtualnej można było użyć łączność wychodząca.  [Punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) bez połączenia wychodzącego jest osiągalna, a nie wchodzą w skład dane przetworzone.  Publicznych adresów IP, takich jak usługi Azure PaaS nie jest dostępna jako punktów końcowych usługi sieci wirtualnej, musi być osiągnięta poprzez łączność wychodząca i count do przetwarzania danych. Połączenia wychodzące przez domyślny SNAT wyłącznie do wewnętrznego modułu równoważenia obciążenia działa jako maszyna wirtualna, nie są dostępne. Wychodzące programowania SNAT jest protokołu transportowego określonej na podstawie protokołu reguły równoważenia obciążenia dla ruchu przychodzącego. | Fronton jeden losowo wybrany, gdy istnieją frontends wiele.  Gdy tylko wewnętrzny moduł równoważenia obciążenia działa jako maszynę wirtualną, używana jest domyślna SNAT. |
| Wiele frontonów | Przychodzące i wychodzące | Tylko transfer przychodzący |
| Operacje zarządzania | Większość operacji < 30 sekund | 60-90 sekund typowe |
| Umowa SLA | 99,99% dla ścieżki danych z maszynami wirtualnymi na dwóch dobrej kondycji | Niejawne w umowie SLA maszyny Wirtualnej | 
| Cennik | Na podstawie liczby reguł, dane przetworzone ruchu przychodzącego lub wychodzącego skojarzony z zasobem  | Bez dodatkowych opłat |

Przegląd [usługi limity dla usługi równoważenia obciążenia](https://aka.ms/lblimits), jak również [cennik](https://aka.ms/lbpricing), i [SLA](https://aka.ms/lbsla).


### <a name="backend"></a>Puli wewnętrznej bazy danych

Rozwija standardowe pul zaplecza modułu równoważenia obciążenia do dowolnego zasobu maszyny wirtualnej w sieci wirtualnej.  Nazwa może zawierać maksymalnie 1000 wystąpień wewnętrznej bazy danych.  Wystąpienie wewnętrznej bazy danych jest konfiguracja adresu IP, która jest właściwością zasobów kart interfejsu Sieciowego.

Puli zaplecza może zawierać autonomicznych maszyn wirtualnych, zestawów dostępności lub zestawy skalowania maszyny wirtualnej.  Można tworzyć zasoby w puli zaplecza. Możesz łączyć maksymalnie 150 zasobów w puli zaplecza dla zasobów usługi równoważenia obciążenia.

Podczas określania, jak zaprojektować pulę zaplecza, można zaprojektować dla o najmniejszej liczby indywidualnych zaplecza puli zasobów, aby jeszcze bardziej zoptymalizować czas trwania operacji zarządzania.  Nie ma różnic w wydajności płaszczyzna danych lub skali.

## <a name="az"></a>Dostępność strefy

Standardowa usługa równoważenia obciążenia obsługuje dodatkowe możliwości w regionach, w której dostępność strefy są dostępne.  Te funkcje są przyrostowe do wszystkich standardowych modułu równoważenia obciążenia zapewnia.  Konfiguracje dostępność strefy są dostępne dla publicznych i wewnętrznych standardowe usługi równoważenia obciążenia.

Inne niż zonal frontends stają się strefowo nadmiarowy domyślnie po wdrożeniu w regionie ze strefami dostępności.   Strefowo nadmiarowy frontonu przeżyje błąd stref i obsługiwanej przez dedykowanej infrastruktury we wszystkich strefach jednocześnie. 

Ponadto można zagwarantować frontonu do określonej strefy. Zonal frontonu udostępnia obecność odpowiedniej strefy i jest obsługiwana tylko przez dedykowanej infrastruktury w jednej strefie.

Równoważenie obciążenia między strefy jest dostępna dla puli zaplecza i dowolnego zasobu maszyny wirtualnej w sieci wirtualnej może być częścią puli wewnętrznej bazy danych.

Przegląd [szczegółowe omówienie stref dostępności dotyczące możliwości](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a> Diagnostyka

Standardowy moduł równoważenia obciążenia zapewnia metryki wielowymiarowej za pośrednictwem Monitora Azure.  Te metryki można filtrować, grupowane i podzielone określonego wymiaru.  Udostępniają one bieżące i historyczne informacje o wydajności i kondycji usługi.  Kondycja zasobu jest również obsługiwany.  Poniżej przedstawiono krótkie omówienie diagnostyki obsługiwane:

| Metryka | Opis |
| --- | --- |
| Dostępność adresu VIP | Standardowe usługi równoważenia obciążenia stale korzysta ze ścieżki danych z w obrębie regionu frontonu aż do stosu SDN, który obsługuje maszyny Wirtualnej modułu równoważenia obciążenia. Tak długo, jak utrzymać dobrej kondycji wystąpień, pomiar następuje taką samą ścieżkę aplikacji z równoważeniem obciążenia ruchu. Ścieżka danych, który jest używany przez klientów również jest weryfikowana. Miara jest niewidoczny dla aplikacji i nie koliduje z innych operacji.|
| Dostępność DIP | Standardowe usługi równoważenia obciążenia używa rozproszonej kondycję sondowanie usługi, który monitoruje kondycję punkt końcowy aplikacji zgodnie z ustawieniami konfiguracji. Ta metryka zapewnia agregacji lub na punkt końcowy filtrowane widoku każdego punktu końcowego poszczególnych wystąpień w usłudze równoważenia obciążenia w puli.  Widać, jak usługa równoważenia obciążenia widoków kondycji aplikacji wskazywany przez konfigurację sondy kondycji.
| SYN pakietów | Standardowe usługi równoważenia obciążenia nie przerwanie połączenia TCP lub interakcji z przepływów pakietów TCP lub UDP. Przepływy i ich uzgodnienia są zawsze między serwerem źródłowym a wystąpienia maszyny Wirtualnej. Aby lepiej rozwiązać scenariuszy protokołu TCP, możesz wprowadzić użycie SYN liczniki pakietów, aby zrozumieć, jak wiele połączeń TCP prób. Metryka raporty o liczbie pakietów TCP SYN, które zostały odebrane.|
| SNAT połączenia | Standardowe usługi równoważenia obciążenia zgłasza liczbę przepływów wychodzących, które są masqueraded do publicznego adresu IP frontonu. Porty SNAT są zużywalnymi zasobami. Ta metryka może przedstawiać jak silnie aplikacji jest oparte na SNAT dla zdalnych przepływów wychodzących.  Liczniki dla udane i nieudane przepływów wychodzących SNAT są raportowane i może służyć do rozwiązywania oraz zrozumienie kondycji przepływów wychodzących.|
| Liczniki bajtów | Standardowe usługi równoważenia obciążenia zgłosił danych przetwarzanych na frontonu.|
| Liczniki pakietów | Standardowe usługi równoważenia obciążenia raporty pakietów przetwarzanych na frontonu.|

Przegląd [szczegółowe omówienie standardowe Diagnostyka usługi równoważenia obciążenia](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>HA portów

Standardowa usługa równoważenia obciążenia obsługuje nowy typ reguły.  

Można skonfigurować reguły, aby udostępnić skali Twojej aplikacji i można zdecydowanie niezawodnej równoważenia obciążenia. Kiedy używać reguły, standardowy moduł równoważenia obciążenia równoważenia obciążenia HA porty zapewni na równoważenia na każdym porcie tymczasowych adresu IP frontonu wewnętrzny standardowy moduł równoważenia obciążenia dla obciążenia przepływu.  Funkcja jest przydatna w innych sytuacjach, w którym jest niepraktyczne lub niepożądane, aby określić poszczególnych portów.

Reguły równoważenia obciążenia HA porty służy do tworzenia aktywny pasywny "lub" aktywny aktywny n + 1 scenariusze dla sieci wirtualnych urządzeń i dowolnej aplikacji, która wymaga duże zakresy portów przychodzących.  Badania kondycji może służyć do określenia, które zapleczy powinni otrzymywać nowych przepływów.  Grupa zabezpieczeń sieci umożliwia emulować scenariusza zakresu portów.

>[!IMPORTANT]
> Jeśli planujesz użyć urządzenie sieci wirtualnej, skontaktuj się z dostawcą, aby uzyskać wskazówki dotyczące tego, czy ich produktu był testowany z portami HA i postępuj zgodnie z ich dokładne wskazówki dotyczące implementacji. 

Przegląd [szczegółowe omówienie HA porty](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Bezpieczeństwo domyślne

Standardowa usługa równoważenia obciążenia jest w pełni dołączać do sieci wirtualnej.  Sieć wirtualna jest w sieci prywatnej, zamknięte.  Ponieważ standardowe usługi równoważenia obciążenia i standardowe publiczne adresy IP pozwalają tej sieci wirtualnej jako dostępne spoza sieci wirtualnej, te zasoby teraz domyślnie, chyba że je otworzyć. Oznacza to, aby jawnie zezwolić na teraz są używane grupy zabezpieczeń sieci (NSG) i dozwolonych dozwolone ruchu.  Można tworzyć z całym wirtualnego centrum danych i podjąć decyzję za pośrednictwem grupy NSG, co i kiedy powinny być dostępne.  Jeśli nie masz grupy NSG podsieci lub kartę interfejsu Sieciowego zasobu maszyny wirtualnej, ruch nie jest dozwolony na osiągnięcie tego zasobu.

Aby dowiedzieć się więcej na temat grup NSG i zastosować je dla danego scenariusza, zobacz [grup zabezpieczeń sieci](../virtual-network/security-overview.md).

### <a name="outbound"></a> Połączenia wychodzące

Moduł równoważenia obciążenia obsługuje scenariusze dla ruchu przychodzącego i wychodzącego.  Standardowa usługa równoważenia obciążenia różni się znacząco dla podstawowe usługi równoważenia obciążenia w odniesieniu do połączeń wychodzących.

Źródło sieci adresu tłumaczenia (SNAT) są używane do mapowania wewnętrzne, prywatne adresy IP w sieci wirtualnej na publiczne adresy IP na frontends modułu równoważenia obciążenia.

Standardowe usługi równoważenia obciążenia wprowadzono nowy algorytm dla [bezawaryjną, skalowalną oraz przewidywalną algorytmu SNAT](load-balancer-outbound-connections.md#snat) i włącza nowe możliwości, usuwa niejednoznaczności i konfiguracje jawne wymusza raczej po stronie efekty. Te zmiany są niezbędne w celu umożliwienia nowych funkcji wyłaniać się. 

Są to kluczowe zasady do zapamiętania podczas pracy ze standardowego modułu równoważenia obciążenia:

- uzupełnianie reguły dyski zasobów usługi równoważenia obciążenia.  wszystkie programowania w języku Azure pochodzi z jej konfiguracji.
- gdy dostępnych wiele frontends frontends wszystkich są używane i liczbę dostępnych portów SNAT mnoży każdego serwera sieci Web
- można wybrać i kontrolki, jeżeli nie chcesz dla konkretnego serwera sieci Web do użycia dla połączeń wychodzących.
- wychodzące scenariusze są jawne i łączność wychodząca nie istnieje, dopóki nie został określony.
- jak zaplanowano SNAT wnioskować o reguły równoważenia obciążenia. Reguły równoważenia obciążenia jest specyficzna dla protokołu. SNAT jest specyficzna dla protokołu i konfiguracji należy uwzględnić to zamiast tworzyć efektem.

#### <a name="multiple-frontends"></a>Wiele frontonów
Jeśli chcesz więcej SNAT portów, ponieważ są oczekiwane lub już występuje duże zapotrzebowanie na połączenia wychodzące, możesz także dodać przyrostowe SNAT portu spisu przez skonfigurowanie dodatkowych frontends, reguł i pul zaplecza w tej samej maszyny wirtualnej zasoby.

#### <a name="control-which-frontend-is-used-for-outbound"></a>Formant frontonu, które jest używany do ruchu wychodzącego
Jeśli chcesz ograniczyć połączenia wychodzące tylko pochodzi z adresu IP określonego serwera sieci Web, opcjonalnie można wyłączyć SNAT wychodzących reguł, który wyrazi wychodzącego mapowania.

#### <a name="control-outbound-connectivity"></a>Łączność wychodząca formantu
Standardowa usługa równoważenia obciążenia istnieje w kontekście sieci wirtualnej.  Sieć wirtualna jest izolowanym sieci prywatnej.  Jeśli nie istnieje skojarzenie z publicznym adresem IP, łączności publicznej w zakresie jest niedozwolone.  Możesz uzyskiwać dostęp [punktów końcowych usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) ponieważ są one oraz lokalnego do sieci wirtualnej.  Jeśli chcesz ustanowić łączność wychodząca docelowego spoza sieci wirtualnej, dostępne są dwie opcje:
- Przypisz adres publiczny adres IP na poziomie wystąpienia zasobu maszyny wirtualnej w postaci standardowy SKU publicznego adresu IP lub
- Umieść zasobu maszyny wirtualnej w puli zaplecza publicznego standardowe usługi równoważenia obciążenia.

Zarówno umożliwi łączność wychodząca z sieci wirtualnej do poza siecią wirtualną. 

Jeśli użytkownik _tylko_ ma wewnętrzny standardowy moduł równoważenia obciążenia skojarzone z pulą wewnętrznej bazy danych, w którym znajduje się zasób maszyny wirtualnej, na komputerze wirtualnym można tylko osiągnąć zasobów sieci wirtualnej i [usługi sieci wirtualnej Punkty końcowe](../virtual-network/virtual-network-service-endpoints-overview.md).  Można wykonać kroki opisane w poprzednim akapicie, aby utworzyć łączność wychodząca.

Łączność wychodząca nie są skojarzone z pozostaje standardowe jednostki SKU jako przed zasobu maszyny wirtualnej.

Przegląd [szczegółowe omówienie połączeń wychodzących](load-balancer-outbound-connections.md).

### <a name="multife"></a>Wiele frontends
Moduł równoważenia obciążenia obsługuje wiele reguł z wielu frontends.  Standardowe usługi równoważenia obciążenia rozszerza to scenariuszy ruchu wychodzącego.  Wychodzące scenariusze są zasadniczo odwrotność reguły równoważenia obciążenia dla ruchu przychodzącego.  Reguły funkcji również równoważenia obciążenia dla ruchu przychodzącego tworzy skojarzenia połączeń wychodzących. Standardowy moduł równoważenia obciążenia używa frontends wszystkie skojarzone z zasobu maszyny wirtualnej za pomocą reguły równoważenia obciążenia.  Ponadto parametru na temat funkcji równoważenia obciążenia reguły i pozwala pominąć reguły na potrzeby łączność wychodząca, który umożliwia wybranie określonych frontends tym żadna z równoważeniem obciążenia.

Dla porównania podstawowe usługi równoważenia obciążenia losowo wybiera pojedynczego serwera sieci Web i nie ma możliwość kontrolowania, które z nich został wybrany.

Przegląd [szczegółowe omówienie połączeń wychodzących](load-balancer-outbound-connections.md).

### <a name="operations"></a> Operacje zarządzania

Standardowe zasoby usługi równoważenia obciążenia istnieje na to całkowicie nowa platforma infrastruktury.  Pozwala to znacznie szybsze operacje zarządzania standardowe jednostki SKU i czas zakończenia jest zwykle mniej niż 30 sekund na standardowy SKU zasobów.  Należy pamiętać, że pule zaplecza zwiększenia rozmiaru, czas trwania wymaganych do wewnętrznej bazy danych puli zmienia się również zwiększenia.

Można modyfikować zasobów standardowe usługi równoważenia obciążenia i przenieść standardowe publiczny adres IP z jednej maszyny wirtualnej do innego szybciej.

## <a name="migration-between-skus"></a>Migracja między jednostki SKU

Jednostki SKU nie jest modyfikowalna. Wykonaj kroki opisane w tej sekcji, aby przenieść od jednego zasobu jednostki SKU.

>[!IMPORTANT]
>Przejrzyj ten dokument w całości, aby poznać różnice między jednostki SKU i należy dokładnie zbadać danego scenariusza.  Należy wprowadzić dodatkowe zmiany, aby były wyrównane danego scenariusza.

### <a name="migrate-from-basic-to-standard-sku"></a>Migracja z podstawowego do wersji Standard

1. Tworzenie nowego zasobu standardowe (Usługa równoważenia obciążenia i publiczne adresy IP, w razie potrzeby). Ponownie utworzyć reguły i definicje sondowania.

2. Utworzyć lub zaktualizować istniejący NSG na karcie Sieciowej lub podsieci do dozwolonego ruchu o zrównoważonym obciążeniu, sondy, a także cały ruch, który chcesz zezwolić.

3. Usuń zasoby podstawowy SKU (Usługa równoważenia obciążenia i publiczne adresy IP, jeśli ma zastosowanie) z wszystkich wystąpień maszyn wirtualnych. Należy również usunięcie wszystkich wystąpień maszyn wirtualnych zestawu dostępności.

4. Dołącz wszystkie wystąpienia maszyny Wirtualnej do nowych zasobów standardowy SKU.

### <a name="migrate-from-standard-to-basic-sku"></a>Migracja z Standard do podstawowy SKU

1. Tworzenie nowego zasobu podstawowego (Usługa równoważenia obciążenia i publiczne adresy IP, w razie potrzeby). Ponownie utworzyć reguły i definicje sondowania. 

2. Usuń zasoby standardowy SKU (Usługa równoważenia obciążenia i publiczne adresy IP, jeśli ma zastosowanie) z wszystkich wystąpień maszyn wirtualnych. Należy również usunięcie wszystkich wystąpień maszyn wirtualnych zestawu dostępności.

3. Dołącz wszystkie wystąpienia maszyny Wirtualnej do nowych zasobów podstawowy SKU.

>[!IMPORTANT]
>
>Istnieją pewne ograniczenia dotyczące użycia Basic i Standard jednostki SKU.
>
>HA portów i informacji diagnostycznych z wersji Standard są dostępne tylko w wersji Standard. Nie można migracji z wersji Standard na podstawowy SKU i również zachować te funkcje.
>
>Zarówno wersji Standard, jak i Basic ma liczbę różnic w sposób opisany w tym artykule.  Upewnij się, zrozumieć i Przygotuj się do nich.
>
>Dla zasobów usługi równoważenia obciążenia i publiczny adres IP muszą być używane dopasowanie jednostki SKU. Nie może mieć kombinację zasobów podstawowy SKU i wersji Standard. Nie można dołączyć autonomicznych maszyn wirtualnych, maszyn wirtualnych w zasobie zestawu dostępności lub zasobów zestawu skalowania maszyn wirtualnych jednocześnie do obu jednostek SKU.

## <a name="region-availability"></a>Dostępność w danym regionie

Standardowe usługi równoważenia obciążenia jest obecnie dostępny we wszystkich regionach chmury publicznej.

## <a name="sla"></a>Umowa SLA

Standardowe moduły równoważenia obciążenia są dostępne z SLA 99,99%.  Przegląd [standardowe SLA usługi równoważenia obciążenia](https://aka.ms/lbsla) szczegółowe informacje.

## <a name="pricing"></a>Cennik

Standardowy moduł równoważenia obciążenia jest produktem obciążona, na podstawie liczby reguł równoważenia obciążenia skonfigurowane i wszystkie dane przychodzące i wychodzące przetworzone. Standardowe informacje o cenach równoważenia obciążenia, można znaleźć [cennik usługi równoważenia obciążenia](https://aka.ms/lbpricing) strony.

## <a name="limitations"></a>Ograniczenia

- Jednostki SKU nie jest modyfikowalna. Nie możesz zmienić numer istniejącego zasobu.
- Zasób autonomicznej maszyny wirtualnej zestawu dostępności zasobów lub zasobu zestawu skali maszyny wirtualnej może się odwoływać jednego identyfikatora jednostki Magazynowej, nigdy nie oba.
- Reguła modułu równoważenia obciążenia nie może obejmować dwie sieci wirtualne.  Frontends i ich wystąpienia pokrewne wewnętrznej bazy danych musi znajdować się w tej samej sieci wirtualnej.  
- Frontends usługi równoważenia obciążenia nie są dostępne w globalnej sieci wirtualnej komunikacji równorzędnej.
- [Subskrypcja operacje są przenoszone](../azure-resource-manager/resource-group-move-resources.md) nie są obsługiwane dla standardowych LB jednostki SKU i PIP zasobów.
- Role pracownika w sieci Web bez sieci wirtualnej i innych usług platformy Microsoft mogą stać się niedostępne po tylko wewnętrzny standardowy moduł równoważenia obciążenia jest używany z powodu efekt uboczny z jak funkcja usług pre-VNet usług i innych platform. Należy nie tylko na tych jako odpowiednie usługi siebie lub podstawowych platform mogą ulec zmianie bez powiadomienia. Należy zawsze zakładać, należy utworzyć [łączność wychodząca](load-balancer-outbound-connections.md) jawnie, jeśli jest to konieczne, korzystając z wewnętrznych standardowy moduł równoważenia obciążenia tylko.
- Moduł równoważenia obciążenia jest produktem TCP lub UDP do równoważenia obciążenia i portu przekazywania tych określonych protokołów IP.  Reguły równoważenia obciążenia i reguły NAT ruchu przychodzącego są obsługiwane w przypadku protokołu TCP i UDP i nie są obsługiwane przez inne protokoły IP, w tym protokołu ICMP. Moduł równoważenia obciążenia nie zakończy, odpowiadać lub inny sposób interakcji z ładunku przepływu UDP lub TCP. Nie jest serwer proxy. Sprawdzeniu poprawności łączności frontonu musi mieć miejsce w paśmie z tego samego protokołu, które są używane w równoważenia lub przychodzącej reguły NAT obciążenia (TCP lub UDP) _i_ co najmniej jeden z maszyn wirtualnych należy wygenerować odpowiedzi do klienta Aby wyświetlić odpowiedzi z frontonu.  Nie odbiera odpowiedź wewnątrzpasmowe z frontonu modułu równoważenia obciążenia oznacza, że żadne maszyny wirtualne zostały mogą odpowiadać.  Nie jest możliwe do interakcji z modułem równoważenia obciążenia frontonu bez mogą odpowiadać maszyny wirtualnej.  Dotyczy to również połączenia wychodzące gdzie [maskowaniu portu SNAT](load-balancer-outbound-connections.md#snat) jest obsługiwana tylko w przypadku protokołu TCP i UDP; żadnych innych protokołów IP ICMP w tym również zakończy się niepowodzeniem.  Przypisz poziomie wystąpienia publicznego adresu IP do ograniczenia.
- W przeciwieństwie do publicznej usługi równoważenia obciążenia, która zapewnia [połączeń wychodzących](load-balancer-outbound-connections.md) podczas przejścia z prywatnych adresów IP w sieci wirtualnej na publiczne adresy IP, wewnętrzne moduły równoważenia obciążenia nie tłumaczenia wychodzącego pochodzi połączenia z frontonu wewnętrznego modułu równoważenia obciążenia jako zarówno znajdują się w prywatnej przestrzeni adresów IP.  Dzięki temu można uniknąć potencjalnych wyczerpania SNAT wewnątrz Unikatowy wewnętrzny przestrzeń adresową gdzie tłumaczenia nie jest wymagana.  Efektem ubocznym jest to, że jeśli przepływu wychodzącego z maszyn wirtualnych w puli zaplecza prób przepływu do frontonu wewnętrznego modułu równoważenia obciążenia w puli, która znajduje się _i_ jest zamapowana do samej siebie, nie są zgodne oba etapy przepływu i przepływ zakończy się niepowodzeniem. .  Jeśli przepływ mapują wróć do tej samej maszyny Wirtualnej w puli zaplecza, który utworzony przepływu frontonu, przepływ zostanie wykonana pomyślnie.   Przepływ mapy do samej siebie przepływu wychodzącego prawdopodobnie wynikają z maszyny Wirtualnej z frontonu, a odpowiedni przepływu ruchu przychodzącego wydaje się pochodzą z maszyny Wirtualnej do samej siebie. Z punktu widzenia systemu operacyjnego gościa na maszynie wirtualnej nie zgadzają się części ruchu przychodzącego i wychodzącego z takim samym przepływie. Stosu TCP nie rozpozna tych połowy takim samym przepływie jako część tego samego przepływu zgodnie z serwera źródłowego i docelowego nie są zgodne.  Przepływ, mapy do innych maszyn wirtualnych w puli zaplecza, połowy przepływ będą zgodne, a maszyna wirtualna może pomyślnie odpowiedzieć przepływu.  Objaw dla tego scenariusza jest sporadyczne połączenia przekroczeń limitu czasu. Istnieje kilka typowych obejścia niezawodny sposób realizacji tego scenariusza (pochodzące są przesyłane z puli zaplecza zaplecza pul odpowiednich wewnętrzny moduł równoważenia obciążenia frontonu) który obejmuje albo wstawiania za obciążenia wewnętrznego serwera proxy innych firm Moduł równoważenia lub [przy użyciu reguł stylu DSR](load-balancer-multivip-overview.md).  Publiczny moduł równoważenia obciążenia można użyć do ograniczenia, wynikowy scenariusz jest podatna na [wyczerpania SNAT](load-balancer-outbound-connections.md#snat) i należy unikać, chyba że dokładnie zarządzane.

## <a name="next-steps"></a>Kolejne kroki

- Informacje o używaniu [standardowego modułu równoważenia obciążenia i dostępności stref](load-balancer-standard-availability-zones.md)
- Dowiedz się więcej o [stref dostępności](../availability-zones/az-overview.md).
- Dowiedz się więcej o [Diagnostyka usługi równoważenia obciążenia standardowego](load-balancer-standard-diagnostics.md).
- Dowiedz się więcej o [obsługiwane metryki wielowymiarowej](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkloadbalancers) diagnostyki w [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).
- Informacje o używaniu [modułu równoważenia obciążenia dla połączeń wychodzących](load-balancer-outbound-connections.md)
- Dowiedz się więcej o [standardowy moduł równoważenia obciążenia z reguły równoważenia obciążenia HA portów](load-balancer-ha-ports-overview.md)
- Informacje o używaniu [modułu równoważenia obciążenia z wieloma Frontends](load-balancer-multivip-overview.md)
- Dowiedz się więcej o [sieci wirtualnych](../virtual-network/virtual-networks-overview.md).
- Dowiedz się więcej o [grup zabezpieczeń sieci](../virtual-network/security-overview.md).
- Dowiedz się więcej o [punktów końcowych usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md)
- Dowiedz się więcej o niektóre inne kluczowe [możliwości w zakresie obsługi](../networking/networking-overview.md) na platformie Azure.
- Dowiedz się więcej o [modułu równoważenia obciążenia](load-balancer-overview.md).
