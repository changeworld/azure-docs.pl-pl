---
title: Wizualizacja danych z usługi Azure Monitor | Dokumenty firmy Microsoft
description: Zawiera podsumowanie dostępnych metod wizualizacji danych metryk i dziennika przechowywanych w usłudze Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 877616f6fd31bdfbe193bd8f03efb3f79317ad42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535373"
---
# <a name="visualizing-data-from-azure-monitor"></a>Wizualizacja danych z usługi Azure Monitor
Ten artykuł zawiera podsumowanie dostępnych metod wizualizacji danych dziennika i metryk przechowywanych w usłudze Azure Monitor.

Wizualizacje, takie jak wykresy i wykresy, mogą pomóc w analizowaniu danych monitorowania w celu przechodzenia do szczegółów problemów i identyfikowania wzorców. W zależności od używanego narzędzia można również udostępnić wizualizacje innym użytkownikom w organizacji i poza nią.

## <a name="workbooks"></a>Skoroszyty
[Skoroszyty](../azure-monitor/app/usage-workbooks.md) to interaktywne dokumenty, które zapewniają szczegółowe informacje na temat danych, badania i współpracy wewnątrz zespołu. Konkretne przykłady, w których skoroszyty są przydatne, to przewodniki rozwiązywania problemów i pośmiertne zdarzenia.

![skoroszyt](media/visualizations/workbook.png)

### <a name="advantages"></a>Zalety
- Obsługuje zarówno metryki, jak i dzienniki.
- Obsługuje parametry włączające interaktywne raporty, w których wybranie elementu w tabeli spowoduje dynamiczną aktualizację skojarzonych wykresów i wizualizacji.
- Przepływ podobny do dokumentu.
- Opcja dla skoroszytów osobistych lub udostępnionych.
- Łatwe, przyjazne dla współpracy środowisko autorskie.
- Szablony obsługują publiczną galerię szablonów opartych na usłudze GitHub.

### <a name="limitations"></a>Ograniczenia
- Brak automatycznego odświeżania.
- Brak gęstego układu, takiego jak pulpity nawigacyjne, które sprawiają, że skoroszyty są mniej przydatne jako pojedyncze okienko szkła. Przeznaczone bardziej do dostarczania głębszych spostrzeżeń.


## <a name="azure-dashboards"></a>Pulpity nawigacyjne platformy Azure
[Pulpity nawigacyjne platformy Azure](../azure-portal/azure-portal-dashboards.md) są podstawową technologią pulpitu nawigacyjnego platformy Azure. Są one szczególnie przydatne w dostarczaniu pojedynczego okienka szkła za pomocą infrastruktury i usług platformy Azure, co pozwala szybko zidentyfikować ważne problemy.

![Pulpit nawigacyjny](media/visualizations/dashboard.png)

### <a name="advantages"></a>Zalety
- Głęboka integracja z platformą Azure. Wizualizacje można przypiąć do pulpitów nawigacyjnych z wielu stron platformy Azure, w tym Eksploratora metryk, analizy dzienników i usługi Application Insights.
- Obsługuje zarówno metryki, jak i dzienniki.
- Łączenie danych z wielu źródeł, w tym dane wyjściowe z [eksploratora metryk,](platform/metrics-charts.md) [kwerendy dziennika](log-query/log-query-overview.md)oraz [mapy](app/app-map.md) i dostępność w usłudze Application Insights.
- Opcja dla osobistych lub udostępnionych pulpitów nawigacyjnych. Zintegrowane z [uwierzytelnianiem opartym na rolach platformy Azure (RBAC).](../role-based-access-control/overview.md)
- Automatyczne odświeżanie. Odświeżanie metryk zależy od zakresu czasu z co najmniej pięcioma minutami. Logi odświeżają się co godzinę, z ręczną opcją odświeżania na żądanie, klikając ikonę "odśwież" na danej wizualizacji lub odświeżając pełny pulpit nawigacyjny.
- Parametryzowane metryki pulpity nawigacyjne z sygnaturą czasową i parametrami niestandardowymi.
- Elastyczne opcje układu.
- Tryb pełnoekranowy.


### <a name="limitations"></a>Ograniczenia
- Ograniczona kontrola nad wizualizacjami dzienników bez obsługi tabel danych. Całkowita liczba serii danych jest ograniczona do 10, a kolejne serie danych są zgrupowane w _innym_ zasobniku.
- Brak obsługi parametrów niestandardowych dla wykresów dziennika.
- Wykresy dziennika są ograniczone do ostatnich 30 dni.
- Wykresy dziennika można przypiąć tylko do udostępnionych pulpitów nawigacyjnych.
- Brak interaktywności z danymi pulpitu nawigacyjnego.
- Ograniczona kontekstowa szczegółowa drążenie.


