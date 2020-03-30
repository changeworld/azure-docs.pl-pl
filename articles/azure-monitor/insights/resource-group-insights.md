---
title: Usługa Azure Monitor Resource Group Insights | Dokumenty firmy Microsoft
description: Opis kondycji i wydajności rozproszonych aplikacji i usług na poziomie grupy zasobów za pomocą usługi Azure Monitor
ms.subservice: ''
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 6d97e40bf2bf2298fb53609621db8ff2c6f1038f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663542"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Monitorowanie grup zasobów za pomocą usługi Azure Monitor (wersja zapoznawcza)

Nowoczesne aplikacje są często złożone i wysoce rozproszone z wieloma oddzielnymi częściami współpracującymi ze sobą w celu świadczenia usługi. Uznając tę złożoność, usługa Azure Monitor udostępnia szczegółowe informacje dotyczące monitorowania grup zasobów. Ułatwia to klasyfikowanie i diagnozowanie wszelkich problemów napotykanych przez poszczególne zasoby, jednocześnie oferując kontekst kondycji i wydajności grupy&mdash;zasobów i aplikacji&mdash;jako całości.

## <a name="access-insights-for-resource-groups"></a>Uzyskiwanie dostępu do szczegółowych informacji dla grup zasobów

1. Wybierz **grupę zasobów** z lewego paska nawigacyjnego.
2. Wybierz jedną z grup zasobów, które chcesz eksplorować. (Jeśli masz dużą liczbę grup zasobów filtrowania według subskrypcji może być czasami pomocne.)
3. Aby uzyskać dostęp do szczegółowych informacji dla grupy zasobów, kliknij pozycję **Insights** w menu po lewej stronie dowolnej grupy zasobów.

