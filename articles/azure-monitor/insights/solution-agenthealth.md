---
title: Rozwiązanie kondycji agenta w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Ten artykuł ma na celu pomóc zrozumieć, jak używać tego rozwiązania do monitorowania kondycji agentów raportowania bezpośrednio do usługi Log Analytics lub System Center Operations Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2020
ms.openlocfilehash: 7093e20473b799a3f05ddf30803721636732241e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663264"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Rozwiązanie kondycji agenta w usłudze Azure Monitor
Rozwiązanie Kondycji agenta na platformie Azure pomaga zrozumieć, dla wszystkich agentów raportowania bezpośrednio do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor lub grupy zarządzania Programu Operations Manager systemu połączone z usługą Azure Monitor, które nie reagują i przekazywania danych operacyjnych.  Można także śledzić liczbę wdrożonych agentów i ich geograficzne rozmieszczenie oraz wykonywać inne zapytania dające informacje na temat rozmieszczenia agentów wdrożonych na platformie Azure, w innych środowiskach w chmurze i lokalnie.    

## <a name="prerequisites"></a>Wymagania wstępne
Przed wdrożeniem tego rozwiązania upewnij się, że aktualnie obsługiwane [agentów systemu Windows](../../log-analytics/log-analytics-windows-agent.md) raportowania do obszaru roboczego usługi Log Analytics lub raportowania do grupy [zarządzania programu Operations Manager](../../azure-monitor/platform/om-agents.md) zintegrowane z obszaru roboczego.

## <a name="solution-components"></a>Składniki rozwiązania
To rozwiązanie składa się z następujących zasobów, które są dodawane do Twojego obszaru roboczego, i bezpośrednio połączonych agentów lub grupy zarządzania połączonej z programem Operations Manager.

