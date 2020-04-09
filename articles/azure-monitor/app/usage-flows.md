---
title: Przepływy użytkowników usługi Azure Application Insights analizują przepływy nawigacji
description: Analizuj sposób, w jaki użytkownicy poruszają się między stronami i funkcjami aplikacji internetowej.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 8622ede9e6f7fba2fde2e0b2e90eb31520a23d04
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892448"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analizowanie wzorców nawigacji użytkownika za pomocą przepływów użytkownika w usłudze Application Insights

![Narzędzie Przepływy użytkowników usługi Application Insights](./media/usage-flows/00001-flows.png)

Narzędzie Przepływy użytkowników wizualizuje sposób, w jaki użytkownicy poruszają się między stronami i funkcjami witryny. Świetnie nadaje się do odpowiadania na pytania, takie jak:

* Jak użytkownicy odsuwają się od strony w witrynie?
* Co użytkownicy klikają na stronę w Twojej witrynie?
* Gdzie są miejsca, które użytkownicy najczęściej churn z witryny?
* Czy są miejsca, w których użytkownicy powtarzają tę samą czynność w kółko?

Narzędzie Przepływy użytkowników rozpoczyna się od początkowego widoku strony, zdarzenia niestandardowego lub wyjątku, który określisz. Biorąc pod uwagę to zdarzenie początkowe, Przepływy użytkownika pokazuje zdarzenia, które miały miejsce przed i później podczas sesji użytkownika. Linie o różnej grubości pokazują, ile razy każda ścieżka była śledzona przez użytkowników. Węzły **Rozpoczęte sesji** specjalne pokazują, gdzie kolejne węzły rozpoczęły sesję. **Węzły zakończone sesją** pokazują, ilu użytkowników nie wysłało żadnych wyświetleń strony lub zdarzeń niestandardowych po poprzednim węźle, podkreślając, gdzie użytkownicy prawdopodobnie opuścili witrynę.