## <a name="power-bi"></a>Power BI
[Usługa Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) jest szczególnie przydatna do tworzenia pulpitów nawigacyjnych i raportów zorientowanych na biznes, a także raportów analizujących długoterminowe trendy kluczowych wskaźników wydajności. Wyniki [kwerendy dziennika można zaimportować](platform/powerbi.md) do zestawu danych usługi Power BI, aby korzystać z jego funkcji, takich jak łączenie danych z różnych źródeł i udostępnianie raportów w sieci Web i na urządzeniach przenośnych.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Zalety
- Rozbudowa wizualizacji.
- Rozbudowana interaktywność, w tym powiększanie i filtrowanie krzyżowe.
- Łatwe udostępnianie w całej organizacji.
- Integracja z innymi danymi z wielu źródeł danych.
- Lepsza wydajność z wynikami buforowane w module.


### <a name="limitations"></a>Ograniczenia
- Obsługuje dzienniki, ale nie metryki.
- Brak integracji z platformą Azure. Nie można zarządzać pulpitami nawigacyjnymi i modelami za pośrednictwem usługi Azure Resource Manager.
- Aby skonfigurować, do modelu usługi Power BI należy zaimportować wyniki kwerend. Ograniczenie rozmiaru i odświeżania wyniku.
- Ograniczone odświeżanie danych osiem razy dziennie.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) to otwarta platforma, która wyróżnia się na operacyjnych pulpitach nawigacyjnych. Jest to szczególnie przydatne do wykrywania i izolowania i triaging incydentów operacyjnych. Wtyczkę [źródła danych Grafana Azure Monitor](platform/grafana-plugin.md) można dodać do subskrypcji platformy Azure, aby wizualizować dane metryk platformy Azure.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>Zalety
- Rozbudowa wizualizacji.
- Bogaty ekosystem źródeł danych.
- Interaktywność danych, w tym powiększanie.
- Obsługuje parametry.

### <a name="limitations"></a>Ograniczenia
- Brak integracji z platformą Azure. Nie można zarządzać pulpitami nawigacyjnymi i modelami za pośrednictwem usługi Azure Resource Manager.
- Koszt obsługi dodatkowej infrastruktury Grafana lub dodatkowe koszty dla Grafana Cloud.


## <a name="build-your-own-custom-application"></a>Tworzenie własnej aplikacji niestandardowej
Dostęp do danych w dziennikach i danych metryk w usłudze Azure Monitor można uzyskać za pośrednictwem interfejsu API przy użyciu dowolnego klienta REST, który umożliwia tworzenie własnych niestandardowych witryn sieci Web i aplikacji.

### <a name="advantages"></a>Zalety
- Pełna elastyczność interfejsu użytkownika, wizualizacji, interaktywności i funkcji.
- Połącz metryki i dane dziennika z innymi źródłami danych.

### <a name="disadvantages"></a>Wady
- Wymagany jest znaczny wysiłek inżynieryjny.


## <a name="azure-monitor-views"></a>Widoki monitora platformy Azure

> [!IMPORTANT]
> Widoki są w trakcie przestarzałe. Zobacz [przewodnik przejścia widoku usługi Azure Monitor do skoroszytów, aby](platform/view-designer-conversion-overview.md) uzyskać wskazówki dotyczące konwertowania widoków na skoroszyty.

[Widoki w usłudze Azure Monitor](platform/view-designer.md) umożliwiają tworzenie wizualizacji niestandardowych z danymi dziennika. Są one używane przez [rozwiązania monitorowania](insights/solutions.md) do prezentowania danych, które zbierają.


![Widok](media/visualizations/view.png)

### <a name="advantages"></a>Zalety
- Rozbudowa wizualizacji danych dziennika.
- Eksportuj i importuj widoki, aby przenieść je do innych grup zasobów i subskrypcji.
- Integruje się z modelem zarządzania usługi Azure Monitor z obszarami roboczymi i rozwiązaniami do monitorowania.
- [Filtry](platform/view-designer-filters.md) dla parametrów niestandardowych.
- Interaktywna, obsługuje wielopoziomowe wcierne wejście (widok, który przechodzi do innego widoku)

### <a name="limitations"></a>Ograniczenia
- Obsługuje dzienniki, ale nie metryki.
- Brak osobistych poglądów. Dostępne dla wszystkich użytkowników z dostępem do obszaru roboczego.
- Brak automatycznego odświeżania.
- Ograniczone opcje układu.
- Brak obsługi wykonywania zapytań w wielu obszarach roboczych lub aplikacjach usługi Application Insights.
- Zapytania są ograniczone w rozmiarze odpowiedzi do 8 MB i czas wykonywania kwerendy 110 sekund.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [danych zebranych przez usługę Azure Monitor](platform/data-platform.md).
- Dowiedz się więcej o [pulpitach nawigacyjnych platformy Azure](../azure-portal/azure-portal-dashboards.md).
- Dowiedz się więcej o [widokach w usłudze Azure Monitor](platform/view-designer.md).
- Dowiedz się więcej o [skoroszytach](../azure-monitor/app/usage-workbooks.md).
- Dowiedz się więcej o [importowaniu danych dziennika do usługi Power BI](../azure-monitor/platform/powerbi.md).
- Dowiedz się więcej o [wtyczce źródła danych Grafana Azure Monitor](../azure-monitor/platform/grafana-plugin.md).

