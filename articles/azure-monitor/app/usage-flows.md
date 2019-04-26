---
title: Analizowanie wzorców nawigacji użytkowników dzięki przepływom użytkowników w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Analizuj, w jaki sposób użytkownicy przechodzą między stronami i funkcje aplikacji sieci web.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 91274fad4e56c69777333c81ea3b32dccdcf64ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60373302"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analizowanie wzorców nawigacji użytkowników dzięki przepływom użytkowników w usłudze Application Insights

![Narzędzie przepływy użytkownika szczegółowych informacji w aplikacji](./media/usage-flows/00001-flows.png)

Narzędzie przepływy użytkownika wizualizuje, w jaki sposób użytkownicy przechodzą między stronami i funkcji lokacji. Jest to idealne narzędzie do odpowiedzi na pytania, takie jak:

* Jak użytkownicy opuścić stronę w witrynie?
* Co użytkownicy klikają stronę w witrynie?
* Gdzie są miejsc, które użytkownicy postęp dokonany w najbardziej z witryny?
* Czy istnieją miejsca, w której użytkownicy Powtórz tę samą akcję wielokrotnie?

Uruchamia narzędzie przepływy użytkownika widoku strony początkowej, zdarzenie niestandardowe lub wyjątek, który określisz. Biorąc pod uwagę to zdarzenie początkowe, przepływy użytkownika pokazuje zdarzenia, które wystąpiły przed i po tym dniu podczas sesji użytkownika. Wiersze o różnej grubości pokazują, ile razy każda ścieżka użytego przez użytkowników. Specjalne **rozpoczęcia sesji** węzłów pokazać, gdzie kolejnych węzłów rozpoczęcia sesji. **Zakończono sesji** węzłów pokazują, jak wielu użytkowników wysyłane nie wyświetleń stron lub zdarzeń niestandardowych po poprzednim węźle wyróżnianie, gdzie użytkownicy prawdopodobnie pozostałych lokacji.

