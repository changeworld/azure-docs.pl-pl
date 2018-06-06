---
title: Ujednolicone alertów w monitorze Azure | Dokumentacja firmy Microsoft
description: Opis elementu ujednoliconego alertów na platformie Azure, które umożliwiają zarządzanie alerty i alerty reguły w usługach Azure.
author: manishsm-msft
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: mamit,bwren
ms.custom: ''
ms.openlocfilehash: dac8052a96c98130891a9ea31994198e331e314d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726469"
---
# <a name="unified-alerts-in-azure-monitor"></a>Ujednolicone alertów w monitorze Azure

## <a name="overview"></a>Przegląd

> [!NOTE]
>  Nową ujednoliconą funkcjonalność alertów, która umożliwia zarządzanie alerty z wieloma subskrypcjami i wprowadza alert stanów i inteligentnych grup jest dostępna w publicznej wersji zapoznawczej. Zobacz [ostatniej sekcji tego artykułu](#enhanced-unified-alerts-experience-public-preview) opis to rozszerzony środowisko i włączony proces.


W tym artykule opisano ujednoliconym środowisku alertów w monitorze Azure. [Doświadczenia alertu](monitoring-overview-alerts.md) jest dostępna z **alertów (klasyczne)** opcji z menu Azure Monitor. 

## <a name="features-of-the-unified-alert-experience"></a>Funkcje ujednoliconym środowisku alertu

Środowisko unified ma następujące zalety za pośrednictwem klasycznego środowiska:

-   **Lepsze systemu powiadomień**: Unified Użyj alerty [grupy akcji]( https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups), które są nazywane grup powiadomień oraz akcje, które mogą być ponownie używane w wielu alertów. 
- **Ujednolicone i tworzenia** — alertami można zarządzać i reguły alertu dla metryki, dzienniki i działania logowania przez Azure Monitor, analizy dzienników i usługi Application Insights w jednym miejscu. 
- **Widok generowane alerty Log Analytics w portalu Azure** -Wyświetl alerty z analizy dzienników z innych alertów z innych źródeł w portalu Azure. Wcześniej były one w oddzielnych portalu.
- **Rozdzielenie Fired alertów i reguł alertów** -alertów, zasad teraz różnią się od alertów. Reguła alertu jest definicje warunków wyzwalania alertu. Alert jest wystąpieniem uruchamiania reguły alertów.
- **Lepsze przepływu pracy** — ujednoliconą alert tworzenia prowadzi użytkownika przez proces konfigurowania reguły alertu.
 
Metryki alerty mają następujące ulepszenia za pośrednictwem klasycznego alerty metryki:

-   **Ulepszone opóźnienia**: metryki alerty można uruchomić nawet co minutę. Klasycznym metryki alerty są zawsze uruchamiane z częstotliwością wynoszącą 5 minut. Alerty dziennika jeszcze dłużej, niż jest jedna minuta z powodu czasu potrzebnego na pozyskiwania dzienniki opóźnienia. 
-   **Obsługa metryki wielowymiarowej**: można alertów na metryki wymiarów, dzięki czemu można monitorować konkretne wystąpienie metryki.
-   **Większa kontrola nad metryki warunków**: można zdefiniować bardziej rozbudowane reguły alertów, które obsługują monitorowanie maksymalnej, minimalnej, średni i całkowitej wartości metryki.
-   **Połączone monitorowanie wielu metryki**: można monitorować metryki maksymalnie dwóch przy użyciu jednej reguły. Alert zostanie wywołany, jeśli oba wskaźniki naruszenia ich odpowiednich progów w określonym przedziale czasu.
-   **Metryki z dzienników** (ograniczone publicznej wersji zapoznawczej): niektóre dziennika danych, przechodząc do analizy dzienników można teraz można wyodrębnić i przekonwertowane na metryki Azure Monitor i następnie alerty o podobnie jak inne metryki. 


## <a name="alert-rules"></a>Reguły alertów
Środowisko unified alerty używa następujące pojęcia do oddzielania reguły alertów z alertów podczas jednorodnej obsługi tworzenia we wszystkich różnych typów alertów.

| Element | Definicja |
|:---|:---|
| Reguła alertów | Definicja warunku można utworzyć alertu. Składa się z _zasób docelowy_, _sygnału_, _kryteria_, i _logiki_. Reguła alertu jest tylko wtedy, gdy znajduje się on w _włączone_ stanu.
| Zasób docelowy | Definiuje określone zasoby i sygnalizuje dostępne alertów. Element docelowy może być dowolnym zasobem Azure.<br>Przykłady: maszyny wirtualne, konta magazynu, zestaw skali maszyny wirtualnej, obszar roboczy analizy dzienników, zasobu usługi Application Insights |
| Sygnał | Źródło danych emitowanych przez zasób docelowy. Sygnał obsługiwane typy to *Metryka*, *dziennik aktywności*, *usługi Application Insights*, i *dziennika*. |
| Kryteria | Kombinacja _sygnału_ i _logiki_ zastosować do zasobu docelowego.<br>Przykłady: Procent procesora CPU > 70%, czas odpowiedzi serwera > 4 ms, liczba wyników dziennika zapytania > 100 itp. |
| Logika | Zdefiniowane przez użytkownika logikę, sprawdź, czy w ramach sygnał Oczekiwano wartości/zakresu. |
| Akcja | Akcja do wykonania, gdy alert jest uruchamiany. Wiele działań może wystąpić po zgłoszeniu alertu. Te alerty obsługują grup działań.<br>Przykłady: wysyłanie wiadomości e-mail adres e-mail, wywoływania adresu URL elementu webhook. |
| Stan monitora | Wskazuje, czy warunek, którego alert metryki utworzone w wyniku tego został rozwiązany. Metryki reguły alertów przykładowe określonej metryki w regularnych odstępach czasu. Spełnieniu kryteriów w regule alertu nowy alert jest tworzony z warunkiem Fired.  Jeśli metryka jest próbkowany ponownie, gdy nadal jest spełnione kryteria, a następnie nic się nie dzieje.  Jeśli jednak nie są spełnione kryteria, warunek uruchomienia alertu został zmieniony na rozwiązane. Przy następnym uruchomieniu spełnione kryteria, a następnie kolejny alert jest tworzony z warunkiem Fired. |


## <a name="alert-pages"></a>Alert stron
Ujednolicone alerty udostępniają jednego miejsca do wyświetlania i zarządzania wszystkie alerty platformy Azure. W poniższych sekcjach opisano funkcje poszczególnych stron ujednoliconym środowisku.

### <a name="alerts-overview-page"></a>Strony Przegląd alertów
**Alerty** strony Przegląd pokazuje zagregowane podsumowanie wszystkich alertów wypalane i łączną liczbę włączyć reguły alertów. Zmiana subskrypcji lub parametrów filtru aktualizuje agregacji i alerty generowane listy.

 ![Przegląd alertów](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>Zarządzania regułami alertów
**Reguły** jest pojedynczej strony do zarządzania wszystkie reguły alertu subskrypcji platformy Azure. Zawiera listę wszystkich reguł alertów i można sortować na podstawie zasobów docelowych, grupy zasobów, nazwa reguły lub stanu. Reguły alertów można także edytowanych nd włączone lub wyłączone na tej stronie.

 ![reguły alertów](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="creating-an-alert-rule"></a>Tworzenie reguły alertu
Alerty można tworzyć w sposób ciągły, niezależnie od usługi monitorowania lub sygnału typu. Wywoływane wszystkie alerty i powiązanych szczegółów są dostępne w jednej strony.
 
Możesz utworzyć nową regułę alertu następujące trzy kroki:
1. Wybierz _docelowej_ alertu.
1. Wybierz _sygnału_ z dostępnych sygnały dla elementu docelowego.
1. Określ _logiki_ ma zostać zastosowany do danych z sygnału.
 
Uproszczony proces tworzenia już wymaga od użytkownika znajomości monitorowania źródła lub sygnały obsługiwane przed wybraniem zasobów platformy Azure. Lista sygnałów dostępne są automatycznie filtrowani w oparciu o wybrane zasobu docelowego i przeprowadza Cię przez definiowanie logiki reguły alertu.

Dowiedz się więcej na temat tworzenia reguł alertów w [tworzenia, wyświetlania, alerty i zarządzaj nimi za pomocą monitora Azure](monitor-alerts-unified-usage.md).

Alerty są dostępne przez kilka Azure monitorowanie usług. Aby uzyskać informacje o tym, jak i kiedy należy używać każdego z tych usług, zobacz [Azure monitorowania aplikacji i zasobów](./monitoring-overview.md). Poniższa tabela zawiera listę typów reguł alertów, które są dostępne na platformie Azure i co to jest obecnie obsługiwane przez środowisko unified alertu.

| **Źródło monitora** | **Typ sygnału**  | **Opis** | 
|-------------|----------------|-------------|
| Monitor systemu Azure | Metryka  | Skrót [alerty metryki niemal czasie rzeczywistym](monitoring-near-real-time-metric-alerts.md), obsługuje sprawdzanie warunków metryki co minutę często i umożliwić reguły metryki wielu metryki i wielowymiarowych. Lista obsługiwane typy zasobów jest dostępna w [nowszej alerty metryki dla usługi Azure w portalu Azure](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).<br>[Klasycznym alerty metryki](monitoring-overview-alerts.md) nie są obsługiwane w nowym środowisku alerty. Można je znaleźć w obszarze alerty (klasyczne) w portalu Azure. Alerty klasycznego obsługuje niektóre typy metryk, które nie zostały przeniesione do nowszej alertów. Aby uzyskać pełną listę, zobacz [obsługiwane metryki](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics). |
| Log Analytics | Dzienniki  | Otrzymywać powiadomienia, lub Uruchom akcje automatyczne, gdy zapytanie wyszukiwania dziennika spełnia określone kryteria. Alerty w analizy dzienników są [kopiowane do nowego środowiska](monitoring-alerts-extend.md). A [Podgląd *analizy dzienników rejestruje jako metryki* ](monitoring-alerts-extend-tool.md) jest dostępna. Wersja zapoznawcza umożliwia wykonać niektóre typy dzienników i przekonwertować je na metryki, gdy użytkownik może następnie alert po wystąpieniu je za pomocą nowego środowiska alertów. Podgląd jest przydatne w przypadku innych niż Azure dzienniki, które chcesz pobrać obok natywnego metryki Azure Monitor. |
| Dzienniki aktywności | Dziennik aktywności | Zawiera rekordy wszystkich akcji tworzenia, aktualizowania lub usuwania, tworzone przez wybranego celu. |
| Service Health | Dziennik aktywności  | Nie jest obsługiwany w ujednoliconego alerty. Zobacz [tworzyć alerty dziennika działania dotyczące powiadomień usługi](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Application Insights | Dzienniki  | Zawiera dzienniki o szczegółach wydajności aplikacji. Zapytanie analytics można zdefiniować warunki działań podejmowanych na podstawie danych aplikacji. |
| Application Insights | Metryka | Nie jest obsługiwany w ujednoliconego alerty. Zobacz [metryki alerty]. (.. /Application-insights/App-insights-Alerts.MD) |
| Application Insights | Badania dostępności sieci Web | Nie jest obsługiwany w ujednoliconego alerty.  Zobacz [alerty testu sieci Web](../application-insights/app-insights-monitor-web-app-availability.md). Dostępne dla wszystkich witryn sieci Web narzędzia do wysyłania danych do usługi Application Insights. Otrzymasz powiadomienie, gdy dostępność lub czasu odpowiedzi witryny sieci Web jest poniżej oczekiwań. |

## <a name="enhanced-unified-alerts-public-preview"></a>Ulepszone ujednoliconego alerty (publicznej wersji zapoznawczej)

Środowisko rozszerzone alerty ujednoliconego został wydany w publicznej wersji zapoznawczej monitora Azure 1 czerwca 2018. To środowisko opiera się na zalet [unified alerty](#overview) wydane 2018 marca i zapewnia możliwość zarządzania i agregowanie poszczególnych alertów Oprócz modyfikowania stan alertu. W tej sekcji opisano nowe funkcje i jak przechodzić nowych alertów stron w portalu Azure.

### <a name="features-enhanced-unified-alerts"></a>Ulepszone funkcje unified alertów

Nowe środowisko zapewnia następujące funkcje, które nie są dostępne w klasycznym ujednoliconym środowisku:

- **Wyświetlanie alertów w subskrypcjach** — teraz można wyświetlać i zarządzać poszczególne wystąpienia alertów w wielu subskrypcji w ramach jednego widoku.
- **Zarządzanie stanem alertów** -alertów jest już w stanie, który wskazuje, czy jego zostały potwierdzone dla zamknięte.
- **Organizowanie alerty z grupami inteligentne** — inteligentny grup automatycznie zgrupować alerty powiązane z, więc można zarządzać ich jako zestaw zamiast pojedynczo.

### <a name="enable-enhanced-unified-alerts"></a>Włącz rozszerzony ujednoliconego alertów
Włącz nowe środowisko unified alertu, klikając na banerze w górnej części strony alertów. Ten proces tworzy alert magazynu, który obejmuje ostatnich 30 dni wypalane alertów poprzez obsługiwane usługi. Po włączeniu nowego środowiska można przełączać i z powrotem nowym i starym doświadczenia, klikając na banerze.

> [!NOTE]
>  Może upłynąć kilka minut, aż nowe środowisko początkowo włączenia.

![Transparent](media/monitoring-overview-unified-alerts/opt-in-banner.png)

Wszystkie subskrypcje, które mają dostęp do zostanie zarejestrowane po włączeniu nowego środowiska. Mimo że całej subskrypcji jest włączone, tylko użytkownicy, którzy wybrane nowe środowisko będzie można go wyświetlić. Inni użytkownicy z dostępem do subskrypcji musisz włączyć środowisko oddzielnie.

Włączanie nowe środowisko alertu nie wpływa na konfigurację grupy akcji lub powiadomienia w regułach alertów. Tylko zmienia sposób wyświetlania i Zarządzanie wystąpieniami wypalane alertów w portalu Azure.

### <a name="smart-groups"></a>Inteligentne grup
Inteligentne grup redukcji szumu, umożliwiając zarządzanie jako pojedyncza jednostka nie Zarządzanie indywidualnych alertów powiązanych alertów. Możesz wyświetlić szczegóły grupy inteligentne i ustawić stan podobny do alertu. Każdy alert jest członkiem grupy inteligentne jeden i tylko jeden.

Inteligentne grupy są tworzone automatycznie przy użyciu machine learning połączyć powiązanych alertów, które reprezentują jeden problem. Podczas tworzenia alertu algorytm dodaje go do nowej grupy inteligentne lub istniejącej grupy inteligentne na podstawie tych informacji jako wzorce historycznych, podobieństwa właściwości i podobieństwa struktury. Obecnie algorytm uwzględnia alertów z tej samej usługi monitor w ramach subskrypcji. Inteligentne grup można ograniczyć do 99% szumu alertów za pośrednictwem tego konsolidacji. Można wyświetlić przyczyny, że alerty zostały uwzględnione w grupie w strona szczegółów grupy inteligentnych.

Nazwa grupy inteligentne nazywa się jego pierwszy alert. Nie można utworzyć lub zmienić nazwę grupy inteligentne.


### <a name="alert-states"></a>Stany alertu
Rozszerzone alerty ujednoliconego wprowadzenie pojęcia stan alertu. Można ustawić stan alertu, aby określić, w której jest procesu rozpoznawania.  Podczas tworzenia alertu ma stan *nowy*. Można zmienić stan, gdy zostało potwierdzone alert, a po jego zamknięciu go. Wszelkie zmiany stanu są przechowywane w historii alertu.

Obsługiwane są następujące stany alertów.

| Stan | Opis |
|:---|:---|
| Nowa | Problem został właśnie wykryte i jeszcze nie przejrzane. |
| Potwierdzono | Administrator ma sprawdzone alert i zaczął działać na nim. |
| Zamknięte | Problem został rozwiązany. Gdy alert został zamknięty, możesz uruchomić go Moje zmianę do innego stanu. |

Stan alertu jest inny niż warunek monitora. Metryki reguły alertów można ustawić alert warunek _rozpoznać_ gdy warunek błędu nie jest już spełniany. Stan alertu jest ustawiony przez użytkownika i jest niezależna od warunek monitora. Mimo że system może ustawić warunek monitora rozwiązany, stan alertu nie ulega zmianie, do momentu zmiany przez użytkownika.

#### <a name="changing-the-state-of-an-alert-or-smart-group"></a>Zmiana stanu alertu lub inteligentne grupy
Można zmienić stanu alert indywidualny lub zarządzanie wiele alertów jednocześnie przez ustawienie stanu inteligentne grupy.

Zmień stan alertu, klikając **zmienić stan alertu** w widoku szczegółów alertu lub zmienianie stanu inteligentne grupy, klikając **zmiany stanu grupy inteligentne** w widoku szczegółów. Można zmienić stanu wielu elementów w tym samym czasie przez zaznaczenie ich w widoku listy, a następnie klikając polecenie **zmiany stanu** w górnej części strony. W obu przypadkach z listy rozwijanej wybierz opcję Nowy stan i opcjonalnie podaj komentarz. Jeśli chcesz zmienić pojedynczego elementu, następnie masz również opcję, aby zastosować te same zmiany dla wszystkich alertów w grupie inteligentne.

![Zmień stan](media/monitoring-overview-unified-alerts/change-tate.png)

### <a name="alerts-page"></a>Strona alertów
Domyślna strona alertów zawiera podsumowanie alertów, które są tworzone w ramach okna określonym czasie. Łączna liczba alertów dla każdego ważność Wyświetla z kolumnami identyfikowanie łączną liczbę alertów w każdym stanie dla każdego ważności. Kliknij dowolny z wag, aby otworzyć [wszystkie alerty](#all-alerts-page) strony filtrowane według tego ważności.

![Strona alertów](media/monitoring-overview-unified-alerts/alerts-page.png)

Ten widok można filtrować, wybierając wartości list rozwijanych w górnej części strony.

| Kolumna | Opis |
|:---|:---|
| Subskrypcja | Wybierz subskrypcje platformy Azure do 5. Tylko alerty w wybranych subskrypcjach znajdują się w widoku. |
| Grupa zasobów | Wybierz pojedyncza grupa zasobów. Tylko alerty o celach w wybranej grupy zasobów są uwzględnione w widoku. |
| Zakres czasu | Tylko alerty generowane w oknie wybrana wartość czasu zostaną uwzględnione w widoku. Obsługiwane wartości to ostatnia godzina, po 24 godzinach, ostatnie 7 dni i ostatnie 30 dni. |

Kliknij następujące wartości w górnej części strony alerty, aby otworzyć stronę.

| Wartość | Opis |
|:---|:---|
| Łączna liczba alertów | Całkowita liczba alertów, które spełniają wybrane kryteria. Kliknij tę wartość, aby otworzyć widoku wszystkie alerty z bez filtru. |
| Inteligentne grup | Całkowita liczba grup inteligentne utworzone na podstawie alertów, które spełniają wybrane kryteria. Kliknij tę wartość, aby otworzyć listę grup inteligentne w widoku wszystkie alerty.
| Łączna liczba reguł alertów | Całkowita liczba reguł alertów w wybranej grupie subskrypcji i zasobu. Kliknij tę wartość, aby otworzyć widok reguł filtrowane wg wybranych subskrypcji i grupy zasobów.


### <a name="all-alerts-page"></a>Wszystkie strony alertów 
Strona wszystkich alertów umożliwia wyświetlanie listy alertów, które zostały utworzone w oknie wybrana wartość czasu. Można wyświetlić listy poszczególnych alertów lub inteligentne grup zawierających alerty. Kliknij przycisk banerze w górnej części strony, aby przełączyć między widokami.

![Wszystkie strony alertów](media/monitoring-overview-unified-alerts/all-alerts-page.png)

Można filtrować widok, wybierając następujące wartości w list rozwijanych w górnej części strony.

| Kolumna | Opis |
|:---|:---|
| Subskrypcja | Wybierz subskrypcje platformy Azure do 5. Tylko alerty w wybranych subskrypcjach znajdują się w widoku. |
| Grupa zasobów | Wybierz pojedyncza grupa zasobów. Tylko alerty o celach w wybranej grupy zasobów są uwzględnione w widoku. |
| Typ zasobu | Wybierz co najmniej jeden typ zasobu. Tylko alerty o celach wybranego typu znajdują się w widoku. W tej kolumnie jest dostępna tylko w przypadku, gdy określono grupę zasobów. |
| Zasób | Wybierz zasób. Tylko alerty o tym zasoby jako element docelowy znajdują się w widoku. W tej kolumnie jest dostępna tylko w przypadku, gdy określono typ zasobu. |
| Ważność | Wybierz alert o ważności lub *wszystkie* uwzględnienie alerty wszystkie poziomy ważności. |
| Stan monitora | Wybierz warunek monitora lub *wszystkie* uwzględnienie alerty warunków. |
| Stan alertu | Wybierz alert, stanu lub wybierz opcję *wszystkie* uwzględnienie alerty stanów. |
| Monitorowanie usługi | Wybierz usługę lub wybierz *wszystkie* uwzględnienie wszystkich usług. Uwzględniane są tylko alertów tworzonych przez zasady przy użyciu usługi jako miejsce docelowe. |
| Zakres czasu | Tylko alerty generowane w oknie wybrana wartość czasu zostaną uwzględnione w widoku. Obsługiwane wartości to ostatnia godzina, po 24 godzinach, ostatnie 7 dni i ostatnie 30 dni. |

Kliknij przycisk **kolumny** w górnej części strony, aby wybrać kolumn do wyświetlenia. Możesz usunąć wszystkie kolumny, z wyjątkiem 

### <a name="alert-detail-page"></a>Strona szczegółów alertu
Kliknięcie alertu, zostanie wyświetlona strona szczegółów alertu. Zawiera szczegóły dotyczące alertu i umożliwia zmianę stanu.

![Szczegóły alertu](media/monitoring-overview-unified-alerts/alert-detail.png)

Strona szczegółów alertu zawiera następujące sekcje.

| Sekcja | Opis |
|:---|:---|
| Podstawy | Wyświetla właściwości oraz inne istotne informacje o alercie. |
| Historia | Wyświetla listę każdej akcji podjętej przez alert i wszelkie zmiany wprowadzone do alertu. To jest obecnie ograniczone do zmiany stanu. |
| Grupy inteligentnych | Zawiera informacje o grupie inteligentne alertu. **Liczbę alertów** odwołuje się do liczby alertów w grupie inteligentne. Dotyczy to również inne alerty, które znajdują się w tej samej grupie inteligentne tego samego utworzonych w ciągu ostatnich 30 dni.  Jest to niezależnie od tego, filtr czasu na stronie listy alertów. Kliknij alert, aby wyświetlić jego szczegóły. |
| Więcej informacji | Wyświetla dalsze informacje kontekstowe alertu, który jest zwykle specyficzny dla typu źródła utworzony alert. |


### <a name="smart-group-detail-page"></a>Strona szczegółów grupy inteligentnych
Po kliknięciu inteligentne grupy, zostanie wyświetlona strona szczegółów grupy inteligentne. Zawiera szczegóły inteligentne grupy, w tym rozsądkiem użyty do utworzenia grupy i umożliwia zmianę stanu.
 
![Szczegóły grupy inteligentnych](media/monitoring-overview-unified-alerts/smart-group-detail.png)


Strona szczegółów grupy inteligentne zawiera następujące sekcje.

| Sekcja | Opis |
|:---|:---|
| Alerty | Zawiera listę poszczególnych alertów, które znajdują się w grupie inteligentne. Kliknij alert, aby otworzyć jego stronę szczegółów alertu. |
| Historia | Wyświetla listę każdej akcji podjętej przez grupę inteligentne i wszelkie zmiany wprowadzone do niego. To jest obecnie ograniczone do zmian stanu i zmiany członkostwa alertu. |

## <a name="next-steps"></a>Kolejne kroki
- [Informacje o sposobie użycia nowego środowiska alertów do tworzenia, wyświetlania i Zarządzanie alertami](monitor-alerts-unified-usage.md)
- [Dowiedz się więcej o alertach dziennika w środowisku alertów](monitor-alerts-unified-log.md)
- [Dowiedz się więcej o alertach metryki środowisko alertów](monitoring-near-real-time-metric-alerts.md)
- [Dowiedz się więcej o alertach dziennika aktywności w środowisku alertów](monitoring-activity-log-alerts-new-experience.md)
