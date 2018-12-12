---
title: Usługa Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) — często zadawane pytania | Dokumentacja firmy Microsoft
description: Usługa Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) to rozwiązanie na platformie Azure, który łączy monitorowania kondycji i wydajności systemu operacyjnego maszyny Wirtualnej platformy Azure. Automatycznie odnajduje składniki aplikacji i zależności z innymi zasobami oraz mapuje komunikację między nimi. Ten artykuł zawiera odpowiedzi na często zadawane pytania.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2018
ms.author: magoedte
ms.openlocfilehash: f5865cf72f413db49e70a08305de54aff955607b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53075237"
---
# <a name="azure-monitor-for-vms-preview-faq"></a>Usługa Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) — często zadawane pytania
Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Monitor dla maszyn wirtualnych. Jeśli masz dodatkowe pytania dotyczące rozwiązania, przejdź do strony [forum dyskusyjnego platformy Azure](https://feedback.azure.com/forums/34192--general-feedback) i Publikuj swoje pytania. Gdy często zadawanych pytań, dodamy ich do tego artykułu, dzięki czemu są one szybko i łatwo.

## <a name="can-i-deploy-vms-to-an-existing-workspace"></a>Czy mogę wdrożyć maszyny wirtualne do istniejącego obszaru roboczego?
Maszyn wirtualnych są już połączone z obszarem roboczym usługi Log Analytics, można nadal używać tego obszaru roboczego, wdrażając je do usługi Azure Monitor dla maszyn wirtualnych. Obszar roboczy musi istnieć w jednym z obsługiwanych regionów wymienionych w sekcji "Wymagania wstępne" [wdrożenia usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)](vminsights-onboard.md#prerequisites).

Podczas wdrażania firma Microsoft skonfiguruj liczniki wydajności dla obszaru roboczego. Ta akcja powoduje, że maszyny wirtualne, które dane raportu do obszaru roboczego, aby rozpocząć zbieranie informacji dla wyświetlania i analizy w usłudze Azure Monitor dla maszyn wirtualnych. W rezultacie zobaczysz dane dotyczące wydajności ze wszystkich maszyn wirtualnych, które są połączone z wybranym obszarem roboczym. Funkcje kondycji i mapy są włączone tylko dla maszyn wirtualnych, które zostały określone dla wdrożenia.

Aby uzyskać więcej informacji o wydajności, które są włączone liczniki, zobacz [wdrożenia usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)](vminsights-onboard.md).

## <a name="can-i-deploy-vms-to-a-new-workspace"></a>Czy mogę wdrożyć maszyny wirtualne do nowego obszaru roboczego? 
Jeśli Twoje maszyny wirtualne nie są obecnie połączone z istniejącego obszaru roboczego usługi Log Analytics, musisz utworzyć nowy obszar roboczy do przechowywania danych. Można utworzyć, wykonując automatyczne konfigurowanie pojedynczej maszyny Wirtualnej dla usługi Azure Monitor dla maszyn wirtualnych w witrynie Azure portal.

Jeśli zdecydujesz się użyć metody opartych na skryptach, zobacz [wdrożenia usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)](vminsights-onboard.md). 

## <a name="what-can-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Co mogę zrobić, jeśli Moja maszyna wirtualna jest już raportowania do istniejącego obszaru roboczego?
Jeśli masz już zbieranie danych z maszyn wirtualnych, może być już skonfigurowano je raportować dane do istniejącego obszaru roboczego usługi Log Analytics. Tak długo, jak ten obszar roboczy znajduje się w jednym z naszych obsługiwane regiony, do istniejącego obszaru roboczego można włączyć usługi Azure Monitor dla maszyn wirtualnych. Pracujemy nad do obsługi dodatkowych regionów.

>[!NOTE]
>Firma Microsoft skonfiguruj liczniki wydajności dla obszaru roboczego, który ma wpływ na wszystkie maszyny wirtualne, które raportują do obszaru roboczego, czy zamierzasz wdrożyć je do usługi Azure Monitor dla maszyn wirtualnych. Aby uzyskać więcej informacji na temat sposobu konfiguracji liczników wydajności dla obszaru roboczego, zobacz sekcję "Konfigurowanie liczników" [Windows i Linux źródła danych dotyczących wydajności w usłudze Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md). Aby uzyskać informacji na temat liczników skonfigurowany dla usługi Azure Monitor dla maszyn wirtualnych, zobacz [wdrożenia usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)](vminsights-onboard.md). 

## <a name="why-did-my-vm-deployment-fail"></a>Dlaczego nie Moje wdrożenie maszyny Wirtualnej?
Gdy wdrożysz Maszynę wirtualną platformy Azure w witrynie Azure portal, zachodzą następujące zdarzenia:

* Domyślnego obszaru roboczego usługi Log Analytics zostanie utworzony, jeśli wybrano opcję.
* Liczniki wydajności są skonfigurowane dla wybranego obszaru roboczego. Jeśli ten krok nie powiedzie się, niektóre wykresy wydajności i tabele nie są wyświetlane dane dla maszyny Wirtualnej została wdrożona. Możesz rozwiązać ten problem, uruchamiając skrypt programu PowerShell, które jest opisane w sekcji "Włączanie przy użyciu programu PowerShell" [wdrożenia usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)](vminsights-onboard.md#enable-with-powershell).
* Agenta usługi Log Analytics jest instalowany na maszynach wirtualnych Azure z rozszerzenia maszyny Wirtualnej, jeśli jest to wymagane. 
* Usługi Azure Monitor dla agenta zależności mapy maszyn wirtualnych jest zainstalowany na maszynach wirtualnych platformy Azure z rozszerzeniem, jeśli jest to wymagane. 
* Składniki platformy Azure Monitor, które obsługują tę funkcję kondycji są skonfigurowane, jeśli to konieczne, a maszyna wirtualna jest skonfigurowana do danych raportu o kondycji.

Podczas wdrażania możemy sprawdzić stan dla każdego z powyższych kroków i zwrócenia stanu powiadomień do Ciebie w portalu. Konfiguracja obszaru roboczego i podczas instalacji agenta zwykle trwa 5 – 10 minut. Wyświetlanie danych monitorowania i kondycji w witrynie Azure portal wykonać dodatkowe 5 do 10 minut. 

Jeśli zainicjowano wdrożenia i wyświetlone komunikaty wskazujące, czy należy wdrożyć maszynę Wirtualną, zezwala na maksymalnie 30 minut dla maszyny Wirtualnej ukończyć proces. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Nie widzę żadnych danych w przypadku wykresów wydajności dla mojej maszyny Wirtualnej
Jeśli dane wydajności nie jest wyświetlane w tabeli dysku lub wykresy wydajności, liczniki wydajności mogą nie być skonfigurowane w obszarze roboczym. Aby rozwiązać ten problem, uruchom skrypt programu PowerShell, które jest opisane w sekcji "Włączanie przy użyciu programu PowerShell" [wdrożenia usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)](vminsights-onboard.md#enable-with-powershell).

## <a name="how-is-the-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Czym różni się usługi Azure Monitor dla funkcji mapy maszyn wirtualnych z mapy usługi?
Usługi Azure Monitor dla funkcji mapy maszyn wirtualnych zależy od rozwiązania Service Map, ale się w następujący sposób:

* Widoku mapy są dostępne z poziomu okienka maszyny Wirtualnej i z usługi Azure Monitor, maszyn wirtualnych w usłudze Azure Monitor.
* Połączenia na mapie teraz wyświetlenia wizytówki dane metryk połączenia w panelu po stronie.
* Nowy interfejs API jest używany do utworzenia mapy w celu skuteczniejszej obsługi bardziej złożone mapy.
* Monitorowane maszyny wirtualne są teraz w węźle grupy klienta i pierścieniowej wykres przedstawia współczynnik monitorowanych do niemonitorowanych maszyn wirtualnych. Można również filtrować listę komputerów, gdy grupa jest rozwinięta.
* Monitorowane maszyny wirtualne są teraz w węzłach grupy portów serwera, a wykres pierścieniowy Wyświetla stopień monitorowanych do niemonitorowanych maszyn. Można również filtrować listę komputerów, gdy grupa jest rozwinięta.
* Zaktualizowano style mapy są bardziej spójne z mapy aplikacji z usługi Azure Application Insights.
* Panele po stronie zostały zaktualizowane, ale jeszcze nie masz pełny zestaw integracji, które były obsługiwane w rozwiązania Service Map: zarządzanie aktualizacjami, śledzenie zmian, zabezpieczeń i pomocy technicznej. 
* Zaktualizowano opcję wybierania grup i komputerów do mapowania. Obsługuje teraz subskrypcji, grupy zasobów, zestawy skalowania maszyn wirtualnych platformy Azure i usług w chmurze.
* Nie można utworzyć nowych grup składników mapy usług maszyny w usłudze Azure Monitor dla funkcji mapy maszyn wirtualnych. 

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Dlaczego mój wykresy wydajności są wyświetlane linie kropkowane?

Wykresy wydajności wyświetlanie linii kropkowanej zamiast linia ciągła z kilku powodów:
* Może być przerwa w zbieraniu danych. 

* Ustawieniem domyślnym dla pobierania próbek danych jest co 60 sekund. Kropkowane linie mogą być wyświetlane, jeśli wybierz zakres czasu wąskie wykresu, a Twoje częstotliwość próbkowania jest mniejsza niż rozmiar zasobnika użyte w wykresie. Załóżmy, że wybrano opcję częstotliwości próbkowania 10 minut każdego przedziału na wykresie wynosi 5 minut. W tym przypadku wybierania szerszy zakres czasu, aby wyświetlić powinno spowodować linii wykresu pojawiało się jako linia ciągła, a nie kropki.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Grupy obsługujące z usługą Azure Monitor dla maszyn wirtualnych?
Tak, po zainstalowaniu agenta zależności, zbierane są informacje z maszyn wirtualnych, aby wyświetlić grupy na podstawie subskrypcji, grupy zasobów, maszyna wirtualna zestawów skalowania i usług w chmurze. Jeśli wcześniej użyto rozwiązania Service Map i utworzono grupy na komputerze, te grupy są wyświetlane również. Grupy komputerów pojawi się także w filtrze grup, jeśli zostały utworzone dla obszaru roboczego, który jest wyświetlany. 

## <a name="how-can-i-display-the-details-about-whats-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Jak wyświetlić szczegółowe informacje o czego zależy używany 95. percentyl zagregowanego wydajności wykresy liniowe?
Domyślnie lista jest sortowana Wam maszyn wirtualnych, które mają najwyższą wartość 95. percentyl dla wybranej metryki. Wyjątek stanowi **dostępnej pamięci** wykres, który pokazuje maszyn o najniższej wartości percentyla piąty. Wybierz wykres, aby otworzyć **lista N najlepszych** widok z odpowiednią wybraną metryką.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-various-virtual-networks-and-subnets"></a>Jak funkcja mapy obsługuje zduplikowane adresy IP w różnych sieciach wirtualnych oraz podsieci?
Jeśli masz duplikowania zakresów adresów IP przy użyciu obu maszyn wirtualnych lub zestawów skalowania maszyn wirtualnych platformy Azure między podsieciami i sieciami wirtualnymi, może to spowodować usługi Azure Monitor dla funkcji mapy maszyn wirtualnych do wyświetlenia nieprawidłowe informacje. Jesteśmy pamiętać o tym problemie i Rozpatrujemy różne opcje, aby ulepszyć środowisko pracy.

## <a name="does-the-map-feature-support-ipv6"></a>Funkcja mapy obsługuje protokół IPv6?
Funkcja mapy aktualnie obsługuje tylko protokół IPv4, dlatego Rozpatrujemy Obsługa protokołu IPv6. Obsługujemy również IPv4, który tunel wewnątrz protokołu IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-why-is-the-map-difficult-to-view"></a>Po załadowaniu mapy dla grupy zasobów lub innych dużych, dlaczego jest plan utrudniało wyświetlanie?
Mimo że firma Microsoft ulepszyła funkcji mapy do obsługi dużych i złożonych konfiguracji, firma Microsoft zorientujesz się, że mapa może mieć wiele węzłów, połączeń i węzłów działających jako klaster. Ponadto zobowiązujemy się do zwiększenia pomocy technicznej w celu zwiększenia skalowalności w dalszym ciągu.  

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-from-the-network-chart-on-the-azure-vm-overview-page"></a>Dlaczego Wykres sieci na karcie wydajność się różnić od wykresu sieci, na stronie Omówienie maszyny Wirtualnej platformy Azure?

Strona przeglądu Maszynie wirtualnej platformy Azure Wyświetla wykresy oparte na hosta pomiaru działań w maszynie Wirtualnej gościa. Wykres sieci, na stronie Omówienie maszyny Wirtualnej platformy Azure przedstawia tylko ruch sieciowy będą naliczane. Ten ekran nie uwzględnia ruchu między sieciami wirtualnymi. Dane i wykresy wyświetlane dla usługi Azure Monitor dla maszyn wirtualnych na podstawie danych z maszyny Wirtualnej gościa, a na wykresach sieci są wyświetlane wszystkie TCP/IP ruchu, który ma ruchu przychodzącego i wychodzącego do tej maszyny Wirtualnej, uwzględniając ruch między sieciami wirtualnymi.

## <a name="what-are-the-limitations-of-the-log-analytics-free-pricing-plan"></a>Jakie są ograniczenia planu cenowego bezpłatnych analizy dziennika?
Jeśli skonfigurowano usługi Azure Monitor z obszarem roboczym usługi Log Analytics przy użyciu *bezpłatna* warstwy cenowej, usługi Azure Monitor dla funkcji mapy maszyny wirtualne obsługuje połączeń tylko pięć maszyn do obszaru roboczego. 

Załóżmy na przykład, że masz pięć maszyny wirtualne podłączone do obszaru roboczego warstwy bezpłatna. Jeśli rozłączyć jedną maszynę Wirtualną, a później połączyć nowy nowej maszyny Wirtualnej nie jest monitorowane i widoczne na stronie mapy. W tym scenariuszu po otwarciu nowej maszyny Wirtualnej zostanie wyświetlony monit użyj **Wypróbuj teraz usługę** opcji, a następnie wybierz pozycję **Insights (wersja zapoznawcza)** z okienka po lewej stronie, nawet w przypadku, po jego zainstalowaniu na maszynie Wirtualnej. Jednakże zostanie wyświetlony monit z opcjami jak zwykle będzie Jeśli maszyna wirtualna nie zostały wdrożone do usługi Azure Monitor dla maszyn wirtualnych. 

## <a name="next-steps"></a>Kolejne kroki
Aby poznać wymagania i metod umożliwiających monitorowanie maszyn wirtualnych, zobacz [wdrożenia usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)](vminsights-onboard.md).
