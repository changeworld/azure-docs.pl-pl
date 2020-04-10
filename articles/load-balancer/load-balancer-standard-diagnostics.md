---
title: Diagnostyka z metrykami, alertami i kondycją zasobów — Azure Standard Load Balancer
description: Użyj dostępnych metryk, alertów i informacji o kondycji zasobów, aby zdiagnozować standardowy moduł równoważenia obciążenia platformy Azure.
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
ms.openlocfilehash: 951f24ad06014f6d95f10c91e1bad8e99bbbc736
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991777"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Diagnostyka usługi Load Balancer w warstwie Standardowa przy użyciu metryk, alertów i kondycji zasobów

Moduł równoważenia obciążenia standardowego platformy Azure udostępnia następujące funkcje diagnostyczne:

* **Wielowymiarowe metryki i alerty:** zapewnia wielowymiarowe funkcje diagnostyczne za pośrednictwem [usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) dla standardowych konfiguracji modułu równoważenia obciążenia. Można monitorować, zarządzać i rozwiązywać problemy ze standardowymi zasobami modułu równoważenia obciążenia.

* **Kondycja zasobów:** Strona Moduł równoważenia obciążenia w witrynie Azure portal i strona Kondycja zasobów (w obszarze Monitor) udostępnia sekcję Kondycja zasobów dla standardowego modułu równoważenia obciążenia. 

Ten artykuł zawiera szybkie okiem tych funkcji i oferuje sposoby ich używania dla standardowego modułu równoważenia obciążenia. 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>Metryki wielowymiarowe

Moduł równoważenia obciążenia platformy Azure udostępnia metryki wielowymiarowe za pośrednictwem metryk platformy Azure w witrynie Azure portal i pomaga uzyskać wgląd diagnostyczny w czasie rzeczywistym w zasoby modułu równoważenia obciążenia. 

Różne standardowe konfiguracje modułu równoważenia obciążenia zawierają następujące metryki:

| Metryka | Typ zasobu | Opis | Zalecana agregacja |
| --- | --- | --- | --- |
| Dostępność ścieżki danych (dostępność usług VIP)| Publiczny i wewnętrzny moduł równoważenia obciążenia | Standardowy moduł równoważenia obciążenia stale wykonuje ścieżkę danych z poziomu regionu do frontonaku modułu równoważenia obciążenia, aż do stosu SDN obsługującego maszynę wirtualną. Tak długo, jak w dobrej kondycji wystąpień pozostają, pomiar podąża tą samą ścieżką, co ruch z równoważenia obciążenia aplikacji. Ścieżka danych, z której korzystają klienci, jest również sprawdzana. Pomiar jest niewidoczny dla aplikacji i nie koliduje z innymi operacjami.| Średnia |
| Stan sondy kondycji (dostępność DIP) | Publiczny i wewnętrzny moduł równoważenia obciążenia | Standardowy moduł równoważenia obciążenia używa rozproszonej usługi sondowania kondycji, która monitoruje kondycję punktu końcowego aplikacji zgodnie z ustawieniami konfiguracji. Ta metryka zawiera widok zagregowany lub filtrowany dla punktu końcowego każdego punktu końcowego wystąpienia w puli modułu równoważenia obciążenia. Można zobaczyć, jak moduł równoważenia obciążenia wyświetla kondycję aplikacji, zgodnie z wskazaniem w konfiguracji sondy kondycji. |  Średnia |
| PAKIETY SYN (synchronizuj) | Publiczny i wewnętrzny moduł równoważenia obciążenia | Standardowy moduł równoważenia obciążenia nie kończy połączeń protokołu TCP (Transmission Control Protocol) ani nie wchodzi w interakcję z przepływami pakietów TCP lub UDP. Przepływy i ich uzgadniania są zawsze między źródłem i wystąpieniem maszyny Wirtualnej. Aby lepiej rozwiązywać scenariusze protokołu TCP, można użyć liczników pakietów SYN, aby dowiedzieć się, ile prób połączenia TCP jest podejmowanych. Metryka raportuje liczbę odebranych pakietów TCP SYN.| Średnia |
| Połączenia SNAT | Publiczny moduł równoważenia obciążenia |Standardowy moduł równoważenia obciążenia raportuje liczbę przepływów wychodzących, które są maskowane do publicznego adresu IP frontonu. Porty tłumaczenia adresów sieciowych źródłowych (SNAT) są zasobem wyczerpującym. Ta metryka może wskazywać, jak mocno aplikacja polega na SNAT dla wychodzących przepływów pochodzących. Liczniki dla pomyślnych i nieudanych wychodzących przepływów SNAT są zgłaszane i mogą służyć do rozwiązywania problemów i zrozumienia kondycji przepływów wychodzących.| Średnia |
| Przydzielone porty SNAT | Publiczny moduł równoważenia obciążenia | Standardowy moduł równoważenia obciążenia raportuje liczbę portów SNAT przydzielonych na wystąpienie zaplecza | Średnia. |
| Używane porty SNAT | Publiczny moduł równoważenia obciążenia | Standardowy moduł równoważenia obciążenia raportuje liczbę portów SNAT, które są używane na wystąpienie wewnętrznej bazy danych. | Średnia | 
| Liczniki bajtów |  Publiczny i wewnętrzny moduł równoważenia obciążenia | Standardowy moduł równoważenia obciążenia raportuje dane przetwarzane na fronton. Można zauważyć, że bajty nie są równomiernie rozłożone między wystąpieniami wewnętrznej bazy danych. Jest to oczekiwane, ponieważ algorytm równoważenia obciążenia platformy Azure jest oparty na przepływach | Średnia |
| Liczniki pakietów |  Publiczny i wewnętrzny moduł równoważenia obciążenia | Standardowy moduł równoważenia obciążenia raportuje pakiety przetworzone na fronton.| Średnia |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Wyświetlanie metryk modułu równoważenia obciążenia w witrynie Azure portal

