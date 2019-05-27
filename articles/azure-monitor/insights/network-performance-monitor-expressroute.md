---
title: Network Performance Monitor rozwiązania w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Użyj możliwości Monitor usługi ExpressRoute w rozwiązania Network Performance Monitor do monitorowania łączności end-to-end i wydajności między oddziałami i platformy Azure, za pośrednictwem usługi Azure ExpressRoute.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: absha
ms.openlocfilehash: 7f9c0d905a7b2bc81063e59229d78a1200894d47
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65963732"
---
# <a name="expressroute-monitor"></a>Monitor usługi ExpressRoute

Można użyć możliwości usługi Azure Monitor usługi ExpressRoute w [rozwiązania Network Performance Monitor](network-performance-monitor.md) do monitorowania łączności end-to-end i wydajności między oddziałami i platformy Azure, za pośrednictwem usługi Azure ExpressRoute. Dostępne są następujące kluczowe korzyści: 

- Obwody automatycznego wykrywania usługi ExpressRoute skojarzonych z Twoją subskrypcją.
- Śledzenie wykorzystania przepustowości, strat i opóźnień w poziomie obwodu, komunikacji równorzędnej i siecią wirtualną platformy Azure dla usługi ExpressRoute.
- Odnajdywanie topologii sieci dla obwodów usługi ExpressRoute.

