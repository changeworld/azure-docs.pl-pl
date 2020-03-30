---
title: Rozwiązanie Monitor wydajności sieci w usłudze Azure Log Analytics | Dokumenty firmy Microsoft
description: Za pomocą funkcji Monitora usługi ExpressRoute w Monitorze wydajności sieci można monitorować kompleksową łączność i wydajność między oddziałami firmy a platformą Azure za pośrednictwem usługi Azure ExpressRoute.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 11/27/2018
ms.openlocfilehash: 6ac610d7dcf9849b6b439741957684867b9d01aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660754"
---
# <a name="expressroute-monitor"></a>ExpressRoute Monitor

Za pomocą usługi Azure ExpressRoute Monitor można korzystać z funkcji Monitora usługi Azure ExpressRoute w [Monitorze wydajności sieci,](network-performance-monitor.md) aby monitorować kompleksową łączność i wydajność między oddziałami firmy a platformą Azure za pośrednictwem usługi Azure ExpressRoute. Kluczowe zalety to: 

- Automatyczne detekcja obwodów usługi ExpressRoute skojarzonych z subskrypcją.
- Śledzenie wykorzystania przepustowości, utraty i opóźnienia na poziomie obwodu, komunikacji równorzędnej i sieci wirtualnej platformy Azure dla usługi ExpressRoute.
- Odnajdowanie topologii sieci obwodów usługi ExpressRoute.