Portal Azure udostępnia metryki modułu równoważenia obciążenia za pośrednictwem strony Metryki, która jest dostępna zarówno na stronie zasobu modułu równoważenia obciążenia dla określonego zasobu, jak i na stronie Usługi Azure Monitor. 

Aby wyświetlić metryki dotyczące zasobów standardowego modułu równoważenia obciążenia:
1. Przejdź do strony Metryki i wykonaj jedną z następujących czynności:
   * Na stronie zasobu modułu równoważenia obciążenia wybierz typ metryki na liście rozwijanej.
   * Na stronie Monitor platformy Azure wybierz zasób modułu równoważenia obciążenia.
2. Ustaw odpowiedni typ agregacji metryki.
3. Opcjonalnie należy skonfigurować wymagane filtrowanie i grupowanie.
4. Opcjonalnie skonfiguruj zakres czasu i agregację. Domyślnie czas jest wyświetlany w czasie UTC.

  >[!NOTE] 
  >Agregacja czasu jest ważne podczas interpretacji niektórych metryk, jak dane są próbkowane raz na minutę. Jeśli agregacja czasu jest ustawiona na pięć minut, a typ agregacji metryki Suma jest używany dla metryk, takich jak alokacja SNAT, na wykresie będzie wyświetlany pięciokrotnie więcej niż całkowita przydzielona porty SNAT. 

