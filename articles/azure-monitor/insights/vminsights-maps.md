---
title: Wyświetlanie zależności aplikacji za pomocą usługi Azure Monitor dla maszyn wirtualnych
description: Mapa jest funkcją usługi Azure Monitor dla maszyn wirtualnych. Automatycznie odnajduje składniki aplikacji w systemach Windows i Linux i mapuje komunikację między usługami. Ten artykuł zawiera szczegółowe informacje na temat korzystania z funkcji Mapa w różnych scenariuszach.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2020
ms.openlocfilehash: acb96984a49e4ad8535f87a41da11b3b63ae207b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283858"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-to-understand-application-components"></a>Użyj funkcji Mapa usługi Azure Monitor dla maszyn wirtualnych, aby zrozumieć składniki aplikacji
W usłudze Azure Monitor dla maszyn wirtualnych można wyświetlać odnalezione składniki aplikacji na maszynach wirtualnych systemu Windows i Linux, które są uruchamiane na platformie Azure lub w Twoim środowisku. Maszyny wirtualne można obserwować na dwa sposoby. Wyświetl mapę bezpośrednio z maszyny Wirtualnej lub wyświetl mapę z usługi Azure Monitor, aby wyświetlić składniki w grupach maszyn wirtualnych. Ten artykuł pomoże Ci zrozumieć te dwie metody wyświetlania i jak korzystać z funkcji Mapa. 