![Monitor usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Konfigurowanie 
Aby otworzyć konfigurację rozwiązania Network Performance Monitor, otwórz [rozwiązania Network Performance Monitor](network-performance-monitor.md) i wybierz **Konfiguruj**.

### <a name="configure-network-security-group-rules"></a>Konfigurowanie reguł sieciowych grup zabezpieczeń 
W przypadku serwerów na platformie Azure, które są używane do monitorowania za pomocą rozwiązania Network Performance Monitor należy skonfigurować reguły Sieciowej grupy zabezpieczeń sieci zezwalającą na ruch TCP na porcie używanym przez rozwiązania Network Performance Monitor na potrzeby transakcji syntetycznych. Domyślnym portem jest port 8084. Ta konfiguracja pozwala agenta usługi Log Analytics, zainstalowane na maszynach wirtualnych platformy Azure do komunikowania się z lokalną agenta monitorowania. 

Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [sieciowe grupy zabezpieczeń](../../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> Przed kontynuowaniem tego kroku Zainstaluj lokalnego agenta serwera i agenta serwer usługi Azure, a następnie uruchom skrypt EnableRules.ps1 programu PowerShell. 

 
### <a name="discover-expressroute-peering-connections"></a>Odkryj połączenia komunikacji równorzędnej usługi ExpressRoute 
 
1. Wybierz **komunikacje równorzędne usługi ExpressRoute** widoku.
2. Wybierz **odnajdywanie teraz** do odnajdywania wszystkich ExpressRoute prywatnej komunikacji równorzędnej, które są połączone z sieciami wirtualnymi w subskrypcji platformy Azure są połączone z tym obszarem roboczym usługi Azure Log Analytics.

    >[!NOTE]
    > Rozwiązania aktualnie odnajduje tylko prywatne komunikacje równorzędne usługi ExpressRoute. 

    >[!NOTE]
    > Tylko do prywatnej komunikacji równorzędnej, połączone z sieciami wirtualnymi skojarzonego z subskrypcją, połączone z tym obszarem roboczym usługi Log Analytics są odnajdywane. Jeśli usługa ExpressRoute jest podłączony do sieci wirtualnej znajdującej się poza subskrypcji połączonej z tym obszarem roboczym, należy utworzyć obszar roboczy usługi Log Analytics w tych subskrypcjach. Następnie można użyć rozwiązania Network Performance Monitor do monitorowania tych komunikacji równorzędnej. 

    ![Konfiguracja Monitora usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   Po zakończeniu odnajdywania odnalezionych połączenia prywatnej komunikacji równorzędnej są wymienione w tabeli. Monitorowanie te komunikacje równorzędne początkowo jest w stanie wyłączenia. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Aby włączyć monitorowanie połączeń komunikacji równorzędnej usługi ExpressRoute 

1. Wybierz połączenie komunikacji równorzędnej prywatnej, który chcesz monitorować.
2. W okienku po prawej stronie wybierz **monitorowanie tej komunikacji równorzędnej** pole wyboru. 
3. Jeśli zamierzasz utworzyć zdarzenia dotyczące kondycji dla tego połączenia, wybierz opcję **Włącz monitorowanie kondycji dla tej komunikacji równorzędnej**. 
4. Wybierz opcję monitorowania warunków. Można ustawić niestandardowe progi dla generowanie zdarzeń kondycji, wprowadzając wartości progowe. Zawsze, gdy wartość warunku przekroczy wartość progu wybranego połączenia komunikacji równorzędnej, jest generowane zdarzenie kondycji. 
5. Wybierz **dodawanie agentów** wybrać agentów monitorowania, których zamierzasz używać na potrzeby monitorowania tego połączenia komunikacji równorzędnej. Upewnij się, możesz dodać agentów na obu końcach połączenia. Należy co najmniej jednego agenta w sieci wirtualnej nawiązanie połączenia komunikacji równorzędnej. Należy również co najmniej jednego agenta w środowisku lokalnym, nawiązanie połączenia komunikacji równorzędnej. 
6. Wybierz **Zapisz** Aby zapisać konfigurację. 

   ![Konfiguracja monitorowania usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


Po włączeniu zasady i wybierz wartości i agentów należy poczekać 30 do 60 minut dla wartości wypełnić i **monitorowania usługi ExpressRoute** Kafelki, aby można było wyświetlane. Jeśli widzisz monitorowania Kafelki, elementy do obwodów usługi ExpressRoute i połączeń zasobów teraz są monitorowane przez rozwiązania Network Performance Monitor. 

>[!NOTE]
> Ta funkcja działa niezawodnie na obszarach roboczych, które zostały uaktualnione do nowego języka zapytań.

## <a name="walkthrough"></a>Przewodnik 

Pulpit nawigacyjny rozwiązania Network Performance Monitor zawiera przegląd kondycji obwodów usługi ExpressRoute i połączeń komunikacji równorzędnej. 

![Pulpit nawigacyjny monitora wydajności sieci](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Lista obwodów 

Umożliwia wyświetlenie listy wszystkich monitorowanych obwodów usługi ExpressRoute, wybierz Kafelek obwodów usługi ExpressRoute. Można wybrać obwodu i wyświetlić jego stan kondycji, wykresy trendów utraty pakietów, wykorzystania przepustowości i opóźnień. Wykresy są interaktywne. Możesz wybrać przedział czasu niestandardowych do kreślenia wykresów. Przeciągnij mysz nad obszarem na wykresie, aby powiększyć obraz i wyświetlić punkty danych szczegółowych. 

![Lista obwodów usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Trendy utraty, opóźnienia i przepływności 

Wykresy wykorzystania, opóźnienia i straty przepustowości są interaktywne. Można powiększyć dowolną sekcję te wykresy za pomocą myszy kontrolek. Również widać przepustowości, opóźnienia i utratę danych dla innych interwałów. W lewym górnym rogu w obszarze **akcje** przycisku Wybierz **daty/godziny**. 

![Czas oczekiwania usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Lista połączeń komunikacji równorzędnej 

Aby wyświetlić listę wszystkich połączeń z sieciami wirtualnymi za pośrednictwem prywatnej komunikacji równorzędnej, wybierz **prywatne komunikacje równorzędne** Kafelek na pulpicie nawigacyjnym. W tym miejscu można wybrać wirtualnej połączenia sieciowego i wyświetlić jego stan kondycji, wykresy trendów utraty pakietów, wykorzystania przepustowości i opóźnień. 

![Komunikacje równorzędne usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Topologia obwodu 

Zaznacz, aby wyświetlić topologię obwodu **topologii** kafelka. Ta akcja spowoduje przejście do widoku topologii wybranego obwód lub komunikacji równorzędnej. Diagram topologii zapewnia opóźnienia dla każdego segmentu w sieci, a każdy przeskok warstwy 3 jest reprezentowany przez węzeł diagramu. Wybieranie przeskoków, co spowoduje wyświetlenie więcej szczegółów na temat przeskoków. Aby zwiększyć poziom widoczności do uwzględnienia w środowisku lokalnym przeskoków, przesuń suwak w obszarze **filtry**. Przenoszenie paska suwaka do lewej lub prawej zwiększa lub zmniejsza liczbę przeskoków na wykresie topologii. Opóźnienie w każdym segmencie jest widoczny, co umożliwia szybsze izolacji segmentów z dużym opóźnieniem w sieci.

![Topologia usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Widok szczegółowy topologii obwodu 

Ten widok przedstawia połączeń sieci wirtualnej. 

![Połączenia sieci wirtualnej usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnostyka 

Rozwiązanie Network Performance Monitor ułatwia diagnozowanie problemów z połączeniem kilka obwodu. Poniżej wymieniono niektóre problemy, które można zobaczyć.

Można wyświetlić kody powiadomień oraz ustawianie alertów dotyczących je przy użyciu **LogAnalytics**. Na **diagnostyki NPM** stronie widać opisy dla każdego komunikatu diagnostyczne wyzwolone.

| Kod powiadomienia (Dzienniki) | Opis |
| --- | --- |
| 5501 | Nie można przejść wzdłuż połączenia pomocniczego obwodu usługi ExpressRoute |
| 5502 | Nie można przejść wzdłuż połączenia podstawowego obwodu usługi ExpressRoute |
| 5503 | Nie znaleziono żadnego obwodu dla subskrypcji połączonej z obszarem roboczym | 
| 5508 | Nie można określić, czy ruch przechodzi przez żadne obwody dla ścieżki |
| 5510 | Ruch nie przechodzi przez zamierzony obwód | 
| 5511 | Ruch nie przechodzi przez zamierzoną sieć wirtualną | 

**Obwód nie działa.** Rozwiązanie Network Performance Monitor powiadamia użytkownika, jak łączność między zasobami lokalnymi i sieciami wirtualnymi platformy Azure zostaną utracone. To powiadomienie ułatwia podjęcie działań aktywnego przed otrzymujesz za użytkownika i ograniczyć przestoje.

![Obwód usługi ExpressRoute nie działa](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**Ruch nie przepływają przez zamierzony obwód.** Rozwiązanie Network Performance Monitor powiadamia zawsze wtedy, gdy ruch nie przepływają przez zamierzony obwód usługi ExpressRoute. Ten problem może się zdarzyć, jeśli obwód nie działa, a ruch przepływają przez kopii zapasowej trasy. On również może nastąpić, jeśli występuje problem z routingiem. Te informacje pomagają aktywnie zarządzać problemach konfiguracji zasad routingu i upewnij się, że najbardziej optymalnego i bezpiecznego marszrucie jest używany. 

 

**Ruch nie przepływają przez obwód podstawowy.** Rozwiązanie Network Performance Monitor powiadamia użytkownika, gdy ruch przepływają przez pomocniczego obwodu usługi ExpressRoute. Mimo że nie będzie środowisko wszelkie problemy z łącznością, w tym przypadku, proaktywnie Rozwiązywanie problemów z obwód podstawowy sprawia, że możesz lepiej przygotować. 

 
![Przepływ ruchu usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Obniżenie wydajności ze względu na użycie szczytowe.** Można skorelować trend wykorzystania przepustowości przy użyciu trend opóźnienia, aby ustalić, czy jest obniżenie obciążenie platformy Azure z powodu szczytowego wykorzystania przepustowości, czy nie. Następnie można podjąć odpowiednie działania w związku z tym.

![Wykorzystanie przepustowości usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Kolejne kroki
[Przeszukiwanie dzienników](../../azure-monitor/log-query/log-query-overview.md) do wyświetlania rekordów danych wydajności szczegółowe sieci.
