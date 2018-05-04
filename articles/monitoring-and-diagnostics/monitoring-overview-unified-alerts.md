---
title: Poznaj nowe środowisko alertów w monitorze Azure | Dokumentacja firmy Microsoft
description: Zrozumienie, jak nowe alerty proste i skalowalne doświadczenie w Azure ułatwia tworzenia, przeglądania i Zarządzanie alertami łatwiejsze
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
ms.date: 03/23/2018
ms.author: mamit
ms.custom: ''
ms.openlocfilehash: c3622b4699ef532f204231c76aa3436be3676763
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="the-new-alerts-experience-in-azure-monitor"></a>Nowe alerty występują w monitorze Azure

## <a name="overview"></a>Przegląd

> [!NOTE]
> W tym artykule opisano nowszej alerty. Starsze klasycznego alerty monitora Azure opisano w [Przegląd alertów classic](monitoring-overview-alerts.md). 
>
>

Alerty ma nowego środowiska. Alerty starsze obsługi jest teraz na karcie alerty (klasyczny). Nowe środowisko alertów ma następujące zalety nad środowiskiem alerty (klasyczne):

-   **Lepsze systemu powiadomień**: Użyj wszystkie alerty nowszej [grupy akcji]( https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups), które są nazywane grup powiadomień oraz akcje, które mogą być ponownie używane w wielu alertów.  Klasycznym alerty metryki i starszych alerty analizy dzienników nie należy używać grup akcji. 
- **A unified środowisko tworzenia** — wszystkich alertów tworzenie miar, dzienników i aktywności logowania przez Monitor Azure Log Analytics i usługi Application Insights znajduje się w jednym miejscu. 
- **Widok generowane alerty Log Analytics w portalu Azure** — można teraz również Zobacz generowane alerty analizy dzienników w ramach subskrypcji. Wcześniej były one w oddzielnych portalu. 
- **Rozdzielenie Fired alertów i reguły alertu** — Alert reguły (definicja warunku wyzwalania alertu) i generowane alerty (wystąpienia uruchamiania reguły alertu) są zróżnicowane, więc widoków operacyjnych i konfiguracji są rozdzielone.
- **Lepsze przepływu pracy** - nowe alerty tworzenia przewodniki środowisko użytkownika wzdłuż przez proces konfigurowania reguły alertu, dzięki czemu łatwiej odnajdywać prawo czynności, aby otrzymywać alerty w.
 
Nowsze metryki alerty w szczególności mają następujące udoskonalenia:
-   **Ulepszone opóźnienia**: alerty metryki nowszej można uruchomić nawet co minutę. Starsze metryki alerty są zawsze uruchamiane z częstotliwością wynoszącą 5 minut. Alerty dziennika jeszcze dłużej niż minutę opóźnienia z powodu czasu jest potrzebnych do pozyskiwania dzienniki. 
-   **Obsługa metryki wielowymiarowej**: można alertów na metryki wymiarów, dzięki czemu można monitorować segmentu interesujące metryki.
-   **Większa kontrola nad metryki warunków**: można zdefiniować bardziej rozbudowane reguły alertów. Alerty nowszej obsługuje monitorowanie maksymalnej, minimalnej, średni i całkowitej wartości metryki.
-   **Połączone monitorowanie wielu metryki**: można monitorować wiele metryk (obecnie do dwóch metryk) przy użyciu jednej reguły. Alert zostanie wywołany, jeśli oba wskaźniki naruszenia ich odpowiednich progów w określonym przedziale czasu.
-   **Metryki z dzienników** (ograniczone publicznej wersji zapoznawczej): niektóre dziennika danych, przechodząc do analizy dzienników można teraz można wyodrębnić i przekonwertowane na metryki Azure Monitor i następnie alerty o podobnie jak inne metryki. 



W poniższych sekcjach opisano, bardziej szczegółowo, jak działa nowego środowiska.

## <a name="alert-rules-terminology"></a>Terminologia reguły alertów
Nowe środowisko alerty używa następujące pojęcia do oddzielania obiektów reguły alertów i Alert wywoływane podczas jednorodnej obsługi tworzenia we wszystkich różnych typów alertów.