> [!NOTE]
> Zasób usługi Application Insights musi zawierać widoki strony lub zdarzenia niestandardowe, aby korzystać z narzędzia Przepływy użytkownika. [Dowiedz się, jak skonfigurować aplikację do automatycznego zbierania widoków stron za pomocą zestawu SDK JavaScript aplikacji Insights](../../azure-monitor/app/javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Zacznij od wybrania zdarzenia początkowego

![Wybieranie zdarzenia początkowego dla przepływów użytkownika](./media/usage-flows/00002-flows-initial-event.png)

Aby rozpocząć odpowiadanie na pytania za pomocą narzędzia Przepływy użytkownika, wybierz początkowy widok strony, zdarzenie niestandardowe lub wyjątek, aby służyć jako punkt wyjścia do wizualizacji:

1. Kliknij łącze w tytule **Co użytkownicy robią po...?** lub kliknij przycisk **Edytuj.**
2. Wybierz widok strony, zdarzenie niestandardowe lub wyjątek z listy rozwijanej **Zdarzenie początkowe.**
3. Kliknij **pozycję Utwórz wykres**.

Kolumna "Krok 1" wizualizacji pokazuje, co użytkownicy robili najczęściej tuż po początkowym zdarzeniu, uporządkowano od góry do dołu od większości do najmniej częstych. "Krok 2" i kolejne kolumny pokazują, co użytkownicy zrobili później, tworząc obraz wszystkich sposobów, w jakie użytkownicy poruszali się po Twojej witrynie.

Domyślnie narzędzie Przepływy użytkowników losowo próbkuje tylko ostatnie 24 godziny wyświetleń strony i zdarzenia niestandardowego z witryny. Można zwiększyć zakres czasu i zmienić równowagę wydajności i dokładności do losowego próbkowania w menu Edycja.

Jeśli niektóre widoki strony, zdarzenia niestandardowe i wyjątki nie są dla Ciebie istotne, kliknij **x** w węzłach, które chcesz ukryć. Po wybraniu węzłów, które chcesz ukryć, kliknij przycisk **Utwórz wykres** pod wizualizacją. Aby wyświetlić wszystkie ukryte węzły, kliknij przycisk **Edytuj,** a następnie przejrzyj sekcję **Wykluczone zdarzenia.**

Jeśli brakuje widoków strony lub zdarzeń niestandardowych, które można oczekiwać, aby zobaczyć na wizualizacji:

* Sprawdź sekcję **Wykluczone zdarzenia** w menu **Edycja.**
* Użyj przycisków plus w **węzłach Inne,** aby uwzględnić rzadsze zdarzenia w wizualizacji.
* Jeśli oczekiwany widok strony lub zdarzenie niestandardowe jest wysyłane rzadko przez użytkowników, spróbuj zwiększyć zakres czasu wizualizacji w menu **Edycja.**
* Upewnij się, że widok strony, zdarzenie niestandardowe lub wyjątek, którego oczekujesz, jest skonfigurowany do zbierania przez zestaw SDK usługi Application Insights w kodzie źródłowym witryny. [Dowiedz się więcej o zbieraniu zdarzeń niestandardowych.](../../azure-monitor/app/api-custom-events-metrics.md)

Jeśli chcesz zobaczyć więcej kroków w wizualizacji, użyj menu rozwijanego **Poprzednie kroki** i **Następne kroki** nad wizualizacją.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Po odwiedzeniu strony lub funkcji, gdzie użytkownicy idą i co klikają?

![Użyj przepływów użytkownika, aby zrozumieć, gdzie użytkownicy klikają](./media/usage-flows/00003-flows-one-step.png)

Jeśli zdarzenie początkowe jest widokiem strony, pierwsza kolumna wizualizacji ("Krok 1") to szybki sposób na zrozumienie, co użytkownicy zrobili natychmiast po odwiedzeniu strony. Spróbuj otworzyć witrynę w oknie obok wizualizacji Przepływy użytkownika. Porównaj swoje oczekiwania dotyczące interakcji użytkowników ze stroną z listą zdarzeń w kolumnie "Krok 1". Często element interfejsu użytkownika na stronie, który wydaje się nieistotny dla zespołu, może być jednym z najczęściej używanych na stronie. Może to być świetny punkt wyjścia dla ulepszeń projektowych witryny.

Jeśli zdarzenie początkowe jest zdarzeniem niestandardowym, pierwsza kolumna pokazuje, co użytkownicy zrobili tuż po wykonaniu tej akcji. Podobnie jak w przypadku wyświetleń strony, należy wziąć pod uwagę, czy obserwowane zachowanie użytkowników odpowiada celom i oczekiwaniom zespołu. Jeśli wybrane zdarzenie początkowe to "Dodano przedmiot do koszyka", na przykład sprawdź, czy wkrótce w wizualizacji pojawią się "Przejdź do realizacji transakcji" i "Ukończony zakup". Jeśli zachowanie użytkownika różni się od oczekiwań, użyj wizualizacji, aby zrozumieć, w jaki sposób użytkownicy są "zalewkowani" przez bieżący projekt witryny.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Gdzie są miejsca, które użytkownicy najczęściej churn z witryny?

Uważaj na węzły **zakończone sesją,** które pojawiają się wysoko w kolumnie w wizualizacji, szczególnie na początku przepływu. Oznacza to, że wielu użytkowników prawdopodobnie churned z witryny po następujące poprzedniej ścieżki stron i interakcji interfejsu użytkownika. Czasami oczekuje się rezygnacji - na przykład po zakończeniu zakupu w witrynie eCommerce - ale zwykle churn jest oznaką problemów z projektowaniem, słabej wydajności lub innych problemów z witryną, które można poprawić.

Należy pamiętać, że węzły **zakończone sesją** są oparte tylko na danych telemetrycznych zebranych przez ten zasób usługi Application Insights. Jeśli usługa Application Insights nie otrzymuje danych telemetrycznych dla niektórych interakcji użytkownika, użytkownicy mogą nadal wchodzić w interakcje z witryną w ten sposób po tym, jak narzędzie Przepływy użytkowników informuje o zakończeniu sesji.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Czy są miejsca, w których użytkownicy powtarzają tę samą czynność w kółko?

Poszukaj widoku strony lub zdarzenia niestandardowego, które jest powtarzane przez wielu użytkowników w kolejnych krokach wizualizacji. Zwykle oznacza to, że użytkownicy wykonują powtarzające się czynności w Witrynie. Jeśli znajdziesz powtórzenie, pomyśl o zmianie projektu witryny lub dodaniu nowych funkcji, aby zmniejszyć powtarzanie. Na przykład dodawanie funkcji edycji zbiorczej, jeśli znajdziesz użytkowników wykonujących powtarzające się akcje w każdym wierszu elementu tabeli.

## <a name="common-questions"></a>Często zadawane pytania

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>Czy zdarzenie początkowe reprezentuje pierwszy raz, gdy zdarzenie pojawia się w sesji lub w dowolnym momencie, gdy pojawia się w sesji?

Zdarzenie początkowe w wizualizacji reprezentuje tylko pierwszy raz, gdy użytkownik wysłał ten widok strony lub zdarzenie niestandardowe podczas sesji. Jeśli użytkownicy mogą wysyłać zdarzenie początkowe wiele razy w sesji, kolumna "Krok 1" pokazuje tylko, jak zachowują się użytkownicy po *pierwszym* wystąpieniu zdarzenia początkowego, a nie wszystkie wystąpienia.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Niektóre węzły w mojej wizualizacji są zbyt wysokiego poziomu. Na przykład węzeł, który po prostu mówi "Button Clicked". Jak podzielić go na bardziej szczegółowe węzły?

Użyj opcji **Podziel według** w menu **Edycja:**

1. Wybierz zdarzenie, które chcesz podzielić w menu **Wydarzenie.**
2. Wybierz wymiar w menu **Wymiar.** Na przykład, jeśli masz zdarzenie o nazwie "Button Clicked", spróbuj niestandardową właściwość o nazwie "Nazwa przycisku".

## <a name="next-steps"></a>Następne kroki

* [Omówienie użycia](usage-overview.md)
* [Użytkownicy, sesje i zdarzenia](usage-segmentation.md)
* [Przechowywanie](usage-retention.md)
* [Dodawanie zdarzeń niestandardowych do aplikacji](../../azure-monitor/app/api-custom-events-metrics.md)
