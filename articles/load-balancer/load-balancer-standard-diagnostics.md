---
title: Usługi równoważenia obciążenia standardowego Azure - diagnostyki | Dokumentacja firmy Microsoft
description: Za pomocą dostępnych informacji metryki i kondycji diagnostyki dla usługi równoważenia obciążenia standardowego Azure
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
ms.openlocfilehash: 7d60925381abe617f6e2fac51176b8e30517c3ba
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Metryki i kondycji diagnostyki dla standardowych usługi równoważenia obciążenia

Azure standardowy moduł równoważenia obciążenia zapewnia następujące funkcje diagnostyczne dla zasobów:
* Metryki wielowymiarowej: Standardowy moduł równoważenia obciążenia udostępnia nowe funkcje diagnostyczne wielowymiarowych dla publicznych i wewnętrznych konfiguracji usługi równoważenia obciążenia. Umożliwia monitorowanie, zarządzanie i rozwiązywanie problemów z zasobami usługi równoważenia obciążenia.

* Kondycja zasobów: Strony usługi równoważenia obciążenia w portalu Azure i strony kondycja zasobów (w obszarze Monitor) ujawnia kondycja zasobów dla konfiguracji publiczny moduł równoważenia obciążenia standardowego modułu równoważenia obciążenia.

W tym artykule przedstawiono szybki przegląd te możliwości oraz sposobów ich używać do standardowego modułu równoważenia obciążenia.

## <a name = "MultiDimensionalMetrics"></a>Metryki wielowymiarowej

Moduł równoważenia obciążenia Azure udostępnia nowe metryki wielowymiarowej za pośrednictwem nowe metryki Azure (wersja zapoznawcza) w portalu i pomagają w czasie rzeczywistym diagnostycznych wgląd w zasoby usługi równoważenia obciążenia. 

Obecnie dla różnych konfiguracji usługi równoważenia obciążenia standardowego (LB) dostępne są następujące metryki:

| Metryka | Typ zasobu | Opis | Zalecane agregacji |
| --- | --- | --- | --- |
| Dostępność adresu VIP (dostępność ścieżki danych) | Public LB | Standardowa usługa równoważenia obciążenia stale wykonuje ścieżka danych od w obrębie regionu do frontonu modułu równoważenia obciążenia aż do stosu SDN, który obsługuje maszyny Wirtualnej. Tak długo, jak utrzymać dobrej kondycji wystąpień, pomiar następuje taką samą ścieżkę aplikacji z równoważeniem obciążenia ruchu. Ścieżka danych, który jest używany przez klientów również jest weryfikowana. Miara jest niewidoczny dla aplikacji i nie koliduje z innych operacji.| Średnia |
| Dostępność DIP (stan sondy kondycji) |  Public & Internal LB | Standardowy moduł równoważenia obciążenia używa rozproszonej kondycję sondowanie usługi, który monitoruje kondycję punkt końcowy aplikacji zgodnie z ustawieniami konfiguracji. Ta metryka zapewnia agregacji lub na punkt końcowy filtrowane widoku każdego punktu końcowego poszczególnych wystąpień w usłudze równoważenia obciążenia w puli.  Widać, jak usługa równoważenia obciążenia widoków kondycji aplikacji wskazywany przez konfigurację sondy kondycji. |  Średnia |
| SYN pakietów |  Public LB | Standardowe usługi równoważenia obciążenia nie przerwanie połączenia TCP lub interakcji z przepływów pakietów TCP lub UDP. Przepływy i ich uzgodnienia są zawsze między serwerem źródłowym a wystąpienia maszyny Wirtualnej. Aby lepiej rozwiązać scenariuszy protokołu TCP, możesz wprowadzić użycie SYN liczniki pakietów, aby zrozumieć, jak wiele połączeń TCP prób. Metryka raporty o liczbie pakietów TCP SYN, które zostały odebrane.| Średnia |
| SNAT połączenia |  Public LB |Standardowa usługa równoważenia obciążenia raporty liczba przepływów wychodzących, które są masqueraded do publicznego adresu IP frontonu adres. Porty SNAT są zużywalnymi zasobami. Ta metryka może przedstawiać jak silnie aplikacji jest oparte na SNAT dla zdalnych przepływów wychodzących.  Liczniki dla udane i nieudane przepływów wychodzących SNAT są raportowane i może służyć do rozwiązywania oraz zrozumienie kondycji przepływów wychodzących.| Średnia |
| Liczniki bajtów |  Public & Internal LB | Standardowa usługa równoważenia obciążenia raporty danych przetwarzanych na frontonie.| Średnia |
| Liczniki pakietów |  Public & Internal LB | Standardowa usługa równoważenia obciążenia raporty pakietów przetwarzanych na frontonie.| Średnia |

