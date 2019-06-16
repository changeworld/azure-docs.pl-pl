---
title: Diagnostyka Azure Load Balancer w warstwie standardowa
titlesuffix: Azure Load Balancer
description: Na użytek dostępne metryki i informacje o kondycji dla diagnostyki Azure Standard Load Balancer.
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2019
ms.author: Kumud
ms.openlocfilehash: ec68038a5b0fe7edca095e0d9b190d5da09c8e82
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66754712"
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Metryki i kondycji diagnostyki dla standardowego modułu równoważenia obciążenia

Usługa Azure Load Balancer Standard uwidacznia standardowa usługi Azure Load Balancer zapewnia następujące funkcje diagnostyczne zasobów:
* **Metryk wielowymiarowych**: Oferuje nowe funkcje diagnostyczne wielowymiarowych za pośrednictwem [usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) publicznych i wewnętrznych obciążenia konfiguracji modułu równoważenia. Można monitorować, zarządzanie i rozwiązywanie problemów z zasobami usługi równoważenia obciążenia.

* **Kondycja zasobu**: Na stronie modułu równoważenia obciążenia w witrynie Azure portal i na stronie kondycji zasobów (w obszarze monitorowanie) ujawniają sekcji kondycja zasobu dla konfiguracji modułu równoważenia obciążenia publiczny Standard Load Balancer.

W tym artykule przedstawiono szybki przegląd tych możliwości, jak również sposoby używania ich do standardowego modułu równoważenia obciążenia.

## <a name = "MultiDimensionalMetrics"></a>Metryk wielowymiarowych

Usługa Azure Load Balancer zapewnia nowych metryk wielowymiarowych za pomocą nowych metryk platformy Azure w witrynie Azure portal oraz pomaga analiz w czasie rzeczywistym diagnostycznych do obciążenia równoważenia zasobów. 

Różne konfiguracje standardowego modułu równoważenia obciążenia zapewnia następujące metryki:

| Metryka | Typ zasobu | Opis | Zalecane agregacji |
| --- | --- | --- | --- |
| Dane ścieżki dostępność (dostępność adresu VIP)| Publiczny moduł równoważenia obciążenia | Stale Standard Load Balancer, która korzysta ze ścieżki danych z w obrębie regionu do frontonu modułu równoważenia obciążenia, aż do stosu SDN, który obsługuje Twoja maszyna wirtualna. Tak długo, jak utrzymać dobrej kondycji wystąpień, pomiar następuje taką samą ścieżkę ruchu ze zrównoważonym obciążeniem w danej aplikacji. Ścieżki danych, których używają Twoi klienci jest również sprawdzany. Miara jest niewidoczny dla aplikacji i nie kolidują z innymi operacjami.| Średnia |
| Stan sondy kondycji (dostępność DIP) |  Moduł równoważenia obciążenia publicznych i wewnętrznych | Standardowy moduł równoważenia obciążenia używa rozproszona usługa badania kondycji, która monitoruje kondycję swojej aplikacji punktu końcowego zgodnie z ustawieniami konfiguracji. Ta metryka zapewnia funkcję agregującą lub na punkt końcowy filtrowany widok każdego wystąpienia punktu końcowego puli modułu równoważenia obciążenia. Widać, jak moduł równoważenia obciążenia Wyświetla kondycję swojej aplikacji, jak wskazano w bieżącej konfiguracji sondy kondycji. |  Średnia |
| SYN (Synchronizuj) pakietów |  Publiczny moduł równoważenia obciążenia | Load Balancer w warstwie standardowa zakończyć połączenia Transmission Control Protocol (TCP) lub nie korzystać z protokołu TCP lub UDP przepływów pakietów. Przepływów i ich uzgodnienia są zawsze między źródłem i wystąpienie maszyny Wirtualnej. Aby lepiej rozwiązywać scenariuszy protokołu TCP, można skorzystać z SYN liczniki pakietów, aby zrozumieć, jak wiele połączeń TCP prób. Metryka zgłasza liczbę pakietów TCP SYN, które zostały odebrane.| Średnia |
| Połączeń SNAT |  Publiczny moduł równoważenia obciążenia |Load Balancer w warstwie standardowa zgłasza liczbę przepływy wychodzące, które są masqueraded frontonie adres publiczny adres IP. Porty tłumaczenia (SNAT) adres sieci źródłowe są wyczerpującymi się zasobami. Ta metryka może stanowić wskazówkę stopnia aplikacji powołuje się na SNAT dla ruchu wychodzącego przepływów. Liczniki dla udane i nieudane przepływy wychodzące SNAT są zgłaszane i może służyć do rozwiązywania oraz zrozumieć kondycję swoich przepływów ruchu wychodzącego.| Średnia |
| Liczniki bajtów |  Moduł równoważenia obciążenia publicznych i wewnętrznych | Load Balancer w warstwie standardowa raporty danych przetwarzanych na frontonie.| Średnia |
| Liczniki pakietów |  Moduł równoważenia obciążenia publicznych i wewnętrznych | Load Balancer w warstwie standardowa raporty pakietów przetwarzanych na frontonie.| Średnia |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Wyświetlaj swoje metryki modułu równoważenia obciążenia w witrynie Azure portal