- **Zasób docelowy** — element docelowy może być dowolnym zasobem Azure. Zasób docelowy definiuje zakres i sygnałów, które są dostępne dla alertów. Przykładowe elementy docelowe: maszyny wirtualne, konta magazynu, zestaw skali maszyny wirtualnej, obszaru roboczego analizy dzienników lub zasobu usługi Application Insights.

- **Kryteria** — kryteria jest kombinacją sygnału i logiki na zasób docelowy. Przykłady: Procent procesora CPU > 70%, czas odpowiedzi serwera > 4 ms, liczba wyników dziennika zapytania > 100 itp. 

- **Sygnał** — sygnały są emitowane przez zasób docelowy i mogą być różne. **Metryka**, **dziennik aktywności**, **usługi Application Insights**, i **dziennika** są obsługiwane typy sygnału.

- **Logika** -zdefiniowane przez użytkownika logikę, sprawdź, czy w ramach sygnał Oczekiwano wartości/zakresu.  
 
- **Akcja** — konkretną akcję wykonywaną, gdy alert jest uruchamiany. Na przykład wysyłanie wiadomości e-mail adres e-mail lub wywoływania adresu URL elementu webhook. Wiele działań może wystąpić po zgłoszeniu alertu. Te alerty obsługują grup działań.  
 
- **Reguła alertu** -warunek, który może powodować Wyzwalanie alertów. Reguła alertu przechwytuje docelowy i kryteria alertów. Reguła alertu może być stan wyłączone lub włączone.
 
    > [!NOTE]
    > Różni się to od środowisko alerty (klasyczne), gdy alert reprezentuje reguły i wypalane alert i dlatego może być w jednym ze stanów ostrzeżenie, aktywna lub wyłączona.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Jednego miejsca, aby wyświetlić alerty i zarządzaj nimi
Celem środowisko alertów jest za jednego miejsca do wyświetlania i zarządzania wszystkie alerty platformy Azure. Poniższe podpunkty opisują funkcje każdego pojedynczego ekranu nowego środowiska.

### <a name="alerts-overview-page"></a>Strony Przegląd alertów
**Monitor — alerty** strony Przegląd pokazuje zagregowane podsumowanie wszystkich wypalane alertów i sumy skonfigurowane lub nie włączono reguł alertów. Zawiera on również lista wszystkich alertów wypalane. Zmiana subskrypcji lub parametrów filtru aktualizuje agregacji i alerty generowane listy.

