---
title: Szczegółowe informacje z grupy zasobów usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Zrozumieć kondycję i wydajność aplikacji rozproszonych i usług na poziomie grupy zasobów z usługą Azure Monitor
services: azure-monitor
author: NumberByColors
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: d5c07e0d4aca8bda42ea9f78a1475ea7bb5861f0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62119425"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Monitor grupy zasobów z usługą Azure Monitor (wersja zapoznawcza)

Nowoczesne aplikacje są często złożone i bardzo rozproszone z wielu poszczególnych części współpracują w celu dostarczenia usługi. Mając tę złożoność, usługi Azure Monitor udostępnia monitorowania szczegółowe informacje o grupach zasobów. To ułatwia klasyfikowanie i diagnozowanie problemów wystąpi poszczególnych zasobów, jednocześnie oferując kontekstu dotyczącego kondycji i wydajności, grupy zasobów&mdash;a Twoją aplikacją&mdash;jako całości.

## <a name="access-insights-for-resource-groups"></a>Informacje na temat technologii dostępu dla grup zasobów

1. Wybierz **grup zasobów** na pasku nawigacyjnym po lewej stronie.
2. Wybierz jedną z grup zasobów, które chcesz zbadać. (Jeśli masz dużą liczbę grup zasobów, filtrowanie według subskrypcji czasami użyteczne może.)
3. Aby uzyskać dostęp do szczegółowych informacji dla grupy zasobów, kliknij przycisk **Insights** w dowolnej grupie zasobów, w menu po lewej stronie.

