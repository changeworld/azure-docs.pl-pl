---
title: Badanie i udostępniać dane użycia interakcyjne skoroszytów w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Analiza demograficznych użytkowników aplikacji sieci web.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/12/2017
ms.author: mbullwin; daviste
ms.openlocfilehash: a871378b3e2cc0b34c925593c6f01952de3aa08e
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="investigate-and-share-usage-data-with-interactive-workbooks-in-application-insights"></a>Badanie i udostępniać dane użycia interakcyjne skoroszytów w usłudze Application Insights

Łączenie skoroszytów [Azure Application Insights](app-insights-overview.md) wizualizacje danych [zapytania analityczne](app-insights-analytics.md)i tekstu w dokumentach interaktywnego. Skoroszyty są edytowany przez innych członków zespołu dostęp do tego samego zasobu platformy Azure. Oznacza to, że zapytania i formanty używane do tworzenia skoroszytu, są dostępne dla innych osób odczytujących skoroszytu, dzięki czemu można je łatwo eksplorować, rozszerzać i sprawdź, czy błędy.

Skoroszyty są przydatne w scenariuszach, takich jak:

* Eksplorowanie użycia aplikacji, jeśli nie wiesz wcześniej metryki zainteresowania: liczby użytkowników, stawki przechowywania, kursy wymiany itp. W przeciwieństwie do innych narzędzi analizy użycia w usłudze Application Insights skoroszytów umożliwiają łączenie wielu rodzajów wizualizacje i analiz nadawania doskonałe rozwiązanie dla tego rodzaju dowolnych eksploracji.
* Do zespołu wyjaśniający, jak działa funkcja nowo wydanego, przez użytkownika przedstawiający Liczba interakcji klucza i innych metryk.
* Udostępnianie wyników A / B eksperymentu w Twojej aplikacji za pomocą innych członków zespołu. Można wyjaśnić cele dotyczące eksperymentów z tekstem, a następnie Pokaż każdy pomiar użycia i zapytania Analytics używane do analizowania eksperymentu, wraz z wyczyść dokumentów wywołania do tego, czy wszystkie metryki został powyżej lub poniżej docelowe.
* Raportowanie wpływ awarii na użycie aplikacji, połączenie danych, wyjaśnienie tekstu i omówienie kolejne kroki, aby uniknąć awarii w przyszłości.

> [!NOTE]
> Zasób usługi Application Insights musi zawierać wyświetleń strony lub zdarzeń niestandardowych w celu użycia skoroszytów. [Dowiedz się, jak skonfigurować aplikację do zbierania wyświetleń strony automatycznie z zestawu SDK usługi Application Insights JavaScript](app-insights-javascript.md).
> 
> 

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Edytowanie, zmienianie rozmieszczenia klonowania i usuwanie sekcji skoroszytu

Skoroszyt jest wprowadzonych sekcji: wizualizacje można edytować niezależnie użycia, wykresów, tabel, tekst i analiza wyników zapytania.

Aby edytować zawartość sekcji skoroszytu, kliknij przycisk **Edytuj** przycisk w dół i w prawo w sekcji skoroszytu.

![Formanty edycji sekcji Application Insights skoroszytów](./media/app-insights-usage-workbooks/editing-controls.png)

1. Po zakończeniu edycji sekcji, kliknij **przeprowadzić edycję** w lewym dolnym rogu sekcji.

2. Aby utworzyć kopię sekcji, kliknij przycisk **klonowania w tej sekcji** ikony. Tworzenie duplikatów sekcji to doskonały sposób w celu wykonania iteracji w zapytaniu bez utraty poprzedniej iteracji.

3. Aby przenieść w górę sekcji w skoroszycie, kliknij przycisk **Przenieś w górę** lub **Przenieś w dół** ikony.

4. Aby trwale usunąć sekcję, kliknij przycisk **Usuń** ikony.

## <a name="adding-usage-data-visualization-sections"></a>Dodawanie sekcji wizualizacji danych użycia

Skoroszyty oferuje cztery typy wizualizacji analizy użycia wbudowanych. Każdy odpowiedzi na typowe pytania dotyczące użycia aplikacji. Aby dodać tabel i wykresów innych niż te cztery sekcje, Dodaj Analytics query sekcje (patrz poniżej).

Aby dodać użytkowników, sesji zdarzeń i przechowywania sekcji do skoroszytu, użyj **Dodaj użytkowników** lub innych odpowiedni przycisk w dolnej części skoroszytu lub u dołu dowolnej sekcji.

![Sekcja użytkownicy w skoroszytach](./media/app-insights-usage-workbooks/users-section.png)

**Użytkownicy** sekcje odpowiedzi "ilu użytkowników wyświetli stronę niektórych lub używać niektórych funkcji Moja witryna"?

**Sesje** sekcje odpowiedzi "ile sesji użytkownicy poświęcają wyświetlanie niektórych strony lub funkcji z mojej lokacji?"

**Zdarzenia** sekcje odpowiedzi "ile razy użytkowników wyświetlania niektóre strony lub funkcja witryny sieci?"

Oferuje każdego z tych typów trzy sekcji tego samego zbioru kontrolek i wizualizacji:

* [Więcej informacji na temat edytowania sekcje użytkownikami, sesjami i zdarzenia](app-insights-usage-segmentation.md)
* Przełącz wykresu głównego, siatki histogram, automatyczne insights i przykładowej wizualizacji użytkowników przy użyciu **Pokaż wykres**, **Pokaż siatkę**, **Pokaż Insights**, i **przykładowych tych użytkowników** pola wyboru na początku każdej sekcji.

