---
title: Tworzenie niestandardowego pulpitu nawigacyjnego w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Ten przewodnik pomaga zrozumieć, jak pulpity nawigacyjne usługi Log Analytics mogą wizualizować wszystkie zapisane przeszukiwania dzienników, dzięki czemu pojedynczego obiektywy, aby wyświetlić swoje środowisko.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: magoedte
ms.openlocfilehash: 613001c8495ba6c6a259063b8d8d3bce21b66960
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53336428"
---
# <a name="create-a-custom-dashboard-for-use-in-log-analytics"></a>Tworzenie niestandardowego pulpitu nawigacyjnego do użycia w usłudze Log Analytics

Ten przewodnik pomaga zrozumieć, jak pulpity nawigacyjne usługi Log Analytics mogą wizualizować wszystkie zapisane przeszukiwania dzienników, dzięki czemu pojedynczego obiektywy, aby wyświetlić swoje środowisko.

>[!NOTE]
> Nie będzie można edytować istniejącą **Mój pulpit nawigacyjny**. Ta funkcja jest w trakcie wycofane.

![Przykładowy pulpit nawigacyjny](./media/dashboards/oms-dashboards-example-dash.png)

Wszystkie niestandardowe pulpity nawigacyjne tworzone w portalu pakietu OMS są również dostępne w aplikacji mobilnej usługi OMS. Zobacz następujące strony, aby dowiedzieć się więcej o aplikacji.

* [Aplikację OMS mobile z Microsoft Store](http://www.windowsphone.com/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)
* [Aplikację OMS mobile z Apple iTunes](https://itunes.apple.com/app/microsoft-operations-management/id1042424859?mt=8)

![mobilnego pulpitu nawigacyjnego](./media/dashboards/oms-search-mobile.png)

## <a name="how-do-i-create-my-dashboard"></a>Jak utworzyć mój pulpit nawigacyjny?
Aby rozpocząć, przejdź do strony Przegląd pakietu OMS. Zobaczysz **Mój pulpit nawigacyjny** kafelka po lewej stronie. Kliknij go, aby przejść do pulpitu nawigacyjnego.

![Przegląd](./media/dashboards/oms-dashboards-overview.png)

## <a name="adding-a-tile"></a>Dodawanie kafelka
W pulpitach nawigacyjnych kafelków są obsługiwane przez zapisane przeszukiwania dzienników. Pakiet OMS jest powiązana z wielu wstępnie utworzonych zapisanych wyszukiwań w dziennikach, dzięki czemu można rozpocząć od razu. Wykonaj następujące kroki, które ukazują, jak zacząć.

W widoku Mój pulpit nawigacyjny, wystarczy kliknąć **Dostosuj** wprowadzenia trybu dostosowania.

![Dostępny obrazkowy](./media/dashboards/oms-dashboards-pictorial01.png)

 Panel, który zostanie otwarty w prawej części strony pokazuje wszystkie obszaru roboczego zapisanych wyszukiwań w dziennikach. Aby wizualizacja przeszukiwania dzienników zapisane jako Kafelek, umieść kursor nad zapisanego kryterium wyszukiwania, a następnie kliknij przycisk **oraz** symboli.

![Dodaj Kafelki 1](./media/dashboards/oms-dashboards-pictorial02.png)

Po kliknięciu **oraz** symbolu, w widoku Mój pulpit nawigacyjny pojawi się nowy Kafelek.

![Dodaj Kafelki 2](./media/dashboards/oms-dashboards-pictorial03.png)

## <a name="edit-a-tile"></a>Edytowanie kafelka
W widoku Mój pulpit nawigacyjny, wystarczy kliknąć **Dostosuj** wprowadzenia trybu dostosowania. Kliknij Kafelek, który chcesz edytować. Zmiany prawym panelu, aby edytować i zapewnia wybór opcji:

![Edytowanie kafelka](./media/dashboards/oms-dashboards-pictorial04.png)

![Edytowanie kafelka](./media/dashboards/oms-dashboards-pictorial05.png)

### <a name="tile-visualizations"></a>Wizualizacje kafelków
Istnieją trzy rodzaje wizualizacje kafelków do wyboru:

| Typ wykresu | Jak działa |
| --- | --- |
| ![Wykres słupkowy](./media/dashboards/oms-dashboards-bar-chart.png) |Wyświetla czasowy wyniki wyszukiwania dziennika zapisane jako wykres słupkowy lub Podaj listę wyników według pola w zależności od tego, jeśli wyszukiwanie w dzienniku agreguje wyniki według pola, czy nie. |
| ![metryka](./media/dashboards/oms-dashboards-metric.png) |Wyświetla Twoje całkowita log search wynik trafień jako liczbę we fragmencie. Kafelki metryk umożliwiają ustawienie progu z wyróżnieniem Kafelek, po osiągnięciu wartości progowej. |
| ![Wiersz](./media/dashboards/oms-dashboards-line.png) |Wyświetla osi czasu w Twojej zapisany dziennik wyszukiwania wyniku trafienia przy użyciu wartości jako wykres liniowy. |

### <a name="threshold"></a>Próg
Próg można utworzyć Kafelek za pomocą metryk wizualizacji. Wybierz na, aby utworzyć wartość progową na kafelku. Wybierz opcję podświetlania kafelka, gdy wartość jest powyżej lub poniżej wybrany próg, a następnie ustaw wartość próg, poniżej.

## <a name="organizing-the-dashboard"></a>Organizowanie pulpitu nawigacyjnego
Aby zorganizować pulpitu nawigacyjnego, przejdź do widoku Mój pulpit nawigacyjny, a następnie kliknij przycisk **Dostosuj** wprowadzenia trybu dostosowania. Kliknij i przeciągnij Kafelek, który chcesz przenieść, a następnie przenieś go w odpowiednie miejsce kafelka jako.

![Organizowanie pulpitu nawigacyjnego](./media/dashboards/oms-dashboards-organize.png)

## <a name="remove-a-tile"></a>Usuń Kafelek
Aby usunąć Kafelek, przejdź do widoku Mój pulpit nawigacyjny, a następnie kliknij przycisk **Dostosuj** wprowadzenia trybu dostosowania. Wybierz Kafelek, aby usunąć, a następnie w prawym panelu wybierz **Usuń Kafelek**.

![Usuń Kafelek](./media/dashboards/oms-dashboards-remove-tile.png)

## <a name="next-steps"></a>Kolejne kroki
* Tworzenie [alerty](../../azure-monitor/platform/alerts-overview.md) w usłudze Log Analytics w celu generowania powiadomień i Korygowanie problemów.