![Zrzut ekranu przedstawiający stronę przeglądu usługi insights grupy zasobów](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Zasoby z aktywnych alertów i problemów z kondycją

Na stronie Przegląd pokazuje, ile alertów zostało wyzwolone i są nadal aktywne, wraz z bieżącym usługi Azure Resource Health każdego zasobu. Razem te informacje może pomóc szybko wychwycić wszelkie zasoby, do których występują problemy. Alerty ułatwiają wykrywanie problemów w kodzie i jak skonfigurowano infrastruktury. Usługa Resource Health powierzchnie problem z platformą Azure z samą platformę Azure, nie są specyficzne dla poszczególnych aplikacji.

![W okienku zrzut ekranu z usługi Azure Resource Health](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Aby wyświetlić usługi Azure Resource Health, zapoznaj się z **Pokaż usługi Azure Resource Health** pole w powyższej tabeli. W tej kolumnie jest domyślne ukryty, aby ułatwić szybkie ładowanie strony.

![Zrzut ekranu z dodać wykres kondycji zasobów](./media/resource-group-insights/0003-overview.png)

Domyślnie zasoby są grupowane według warstwy aplikacji i typu zasobu. **Warstwa aplikacji** jest proste kategoryzacji typów zasobów, a, który istnieje tylko w kontekście stronę przeglądu usługi insights grupy zasobów. Istnieją typy zasobów powiązany kod aplikacji, obliczenia, infrastruktury, sieci, Magazyn i bazy danych. Narzędzia do zarządzania uzyskanie własne warstwy aplikacji i wszystkich innych zasobów jest dzielony na kategorie jako należące do **innych** warstwy aplikacji. Ta metoda grupowania może pomóc w skrócie jakie podsystemy aplikacji są zdrowe i niezdrowe.

## <a name="diagnose-issues-in-your-resource-group"></a>Diagnozuj problemy w swojej grupie zasobów

Stronie szczegółowe informacje dotyczące grupy zasobów zawiera kilka innych narzędzi, o określonym zakresie, aby ułatwić diagnozowanie problemów

   |         |          |
   | ---------------- |:-----|
   | [**Alerts**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)      |  Wyświetlanie, tworzenie i Zarządzanie alertami. |
   | [**Metryki**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) | Wizualizować i eksplorować dane na podstawie metryki.    |
   | [**Dzienniki aktywności**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) | Subskrypcja zdarzeń na poziomie, które wystąpiły na platformie Azure.  |
   | [**Mapa aplikacji**](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) | Przejdź topologii aplikacji rozproszonej do identyfikowania wąskich gardeł wydajności lub obszarów nadmiernej aktywności błędów. |

## <a name="failures-and-performance"></a>Błędy i wydajności

Co zrobić, jeśli Zauważyłeś, aplikacja działa wolno lub użytkownicy zgłaszali błędy? Jest czasochłonne przeszukiwanie wszystkich Twoich zasobów, aby wyizolować problemy.

**Wydajności** i **błędy** karty upraszcza ten proces, łącząc wydajności i niepowodzeń Widoki diagnostyczne wielu powszechnie używanych typów zasobów.

Większość typów zasobów zostanie otwarty szablonów galerii platformy Azure Monitor skoroszytu. Można dostosować skoroszytów, które tworzysz, zapisane udostępniony Twojemu zespołowi i ponownie użyte w przyszłości, aby zdiagnozować podobne problemy.

### <a name="investigate-failures"></a>Zbadaj błędy

Do przetestowania wybierz kartę błędów **błędów** w obszarze **zbadaj** w menu po lewej stronie.

Na pasku menu po lewej stronie zmienia się po dokonaniu wyboru oferty nowych opcji.

![Zrzut ekranu błąd okienko omówienia](./media/resource-group-insights/00004-failures.png)

Po wybraniu usługi App Service są prezentowane za pomocą szablonów galerii platformy Azure Monitor skoroszytu.

![Zrzut ekranu przedstawiający galerii skoroszytu w aplikacji](./media/resource-group-insights/0005-failure-insights-workbook.png)

Wybieranie szablonu dla błędów szczegółowych informacji zostanie otwarty skoroszyt.

![Zrzut ekranu przedstawiający raport z niepowodzenia](./media/resource-group-insights/0006-failure-visual.png)

Można wybrać żadnego wiersza. Zaznaczenie zostanie wyświetlona w widoku szczegółowym graficznego.

![Zrzut ekranu przedstawiający szczegóły błędu](./media/resource-group-insights/0007-failure-details.png)

Skoroszyty natychmiast abstrakcyjna trudną pracę tworzenia niestandardowych raportów i wizualizacji w formie łatwy w użyciu. Gdy niektórzy użytkownicy mogą tylko chcesz dostosować parametry wbudowanych, skoroszyty są całkowicie dostosowany.

Aby uzyskać zorientować się, jak działa ten skoroszyt wewnętrznie, wybierz **Edytuj** na górnym pasku.

![Zrzut ekranu przedstawiający opcję Edytuj dodatkowe](./media/resource-group-insights/0008-failure-edit.png)

Szereg **Edytuj** pola są wyświetlane w pobliżu różne elementy skoroszytu. Wybierz **Edytuj** pole pod tabelą operacji.

![Zrzut ekranu przedstawiający pól edycji](./media/resource-group-insights/0009-failure-edit-graph.png)

Takie działanie spowoduje wyświetlenie zapytanie dziennika podstawowego, umożliwiające obsługę wizualizacji tabeli.

 ![Zrzut ekranu przedstawiający okno zapytania dziennika](./media/resource-group-insights/0010-failure-edit-query.png)

Zapytania można zmodyfikować bezpośrednio. Lub można go użyć jako odwołania i "pożyczać" z niego, opracowując własne niestandardowe skoroszytu sparametryzowanych.

### <a name="investigate-performance"></a>Zbadaj wydajność

Wydajność oferuje swój własny galerii skoroszytów. W przypadku usługi App Service wbudowanych skoroszytu wydajność aplikacji oferuje następujący widok:

 ![Zrzut ekranu przedstawiający widok wydajności](./media/resource-group-insights/0011-performance.png)

W tym przypadku jeśli wybierzesz edycji pojawi się, że ten zestaw wizualizacji jest obsługiwana przez metryk usługi Azure Monitor.

 ![Zrzut ekranu przedstawiający widok wydajności z metryk usługi Azure](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="enabling-access-to-alerts"></a>Umożliwianie dostępu do alertów

Aby wyświetlić alerty w usłudze Azure Monitor dla grup zasobów, inna osoba z roli współautora lub właściciela dla tej subskrypcji musi otworzyć usługi Azure Monitor dla grup zasobów dla każdej grupy zasobów w subskrypcji. Spowoduje to włączenie każda osoba mająca dostęp do odczytu, aby wyświetlić alerty w usłudze Azure Monitor dla grup zasobów dla wszystkich grup zasobów w subskrypcji. Jeśli masz roli współautora lub właściciela, Odśwież tę stronę za kilka minut.

Usługa Azure Monitor dla grup zasobów zależy od systemu zarządzania alertami monitorowania platformy Azure można pobrać stanu alertu. Zarządzanie alertami nie jest domyślnie skonfigurowana do każdej grupy zasobów i subskrypcji, i można ją włączyć tylko przez użytkownika z roli współautora lub właściciela. Można ją włączyć za:
* Otwieranie usługi Azure Monitor dla grup zasobów do dowolnej grupie zasobów w subskrypcji.
* Lub przechodząc do subskrypcji, klikając **dostawców zasobów**, klikając **zarejestrować Alerts.Management**.

## <a name="next-steps"></a>Kolejne kroki

- [Usługa Azure Monitor skoroszytów](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Alerty usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)
