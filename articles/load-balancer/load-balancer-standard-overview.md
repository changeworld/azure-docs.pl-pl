---
title: Co to jest platforma Azure usługa Load Balancer w warstwie Standardowa?
titleSuffix: Azure Load Balancer
description: Korzystając z tej ścieżki szkoleniowej, Rozpocznij pracę z omówieniem funkcji usługa Load Balancer w warstwie Standardowa platformy Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: allensu
ms.openlocfilehash: 3b6a16436b2719d1571f5d5a3c16711a9100b75d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894427"
---
# <a name="azure-standard-load-balancer-overview"></a>Omówienie usługi Azure usługa Load Balancer w warstwie Standardowa

Azure Load Balancer umożliwia skalowanie aplikacji i tworzenie wysokiej dostępności dla usług. Load Balancer można używać na potrzeby ruchu przychodzącego, a także scenariuszy wychodzących i zapewniania małych opóźnień, dużej przepływności i skalowania do milionów przepływów dla wszystkich aplikacji TCP i UDP. 

Ten artykuł koncentruje się na usługa Load Balancer w warstwie Standardowa.  Aby zapoznać się z bardziej ogólnym omówieniem Azure Load Balancer, zapoznaj się z tematem [Load Balancer Omówienie](load-balancer-overview.md) .

## <a name="what-is-standard-load-balancer"></a>Co to jest usługa Load Balancer w warstwie Standardowa?

Usługa Load Balancer w warstwie Standardowa to nowy Load Balancer produkt dla wszystkich aplikacji TCP i UDP z rozszerzoną i bardziej szczegółowym zestawem funkcji dla podstawowych Load Balancer.  Chociaż istnieje wiele podobieństw, ważne jest zapoznanie się z różnicami, które opisano w tym artykule.

Usługa Load Balancer w warstwie Standardowa można używać jako Load Balancer publicznego lub wewnętrznego. A maszyna wirtualna może być połączona z jednym publicznym i jednym wewnętrznym zasobem Load Balancer.

Funkcje zasobu Load Balancer są zawsze wyrażone jako fronton, reguła, sonda kondycji i definicja puli zaplecza.  Zasób może zawierać wiele reguł. Maszyny wirtualne można umieścić w puli zaplecza, określając pulę zaplecza z zasobu karty sieciowej maszyny wirtualnej.  Ten parametr jest przesyłany przez profil sieciowy i rozwinięty podczas korzystania z zestawów skalowania maszyn wirtualnych.

Jednym z kluczowych aspektów jest zakres sieci wirtualnej dla zasobu.  Chociaż podstawowa Load Balancer istnieje w zakresie zestawu dostępności, usługa Load Balancer w warstwie Standardowa jest w pełni zintegrowana z zakresem sieci wirtualnej i ma zastosowanie wszystkie pojęcia dotyczące sieci wirtualnej.

Zasoby Load Balancer są obiektami, w których można wypróbować, w jaki sposób platforma Azure powinna programować infrastrukturę z wieloma dzierżawcami, aby osiągnąć scenariusz, który ma zostać utworzony.  Nie ma bezpośredniej relacji między zasobami Load Balancer i rzeczywistą infrastrukturą; utworzenie Load Balancer nie powoduje utworzenia wystąpienia, pojemność jest zawsze dostępna i nie ma opóźnień uruchamiania ani skalowania do uwzględnienia. 

## <a name="why-use-standard-load-balancer"></a>Dlaczego warto używać usługa Load Balancer w warstwie Standardowa?

Usługa Load Balancer w warstwie Standardowa pozwala skalować aplikacje i zapewniać wysoką dostępność zarówno wdrożeniom o małej skali, jak i dużym oraz złożonym architekturom obejmującym wiele stref.

Zapoznaj się z tabelą poniżej, aby zapoznać się z omówieniem różnic między usługa Load Balancer w warstwie Standardowa i Load Balancer Basic:

