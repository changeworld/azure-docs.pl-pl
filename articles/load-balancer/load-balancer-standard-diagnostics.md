---
title: Diagnostyka przy użyciu metryk, alertów i kondycji zasobów — usługa Load Balancer w warstwie Standardowa platformy Azure
description: Korzystając z dostępnych metryk, alertów i informacji o kondycji zasobów, można zdiagnozować usługa Load Balancer w warstwie Standardowa platformy Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: allensu
ms.openlocfilehash: c362829b1babf954868452a3858da1f319008a9a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990780"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Diagnostyka usługi Load Balancer w warstwie Standardowa przy użyciu metryk, alertów i kondycji zasobów

Usługa Azure usługa Load Balancer w warstwie Standardowa udostępnia następujące możliwości diagnostyczne:

* **Wielowymiarowe metryki i alerty**: zapewniają wielowymiarowe funkcje diagnostyczne, [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) w przypadku konfiguracji usługi równoważenia obciążenia w warstwie Standardowa. Możesz monitorować zasoby standardowego modułu równoważenia obciążenia, zarządzać nimi i rozwiązywać problemy.

* **Kondycja zasobów**: Strona Load Balancer na Azure Portal i Resource Health stronie (w obszarze monitor) uwidacznia sekcję Resource Health dla usługa Load Balancer w warstwie Standardowa. 

Ten artykuł zawiera krótki przewodnik po tych możliwościach i oferuje sposoby ich używania do usługa Load Balancer w warstwie Standardowa. 

## <a name = "MultiDimensionalMetrics"></a>Metryki wielowymiarowe

Azure Load Balancer udostępniają wielowymiarowe metryki za pośrednictwem metryk platformy Azure w Azure Portal i ułatwiają uzyskiwanie informacji diagnostycznych w czasie rzeczywistym do zasobów modułu równoważenia obciążenia. 

Różne konfiguracje usługa Load Balancer w warstwie Standardowa zapewniają następujące metryki:

| Metryka | Typ zasobu | Opis | Zalecana agregacja |
| --- | --- | --- | --- |
| Dostępność ścieżki danych (dostępność VIP)| Publiczny i wewnętrzny moduł równoważenia obciążenia | Usługa Load Balancer w warstwie Standardowa stale wykonuje ścieżkę danych z regionu do frontonu modułu równoważenia obciążenia, aż do stosu SDN, który obsługuje maszynę wirtualną. Tak długo, jak wystąpienia w dobrej kondycji, pomiar jest zgodny z tą samą ścieżką, co ruch o zrównoważonym obciążeniu aplikacji. Ścieżka danych używana przez klientów jest również sprawdzana. Pomiar jest niewidoczny dla aplikacji i nie zakłóca innych operacji.| Średnia |
| Stan sondy kondycji (dostęp DIP) | Publiczny i wewnętrzny moduł równoważenia obciążenia | Usługa Load Balancer w warstwie Standardowa używa rozproszonej usługi badania kondycji, która monitoruje kondycję punktu końcowego aplikacji zgodnie z ustawieniami konfiguracji. Ta Metryka zawiera Zagregowany widok filtrowany lub na punkt końcowy każdego punktu końcowego wystąpienia w puli modułu równoważenia obciążenia. Możesz zobaczyć, jak Load Balancer przegląda kondycję aplikacji zgodnie z konfiguracją sondy kondycji. |  Średnia |
| Pakiety SYN (Synchronize) | Publiczny i wewnętrzny moduł równoważenia obciążenia | Usługa Load Balancer w warstwie Standardowa nie przerywa połączeń Transmission Control Protocol (TCP) ani nie współdziała z przepływami pakietów TCP lub UDP. Przepływy i ich uzgodnienia są zawsze między wystąpieniem źródłowym a maszyną wirtualną. Aby lepiej rozwiązać problemy ze scenariuszami protokołu TCP, można użyć liczników pakietów SYN, aby zrozumieć, ile prób połączenia TCP zostało nawiązane. Metryka zgłasza liczbę odebranych pakietów TCP SYN.| Średnia |
| Połączenia z przyłączaniem | Publiczny moduł równoważenia obciążenia |Usługa Load Balancer w warstwie Standardowa raportuje liczbę przepływów wychodzących, które są zamaskowane do frontonu publicznego adresu IP. Porty translatora adresów sieciowych (Resource Address Translation) to zasób exhaustible. Ta Metryka może wskazywać na to, jak silna aplikacja jest zależna od przychodzących przepływów. Są raportowane liczniki dla zakończonych powodzeniem i zakończonych niepowodzeniem przepływów przychodzących obiektów przeruchowych, które mogą służyć do rozwiązywania problemów i zrozumienia kondycji przepływów wychodzących.| Średnia |
| Przydzielono porty przydziałów adresów sieciowych | Publiczny moduł równoważenia obciążenia | usługa Load Balancer w warstwie Standardowa zgłasza liczbę portów przyznanych przez wystąpienie wewnętrznej bazy danych | Obliczon. |
| Używane porty | Publiczny moduł równoważenia obciążenia | Usługa Load Balancer w warstwie Standardowa raportuje liczbę portów, które są używane dla wystąpienia zaplecza. | Średnia | 
| Liczniki bajtów |  Publiczny i wewnętrzny moduł równoważenia obciążenia | Usługa Load Balancer w warstwie Standardowa raportuje dane przetworzone na fronton. Można zauważyć, że bajty nie są równomiernie dystrybuowane między wystąpieniami zaplecza. Jest to oczekiwane, ponieważ algorytm Load Balancer platformy Azure jest oparty na przepływach | Średnia |
| Liczniki pakietów |  Publiczny i wewnętrzny moduł równoważenia obciążenia | Usługa Load Balancer w warstwie Standardowa zgłasza pakiety przetwarzane na fronton.| Średnia |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Wyświetl metryki modułu równoważenia obciążenia w Azure Portal