Witryna Azure portal udostępnia metryki modułu równoważenia obciążenia za pośrednictwem strony metryk, który jest dostępny na stronie zasobów modułu równoważenia obciążenia dla określonego zasobu i na stronie usługi Azure Monitor. 

Aby wyświetlić metryki dla zasobów usługi Load Balancer w warstwie standardowa:
1. Przejdź do strony metryki i wykonaj jedną z następujących czynności:
   * Na stronie zasobów modułu równoważenia obciążenia wybierz typ metryki na liście rozwijanej.
   * Na stronie usługi Azure Monitor wybierz zasób modułu równoważenia obciążenia.
2. Ustaw typ odpowiednich agregacji.
3. Opcjonalnie można skonfigurować wymagane filtrowanie i grupowanie.

    ![Metryki dla standardowego modułu równoważenia obciążenia](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

    *Rysunek: Metryki dostępności ścieżki danych dla standardowego modułu równoważenia obciążenia*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Pobieranie metryk wielowymiarowych programowo za pośrednictwem interfejsów API

Aby uzyskać wskazówki dotyczące interfejsów API do pobierania definicji metryk wielowymiarowych i wartości, zobacz [wskazówki interfejsu API REST usługi Azure Monitoring](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api).

### <a name = "DiagnosticScenarios"></a>Typowe scenariusze diagnostycznych i zalecane widoków

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Jest ścieżka danych, włączone i dostępne dla mojego modułu równoważenia obciążenia wirtualnego adresu IP?

Metryki dostępności adresu VIP w tym artykule opisano kondycji ścieżki danych w regionie w celu hosta obliczeniowych, gdzie znajdują się maszyny wirtualne. Metryka wskazuje odbicia kondycję infrastruktury platformy Azure. Możesz użyć metrykę, aby:
- Monitorowanie dostępności usługi zewnętrzne
- Szczegółowe informacje i zrozumieć platformy, na którym wdrożono usługę jest w dobrej kondycji, lub czy Twojego systemu operacyjnego gościa lub wystąpienie aplikacji jest w dobrej kondycji.
- Wyizolować, czy zdarzenie dotyczy usługi podstawowej płaszczyzny danych. Nie należy mylić tej metryki ze stanem sondy kondycji ("DIP availability").

Aby uzyskać dostępność ścieżki danych dla zasobów usługi Load Balancer w warstwie standardowa:
1. Upewnij się, że jest zaznaczona opcja zasobu modułu równoważenia obciążenia poprawne. 
2. W **metryki** listy rozwijanej wybierz **dostępność ścieżki danych**. 
3. W **agregacji** listy rozwijanej wybierz **Avg**. 
4. Ponadto Dodaj filtr adresów IP frontonu lub port frontonu, jak wymiar o wymagany adres IP frontonu lub port frontonu i pogrupować je według wybranych wymiarów.

![Sondowanie adresów VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Rysunek: Obciążenia równoważenia frontonu sondowania informacji*

Metryka jest generowany przez aktywne, wewnątrzpasmowe miary. Usługa badania w obrębie regionu pochodzi ruch do pomiaru. Usługa została aktywowana, natychmiast Utwórz wdrożenie z publicznych frontonu i kontynuuje, dopóki nie usuniesz frontonu. 

>[!NOTE]
>Wewnętrznych frontonów nie są obsługiwane w tej chwili. 

Generowany jest okresowo pakiet dopasowanie wdrożenia frontonu i reguły. Przez region ze źródła do hosta, gdzie znajduje się maszyny Wirtualnej w puli zaplecza. Infrastruktura równoważenia obciążenia wykonuje obciążenia równoważenia i tłumaczenie operacji, jak w przypadku cały pozostały ruch. To sondowanie jest wewnątrzpasmowe na punkt końcowy z równoważeniem obciążenia. Po sondy nadejściu na hoście obliczeniowych, gdzie znajduje się dobrej kondycji maszyn wirtualnych w puli zaplecza, hosta compute generuje odpowiedzi do sondowania usługi. Maszyna wirtualna nie widzi tego ruchu.

Adres VIP dostępności nie powiodła się z następujących powodów:
- Wdrożenie nie ma żadnych dobrej kondycji maszyn wirtualnych pozostających w puli zaplecza. 
- Wystąpiła awaria infrastruktury.

Do celów diagnostycznych, możesz użyć [metryki dostępności ścieżki danych wraz z stanem sondy kondycji](#vipavailabilityandhealthprobes).

Użyj **średni** jako agregacji w przypadku większości scenariuszy.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>Wystąpień zaplecza dla mojej wirtualnego adresu IP odpowiada na sondy?

Metryki stanie sondy kondycji opisuje kondycji wdrożenia aplikacji, zgodnie z konfiguracją przez Ciebie, po skonfigurowaniu sondy kondycji modułu równoważenia obciążenia. Moduł równoważenia obciążenia używa stan sondę kondycji, aby określić, gdzie wysyłać nowych przepływów. Sondy kondycji pochodzą z adresu infrastruktury platformy Azure i są widoczne w systemie operacyjnym gościa maszyny wirtualnej.

Aby wyświetlić stan sondy kondycji dla zasobów usługi Load Balancer w warstwie standardowa:
1. Wybierz **stan sondy kondycji** metryk z **Avg** typ agregacji. 
2. Zastosuj filtr na wymagany adres IP frontonu lub portu (lub obu).

Sondy kondycji się nie powieść z następujących powodów:
- Konfigurowanie sondy kondycji do portu, który jest nie nasłuchuje lub Agent nie odpowiada lub używa tej nazwy nieprawidłowy protokół. Jeśli usługa korzysta z bezpośredniego zwrotu z serwera (DSR, lub pływający adres IP) reguły, upewnij się, czy usługa nasłuchuje na adresie IP w konfiguracji adresu IP karty Sieciowej i nie tylko na sprzężenie zwrotne, którego skonfigurowano adres IP frontonu adresów.
- Sonda usługi nie jest dozwolona przez sieciową grupę zabezpieczeń, Zapora systemu operacyjnego gościa maszyny Wirtualnej lub filtry warstwy aplikacji.

Użyj **średni** jako agregacji w przypadku większości scenariuszy.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Jak sprawdzić Moje statystyki połączenia wychodzące? 

Metryki połączeń SNAT opisuje wielkość udane i nieudane połączenia dla [przepływy wychodzące](https://aka.ms/lboutbound).

Połączenia zakończone niepowodzeniem woluminu większym niż zero wskazuje SNAT wyczerpanie portów. Użytkownik musi dalsze badanie w celu ustalenia, co może powodować awarie tych. Wyczerpanie portów SNAT manifesty jako nieudanej próbie nawiązania [przepływu wychodzącego](https://aka.ms/lboutbound). Zapoznaj się z artykułem na temat połączeń wychodzących zrozumieć scenariusze i mechanizmy w miejscu pracy, a także Dowiedz się, jak rozwiązać i projektowanie pod kątem unikania SNAT wyczerpanie portów. 

Aby wyświetlić statystyki połączeń SNAT:
1. Wybierz **połączeń SNAT** typu metryki i **suma** jako agregacji. 
2. Grupuj według **stan połączenia** dla udane i nieudane liczby połączeń SNAT, które są reprezentowane przez linie różnych. 

![Połączeń SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Rysunek: Liczba połączeń SNAT modułu równoważenia obciążenia*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Jak sprawdzić prób nawiązania połączenia przychodzącego/wychodzącego dla mojej usługi?

Metryka pakietów SYN opisuje woluminu TCP SYN pakiety, które zostały dostarczone lub zostały wysłane (dla [przepływy wychodzące](https://aka.ms/lboutbound)) które są skojarzone z określonym frontonu. Ta Metryka służy do zrozumienia próby nawiązania połączenia TCP z usługą.

Użyj **całkowita** jako agregacji w przypadku większości scenariuszy.

![SYN połączenia](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Rysunek: Liczba SYN modułu równoważenia obciążenia*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Jak sprawdzić mojej zużycie przepustowości sieci? 

Bajtów i pakietów liczników, metryki w tym artykule opisano ilości bajtów i pakietów, które są wysyłane lub odbierane przez usługę na podstawie poszczególnych front-end.

Użyj **całkowita** jako agregacji w przypadku większości scenariuszy.

Aby uzyskać pakiet lub bajt statystykę wyrazów:
1. Wybierz **liczba bajtów** i/lub **liczba pakietów** typu metryki z **Avg** jako agregacja. 
2. Wykonaj jedną z następujących czynności:
   * Zastosuj filtr na określony adres IP frontonu, portu frontonu, adresu IP zaplecza lub portu zaplecza.
   * Pobierz ogólne statystyki dla zasobu modułu równoważenia obciążenia, bez żadnego filtrowania.

![Liczba bajtów](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Rysunek: Liczba bajtów modułu równoważenia obciążenia*

#### <a name = "vipavailabilityandhealthprobes"></a>Jak zdiagnozować Moje wdrożenie modułu równoważenia obciążenia?

Przy użyciu kombinacji adresu VIP dostępności i metryki sondy kondycji na pojedynczym wykresie można wskazać, gdzie szukać problem i rozwiązać problem. Można uzyskać pewność, że Azure działa poprawnie i wykorzystanie tej wiedzy do ostatecznie określić, że główną przyczyną jest konfiguracja lub aplikacji.

Aby dowiedzieć się, jak Azure widoków kondycji wdrożenia zgodnie z konfiguracji, które zostały podane, można użyć metryki sondy kondycji. Patrząc sond kondycji jest zawsze pierwszy wielki krok monitorowania lub określanie przyczynę.

Można zabrać krok dalej i Użyj metryki dostępności adresu VIP, aby uzyskać wgląd w sposób Azure widoków kondycji podstawowych płaszczyzny danych, który jest odpowiedzialny za szczególnego wdrożenia. Łącząc oba wskaźniki, można izolować gdzie mogą być usterki, jak pokazano w tym przykładzie:

![Łącząc metryki dostępności ścieżki danych i stanu sondy kondycji](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Rysunek: Łącząc metryki dostępności ścieżki danych i stanu sondy kondycji*

Na wykresie kreślone są następujące informacje:
- Infrastruktury hostingu maszyn wirtualnych była niedostępna i na wartość 0 procent na początku wykresu. Później infrastruktura była w dobrej kondycji i maszyny wirtualne stanowiły dostępny i więcej niż jedna maszyna wirtualna została umieszczona w wewnętrznej. Informacja ta jest wskazywany przez niebieski śledzenia dostępności ścieżki danych (dostępność adresu VIP), będącą nowsze do 100 procent. 
- Sondy stanu kondycji (dostępność DIP), wskazywane przez śledzenie purpurowy wynosi 0 procent na początku wykresu. Kółku obszar zielony najważniejsze funkcje, których stan sondy kondycji (dostępność DIP) stało się dobrej kondycji, a w tym momencie wdrażania klienta mógł akceptować nowych przepływów.

Wykres umożliwia klientom Rozwiązywanie problemów z wdrożeniem w ich własnych bez konieczności odgadnięcia lub poproś pomocy technicznej, czy występują inne problemy. Usługa była niedostępna, ponieważ sond kondycji kończył się niepowodzeniem z powodu błędnej konfiguracji lub aplikacji nie powiodło się.

### <a name = "Limitations"></a>Ograniczenia

Dostępność adresu VIP jest obecnie dostępna tylko w przypadku publicznych frontonów.

## <a name = "ResourceHealth"></a>Stan kondycji zasobu

Stan kondycji zasobów Balancer w warstwie standardowa jest uwidaczniany za pomocą istniejącego **Resource health** w obszarze **Monitor > Service Health**.

>[!NOTE]
>Kondycja zasobu dla modułu równoważenia obciążenia jest obecnie dostępny dla tylko publiczne Konfiguracja standardowego modułu równoważenia obciążenia. Zasobów modułu równoważenia obciążenia wewnętrznego lub zasobów podstawowego jednostki SKU z modułu równoważenia obciążenia nie ujawniaj kondycji zasobu.

Aby wyświetlić kondycję zasobów publicznych standardowego modułu równoważenia obciążenia:
1. Wybierz **Monitor** > **kondycji usługi**.

   ![Strona monitorowania](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Rysunek: Link kondycji usługi Azure Monitor*

2. Wybierz **Resource Health**, a następnie upewnij się, że **identyfikator subskrypcji** i **typ zasobu = modułu równoważenia obciążenia** są zaznaczone.

   ![Stan kondycji zasobu](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Rysunek: Wybierz zasób dla widoku kondycji*

3. Na liście wybierz zasobów modułu równoważenia obciążenia, aby wyświetlić jej stan kondycji historycznych.

    ![Stan kondycji modułu równoważenia obciążenia](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Rysunek: Widoku kondycji zasobu modułu równoważenia obciążenia*
 
W poniższej tabeli przedstawiono różne stany kondycji zasobów i ich opisy: 

| Stan kondycji zasobu | Opis |
| --- | --- |
| Dostępne | Zasób modułu równoważenia obciążenia standardowy publiczny jest w dobrej kondycji i dostępności. |
| Niedostępny | Zasób modułu równoważenia obciążenia standardowy publiczny nie jest w dobrej kondycji. Diagnozowanie kondycji, wybierając **usługi Azure Monitor** > **metryki**.<br>(*Unavailable* stan może także oznaczać, że zasób nie jest połączony z modułu równoważenia obciążenia standardowego publicznego.) |
| Nieznane | Kondycja zasobu dla zasobu modułu równoważenia obciążenia standardowy publiczny nie został jeszcze zaktualizowany.<br>(*Nieznany* stan może także oznaczać, że zasób nie jest połączony z modułu równoważenia obciążenia standardowego publicznego.)  |

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).
- Dowiedz się więcej o swojej [łączności wychodzącej modułu równoważenia obciążenia](https://aka.ms/lboutbound).
- Dowiedz się więcej o [usługa Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).
- Dowiedz się więcej o [interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) i [sposób pobierania metryk za pośrednictwem interfejsu API REST](/rest/api/monitor/metrics/list).


