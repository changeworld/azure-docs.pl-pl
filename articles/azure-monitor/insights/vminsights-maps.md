---
title: Jak wyświetlić zależności aplikacji z usługą Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Mapa jest funkcją usługi Azure Monitor dla maszyn wirtualnych. Automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Ten artykuł zawiera szczegółowe informacje na temat korzystania z funkcji mapy w różnych scenariuszach.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: f6273e9b6c7ed0c4685479976343497f01201b0b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206756"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-preview-to-understand-application-components"></a>Funkcja mapy usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) pozwala zrozumieć składników aplikacji
W usłudze Azure Monitor dla maszyn wirtualnych możesz wyświetlić składniki odnalezionych aplikacji na Windows i Linux maszyn wirtualnych (VM działających na platformie Azure lub w danym środowisku). Możesz obserwować maszyn wirtualnych na dwa sposoby. Umożliwia wyświetlenie mapy bezpośrednio z poziomu maszyny Wirtualnej lub wyświetlić mapę z usługi Azure Monitor, aby wyświetlić składniki między grupami maszyn wirtualnych. Ten artykuł pomoże zrozumieć te dwie metody wyświetlania i jak za pomocą funkcji mapy. 

Aby uzyskać informacje o konfigurowaniu usługi Azure Monitor do maszyn wirtualnych, zobacz [włączyć usługi Azure Monitor dla maszyn wirtualnych](vminsights-enable-overview.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Wprowadzenie do środowiska mapy
Przed zagłębieniem się w środowisku mapy, należy zrozumieć, jak przedstawiono i wizualizuje informacji. Czy funkcja mapy wybierz się bezpośrednio z maszyny Wirtualnej lub usługi Azure Monitor, funkcja mapy przedstawia spójne środowisko. Jedyną różnicą jest to, że z usługi Azure Monitor jedną mapę pokazuje wszystkie elementy członkowskie aplikacji n warstwowych lub klastra.

Funkcja mapy umożliwia wizualizowanie zależności maszyny Wirtualnej przez odnajdywanie uruchomione procesy, które mają: 

- Aktywnych połączeń sieciowych między serwerami.
- Czas oczekiwania na połączenie przychodzące i wychodzące.
- Portami w dowolnej architekturze połączenia TCP za pośrednictwem określonego przedziału czasu.  
 
Rozwiń maszynę Wirtualną, aby wyświetlić szczegóły procesu i tylko procesy, które komunikują się z maszyną Wirtualną. Grupa klientów pokazuje liczbę frontonu klienci, którzy łączą się z maszyną wirtualną. Grupy port serwera pokazują liczbę serwerów zaplecza, z którymi łączy się z maszyną Wirtualną. Rozwiń grupę port serwera, aby wyświetlić szczegółową listę serwerów łączących się za pośrednictwem tego portu.  

Po wybraniu maszyny Wirtualnej, **właściwości** w okienku po prawej stronie są wyświetlane właściwości maszyny Wirtualnej. Właściwości obejmują informacje o systemie zgłoszony przez system operacyjny, właściwości maszyny Wirtualnej platformy Azure i wykres pierścieniowy, który podsumowuje odnalezionych połączeń. 

![Zawartość okienka właściwości](./media/vminsights-maps/properties-pane-01.png)

Po prawej stronie okienka wybierz **zdarzenia dziennika** do wyświetlenia listy danych, które maszyny Wirtualnej zostało wysłane do usługi Azure Monitor. Te dane są dostępne dla zapytań.  Wybierz dowolny typ rekordu, aby otworzyć **dzienniki** strony, gdzie zobaczysz wyniki dla danego typu rekordu. Zobaczysz również wstępnie skonfigurowane zapytanie, które są filtrowane względem maszyny Wirtualnej.  

![W okienku dziennika zdarzeń](./media/vminsights-maps/properties-pane-logs-01.png)

Zamknij **dzienniki** strony, a następnie wróć do **właściwości** okienka. Tam, wybierz **alerty** przeglądający alerty kryteria kondycji maszyny Wirtualnej. Funkcja mapy integruje się z usługą Azure Alerts, aby wyświetlić alerty dla wybranego serwera w wybranym zakresie czasu. Serwer Wyświetla ikonę bieżące alerty i **alerty maszyny** okienku są wyświetlane alerty. 

![W okienku alertów](./media/vminsights-maps/properties-pane-alerts-01.png)

Aby funkcja mapy wyświetlania powiązanych alertów, Utwórz regułę alertu, który ma zastosowanie do konkretnego komputera:

- Zawiera klauzulę grupy alertów według komputera (na przykład **komputera interwał 1 minutę**).
- Podstawa alert dla metryki.

Aby uzyskać więcej informacji o alertach platformy Azure i tworzenia reguł alertów, zobacz [Unified alertów w usłudze Azure Monitor](../../azure-monitor/platform/alerts-overview.md).

W prawym górnym rogu **legendy** opcja opisuje symboli i role na mapie. Aby uzyskać bliższe spojrzenie na mapie i przenieść je wokół należy użyć kontrolki powiększania w prawym dolnym rogu. Można ustawić poziom powiększenia i dopasować mapę do rozmiaru strony.  

## <a name="connection-metrics"></a>Metryki połączeń
**Połączeń** okienko wyświetla standardowych metryk dla wybranego połączenia z maszyną Wirtualną za pośrednictwem portu TCP. Metryki obejmują czas odpowiedzi, żądań na minutę, przepływność ruchu sieciowego i łącza.  

![Wykresy łączności sieciowej w okienku połączenia](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Połączenia zakończone niepowodzeniem
Mapa zawiera połączenia zakończone niepowodzeniem dla procesów i komputerów. Czerwoną linię przerywaną, co oznacza, że system klienta nie powiodło się dotrzeć do procesu lub portu. Dla systemów, które używają agenta zależności agent raporty dotyczące prób nawiązania połączenia nie powiodło się. Funkcja mapy monitoruje proces obserwując gniazda TCP, których nie można ustanowić połączenia. Ten błąd może spowodować z zapory, błędnej konfiguracji klienta, serwera lub niedostępna usługi zdalnej.

![Nie powiodło się połączenie na mapie](./media/vminsights-maps/map-group-failed-connection-01.png)

Opis połączenia zakończone niepowodzeniem mogą ułatwić rozwiązywanie problemów z, zweryfikuj migrację, analizowania zabezpieczeń i interpretację ogólna Architektura usług. Połączenia zakończone niepowodzeniem są czasami nieszkodliwe, ale często wskazują na problem. Połączenia może zakończyć się niepowodzeniem, na przykład, gdy środowisko pracy awaryjnej nagle stanie się niedostępny lub gdy dwie warstwy aplikacji nie może komunikować się ze sobą po migracji do chmury.

### <a name="client-groups"></a>Grup klientów
Na mapie grup klientów reprezentują komputerami klienckimi, które połączenie z maszyną zamapowany. Pojedynczej grupy klientów reprezentuje klientów dla poszczególnych proces lub komputera.

![Grupa klientów na mapie](./media/vminsights-maps/map-group-client-groups-01.png)

Aby zobaczyć monitorowanych klientów i adresy IP należące do grupy klientów, wybierz grupę. Zawartość grupy są wyświetlone poniżej.  

![Grupa klientów listę adresów IP na mapie](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Jeśli grupa zawiera monitorowanych i niemonitorowanych klientów, możesz wybrać do odpowiedniej sekcji wykresu pierścieniowego grupy do filtrowania klientów.

### <a name="server-port-groups"></a>Port serwera grup
Port serwera grupy reprezentują portów na serwerach, które mają połączenia przychodzące z maszyny zamapowany. Grupa zawiera port serwera i liczbę serwerów, które mają połączeń do tego portu. Wybierz grupę, aby wyświetlić poszczególne serwery i połączeń. 

![Grupa port serwera, na mapie](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Jeśli grupa zawiera monitorowanych i monitorowany serwerów, możesz wybrać odpowiednie części wykresu pierścieniowego grupy do filtrowania serwery.

## <a name="view-a-map-from-a-vm"></a>Umożliwia wyświetlenie mapy z maszyny Wirtualnej 

Dostęp do usługi Azure Monitor dla maszyn wirtualnych bezpośrednio z poziomu maszyny Wirtualnej:

1. W witrynie Azure portal wybierz **maszyn wirtualnych**. 
2. Z listy wybierz Maszynę wirtualną. W **monitorowanie** wybierz pozycję **Insights (wersja zapoznawcza)** .  
3. Wybierz **mapy** kartę.

Mapa umożliwia wizualizowanie zależności maszyny Wirtualnej przez odnajdywanie uruchamianie grupy procesów i procesy z aktywnymi połączeniami sieciowymi za pośrednictwem określonego przedziału czasu.  

Domyślnie mapy zawiera ostatnich 30 minut. Jeśli chcesz zobaczyć, jak wyglądał zależności w przeszłości, można wysyłać zapytania dotyczące zakresy czasu historycznych maksymalnie jedną godzinę. Aby uruchomić zapytanie, należy użyć **TimeRange** selektor w lewym górnym rogu. Zapytanie, może działać, na przykład podczas zdarzenia lub aby wyświetlić stan przed zmianą.  

![Bezpośredni Przegląd mapy maszyn wirtualnych](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Umożliwia wyświetlenie mapy z zestawu skalowania maszyn wirtualnych

Dostęp do usługi Azure Monitor dla maszyn wirtualnych bezpośrednio z zestawu skalowania maszyn wirtualnych:

1. W witrynie Azure portal wybierz **zestawy skalowania maszyn wirtualnych**.
2. Z listy wybierz Maszynę wirtualną. Następnie w **monitorowanie** wybierz pozycję **Insights (wersja zapoznawcza)** .  
3. Wybierz **mapy** kartę.

Mapa wizualizuje wszystkich wystąpień w zestawie jako węzeł grupy wraz z zależnościami grupy skalowania. Rozwinięty węzeł zawiera listę wystąpień w zestawie skalowania. Te wystąpienia 10 można przewijać w danym momencie. 

Aby załadować mapy dla konkretnego wystąpienia, należy najpierw wybrać tego wystąpienia na mapie. Następnie wybierz pozycję **wielokropka** przycisku (...) po prawej stronie i wybierz polecenie **Załaduj mapę serwera**. Na mapie, który pojawia się Zobacz grupy procesów i procesy z aktywnymi połączeniami sieciowymi za pośrednictwem określonego przedziału czasu. 

Domyślnie mapy zawiera ostatnich 30 minut. Jeśli chcesz zobaczyć, jak wyglądał zależności w przeszłości, można wysyłać zapytania dotyczące zakresy czasu historycznych maksymalnie jedną godzinę. Aby uruchomić zapytanie, należy użyć **TimeRange** selektora. Zapytanie, może działać, na przykład podczas zdarzenia lub aby wyświetlić stan przed zmianą.

![Bezpośredni Przegląd mapy maszyn wirtualnych](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>Można także przejść do mapy dla konkretnego wystąpienia z **wystąpień** widoku dla zestawu skalowania maszyn wirtualnych. W **ustawienia** przejdź do sekcji **wystąpień** > **Insights (wersja zapoznawcza)** .

## <a name="view-a-map-from-azure-monitor"></a>Umożliwia wyświetlenie mapy z usługi Azure Monitor
W usłudze Azure Monitor funkcja mapy zapewnia globalny widok Twoich maszyn wirtualnych i ich zależności. Dostęp do funkcji mapy w usłudze Azure Monitor:

1. W witrynie Azure portal wybierz **Monitor**. 
2. W **Insights** wybierz pozycję **maszyny wirtualne (wersja zapoznawcza)** .
3. Wybierz **mapy** kartę.

   ![Usługa Azure Monitor mapowanie przeglądów z wielu maszyn wirtualnych](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Wybierz obszar roboczy za pomocą **obszaru roboczego** selektor w górnej części strony. Jeśli masz więcej niż jeden obszar roboczy usługi Log Analytics, wybierz obszar roboczy, który został włączony, za pomocą rozwiązania i ma maszynach wirtualnych wysyłających raporty do niego. 

**Grupy** selektor zwraca subskrypcji, grupy zasobów [grup komputerów](../../azure-monitor/platform/computer-groups.md)i zestawy skalowania maszyn wirtualnych, komputerów, które są powiązane z wybranym obszarem roboczym. Wybór dotyczy tylko funkcji mapy i nie jest przenoszone do wydajności i kondycji.

Domyślnie mapy zawiera ostatnich 30 minut. Jeśli chcesz zobaczyć, jak wyglądał zależności w przeszłości, można wysyłać zapytania dotyczące zakresy czasu historycznych maksymalnie jedną godzinę. Aby uruchomić zapytanie, należy użyć **TimeRange** selektora. Zapytanie, może działać, na przykład podczas zdarzenia lub aby wyświetlić stan przed zmianą.  

## <a name="next-steps"></a>Kolejne kroki
- Aby dowiedzieć się, jak korzystać z funkcji Health, zobacz [maszyny Wirtualnej platformy Azure w widoku kondycji](vminsights-health.md). 
- Zidentyfikuj wąskie gardła, sprawdź wydajność oraz zrozumienie ogólnego użycia maszyn wirtualnych, zobacz [wyświetlić stan wydajności dla usługi Azure Monitor dla maszyn wirtualnych](vminsights-performance.md). 
