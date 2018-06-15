---
title: Diagnostyka standardowy moduł równoważenia obciążenia Azure | Dokumentacja firmy Microsoft
description: Korzystanie z dostępnych informacji metryki i kondycji diagnostyki Azure standardowe usługi równoważenia obciążenia.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: Kumud
ms.openlocfilehash: 9d5d596254f673b86650e8d9754dacdb70be0666
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32179798"
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Metryki i kondycji diagnostyki dla standardowych usługi równoważenia obciążenia

Azure standardowy moduł równoważenia obciążenia zapewnia zasobami następujące funkcje diagnostyczne:
* **Metryki wielowymiarowej**: udostępnia nowe funkcje diagnostyczne wielowymiarowych dla publicznych i wewnętrznych obciążenia konfiguracji usługi równoważenia. Można monitorować, zarządzanie i rozwiązywanie problemów z zasobami usługi równoważenia obciążenia.

* **Kondycja zasobów**: strony moduł równoważenia obciążenia w portalu Azure i strony kondycja zasobów (w obszarze Monitor) ujawnia sekcji zasobów kondycji dla konfiguracji usługi równoważenia obciążenia publicznego standardowe usługi równoważenia obciążenia.

W tym artykule przedstawiono szybki przegląd tych funkcji, jak również sposoby używania ich do standardowego modułu równoważenia obciążenia.

## <a name = "MultiDimensionalMetrics"></a>Metryki wielowymiarowej

Moduł równoważenia obciążenia Azure udostępnia nowe metryki wielowymiarowej za pośrednictwem nowe metryki Azure (wersja zapoznawcza) w portalu Azure i pomaga uzyskać równoważenia zasobów w czasie rzeczywistym diagnostycznych wgląd obciążenia. 

Różne konfiguracje standardowego modułu równoważenia obciążenia zawierają następujące metryki:

| Metryka | Typ zasobu | Opis | Zalecane agregacji |
| --- | --- | --- | --- |
| Dostępność adresu VIP (dostępność ścieżki danych) | Publiczny moduł równoważenia obciążenia | Standardowy moduł równoważenia obciążenia stale korzysta ze ścieżki danych z w obrębie regionu obciążenia równoważenia frontonie, aż do stosu SDN, który obsługuje maszyny Wirtualnej. Tak długo, jak utrzymać dobrej kondycji wystąpień, pomiar następuje taką samą ścieżkę aplikacji z równoważeniem obciążenia ruchu. Również jest weryfikowana w ścieżce danych używanego przez klientów. Miara jest niewidoczny dla aplikacji i nie koliduje z innych operacji.| Średnia |
| Dostępność DIP (stan sondy kondycji) |  Moduł równoważenia obciążenia publicznych oraz wewnętrznych | Standardowy moduł równoważenia obciążenia używa rozproszonego usługi badania kondycji, który monitoruje kondycję punkt końcowy aplikacji zgodnie z ustawieniami konfiguracji. Ta metryka zawiera zagregowaną lub na punkt końcowy filtrować widok każdego punktu końcowego wystąpienia w puli usługi równoważenia obciążenia. Widać, jak usługi równoważenia obciążenia widoków kondycji aplikacji, jak wskazano w bieżącej konfiguracji sondy kondycji. |  Średnia |
| SYN (należy zsynchronizować) pakietów |  Publiczny moduł równoważenia obciążenia | Standardowe usługi równoważenia obciążenia nie przerwanie połączenia protokołu Transmission Control Protocol (TCP) lub interakcji z przepływów pakietów TCP lub UDP. Przepływy i ich uzgodnienia są zawsze między serwerem źródłowym a wystąpienia maszyny Wirtualnej. Aby lepiej rozwiązać scenariuszy protokołu TCP, możesz wprowadzić użycie SYN liczniki pakietów, aby zrozumieć, jak wiele połączeń TCP prób. Metryka raporty o liczbie pakietów TCP SYN, które zostały odebrane.| Średnia |
| SNAT połączenia |  Publiczny moduł równoważenia obciążenia |Standardowa usługa równoważenia obciążenia raporty liczba przepływów wychodzących, które są masqueraded frontonie adres publiczny adres IP. Źródło porty adres tłumaczenia (SNAT) są zużywalnymi zasobami. Ta metryka może przedstawiać jak silnie aplikacji jest oparte na SNAT dla zdalnych przepływów wychodzących. Liczniki dla udane i nieudane przepływów wychodzących SNAT są raportowane i może służyć do rozwiązywania oraz zrozumienie kondycji przepływów wychodzących.| Średnia |
| Liczniki bajtów |  Moduł równoważenia obciążenia publicznych oraz wewnętrznych | Standardowa usługa równoważenia obciążenia raporty danych przetwarzanych na frontonu.| Średnia |
| Liczniki pakietów |  Moduł równoważenia obciążenia publicznych oraz wewnętrznych | Standardowa usługa równoważenia obciążenia raporty pakietów przetwarzanych na frontonu.| Średnia |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Wyświetl Twoje metryki usługi równoważenia obciążenia w portalu Azure

