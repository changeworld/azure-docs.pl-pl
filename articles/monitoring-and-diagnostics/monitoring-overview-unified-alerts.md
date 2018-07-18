---
title: Ujednolicone alertów w usłudze Azure Monitor
description: Opis ujednoliconego alertów na platformie Azure, które umożliwiają zarządzanie alerty i alerty reguły w usługach platformy Azure.
author: manishsm-msft
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: mamit
ms.component: alerts
ms.openlocfilehash: c4c8279a1d4638a1c5d889b53e2d9e89e458cc37
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39117616"
---
# <a name="unified-alerts-in-azure-monitor"></a>Ujednolicone alertów w usłudze Azure Monitor

## <a name="overview"></a>Przegląd

> [!NOTE]
>  Nowym ujednoliconym środowisku alertu, który pozwala na zarządzanie alerty z wieloma subskrypcjami i wprowadza alert stanów i inteligentne grup jest obecnie dostępna w publicznej wersji zapoznawczej. Zobacz ostatnią sekcję w tym artykule, aby uzyskać opis udoskonalone środowisko i proces Włączanie go.


W tym artykule opisano ujednoliconego środowiska alertów w usłudze Azure Monitor. [Poprzednie środowisko alertów](monitoring-overview-alerts.md) jest dostępne z **alerty (klasyczne)** opcji w menu usługi Azure Monitor. 

## <a name="features-of-the-unified-alert-experience"></a>Funkcje ujednoliconego środowiska alertu

Ujednolicone środowisko ma następujące korzyści za pośrednictwem klasycznego środowiska:

-   **Lepsze systemu powiadomień**: [grup akcji](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) są nazywane grupami powiadomienia i akcje, które mogą być ponownie używane w wielu alertów. 
- **Ujednolicone środowisko tworzenia**: alerty i reguły alertów metryk, dzienników i dzienników aktywności usługi Azure Monitor, usługi Log Analytics i usługi Application Insights można zarządzać w jednym miejscu. 
- **Wyświetl wyzwolone alerty usługi Log Analytics w witrynie Azure portal**: teraz można wyświetlać alerty usługi Log Analytics przy użyciu alertów z innych źródeł, w witrynie Azure portal. Wcześniej alertów z innych źródeł były związane z osobnego portalu.
- **Oddzielanie wyzwolone alerty i reguł alertów**: reguły alertów teraz różnią się od alertów. Reguła alertu jest definicja warunek, który wyzwala alert. Alert jest wystąpieniem uruchomieniu którego reguły alertu.
- **Lepszy przepływ pracy**: ten alert ujednolicone środowisko tworzenia przeprowadzi Cię przez proces konfigurowania reguły alertu.
 
Alerty metryki mają następujące ulepszenia za pośrednictwem klasycznego alertów dotyczących metryk:

-   **Ulepszone opóźnienie**: alertów dotyczących metryk można uruchomić tak często, jak raz na minutę. Klasyczne alertów dotyczących metryk są zawsze uruchamiane z częstotliwością co 5 minut. Alerty dzienników jeszcze opóźnienie dłużej niż minutę ze względu na czas potrzebny do odbierania dzienników. 
-   **Obsługa metryk wielowymiarowych**: może generować alerty na metryki jednowymiarowe oznacza, że można monitorować określone wystąpienie metryki.
-   **Większa kontrola nad metryki warunki**: można zdefiniować bardziej rozbudowane reguł alertów, które obsługują monitorowanie maksymalna, minimalna, średnia i łączna liczba wartości metryk.
-   **Monitorowanie wielu metryk w połączeniu**: można monitorować metryki do dwóch przy użyciu jednej reguły. Alert jest wyzwalany, jeśli oba wskaźniki naruszenia ich odpowiednich progów w określonym przedziale czasu.
-   **Metryki z dzienników** (ograniczonej publicznej wersji zapoznawczej): niektóre dziennika danych, które są przesyłane do usługi Log Analytics mogą teraz można wyodrębnić i przekształcone w metryki usługi Azure Monitor i następnie alerty o podobnie jak inne metryki. 


## <a name="alert-rules"></a>Reguły alertów
Środowisko alertów ujednoliconego wykorzystuje następujące pojęcia do oddzielnych reguł alertów z alertów podczas jednorodnej obsługi tworzenia we wszystkich różnych typów alertów.