> [!NOTE]
> Zasób usługi Application Insights musi zawierać wyświetleń stron lub zdarzeń niestandardowych, aby użyć narzędzia przepływy użytkownika. [Dowiedz się, jak skonfigurować aplikację do zbierania wyświetleń stron, które automatycznie za pomocą zestawu SDK języka JavaScript usługi Application Insights](../../azure-monitor/app/javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Rozpocznij, wybierając zdarzenie początkowe

![Wybierz zdarzenie początkowe przepływy użytkownika](./media/usage-flows/00002-flows-initial-event.png)

Aby rozpocząć, odpowiadanie na pytania za pomocą narzędzia przepływy użytkownika, wybierz widok strony początkowej, zdarzenie niestandardowe lub wyjątków, która będzie służyć jako punkt początkowy dla wizualizacji:

1. Kliknij link w **co robią użytkownicy po...?**  tytułu, lub kliknij przycisk **Edytuj** przycisku.
2. Wybierz widok strony, zdarzenie niestandardowe lub wyjątek z **zdarzeń początkowego** listy rozwijanej.
3. Kliknij przycisk **Utwórz wykres**.

Kolumna "Krok 1" w wizualizacji, pokazuje, co użytkownicy czy najczęściej zaraz po zdarzenie początkowe, uporządkowane od góry do dołu od najczęściej do najrzadziej. "Krok 2" i kolejnych kolumn przedstawiające, co użytkownicy czy później, utworzenie obrazu wszystkich użytkowników sposoby przeszedł za pośrednictwem swojej witryny.

Domyślnie narzędzie przepływy użytkownika przykłady losowo tylko ostatnich 24 godzinach wyświetleń stron i zdarzeń niestandardowych z witryny. Można zwiększyć zakres czasu i zmienić saldo wydajności i dokładności losowego próbkowanie w menu Edycja.

Jeśli wyświetleń stron, niestandardowych zdarzeń i wyjątków nie są istotne dla Ciebie, kliknij przycisk **X** w węzłach, którą chcesz ukryć. Po wybraniu węzłów, aby ukryć, kliknij przycisk **Utwórz wykres** znajdujący się poniżej wizualizacji. Aby wyświetlić wszystkie węzły zostały ukryte, kliknij **Edytuj** przycisk, a następnie sprawdź **wykluczone zdarzenia** sekcji.

Jeśli wyświetleń stron lub zdarzeń niestandardowych brakuje który powinna się pojawić na wizualizacji:

* Sprawdź **wykluczone zdarzenia** sekcji **Edytuj** menu.
* Przyciski plus na **innych** węzłów do uwzględnienia rzadziej zdarzenia w wizualizacji.
* Jeśli wyświetlenie strony lub zdarzeń niestandardowych oczekujesz, że jest rzadko przez użytkowników, spróbuj wysłać zwiększenie zakresu czasu dla wizualizacji w **Edytuj** menu.
* Wyświetl stronę upewnij się, że, zdarzenie niestandardowe lub wyjątku, jakiego oczekujesz jest skonfigurować mają zostać zebrane przez zestaw SDK usługi Application Insights w kodzie źródłowym w lokacji. [Dowiedz się więcej na temat zbierania zdarzeń niestandardowych.](../../azure-monitor/app/api-custom-events-metrics.md)

Jeśli chcesz zobaczyć więcej czynności w wizualizacji, użyj **poprzednie kroki** i **następne kroki** list rozwijanych nad wizualizacją.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Po odwiedzeniu strony lub funkcja, której przejdzie i co kliknięciu?

![Zapoznaj się z przepływy użytkownika, aby zrozumieć, gdy użytkownik kliknie](./media/usage-flows/00003-flows-one-step.png)

Jeśli Twoje zdarzenie początkowe widok strony, pierwszą kolumnę wizualizację ("krok 1") jest szybko zrozumieć użytkowników została natychmiast po odwiedzając stronę. Spróbuj otworzyć witryny w oknie obok wizualizacji przepływów użytkownika. Porównaj Twoje oczekiwania sposobu interakcji użytkowników ze stroną do listy zdarzeń, w kolumnie "Krok 1". Często elementu interfejsu użytkownika na stronie, który wydaje się nieważny do Twojego zespołu może być między najczęściej używanych na stronie. Może być warto od niego ulepszenia projektu do witryny.

Jeśli Twoje zdarzenie początkowe zdarzenie niestandardowe, pierwsza kolumna zawiera użytkowników czy zaraz po wykonaniu tej akcji. Podobnie jak w przypadku wyświetlenia strony rozważ, czy zachowanie obserwowanych użytkowników odpowiada oczekiwań i celów zespołu. Jeśli wybrane zdarzenia początkowej "Dodano element do koszyka", na przykład sprawdzić, jeśli "Przejdź do wyewidencjonowania" i "Kup ukończone" są wyświetlane w wizualizacji w krótkim czasie. Jeśli zachowanie użytkownika różni się od Twoich oczekiwań, należy użyć wizualizacji, aby zrozumieć, w jaki sposób użytkownicy otrzymują "zablokował" przez bieżący projekt sieci Web.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Gdzie są miejsc, które użytkownicy postęp dokonany w najbardziej z witryny?

Poszukaj **sesja zakończona** węzły, które pojawiają się w górę o wysokiej kolumny w wizualizacji, szczególnie na wczesnym etapie przepływu. Oznacza to, wielu użytkowników, prawdopodobnie współczynnik z lokacji po wykonaniu powyższa ścieżka stron i interakcje interfejsu użytkownika. Czasami ilość danych churn — po zakończeniu zakupu w witrynie handlu elektronicznego, na przykład -, ale zmian jest zazwyczaj logowania problemy związane z projektem, niska wydajność lub inne problemy z witryny, można zwiększyć.

Należy pamiętać, że **sesja zakończona** węzły są tylko na podstawie danych telemetrycznych zebranych przez ten zasób usługi Application Insights. Jeśli usługi Application Insights nie odbiera danych telemetrycznych dla niektórych interakcji użytkownika, użytkownicy nadal może mieć interakcji z witryny w tych celach, po narzędzie przepływy użytkownika mówi, sesja została zakończona.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Czy istnieją miejsca, w której użytkownicy Powtórz tę samą akcję wielokrotnie?

Zwróć uwagę na wyświetlenie strony lub niestandardowe zdarzenie, które powtarza się przez wielu użytkowników w kolejnych krokach wizualizacji. Zwykle oznacza to, że użytkownicy wprowadzają powtarzających się czynności w witrynie. Jeśli okaże się powtórzenia myśleć o zmianę projektu witryny sieci lub dodanie nowych funkcji, aby zmniejszyć powtórzenia. Na przykład dodanie funkcji edycji zbiorczej, jeśli okaże się użytkownikom wykonywanie powtarzających się akcji w każdym wierszu table element.

## <a name="common-questions"></a>Często zadawane pytania

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>Nie reprezentują zdarzenie początkowe zdarzenie pojawi się w ramach sesji po raz pierwszy, lub dowolnym momencie, w jakiej występuje w sesji?

Zdarzenie początkowe na wizualizacji reprezentuje tylko użytkownik wysłał tego widoku strony lub niestandardowe zdarzenie podczas sesji po raz pierwszy. Jeśli użytkownicy mogą wysłać zdarzenie początkowe wielokrotnie podczas sesji, a następnie w kolumnie "Krok 1" zawiera tylko zachowanie użytkowników po *pierwszy* wystąpienia zdarzenie początkowe, nie wszystkie wystąpienia.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Niektóre węzły w mojej wizualizacji są zbyt ogólne. Na przykład węzeł, który właśnie jest wyświetlany komunikat "Kliknięciu przycisku". Jak mogę ją rozbić na bardziej szczegółowe węzły?

Użyj **podział według** opcji na liście **Edytuj** menu:

1. Wybierz zdarzenie ma być podziału **zdarzeń** menu.
2. Wybierz wymiar w **wymiaru** menu. Na przykład w przypadku zdarzeń o nazwie "Kliknięty przycisk" Spróbuj właściwość niestandardowa o nazwie "Przycisk Name".

## <a name="next-steps"></a>Kolejne kroki

* [Przegląd wykorzystania](usage-overview.md)
* [Użytkownicy, sesje i zdarzenia](usage-segmentation.md)
* [Przechowywanie](usage-retention.md)
* [Dodawanie zdarzeń niestandardowych do aplikacji](../../azure-monitor/app/api-custom-events-metrics.md)