![ExpressRoute Monitor](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Konfigurowanie 
Aby otworzyć konfigurację Monitora wydajności sieci, otwórz [rozwiązanie Monitor wydajności sieci](network-performance-monitor.md) i wybierz pozycję **Konfiguruj**.

### <a name="configure-network-security-group-rules"></a>Konfigurowanie reguł sieciowej grupy zabezpieczeń 
W przypadku serwerów na platformie Azure, które są używane do monitorowania za pośrednictwem Monitora wydajności sieci, skonfiguruj reguły sieciowej grupy zabezpieczeń (NSG), aby zezwolić na ruch TCP na porcie używanym przez Monitor wydajności sieci dla transakcji syntetycznych. Domyślny port to 8084. Ta konfiguracja umożliwia agentowi usługi Log Analytics zainstalowanemu na maszynach wirtualnych platformy Azure komunikowanie się z lokalnym agentem monitorowania. 

Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [Grupy zabezpieczeń sieci](../../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> Przed kontynuowaniem tego kroku należy zainstalować lokalnego agenta serwera i agenta serwera platformy Azure i uruchomić skrypt Programu PowerShell EnableRules.ps1. 

 
### <a name="discover-expressroute-peering-connections"></a>Odnajdowanie połączeń komunikacji równorzędnej usługi ExpressRoute 
 
1. Wybierz widok **Komunikacji równorzędnej usługi ExpressRoute.**
2. Wybierz **opcję Odkryj teraz,** aby odkryć wszystkie prywatne programy równorzędne usługi ExpressRoute, które są połączone z sieciami wirtualnymi w ramach subskrypcji platformy Azure połączonych z tym obszarem roboczym usługi Azure Log Analytics.

    >[!NOTE]
    > Rozwiązanie obecnie odnajduje tylko usługi ExpressRoute prywatnych komunikacji równorzędnej. 

    >[!NOTE]
    > Odnajdowane są tylko prywatne programy komunikacji równorzędnej połączone z sieciami wirtualnymi skojarzonymi z subskrypcją połączoną z tym obszarem roboczym usługi Log Analytics. Jeśli usługa ExpressRoute jest połączona z sieciami wirtualnymi poza subskrypcją połączoną z tym obszarem roboczym, utwórz obszar roboczy usługi Log Analytics w tych subskrypcjach. Następnie użyj Monitora wydajności sieci, aby monitorować te komunikacji równorzędnej. 

    ![Konfiguracja monitora usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   Po zakończeniu odnajdywania odnalezione prywatne połączenia równorzędne są wyświetlane w tabeli. Monitorowanie dla tych komunikacji równorzędnej jest początkowo w stanie wyłączenia. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Włączanie monitorowania połączeń równorzędnych usługi ExpressRoute 

1. Wybierz prywatne połączenie komunikacji równorzędnej, które chcesz monitorować.
2. W okienku po prawej stronie zaznacz pole wyboru **Monitoruj tę komunikację równorzędna.** 
3. Jeśli zamierzasz utworzyć zdarzenia dotyczące kondycji dla tego połączenia, wybierz pozycję **Włącz monitorowanie kondycji dla tej komunikacji równorzędnej**. 
4. Wybierz warunki monitorowania. Można ustawić niestandardowe progi dla generowania zdarzeń kondycji, wprowadzając wartości progowe. Za każdym razem, gdy wartość warunku przekracza jego wybrany próg dla połączenia równorzędnego, generowane jest zdarzenie kondycji. 
5. Wybierz **dodaj agentów,** aby wybrać agentów monitorowania, które mają być używane do monitorowania tego połączenia komunikacji równorzędnej. Upewnij się, że dodano agentów na obu końcach połączenia. Potrzebujesz co najmniej jednego agenta w sieci wirtualnej podłączonej do tej komunikacji równorzędnej. Potrzebujesz również co najmniej jednego agenta lokalnego połączonego z tym komunikacją równorzędną. 
6. Wybierz **pozycję Zapisz,** aby zapisać konfigurację. 

   ![Konfiguracja monitorowania usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


Po włączeniu reguł i wybraniu wartości i agentów odczekaj od 30 do 60 minut, aż wartości mają być wypełniane, a kafelki **monitorowania usługi ExpressRoute** będą wyświetlane. Gdy zobaczysz kafelki monitorowania, obwody usługi ExpressRoute i zasoby połączeń są teraz monitorowane przez Monitor wydajności sieci. 

>[!NOTE]
> Ta funkcja działa niezawodnie na obszarach roboczych, które zostały uaktualnione do nowego języka zapytań.

## <a name="walkthrough"></a>Przewodnik 

Pulpit nawigacyjny Monitora wydajności sieci zawiera omówienie kondycji obwodów usługi ExpressRoute i połączeń komunikacji równorzędnej. 

![Pulpit nawigacyjny Monitora wydajności sieci](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Lista obwodów 

Aby wyświetlić listę wszystkich monitorowanych obwodów usługi ExpressRoute, wybierz kafelek obwodów usługi ExpressRoute. Można wybrać obwód i wyświetlić jego stan kondycji, wykresy trendów dla utraty pakietów, wykorzystania przepustowości i opóźnienia. Wykresy są interaktywne. Można wybrać niestandardowe okno czasu do drukowania wykresów. Przeciągnij wskaźnik myszy na obszar wykresu, aby powiększyć widok i wyświetlić szczegółowe punkty danych. 

![Lista obwodów usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Tendencje strat, opóźnień i przepływności 

Wykresy wykorzystania przepustowości, opóźnienia i strat są interaktywne. Możesz powiększyć dowolną sekcję tych wykresów za pomocą kontrolek myszy. Można również zobaczyć dane o przepustowości, opóźnieniach i stratach dla innych interwałów. W lewym górnym rogu pod przyciskiem **Akcje** wybierz **pozycję Data/godzina**. 

![Opóźnienie usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Lista komunikacji równorzędnej 

Aby wyświetlić listę wszystkich połączeń z sieciami wirtualnymi za pośrednictwem prywatnej komunikacji równorzędnej, wybierz kafelek **Private Peerings** na pulpicie nawigacyjnym. W tym miejscu można wybrać połączenie sieci wirtualnej i wyświetlić jego stan kondycji, wykresy trendów dotyczące utraty pakietów, wykorzystania przepustowości i opóźnienia. 

![Komunikacja równorzędna usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Topologia obwodów 

Aby wyświetlić topologię obwodów, wybierz kafelek **Topologia.** Ta akcja umożliwia przejście do widoku topologii wybranego obwodu lub komunikacji równorzędnej. Diagram topologii zapewnia opóźnienie dla każdego segmentu w sieci, a każdy przeskok warstwy 3 jest reprezentowany przez węzeł diagramu. Wybranie przeskoku powoduje wyświetlenie więcej szczegółów na temat przeskoku. Aby zwiększyć poziom widoczności w celu uwzględnienia przeskoków lokalnych, przesuń pasek suwaka w obszarze **FILTRY**. Przesunięcie suwaka w lewo lub w prawo zwiększa lub zmniejsza liczbę przeskoków na wykresie topologii. Opóźnienie w każdym segmencie jest widoczne, co pozwala na szybszą izolację segmentów o dużym opóźnieniu w sieci.

![Topologia usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Szczegółowy widok topologii obwodu 

W tym widoku są wyświetlane połączenia sieci wirtualnej. 

![Połączenia sieci wirtualnej usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnostyka 

Monitor wydajności sieci pomaga zdiagnozować kilka problemów z łącznością obwodu. Niektóre z problemów, które można zobaczyć są wymienione poniżej.

Możesz zobaczyć kody powiadomień i ustawić alerty na nich za pośrednictwem **LogAnalytics**. Na stronie **Diagnostyka npm** można wyświetlić opisy dla każdego komunikatu diagnostycznego wyzwalane.

| Kod powiadomienia (dzienniki) | Opis |
| --- | --- |
| 5501 | Nie można przechodzić przez połączenie pomocnicze obwodu usługi ExpressRoute |
| 5502 | Nie można przechodzić przez połączenie podstawowe obwodu usługi ExpressRoute |
| 5503 | Nie znaleziono obwodu dla subskrypcji połączonej z obszarem roboczym | 
| 5508 | Nie można określić, czy ruch przechodzi przez jakikolwiek obwód(-y) dla ścieżki |
| 5510 | Ruch nie przechodzi przez zamierzony obwód | 
| 5511 | Ruch nie przechodzi przez zamierzoną sieć wirtualną | 

**Obwód jest w dół.** Monitor wydajności sieci powiadamia użytkownika natychmiast po utracie łączności między zasobami lokalnymi a sieciami wirtualnymi platformy Azure. To powiadomienie ułatwia podejmowanie proaktywnych działań przed otrzymaniem eskalacji użytkowników i skracają przestoje.

![Obwód usługi ExpressRoute jest wyłączony](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**Ruch nieprzepływający przez zamierzony obwód.** Monitor wydajności sieci powiadamia użytkownika, gdy ruch nie przepływa przez zamierzony obwód usługi ExpressRoute. Ten problem może się zdarzyć, jeśli obwód jest w dół i ruch przepływa przez trasę kopii zapasowej. Może się również zdarzyć, jeśli występuje problem z routingiem. Te informacje pomagają aktywnie zarządzać wszelkimi problemami z konfiguracją w zasadach routingu i upewnić się, że używana jest najbardziej optymalna i bezpieczna trasa. 

 

**Ruch nie przepływający przez obwód podstawowy.** Monitor wydajności sieci powiadamia użytkownika, gdy ruch przepływa przez pomocniczy obwód usługi ExpressRoute. Mimo że w tym przypadku nie wystąpią żadne problemy z łącznością, proaktywne rozwiązywanie problemów z obwodem podstawowym sprawia, że jesteś lepiej przygotowany. 

 
![Przepływ ruchu usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Degradacja spowodowana szczytowym wykorzystaniem.** Można skorelować trend wykorzystania przepustowości z trendem opóźnienia, aby określić, czy degradacja obciążenia platformy Azure jest spowodowana szczytowym wykorzystaniem przepustowości, czy nie. Następnie można podjąć odpowiednie działania.

![Wykorzystanie przepustowości usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Następne kroki
[Wyszukaj dzienniki,](../../azure-monitor/log-query/log-query-overview.md) aby wyświetlić szczegółowe rekordy danych wydajności sieci.