| Element | Definicja |
|:---|:---|
| Reguła alertów | Definicja warunku w celu utworzenia alertu. Reguła alertu składa się z _zasób docelowy_, _sygnału_, _kryteria_, i _logiki_. Reguła alertu jest tylko wtedy, gdy jest on _włączone_ stanu.
| Zasób docelowy | Definiuje określonych zasobów i sygnałów, które są dostępne dla alertów. Obiekt docelowy może być dowolnym zasobem platformy Azure.<br><br>Przykłady: maszyna wirtualna, konto magazynu, zestaw skalowania maszyn wirtualnych, obszar roboczy usługi Log Analytics, zasób usługi Application Insights |
| Sygnał | Źródło danych emitowanych przez zasób docelowy. Sygnał obsługiwane typy to *metryki*, *dziennika aktywności*, *usługi Application Insights*, i *dziennika*. |
| Kryteria | Kombinacja _sygnału_ i _logiki_ zastosowany do zasobu docelowego.<br><br>Przykłady: Procent procesora CPU > 70%, czas odpowiedzi serwera > 4 ms, liczba wyników dziennika zapytań > 100 i tak dalej |
| Logic Apps | Zdefiniowane przez użytkownika logiki, aby sprawdzić, czy sygnał mieści się w oczekiwany zakres/wartości. |
| Akcja | Akcja do wykonania po wyzwoleniu alertu. Wiele operacji może wystąpić, gdy zostanie wyzwolony alert. Te alerty obsługuje grupy akcji.<br><br>Przykłady: wysyłanie wiadomości e-mail na adres e-mail, wywoływanie adres URL elementu webhook |
| Warunek monitora | Wskazuje, czy warunek, który utworzono alert metryki został rozwiązany. Reguły alertów metryk przykładowe określonej metryki w regularnych odstępach czasu. Jeśli są spełnione kryteria reguły alertu, nowy alert jest tworzony z warunkiem "uruchamiane".  Gdy Metryka próbkowania ponownie, gdy nadal są spełnione kryteria następnie nic się nie dzieje.  Jeśli nie są spełnione kryteria, następnie warunek uruchomienia alertu zostanie zmieniony na "rozwiązany". Przy następnym uruchomieniu kryteria zostaną spełnione, kolejny alert jest tworzony z warunkiem "uruchamiane". |


## <a name="alert-pages"></a>Strony alertu
Ujednolicone alerty zapewniają jednego miejsca można wyświetlać i zarządzać wszystkimi alertami platformy Azure. W poniższych sekcjach opisano funkcje poszczególnych stron z ujednoliconym środowisku.

