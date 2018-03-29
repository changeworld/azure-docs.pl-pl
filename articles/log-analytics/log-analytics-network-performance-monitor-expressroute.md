---
title: Sieci monitora wydajności rozwiązania Azure Log Analytics | Dokumentacja firmy Microsoft
description: Umożliwia możliwości Menedżera usługi ExpressRoute w Monitorze wydajności sieci monitorować wydajność między oddziałach i Azure i na trasie łączności za pośrednictwem usługi Azure ExpressRoute.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 9610a8b37ead976cfdfa2fed81d4d3932055ddcc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="expressroute-manager"></a>ExpressRoute Manager

Korzystając z możliwości usługi Azure ExpressRoute Manager w [monitora wydajności sieci](log-analytics-network-performance-monitor.md) do monitorować wydajność między oddziałach i Azure i na trasie łączności za pośrednictwem usługi Azure ExpressRoute. Dostępne są następujące kluczowe korzyści: 

- Obwody automatycznego wykrywania ExpressRoute skojarzonych z Twoją subskrypcją.
- Śledzenie wykorzystania przepustowości, utrata i czas oczekiwania na poziomie obwodu, zaglądanie i sieć wirtualna Azure ExpressRoute.
- Odnajdywanie topologii sieci Twojej obwody usługi ExpressRoute.

![Monitor usługi ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-intro.png)

## <a name="configuration"></a>Konfigurowanie 
Aby otworzyć konfigurację monitora wydajności w sieci, otwórz [rozwiązania monitora wydajności sieci](log-analytics-network-performance-monitor.md) i wybierz **Konfiguruj**.

### <a name="configure-network-security-group-rules"></a>Konfigurowanie reguł grup zabezpieczeń sieci 
Dla serwerów na platformie Azure, które są używane do monitorowania za pomocą Monitora wydajności w sieci należy skonfigurować reguły grupa zabezpieczeń sieci zezwalająca na ruch TCP na porcie używanym przez Monitor wydajności sieci dla transakcji syntetycznych. Domyślny port to 8084. Ta konfiguracja pozwala agentowi Operations Management Suite zainstalowanych na maszynach wirtualnych Azure do komunikowania się z lokalnymi agenta monitorowania. 

