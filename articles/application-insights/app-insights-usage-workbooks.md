---
title: Badanie i udostępniać dane dotyczące użycia interaktywne skoroszyty w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Analiza demograficznych użytkowników aplikacji sieci web.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 06/12/2017
ms.reviewer: daviste
ms.author: mbullwin
ms.openlocfilehash: 016a26acc153fba1c38d926fd5389d02755c2ff5
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35648968"
---
# <a name="investigate-and-share-usage-data-with-interactive-workbooks-in-application-insights"></a>Badanie i udostępniać dane dotyczące użycia interaktywne skoroszyty w usłudze Application Insights

Połącz skoroszyty [usługi Azure Application Insights](app-insights-overview.md) wizualizacje danych [zapytań analitycznych](app-insights-analytics.md)i tekstu w dokumentach interaktywne. Skoroszyty są edytowalne przez innych członków zespołu, dostęp do tego samego zasobu platformy Azure. Oznacza to, że zapytania i kontrolek używanych w celu utworzenia skoroszytu są dostępne dla innych osób odczytu skoroszytu, dzięki czemu można łatwo eksplorować, rozszerzanie i sprawdź, czy błędów.

Skoroszyty są przydatne w scenariuszach, takich jak:

* Eksplorowanie użycia aplikacji, kiedy nie wiesz wcześniej istotne metryki: liczby użytkowników, danych, współczynniki konwersji itp. W przeciwieństwie do innych narzędzi analitycznych użycia w usłudze Application Insights skoroszyty pozwalają połączyć wiele rodzajów wizualizacji i analiz, dzięki czemu idealne narzędzie do tego rodzaju dowolnych eksploracji.
* Do zespołu wyjaśniające, jak działa funkcja nowo wydanego, przedstawiający użytkownika Liczba interakcji kluczy i innych metryk.
* Udostępnianie wyników, a / B eksperymentów w swojej aplikacji z innymi członkami zespołu. Można wyjaśnić cele eksperymentu z tekstem, a następnie Pokaż każdego metryki użycia i zapytania usługi Analytics używane do oceny eksperymentów, wraz z wyczyść wezwaniem dla tego, czy wszystkie metryki został powyżej lub poniżej docelowymi.
* Raportowanie wpływ awarii na użycie aplikacji, łącząc dane, tekst wyjaśnienie i dyskusję na temat następnych kroków, aby uniknąć przerw w przyszłości.

> [!NOTE]
> Zasób usługi Application Insights musi zawierać wyświetleń stron lub zdarzeń niestandardowych, aby użyć skoroszytów. [Dowiedz się, jak skonfigurować aplikację do zbierania wyświetleń stron, które automatycznie za pomocą zestawu SDK języka JavaScript usługi Application Insights](app-insights-javascript.md).
> 
> 

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Edytowanie, rozmieszczanie, klonowanie i usuwanie sekcji skoroszytu

Skoroszyt jest wprowadzonych w sekcjach: wizualizacje można edytować niezależnie użycia, wykresy, tabele, tekst lub wyniki zapytania usługi Analytics.

Aby edytować zawartość sekcji skoroszytu, kliknij przycisk **Edytuj** przycisk poniżej, a po prawej stronie w sekcji skoroszytu.

![Application Insights skoroszyty sekcji formanty edycji](./media/app-insights-usage-workbooks/editing-controls.png)

1. Po zakończeniu edycji sekcji, kliknij **przeprowadzić edycję** w lewym dolnym rogu sekcji.

2. Aby utworzyć duplikat sekcji, kliknij przycisk **Sklonuj tę sekcję** ikony. Tworzenie duplikatów sekcje to doskonały sposób iteracji w zapytaniu bez utraty poprzednich iteracji.

3. Aby przenieść w górę do sekcji w skoroszycie, kliknij przycisk **Przenieś w górę** lub **Przenieś w dół** ikony.

4. Aby trwale usunąć sekcję, kliknij przycisk **Usuń** ikony.

## <a name="adding-usage-data-visualization-sections"></a>Dodawanie sekcji wizualizacji danych użycia

Skoroszyty oferuje cztery typy wizualizacje analizy użycia wbudowanych. Każdej odpowiedzi na typowe pytania dotyczące użycia aplikacji. Aby dodać, tabel i wykresów innych niż te cztery sekcje, Dodaj sekcje zapytania usługi Analytics (patrz poniżej).

Aby dodać użytkowników, sesje, zdarzenia lub przechowywania sekcji do skoroszytu, użyj **Add Users** lub inny odpowiedni przycisk w dolnej części skoroszytu lub u dołu dowolną sekcję.

![Sekcja użytkownicy arkusza w skoroszycie](./media/app-insights-usage-workbooks/users-section.png)

**Użytkownicy** sekcje odpowiedzi "ilu użytkowników niektóre strony wyświetli lub używać niektórych funkcji Moja witryna"?

**Sesje** sekcje odpowiedzi "ile sesji użytkownicy spędzają wyświetlanie niektóre strony lub korzystanie z niektórych funkcji Moja witryna?"

**Zdarzenia** sekcje odpowiedzi "ile razy użytkownikom wyświetlanie niektóre strony lub korzystać z niektórych funkcji Moja witryna?"

Każdy z tych typów trzy części oferuje ten sam zestawów formantów i wizualizacji:

* [Dowiedz się więcej o edytowaniu w sekcji użytkownicy, sesje i zdarzenia](app-insights-usage-segmentation.md)
* Przełącz wykresu głównego, siatek histogram, automatyczne szczegółowych informacji i przykładowy użytkowników wizualizacji przy użyciu **Pokaż wykres**, **Pokaż siatkę**, **Insights Pokaż**i **Przykładowych użytkowników te** pól wyboru w górnej części każdej sekcji.

![Sekcja przechowywania arkusza w skoroszycie](./media/app-insights-usage-workbooks/retention-section.png)

**Przechowywanie** sekcje odpowiedzi "Osób, które niektóre strony wyświetli lub używać niektórych funkcji w ciągu jednego dnia lub tygodnia, ile wrócił następnego dnia lub tygodnia?"

* [Dowiedz się więcej o edytowaniu sekcje przechowywania](app-insights-usage-retention.md)
* Przełącz do opcjonalne całkowity okres przechowywania wykresu przy użyciu **wykres całkowitego przechowywania Show** pole wyboru w górnej części tej sekcji.

## <a name="adding-application-insights-analytics-sections"></a>Dodawanie sekcji analizy usługi Application Insights

![Sekcja analizy arkusza w skoroszycie](./media/app-insights-usage-workbooks/analytics-section.png)

Aby dodać sekcję zapytania analizy usługi Application Insights do swojego skoroszytu, użyj **zapytania analizy Dodaj** przycisk w dolnej części skoroszytu lub u dołu dowolną sekcję.

Sekcje zapytania usługi Analytics pozwalają na dodawanie zapytań o dowolne danych usługi Application Insights do skoroszytów. Ta elastyczność oznacza, że sekcje zapytania usługi Analytics powinna być praktyczny na dla odpowiedzi na pytania dotyczące witryny innej niż czterech wymienionych powyżej dla użytkowników, sesje, zdarzenia i przechowywania, takich jak:

* Jak wiele wyjątków throw witryny w przedziale czasu jako spadek użycia?
* Jaki był Rozkład czasów ładowania strony użytkownikom, którzy wyświetlają niektóre strony?
* Ilu użytkowników wyświetlane niektóre zbiór stron w witrynie, ale nie inny zestaw stron? Może to być przydatne do zrozumienia, jeśli masz klastrów użytkowników, którzy korzystają z różnych podzbiory funkcji witryny sieci (Użyj `join` operator `kind=leftanti` modyfikator w języku zapytań usługi Log Analytics).

Użyj [dokumentacja języka zapytań usługi Log Analytics](https://docs.loganalytics.io/) Aby dowiedzieć się więcej na temat pisania zapytań.

## <a name="adding-text-and-markdown-sections"></a>Dodawanie tekstu i sekcje znaczników Markdown

Dodawanie nagłówków, wyjaśnień i komentarz do skoroszyty pomaga przekształcić zbiór tabel i wykresów narracji. Sekcje tekstu w pomocy technicznej skoroszyty [składnię języka znaczników Markdown](https://daringfireball.net/projects/markdown/) formatowania, takich jak nagłówki, pogrubienie, kursywa i list punktowanych tekstu.

Aby dodać sekcję tekstu do skoroszytu, należy użyć **Dodaj tekst** przycisk w dolnej części skoroszytu lub u dołu dowolną sekcję.

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Zapisywanie i udostępnianie skoroszytów swojemu zespołowi

Skoroszyty są zapisywane w ramach zasobu usługi Application Insights w **Moje raporty** sekcja, która jest prywatny, w przypadku lub **udostępnione raporty** sekcja, która jest dostępny dla wszystkich użytkowników z dostępem do Zasób usługi Application Insights. Aby wyświetlić wszystkie skoroszyty w zasobie, kliknij **Otwórz** przycisk na pasku akcji.

Aby udostępnić skoroszytu, w którym znajduje się obecnie w **Moje raporty**:

1. Kliknij przycisk **Otwórz** na pasku akcji
2. Kliknij przycisk "..." obok skoroszytu, który chcesz udostępnić
3. Kliknij przycisk **Przenieś do raportów udostępnionych**.

Aby udostępnić skoroszyt za pomocą łącza lub za pośrednictwem poczty e-mail, kliknij przycisk **udostępnianie** na pasku akcji. Należy pamiętać, że adresaci łącza muszą mieć dostęp do tego zasobu w witrynie Azure portal, aby wyświetlić skoroszyt. Aby dokonać edycji, adresaci muszą dysponować co najmniej uprawnienia współautora dla zasobu.

Aby przypiąć łącze do skoroszytu na pulpicie nawigacyjnym platformy Azure:

1. Kliknij przycisk **Otwórz** na pasku akcji
2. Kliknij przycisk "..." obok skoroszytu, który chcesz przypiąć
3. Kliknij przycisk **Przypnij do pulpitu nawigacyjnego**.

## <a name="next-steps"></a>Kolejne kroki

## <a name="next-steps"></a>Kolejne kroki
- Aby umożliwić użycie środowiska, Rozpocznij wysyłanie [zdarzenia niestandardowe](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) lub [wyświetlenia strony](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Jeśli już wysyłać niestandardowe zdarzenia lub wyświetlenia stron, zapoznaj się z narzędzia obciążenia, aby dowiedzieć się, jak używać usługi przez użytkowników.
    - [Użytkownicy, sesje, zdarzenia](app-insights-usage-segmentation.md)
    - [Lejki](usage-funnels.md)
    - [Przechowywanie](app-insights-usage-retention.md)
    - [User Flows (Przepływy użytkowników)](app-insights-usage-flows.md)
    - [Dodawanie kontekstu użytkownika](app-insights-usage-send-user-context.md)
    
