---
title: Jak wyświetlić zależności aplikacji z usługą Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Mapa jest funkcją monitora platformy Azure dla maszyn wirtualnych, które automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Ten artykuł zawiera szczegółowe informacje dotyczące sposobu używania go w różnych scenariuszach.
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
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: 34e6ce7f3b38dfd583aa557d2f1d7340ea444da9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798047"
---
# <a name="using-azure-monitor-for-vms-preview-map-to-understand-application-components"></a>W przypadku maszyn wirtualnych (wersja zapoznawcza) mapę, aby poznać składniki aplikacji za pomocą usługi Azure Monitor
Wyświetlanie składniki odnalezionych aplikacji na maszynach wirtualnych Windows i Linux, działające na platformie Azure w środowisku można zaobserwować na dwa sposoby, za pomocą usługi Azure Monitor w przypadku maszyn wirtualnych z maszyny wirtualnej, który jest bezpośrednio lub grupami maszyn wirtualnych za pomocą usługi Azure Monitor. 

Ten artykuł pomoże zrozumieć środowisko między dwóch perspektyw i jak za pomocą funkcji mapy. Aby uzyskać informacje o konfigurowaniu usługi Azure Monitor do maszyn wirtualnych, zobacz [włączyć usługi Azure Monitor dla maszyn wirtualnych](vminsights-onboard.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="introduction-to-map-experience"></a>Wprowadzenie do środowiska mapy
Przed zagłębieniem się w wyświetlania mapy dla pojedynczej maszyny wirtualnej lub grupy maszyn wirtualnych, ważne jest, że firma Microsoft zapewnia krótkie wprowadzenie do funkcji, aby zrozumieć sposób prezentowania informacji i reprezentują wizualizacji.  

Czy funkcja mapy wybierz się bezpośrednio z maszyny Wirtualnej lub usługi Azure Monitor, przedstawia spójne środowisko.  Jedyną różnicą jest z usługi Azure Monitor możesz zobaczyć wszystkie elementy członkowskie wielowarstwową aplikację lub klastra, w jedną mapę.

Mapy wizualizuje zależności maszyn wirtualnych, odnajdując uruchomione procesy z aktywnymi połączeniami sieciowymi między serwerami, czas oczekiwania na połączenie przychodzące i wychodzące i porty w dowolnej architekturze połączenia TCP za pośrednictwem określonego przedziału czasu.  Rozwijanie maszyny Wirtualnej przedstawia szczegóły procesu i wyświetlane są tylko procesy, które komunikują się z maszyną Wirtualną. Wskazuje liczbę frontonu klienci, którzy łączą się z maszyną wirtualną z grupy klientów. Liczba serwerów zaplecza maszyna wirtualna łączy się z są wskazywane na serwerze grup portów. Rozwiń grupę portu serwera, aby wyświetlić szczegółową listę serwerów połączonych za pośrednictwem tego portu.  

Po kliknięciu na maszynie wirtualnej **właściwości** podzielonego w okienku po prawej stronie, aby wyświetlić właściwości elementu zaznaczone, takie jak informacje o systemie zgłoszony przez system operacyjny, właściwości maszyny Wirtualnej platformy Azure i pierścieniowe Podsumowanie odnalezionych połączeń. 

![Właściwości systemu komputera](./media/vminsights-maps/properties-pane-01.png)

Na stronie po prawej stronie okienka, kliknij **zdarzenia dziennika** ikonę, aby przełączać fokus okienka, aby wyświetlić listę tabel, które zbiera dane z maszyny Wirtualnej zostało wysłane do usługi Azure Monitor i jest dostępna dla zapytań.  Kliknięcie na jednym z typów rekordów na liście spowoduje otwarcie **dzienniki** strony, aby wyświetlić wyniki dla tego typu, ze wstępnie skonfigurowanym zapytaniem filtrowany na podstawie określonej maszyny wirtualnej.  

![Lista wyszukiwania dzienników w okienku właściwości](./media/vminsights-maps/properties-pane-logs-01.png)

Zamknij **dzienniki** i wrócić do **właściwości** okienka, a następnie wybierz **alerty** przeglądający alerty, które alerty zgłoszone dla maszyny Wirtualnej z kryteria kondycji. Mapa integruje się z alertami platformy Azure, aby wyświetlić wyzwolone alerty dla wybranego serwera w wybranym zakresie czasu. Serwer wyświetla ikony, jeśli istnieją bieżące alerty, a okienko maszyny alertów zawiera alerty. 

![Alerty komputera w okienku właściwości](./media/vminsights-maps/properties-pane-alerts-01.png)

Aby włączyć funkcję Map do wyświetlenia powiązanych alertów, Utwórz regułę alertu, który jest uruchamiany dla określonego komputera. Aby utworzyć odpowiednie alerty:
- Zawiera klauzulę do grupy według komputera (na przykład **komputera interwał 1 minutę**).
- Wybierz alert w oparciu metryki pomiaru.

Aby uzyskać więcej informacji o alertach platformy Azure i tworzenia reguł alertów, zobacz [Unified alertów w usłudze Azure Monitor](../../azure-monitor/platform/alerts-overview.md)

**Legendy** opcji w prawym górnym rogu w tym artykule opisano symboli i role na mapie.  Aby powiększyć bliższe spojrzenie na mapie, a następnie przenieść it wokół kontrolki powiększania u dołu po prawej stronie strony ustawia poziom powiększenia i rozmiaru strony do rozmiaru bieżącej strony.  

## <a name="connection-metrics"></a>Metryki połączeń
**Połączeń** okienku są wyświetlane metryki standardowe łączności dla wybranego połączenia z maszyną Wirtualną za pośrednictwem portu TCP. Metryki obejmują czas odpowiedzi, żądań na minutę, przepływność ruchu sieciowego i łącza.  

![Przykład w okienku wykresy łączności sieciowej](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Połączenia zakończone niepowodzeniem
Połączenia zakończone niepowodzeniem są wyświetlane na mapie dla procesów i komputerów z kreskowanym czerwoną linią wskazującym, czy system klienta nie może dotrzeć do procesu lub portu. Połączenia zakończone niepowodzeniem są zgłaszane z dowolnego systemu za pomocą agenta zależności, w przypadku tego systemu jest próba nawiązania połączenia nie powiodło się. Mapy mierzy ten proces, obserwując gniazda TCP, których nie można ustanowić połączenia. Ten błąd może wynikać z zapory, błędnej konfiguracji klienta, serwera lub usługi zdalnej jest niedostępny.

![Przykład połączenia nie powiodło się na mapie](./media/vminsights-maps/map-group-failed-connection-01.png)

Omówienie połączenia zakończone niepowodzeniem może pomóc w rozwiązywaniu problemów, weryfikacja migracji, analizy zabezpieczeń i zrozumienie ogólna Architektura usług. Połączenia zakończone niepowodzeniem są czasami nieszkodliwe, ale często wskazują bezpośrednio do problemu, takie jak środowisko pracy awaryjnej, nagle staje się niedostępny lub dwie warstwy aplikacji nie będzie w stanie komunikować się ze sobą po migracji do chmury.

### <a name="client-groups"></a>Grupy klientów
Grup klientów na mapie reprezentują komputerów klienckich, które mają połączenie z maszyną zamapowanego. Pojedynczą grupę klienta reprezentuje klientów dla poszczególnych proces lub komputera.

![Przykład grup klienta na mapie](./media/vminsights-maps/map-group-client-groups-01.png)

Aby zobaczyć monitorowanych klientów i adresy IP należące do grupy klientów, wybierz grupę. Zawartość grupy są wymienione pod grupą.  

![Przykład listy adresów IP grupy klienta na mapie](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Grupa zawiera klientów z monitorowanych i bez monitorowania, po wybraniu odpowiedniej sekcji wykresu pierścieniowego grupy do filtrowania klientów.

### <a name="server-port-groups"></a>Port serwera grup
Port serwera grupy reprezentują porty serwera na serwerach, które mają połączenia przychodzące z maszyny zamapowany. Grupa zawiera port serwera i liczbę serwerów przy użyciu połączenia do tego portu. Wybierz grupę, aby wyświetlić poszczególne serwery i połączeń na liście. 

![Przykład grupy portów serwera, na mapie](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Grupa zawiera serwery monitorowanych i bez monitorowania, po wybraniu odpowiedniej sekcji wykresu pierścieniowego grupy do filtrowania serwery.

## <a name="view-map-directly-from-a-virtual-machine"></a>Wyświetlanie mapy bezpośrednio z maszyny wirtualnej 

Aby uzyskać dostęp do usługi Azure Monitor dla maszyn wirtualnych bezpośrednio z maszyny wirtualnej, wykonaj następujące czynności.

1. W witrynie Azure portal wybierz **maszyn wirtualnych**. 
2. Z listy wybierz Maszynę wirtualną i **monitorowanie** wybierz sekcję **Insights (wersja zapoznawcza)**.  
3. Wybierz **mapy** kartę.

Mapa wizualizuje zależności maszyn wirtualnych, które działa grupa procesów i procesy z aktywnymi połączeniami sieciowymi, za pośrednictwem określonego przedziału czasu.  Domyślnie mapy zawiera ostatnich 30 minut.  Za pomocą **TimeRange** selektor w lewym górnym rogu, możesz wyszukać zakresy czasu historycznych maksymalnie jedną godzinę, aby pokazać, jak zależności będzie wyglądał w przeszłości (na przykład podczas zdarzenia lub przed wystąpieniem zmiany).  

![Bezpośredni Przegląd mapy maszyn wirtualnych](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-map-from-azure-monitor"></a>Wyświetlanie mapy z usługi Azure Monitor
Z usługi Azure Monitor funkcja mapy zapewnia globalny widok Twoich maszyn wirtualnych i ich zależności.  Aby skorzystać z funkcji mapy, z usługi Azure Monitor, wykonaj następujące czynności. 

1. W witrynie Azure portal wybierz **Monitor**. 
2. Wybierz **maszyny wirtualne (wersja zapoznawcza)** w **Insights** sekcji.
3. Wybierz **mapy** kartę.

![Omówienie mapy wielu maszyn wirtualnych w usłudze Azure Monitor](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Z **obszaru roboczego** selektor w górnej części strony, jeśli masz więcej niż jednym obszarze roboczym usługi Log Analytics wybierz obszar roboczy, który jest włączone za pomocą rozwiązania i ma raportujących do niego maszyny wirtualne. **Grupy** selektor zwróci subskrypcji, grupy zasobów [grup komputerów](../../azure-monitor/platform/computer-groups.md), a usługi VM scale sets komputerów powiązanych z wybranym obszarem roboczym. Wybór tylko dotyczy funkcji mapy i nie jest przenoszone wydajności lub mapy.

Domyślnie mapy zawiera ostatnich 30 minut. Za pomocą **TimeRange** selektor, możesz wyszukać zakresy czasu historycznych maksymalnie jedną godzinę, aby pokazać, jak zależności będzie wyglądał w przeszłości (na przykład podczas zdarzenia lub przed wystąpieniem zmiany).   

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak korzystać z funkcji health, zobacz [Wyświetl kondycję maszyn wirtualnych platformy Azure](vminsights-health.md), lub do identyfikowania wąskich gardeł i ogólnego użycia za pomocą wydajność maszyn wirtualnych, zobacz [widok usługi Azure Monitor wydajności maszyn wirtualnych](vminsights-performance.md). 