>[!NOTE]
> Nowe projekty powinny zostać dostosowane do modułu równoważenia obciążenia w warstwie Standardowa. 

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Przejrzyj [limity usługi dla Load Balancer](https://aka.ms/lblimits), a także [ceny](https://aka.ms/lbpricing)i [umowy SLA](https://aka.ms/lbsla).


### <a name="backend"></a>Pula zaplecza

Usługa Load Balancer w warstwie Standardowa pule zaplecza rozszerzają się do dowolnego zasobu maszyny wirtualnej w sieci wirtualnej.  Może zawierać do 1000 wystąpień zaplecza.  Wystąpienie wewnętrznej bazy danych jest konfiguracją IP, która jest właściwością zasobu karty sieciowej.

Pula zaplecza może zawierać autonomiczne maszyny wirtualne, zestawy dostępności lub zestawy skalowania maszyn wirtualnych.  Możesz również mieszać zasoby w puli zaplecza. Można połączyć maksymalnie 150 zasobów w puli zaplecza na Load Balancer zasób.

Biorąc pod uwagę sposób projektowania puli zaplecza, można projektować dla najmniejszej liczby zasobów puli zaplecza, aby dodatkowo zoptymalizować czas trwania operacji zarządzania.  Nie ma różnicy w wydajności lub skali danych.

### <a name="probes"></a>Sondy kondycji
  
Usługa Load Balancer w warstwie Standardowa dodaje obsługę [sond kondycji protokołu HTTPS](load-balancer-custom-probe-overview.md#httpprobe) (sondowanie http z otoką Transport Layer Security (TLS)) w celu dokładnego monitorowania aplikacji https.  

Ponadto, gdy cała [sonda](load-balancer-custom-probe-overview.md#probedown)puli zaplecza nie działa, usługa Load Balancer w warstwie Standardowa zezwala na kontynuowanie wszystkich ustanowionych połączeń TCP. (Podstawowa Load Balancer zakończy wszystkie połączenia TCP ze wszystkimi wystąpieniami).

Szczegółowe informacje znajdują się w [Load Balancer sondy kondycji](load-balancer-custom-probe-overview.md) .

### <a name="az"></a>Strefy dostępności

>[!IMPORTANT]
>Przejrzyj [strefy dostępności](../availability-zones/az-overview.md) Tematy pokrewne, w tym informacje specyficzne dla regionu.

Usługa Load Balancer w warstwie Standardowa obsługuje dodatkowe możliwości w regionach, w których Strefy dostępności są dostępne.  Te funkcje są stopniowo dostępne dla wszystkich usługa Load Balancer w warstwie Standardowa.  Konfiguracje Strefy dostępności są dostępne dla usługa Load Balancer w warstwie Standardowa publicznych i wewnętrznych.

Frontony niebędące strefami są domyślnie strefowo nadmiarowe, gdy są wdrożone w regionie z Strefy dostępności.   Strefa nadmiarowa przeżyje awarię strefy i jest obsługiwana przez dedykowaną infrastrukturę we wszystkich strefach jednocześnie. 

Ponadto możesz zagwarantowanie frontonu dla określonej strefy. Strefa frontonu współużytkuje losy z odpowiednią strefą i jest obsługiwana tylko przez dedykowaną infrastrukturę w jednej strefie.

Równoważenie obciążenia między strefami jest dostępne dla puli zaplecza, a każdy zasób maszyny wirtualnej w sieci wirtualnej może być częścią puli zaplecza.

Przejrzyj [szczegółowe omówienie strefy dostępności związanych z nimi możliwości](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a>Programów

Usługa Load Balancer w warstwie Standardowa udostępnia metryki wielowymiarowe za Azure Monitor.  Te metryki można filtrować, grupować i rozdzielić w danym wymiarze.  Zapewniają one bieżące i historyczne informacje o wydajności i kondycji usługi.  Obsługiwane są również Resource Health.  Poniżej przedstawiono krótkie omówienie obsługiwanej diagnostyki:

| Metryka | Opis |
| --- | --- |
| Dostępność adresu VIP | Usługa Load Balancer w warstwie Standardowa stale wykonuje ścieżkę danych z poziomu regionu do Load Balancer frontonu w celu uzyskania dostępu do stosu SDN obsługującego maszynę wirtualną. Tak długo, jak wystąpienia w dobrej kondycji, pomiar jest zgodny z tą samą ścieżką, co ruch o zrównoważonym obciążeniu aplikacji. Zostanie również sprawdzona ścieżka danych używana przez klientów. Pomiar jest niewidoczny dla aplikacji i nie zakłóca innych operacji.|
| Dostępność DIP | Usługa Load Balancer w warstwie Standardowa używa usługi badania kondycji rozproszonej, która monitoruje kondycję punktu końcowego aplikacji zgodnie z ustawieniami konfiguracji. Ta Metryka zawiera zagregowany lub przefiltrowany do punktu końcowego widok poszczególnych punktów końcowych poszczególnych wystąpień w puli Load Balancer.  Możesz zobaczyć, jak Load Balancer przegląda kondycję aplikacji zgodnie z konfiguracją sondy kondycji.
| Pakiety SYN | Usługa Load Balancer w warstwie Standardowa nie przerywa połączeń TCP ani nie współdziała z przepływami pakietów TCP lub UDP. Przepływy i ich uzgodnienia są zawsze między wystąpieniem źródłowym a maszyną wirtualną. Aby lepiej rozwiązać problemy ze scenariuszami protokołu TCP, można użyć liczników pakietów SYN, aby zrozumieć, ile prób połączenia TCP zostało nawiązane. Metryka zgłasza liczbę odebranych pakietów TCP SYN.|
| Połączenia z przyłączaniem | Usługa Load Balancer w warstwie Standardowa raportuje liczbę przepływów wychodzących, które są zamaskowane do frontonu publicznego adresu IP. Porty przyłączone do adresów sieciowych to zasób exhaustible. Ta Metryka może wskazywać na to, jak silna aplikacja jest zależna od przychodzących przepływów.  Są raportowane liczniki dla zakończonych powodzeniem i zakończonych niepowodzeniem przepływów przychodzących obiektów przeruchowych, które mogą służyć do rozwiązywania problemów i zrozumienia kondycji przepływów wychodzących.|
| Liczniki bajtów | Usługa Load Balancer w warstwie Standardowa raportuje dane przetworzone na fronton.|
| Liczniki pakietów | Usługa Load Balancer w warstwie Standardowa raportuje przetworzone pakiety na fronton.|

Przejrzyj [szczegółowe omówienie diagnostyki usługa Load Balancer w warstwie Standardowa](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>Porty HA

Usługa Load Balancer w warstwie Standardowa obsługuje nowy typ reguły.  

Można skonfigurować reguły równoważenia obciążenia, aby umożliwić skalowanie aplikacji i zapewnić wysoką niezawodność. W przypadku korzystania z reguły równoważenia obciążenia z portami HA usługa Load Balancer w warstwie Standardowa zapewnia Równoważenie obciążenia dla przepływu na każdy port tymczasowych usługa Load Balancer w warstwie Standardowa adres IP frontonu.  Ta funkcja jest przydatna w przypadku innych scenariuszy, w których nie można określić pojedynczych portów.

Reguła równoważenia obciążenia z portami HA umożliwia tworzenie aktywnych lub aktywnych scenariuszy n + 1 dla sieciowych urządzeń wirtualnych i aplikacji, które wymagają dużych zakresów portów przychodzących.  Sondy kondycji można użyć do określenia, które punkty końcowe powinny otrzymywać Nowe przepływy.  Do emulowania scenariusza zakresu portów można użyć sieciowej grupy zabezpieczeń.

>[!IMPORTANT]
> Jeśli planujesz używanie sieciowego urządzenia wirtualnego, skontaktuj się z dostawcą, aby uzyskać wskazówki dotyczące tego, czy ich produkt został przetestowany przy użyciu portów HA, i postępuj zgodnie ze szczegółowymi wskazówkami dotyczącymi implementacji. 

Przejrzyj [szczegółowe omówienie portów ha](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Domyślnie zabezpieczone

Usługa Load Balancer w warstwie Standardowa zostało w pełni dołączone do sieci wirtualnej.  Sieć wirtualna jest siecią prywatną, zamkniętą.  Ze względu na to, że standardowe usługi równoważenia obciążenia i standardowe publiczne adresy IP zostały zaprojektowane tak, aby umożliwić dostęp do tej sieci wirtualnej spoza sieci wirtualnej, te zasoby są teraz domyślnie zamknięte, chyba że zostaną otwarte. Oznacza to, że sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) są teraz używane do jawnego zezwolenia i dozwolonych dozwolonych danych.  Możesz utworzyć całe wirtualne centrum danych i zdecydować się na sieciowej grupy zabezpieczeń, co i kiedy powinna być dostępna.  Jeśli nie masz sieciowej grupy zabezpieczeń w podsieci lub karcie sieciowej zasobu maszyny wirtualnej, ruch nie może nawiązać połączenia z tym zasobem.

Aby dowiedzieć się więcej o sieciowych grup zabezpieczeń i sposobach ich stosowania w danym scenariuszu, zobacz [sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md).

### <a name="outbound"></a>Połączenia wychodzące

Load Balancer obsługuje scenariusze ruchu przychodzącego i wychodzącego.  Usługa Load Balancer w warstwie Standardowa znacznie różni się od podstawowych Load Balancer w odniesieniu do połączeń wychodzących.

Translator adresów sieciowych jest używany do mapowania wewnętrznych prywatnych adresów IP w sieci wirtualnej na publiczne adresy IP na Load Balancer frontonów.

Usługa Load Balancer w warstwie Standardowa wprowadza nowy algorytm [bardziej niezawodnego, skalowalnego i przewidywalnego algorytmu](load-balancer-outbound-connections.md#snat) przedziałów adresów sieciowych oraz umożliwia nowe możliwości, usuwa niejednoznaczność i wymusza jawne konfiguracje, a nie efekty uboczne. Te zmiany są niezbędne, aby umożliwić wypróbowanie nowych funkcji. 

Oto kluczowe założenia podczas pracy z usługa Load Balancer w warstwie Standardowa:

- zakończenie reguły dyskowej dla Load Balancer zasobu.  wszystkie programowanie platformy Azure wynika z jego konfiguracji.
- gdy jest dostępnych wiele frontonów, wszystkie frontony są używane, a każdy fronton mnoży liczbę dostępnych portów.
- Możesz wybrać i kontrolować, czy nie chcesz, aby konkretny fronton był używany do połączeń wychodzących.
- scenariusze wychodzące są jawne, a łączność wychodząca nie istnieje, dopóki nie została określona.
- reguły równoważenia obciążenia wnioskują o sposób zaprogramowania tego mechanizmu. Reguły równoważenia obciążenia są specyficzne dla protokołu. Protokół reportowy jest specyficzny dla protokołu, a konfiguracja powinna odzwierciedlać to, zamiast tworzyć efekt uboczny.

#### <a name="multiple-frontends"></a>Wiele frontonów
Jeśli potrzebujesz więcej portów, ponieważ oczekujesz lub masz już duże zapotrzebowanie na połączenia wychodzące, możesz również dodać przyrostowy spis portów dla ruchu przychodzącego, konfigurując dodatkowe frontony, reguły i pule zaplecza na tej samej maszynie wirtualnej. produkcyjnych.

#### <a name="control-which-frontend-is-used-for-outbound"></a>Kontrolowanie, który fronton jest używany do wychodzącego
Jeśli chcesz ograniczyć połączenia wychodzące wyłącznie z określonego adresu IP frontonu, możesz opcjonalnie wyłączyć wychodzący element wiążący dla reguły, która wyraża mapowanie wychodzące.

#### <a name="control-outbound-connectivity"></a>Kontrola łączności wychodzącej
Usługa Load Balancer w warstwie Standardowa istnieje w kontekście sieci wirtualnej.  Sieć wirtualna jest izolowaną siecią prywatną.  Jeśli skojarzenie z publicznym adresem IP nie istnieje, łączność publiczna jest niedozwolona.  [Punkty końcowe usługi](../virtual-network/virtual-network-service-endpoints-overview.md) sieci wirtualnej można osiągnąć, ponieważ znajdują się one w zakresie i są lokalne w ramach Twojej lokalizacji wirtualnej.  Jeśli chcesz nawiązać łączność wychodzącą z miejscem docelowym poza siecią wirtualną, masz dwie opcje:
- Przypisz standardowy publiczny adres IP jednostki SKU jako publiczny adres IP na poziomie wystąpienia do zasobu maszyny wirtualnej lub
- Umieść zasób maszyny wirtualnej w puli zaplecza usługa Load Balancer w warstwie Standardowa publicznej.

Oba będą zezwalały na połączenia wychodzące z sieci wirtualnej poza siecią wirtualną. 

Jeśli istnieje _tylko_ wewnętrzna usługa Load Balancer w warstwie Standardowa skojarzona z pulą zaplecza, w której znajduje się zasób maszyny wirtualnej, maszyna wirtualna może uzyskać dostęp tylko do zasobów sieci wirtualnej i [punktów końcowych usługi wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md).  Aby utworzyć łączność wychodzącą, można wykonać kroki opisane w poprzednim akapicie.

Łączność wychodząca zasobu maszyny wirtualnej nie skojarzona ze standardowymi jednostkami SKU pozostanie tak jak wcześniej.

Przejrzyj [szczegółowe omówienie połączeń wychodzących](load-balancer-outbound-connections.md).

### <a name="multife"></a>Wiele frontonów
Load Balancer obsługuje wiele reguł z wieloma frontonami.  Usługa Load Balancer w warstwie Standardowa rozszerza to na scenariusze wychodzące.  Scenariusze ruchu wychodzącego są zasadniczo odwrotnością reguły równoważenia obciążenia przychodzącego.  Reguła równoważenia obciążenia przychodzącego tworzy również skojarzenie dla połączeń wychodzących. W usługa Load Balancer w warstwie Standardowa są wykorzystywane wszystkie frontony skojarzone z zasobem maszyny wirtualnej za pośrednictwem reguły równoważenia obciążenia.  Ponadto parametr reguły równoważenia obciążenia i pozwala pominąć regułę równoważenia obciążenia na potrzeby łączności wychodzącej, co pozwala na wybór konkretnych frontonów, w tym braku.

W przypadku porównania podstawowa Load Balancer wybiera jedną fronton losowo i nie ma możliwości kontrolowania, która z nich została wybrana.

Przejrzyj [szczegółowe omówienie połączeń wychodzących](load-balancer-outbound-connections.md).

### <a name="operations"></a>Operacje zarządzania

Zasoby usługa Load Balancer w warstwie Standardowa istnieją na całkowicie nowej platformie infrastruktury.  Umożliwia to szybsze operacje zarządzania w przypadku standardowych jednostek SKU i czasów ukończenia zwykle poniżej 30 sekund na zasób standardowej jednostki SKU.  W miarę wzrostu rozmiaru pul zaplecza, okres wymagany dla zmian puli zaplecza również wzrasta.

Można modyfikować zasoby usługa Load Balancer w warstwie Standardowa i przenosić standardowy publiczny adres IP z jednej maszyny wirtualnej do innej znacznie szybszy.

## <a name="migration-between-skus"></a>Migracja między jednostkami SKU

Jednostki SKU nie są modyfikowalne. Postępuj zgodnie z instrukcjami w tej sekcji, aby przejść z jednej jednostki SKU zasobów do innej.

>[!IMPORTANT]
>Zapoznaj się z tym dokumentem w całości, aby poznać różnice między jednostkami SKU i starannie zbadać swój scenariusz.  Może zajść potrzeba wprowadzenia dodatkowych zmian w celu wyrównania Twojego scenariusza.

### <a name="migrate-from-basic-to-standard-sku"></a>Migrowanie z podstawowej do standardowej jednostki SKU

1. Utwórz nowy zasób standardowy (w razie konieczności Load Balancer i publicznych adresów IP). Utwórz ponownie reguły i definicje sondy.  Jeśli wcześniej używasz sondy TCP do 443/TCP, Rozważ zmianę tego protokołu sondowania na sondę HTTPS i dodanie ścieżki.

2. Utwórz nowe lub zaktualizuj istniejące sieciowej grupy zabezpieczeń na karcie sieciowej lub podsieci, aby dozwolonych ruch o zrównoważonym obciążeniu, sondę, a także inny ruch, który chcesz zezwolić.

3. Usuń podstawowe zasoby jednostki SKU (Load Balancer i publiczne adresy IP zgodnie z wymaganiami) ze wszystkich wystąpień maszyn wirtualnych. Pamiętaj również o usunięciu wszystkich wystąpień maszyn wirtualnych zestawu dostępności.

4. Dołącz wszystkie wystąpienia maszyn wirtualnych do nowych zasobów standardowej jednostki SKU.

### <a name="migrate-from-standard-to-basic-sku"></a>Migrowanie z wersji Standard do podstawowa

1. Utwórz nowy zasób podstawowy (w razie konieczności Load Balancer i publicznych adresów IP). Utwórz ponownie reguły i definicje sondy.  Zmień sondę HTTPS na sondę TCP na 443/TCP. 

2. Usuń standardowe zasoby jednostki SKU (Load Balancer i publiczne adresy IP zgodnie z wymaganiami) ze wszystkich wystąpień maszyn wirtualnych. Pamiętaj również o usunięciu wszystkich wystąpień maszyn wirtualnych zestawu dostępności.

3. Dołącz wszystkie wystąpienia maszyn wirtualnych do nowych zasobów podstawowej jednostki SKU.

>[!IMPORTANT]
>
>Istnieją ograniczenia dotyczące korzystania z podstawowych i standardowych jednostek SKU.
>
>Porty HA i Diagnostyka standardowej jednostki SKU są dostępne tylko w standardowej jednostce SKU. Nie można migrować ze standardowej jednostki SKU do podstawowej jednostki SKU, a także zachować te funkcje.
>
>Podstawowa i standardowa jednostka SKU mają wiele różnic, jak opisano w tym artykule.  Upewnij się, że rozumiesz i przygotowasz do nich.
>
>Do Load Balancer i publicznych zasobów IP muszą być używane zgodne jednostki SKU. Nie można korzystać z kombinacji podstawowych zasobów jednostki SKU i standardowych zasobów jednostki SKU. Nie można dołączyć autonomicznych maszyn wirtualnych, maszyn wirtualnych w zasobie zestawu dostępności lub zasobów zestawu skalowania maszyn wirtualnych jednocześnie do obu jednostek SKU.

## <a name="region-availability"></a>Dostępność w poszczególnych regionach

Usługa Load Balancer w warstwie Standardowa jest obecnie dostępna we wszystkich regionach chmury publicznej.

## <a name="sla"></a>Umowa SLA

Usługi równoważenia obciążenia w warstwie Standardowa są dostępne z umową SLA na 99,99%.  Aby uzyskać szczegółowe informacje, zapoznaj się z umową [SLA usługa Load Balancer w warstwie Standardowa](https://aka.ms/lbsla) .

## <a name="pricing"></a>Cennik

Usługa Load Balancer w warstwie Standardowa jest płatna.

- Liczba skonfigurowanych reguł równoważenia obciążenia i reguł dla ruchu wychodzącego (reguły NAT dla ruchu przychodzącego nie są wliczane do łącznej liczby reguł).
- Ilość przetworzonych danych dla ruchu przychodzącego i wychodzącego niezależnie od reguły. 

Aby uzyskać informacje na temat cen modułu równoważenia obciążenia w warstwie Standardowa, przejdź na stronę [cennika modułu równoważenia obciążenia](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="limitations"></a>Ograniczenia

- Jednostki SKU nie są modyfikowalne. Nie można zmienić jednostki SKU istniejącego zasobu.
- Zasób autonomicznej maszyny wirtualnej, zasób zestawu dostępności lub zasób zestawu skalowania maszyn wirtualnych może odwoływać się do jednej jednostki SKU, nigdy nie obu.
- Reguła Load Balancer nie może obejmować dwóch sieci wirtualnych.  Frontony i powiązane z nimi wystąpienia zaplecza muszą znajdować się w tej samej sieci wirtualnej.  
- [Operacje przenoszenia subskrypcji](../azure-resource-manager/resource-group-move-resources.md) nie są obsługiwane w przypadku zasobów w warstwie Standardowa i PIP.
- Role procesów roboczych sieci Web bez sieci wirtualnej i innych usług platformy firmy Microsoft mogą być dostępne, gdy jest używany tylko wewnętrzny usługa Load Balancer w warstwie Standardowa ze względu na efekt uboczny od sposobu, w jaki działają usługi frontonu i inne usługi platformy. Nie należy polegać na tym, ponieważ sama usługa lub podstawowa platforma może ulec zmianie bez powiadomienia. Zawsze należy założyć, że konieczne jest utworzenie [połączenia wychodzącego](load-balancer-outbound-connections.md) jawnie, jeśli jest to potrzebne, przy użyciu tylko wewnętrznego usługa Load Balancer w warstwie Standardowa.
- Moduł równoważenia obciążenia to produkt protokołu TCP lub UDP służący do równoważenia obciążenia i przekierowania portów na potrzeby tych konkretnych protokołów IP.  Reguły równoważenia obciążenia i reguły NAT dla ruchu przychodzącego są obsługiwane dla protokołów TCP i UDP, ale nie są obsługiwane dla innych protokołów IP, w tym protokołu ICMP. Moduł równoważenia obciążenia nie kończy, nie odpowiada ani w żaden inny sposób nie wchodzi w interakcję z ładunkiem przepływu protokołu UDP ani TCP. Nie jest to serwer proxy. Pomyślne zweryfikowanie łączności z frontonem musi odbywać się w paśmie przy użyciu tego samego protokołu, który jest używany w ramach równoważenia obciążenia lub przychodzącej reguły NAT (TCP lub UDP) _, a_ co najmniej jedna maszyna wirtualna musi generować odpowiedź dla klienta, aby zobaczyć odpowiedź z frontonu.  Nieodebrana odpowiedź w paśmie z frontonu Load Balancer nie wskazuje, że żadne maszyny wirtualne nie mogły odpowiedzieć.  Nie jest możliwe korzystanie z Load Balancer frontonu bez możliwości reagowania maszyny wirtualnej.  Dotyczy to również połączeń wychodzących, dla których [translator adresów sieciowych opartych na źródle na potrzeby maskowania portów](load-balancer-outbound-connections.md#snat) jest obsługiwany tylko w przypadku protokołów TCP i UDP; dla wszystkich innych protokołów IP, w tym ICMP, również zakończy się to niepowodzeniem.  Przypisz publiczny adres IP na poziomie wystąpienia, aby rozwiązać ten problem.
- W przeciwieństwie do publicznych modułów równoważenia obciążenia, które zapewniają [połączenia wychodzące](load-balancer-outbound-connections.md) w przypadku przejścia z prywatnych adresów IP w sieci wirtualnej do publicznych adresów IP, wewnętrzne moduły równoważenia obciążenia nie tłumaczą wychodzących połączeń przychodzących na fronton Load Balancer wewnętrznego, tak jak w przypadku elementów prywatnych.  Pozwala to uniknąć potencjalnych wyczerpania adresów IP wewnątrz unikatowej wewnętrznej przestrzeni adresowej, w której tłumaczenie nie jest wymagane.  Efekt uboczny polega na tym, że jeśli przepływ wychodzący z maszyny wirtualnej w puli zaplecza próbuje przepływ do frontonu wewnętrznego Load Balancer, w którym znajduje się ta pula, _i_ jest mapowany do samego siebie, oba etapy przepływu nie są zgodne i przepływ zakończy się niepowodzeniem.  Jeśli przepływ nie został zmapowany z powrotem do tej samej maszyny wirtualnej w puli zaplecza, która utworzyła przepływ na fronton, przepływ zakończy się pomyślnie.   Gdy przepływ zamapuje się z powrotem do samego siebie, wydaje się, że przepływ wychodzący pochodzi z maszyny wirtualnej do frontonu, a odpowiedni przepływ przychodzący wydaje się pochodzić z maszyny wirtualnej do samej siebie. Z punktu widzenia systemu operacyjnego gościa części dotyczące ruchu przychodzącego i wychodzącego tego samego przepływu nie są zgodne w ramach maszyny wirtualnej. Stos TCP nie rozpozna tych części jednego przepływu jako należących do tego samego przepływu, ponieważ elementy źródłowe i docelowe nie będą zgodne.  Gdy przepływ jest mapowany na dowolną inną maszynę wirtualną w puli zaplecza, połowy przepływu będą zgodne, a maszyna wirtualna może pomyślnie odpowiedzieć na przepływ.  Objawem tego scenariusza jest sporadyczne przekroczenie limitu czasu połączenia. Istnieje kilka typowych obejść do niezawodnego osiągania tego scenariusza (przepływy pochodzące z puli zaplecza do pul zaplecza z wewnętrznymi Load Balancer frontonu), które obejmują Wstawianie serwera proxy innej firmy za wewnętrzną Load Balancer lub [przy użyciu reguł stylu DSR](load-balancer-multivip-overview.md).  Użycie publicznego modułu równoważenia obciążenia jest możliwe w celu rozwiązania tego problemu, ale wynikowy scenariusz jest podatny na [wyczerpanie translatora adresów sieciowych opartych na źródle](load-balancer-outbound-connections.md#snat) i należy tego unikać lub starannie kontrolować.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o korzystaniu z [Usługa Load Balancer w warstwie Standardowa i strefy dostępności](load-balancer-standard-availability-zones.md).
- Dowiedz się więcej o [sondach kondycji](load-balancer-custom-probe-overview.md).
- Dowiedz się więcej o [strefy dostępności](../availability-zones/az-overview.md).
- Dowiedz się więcej na temat [diagnostyki usługa Load Balancer w warstwie Standardowa](load-balancer-standard-diagnostics.md).
- Dowiedz się więcej na temat [obsługiwanych metryk wielowymiarowych](../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) dla diagnostyki w [Azure monitor](../monitoring-and-diagnostics/monitoring-overview.md).
- Dowiedz się więcej o korzystaniu z [modułu równoważenia obciążenia dla połączeń wychodzących](load-balancer-outbound-connections.md).
- Poznaj [reguły ruchu wychodzącego](load-balancer-outbound-rules-overview.md).
- Więcej informacji [na temat resetowania protokołu TCP w trybie bezczynności](load-balancer-tcp-reset.md).
- Dowiedz się więcej o [Usługa Load Balancer w warstwie Standardowa z regułami równoważenia obciążenia dla portów ha](load-balancer-ha-ports-overview.md).
- Dowiedz się więcej o korzystaniu [z Load Balancer z wieloma frontonami](load-balancer-multivip-overview.md).
- Dowiedz się więcej o [sieciach wirtualnych](../virtual-network/virtual-networks-overview.md).
- Dowiedz się więcej na temat [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md).
- Dowiedz się więcej o [punktach końcowych usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md).
- Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) na platformie Azure.
- Dowiedz się więcej o [Load Balancer](load-balancer-overview.md).