Aby uzyskać więcej informacji na temat grupy NSG, zobacz [sieciowej grupy zabezpieczeń](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 

>[!NOTE]
> Przed kontynuowaniem w tym kroku Zainstaluj agenta programu server lokalnymi i agent serwera usługi Azure, a następnie uruchom skrypt programu EnableRules.ps1 PowerShell. 

 
### <a name="discover-expressroute-peering-connections"></a>Odnajdywanie połączenia komunikacji równorzędnej ExpressRoute 
 
1. Wybierz **komunikacji równorzędnych ExpressRoute** widoku.
2. Wybierz **odnajdywanie teraz** do odnajdywania wszystkich ExpressRoute prywatnej komunikacji równorzędnych, które są podłączone do sieci wirtualnych w subskrypcji platformy Azure są połączone z tym obszarem roboczym usługi Analiza dzienników Azure.

    >[!NOTE]
    > Rozwiązanie umożliwia odnalezienie obecnie tylko ExpressRoute prywatnej komunikacji równorzędnych. 

    >[!NOTE]
    > Tylko prywatnej komunikacji równorzędnych połączony skojarzone z subskrypcją połączone z tym obszarem roboczym analizy dzienników sieci wirtualne są wykrywane. Jeśli ExpressRoute jest podłączony do sieci wirtualnych poza subskrypcji połączone z tym obszarem roboczym, należy utworzyć obszaru roboczego analizy dzienników w tych subskrypcjach. Następnie należy użyć Monitora wydajności sieci do monitorowania tych komunikacji równorzędnych. 

    ![Konfiguracji usługi ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-configure.png)
 
 Po zakończeniu odnajdywania odnalezionych prywatnej komunikacji równorzędnej połączeń są wyświetlane w tabeli. Do monitorowania tych komunikacji równorzędnych początkowo jest w stanie wyłączenia. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Aby włączyć monitorowanie połączeń komunikacji równorzędnej ExpressRoute 

1. Wybierz prywatnej komunikacji równorzędnej połączenie, które chcesz monitorować.
2. W okienku po prawej stronie zaznacz **monitorować tej komunikacji równorzędnej** pole wyboru. 
3. Jeśli zamierzasz utworzyć zdarzenia kondycji dla tego połączenia, wybierz **Włącz monitorowanie kondycji dla komunikacji równorzędnej**. 
4. Wybierz warunki monitorowania. Można ustawić progami niestandardowymi kondycji zdarzenie generacji, wprowadzając wartości progowe. Zawsze, gdy wartość warunku wykraczają poza jego próg dla połączenia komunikacji równorzędnej, jest generowane zdarzenie kondycji. 
5. Wybierz **dodać agentów** możliwość monitorowania agentów, które mają być używane do monitorowania tego połączenia komunikacji równorzędnej. Upewnij się, możesz dodać agentów na obu końców połączenia. Należy co najmniej jednego agenta w sieci wirtualnej podłączonej do komunikacji równorzędnej. Należy również co najmniej jednego agenta lokalnie podłączone do komunikacji równorzędnej. 
6. Wybierz **zapisać** Aby zapisać konfigurację. 

   ![Konfiguracja monitorowania usługi ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-configure-discovery.png)


Po włączeniu zasady i wartości w poleceniu select i agentów, poczekaj 30 do 60 minut dla wartości do wypełnienia i **ExpressRoute monitorowania** Kafelki, które są wyświetlane. Po wyświetleniu monitorowania Kafelki obwody usługi ExpressRoute i siecią połączenia są teraz monitorowane przez Monitor wydajności sieci. 

>[!NOTE]
> Ta funkcja działa niezawodnie na obszary robocze, które zostały uaktualnione do nowego języka zapytań.

## <a name="walkthrough"></a>Przewodnik 

Pulpit nawigacyjny monitora wydajności sieci zawiera przegląd kondycji obwody usługi ExpressRoute i połączenia komunikacji równorzędnej. 

![Pulpit nawigacyjny sieci monitora wydajności](media/log-analytics-network-performance-monitor/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Obwody listy 

Aby wyświetlić listę wszystkich monitorowanych obwody usługi ExpressRoute, wybierz Kafelek obwody usługi ExpressRoute. Możesz wybrać obwód i wyświetlić jej stan kondycji, wykresy trendu do utraty pakietów, użycie przepustowości i opóźnień. Wykresy są interaktywne. Możesz wybrać okno czasu niestandardowych do kreślenia wykresy. Przeciągnij mysz nad obszarem na wykresie powiększanie i zobaczyć punkty szczegółowych danych. 

![Lista obwody usługi ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Trendy utraty, opóźnienia i przepływności 

Wykresy użycia, opóźnienia i utratę przepustowości są interaktywne. Możesz powiększać do dowolnej sekcji te wykresy za pomocą formantów myszy. Możesz również wyświetlone przepustowości, opóźnienia i utratę danych innych interwałów. W lewym górnym obszarze **akcje** przycisku Wybierz **daty/godziny**. 

![Opóźnienie ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-latency.png) 

### <a name="peerings-list"></a>Lista komunikacji równorzędnych 

Aby wyświetlić listę wszystkich połączeń do sieci wirtualnych za pośrednictwem prywatnej komunikacji równorzędnej, wybierz **prywatnej komunikacji równorzędnych** kafelka na pulpicie nawigacyjnym. W tym miejscu można wybrać wirtualnej połączenie sieciowe i wyświetlić jej stan kondycji, wykresy trendu do utraty pakietów, użycie przepustowości i opóźnień. 

![Komunikacji równorzędnych usługi ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Topologia obwodu 

Zaznacz, aby wyświetlić topologii obwodu **topologii** kafelka. Ta akcja umożliwia przejście do wybranego widoku topologii obwodu lub komunikacji równorzędnej. Diagram topologii udostępnia czas oczekiwania na każdy z segmentów w sieci, a każdy przeskok warstwy 3 jest reprezentowany przez węzeł diagramu. Wybieranie przeskoku ujawnia więcej szczegółów na temat przeskoku. Aby zwiększyć poziom widoczności uwzględnienie przeskoków lokalnymi, przesuń suwak w obszarze **filtry**. Przenoszenie suwak do lewego lub prawego zwiększa lub zmniejsza liczbę przeskoków na wykresie topologii. Czas oczekiwania w każdym segmencie jest widoczny, która umożliwia szybsze izolacji segmentów dużymi opóźnieniami w sieci. 

![Topologia usługi ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Widok szczegółowy topologii obwodu 

Ten widok przedstawia połączeń sieci wirtualnej. 

![Połączenia wirtualnej sieci ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-vnet.png)
 

### <a name="diagnostics"></a>Diagnostyka 

Monitor wydajności sieci ułatwia diagnozowanie problemów z łącznością kilka obwodu. Niektóre problemy są wyświetlane tutaj. 

**Obwód jest wyłączony.** Monitor wydajności sieci powiadamia jak łączność między lokalnymi zasobami i sieciami wirtualnymi platformy Azure zostaną utracone. Pomaga to powiadomienie podjęcia działań aktywnego odbierania za użytkownika i skrócić czas przestojów.

![Obwód usługi expressroute nie działa](media/log-analytics-network-performance-monitor/expressroute-circuit-down.png)
 

**Ruch nie przepływają danego obwodu.** Monitor wydajności sieci powiadamia zawsze, gdy ruch nie jest przepływających przez danego obwodu usługi expressroute. Ten problem może się zdarzyć, jeśli obwód nie działa, a ruch jest przepływających przez trasy kopii zapasowej. On również może nastąpić, jeśli występuje problem routingu. Te informacje pomagają aktywnie zarządzać problemów dotyczących konfiguracji w zasad routingu i upewnij się, że używana jest najbardziej optymalnego i bezpiecznego trasa. 

 

**Ruch nie przepływają głównej obwodu.** Monitor wydajności sieci powiadamia użytkownika, gdy ruch jest przepływających przez dodatkowej obwodu usługi expressroute. Mimo że nie występują problemy z łącznością w takim przypadku, aktywne rozwiązywania problemów z podstawowym obwodu sprawia, że można lepiej przygotowane. 

 
![Przepływ ruchu usługi ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-traffic-flow.png)


**Pogorszenia się z powodu szczytowego wykorzystania.** Można dostosować trend wykorzystania przepustowości z panującym trendem opóźnienia, aby określić, czy degradacji Azure obciążenie jest spowodowane szczytowego użycia przepustowości. Następnie można odpowiednio podjęcia działania.

![Użycie przepustowości ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Kolejne kroki
[Wyszukaj dzienniki](log-analytics-log-searches.md) do wyświetlania rekordów danych wydajności szczegółowe sieci.