![Metryki dla standardowego modułu równoważenia obciążenia](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*Rysunek: Metryka dostępności ścieżki danych dla standardowego modułu równoważenia obciążenia*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Programowo pobieranie metryk wielowymiarowych za pośrednictwem interfejsów API

Aby uzyskać wskazówki dotyczące interfejsu API dotyczące pobierania wielowymiarowych definicji i wartości metryk, zobacz [Instruktaż interfejsu API interfejsu REST monitorowania platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api). Te metryki mogą być zapisywane na koncie magazynu tylko za pomocą opcji "Wszystkie metryki". 

### <a name="configure-alerts-for-multi-dimensional-metrics"></a>Konfigurowanie alertów dla metryk wielowymiarowych ###

Azure Standard Load Balancer obsługuje łatwe konfigurowalne alerty dla metryk wielowymiarowych. Skonfiguruj niestandardowe progi dla określonych metryk, aby wyzwalać alerty o różnym poziomie ważności, aby umożliwić monitorowanie zasobów bezdotykowych.

Aby skonfigurować alerty:
1. Przejdź do podraźnika alertu dla modułu równoważenia obciążenia
1. Tworzenie nowej reguły alertu
    1.  Konfigurowanie stanu alertu
    1.  (Opcjonalnie) Dodaj grupę akcji do automatycznej naprawy
    1.  Przypisywanie ważności alertu, nazwy i opisu, które umożliwiają intuicyjną reakcję

  >[!NOTE]
  >Okno konfiguracji stanu alertu wyświetli szeregi czasowe dla historii sygnału. Istnieje możliwość filtrowania tych szeregów czasowych według wymiarów, takich jak adres IP wewnętrznej bazy danych. Spowoduje to filtrowanie wykresu szeregów czasowych, ale **nie** samego alertu. Nie można skonfigurować alertów dla określonych adresów IP wewnętrznej bazy danych.

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>Typowe scenariusze diagnostyczne i zalecane widoki

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Czy ścieżka danych jest dostępna dla mojego modułu VIP równoważenia obciążenia?
<details><summary>Rozwiń</summary>

Metryka dostępności programu VIP opisuje kondycję ścieżki danych w regionie do hosta obliczeniowego, na którym znajdują się maszyny wirtualne. Metryka jest odzwierciedleniem kondycji infrastruktury platformy Azure. Metryki można użyć do:
- Monitorowanie zewnętrznej dostępności usługi
- Kopać głębiej i zrozumieć, czy platforma, na której wdrożona usługa jest w dobrej kondycji lub czy twój system operacyjny gościa lub wystąpienie aplikacji jest w dobrej kondycji.
- Wyizoluj, czy zdarzenie jest związane z usługą lub podstawową płaszczyzną danych. Nie należy mylić tej metryki ze stanem sondy kondycji ("dostępność DIP").

Aby uzyskać dostępność ścieżki danych dla zasobów standardowego modułu równoważenia obciążenia:
1. Upewnij się, że wybrano poprawny zasób modułu równoważenia obciążenia. 
2. Z listy rozwijanej **Metryka** wybierz pozycję **Dostępność ścieżki danych**. 
3. Z listy rozwijanej **Agregacja** wybierz pozycję **Średnia**. 
4. Ponadto należy dodać filtr na adresie IP frontu lub portie Frontend jako wymiar z wymaganym adresem IP front-end lub portem front-end, a następnie pogrupować je według wybranego wymiaru.

![Sondowanie VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Rysunek: Szczegóły sondowania frontendu modułu równoważenia obciążenia*

Metryka jest generowana przez aktywny pomiar w paśmie. Usługa sondowania w regionie pochodzi z ruchu do pomiaru. Usługa jest aktywowana natychmiast po utworzeniu wdrożenia z publicznego fronto- i będzie kontynuowana do momentu usunięcia frontokału. 

Pakiet pasujący do frontu i reguły wdrożenia jest generowany okresowo. Przechodzi przez region ze źródła do hosta, w którym znajduje się maszyna wirtualna w puli zaplecza. Infrastruktura modułu równoważenia obciążenia wykonuje te same operacje równoważenia obciążenia i tłumaczenia, jak w przypadku całego innego ruchu. Ta sonda jest w paśmie dla punktu końcowego równoważenia obciążenia. Po nadejdzie sondy na hoście obliczeniowym, gdzie znajduje się w dobrej kondycji maszyny Wirtualnej w puli zaplecza, host obliczeń generuje odpowiedź na usługę sondowania. Maszyna wirtualna nie widzi tego ruchu.

Dostępność vip nie powiedzie się z następujących powodów:
- Wdrożenie nie ma żadnych zdrowych maszyn wirtualnych pozostałych w puli zaplecza. 
- Wystąpiła awaria infrastruktury.

Do celów diagnostycznych można użyć [metryki Dostępność ścieżki danych wraz ze stanem sondy kondycji](#vipavailabilityandhealthprobes).

Użyj **średniej** jako agregacji dla większości scenariuszy.
</details>

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>Czy wystąpienia zaplecza dla mojego VIP odpowiada na sondy?
<details>
  <summary>Rozwiń</summary>
Metryka stanu sondy kondycji opisuje kondycję wdrożenia aplikacji skonfigurowanej przez użytkownika podczas konfigurowania sondy kondycji modułu równoważenia obciążenia. Moduł równoważenia obciążenia używa stanu sondy kondycji, aby określić, gdzie należy wysyłać nowe przepływy. Sondy kondycji pochodzą z adresu infrastruktury platformy Azure i są widoczne w obszarze operacyjnym gościa maszyny Wirtualnej.

Aby uzyskać stan sondy kondycji dla zasobów standardowego modułu równoważenia obciążenia:
1. Wybierz metrykę **Stan sondy kondycji** z typem agregacji **Avg.** 
2. Zastosuj filtr na wymaganym adresie IP i porcie frontu (lub obu).

Sondy kondycji nie powiodą się z następujących powodów:
- Skonfiguruj sondę kondycji do portu, który nie nasłuchuje lub nie odpowiada lub używa niewłaściwego protokołu. Jeśli usługa używa reguł bezpośredniego zwracania serwera (DSR lub zmiennego adresu IP), upewnij się, że usługa nasłuchuje na adres IP konfiguracji adresów SIECI, a nie tylko na sprzęcie zwrotnym skonfigurowanym z adresem IP front-end.
- Sonda nie jest dozwolona przez grupę zabezpieczeń sieci, zaporę systemu operacyjnego gościa maszyny Wirtualnej ani filtry warstwy aplikacji.

Użyj **średniej** jako agregacji dla większości scenariuszy.
</details>

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Jak sprawdzić statystyki połączenia wychodzącego? 
<details>
  <summary>Rozwiń</summary>
Metryka połączeń SNAT opisuje wolumin połączeń udanych i nieudanych dla [przepływów wychodzących](https://aka.ms/lboutbound).

Wolumin połączeń nie powiodło się większa niż zero wskazuje wyczerpanie portu SNAT. Należy zbadać dalej, aby ustalić, co może być przyczyną tych błędów. Wyczerpanie portu SNAT objawia się jako brak ustanowienia [przepływu wychodzącego](https://aka.ms/lboutbound). Przejrzyj artykuł o połączeniach wychodzących, aby zrozumieć scenariusze i mechanizmy w pracy i dowiedzieć się, jak ograniczyć i zaprojektować, aby uniknąć wyczerpania portów SNAT. 

Aby uzyskać statystyki połączeń SNAT:
1. Wybierz typ metryki **Połączenia SNAT** i **Suma** jako agregację. 
2. Grupuj według **stanu połączenia** dla pomyślnych i nieudanych zliczeń połączeń SNAT, które są reprezentowane przez różne wiersze. 

![Połączenie SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Rysunek: Liczba połączeń SNAT modułu równoważenia obciążenia*
</details>


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>Jak sprawdzić użycie i alokację portu SNAT?
<details>
  <summary>Rozwiń</summary>
Metryka SNAT Usage wskazuje, ile unikatowych przepływów jest ustanawianych między źródłem internetowym a zestawem skalowania wewnętrznej bazy danych lub maszyny wirtualnej, który znajduje się za modułem równoważenia obciążenia i nie ma publicznego adresu IP. Porównując to z metryki alokacji SNAT, można określić, czy usługa występuje lub na ryzyko wyczerpania SNAT i wynikające z awarii przepływu wychodzącego. 

Jeśli metryki wskazują na ryzyko awarii [przepływu wychodzącego,](https://aka.ms/lboutbound) odwołaj się do tego artykułu i podejmij kroki w celu ograniczenia tego stanu zdrowia usługi.

Aby wyświetlić użycie i alokację portu SNAT:
1. Ustaw agregację czasu wykresu na 1 minutę, aby zapewnić wyświetlanie żądanych danych.
1. Wybierz **użycie SNAT** i/lub **Alokację SNAT** jako typ metryki i **Średnią** jako agregację
    * Domyślnie jest to średnia liczba portów SNAT przydzielonych lub używanych przez każdą maszyn wirtualnych zaplecza lub VMSSes, odpowiadająca wszystkim publicznym ipom frontendu mapowane do modułu równoważenia obciążenia, zagregowane za pomocą TCP i UDP.
    * Aby wyświetlić całkowitą liczbę portów SNAT używanych przez moduł równoważenia obciążenia lub przydzielonych dla modułu równoważenia obciążenia, użyj **sumy** agregacji metryk
1. Filtrowanie do określonego **typu protokołu,** zestawu adresów **IP zaplecza**i/lub **adresów IP zaplecza**.
1. Aby monitorować kondycję na wystąpienie wewnętrznej bazy danych lub wewnętrznej bazy, należy zastosować podział. 
    * Dzielenie notatek umożliwia wyświetlanie tylko pojedynczej metryki naraz. 
1. Na przykład, aby monitorować użycie SNAT dla przepływów TCP na komputerze, agreguj według **średniej, podzielonej**według **adresów IP wewnętrznej bazy** danych i filtrowania według **typu protokołu**. 

![Alokacja i użycie SNAT](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*Rysunek: Średnia alokacja i użycie portów TCP SNAT dla zestawu maszyn wirtualnych wewnętrznej bazy danych*

![Użycie SNAT przez wystąpienie wewnętrznej bazy danych](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*Rysunek: Użycie portu TCP SNAT na wystąpienie wewnętrznej bazy danych*
</details>

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Jak sprawdzić próby połączenia przychodzącego/wychodzącego dla mojej usługi?
<details>
  <summary>Rozwiń</summary>
Metryka pakietów SYN opisuje ilość pakietów TCP SYN, które dotarły lub zostały wysłane (dla [przepływów wychodzących),](https://aka.ms/lboutbound)które są skojarzone z określonym frontokiem. Za pomocą tej metryki można zrozumieć próby połączenia TCP z usługą.

Użyj **sumy** jako agregacji dla większości scenariuszy.

![Połączenie SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Rysunek: Liczba synów modułu równoważenia obciążenia*
</details>


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Jak sprawdzić zużycie przepustowości sieci? 
<details>
  <summary>Rozwiń</summary>
Metryka liczników bajtów i pakietów opisuje liczbę bajtów i pakietów wysyłanych lub odbieranych przez usługę na podstawie typu front-end.

Użyj **sumy** jako agregacji dla większości scenariuszy.

Aby uzyskać statystyki liczby bajtów lub pakietów:
1. Wybierz typ **metryki Liczba bajtów** i/lub **Liczba pakietów,** **a** agregacją jest średnia. 
2. Wykonaj jedną z następujących czynności:
   * Zastosuj filtr na określonym przednim adresie IP, porcie front-end, zapleczu IP lub portie zaplecza.
   * Uzyskaj ogólne statystyki zasobu modułu równoważenia obciążenia bez filtrowania.

![Liczba bajtów](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Rysunek: Liczba bajtów modułu równoważenia obciążenia*
</details>

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>Jak zdiagnozować wdrożenie modułu równoważenia obciążenia?
<details>
  <summary>Rozwiń</summary>
Za pomocą kombinacji dostępności VIP i metryki sondy kondycji na jednym wykresie można określić, gdzie szukać problemu i rozwiązać problem. Można uzyskać pewność, że platforma Azure działa poprawnie i użyć tej wiedzy, aby ostatecznie ustalić, że konfiguracja lub aplikacja jest główną przyczyną.

Można użyć metryki sondy kondycji, aby zrozumieć, jak platforma Azure wyświetla kondycję wdrożenia zgodnie z konfiguracją, która została udostępnione. Patrzenie na sondy kondycji jest zawsze doskonałym pierwszym krokiem w monitorowaniu lub określaniu przyczyny.

Możesz zrobić krok dalej i użyć metryk dostępności programu VIP, aby uzyskać wgląd w sposób, w jaki platforma Azure wyświetla kondycję podstawowej płaszczyzny danych, która jest odpowiedzialna za określone wdrożenie. Po połączeniu obu metryk można wyizolować, gdzie może być błąd, jak pokazano w tym przykładzie:

![Łączenie danych o dostępności ścieżki danych i metrykach stanu sondy kondycji](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Rysunek: Łączenie danych dostępności ścieżki danych i metryk stanu sondy kondycji*

Na wykresie wyświetlane są następujące informacje:
- Infrastruktura obsługująca maszyny wirtualne była niedostępna i na 0 procent na początku wykresu. Później infrastruktura była w dobrej kondycji, a maszyny wirtualne były osiągalne, a więcej niż jedna maszyna wirtualna została umieszczona w zapleczu. Te informacje są wskazywane przez niebieski śledzenia dostępności ścieżki danych (dostępność VIP), który był później na 100 procent. 
- Stan sondy kondycji (dostępność DIP), wskazywany przez fioletowy ślad, wynosi 0 procent na początku wykresu. Zakreślony obszar w kolorze zielonym podkreśla, gdzie stan sondy kondycji (dostępność DIP) stał się zdrowy i w którym momencie wdrożenie klienta było w stanie zaakceptować nowe przepływy.

Wykres umożliwia klientom samodzielne rozwiązywanie problemów z wdrożeniem bez konieczności odgadywania lub pytania o pomoc techniczną, czy występują inne problemy. Usługa była niedostępna, ponieważ sondy kondycji uległy awarii z powodu błędnej konfiguracji lub nieudanej aplikacji.
</details>

## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>Stan kondycji zasobu

Stan kondycji zasobów standardowego modułu równoważenia obciążenia jest narażony za pośrednictwem istniejącego **kondycji zasobów** w obszarze **Monitoruj kondycję usługi >**.

Aby wyświetlić kondycję publicznych zasobów standardowego modułu równoważenia obciążenia:
1. Wybierz pozycję > **Monitoruj kondycję usługi**. **Monitor**

   ![Strona monitora](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Rysunek: Łącze Kondycja usługi w usłudze Azure Monitor*

2. Wybierz **health resource**, a następnie upewnij się, że wybrano identyfikator **subskrypcji** i typ **zasobu = Moduł równoważenia obciążenia.**

   ![Stan kondycji zasobu](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Rysunek: Wybierz zasób dla widoku kondycji*

3. Na liście wybierz zasób Moduł równoważenia obciążenia, aby wyświetlić jego historyczny stan kondycji.

    ![Stan kondycji modułu równoważenia obciążenia](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Rysunek: Widok kondycji zasobu modułu równoważenia obciążenia*
 
Różne stany kondycji zasobów i ich opisy są wymienione w poniższej tabeli: 

| Stan kondycji zasobu | Opis |
| --- | --- |
| Dostępne | Standardowy zasób modułu równoważenia obciążenia jest w dobrej kondycji i dostępny. |
| Niedostępny | Standardowy zasób modułu równoważenia obciążenia nie jest w dobrej kondycji. Diagnozuj kondycję, wybierając**metryki** **usługi Azure Monitor** > .<br>(Stan*niedostępny* może również oznaczać, że zasób nie jest połączony ze standardowym modułem równoważenia obciążenia). |
| Nieznane | Stan kondycji zasobów dla standardowego zasobu modułu równoważenia obciążenia nie został jeszcze zaktualizowany.<br>*(Nieznany* stan może również oznaczać, że zasób nie jest połączony ze standardowym modułem równoważenia obciążenia).  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).
- Dowiedz się więcej o [łączności wychodzącej modułu równoważenia obciążenia](https://aka.ms/lboutbound).
- Dowiedz się więcej o [usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).
- Dowiedz się więcej o [interfejsie API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) i sposobie pobierania [metryk za pośrednictwem interfejsu API REST.](/rest/api/monitor/metrics/list)