Aby uzyskać informacje dotyczące konfigurowania usługi Azure Monitor dla maszyn wirtualnych, zobacz [Włączanie usługi Azure Monitor dla maszyn wirtualnych.](vminsights-enable-overview.md)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do [Portalu Azure](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Wprowadzenie do środowiska mapy
Przed zagłębieniem się w środowisko mapy należy zrozumieć, w jaki sposób prezentuje i wizualizuje informacje. Niezależnie od tego, czy wybierzesz obiekt Mapy bezpośrednio z maszyny Wirtualnej, czy z usługi Azure Monitor, funkcja Mapa zapewnia spójne środowisko. Jedyną różnicą jest to, że z usługi Azure Monitor, jedna mapa pokazuje wszystkich członków aplikacji wielowarstwowej lub klastra.

Funkcja Mapa wizualizuje zależności maszyn wirtualnych, odnajdując uruchomione procesy, które mają: 

- Aktywne połączenia sieciowe między serwerami.
- Opóźnienie połączenia przychodzącego i wychodzącego.
- Porty w dowolnej architekturze połączonej z TCP w określonym zakresie czasu.  
 
Rozwiń maszynę wirtualną, aby wyświetlić szczegóły procesu i tylko te procesy, które komunikują się z maszyną wirtualną. Grupa klientów pokazuje liczbę klientów front-end, które łączą się z maszyną wirtualną. Grupy portów serwera pokazują liczbę serwerów zaplecza, z którymi łączy się maszyna wirtualna. Rozwiń grupę portów serwera, aby wyświetlić szczegółową listę serwerów łączących się za pomocą tego portu.  

Po wybraniu maszyny Wirtualnej, **właściwości** okienka po prawej stronie pokazuje właściwości maszyny Wirtualnej. Właściwości obejmują informacje o systemie zgłaszane przez system operacyjny, właściwości maszyny Wirtualnej platformy Azure i wykres pierścieniowy, który podsumowuje odnalezione połączenia. 

![Okienko Właściwości](./media/vminsights-maps/properties-pane-01.png)

Po prawej stronie okienka wybierz pozycję **Zdarzenia dziennika,** aby wyświetlić listę danych wysłanych przez maszynę wirtualną do usługi Azure Monitor. Te dane są dostępne do wykonywania zapytań.  Wybierz dowolny typ rekordu, aby otworzyć stronę **Dzienniki,** na której są widoczne wyniki dla tego typu rekordu. Zobaczysz również wstępnie skonfigurowane zapytanie, które jest filtrowane względem maszyny Wirtualnej.  

![Okienko Zdarzenia dziennika](./media/vminsights-maps/properties-pane-logs-01.png)

Zamknij stronę **Dzienniki** i wróć do okienka **Właściwości.** Tam wybierz **alerty,** aby wyświetlić alerty o kryteriach kondycji maszyny Wirtualnej. Funkcja Mapa integruje się z alertami platformy Azure, aby wyświetlić alerty dla wybranego serwera w wybranym zakresie czasu. Serwer wyświetla ikonę bieżących alertów, a okienko **Alerty maszynowe** wyświetla listę alertów. 

![Okienko Alerty](./media/vminsights-maps/properties-pane-alerts-01.png)

Aby funkcja Mapa wyświetlała odpowiednie alerty, utwórz regułę alertu, która ma zastosowanie do określonego komputera:

- Dołącz klauzulę do grupowania alertów według komputera (na przykład **przez interwał komputera 1 minuta).**
- Oprzeć alert na metryki.

Aby uzyskać więcej informacji na temat alertów platformy Azure i tworzenia reguł [alertów, zobacz Ujednolicone alerty w usłudze Azure Monitor.](../../azure-monitor/platform/alerts-overview.md)

W prawym górnym rogu opcja **Legenda** opisuje symbole i role na mapie. Aby bliżej przyjrzeć się mapie i przenieść ją, użyj kontrolek powiększenia w prawym dolnym rogu. Można ustawić poziom powiększenia i dopasować mapę do rozmiaru strony.  

## <a name="connection-metrics"></a>Metryki połączeń
W okienku **Połączenia** są wyświetlane standardowe metryki dla wybranego połączenia z maszyny Wirtualnej za portem TCP. Metryki obejmują czas odpowiedzi, żądania na minutę, przepływność ruchu i łącza.  

![Wykresy łączności sieciowej w okienku Połączenia](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Nieudane połączenia
Mapa pokazuje nieudane połączenia dla procesów i komputerów. Przerywana czerwona linia wskazuje, że system kliencki nie może osiągnąć procesu lub portu. W przypadku systemów, które używają agenta zależności, agent raportuje próby połączenia nie powiodło się. Funkcja Mapa monitoruje proces, obserwując gniazda TCP, które nie mogą ustanowić połączenia. Ten błąd może wynikać z zapory, błędnej konfiguracji na kliencie lub serwerze lub niedostępnej usługi zdalnej.

![Nieudane połączenie na mapie](./media/vminsights-maps/map-group-failed-connection-01.png)

Opis połączeń nie powiodło się może pomóc w rozwiązywaniu problemów, sprawdzania poprawności migracji, analizowaniu zabezpieczeń i zrozumieniu ogólnej architektury usługi. Nieudane połączenia są czasami nieszkodliwe, ale często wskazują na problem. Połączenia mogą zakończyć się niepowodzeniem, na przykład gdy środowisko pracy awaryjnej nagle staje się nieosiągalne lub gdy dwie warstwy aplikacji nie mogą komunikować się ze sobą po migracji do chmury.

### <a name="client-groups"></a>Grupy klientów
Na mapie grupy klientów reprezentują komputery klienckie, które łączą się z mapowanym komputerem. Pojedyncza grupa klientów reprezentuje klientów dla poszczególnych procesów lub maszyn.

![Grupa klientów na mapie](./media/vminsights-maps/map-group-client-groups-01.png)

Aby wyświetlić monitorowanych klientów i adresy IP systemów w grupie klientów, wybierz grupę. Zawartość grupy pojawi się poniżej.  

![Lista adresów IP grupy klientów na mapie](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Jeśli grupa zawiera monitorowanych i niemonitorowanych klientów, można wybrać odpowiednią sekcję wykresu pierścieniowego grupy, aby filtrować klientów.

### <a name="server-port-groups"></a>Grupy portów serwera
Grupy portów serwera reprezentują porty na serwerach, które mają połączenia przychodzące z mapowanych komputerów. Grupa zawiera port serwera i liczbę serwerów, które mają połączenia z tym portem. Wybierz grupę, aby wyświetlić poszczególne serwery i połączenia. 

![Grupa portów serwera na mapie](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Jeśli grupa zawiera monitorowane i niemonitorowane serwery, można wybrać odpowiednią sekcję wykresu pierścieniowego grupy, aby filtrować serwery.

## <a name="view-a-map-from-a-vm"></a>Wyświetlanie mapy z maszyny Wirtualnej 

Aby uzyskać dostęp do usługi Azure Monitor dla maszyn wirtualnych bezpośrednio z maszyny Wirtualnej:

1. W portalu Azure wybierz pozycję **Maszyny wirtualne**. 
2. Z listy wybierz maszynę wirtualną. W sekcji **Monitorowanie** wybierz pozycję **Insights**.  
3. Wybierz kartę **Mapa.**

Mapa wizualizuje zależności maszyny Wirtualnej, odnajdując uruchomione grupy procesów i procesy, które mają aktywne połączenia sieciowe w określonym zakresie czasu.  

Domyślnie mapa pokazuje ostatnie 30 minut. Jeśli chcesz zobaczyć, jak zależności wyglądały w przeszłości, możesz zbadać historyczne zakresy czasu do jednej godziny. Aby uruchomić kwerendę, użyj selektora **TimeRange** w lewym górnym rogu. Można uruchomić kwerendę, na przykład podczas zdarzenia lub aby zobaczyć stan przed zmianą.  

![Omówienie mapy bezpośredniej maszyny Wirtualnej](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Wyświetlanie mapy z zestawu skalowania maszyny wirtualnej

Aby uzyskać dostęp do usługi Azure Monitor dla maszyn wirtualnych bezpośrednio z zestawu skalowania maszyny wirtualnej:

1. W portalu Azure wybierz pozycję **Zestawy skalowania maszyny wirtualnej**.
2. Z listy wybierz maszynę wirtualną. Następnie w sekcji **Monitorowanie** wybierz pozycję **Insights**.  
3. Wybierz kartę **Mapa.**

Mapa wizualizuje wszystkie wystąpienia w zestawie skalowania jako węzeł grupy wraz z zależnościami grupy. Rozwinięty węzeł wyświetla listę wystąpień w zestawie skalowania. Można przewijać te wystąpienia 10 naraz. 

Aby załadować mapę dla określonego wystąpienia, należy najpierw wybrać to wystąpienie na mapie. Następnie wybierz przycisk **wielokropka** (...) po prawej stronie i wybierz polecenie **Załaduj mapę serwera**. Na wyświetlonej mapie są wyświetlane grupy procesów i procesy, które mają aktywne połączenia sieciowe w określonym zakresie czasu. 

Domyślnie mapa pokazuje ostatnie 30 minut. Jeśli chcesz zobaczyć, jak zależności wyglądały w przeszłości, możesz zbadać historyczne zakresy czasu do jednej godziny. Aby uruchomić kwerendę, użyj selektora **TimeRange.** Można uruchomić kwerendę, na przykład podczas zdarzenia lub aby zobaczyć stan przed zmianą.

![Omówienie mapy bezpośredniej maszyny Wirtualnej](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>Można również uzyskać dostęp do mapy dla określonego wystąpienia z widoku **Wystąpienia** dla zestawu skalowania maszyny wirtualnej. W sekcji **Ustawienia** przejdź do **sekcji Statystyki wystąpień** > **.**

## <a name="view-a-map-from-azure-monitor"></a>Wyświetlanie mapy z usługi Azure Monitor

W usłudze Azure Monitor funkcja Mapa zapewnia globalny widok maszyn wirtualnych i ich zależności. Aby uzyskać dostęp do funkcji Mapy w usłudze Azure Monitor:

1. W witrynie Azure portal wybierz pozycję **Monitor**. 
2. W sekcji **Insights** wybierz pozycję **Maszyny wirtualne**.
3. Wybierz kartę **Mapa.**

   ![Mapa przeglądowa usługi Azure Monitor wielu maszyn wirtualnych](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Wybierz obszar roboczy za pomocą selektora **obszaru roboczego** u góry strony. Jeśli masz więcej niż jeden obszar roboczy usługi Log Analytics, wybierz obszar roboczy, który jest włączony z rozwiązaniem i który ma maszyny wirtualne raportowania do niego. 

Selektor **Grup** zwraca subskrypcje, grupy zasobów, [grupy komputerów](../../azure-monitor/platform/computer-groups.md)i zestawy skalowania maszyn wirtualnych komputerów, które są powiązane z wybranym obszarem roboczym. Wybór dotyczy tylko funkcji Mapa i nie jest przesuń do wydajności ani kondycji.

Domyślnie mapa pokazuje ostatnie 30 minut. Jeśli chcesz zobaczyć, jak zależności wyglądały w przeszłości, możesz zbadać historyczne zakresy czasu do jednej godziny. Aby uruchomić kwerendę, użyj selektora **TimeRange.** Można uruchomić kwerendę, na przykład podczas zdarzenia lub aby zobaczyć stan przed zmianą.  

## <a name="next-steps"></a>Następne kroki

Aby zidentyfikować wąskie gardła, sprawdź wydajność i zrozumieć ogólne wykorzystanie maszyn wirtualnych, zobacz [Wyświetlanie stanu wydajności usługi Azure Monitor dla maszyn wirtualnych.](vminsights-performance.md) 