Azure portal udostępnia metryki usługi równoważenia obciążenia za pośrednictwem strony metryki (wersja zapoznawcza), który jest dostępny na obu obciążenia równoważenia zasobów strony dla określonego zasobu i Azure Monitor strony. 

Aby wyświetlić metryki dla zasobów standardowe usługi równoważenia obciążenia:
1. Przejdź do strony metryki (wersja zapoznawcza) i wykonaj jedną z następujących czynności:
   * Na stronie zasobów usługi równoważenia obciążenia wybierz typ metryki na liście rozwijanej.
   * Na stronie monitora Azure wybierz zasób usługi równoważenia obciążenia.
2. Ustaw typ odpowiednich agregacji.
3. Opcjonalnie Skonfiguruj wymagane filtrowania i grupowania.

![Podgląd metryki dla usługi równoważenia obciążenia standardowego](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*Rysunek: DIP dostępności i metryki stan sondy kondycji standardowe usługi równoważenia obciążenia*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Pobrać metryki wielowymiarowej programowo za pośrednictwem interfejsów API

Aby interfejs API pobierania wielowymiarowych definicji metryk i wartości, zobacz [interfejsu API REST Azure monitorowania wskazówki](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api).

### <a name = "DiagnosticScenarios"></a>Typowe scenariusze diagnostycznych i zalecane widoków

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Jest ścieżką danych i skonfigurowano do mojego modułu równoważenia obciążenia wirtualnego adresu IP?

Metryki dostępności VIP opisuje kondycji ścieżki danych do hosta obliczeniowego w regionie, w którym znajdują się maszyny wirtualne. Metryka wskazuje odbicie kondycję infrastruktury platformy Azure. Możesz użyć metrykę, aby:
- Monitorowanie dostępności usługi zewnętrzne
- Szczegółowej analizy i zrozumieć platformy, na którym wdrożono usługę jest w dobrej kondycji, lub czy Twojego systemu operacyjnego gościa lub wystąpienia aplikacji jest w dobrej kondycji.
- Określ, czy zdarzeń związanych z usługą lub podstawowej płaszczyzna danych. Nie należy mylić ta metryka ze stanem badania kondycji ("DIP availability").

Aby uzyskać dostępności VIP dla zasobów standardowe usługi równoważenia obciążenia:
1. Upewnij się, że wybrano zasobu usługi równoważenia obciążenia poprawne. 
2. W **Metryka** listy rozwijanej wybierz **dostępności VIP**. 
3. W **agregacji** listy rozwijanej wybierz **Avg**. 
4. Ponadto Dodaj filtr do adresu VIP lub port adresu VIP jako wymiar z wymagany adres IP frontonu portów frontonu i grupować według wybranego wymiaru.

![Sondowanie VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Rysunek: VIP usługi równoważenia obciążenia sondowanie szczegóły*

Metryka jest generowany przez aktywne, wewnątrzpasmowe miary. Usługa sondowania w regionie pochodzi ruchu dla miary. Usługa została aktywowana natychmiast Utwórz wdrożenie z publicznego frontonu i nadal przed usunięciem frontonu. 

>[!NOTE]
>Wewnętrzny front kończy się nie są obsługiwane w tej chwili. 

Generowany jest okresowo pakiet dopasowania danego wdrożenia frontonu i reguły. Wzdłuż region ze źródła do hosta którym znajduje się maszyn wirtualnych w puli zaplecza. Infrastruktury usługi równoważenia obciążenia wykonuje te same operacje równoważenia i translację obciążenia jak w przypadku wszystkich pozostałych rodzajów ruchu. To sondowanie jest wewnątrzpasmowe w punkcie końcowym z równoważeniem obciążenia. Po sondy dociera na hoście obliczeń, gdzie znajduje się dobra maszyn wirtualnych w puli zaplecza, hosta obliczeniowego generuje odpowiedzi z usługą sondowania. Maszyna wirtualna nie widzi tego ruchu.

Dostępność adresu VIP nie powiedzie się z następujących powodów:
- Wdrożenie ma dobrej kondycji maszyn wirtualnych w puli zaplecza. 
- Wystąpiła awaria infrastruktury.

W celach diagnostycznych, można użyć [metryki dostępności VIP oraz stan sondy kondycji](#vipavailabilityandhealthprobes).

Użyj **średni** jako agregacja w przypadku większości scenariuszy.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>Wystąpienia zaplecza dla moich wirtualnego adresu IP nie odpowiada na sond?

Metryka stan sondy kondycji opisano kondycji wdrażania aplikacji skonfigurowane przez użytkownika podczas konfigurowania sondy kondycji z usługi równoważenia obciążenia. Usługi równoważenia obciążenia używa stanu sondy kondycji w celu określenia, gdzie nowych przepływów. Sondy kondycji pochodzą z adresu infrastruktury platformy Azure i są widoczne w system operacyjny gościa maszyny wirtualnej.

Aby uzyskać DIP dostępności zasobów standardowe usługi równoważenia obciążenia:
1. Wybierz **dostępności DIP** metryki z **Avg** typ agregacji. 
2. Filtr wymagany adres IP wirtualnego adresu IP lub portu (lub obie).

![Dostępność DIP](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*Rysunek: Dostępność adresu VIP usługi równoważenia obciążenia*

Sondy kondycji się nie powieść z następujących powodów:
- Skonfiguruj badanie kondycji do portu, który nie nasłuchuje lub nie odpowiada lub używa tej nazwy nieprawidłowy protokół. Jeśli usługa korzysta z bezpośredniego zwrotu serwera (DSR lub zmienny adres IP) reguły, upewnij się, że usługa nasłuchuje na adresie IP konfiguracji adresu IP karty Sieciowej i nie tylko na sprzężenia zwrotnego, którego skonfigurowano IP frontonu adresów.
- Sondy sieci nie jest dozwolona przez grupę zabezpieczeń sieci, zapory systemu operacyjnego gościa maszyny Wirtualnej lub filtry warstwy aplikacji.

Użyj **średni** jako agregacja w przypadku większości scenariuszy.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Jak sprawdzić Moje statystyki wychodzące połączenie? 

Metryka połączeń SNAT opisano wielkość udane i nieudane połączenia dla [przepływów wychodzących](https://aka.ms/lboutbound).

Nieudane połączenia woluminu większym niż zero wskazuje SNAT wyczerpania portu. Musi należy zbadać dokładnie ustalenie, co może powodować błędy tego typu. Wyczerpanie portu SNAT manifesty jako Niepowodzenie ustanowienia [przepływu wychodzącego](https://aka.ms/lboutbound). Zapoznaj się z artykułem o połączeń wychodzących opisano scenariusze i mechanizmów w miejscu pracy oraz do Dowiedz się, jak ograniczyć i projektowania w celu uniknięcia SNAT wyczerpania portu. 

Aby wyświetlić statystyki połączeń SNAT:
1. Wybierz **połączeń SNAT** typu metryki i **suma** jako agregacji. 
2. Grupuj według **stan połączenia** przypadku udane i nieudane SNAT połączeń reprezentowanych przez różnych wierszy. 

![SNAT połączenia](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Rysunek: Liczba połączeń SNAT usługi równoważenia obciążenia*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Jak sprawdzić próby połączenia przychodzącego/wychodzącego dla moich usługi?

Metryka pakietów SYN opisano wielkość pakietów TCP SYN, które zostały dostarczone lub zostały wysłane (dla [przepływów wychodzących](https://aka.ms/lboutbound)) skojarzonych z określonym frontonu. Ta Metryka służy do zrozumienia TCP próby nawiązania połączenia z usługą.

Użyj **całkowita** jako agregacja w przypadku większości scenariuszy.

![SYN połączenia](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Rysunek: Liczba SYN usługi równoważenia obciążenia*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Jak sprawdzić Moje zużycie przepustowości sieci? 

Bajty i liczniki pakietów Metryka opisuje ilości bajtów i pakiety, które są wysyłane lub odbierane przez usługi na podstawie na wierzch — wewnętrzne.

Użyj **całkowita** jako agregacja w przypadku większości scenariuszy.

Aby wyświetlić statystyki liczba bajtów lub pakietów:
1. Wybierz **liczba bajtów** i/lub **liczba pakietów** typem metryki z **Avg** jako agregacja. 
2. Wykonaj jedną z następujących czynności:
   * Zastosuj filtr na określony adres IP frontonu, portów frontonu, wewnętrznego adresu IP lub portu zaplecza.
   * Pobierz ogólne statystyki dla zasobu usługi równoważenia obciążenia bez żadnego filtrowania.

![Liczba bajtów](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Rysunek: Liczba bajtów modułu równoważenia obciążenia*

#### <a name = "vipavailabilityandhealthprobes"></a>Jak diagnozować Mój. wdrożenie modułu równoważenia obciążenia?

Przy użyciu kombinacji adresu VIP dostępności i metryki sondy kondycji na jeden wykres można określić, gdzie Znajdź problemu i rozwiąż problem. Można uzyskać gwarantują, że Azure działa poprawnie i umożliwia wiedzy ostatecznie określić, że konfiguracja lub aplikacja jest główną przyczynę.

Metryki sondy kondycji służy do zrozumienia, jak Azure widoków kondycji wdrożenia zgodnie z harmonogramem konfiguracji, które zostały podane. Spojrzenie na sondy kondycji jest zawsze dużą pierwszy krok w monitorowania lub określanie przyczyny.

Można przełączyć go krok dalsze i Użyj metryki dostępności adresu VIP, aby uzyskać wgląd w sposób Azure widoków kondycji płaszczyzny danych podstawowych jest odpowiedzialny za określonego wdrożenia. Połączenie obu metryki można odizolować gdzie mogą być usterki, jak pokazano w poniższym przykładzie:

![Diagnostyka VIP](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*Rysunek: Łączenie DIP i VIP metryki dostępności*

Wykres zawiera następujące informacje:
- Infrastruktura sam był dobrej kondycji, infrastruktury obsługi maszyn wirtualnych był dostępny, a więcej niż jedna maszyna wirtualna została umieszczona w wewnętrznej. Te informacje jest określane przez niebieski śledzenia dostępności adresu VIP, który wynosi 100 procent. 
- Sondowanie stanu (dostępność DIP) jest jednak 0 procent na początku wykresu, wskazywany przez pomarańczowy śledzenia. Obszarze kółku zielony najważniejsze funkcje, których stan (dostępność DIP) stał się dobrej kondycji, i w tym momencie wdrożenia klienta mógł akceptować nowych przepływów.

Wykres umożliwia rozwiązywanie problemów z wdrożenia na ich własnych bez konieczności odgadnąć lub poproś pomocy technicznej, czy występują inne problemy. Usługa nie jest dostępny, ponieważ sondy kondycji niepowodzeniem z powodu błędnej konfiguracji lub aplikacji nie powiodło się.

### <a name = "Limitations"></a>Ograniczenia

Dostępność adresu VIP jest obecnie dostępny tylko dla publicznych front kończy się.

## <a name = "ResourceHealth"></a>Stan kondycji zasobu

Stan kondycji zasobów standardowe usługi równoważenia obciążenia jest uwidaczniany za pośrednictwem istniejące **kondycja zasobów** w obszarze **Monitor > kondycja usługi**.

>[!NOTE]
>Stan kondycji zasobu dla usługi równoważenia obciążenia jest obecnie dostępna tylko publicznej konfiguracji usługi równoważenia obciążenia standardowego. Zasoby usługi równoważenia obciążenia wewnętrznego lub zasoby podstawowe jednostki SKU z usługi równoważenia obciążenia nie ujawniaj kondycja zasobów.

Aby wyświetlić kondycję publicznego zasobów standardowe usługi równoważenia obciążenia:
1. Wybierz **Monitor** > **usługi kondycji**.

   ![Strona monitorowania](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Rysunek: Łącze usługi kondycji na monitorze Azure*

2. Wybierz **kondycja zasobów**i upewnij się, że **identyfikator subskrypcji** i **typ zasobu = modułu równoważenia obciążenia** są wybrane.

   ![Stan kondycji zasobu](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Rysunek: Wybierz zasób dla widoku kondycji*

3. Na liście Wybierz zasób usługi równoważenia obciążenia, aby wyświetlić jej stan kondycji historycznych.

    ![Stan kondycji modułu równoważenia obciążenia](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Rysunek: Widok kondycji zasobów usługi równoważenia obciążenia*
 
W poniższej tabeli wymieniono różne stany kondycji zasobów i ich opisy: 

| Stan kondycji zasobu | Opis |
| --- | --- |
| Dostępna | Zasób usługi równoważenia obciążenia standardowego publicznego jest dobra i dostępne. |
| Niedostępny | Zasób usługi równoważenia obciążenia standardowego publicznego nie jest dobra. Diagnozowanie kondycji wybierając **Azure Monitor** > **metryki**.<br>(*Niedostępny* stanu również może oznaczać, że zasób nie jest połączony z Twojej publiczny standardowy moduł równoważenia obciążenia.) |
| Brak informacji | Stan kondycji zasobu dla zasobu usługi równoważenia obciążenia standardowego publiczny nie została jeszcze zaktualizowana.<br>(*Nieznany* stanu również może oznaczać, że zasób nie jest połączony z Twojej publiczny standardowy moduł równoważenia obciążenia.)  |

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).
- Dowiedz się więcej o sieci [łączność wychodząca usługi równoważenia obciążenia](https://aka.ms/lboutbound).