### <a name="alerts-overview-page"></a>Strony Przegląd alertów
**Alerty** strona przeglądu zawiera zagregowane podsumowanie wszystkich wyzwolone alerty i łączną liczbę Włącz reguły alertów. Zmiana subskrypcji lub parametrów filtru aktualizuje wartości zagregowanych i alerty wyzwalane listy.

 ![Przegląd alertów](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>Zarządzanie regułami alertów
**Reguły** to pojedyncza strona zarządzania wszystkich reguł alertów wszystkich subskrypcji platformy Azure. On zawiera listę wszystkich reguł alertów i może być sortowane w oparciu o zasoby docelowe, grupy zasobów, nazwę reguły lub stanu. Reguły alertów można również można edytować, włączone lub wyłączone na tej stronie.

 ![reguły alertów](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Tworzenie reguły alertu
Alerty można tworzyć w spójny sposób niezależnie od tego, usługi monitorowania lub sygnał typu. Wszystkie wyzwolone alerty i powiązanych szczegółów znajduje się w jednej strony.
 
Utworzysz nową regułę alertu z trzech kroków:
1. Wybierz _docelowej_ alertu.
1. Wybierz _sygnału_ z dostępne sygnały dla elementu docelowego.
1. Określ _logiki_ mają być stosowane do danych z sygnałów.
 
To uproszczony proces tworzenia pakietów administracyjnych nie wymaga już znajomości źródło monitorowania lub sygnałów, które są obsługiwane przed wybraniem zasobu platformy Azure. Lista sygnałów dostępnych jest automatycznie filtrowany według zasobu docelowego, który można wybrać i prowadzi użytkownika przez definiowanie logiki reguły alertu.

Dowiedz się więcej na temat sposobu tworzenia reguły alertów w [Utwórz, Wyświetl, alerty i zarządzaj nimi przy użyciu usługi Azure Monitor](monitor-alerts-unified-usage.md).

Alerty są dostępne przez kilka usługi monitorowania platformy Azure. Aby uzyskać informacje o tym, jak i kiedy należy używać każdego z tych usług, zobacz [monitorowania platformy Azure, aplikacje i zasoby](./monitoring-overview.md). Poniższa tabela zawiera listę typów reguł alertów, które są dostępne na platformie Azure. Zawiera również listę co to jest aktualnie obsługiwana w ujednoliconym środowisku alertu.

| **Źródło monitora** | **Typ sygnału**  | **Opis** | 
|-------------|----------------|-------------|
| Azure Monitor | Metryka  | Nazywane również [alertów dotyczących metryk niemal w czasie rzeczywistym](monitoring-near-real-time-metric-alerts.md), obsługi, obliczania warunków metryki tak często, jak raz na minutę i umożliwiają wielu metryk i wielowymiarowych reguł metryk. Lista obsługiwane typy zasobów jest dostępna w [nowszych alertów metryk dla usług platformy Azure w witrynie Azure portal](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).<br>[Klasyczne alertów dotyczących metryk](monitoring-overview-alerts.md) nie są obsługiwane w nowe środowisko alertów. Można je znaleźć w obszarze alerty (klasyczne) w witrynie Azure portal. Alertów klasycznych obsługuje niektóre typy metryki, które nie zostały przeniesione do nowszych alertów. Aby uzyskać pełną listę, zobacz [metryki obsługiwane](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics). |
| Log Analytics | Dzienniki  | Otrzymuj powiadomienia lub Uruchom automatyczne akcje, gdy zapytanie wyszukiwania w dzienniku spełnia określone kryteria. Alerty w usłudze Log Analytics są [są kopiowane do nowego środowiska](monitoring-alerts-extend.md). A [omówienie *dzienników usługi Log Analytics, jako metryki* ](monitoring-alerts-extend-tool.md) jest dostępna. (Wersja zapoznawcza) umożliwia podjęcia pewnych typów dzienników i konwertować je na metryk, w którym następnie alerty na nich za pomocą nowego środowiska alertów. Podgląd jest przydatne w przypadku dzienników spoza platformy Azure, które chcesz pobrać wraz z natywnych metryk usługi Azure Monitor. |
| Dzienniki aktywności | Dziennik aktywności | Zawiera wszystkie rekordy tworzenie, aktualizowanie i usuwanie działań, które zostały utworzone przez wybranego obiektu docelowego. |
| Kondycja usługi | Dziennik aktywności  | Nieobsługiwane w ujednoliconego alertów. Zobacz [Tworzenie alertów dziennika aktywności dla powiadomień dotyczących usług](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Application Insights | Dzienniki  | Zawiera dzienników za pomocą szczegółowe informacje dotyczące wydajności aplikacji. Za pomocą zapytania usługi analytics, można określić warunki akcje do wykonania na podstawie danych aplikacji. |
| Application Insights | Metryka | Nieobsługiwane w ujednoliconego alertów. Zobacz [alertów dotyczących metryk](../application-insights/app-insights-alerts.md). |
| Application Insights | Testy dostępności sieci Web | Nieobsługiwane w ujednoliconego alertów.  Zobacz [alerty testu internetowego](../application-insights/app-insights-monitor-web-app-availability.md). Dostępne w dowolnej witrynie sieci Web, który został zinstrumentowany na wysyłanie danych do usługi Application Insights. Otrzymuj powiadomienie, gdy dostępności i czasu odpowiedzi witryny sieci Web znajduje się poniżej oczekiwania. |

## <a name="enhanced-unified-alerts-public-preview"></a>Ulepszone alerty ujednoliconego (publiczna wersja zapoznawcza)

Środowisko rozszerzonego, ujednoliconego alertów została wydana w publicznej wersji zapoznawczej dla usługi Azure Monitor od 1 czerwca 2018 r. To środowisko jest oparta na korzyści wynikające z [unified alerty](#overview), które zostały wydane w marcu 2018 r. i który umożliwia zarządzanie i agregowanie poszczególnych alertów i modyfikowania stan alertu. W tej sekcji opisano nowe funkcje i korzystanie z nowych stron alertów w witrynie Azure portal.

### <a name="enhanced-unified-alerts"></a>Ulepszone alerty ujednolicone

Nowe środowisko obejmuje następujące funkcje, które nie są dostępne w klasycznym ujednoliconego środowiska:

- **Wyświetlanie alertów w subskrypcjach**: można teraz wyświetlać i zarządzać poszczególne wystąpienia alerty wielu subskrypcji w ramach jednego widoku.
- **Zarządzanie stanem alertów**: alerty mają teraz stanu, która wskazuje, czy zostały one została potwierdzona jako zamknięty.
- **Organizowanie alertów przy użyciu inteligentnych grup**: grup inteligentne automatycznie pogrupować powiązane alerty, aby zarządzać nimi jako zestawem, nie indywidualnie.

### <a name="enable-enhanced-unified-alerts"></a>Włącz rozszerzony ujednoliconego alertów
Włącz nowe środowisko alertów ujednolicone, wybierając na banerze w górnej części na stronie alertów. Ten proces tworzy magazyn alertu, obejmującą z ostatnich 30 dni wyzwolone alerty obsługiwanych usługach. Po włączeniu nowego środowiska pracy, można przełączać się i z powrotem między nowym i starym doświadczenia, wybierając ten transparent.

> [!NOTE]
>  Może upłynąć kilka minut, aż nowe środowisko początkowo włączenia.

![Transparent](media/monitoring-overview-unified-alerts/opt-in-banner.png)

Wszystkie subskrypcje, które mają dostęp do zarejestrowanych, po włączeniu nowego środowiska. Chociaż cała subskrypcja jest włączona, można wyświetlić tylko użytkownicy, którzy wybierz nowe środowisko. Inni użytkownicy z dostępem do subskrypcji należy włączyć to środowisko oddzielnie.

Włączanie nowego środowiska alertów nie ma wpływu na konfigurację grupy akcji lub powiadomienia w reguł alertu. Zmienia tylko sposób wyświetlania i zarządzania jego wystąpieniami wyzwoleń alertów w witrynie Azure portal.

### <a name="smart-groups"></a>Inteligentne grup
Inteligentne grup szumu, umożliwiając zarządzanie powiązanych alertów jako pojedynczą jednostkę, a nie poszczególnych alertów. Możesz wyświetlić szczegóły grupy inteligentne i Ustaw stan podobnie jak można się z alertami. Każdy alert jest elementem członkowskim grupy jeden i tylko jeden inteligentne.

Inteligentne grupy są tworzone automatycznie za pomocą uczenia maszynowego, aby połączyć powiązanych alertów, które reprezentują pojedynczego problemu. Po utworzeniu alertu algorytm dodaje go do nowej grupy inteligentne lub istniejącej grupy inteligentne w oparciu o informacje, takie jak historyczne wzorce, podobne właściwości i podobną strukturę. 

Obecnie algorytm uwzględnia alertów z tej samej usługi monitor w ramach subskrypcji. Inteligentne grup może zmniejszyć do 99% liczby niepotrzebnych alertów za pośrednictwem dzięki konsolidacji. Można wyświetlić przyczynę, że alerty zostały uwzględnione w grupie w strona szczegółów grupy inteligentne.

Nazwa grupy inteligentne nazywa się jej pierwszy alert. Nie można utworzyć lub zmienić nazwę grupy inteligentne.


### <a name="alert-states"></a>Stany alertów
Ulepszone alerty ujednoliconego wprowadzono koncepcję stan alertu. Można ustawić stan alertu, aby określić, gdzie są dostępne w procesie rozpoznawania. Gdy alert jest tworzony, ma ona stan *New*. Stan można zmienić, gdy użytkownik potwierdza alertu i po zamknięciu. Wszystkie zmiany stanu są przechowywane w historii alertu.

Obsługiwane są następujące stany alertów.

| Stan | Opis |
|:---|:---|
| Nowa | Problem został wykryty i jeszcze nie została sprawdzona. |
| Potwierdzono | Administrator przejrzał alertu i do pracy w niej. |
| Zamknięte | Problem został rozwiązany. Po zamknięciu alertu możesz otworzyć go ponownie, zmieniając go do innego stanu. |

Stan alertu jest inny niż warunek monitora. Reguły alertów metryk można ustawić warunek alertu _rozwiązane_ kiedy warunek błędu nie jest już spełniany. Stan alertu jest ustawiony przez użytkownika i jest niezależna od warunek monitora. Mimo że system można ustawić warunek monitora na "rozwiązany", stan alertu nie jest zmieniany, do momentu zmiany przez użytkownika.

#### <a name="change-the-state-of-an-alert-or-smart-group"></a>Zmienianie stanu alertu lub inteligentne grupy
Można zmienić stanu alert indywidualny lub zarządzanie wiele alertów jednocześnie przez ustawienie stanu grupy inteligentne.

Zmień stan alertu, wybierając **Zmień stan alertu** w widoku szczegółów alertu. Lub zmieniają stan grupy inteligentne przez zaznaczenie **zmiany stanu grupy inteligentne** w widoku szczegółów. Zmień stan z wielu elementów w tym samym czasie najpierw wybierając je w widoku listy, a następnie wybierając **zmiany stanu** w górnej części strony. 

W obu przypadkach wybierz nowy stan z menu rozwijanego. Następnie wprowadź opcjonalny komentarz. Jeśli zmieniasz pojedynczy element masz również opcję, aby zastosować te same zmiany dla wszystkich alertów w grupie inteligentne.

![Zmień stan](media/monitoring-overview-unified-alerts/change-tate.png)

### <a name="alerts-page"></a>Strona alertów
Na stronie alertów domyślny zawiera podsumowanie alertów, które są tworzone w danym przedziale czasu. Wyświetla łączna liczba alertów w przypadku każdej wagi z kolumnami, które identyfikują łączną liczbę alertów w każdym stanie w przypadku każdej wagi. Wybierz dowolne ważności, aby otworzyć [wszystkie alerty](#all-alerts-page) strona jest filtrowana według tego ważności.

![Strona alertów](media/monitoring-overview-unified-alerts/alerts-page.png)

Ten widok można filtrować, wybierając wartości w menu rozwijanych w górnej części strony.

| Kolumna | Opis |
|:---|:---|
| Subskrypcja | Wybierz maksymalnie pięć subskrypcji platformy Azure. Tylko w wybranych subskrypcjach są uwzględniane alerty w widoku. |
| Grupa zasobów | Wybierz pojedynczą grupę zasobów. Tylko alerty z usługą obiektów docelowych w wybranej grupie zasobów znajdują się w widoku. |
| Przedział czasu | Tylko alerty wyzwalane w przedziale czasu wybranego znajdują się w widoku. Obsługiwane wartości to ostatniej godziny, ostatnich 24 godzin, w ciągu ostatnich 7 dni i ostatnich 30 dni. |

Wybierz następujące wartości w górnej części strony alerty, aby otworzyć innej strony.

| Wartość | Opis |
|:---|:---|
| Łączna liczba alertów | Całkowita liczba alertów, które spełniają wybrane kryteria. Wybierz tę wartość, aby otworzyć widok wszystkich alertów z żadnego filtru. |
| Inteligentne grup | Całkowita liczba grup inteligentne, które zostały utworzone przy użyciu alertów, które spełniają wybrane kryteria. Wybierz tę wartość, aby otworzyć listę grup inteligentne w widoku wszystkie alerty.
| Łączna liczba reguł alertów | Całkowita liczba reguł alertów w wybranej subskrypcji i grupie zasobów. Wybierz tę wartość, aby otworzyć widok reguł filtrowane według wybranej subskrypcji i grupie zasobów.


### <a name="all-alerts-page"></a>Wszystkie strony alerty 
Na stronie wszystkie alerty, można wyświetlić listy alertów, które zostały utworzone w przedziale czasu wybranego. Możesz wyświetlić listę poszczególnych alertów lub listę grup inteligentne, które zawierają alertów. Wybierz Baner w górnej części strony Aby przełączać się między widokami.

![Wszystkie strony alerty](media/monitoring-overview-unified-alerts/all-alerts-page.png)

Widok można filtrować, wybierając następujące wartości w menu rozwijanych w górnej części strony.

| Kolumna | Opis |
|:---|:---|
| Subskrypcja | Wybierz maksymalnie pięć subskrypcji platformy Azure. Tylko w wybranych subskrypcjach są uwzględniane alerty w widoku. |
| Grupa zasobów | Wybierz pojedynczą grupę zasobów. Tylko alerty z usługą obiektów docelowych w wybranej grupie zasobów znajdują się w widoku. |
| Typ zasobu | Wybierz jeden lub więcej typów zasobów. Tylko alerty o celach wybranego typu znajdują się w widoku. W tej kolumnie jest dostępna tylko po określono grupę zasobów. |
| Zasób | Wybierz zasób. Tylko alerty o zasobu jako obiekt docelowy znajdują się w widoku. W tej kolumnie jest dostępna tylko po został określony jako typ zasobu. |
| Ważność | Wybierz alert o ważności lub *wszystkich* obejmujący alerty wszystkie poziomy ważności. |
| Warunek monitora | Wybierz warunek monitora lub *wszystkich* obejmujący alerty warunków. |
| Stan alertu | Wybierz stan alertu lub *wszystkich* obejmujący alerty stanów. |
| Monitorowanie usługi | Wybierz usługę, lub wybierz *wszystkich* obejmujący wszystkie usługi. Uwzględniane są tylko alerty tworzone przez reguły, które korzystają z usługi jako element docelowy. |
| Przedział czasu | Tylko alerty wyzwalane w przedziale czasu wybranego znajdują się w widoku. Obsługiwane wartości to ostatniej godziny, ostatnich 24 godzin, w ciągu ostatnich 7 dni i ostatnich 30 dni. |

Wybierz **kolumn** w górnej części strony Aby wybrać kolumny do wyświetlenia. 

### <a name="alert-detail-page"></a>Strona szczegółów alertu
Po wybraniu alertu, zostanie wyświetlona strona szczegółów alertu. Ona zawiera szczegóły dotyczące alertu i umożliwia zmianę stanu.

![Szczegóły alertu](media/monitoring-overview-unified-alerts/alert-detail.png)

Strona szczegółów alertu zawiera następujące sekcje.

| Sekcja | Opis |
|:---|:---|
| Podstawy | Wyświetla właściwości i inne istotne informacje dotyczące alertu. |
| Historia | Wyświetla listę każdej akcji podjętej przez alert i wszelkie zmiany wprowadzone do alertu. To jest obecnie ograniczona do zmiany stanu. |
| Inteligentne grupy | Informacje na temat inteligentnych grupy alertu znajduje się w. *Liczba alertów* odnosi się do liczby alertów, które znajdują się w grupie inteligentne. Obejmuje to innych alertów w tej samej grupie inteligentne, które zostały utworzone w ciągu ostatnich 30 dni.  Jest to niezależnie od tego, filtr czasu na stronie listy alertów. Wybierz alert, aby wyświetlić jego szczegóły. |
| Więcej informacji | Umożliwia wyświetlanie dodatkowych informacji kontekstowych dla alertu, który jest zazwyczaj specyficzny dla typu źródła, która utworzyła alert. |


### <a name="smart-group-detail-page"></a>Strona szczegółów grupy inteligentne
Po wybraniu grupy inteligentne, zostanie wyświetlona strona szczegółów grupy inteligentne. Zapewnia szczegółowe informacje o inteligentne grupy, w tym przyczyny, dla których został użyty do utworzenia grupy, która umożliwia zmianę stanu.
 
![Szczegóły grupy inteligentne](media/monitoring-overview-unified-alerts/smart-group-detail.png)


Strona szczegółów grupy inteligentnych zawiera następujące sekcje.

| Sekcja | Opis |
|:---|:---|
| Alerty | Zawiera listę poszczególnych alertów, które znajdują się w grupie inteligentne. Wybierz alert, aby otworzyć jego stronę szczegółów alertu. |
| Historia | Wyświetla listę każdej akcji podjętej przez inteligentny grupy i wszelkie zmiany, które zostały wprowadzone. To jest obecnie ograniczona do zmiany stanu i zmiany członkostwa alertu. |

## <a name="next-steps"></a>Kolejne kroki
- [Dowiedz się, jak tworzyć, wyświetlać, alerty i zarządzaj nimi za pomocą nowego środowiska alertów](monitor-alerts-unified-usage.md)
- [Więcej informacji na temat alertów dziennika w środowisko alertów](monitor-alerts-unified-log.md)
- [Więcej informacji na temat alertów dotyczących metryk w środowisko alertów](monitoring-near-real-time-metric-alerts.md)
- [Więcej informacji na temat alertów dziennika aktywności w środowisko alertów](monitoring-activity-log-alerts-new-experience.md)