> [!NOTE]
> Wypalane alertów przedstawianych w alerty są ograniczone do obsługiwanych metryki i alerty dziennika aktywności; Liczba alertów wypalane, włącznie z zawartymi w starszych alerty Azure zawiera omówienie narzędzia Monitor systemu Azure

 ![Przegląd alertów](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>Zarządzania regułami alertów
**Monitor — Alerty > reguły** jest pojedynczej strony do zarządzania wszystkie reguły alertu subskrypcji platformy Azure. Wyświetla listę wszystkich reguł alertów (włączona lub wyłączona) i można sortować na podstawie zasobów docelowych, grupy zasobów, nazwa reguły lub stanu. Reguły alertów można również włączony/wyłączony lub edytować z poziomu tej strony.  

 ![reguły alertów](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Jeden alert tworzenia we wszystkich źródłach monitorowania
W nowym środowisku alerty alertów można tworzyć w sposób ciągły, niezależnie od usługi monitorowania lub sygnału typu. Wszystkie alerty generowane i powiązanych szczegółów są dostępne na jednej stronie.  
 
Tworzenia alertu jest trzech etapów zadania, gdy użytkownik najpierw wybiera element docelowy dla alertu, następuje zaznaczenie prawym sygnału, a następnie określając logiki mają być stosowane na sygnał jako część reguły alertów. Uproszczony proces tworzenia już wymaga od użytkownika znajomości monitorowania źródła lub sygnały obsługiwane przed wybraniem zasobów platformy Azure. Wspólne środowisko tworzenia automatycznie filtruje listę dostępnych sygnały oparte na wybrany zasób docelowy i przeprowadza tworzenia logiki alertu

Dowiedz się więcej na temat tworzenia następujące typy alertów [tutaj](monitor-alerts-unified-usage.md).
- Alerty metryki
- Alerty dzienników (Log Analytics)
- Alerty dziennika (Dzienniki aktywności)
- Alerty dziennika (usługi Application Insights)

 
## <a name="alerts-supported-in-new-experience"></a>Alerty w nowym środowisku obsługiwane
Alerty są dostępne przez kilka Azure monitorowanie usług. Aby uzyskać informacje o tym, jak i kiedy należy używać tych usług [znajduje się w artykule](./monitoring-overview.md). W tym miejscu jest podział typów alertów dostępna na platformie Azure i co to jest obecnie obsługiwane przez nowe środowisko alerty. 


| **Typ sygnału** | **Źródło monitora** | **Opis** | 
|-------------|----------------|-------------|
| Metryka | Monitor systemu Azure | Skrót [alerty metryki niemal czasie rzeczywistym](monitoring-near-real-time-metric-alerts.md), obsługuje sprawdzanie warunków metryki co minutę często i umożliwić reguły metryki wielu metryki i wielowymiarowych. Dostępna jest lista obsługiwane typy zasobów [tutaj](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported). |
| Metryka | Monitor systemu Azure | [Starsze klasycznego alerty metryki](monitoring-overview-alerts.md) nie są obsługiwane w nowym środowisku alerty. Można je znaleźć w obszarze alerty (klasyczne) w portalu Azure. Alerty klasycznego obsługuje niektóre typy metryk, które nie zostały przeniesione do nowszej alertów. Aby uzyskać pełną listę, zobacz [obsługiwane metryk](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics)
| Dzienniki  | Log Analytics | Otrzymywać powiadomienia, lub Uruchom akcje automatyczne, gdy dziennik zapytania wyszukiwania danych metryki i/lub zdarzeń spełnia określone kryteria. Starsze alerty analizy dzienników są nadal dostępne, ale są [kopiowane do nowego środowiska](monitoring-alerts-extend.md). Ponadto [Podgląd *analizy dzienników rejestruje jako metryki* ](monitoring-alerts-extend-tool.md) jest dostępna. Wersja zapoznawcza umożliwia wykonać niektóre typy dzienników i przekonwertować je na metryki, gdy użytkownik może następnie alert po wystąpieniu je za pomocą nowego środowiska alertów. Podgląd jest przydatne w przypadku innych niż Azure dzienniki, które chcesz pobrać obok natywnego metryki Azure Monitor. |
| Dziennik aktywności | Dzienniki aktywności (Ogólne) | Zawiera rekordy wszystkie akcje tworzenia, aktualizowania lub usuwania wykonywane za pomocą wybranego celu (Grupa zasobów/zasobów/subskrypcji). |
| Dziennik aktywności  | Service Health | Nie jest obsługiwany w nowym środowisku alerty. Zobacz [tworzyć alerty dziennika działania dotyczące powiadomień usługi](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Dzienniki  | Application Insights | Zawiera dzienniki o szczegółach wydajności aplikacji. Zapytanie analytics można zdefiniować warunki dla działań, które mają być wykonane — na podstawie danych aplikacji. |
| Metryka | Application Insights | Nie jest obsługiwany w nowym środowisku alerty. Zobacz [metryki alertów](../application-insights/app-insights-alerts.md) |
| Badania dostępności sieci Web | Application Insights | Nie są obsługiwane w środowisku alerty.  Zobacz [alerty testu sieci Web](../application-insights/app-insights-monitor-web-app-availability.md). Dostępne dla wszystkich witryn sieci Web narzędzia do wysyłania danych do usługi Application Insights. Otrzymasz powiadomienie, gdy dostępność lub czasu odpowiedzi witryny sieci Web jest poniżej oczekiwań. |




## <a name="next-steps"></a>Kolejne kroki
- [Informacje o sposobie użycia nowego środowiska alertów do tworzenia, wyświetlania i Zarządzanie alertami](monitor-alerts-unified-usage.md)
- [Dowiedz się więcej o alertach dziennika w środowisku alertów](monitor-alerts-unified-log.md)
- [Dowiedz się więcej o alertach metryki środowisko alertów](monitoring-near-real-time-metric-alerts.md)
- [Dowiedz się więcej o alertach dziennika aktywności w środowisku alertów](monitoring-activity-log-alerts-new-experience.md)