![Zrzut ekranu przedstawiający stronę przeglądu statystyk grupy zasobów](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Zasoby z aktywnymi alertami i problemami zdrowotnymi

Na stronie przeglądu pokazano, ile alertów zostało zwolnionych i nadal są aktywne, wraz z bieżącym kondycji zasobów platformy Azure każdego zasobu. Razem te informacje mogą pomóc szybko wykryć wszelkie zasoby, które występują problemy. Alerty ułatwią wykrywanie problemów w kodzie i sposobu skonfigurowania infrastruktury. Powierzchnie usługi Azure Resource Health problem z samej platformy Azure, które nie są specyficzne dla poszczególnych aplikacji.

![Zrzut ekranu przedstawiający okienko Kondycja zasobów platformy Azure](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Aby wyświetlić kondycję zasobów platformy Azure, zaznacz pole **Pokaż kondycję zasobów platformy Azure** nad tabelą. Ta kolumna jest domyślnie ukryta, aby ułatwić szybkie załadowanie strony.

![Zrzut ekranu z dodanym wykresem kondycji zasobów](./media/resource-group-insights/0003-overview.png)

Domyślnie zasoby są pogrupowane według warstwy aplikacji i typu zasobu. **Warstwa aplikacji** to prosta kategoryzacja typów zasobów, która istnieje tylko w kontekście strony przeglądu szczegółowych informacji o grupie zasobów. Istnieją typy zasobów związane z kodem aplikacji, infrastrukturą obliczeniową, siecią, magazynem + bazami danych. Narzędzia do zarządzania otrzymują własne warstwy aplikacji, a każdy inny zasób jest klasyfikowany jako należący do warstwy **Inne aplikacje.** To grupowanie może pomóc zobaczyć w skrócie, jakie podsystemy aplikacji są w dobrej kondycji i niezdrowe.

## <a name="diagnose-issues-in-your-resource-group"></a>Diagnozowanie problemów w grupie zasobów

Strona szczegółowych informacji o grupie zasobów zawiera kilka innych narzędzi o zakresie ułatwiających diagnozowanie problemów

   |         |          |
   | ---------------- |:-----|
   | [**Alerty**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)      |  Wyświetlaj, twórz alerty i zarządzaj nimi. |
   | [**Metryki**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) | Wizualizuj i eksploruj dane oparte na danych metryk.    |
   | [**Dzienniki aktywności**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) | Zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure.  |
   | [**Mapa aplikacji**](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) | Nawiguj po topologii aplikacji rozproszonej, aby zidentyfikować wąskie gardła wydajności lub punkty aktywne awarii. |

## <a name="failures-and-performance"></a>Awarie i wydajność

Co zrobić, jeśli zauważyłeś, że aplikacja działa wolno lub użytkownicy zgłaszali błędy? Wyszukiwanie wszystkich zasobów w celu wyizolowania problemów jest czasochłonne.

Karty **Wydajność** i **awarie** upraszczają ten proces, łącząc widoki diagnostyki wydajności i awarii dla wielu typowych typów zasobów.

Większość typów zasobów otworzy galerię szablonów skoroszytu usługi Azure Monitor. Każdy utworzony skoroszyt można dostosować, zapisać, udostępnić zespołowi i ponownie w przyszłości zdiagnozować podobne problemy.

### <a name="investigate-failures"></a>Badanie błędów

Aby przetestować kartę Błędy, **wybierz** błędy w obszarze **Zbadaj** w menu po lewej stronie.

Pasek menu po lewej stronie zmienia się po dokonaniu wyboru, oferując nowe opcje.

![Zrzut ekranu przedstawiający okienko Przegląd awarii](./media/resource-group-insights/00004-failures.png)

Po wybraniu usługi App Service zostanie wyświetlona galeria szablonów skoroszytu usługi Azure Monitor.

![Zrzut ekranu przedstawiający galerię skoroszytu aplikacji](./media/resource-group-insights/0005-failure-insights-workbook.png)

Wybranie szablonu dla usługi Failure Insights spowoduje otwarcie skoroszytu.

![Zrzut ekranu przedstawiający raport o niepowodzeniu](./media/resource-group-insights/0006-failure-visual.png)

Można wybrać dowolny z wierszy. Zaznaczenie jest następnie wyświetlane w graficznym widoku szczegółów.

![Zrzut ekranu przedstawiający szczegóły awarii](./media/resource-group-insights/0007-failure-details.png)

Skoroszyty odciął trudną pracę tworzenia niestandardowych raportów i wizualizacji w formacie łatwo zużywalnym. Podczas gdy niektórzy użytkownicy mogą chcieć tylko dostosować wstępnie utworzone parametry, skoroszyty można w pełni dostosować.

Aby zorientować się, jak ten skoroszyt działa wewnętrznie, wybierz pozycję **Edytuj** na górnym pasku.

![Zrzut ekranu przedstawiający dodatkową opcję edycji](./media/resource-group-insights/0008-failure-edit.png)

W pobliżu różnych elementów skoroszytu pojawi się wiele pól **Edycji.** Zaznacz pole **Edytuj** pod tabelą operacji.

![Zrzut ekranu przedstawiający pola edycji](./media/resource-group-insights/0009-failure-edit-graph.png)

Spowoduje to wyświetlenia podstawowej kwerendy dziennika, która napędza wizualizację tabeli.

 ![Zrzut ekranu przedstawiający okno kwerendy dziennika](./media/resource-group-insights/0010-failure-edit-query.png)

Kwerendę można zmodyfikować bezpośrednio. Możesz też użyć go jako odniesienia i pożyczyć od niego podczas projektowania własnego niestandardowego skoroszytu sparametryzowanego.

### <a name="investigate-performance"></a>Badanie wydajności

Performance oferuje własną galerię skoroszytów. W przypadku usługi App Service skoroszyt wydajności aplikacji jest przeznaczony dla wstępnie utworzonego:

 ![Zrzut ekranu przedstawiający widok wydajności](./media/resource-group-insights/0011-performance.png)

W takim przypadku po wybraniu opcji edytuj zobaczysz, że ten zestaw wizualizacji jest obsługiwany przez metryki usługi Azure Monitor.

 ![Zrzut ekranu przedstawiający widok wydajności za pomocą metryk platformy Azure](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="enabling-access-to-alerts"></a>Włączanie dostępu do alertów

Aby wyświetlić alerty w usłudze Azure Monitor dla grup zasobów, osoba z rolą właściciela lub współautora dla tej subskrypcji musi otworzyć usługę Azure Monitor dla grup zasobów dla dowolnej grupy zasobów w ramach subskrypcji. Umożliwi to każdemu, kto ma dostęp do odczytu, wyświetlanie alertów w usłudze Azure Monitor dla grup zasobów dla wszystkich grup zasobów w ramach subskrypcji. Jeśli masz rolę Właściciela lub Współautora, odśwież tę stronę w ciągu kilku minut.

Usługa Azure Monitor for Resource Groups polega na systemie zarządzania alertami usługi Azure Monitor w celu pobrania stanu alertów. Zarządzanie alertami nie jest domyślnie skonfigurowane dla każdej grupy zasobów i subskrypcji i może być włączone tylko przez osobę z rolą Właściciela lub Współautora. Można go włączyć przez:
* Otwieranie usługi Azure Monitor dla grup zasobów dla dowolnej grupy zasobów w ramach subskrypcji.
* Lub przechodząc do subskrypcji, klikając **dostawców zasobów,** a następnie klikając **pozycję Zarejestruj alerty.Zarządzanie**.

## <a name="next-steps"></a>Następne kroki

- [Skoroszyty monitora platformy Azure](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Alerty monitora platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)
