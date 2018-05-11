---
title: Utwórz niestandardowy pulpit nawigacyjny w Azure Log Analytics | Dokumentacja firmy Microsoft
description: Ten przewodnik pomaga zrozumieć, jak pulpity nawigacyjne analizy dzienników można Wizualizuj wszystkie dziennika zapisanych wyszukiwań, umożliwiając pojedynczego obiektyw, aby wyświetlić środowiska.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7eaa74655b009e3154e2cc4f09d5599040ec4589
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="create-a-custom-dashboard-for-use-in-log-analytics"></a>Utwórz niestandardowy pulpit nawigacyjny do użycia w analizy dzienników

Ten przewodnik pomaga zrozumieć, jak pulpity nawigacyjne analizy dzienników można Wizualizuj wszystkie dziennika zapisanych wyszukiwań, umożliwiając pojedynczego obiektyw, aby wyświetlić środowiska.

>[!NOTE]
> Nie będzie można edytować istniejące **Mój pulpit nawigacyjny**. Ta funkcja jest w trakcie przestarzałe.

![Przykład pulpitu nawigacyjnego](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

Wszystkie niestandardowe pulpity nawigacyjne utworzone w portalu OMS są także dostępne w aplikacji mobilnej OMS. Zobacz następujące strony, aby uzyskać więcej informacji o aplikacjach.

* [OMS aplikacji mobilnej z Microsoft Store](http://www.windowsphone.com/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)
* [OMS aplikacji mobilnej z iTunes firmy Apple](https://itunes.apple.com/app/microsoft-operations-management/id1042424859?mt=8)

![mobilnego pulpitu nawigacyjnego](./media/log-analytics-dashboards/oms-search-mobile.png)

## <a name="how-do-i-create-my-dashboard"></a>Jak utworzyć mój pulpit nawigacyjny?
Aby rozpocząć, przejdź do strony Przegląd OMS. Zobaczysz **Mój pulpit nawigacyjny** kafelka po lewej stronie. Kliknij go, aby przejść do pulpitu nawigacyjnego.

![Przegląd](./media/log-analytics-dashboards/oms-dashboards-overview.png)

## <a name="adding-a-tile"></a>Dodawanie kafelka
W pulpitach nawigacyjnych Kafelki są obsługiwane przez zapisany dziennik wyszukiwania. OMS zawiera wiele wstępnie wprowadzone zapisany dziennik wyszukiwania, dzięki czemu będzie można od razu. Wykonaj następujące kroki, które przedstawiają sposób rozpocząć.

W widoku Mój pulpit nawigacyjny, wystarczy kliknąć **Dostosuj** wprowadzenia trybu dostosowania.

![Obrazkami](./media/log-analytics-dashboards/oms-dashboards-pictorial01.png)

 Panel, który zostanie otwarty w prawej części strony są wyświetlane wszystkie obszaru roboczego zapisany dziennik wyszukiwania. Do wizualizacji wyszukiwania zapisany dziennik jako Kafelek, umieść kursor nad zapisanego kryterium wyszukiwania, a następnie kliknij przycisk **plus** symbolu.

![Dodaj Kafelki 1](./media/log-analytics-dashboards/oms-dashboards-pictorial02.png)

Po kliknięciu **plus** symboli, fragment pojawia się w widoku Mój pulpit nawigacyjny.

![Dodaj Kafelki 2](./media/log-analytics-dashboards/oms-dashboards-pictorial03.png)

## <a name="edit-a-tile"></a>Edytowanie kafelka
W widoku Mój pulpit nawigacyjny, wystarczy kliknąć **Dostosuj** wprowadzenia trybu dostosowania. Kliknij Kafelek, który chcesz edytować. Zmiany prawym panelu, aby edytować i umożliwia wybranie opcji:

![Edytowanie kafelka](./media/log-analytics-dashboards/oms-dashboards-pictorial04.png)

![Edytowanie kafelka](./media/log-analytics-dashboards/oms-dashboards-pictorial05.png)

### <a name="tile-visualizations"></a>Wizualizacje kafelka
Istnieją trzy rodzaje wizualizacje kafelka do wyboru:

| Typ wykresu | jaką pełni funkcję |
| --- | --- |
| ![Wykres słupkowy](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png) |Wyświetla oś czasu wyników wyszukiwania zapisany dziennik jako wykres słupkowy lub lista wyników według pola w zależności od, jeśli wyszukiwanie dziennika agreguje wyniki według pola, czy nie. |
| ![metryka](./media/log-analytics-dashboards/oms-dashboards-metric.png) |Wyświetla trafień wynik wyszukiwania z całkowitej dziennika jako liczby na kafelku. Metryki kafelków umożliwiają ustawienie progu wyróżniane kafelka, po osiągnięciu progu. |
| ![wiersz](./media/log-analytics-dashboards/oms-dashboards-line.png) |Wyświetla wykres liniowy osi czasu z zapisany dziennik wyszukiwania wynik trafień wartościami. |

### <a name="threshold"></a>Próg
Próg można utworzyć na kafelku, za pomocą metryki wizualizacji. Wybierz na, aby utworzyć wartość progową na kafelku. Wybierz opcję podświetlania kafelka, gdy wartość jest powyżej lub poniżej wybrany próg, a następnie ustaw poniżej wartości progowej.

## <a name="organizing-the-dashboard"></a>Organizowanie pulpitu nawigacyjnego
Aby zorganizować pulpitu nawigacyjnego, przejdź do widoku Mój pulpit nawigacyjny, a następnie kliknij przycisk **Dostosuj** wprowadzenia trybu dostosowania. Kliknij i przeciągnij Kafelek, który chcesz przenieść, a następnie przenieś go do której ma być kafelka.

![Organizowanie pulpitu nawigacyjnego](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## <a name="remove-a-tile"></a>Usuń Kafelek
Aby usunąć kafelka, przejdź do widoku Mój pulpit nawigacyjny, a następnie kliknij przycisk **Dostosuj** wprowadzenia trybu dostosowania. Wybierz Kafelek, który chcesz usunąć, a następnie w prawym okienku wybierz **usunąć kafelka**.

![Usuń Kafelek](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## <a name="next-steps"></a>Kolejne kroki
* Utwórz [alerty](log-analytics-alerts.md) w analizy dzienników, aby generować powiadomienia i Korygowanie problemów.