### <a name="view-load-balancer-metrics-in-the-portal"></a>Wyświetl metryki usługi równoważenia obciążenia w portalu

Azure portal udostępnia metryki usługi równoważenia obciążenia za pośrednictwem strony metryki (wersja zapoznawcza), który jest dostępny na stronie zasobów usługi równoważenia obciążenia dla określonego zasobu usługi równoważenia obciążenia, a także na stronie Azure Monitor. 

Aby wyświetlić metryki dla zasobów standardowy moduł równoważenia obciążenia, przeglądania metryki (wersja zapoznawcza) w jednej z tych lokalizacji, wybierz (obciążenia modułu równoważenia zasobów w obszarze strony Monitor) Metryka typ z listy rozwijanej, ustaw odpowiedni typ agregacji i, opcjonalnie, Skonfiguruj wymagane filtrowanie i grupowanie i będzie możliwe wyświetlić widok historycznych na podstawie metryk warunkach podana.

![Podgląd metryki dla usługi równoważenia obciążenia standardowego](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*Rysunek - dostępności DIP / kondycji sondowania stanu metryki dla usługi równoważenia obciążenia*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Pobrać metryki wielowymiarowej programowo za pośrednictwem interfejsów API

Wskazówki dotyczące interfejsu API do pobierania Metryka wielowymiarowe definicje i wartości jest dostępna na [monitorowania Walkthrouh interfejsu API REST > interfejsu API wielowymiarowe](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api)

### <a name = "DiagnosticScenarios"></a>Typowe scenariusze diagnostycznych i zalecane widoków

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Jest ścieżką danych, konfigurowania i dostępne dla moich VIP usługi równoważenia obciążenia?

Metryki dostępności VIP opisuje kondycji ścieżki danych do hosta obliczeniowego w regionie, w którym znajdują się maszyny wirtualne. Jest odbicia kondycję infrastruktury platformy Azure. Możesz użyć tej metryki, aby:
- Monitorowanie dostępności usługi zewnętrzne
- Szczegółowej analizy i zrozumieć czy platformy, na którym wdrożono usługę jest w dobrej kondycji, czy też z systemu operacyjnego gościa lub wystąpienia aplikacji są w dobrej kondycji
- Określ, czy zdarzeń związanych z usługą lub podstawowej płaszczyzna danych. Nie należy mylić to ze stanem badania kondycji ("DIP availability").

Aby uzyskać dostępności VIP dla zasobów standardowe usługi równoważenia obciążenia:
- Upewnij się, że wybrano poprawny zasobu usługi równoważenia obciążenia. 
- Wybierz **dostępności VIP** z **Metryka** listy rozwijanej. 
- Wybierz **Avg** dla **agregacji**. 
- Ponadto Dodaj filtr do adresu VIP lub port adresu VIP wymiaru przy użyciu adresu IP frontonu wymagane lub portów frontonu i grupy przez wybranego wymiaru.

![Sondowanie VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Rysunek - sondowanie szczegóły VIP usługi równoważenia obciążenia*

Metryka jest generowany przez aktywne, wewnątrzpasmowe miary. Sondowania usługi w obrębie regionu pochodzi ruchu dla tego pomiaru i jest aktywowana, jak Utwórz wdrożenie z publicznego serwera sieci Web i będzie kontynuowane do czasu usunięcia serwera sieci Web. 

>[!NOTE]
>Wewnętrzny frontends nie są obsługiwane w tej chwili. 

Generowany jest okresowo pakiet dopasowania frontonu i reguły danego wdrożenia. Wzdłuż region ze źródła do hosta którym znajduje się maszyn wirtualnych w puli zaplecza. Infrastruktury usługi równoważenia obciążenia będzie wykonywać te same operacje równoważenia i translację obciążenia jak w przypadku wszystkich pozostałych rodzajów ruchu. To sondowanie jest wewnątrzpasmowe na obciążenia zrównoważonym punktu końcowego. Po sondy dociera hosta obliczeniowego, w którym znajduje się dobra maszyn wirtualnych w puli zaplecza, hosta obliczeniowego wygeneruje odpowiedzi z usługą sondowania. Maszyna wirtualna nie widzi tego ruchu.
Dostępność adresu VIP nie powiedzie się z następujących powodów:
- Wdrożenie ma dobrej kondycji maszyn wirtualnych w puli zaplecza. 
- Wystąpił awarii infrastruktury, który powoduje, że dostępność adresu VIP, aby zakończyć się niepowodzeniem.

Można użyć [metryki dostępności VIP oraz stan sondy kondycji do celów diagnostycznych](#vipavailabilityandhealthprobes).

Użyj **średni** jako agregacja w przypadku większości scenariuszy.

#### <a name="are-the-backend-instances-for-my-vip-responding-to-probes"></a>Wystąpienia wewnętrznej bazy danych dla moich wirtualnego adresu IP nie odpowiada na sond?

Metryka stan sondy kondycji opisano kondycji wdrażania aplikacji skonfigurowane przez użytkownika podczas konfigurowania sondy kondycji modułu równoważenia obciążenia. Moduł równoważenia obciążenia używa stanu sondy kondycji ustalenie, który ma zostać wysłana nowych przepływów. Sondy kondycji pochodzą z adresu infrastruktury platformy Azure i są widoczne w system operacyjny gościa maszyny wirtualnej.

Aby uzyskać dostępności DIP zasobami standardowe usługi równoważenia obciążenia
- Wybierz **dostępności DIP** metryki z **Avg** typ agregacji. 
- Filtr wymagany adres IP wirtualnego adresu IP lub portu (lub obie).

![Dostępność DIP](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*Rysunek — dostępność adresu VIP usługi równoważenia obciążenia*

Sondy kondycji się nie powieść z następujących powodów:
- Jeśli skonfigurujesz sondy kondycji, portu, na którym nie nasłuchuje lub nie odpowiada lub nieprawidłowy protokół. Usługa korzysta z reguł DSR (pływającego adresu IP), należy najpierw upewnij się, że usługa nasłuchuje na adresie IP konfiguracji adresu IP karty Sieciowej, a nie tylko na sprzężenia zwrotnego skonfigurowano frontonu adresu IP.
- Sonda sieci nie jest dozwolone przez sieciowej grupy zabezpieczeń, filtry zapory systemu operacyjnego gościa maszyny Wirtualnej lub warstwy aplikacji.

Użyj **średni** jako agregacja w przypadku większości scenariuszy.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Jak sprawdzić Moje statystyki wychodzące połączenie? 

Metryka SNAT połączenia opisano wielkość udane i nieudane (dla [przepływów wychodzących](https://aka.ms/lboutbound)).

Większa niż zero oznacza wyczerpania portu SNAT woluminu połączenia nie powiodło się. Należy zbadać i określić, co może powodować błędy tego typu. Manifesty wyczerpania portu SNAT jako błędy, aby ustanowić [przepływów wychodzących](https://aka.ms/lboutbound). Zapoznaj się z artykułem na połączenia wychodzące, aby zrozumieć scenariusze mechanizmów w pracy i jak ograniczyć / projektowania w celu uniknięcia SNAT wyczerpania portu. 

Aby uzyskać statystyki połączeń SNAT,
- Wybierz **połączeń SNAT** typu metryki i **suma** jako agregacji. 
- Grupuj według **stan połączenia** udane i nieudane połączenia SNAT liczby reprezentowanego przez różnych wierszy. 

![SNAT połączenia](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Rysunek - SNAT liczba połączeń dla usługi równoważenia obciążenia*


#### <a name="how-do-i-check-inbound--outbound-connection-attempts-for-my-service"></a>Jak sprawdzić próby połączenia przychodzącego / wychodzącego dla moich usługi?

Metryka pakietów SYN opisano wielkość pakietów TCP SYN, które zostały dostarczone lub zostały wysłane (dla [przepływów wychodzących](https://aka.ms/lboutbound)) skojarzone z danym frontonu. Ta metryka może służyć do zrozumienia TCP próby połączenia z usługą.
Użyj łącznie jako agregacji dla większości scenariuszy.

![SYN połączenia](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Rysunek — liczba SYN dla usługi równoważenia obciążenia*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Jak sprawdzić Moje zużycie przepustowości sieci? 

Byte / Metryka liczniki pakietów w tym artykule opisano ilości bajtów i pakiety wysyłane lub odbierane przez usługę na podstawie na frontonie.
Użyj **całkowita** jako agregacja w przypadku większości scenariuszy.

Aby uzyskać statystyki liczba bajtów lub pakietów
- Wybierz **liczba bajtów** i / lub **liczba pakietów** metryki typu o **Avg** jako agregacja. 
- Zastosuj filtr adresu IP określonego frontonu, portu frontonu lub adresu IP zaplecza lub danego portu. 
- lub Załóż ogólne statystyki dla zasobu usługi równoważenia obciążenia bez żadnego filtrowania.


Niektóre widoki przykład dla metryki z różnymi konfiguracjami-

![Liczba bajtów](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Rysunek — liczba bajtów dla usługi równoważenia obciążenia*

#### <a name = "vipavailabilityandhealthprobes"></a>Jak diagnozować Moje wdrożenie modułu równoważenia obciążenia?

Kombinacja adresu VIP dostępności & sondy kondycji metryki dotyczące pojedynczego wykresu można umożliwia określenie miejsca Znajdź problemu i rozwiąż problem. Można uzyskać gwarantują, że Azure działa poprawnie i użyj go ostatecznie ustalić konfiguracji lub aplikacja jest główną przyczynę.

Metryki sondy kondycji służy do zrozumienia, jak Azure widoków kondycji wdrożenia zgodnie z harmonogramem konfiguracji, które zostały podane. Spojrzenie na sondy kondycji jest zawsze dużą pierwszy krok w monitorowania lub określanie przyczyny.

Można podjęcie dalszych kroku i metryki dostępności adresu VIP do wgląd w sposób Azure widoków kondycji podstawowej odpowiedzialne za wdrożenie określonych płaszczyzna danych. Połączenie obu metryki można odizolować, gdzie mogą być usterki przedstawionych w tym przykładzie:

![Diagnostyka VIP](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*Rysunek — łączenie DIP i VIP metryki dostępności*

Wykres przedstawia następujące informacje:
- Infrastruktura sam był dobrej kondycji, infrastruktury obsługi maszyn wirtualnych był dostępny, a więcej niż jedna maszyna wirtualna została umieszczona w wewnętrznej bazie danych. Wskazuje niebieski śledzenia dla adresów VIP dostępności, która zawiera 100%. 
- Jednak stan sondy kondycji (dostępność DIP) jest 0% na początku wykresu wskazywany przez pomarańczowy śledzenia. Obszarze kółku red najważniejsze funkcje, których sondy kondycji (dostępność DIP) stał się dobrej kondycji i w takim przypadku wdrożenia klienta mógł akceptować nowych przepływów.

Wykres dozwolone klientowi Rozwiązywanie problemów z wdrożenia samodzielnie, bez konieczności odgadnąć lub poproś pomocy technicznej, czy wystąpiły inne problemy występujące. Usługa klienta nie jest dostępny, ponieważ sondy kondycji niepowodzeniem powodu błędnej konfiguracji lub aplikacji nie powiodło się.

### <a name = "Limitations"></a>Ograniczenia

- Dostępność adresu VIP jest obecnie dostępny tylko dla publicznych frontends.

## <a name = "ResourceHealth"></a>Stan kondycji zasobu

Stan kondycji zasobów standardowe usługi równoważenia obciążenia jest uwidaczniany za pośrednictwem istniejące **kondycja zasobów** w obszarze **Monitor > kondycja usługi**.

>[!NOTE]
>Kondycja zasobów dla usługi równoważenia obciążenia jest obecnie dostępny dla publicznej konfiguracji tylko standardowe równoważenia obciążenia. Wewnętrzny moduł równoważenia obciążenia zasobów lub zasobów podstawowa jednostka SKU równoważenia obciążenia nie ujawniaj kondycja zasobów.

Aby wyświetlić kondycję zasobami publicznego standardowe usługi równoważenia obciążenia
 - Przejdź do **Monitor > Usługa kondycji**.

  ![Strona monitorowania](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Rysunek — usługa kondycji na monitorze Azure*

 - Wybierz **kondycja zasobów** i upewnij się, że poprawne, że **identyfikator subskrypcji** i **typ zasobu = modułu równoważenia obciążenia** jest zaznaczone.

  ![Stan kondycji zasobu](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Rysunek — wybierz zasób dla widoku kondycji*

 - Kliknij zasób usługi równoważenia obciążenia z listy, aby wyświetlić ich stan kondycji historycznych.

    ![Stan kondycji modułu równoważenia obciążenia](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Rysunek — widok kondycji zasobów usługi równoważenia obciążenia*
 
W poniższej tabeli wymieniono różne stan kondycji zasobu i ich opisy. 

| Stan kondycji zasobu | Opis |
| --- | --- |
| Dostępna | Zasobie publicznego standardowy moduł równoważenia obciążenia jest dobrej kondycji i dostępności |
| Niedostępny | Zasób publiczny moduł równoważenia obciążenia standardowego nie jest dobra. Diagnozowanie kondycji przez Azure Monitor > metryki. (*Może to także oznaczać, że zasób nie jest publiczny moduł równoważenia obciążenia standardowego*) |
| Brak informacji | Kondycja zasobów dla publicznych standardowy moduł równoważenia obciążenia nie został jeszcze zaktualizowany. (*Może to także oznaczać, że zasób nie jest publiczny moduł równoważenia obciążenia standardowego*)  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [standardowego modułu równoważenia obciążenia](load-balancer-standard-overview.md)
- Dowiedz się więcej o [łączność wychodząca usługi równoważenia obciążenia](https://aka.ms/lboutbound)


