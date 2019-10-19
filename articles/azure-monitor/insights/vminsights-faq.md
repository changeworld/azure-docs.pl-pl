---
title: Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) — często zadawane pytania | Microsoft Docs
description: Azure Monitor dla maszyn wirtualnych to rozwiązanie na platformie Azure, które łączy kondycję i monitorowanie wydajności systemu operacyjnego Azure VM, a także automatycznie odnajduje składniki aplikacji i zależności z innymi zasobami oraz mapuje komunikację między niego. Ten artykuł zawiera odpowiedzi na często zadawane pytania.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/09/2018
ms.openlocfilehash: 675277a33a5613507297f1c77ee9cef3215b22a2
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555213"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) — często zadawane pytania
Ta firma Microsoft — często zadawane pytania dotyczące Azure Monitor dla maszyn wirtualnych. Jeśli masz dodatkowe pytania dotyczące rozwiązania, przejdź do [forum dyskusyjnego](https://feedback.azure.com/forums/34192--general-feedback) i Opublikuj swoje pytania. Gdy pytanie jest często zadawane, dodamy je do tego artykułu, aby można je było szybko i łatwo znaleźć.

## <a name="can-i-onboard-to-an-existing-workspace"></a>Czy mogę dołączyć do istniejącego obszaru roboczego?
Jeśli Twoje maszyny wirtualne są już połączone z obszarem roboczym Log Analytics, można nadal korzystać z tego obszaru roboczego podczas dołączania do Azure Monitor dla maszyn wirtualnych, pod warunkiem, że znajduje się on w jednym z obsługiwanych regionów wymienionych w [tym miejscu](vminsights-enable-overview.md#prerequisites).

Podczas dołączania konfigurujemy liczniki wydajności dla obszaru roboczego, które spowodują, że wszystkie maszyny wirtualne będą zgłaszać dane do obszaru roboczego, aby rozpocząć zbieranie tych informacji do wyświetlania i analizy w Azure Monitor dla maszyn wirtualnych.  W efekcie zobaczysz dane wydajności ze wszystkich maszyn wirtualnych połączonych z wybranym obszarem roboczym.  Funkcje kondycji i mapy są włączone tylko dla maszyn wirtualnych, które zostały określone do dołączenia.

Aby uzyskać więcej informacji o włączonych licznikach wydajności, zapoznaj się z artykułem [Omówienie włączania](vminsights-enable-overview.md#performance-counters-enabled) .

## <a name="can-i-onboard-to-a-new-workspace"></a>Czy mogę dołączyć do nowego obszaru roboczego? 
Jeśli maszyny wirtualne nie są obecnie połączone z istniejącym obszarem roboczym Log Analytics, musisz utworzyć nowy obszar roboczy do przechowywania danych. Tworzenie nowego domyślnego obszaru roboczego jest wykonywane automatycznie, jeśli skonfigurowano jedną maszynę wirtualną platformy Azure dla Azure Monitor dla maszyn wirtualnych za pomocą Azure Portal.

Jeśli zdecydujesz się na użycie metody opartej na skrypcie, te kroki zostaną omówione w artykule [włączanie Azure monitor dla maszyn wirtualnych (wersja zapoznawcza) przy użyciu szablonu Azure PowerShell lub Menedżer zasobów](vminsights-enable-at-scale-powershell.md) . 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Co zrobić, jeśli moja maszyna wirtualna jest już raportowana w istniejącym obszarze roboczym?
Jeśli dane są już zbierane z maszyn wirtualnych, być może zostały już skonfigurowane do raportowania danych do istniejącego obszaru roboczego Log Analytics.  O ile ten obszar roboczy znajduje się w jednym z obsługiwanych regionów, możesz włączyć Azure Monitor dla maszyn wirtualnych do tego istniejącego obszaru roboczego.  Jeśli obszar roboczy, którego już używasz, nie znajduje się w jednym z obsługiwanych regionów, nie będziesz w stanie teraz dołączyć do Azure Monitor dla maszyn wirtualnych.  Aktywnie pracujemy nad obsługą dodatkowych regionów.

>[!NOTE]
>Konfigurujemy liczniki wydajności dla obszaru roboczego, które mają wpływ na wszystkie maszyny wirtualne, które są zgłaszane do obszaru roboczego, niezależnie od tego, czy wybrano, aby dołączyć je do Azure Monitor dla maszyn wirtualnych. Aby uzyskać więcej informacji na temat konfiguracji liczników wydajności dla obszaru roboczego, zapoznaj się z naszą [dokumentacją](../../azure-monitor/platform/data-sources-performance-counters.md). Informacje o licznikach skonfigurowanych dla Azure Monitor dla maszyn wirtualnych można znaleźć w artykule dotyczącym [włączania Azure monitor dla maszyn wirtualnych](vminsights-enable-overview.md#performance-counters-enabled) .  

## <a name="why-did-my-vm-fail-to-onboard"></a>Dlaczego moja maszyna wirtualna nie mogła dołączyć?
Podczas dołączania maszyny wirtualnej platformy Azure z Azure Portal należy wykonać następujące czynności:

* Zostanie utworzony domyślny obszar roboczy Log Analytics, jeśli ta opcja została wybrana.
* Liczniki wydajności zostały skonfigurowane dla wybranego obszaru roboczego. Jeśli ten krok zakończy się niepowodzeniem, Zauważ, że niektóre wykresy i tabele wydajności nie wyświetlają danych dla dodanej maszyny wirtualnej. Aby rozwiązać ten problem, można uruchomić skrypt programu PowerShell opisany [tutaj](vminsights-enable-at-scale-powershell.md#enable-performance-counters).
* Agent Log Analytics jest instalowany na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia maszyny wirtualnej, o ile jest to wymagane.  
* Agent Azure Monitor dla maszyn wirtualnych zależności mapy jest instalowany na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia, o ile jest to wymagane.  
* W razie potrzeby konfigurowane są składniki Azure Monitor obsługujące kondycję, a maszyna wirtualna jest skonfigurowana do raportowania danych o kondycji.

Podczas procesu dołączania sprawdzimy stan każdego z powyższych, aby zwrócić do Ciebie status powiadomienia w portalu. Konfiguracja obszaru roboczego i Instalacja agenta zazwyczaj trwa od 5 do 10 minut. Wyświetlanie danych monitorowania i kondycji w portalu zajmuje więcej niż 5 – 10 minut.  

W przypadku zainicjowania dołączania i wyświetlania komunikatów wskazujących, że maszyna wirtualna musi zostać dołączona, możesz ukończyć proces przez maksymalnie 30 minut. 

## <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Tylko włączone Azure Monitor dla maszyn wirtualnych, Dlaczego widzę wszystkie moje maszyny wirtualne monitorowane przez funkcję kondycji?
Funkcja kondycji jest włączona dla wszystkich maszyn wirtualnych, które są połączone z obszarem roboczym Log Analytics, nawet gdy akcja zostanie zainicjowana dla jednej maszyny wirtualnej.

## <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Czy mogę zmodyfikować harmonogram, dla którego kryteria kondycji szacują warunek?
Nie, nie można zmodyfikować okresu i częstotliwości kryteriów kondycji w tej wersji. 

## <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>Czy można wyłączyć kryteria kondycji dla warunku, który nie musi być monitorowany?
W tej wersji nie można wyłączyć kryteriów kondycji.

## <a name="are-the-health-alert-severities-configurable"></a>Czy skonfigurowano alerty kondycji?  
Nie można zmodyfikować ważności alertu kondycji. można go włączyć lub wyłączyć. Ponadto niektóre serwery alertów są aktualizowane na podstawie stanu kryteriów kondycji. 

## <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Jeśli ponownie skonfigurujemy ustawienia określonych kryteriów kondycji, można je ograniczyć do określonego wystąpienia?  
W przypadku zmodyfikowania dowolnego ustawienia wystąpienia kryterium kondycji są modyfikowane wszystkie wystąpienia kryteriów kondycji tego samego typu na maszynie wirtualnej platformy Azure. Na przykład jeśli zostanie zmodyfikowany próg wystąpienia kryterium kondycji wolnego miejsca na dysku odpowiadającego dyskowi logicznemu C:, ten próg dotyczy wszystkich innych dysków logicznych, które są odnajdywane i monitorowane dla tej samej maszyny wirtualnej.

## <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>Czy funkcja kondycji monitoruje procesory logiczne i rdzenie?
Nie, w przypadku systemu Windows nie są uwzględniane kryteria kondycji poszczególnych procesorów i logicznych poziomów procesora, a domyślnie tylko całkowite użycie procesora CPU jest monitorowane w celu efektywnego oszacowania użycia procesora na podstawie łącznej liczby procesorów logicznych dostępnych dla maszyny wirtualnej platformy Azure. 

## <a name="are-all-health-criteria-thresholds-configurable"></a>Czy wszystkie progi kryteriów kondycji są konfigurowalne?  
Progi dla kryteriów kondycji przeznaczonych dla maszyny wirtualnej z systemem Windows nie można modyfikować, ponieważ ich Stany kondycji są ustawione na *Uruchamianie* lub *dostępne*. Po zbadaniu kondycji w [interfejsie API monitora obciążenia](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)jest wyświetlana wartość *operatorporównania* **LessThan** lub **GreaterThan** z wartością *progową* **4** dla usługi lub jednostki, jeśli:
   - Service Health klienta DNS — usługa nie jest uruchomiona. 
   - Kondycja usługi klienta DHCP — usługa nie jest uruchomiona. 
   - RPC Service Health — usługa nie jest uruchomiona. 
   - Kondycja usługi zapory systemu Windows — usługa nie jest uruchomiona.
   - Kondycja usługi dziennika zdarzeń systemu Windows — usługa nie jest uruchomiona. 
   - Kondycja usługi serwera — usługa nie jest uruchomiona. 
   - Kondycja usługi zdalnego zarządzania systemem Windows — usługa nie jest uruchomiona. 
   - Błąd lub uszkodzenie systemu plików — dysk logiczny jest niedostępny.

Progi dla następujących kryteriów kondycji systemu Linux nie można modyfikować, ponieważ ich stan kondycji jest już ustawiony na *wartość true*. Stan kondycji przedstawia *operatorporównania* z wartością **LessThan** i wartością *progową* **1** w przypadku zapytania z interfejsu API monitorowania obciążenia dla jednostki, w zależności od jego kontekstu:
   - Stan dysku logicznego — dysk logiczny nie jest w trybie online/jest dostępny
   - Stan dysku — dysk nie jest dostępny w trybie online
   - Stan karty sieciowej — karta sieciowa jest wyłączona

## <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Jak mogę modyfikować alerty dołączone do funkcji kondycji?
Reguły alertów, które są zdefiniowane dla każdego kryterium kondycji, nie są wyświetlane w Azure Portal. Regułę alertu kondycji można włączyć lub wyłączyć tylko w [interfejsie API monitora obciążenia](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Ponadto nie można przypisać [Azure monitor grupy akcji](../../azure-monitor/platform/action-groups.md) dla alertów dotyczących kondycji w Azure Portal. Za pomocą interfejsu API ustawienia powiadomienia można skonfigurować grupę akcji, która ma być wyzwalana za każdym razem, gdy zostanie wyzwolony alert kondycji. Obecnie można przypisać grupy akcji do maszyny wirtualnej, aby wszystkie *alerty kondycji* zostały wyzwolone dla maszyny wirtualnej wyzwalają te same grupy akcji. W przeciwieństwie do tradycyjnych alertów platformy Azure nie ma koncepcji oddzielnej grupy akcji dla każdej reguły alertu kondycji. Ponadto podczas wyzwalania alertów kondycji są obsługiwane tylko grupy akcji, które są skonfigurowane pod kątem dostarczania wiadomości e-mail lub powiadomień SMS. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Nie widzę niektórych lub żadnych danych na wykresach wydajności dla mojej maszyny wirtualnej
Jeśli nie widzisz danych dotyczących wydajności w tabeli dysku lub na niektórych wykresach wydajności, liczniki wydajności mogą nie być skonfigurowane w obszarze roboczym. Aby rozwiązać ten problem, uruchom następujący [skrypt programu PowerShell](vminsights-enable-at-scale-powershell.md#enable-with-powershell).

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Jak funkcja mapowania Azure Monitor dla maszyn wirtualnych różni się od Service Map?
Funkcja mapy Azure Monitor dla maszyn wirtualnych jest oparta na Service Map, ale ma następujące różnice:

* Dostęp do widoku mapy można uzyskać z bloku VM i z Azure Monitor dla maszyn wirtualnych w obszarze Azure Monitor.
* Połączenia na mapie są teraz klikane i wyświetlają widok danych metryk połączenia w panelu bocznym dla wybranego połączenia.
* Istnieje nowy interfejs API, który jest używany do tworzenia map, aby lepiej obsługiwać bardziej złożone mapy.
* Monitorowane maszyny wirtualne są teraz uwzględnione w węźle Grupa klientów, a wykres pierścieniowy przedstawia proporcje monitorowanych i niemonitorowanych maszyn wirtualnych w grupie.  Można go również użyć do filtrowania listy maszyn, gdy grupa jest rozwinięta.
* Monitorowane maszyny wirtualne znajdują się teraz w węzłach grupy portów serwera, a wykres pierścieniowy przedstawia część monitorowanych i niemonitorowanych maszyn w grupie.  Można go również użyć do filtrowania listy maszyn, gdy grupa jest rozwinięta.
* Styl mapy został zaktualizowany tak, aby był bardziej spójny z mapą aplikacji z usługi Application Insights.
* Panele boczne zostały zaktualizowane i nie mają pełnego zestawu integracji, które były obsługiwane w Service Map-Update Management, Change Tracking, zabezpieczeniach i usług. 
* Opcja wyboru grup i maszyn do mapowania została zaktualizowana i obecnie obsługuje subskrypcje, grupy zasobów, zestawy skalowania maszyn wirtualnych platformy Azure i usługi w chmurze.
* W funkcji mapy Azure Monitor dla maszyn wirtualnych nie można tworzyć nowych grup maszyn Service Map.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Dlaczego wykresy wydajności są wyświetlane z kropkowanymi liniami?
Może się to zdarzyć z kilku powodów.  W przypadkach, gdy istnieje przerwy w zbieraniu danych, przedstawiamy linie jako kropkowane.  Jeśli zmodyfikowano częstotliwość próbkowania danych dla włączonych liczników wydajności (ustawienie domyślne to zbiera dane co 60 sekund), można zobaczyć kropkowane linie na wykresie, jeśli wybierzesz wąski zakres czasu dla wykresu, a częstotliwość próbkowania jest mniejsza niż rozmiar zasobnika używany na wykresie (na przykład częstotliwość próbkowania jest co 10 minut, a każdy zasobnik na wykresie to 5 minut).  Wybranie szerszego zakresu czasu do wyświetlenia powinno spowodować, że linie wykresu będą widoczne jako linie kryjące zamiast kropek w tym przypadku.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Czy grupy są obsługiwane w Azure Monitor dla maszyn wirtualnych?
Tak, po zainstalowaniu agenta zależności Zbieramy informacje z maszyn wirtualnych w celu wyświetlenia grup na podstawie subskrypcji, grupy zasobów, zestawów skalowania maszyn wirtualnych i usług Cloud Services.  Jeśli używasz Service Map i utworzono grupy maszyn, są one również wyświetlane.  Grupy komputerów będą również wyświetlane w filtr grup, jeśli zostały utworzone dla obszaru roboczego, który jest wyświetlany. 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Jak mogę wyświetlić szczegółowe informacje o tym, co jest związane z wierszem percentylu używany 95. na zagregowanych wykresach wydajności?
Domyślnie lista jest sortowana w celu wyświetlenia maszyn wirtualnych o najwyższej wartości używany 95. percentylu dla wybranej metryki, z wyjątkiem dostępnego wykresu pamięci, który pokazuje komputery z najniższą wartością piątego percentylu.  Kliknięcie wykresu spowoduje otwarcie widoku **listy pierwszych N** z wybraną odpowiednią metryką.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Jak funkcja mapy obsługuje zduplikowane adresy IP w różnych sieci wirtualnychach i podsieciach?
W przypadku duplikowania zakresów adresów IP z maszynami wirtualnymi lub zestawami skalowania maszyn wirtualnych platformy Azure w różnych podsieciach i sieci wirtualnych może dojść do wyświetlania nieprawidłowych informacji na mapie Azure Monitor dla maszyn wirtualnych. Jest to znany problem i badamy opcje w celu ulepszenia tego środowiska.

## <a name="does-map-feature-support-ipv6"></a>Czy funkcja map obsługuje protokół IPv6?
Funkcja map obsługuje obecnie tylko protokół IPv4 i badamy obsługę protokołu IPv6. Obsługujemy również protokół IPv4, który jest tunelowany w protokole IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Gdy ładujemy mapę dla grupy zasobów lub innej dużej grupy, mapowanie jest trudne do wyświetlenia
Chociaż wprowadzono ulepszenia do mapowania w celu obsługi dużych i złożonych konfiguracji, wiemy, że mapa może mieć wiele węzłów, połączeń i węzłów działających jako klaster.  Firma Microsoft zobowiązuje się do kontynuowania ulepszania wsparcia, aby zwiększyć skalowalność.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Dlaczego wykres sieciowy na karcie wydajność wygląda inaczej niż wykres sieciowy na stronie Przegląd maszyny wirtualnej platformy Azure?

Na stronie Przegląd dla maszyny wirtualnej platformy Azure są wyświetlane wykresy oparte na pomiarach aktywności hosta na maszynie wirtualnej gościa.  W przypadku wykresu sieciowego na maszynie wirtualnej platformy Azure przegląd jest wyświetlany tylko ruch sieciowy, który zostanie rozliczony.  Nie obejmuje to ruchu między sieciami wirtualnymi.  Dane i wykresy wyświetlane dla Azure Monitor dla maszyn wirtualnych są oparte na danych z maszyny wirtualnej gościa, a wykres sieciowy przedstawia cały ruch TCP/IP, który jest przychodzący i wychodzący do tej maszyny wirtualnej, w tym między sieciami wirtualnymi.

## <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Jak jest mierzony czas odpowiedzi dla danych przechowywanych w VMConnection i wyświetlany w panelu połączenia i w skoroszytach?

Czas odpowiedzi to przybliżenie. Ponieważ nie ma Instrumentacji kodu aplikacji, firma Microsoft nie wie, kiedy rozpocznie się żądanie i kiedy nadejdzie odpowiedź. Zamiast tego zauważymy, że dane są wysyłane w ramach połączenia, a następnie dane zostaną przywrócone z tego połączenia. Nasz Agent śledzi te wysyłanie i odbieranie i próbuje je sparować: sekwencja wysyłania, a następnie sekwencja odbierania jest interpretowana jako para żądania/odpowiedzi. Czas odpowiedzi między tymi operacjami to godzina reakcji. Obejmuje to opóźnienie sieci i czas przetwarzania serwera.

To zbliżenie sprawdza się w przypadku protokołów, na których oparto żądania/odpowiedzi: jedno żądanie jest wykonywane w ramach połączenia i odbierane są pojedyncze odpowiedzi. Dotyczy to w przypadku protokołu HTTP (bez przetwarzania potokowego), ale nie jest spełniony dla innych protokołów.

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Czy istnieją ograniczenia dotyczące bezpłatnego planu cenowego Log Analytics?
Jeśli skonfigurowano Azure Monitor z obszarem roboczym Log Analytics za pomocą warstwy cenowej *bezpłatna* , funkcja mapy Azure monitor dla maszyn wirtualnych będzie obsługiwać tylko pięć połączonych maszyn połączonych z obszarem roboczym. Jeśli masz pięć maszyn wirtualnych połączonych z bezpłatnym obszarem roboczym, odłączysz jedną z maszyn wirtualnych, a następnie nawiążesz połączenie nowej maszyny wirtualnej, Nowa maszyna wirtualna nie będzie monitorowana i odzwierciedlona na stronie Mapa.  

W tym stanie zostanie wyświetlony monit z opcją **Wypróbuj teraz** po otwarciu maszyny wirtualnej i wybraniu **szczegółowych informacji (wersja zapoznawcza)** w okienku po lewej stronie, nawet po zainstalowaniu go już na maszynie wirtualnej.  Nie jest jednak wyświetlany monit z opcjami, które zwykle wystąpią, jeśli ta maszyna wirtualna nie została dołączona do Azure Monitor dla maszyn wirtualnych. 

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z tematem [włączanie Azure monitor dla maszyn wirtualnych](vminsights-enable-overview.md) , aby zrozumieć wymagania i metody umożliwiające monitorowanie maszyn wirtualnych.