![Przechowywania części skoroszytów](./media/app-insights-usage-workbooks/retention-section.png)

**Przechowywania** sekcje odpowiedzi "Osób, które są wyświetlane niektóre strony lub używać niektórych funkcji w ciągu jednego dnia lub tygodnia, ile wrócił następnego dnia lub tygodnia?"

* [Dowiedz się więcej na temat edytowania sekcje przechowywania](app-insights-usage-retention.md)
* Przełącz opcjonalne użycie ogólnej przechowywania wykresu **Pokaż ogólną przechowywania wykresu** wyboru w górnej części sekcji.

## <a name="adding-application-insights-analytics-sections"></a>Dodawanie sekcji Application Insights analityka

![Analiza części skoroszytów](./media/app-insights-usage-workbooks/analytics-section.png)

Aby dodać sekcję zapytania analizy aplikacji wgląd do skoroszytu, użyj **dodać Analytics query** przycisk w dolnej części skoroszytu lub u dołu dowolnej sekcji.

Analiza zapytania sekcje umożliwiają dodawanie zapytań o dowolnego przez dane usługi Application Insights do skoroszytów. Taka elastyczność oznacza, że sekcje zapytania Analytics powinna być Twoje przejdź do odpowiedzi na pytania dotyczące witryny innej niż cztery wymienione powyżej dla użytkowników, sesji zdarzeń i przechowywania, takich jak:

* Jak wiele wyjątków throw witryny w okresie czasu jako spadek użycia?
* Jaki był dystrybucji czas ładowania strony dla użytkowników, którzy przeglądają niektóre strony?
* Ilu użytkowników wyświetlić niektórych zbiór stron w witrynie, ale nie innymi zestaw stron? Może to być przydatne zrozumieć, jeśli masz klastrów użytkowników, którzy korzystają z różnych podzbiory funkcji witryny (Użyj `join` operatora z `kind=leftanti` modyfikator w języku zapytań usługi Analiza dzienników).

Użyj [materiały referencyjne dotyczące języka zapytań usługi Analiza dzienników](https://docs.loganalytics.io/) Aby dowiedzieć się więcej o pisaniu zapytań.

## <a name="adding-text-and-markdown-sections"></a>Dodawanie tekstu i sekcji języka znaczników Markdown

Dodawanie nagłówków, wyjaśnienia i komentarzami do skoroszytów pomaga przekształcić zestaw tabel i wykresów udostępniono. Sekcje tekstu w pomocy technicznej skoroszyty [składni języka Markdown](https://daringfireball.net/projects/markdown/) tekstu formatowania, takich jak nagłówki pogrubienie, kursywa i listy punktowane.

Aby dodać sekcję tekstu do skoroszytu, użyj **Dodawanie tekstu** przycisk w dolnej części skoroszytu lub u dołu dowolnej sekcji.

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Zapisywanie i udostępnianie skoroszytów z zespołem

Skoroszyty są zapisywane w ramach zasobu usługi Application Insights w **Moje raporty** sekcja, która jest prywatny, w przypadku lub **udostępnionych raportów** sekcji, który jest dostępny dla wszystkich użytkowników mających dostęp do zasobu usługi Application Insights. Aby wyświetlić wszystkie skoroszyty w zasobie, kliknij przycisk **Otwórz** na pasku akcji.

Aby udostępnić skoroszytu, który jest aktualnie **Moje raporty**:

1. Kliknij przycisk **Otwórz** na pasku akcji
2. Kliknij przycisk "..." obok skoroszytu, który chcesz udostępnić
3. Kliknij przycisk **Przenieś do raportów udostępnionych**.

Aby udostępnić skoroszyt z łączem lub za pośrednictwem poczty e-mail, kliknij przycisk **udostępnianie** na pasku akcji. Należy pamiętać, że adresaci łącza muszą mieć dostęp do tego zasobu w portalu Azure, aby wyświetlić skoroszytu. Aby dokonać edycji, odbiorców, muszą mieć co najmniej uprawnienia współautora dla zasobu.

Aby przypiąć łącze do skoroszytu do pulpitu nawigacyjnego platformy Azure:

1. Kliknij przycisk **Otwórz** na pasku akcji
2. Kliknij przycisk "..." obok skoroszytu, który chcesz przypiąć
3. Kliknij przycisk **Przypnij do pulpitu nawigacyjnego**.

## <a name="next-steps"></a>Kolejne kroki

## <a name="next-steps"></a>Kolejne kroki
- Aby umożliwić korzystanie z użycia, Rozpocznij wysyłanie [zdarzeń niestandardowych](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) lub [wyświetlenia strony](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Jeżeli już zdarzeń niestandardowych lub wyświetleń strony, Poznaj narzędzia użycia, aby dowiedzieć się, jak używać usługi przez użytkowników.
    - [Użytkownicy, sesje, zdarzenia](app-insights-usage-segmentation.md)
    - [Lejki](usage-funnels.md)
    - [Przechowywanie](app-insights-usage-retention.md)
    - [User Flows (Przepływy użytkowników)](app-insights-usage-flows.md)
    - [Dodaj kontekstu użytkownika](app-insights-usage-send-user-context.md)
    