Azure Portal uwidacznia metryki modułu równoważenia obciążenia za pośrednictwem strony metryk, która jest dostępna na stronie zasobów modułu równoważenia obciążenia dla określonego zasobu i strony Azure Monitor. 

Aby wyświetlić metryki dla zasobów usługa Load Balancer w warstwie Standardowa:
1. Przejdź do strony metryki i wykonaj jedną z następujących czynności:
   * Na stronie zasób usługi równoważenia obciążenia wybierz typ metryki z listy rozwijanej.
   * Na stronie Azure Monitor wybierz zasób usługi równoważenia obciążenia.
2. Ustaw odpowiedni typ agregacji.
3. Opcjonalnie skonfiguruj wymagane filtrowanie i grupowanie.

    ![Metryki dla usługa Load Balancer w warstwie Standardowa](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

    *Ilustracja: Metryka dostępności ścieżki danych dla usługa Load Balancer w warstwie Standardowa*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Programowe pobieranie metryk wielowymiarowych za pośrednictwem interfejsów API

Aby uzyskać wskazówki dotyczące interfejsu API na potrzeby pobierania wielowymiarowych definicji i wartości metryk, zobacz [Przewodnik po interfejsie API REST monitorowania platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api). Te metryki można zapisywać na koncie magazynu za pośrednictwem tylko opcji "wszystkie metryki". 

### <a name = "DiagnosticScenarios"></a>Typowe scenariusze diagnostyczne i zalecane widoki

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Czy ścieżka danych jest dostępna dla wirtualnego adresu VIP modułu równoważenia obciążenia?

Metryka dostępności adresu VIP opisuje prawidłowość ścieżki danych w regionie do hosta obliczeniowego, w którym znajdują się maszyny wirtualne. Metryka to odbicie kondycji infrastruktury platformy Azure. Możesz użyć metryki, aby:
- Monitorowanie zewnętrznej dostępności usługi
- Dig się bardziej szczegółowo i Dowiedz się, czy platforma, w której jest wdrażana usługa, jest w dobrej kondycji, czy jej wystąpienie jest w dobrej kondycji.
- Izoluj, czy zdarzenie jest powiązane z usługą lub podstawową płaszczyzną danych. Nie należy mylić tej metryki ze stanem sondy kondycji ("dostępność DIP").

Aby uzyskać dostępność ścieżki danych dla usługa Load Balancer w warstwie Standardowa zasobów:
1. Upewnij się, że wybrano prawidłowy zasób modułu równoważenia obciążenia. 
2. Z listy rozwijanej **Metryka** wybierz opcję **dostępność ścieżki danych**. 
3. Z listy rozwijanej **agregacja** wybierz pozycję **średnia**. 
4. Dodatkowo należy dodać filtr dla adresu IP frontonu lub portu frontonu jako wymiar z wymaganym adresem IP frontonu lub portem frontonu, a następnie zgrupować je według wybranego wymiaru.

![Sondowanie VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Ilustracja: szczegóły dotyczące sondowania frontonu Load Balancer*

Metryka jest generowana przez aktywną miarę w paśmie. Usługa sondowania w regionie odniesie ruch do miary. Usługa zostanie aktywowana zaraz po utworzeniu wdrożenia z publicznym frontonem i będzie kontynuowane do momentu usunięcia frontonu. 

Pakiet zgodny z frontonem i regułą wdrożenia jest generowany okresowo. Przechodzą region z lokalizacji źródłowej na host, na którym znajduje się maszyna wirtualna w puli zaplecza. Infrastruktura modułu równoważenia obciążenia wykonuje te same operacje związane z równoważeniem obciążenia i translacji, co dla całego ruchu. To sondowanie jest w paśmie w punkcie końcowym ze zrównoważonym obciążeniem. Po nadejściu sondy na hoście obliczeniowym, w którym znajduje się dobra maszyna wirtualna w puli zaplecza, Host obliczeniowy generuje odpowiedź do usługi sondowania. Ten ruch nie jest widoczny dla maszyny wirtualnej.

Dostępność adresu VIP kończy się niepowodzeniem z następujących powodów:
- Wdrożenie nie obejmuje żadnych prawidłowych maszyn wirtualnych w puli zaplecza. 
- Wystąpiła awaria infrastruktury.

W celach diagnostycznych można użyć [metryki dostępności ścieżki danych wraz ze stanem sondy kondycji](#vipavailabilityandhealthprobes).

Użyj **średniej** jako agregacji dla większości scenariuszy.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>Czy wystąpienia zaplecza dla adresu VIP odpowiadają na sondy?

Metryka stanu sondy kondycji opisuje kondycję wdrożenia aplikacji zgodnie z konfiguracją podczas konfigurowania sondy kondycji modułu równoważenia obciążenia. Moduł równoważenia obciążenia używa stanu sondy kondycji, aby określić, gdzie mają być wysyłane Nowe przepływy. Sondy kondycji pochodzą z adresu infrastruktury platformy Azure i są widoczne w systemie operacyjnym gościa maszyny wirtualnej.

Aby uzyskać stan sondy kondycji dla zasobów usługa Load Balancer w warstwie Standardowa:
1. Wybierz metrykę **stanu sondy kondycji** z **średnim** typem agregacji. 
2. Zastosuj filtr na wymaganym adresie IP frontonu (lub w obu portach).

Sondy kondycji nie powiodły się z następujących powodów:
- Można skonfigurować sondę kondycji do portu, który nie nasłuchuje lub nie odpowiada lub używa niewłaściwego protokołu. Jeśli usługa korzysta z bezpośrednich reguł powrotu serwera (DSR lub zmiennoprzecinkowych adresów IP), upewnij się, że usługa nasłuchuje na adresie IP konfiguracji protokołu IP karty sieciowej, a nie tylko w sprzężeniu zwrotnym, które skonfigurowano przy użyciu adresu IP frontonu.
- Sonda nie jest dozwolona przez grupę zabezpieczeń sieci, zaporę systemu operacyjnego gościa maszyny wirtualnej ani filtry warstwy aplikacji.

Użyj **średniej** jako agregacji dla większości scenariuszy.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Jak mogę sprawdzić statystykę połączenia wychodzącego? 

Metryka połączenia z przyłączaniem do translatora adresów sieciowych opisuje ilość pomyślnych i nieudanych połączeń dla [przepływów wychodzących](https://aka.ms/lboutbound).

Liczba nieudanych połączeń w liczbie większej niż zero oznacza wyczerpanie portów przez translatora adresów sieciowych. Aby określić, co może powodować te błędy, należy zbadać więcej. Manifesty wyczerpania portów strumienia adresów sieciowych jako niepowodzenie do ustanowienia [przepływu wychodzącego](https://aka.ms/lboutbound). Zapoznaj się z artykułem dotyczącym połączeń wychodzących, aby poznać scenariusze i mechanizmy w pracy oraz dowiedzieć się, jak ograniczyć i zaprojektować, aby uniknąć wyczerpania portów. 

Aby uzyskać statystyki połączeń z podłączaniem adresów sieciowych:
1. Wybierz typ metryki połączenia z podłączaniem **adresów sieciowych** i **sumę** jako agregację. 
2. Grupuj według **stanu połączenia** dla zakończonych powodzeniem i niepowodzeniem liczby połączeń z dołączonym translatorem adresów sieciowych, które są reprezentowane przez różne wiersze. 

![Połączenie z przywiązaniem](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Rysunek: Load Balancer liczba połączeń z przyłączaniem do adresów sieciowych*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Jak mogę sprawdzić połączenia przychodzące/wychodzące dla mojej usługi?

Metryka pakietów SYN opisuje ilość pakietów TCP SYN, które zostały odebrane lub wysłane (dla [przepływów wychodzących](https://aka.ms/lboutbound)), które są skojarzone z określonym frontonem. Ta Metryka służy do zrozumienia prób połączenia TCP z usługą.

Użyj **sum** jako agregacji dla większości scenariuszy.

![Połączenie SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Ilustracja: liczba SYN Load Balancer*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Jak mogę sprawdzić użycie przepustowości sieci? 

Metryki bajtów i pakietów opisują ilość bajtów i pakietów, które są wysyłane lub odbierane przez usługę, na podstawie frontonu.

Użyj **sum** jako agregacji dla większości scenariuszy.

Aby uzyskać statystykę liczby bajtów lub pakietów:
1. Wybierz **liczbę bajtów** i/lub typ metryki **Liczba pakietów** , z **średnim** jako agregacją. 
2. Wykonaj jedną z następujących czynności:
   * Zastosuj filtr dla określonego adresu IP frontonu, portu frontonu, adresu IP zaplecza lub portu zaplecza.
   * Uzyskaj ogólne statystyki dla zasobu modułu równoważenia obciążenia bez filtrowania.

![Liczba bajtów](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Ilustracja: liczba bajtów Load Balancer*

#### <a name = "vipavailabilityandhealthprobes"></a>Jak mogę zdiagnozować wdrożenie modułu równoważenia obciążenia?

Korzystając z kombinacji metryk dostępności adresu VIP i sondy kondycji na jednym wykresie, można określić, gdzie szukać problemu i rozwiązać problem. Możesz uzyskać gwarancję, że platforma Azure działa prawidłowo i korzystaj z tej wiedzy, aby w sposób niejednoznaczny określić, że konfiguracja lub aplikacja jest główną przyczyną.

Metryki sondy kondycji umożliwiają zrozumienie sposobu, w jaki platforma Azure przegląda kondycję wdrożenia zgodnie z podaną konfiguracją. Spojrzenie na sondy kondycji jest zawsze doskonałym pierwszym krokiem w zakresie monitorowania lub określania przyczyny.

Aby uzyskać wgląd w to, jak platforma Azure przegląda kondycję źródłowej płaszczyzny danych, która jest odpowiedzialna za konkretne wdrożenie, możesz wykonać tę czynność. Podczas łączenia obu metryk można wyizolować miejsce, w którym może się pojawić błąd, jak pokazano w tym przykładzie:

![Łączenie metryk stanu badania dostępności i kondycji ścieżki danych](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Ilustracja: łączenie metryk dostępności ścieżki danych i stanu sondy kondycji*

Na wykresie są wyświetlane następujące informacje:
- Infrastruktura obsługująca maszyny wirtualne była niedostępna, a na początku wykresu wynosi 0 procent. Później infrastruktura była w dobrej kondycji, a maszyny wirtualne były dostępne i więcej niż jedna maszyna wirtualna została umieszczona w zapleczu. Te informacje są wskazywane przez niebieskie śledzenie dostępności ścieżki danych (dostępność VIP), która była późniejsza o 100%. 
- Stan sondy kondycji (dostępność DIP), wskazywany przez ślad purpurowy, wynosi 0 procent na początku wykresu. Obszar koła w zielonym świetleniu, w którym stan sondy kondycji (w przypadku dostępności DIP) jest w dobrej kondycji i w którym momencie wdrożenie klienta mogło akceptować Nowe przepływy.

Wykres umożliwia klientom Samodzielne rozwiązywanie problemów ze wdrożeniem bez konieczności odgadnięcia lub poproszenia o ewentualne problemy. Usługa była niedostępna, ponieważ sondy kondycji kończyły się niepowodzeniem z powodu błędnej konfiguracji lub niepowodzenia aplikacji.

## <a name = "ResourceHealth"></a>Stan kondycji zasobu

Stan kondycji zasobów usługa Load Balancer w warstwie Standardowa jest udostępniany za pośrednictwem istniejącej **kondycji zasobów** w obszarze **monitorowanie > Service Health**.

Aby wyświetlić kondycję publicznych zasobów usługa Load Balancer w warstwie Standardowa:
1. Wybierz pozycję **monitoruj** > **Service Health**.

   ![Strona monitorowanie](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Ilustracja: łącze Service Health na Azure Monitor*

2. Wybierz pozycję **Resource Health**, a następnie upewnij się, że wybrano opcję **Identyfikator subskrypcji** i **Typ zasobu = Load Balancer** .

   ![Stan kondycji zasobu](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Ilustracja: Wybieranie zasobów dla widoku kondycji*

3. Z listy wybierz zasób Load Balancer, aby wyświetlić jego historyczny stan kondycji.

    ![Load Balancer stan kondycji](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Ilustracja: widok kondycji zasobów Load Balancer*
 
W poniższej tabeli wymieniono różne stany kondycji zasobów i ich opisy: 

| Stan kondycji zasobu | Opis |
| --- | --- |
| Dostępna | Zasób standardowego modułu równoważenia obciążenia jest w dobrej kondycji i jest dostępny. |
| Niedostępny | Zasób standardowego modułu równoważenia obciążenia nie jest w dobrej kondycji. Diagnozuj kondycję, wybierając pozycję **Azure Monitor** > **metryki**.<br>(Stan*niedostępny* może również oznaczać, że zasób nie jest połączony z usługą równoważenia obciążenia w warstwie Standardowa). |
| Nieznane | Stan kondycji zasobu dla zasobu standardowego modułu równoważenia obciążenia nie został jeszcze zaktualizowany.<br>(*Nieznany* stan może również oznaczać, że zasób nie jest połączony z usługą równoważenia obciążenia w warstwie Standardowa).  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).
- Dowiedz się więcej o [łączności wychodzącej modułu równoważenia obciążenia](https://aka.ms/lboutbound).
- Dowiedz się więcej na temat [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview).
- Dowiedz się więcej o [interfejsie API rest Azure monitor](https://docs.microsoft.com/rest/api/monitor/) i [sposobach pobierania metryk za pośrednictwem interfejsu API REST](/rest/api/monitor/metrics/list).
