---
title: Usługa Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) — często zadawane pytania | Dokumentacja firmy Microsoft
description: Usługa Azure Monitor dla maszyn wirtualnych to rozwiązanie na platformie Azure, która łączy kondycji i wydajności monitorowania systemu operacyjnego maszyny Wirtualnej platformy Azure, a także automatycznie odnajdywania składników aplikacji i zależności z innymi zasobami oraz mapuje komunikację między je. Ten artykuł zawiera odpowiedzi na często zadawane pytania.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: magoedte
ms.openlocfilehash: 420ba9d74532095c2d028fef8f549d532e5dfa05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522208"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>Usługa Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) — często zadawane pytania
Ta FAQ firmy Microsoft znajduje się lista często zadawane pytania dotyczące usługi Azure Monitor dla maszyn wirtualnych. Jeśli masz dodatkowe pytania dotyczące rozwiązania, przejdź do strony [forum dyskusyjne](https://feedback.azure.com/forums/34192--general-feedback) i Publikuj swoje pytania. Gdy zadawane pytanie dodajemy go do tego artykułu tak, aby możliwe było szybkie i łatwe.

## <a name="can-i-onboard-to-an-existing-workspace"></a>Mogę dołączyć do istniejącego obszaru roboczego?
W przypadku maszyn wirtualnych są już połączone z obszarem roboczym usługi Log Analytics, mogą nadal używać tego obszaru roboczego podczas dołączania do usługi Azure Monitor w przypadku maszyn wirtualnych pod warunkiem, że jest w jednym z obsługiwanych regionów na liście [tutaj](vminsights-enable-overview.md#prerequisites).

Podczas dołączania, firma Microsoft skonfiguruj liczniki wydajności dla obszaru roboczego, który spowoduje, że wszystkie maszyny wirtualne raportowania danych do obszaru roboczego, aby rozpocząć zbieranie tych informacji do wyświetlania i analizy w usłudze Azure Monitor dla maszyn wirtualnych.  W rezultacie zobaczysz dane dotyczące wydajności z wszystkich maszyn wirtualnych połączonych z wybranym obszarem roboczym.  Funkcje kondycji i mapy tylko są włączone dla maszyn wirtualnych, które wybrano dołączyć.

Aby uzyskać więcej informacji o wydajności, które są włączone liczniki, zobacz nasze [Włącz przegląd](vminsights-enable-overview.md#performance-counters-enabled) artykułu.

## <a name="can-i-onboard-to-a-new-workspace"></a>Mogę dołączyć do nowego obszaru roboczego? 
Jeśli maszyny wirtualne nie są aktualnie połączone do istniejącego obszaru roboczego usługi Log Analytics, musisz utworzyć nowy obszar roboczy do przechowywania danych. Tworzenie nowego domyślnego obszaru roboczego odbywa się automatycznie w przypadku skonfigurowania jednej maszyny Wirtualnej platformy Azure dla usługi Azure Monitor dla maszyn wirtualnych za pośrednictwem witryny Azure portal.

Jeśli zdecydujesz się użyć metody opartych na skryptach, te kroki są objęte [włączyć usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) przy użyciu szablonu programu Azure PowerShell lub Menedżera zasobów](vminsights-enable-at-scale-powershell.md) artykułu. 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Co należy zrobić, jeśli Moja maszyna wirtualna jest już raportowania do istniejącego obszaru roboczego?
Jeśli są już zbieranie danych z maszyn wirtualnych, może już skonfigurowano je raportować dane do istniejącego obszaru roboczego usługi Log Analytics.  Tak długo, jak ten obszar roboczy znajduje się w jednym z naszych obsługiwane regiony, do tego istniejącego obszaru roboczego można włączyć usługi Azure Monitor dla maszyn wirtualnych.  Jeśli obszar roboczy, z którego korzystasz już nie jest w jednym z naszych obsługiwane regiony, będzie niemożliwe do dołączenia do usługi Azure Monitor dla maszyn wirtualnych w tej chwili.  Pracujemy nad do obsługi dodatkowych regionów.

>[!NOTE]
>Firma Microsoft skonfiguruj liczniki wydajności dla obszaru roboczego, który ma wpływ na wszystkie maszyny wirtualne, które raportują do obszaru roboczego, czy została wybrana do dołączenia ich do usługi Azure Monitor dla maszyn wirtualnych. Aby uzyskać szczegółowe informacje na temat sposobu konfiguracji liczników wydajności dla obszaru roboczego, można znaleźć na naszej [dokumentacji](../../azure-monitor/platform/data-sources-performance-counters.md). Informacji na temat liczników skonfigurowany dla usługi Azure Monitor dla maszyn wirtualnych, można znaleźć naszych [włączenia monitora platformy Azure dla maszyn wirtualnych](vminsights-enable-overview.md#performance-counters-enabled) artykułu.  

## <a name="why-did-my-vm-fail-to-onboard"></a>Dlaczego moja maszyna wirtualna nie dołączania?
Podczas dołączania maszyny Wirtualnej platformy Azure w witrynie Azure portal, wykonywane są następujące kroki:

* Domyślnego obszaru roboczego usługi Log Analytics zostanie utworzony, jeśli wybrano opcję.
* Liczniki wydajności są skonfigurowane dla wybranego obszaru roboczego. Jeśli ten krok nie powiedzie się, można zauważyć, że niektóre wykresy wydajności i tabele nie są wyświetlane dane dla maszyny Wirtualnej została dołączona. Można to naprawić, uruchamiając skrypt programu PowerShell udokumentowane [tutaj](vminsights-enable-at-scale-powershell.md#enable-performance-counters).
* Agenta usługi Log Analytics jest zainstalowany na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia maszyny Wirtualnej, jeśli ustalił, że jest to wymagane.  
* Usługi Azure Monitor dla agenta zależności mapy maszyn wirtualnych jest zainstalowany na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia, jeśli ustalił, że jest to wymagane.  
* Obsługa funkcji kondycji składników platformy Azure Monitor są skonfigurowane, jeśli to konieczne, a maszyna wirtualna jest skonfigurowana do danych raportu o kondycji.

Podczas procesu dołączania, możemy sprawdzić stan każdego z powyższych próbę zwrócenia stanu powiadomień do Ciebie w portalu. Konfiguracja obszaru roboczego i podczas instalacji agenta zwykle trwa 5 – 10 minut. Wyświetlanie danych monitorowania i kondycji w portalu wykonaj dodatkowe 5 do 10 minut.  

Jeśli zainicjowano dołączania i wyświetlić komunikaty wskazujący, że maszyna wirtualna musi być dołączona umożliwiają do 30 minut dla maszyny Wirtualnej ukończyć proces. 

## <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Tylko usługi Azure Monitor I włączone dla maszyn wirtualnych, dlaczego są wyświetlane wszystkie monitorowane przez funkcję kondycji maszyn wirtualnych?
Funkcja kondycji jest włączona dla wszystkich maszyn wirtualnych, które są połączone z obszarem roboczym usługi Log Analytics, nawet wtedy, gdy akcja jest inicjowane dla pojedynczej maszyny Wirtualnej.

## <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Można zmodyfikować harmonogram gdy kryteria kondycji sprawdza warunek?
Nie, w tej wersji nie można zmodyfikować okres czasu i częstotliwość kryteria kondycji. 

## <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>Czy można wyłączyć kryteria kondycji dla warunku, który nie należy monitorować?
Kryteria kondycji nie może być wyłączone w tej wersji.

## <a name="are-the-health-alert-severities-configurable"></a>Czy kondycji ważności alertu można skonfigurować?  
Ważność alertu kondycji nie może być modyfikowany, ich można tylko włączać lub wyłączać. Ponadto niektóre zaktualizować wagi alertu na podstawie stanu kondycji kryteriów. 

## <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Jeśli ponownie I skonfigurować ustawienia kryteria kondycji konkretnego, może ona należeć do zakresu określonego wystąpienia?  
W przypadku zmodyfikowania wszystkie ustawienia wystąpienia kryterium kondycji wszystkich wystąpień tego samego typu na maszynie Wirtualnej platformy Azure kryteria kondycji są modyfikowane. Na przykład w przypadku modyfikowania próg wystąpienia kryterium kondycji wolnego miejsca na dysku, które odnosi się do dysku logicznego C: tego progu ma zastosowanie do wszystkich innych dysków logicznych, które są odnajdywane i monitorowane pod kątem tej samej maszyny Wirtualnej.

## <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>Funkcja kondycji monitoruje procesorów logicznych i rdzeni?
Nie, poszczególne procesora i kryteria kondycji poziomu procesor logiczny nie jest uwzględniony w Windows, tylko łączne wykorzystanie procesora CPU jest monitorowana przez domyślne można obliczyć efektywnego wykorzystania procesora CPU na podstawie całkowitej liczby procesorów logicznych, które są dostępne dla maszyny Wirtualnej platformy Azure. 

## <a name="are-all-health-criteria-thresholds-configurable"></a>Czy wszystkie dane kondycji można skonfigurować progi kryteria?  
Progi dla kryteria kondycji, których platformą docelową maszyny Wirtualnej z systemem Windows nie są można modyfikować, ponieważ ich stany kondycji są ustawione na *systemem* lub *dostępne*. Podczas wysyłania zapytania stanu kondycji z [API Monitor obciążenia](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components), wyświetla *OperatorPorównania* wartość **mniejsze** lub **większe** z *próg* wartość **4** usługi lub jednostki jeśli:
   - Kondycja usługi klienta DNS — usługa nie jest uruchomiona. 
   - Kondycja usługi klienta DHCP — usługa nie jest uruchomiona. 
   - Kondycja usługi RPC — usługa nie jest uruchomiona. 
   - Kondycja usługi zapory Windows — usługa nie jest uruchomiona.
   - Kondycja usługi dziennika zdarzeń Windows — usługa nie jest uruchomiona. 
   - Kondycja usługi Serwer — usługa nie jest uruchomiona. 
   - Kondycja usługi zdalnego zarządzania Windows — usługi nie jest uruchomiony. 
   - Błąd systemu plików lub uszkodzeniem — dysku logicznego jest niedostępny.

Progi następujące kryteria kondycji systemu Linux nie są można modyfikować, ponieważ ich kondycji, są już ustawione na *true*. Wyświetla stan kondycji *OperatorPorównania* z wartością **mniejsze** i *próg* wartość **1** po otrzymaniu kwerendy od Obciążenie monitorowania interfejsu API dla jednostki, w zależności od jej kontekstu:
   - Stan dysku logicznego — dysk logiczny nie jest online / dostępne
   - Stan dysku — dysku nie jest online / dostępne
   - Stan karty sieciowej — karty sieciowej jest wyłączona.

## <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Jak zmodyfikować alerty, które są dołączone do funkcji Health?
Reguły alertów, które są zdefiniowane dla każdego kryterium kondycji nie są wyświetlane w witrynie Azure portal. Można włączać lub wyłączać alert o kondycji reguły tylko w [API Monitor obciążenia](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Ponadto nie można przypisać [grupy akcji usługi Azure Monitor](../../azure-monitor/platform/action-groups.md) dla alertów dotyczących kondycji w witrynie Azure portal. Interfejs API ustawienie powiadomień umożliwiają tylko skonfigurowanie grupy akcji wyzwolenie przy każdym wyzwoleniu alertu kondycji. Obecnie można przypisać grup akcji względem maszyny Wirtualnej tak, aby wszystkie *alerty dotyczące kondycji* uruchamiane względem wyzwalacza maszyn wirtualnych tej samej grupy akcji. W przeciwieństwie do tradycyjnych alertów platformy Azure nie obowiązuje koncepcja grupy osobną akcję dla każdej reguły alertu kondycji. Ponadto podczas są wyzwalane przez alerty dotyczące kondycji są obsługiwane tylko grupy akcji, które są skonfigurowane do dostarczania poczty e-mail lub powiadomienia SMS. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Nie widzę żadnych danych w przypadku wykresów wydajności dla mojej maszyny Wirtualnej
Jeśli nie widzisz dane dotyczące wydajności w tabeli dysku lub niektóre wykresy wydajności liczniki wydajności nie może być skonfigurowany w obszarze roboczym. Aby rozwiązać problem, uruchom następujące polecenie [skrypt programu PowerShell](vminsights-enable-at-scale-powershell.md#enable-with-powershell).

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Czym różni się usługi Azure Monitor dla funkcji mapy maszyn wirtualnych z mapy usługi?
Usługi Azure Monitor dla funkcji mapy maszyn wirtualnych jest oparty na mapie usługi, ale się w następujący sposób:

* Widok mapy jest możliwy z bloku maszyny Wirtualnej i z usługi Azure Monitor dla maszyn wirtualnych w ramach usługi Azure Monitor.
* Połączenia na mapie teraz kliknąć, aby wyświetlić dane metryk połączenia w panelu po stronie dla wybranego połączenia.
* Brak nowego interfejsu API, który jest używany do tworzenia mapy w celu skuteczniejszej obsługi bardziej złożone mapy.
* Monitorowane maszyny wirtualne znajdują się teraz w węźle grupy klienta i wykres pierścieniowy zawiera część monitorowanych vs niemonitorowanych maszyn wirtualnych w grupie.  Jego można również filtrować listę maszyn, gdy grupa jest rozwinięta.
* Monitorowane maszyny wirtualne znajdują się teraz w węzłach grupy portów serwera, a wykres pierścieniowy zawiera część monitorowanych vs niemonitorowanych maszyn w grupie.  Jego można również filtrować listę maszyn, gdy grupa jest rozwinięta.
* Zaktualizowano style mapy są bardziej spójne z mapy aplikacji z usługi Application insights.
* Panele po stronie zostały zaktualizowane, a nie masz pełny zestaw integracji firmy, były obsługiwane w rozwiązania Service Map — zarządzanie aktualizacjami, śledzenie zmian, zabezpieczeń i pomocy technicznej. 
* Opcja dotyczące wybierania grup i komputerów do mapowania została zaktualizowana i obsługuje teraz subskrypcji, grupy zasobów, zestawy skalowania maszyn wirtualnych platformy Azure i usług w chmurze.
* Nie można utworzyć nowych grup składników mapy usług maszyny w usłudze Azure Monitor dla funkcji mapy maszyn wirtualnych.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Dlaczego mój wykresy wydajności są wyświetlane linie kropkowane?
Może to wystąpić z kilku powodów.  W przypadkach, w przypadku, gdy występuje przerwa w zbieraniu danych Firma Microsoft będzie przedstawiać wiersze jako kropkami.  Jeśli zmodyfikowano częstotliwość próbkowania danych liczników wydajności, włączone (ustawienie domyślne jest do zbierania danych co 60 sekund), można wyświetlić linii kropkowanej wykresu wybierz zakres czasu wąskie wykresu i Twoje częstotliwość próbkowania jest mniejsza niż użyte w wykresie rozmiaru przedziału (na przykład częstotliwość próbkowania wynosi 10 minut i każdego przedziału na wykresie wynosi 5 minut).  Wybieranie szerszy zakres czasu, aby wyświetlić powinno spowodować linii wykresu, w tym przypadku były wyświetlane jako linia ciągła, a nie kropki.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Grupy obsługujące z usługą Azure Monitor dla maszyn wirtualnych?
Tak, po zainstalowaniu agenta zależności, zbierane są informacje z maszyn wirtualnych, aby wyświetlić grupy na podstawie subskrypcji, grupy zasobów, maszyna wirtualna zestawów skalowania i usług w chmurze.  Jeśli masz doświadczenie z rozwiązania Service Map i utworzono grupy na komputerze, są one widoczne również.  Grupy komputerów pojawi się także w filtrze grup Jeśli utworzono je do obszaru roboczego, który jest wyświetlany. 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Jak wyświetlić szczegóły dla czego zależy używany 95. percentyl zagregowanego wydajności wykresy liniowe?
Domyślnie lista jest sortowana Wam maszyn wirtualnych, które mają najwyższą wartość 95. percentyl dla wybranej metryki, z wyjątkiem wykres dostępnej pamięci, który pokazuje maszyn o najniższej wartości 5. percentyl.  Kliknięcie na wykresie spowoduje otwarcie **lista N najlepszych** widok z odpowiednią wybraną metryką.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Jak funkcja mapy obsługuje zduplikowane adresy IP w różnych sieciach wirtualnych i podsieci?
Jeśli są duplikowania zakresów adresów IP przy użyciu maszyn wirtualnych lub maszyn wirtualnych zestawów skalowania w podsieci i sieci wirtualne, może to spowodować usługi Azure Monitor dla maszyn wirtualnych mapę, aby wyświetlić nieprawidłowe informacje. Jest to znany problem i Rozpatrujemy różne opcje, aby ulepszyć to środowisko.

## <a name="does-map-feature-support-ipv6"></a>Jest mapowany obsługi różnych funkcji IPv6?
Funkcja mapy aktualnie obsługuje tylko IPv4, dlatego Rozpatrujemy Obsługa protokołu IPv6. Obsługujemy również IPv4, który tunel wewnątrz protokołu IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Po załadowaniu mapę dla grupy zasobów lub innych dużych mapy jest trudny do wyświetlenia
Gdy wprowadziliśmy ulepszenia do mapy do obsługi dużych i złożonych konfiguracji Zdajemy sobie sprawę, mapy może mieć wiele węzłów, połączeń i języka node działa jako klaster.  Ponadto zobowiązujemy się do zwiększenia pomocy technicznej w celu zwiększenia skalowalności w dalszym ciągu.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Dlaczego Wykres sieci na karcie Wydajność wygląda inaczej niż wykresu sieci, na stronie Omówienie maszyny Wirtualnej platformy Azure?

Strona przeglądu Maszynie wirtualnej platformy Azure Wyświetla wykresy oparte na hosta pomiaru działań w maszynie Wirtualnej gościa.  Wykresu sieci na omówienie maszyny Wirtualnej platformy Azure tylko Wyświetla ruch sieciowy będą naliczane.  Obejmuje to ruch między sieciami wirtualnymi.  Dane i wykresy wyświetlane dla usługi Azure Monitor dla maszyn wirtualnych na podstawie danych z maszyny Wirtualnej gościa i wykresach sieci są wyświetlane wszystkie TCP/IP ruchu przychodzącego i wychodzącego do tej maszyny Wirtualnej, w tym między sieciami wirtualnymi.

## <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Sposób mierzenia czas odpowiedzi dla danych przechowywanych w VMConnection i wyświetlane w panelu połączenia i skoroszyty?

Czas odpowiedzi jest przybliżeniem. Ponieważ kod aplikacji nie jest zinstrumentowanie, nie tak naprawdę wiemy, kiedy rozpoczyna się żądaniem i po nadejściu odpowiedzi. Zamiast tego zaobserwować wysyłanych z połączenia danych, a następnie dane powracające dla tego połączenia. Naszego agenta śledzi te wysyła i odbiera i spróbuje je sparować: odbiera sekwencję wysyła, następuje sekwencja jest interpretowany jako parze żądanie/odpowiedź. Czas między tych operacji to czas odpowiedzi. Będzie ona zawierała opóźnienie sieci i czasu przetwarzania na serwerze.

Zbliżenia działa dobrze w przypadku protokołów, które są na podstawie żądań/odpowiedzi: pojedyncze żądanie trafia połączenia i nadejściu pojedynczą odpowiedź. Dotyczy to HTTP (S) (bez ich przetwarzanie potokowe), ale nie spełnia dla innych protokołów.

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Czy ich ograniczenia, jeśli jestem na Log Analytics bezpłatnego planu cenowego?
Jeśli skonfigurowano usługi Azure Monitor z obszaru roboczego usługi Log Analytics przy użyciu *bezpłatna* warstwy cenowej, usługi Azure Monitor dla funkcji mapy maszyn wirtualnych obsługują tylko pięć maszyn połączonych połączone z obszarem roboczym. W przypadku pięciu maszyn wirtualnych podłączone do obszaru roboczego warstwy bezpłatna rozłączyć jednej z maszyn wirtualnych, a następnie później Połącz z nową maszynę Wirtualną, nowa maszyna wirtualna nie jest monitorowane i widoczne na stronie mapy.  

W tej sytuacji, zostanie wyświetlony monit o **Wypróbuj teraz usługę** opcji otwieranie maszyny Wirtualnej i wybrania **Insights (wersja zapoznawcza)** z okienka po lewej stronie, nawet po jego zainstalowaniu już na maszynie Wirtualnej.  Jednak monit nie jest z opcjami jak zwykle może wystąpić, jeśli ta maszyna wirtualna nie zostały dołączone do usługi Azure Monitor dla maszyn wirtualnych. 

## <a name="next-steps"></a>Kolejne kroki
Przegląd [włączyć usługi Azure Monitor dla maszyn wirtualnych](vminsights-enable-overview.md) , aby zrozumieć wymagania i metody, aby włączyć monitorowanie maszyn wirtualnych.