### <a name="management-packs"></a>Pakiety administracyjne
Jeśli grupa zarządzania programu System Center Operations Manager jest połączona z obszarem roboczym usługi Log Analytics, w programie Operations Manager są instalowane następujące pakiety administracyjne.  Te pakiety administracyjne są również instalowane na bezpośrednio połączonych komputerach z systemem Windows po dodaniu tego rozwiązania. W przypadku tych pakietów administracyjnych nie trzeba niczego konfigurować ani niczym zarządzać.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack  (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Aby uzyskać więcej informacji na temat aktualizowania pakietów administracyjnych rozwiązania, zobacz artykuł [Connect Operations Manager to Log Analytics](../../azure-monitor/platform/om-agents.md) (Połączenie programu Operations Manager z usługą Log Analytics).

## <a name="configuration"></a>Konfigurowanie
Dodaj rozwiązanie Kondycji agenta do obszaru roboczego usługi Log Analytics przy użyciu procesu opisanego w [add solutions](solutions.md). Nie są wymagane żadne dalsze czynności konfiguracyjne.


## <a name="data-collection"></a>Zbieranie danych
### <a name="supported-agents"></a>Obsługiwani agenci
W poniższej tabeli opisano połączone źródła, które obsługuje to rozwiązanie.

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| Agenci dla systemu Windows | Tak | Zdarzenia pulsu są zbierane z bezpośrednich agentów systemu Windows.|
| Grupa zarządzania programu System Center Operations Manager | Tak | Zdarzenia pulsu są zbierane od agentów raportujących do grupy zarządzania co 60 sekund, a następnie przekazywane do usługi Azure Monitor. Bezpośrednie połączenie z agentami programu Operations Manager z usługą Azure Monitor nie jest wymagane. Dane zdarzeń pulsu są przekazywane z grupy zarządzania do obszaru roboczego usługi Log Analytics.|

## <a name="using-the-solution"></a>Użycie rozwiązania
Po dodaniu rozwiązania do obszaru roboczego usługi Log Analytics kafelek **kondycji agenta** zostanie dodany do pulpitu nawigacyjnego. Ten kafelek pokazuje całkowitą liczbę agentów oraz liczbę nieodpowiadających agentów w ciągu ostatnich 24 godzin.<br><br> ![Kafelek rozwiązania Agent Health na pulpicie nawigacyjnym](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Kliknij kafelek **Agent Health**, aby otworzyć pulpit nawigacyjny rozwiązania **Agent Health**.  Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli. Każda kolumna zawiera listę dziesięciu najważniejszych zdarzeń według liczby odpowiadającej kryteriom tej kolumny dla określonego zakresu czasu. Możesz uruchomić przeszukiwanie rejestru zwracające pełną listę, wybierając pozycję **Zobacz wszystko** w prawej dolnej części kolumny lub klikając kolumnę nagłówka.

| Kolumna | Opis |
|--------|-------------|
| Liczba agentów w miarę upływu czasu | Trend liczby agentów w okresie siedmiu dni dla agentów systemu Linux i Windows.|
| Liczba nieodpowiadających agentów | Lista agentów, którzy nie wysłali pulsu w ciągu ostatnich 24 godzin.|
| Rozkład według typu systemu operacyjnego | Rozkład liczby agentów systemu Windows i Linux w Twoim środowisku.|
| Rozkład według wersji agenta | Rozkład różnych wersji agentów zainstalowanych w Twoim środowisku i liczba agentów dla każdej z nich.|
| Rozkład według kategorii agenta | Rozkład różnych kategorii agentów wysyłających zdarzenia pulsu: bezpośredni agenci, agenci programu OpsMgr i serwer zarządzania programu OpsMgr.|
| Rozkład według grupy zarządzania | Partycja różnych grup zarządzania programu Operations Manager w twoim środowisku.|
| Lokalizacja geograficzna agentów | Partycja różnych krajów/regionów, w których są agenci, oraz całkowita liczba agentów zainstalowanych w każdym kraju/regionie.|
| Liczba zainstalowanych bram | Liczba serwerów z zainstalowaną bramą usługi Log Analytics i lista tych serwerów.|

![Przykład pulpitu nawigacyjnego rozwiązania Agent Health](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Rekordy dziennika usługi Azure Monitor
Rozwiązanie tworzy jeden typ rekordu w obszarze roboczym usługi Log Analytics.  

### <a name="heartbeat-records"></a>Rekordy Heartbeat
Tworzony jest rekord o typie **Heartbeat**.  Te rekordy mają właściwości podane w poniższej tabeli.  

| Właściwość | Opis |
| --- | --- |
| `Type` | *Heartbeat*|
| `Category` | Wartością jest *Direct Agent*, *SCOM Agent* lub *SCOM Management Server*.|
| `Computer` | Nazwa komputera.|
| `OSType` | System operacyjny Windows lub Linux.|
| `OSMajorVersion` | Wersja główna systemu operacyjnego.|
| `OSMinorVersion` | Wersja pomocnicza systemu operacyjnego.|
| `Version` | Wersja agenta usługi Log Analytics lub agenta programu Operations Manager.|
| `SCAgentChannel` | Wartością jest *Direct* i/lub *SCManagementServer*.|
| `IsGatewayInstalled` | Jeśli zainstalowana jest brama usługi Log Analytics, wartość jest *prawdziwa,* w przeciwnym razie wartość jest *fałszywa*.|
| `ComputerIP` | Publiczny adres IP komputera. Na maszynach wirtualnych platformy Azure spowoduje wyświetlenia publicznego adresu IP, jeśli jest dostępny. W przypadku maszyn wirtualnych korzystających z prywatnych adresów IP spowoduje wyświetlenie adresu SNAT platformy Azure (a nie prywatnego adresu IP). |
| `RemoteIPCountry` | Lokalizacja geograficzna, w której wdrożony jest komputer.|
| `ManagementGroupName` | Nazwa grupy zarządzania programu Operations Manager.|
| `SourceComputerId` | Unikatowy identyfikator komputera.|
| `RemoteIPLongitude` | Długość geograficzna lokalizacji geograficznej komputera.|
| `RemoteIPLatitude` | Szerokość geograficzna lokalizacji geograficznej komputera.|

Każdy agent raportujący serwer zarządzania programu Operations Manager wyśle dwa pulsy, a wartość właściwości SCAgentChannel będzie zawierać zarówno **Direct,** jak i **SCManagementServer** w zależności od źródeł danych i rozwiązań monitorowania, które zostały włączone w subskrypcji. W przypadku odwołania dane z rozwiązań są wysyłane bezpośrednio z serwera zarządzania programu Operations Manager do usługi Azure Monitor lub ze względu na ilość danych zebranych w agencie są wysyłane bezpośrednio z agenta do usługi Azure Monitor. W przypadku zdarzeń pulsu, które mają wartość **SCManagementServer**, wartość ComputerIP jest adresem IP serwera zarządzania, ponieważ dane są faktycznie przekazywane przez niego.  W przypadku pulsów z parametrem SCAgentChannel ustawionym na wartość **Direct** jest to publiczny adres IP agenta.  

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników
Poniższa tabela zawiera przykładowe wyszukiwania dzienników dla rekordów zbieranych przez to rozwiązanie.

| Zapytanie | Opis |
|:---|:---|
| Heartbeat &#124; distinct Computer |Łączna liczba agentów |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Liczba nieodpowiadających agentów w ciągu ostatnich 24 godzin |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Liczba nieodpowiadających agentów w ciągu ostatnich 15 minut |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Komputery w trybie online (w ciągu ostatnich 24 godzin) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Całkowita liczba agentów w trybie offline w ciągu ostatnich 30 minut (dla ostatnich 24 godzin) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Pobierz trend liczby agentów w miarę upływu czasu według wartości OSType|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Rozkład według typu systemu operacyjnego |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Rozkład według wersji agenta |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Rozkład według kategorii agenta |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Rozkład według grupy zarządzania |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Lokalizacja geograficzna agentów |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Liczba zainstalowanych bram usługi Log Analytics |




## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [alertach w usłudze Azure Monitor, aby](../platform/alerts-overview.md) uzyskać szczegółowe informacje na temat generowania alertów z zapytań dziennika. 
