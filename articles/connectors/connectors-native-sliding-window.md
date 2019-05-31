---
title: Planowanie zadań cyklicznych w oknie kroczącym wyzwalacza — Azure Logic Apps
description: Planowanie i uruchamianie automatycznych powtarzających się zadań i przepływów pracy za pomocą wyzwalacza przesuwanego okna, w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 44944955019fcf81fb0d296592577e2b00a15928
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299505"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-sliding-window-trigger-in-azure-logic-apps"></a>Tworzenie, planowanie i uruchamianie zadań cyklicznych i przepływów pracy za pomocą wyzwalacza przesuwanego okna, w usłudze Azure Logic Apps

Regularnie uruchamianie zadań, procesy lub zadania, które musi obsłużyć dane we fragmentach ciągłe, można uruchomić przepływu pracy aplikacji logiki za pomocą **oknie kroczącym — harmonogram** wyzwalacza. Można ustawić daty i godziny, a także strefę czasową dla uruchamiania przepływu pracy oraz cyklu dla powtarzających się ten przepływ pracy. Jeśli cykli zostaną pominięte jakiegokolwiek powodu, ten wyzwalacz przetwarza te pominięte cykle. Na przykład podczas synchronizowania danych między bazą danych i magazynu kopii zapasowych, użyj wyzwalacza przesuwanego okna, dzięki czemu dane są pobierane synchronizowane bez powodowania przerw. Aby uzyskać więcej informacji o wbudowanych wyzwalaczach harmonogramu i akcji, zobacz [harmonogramu i wykonywania cyklicznych automatycznych, zadań i przepływów pracy za pomocą usługi Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Poniżej przedstawiono niektóre wzorce, które ten wyzwalacz obsługuje:

* Natychmiastowe uruchomienie i Powtórz co *n* liczbę sekund, minuty lub godziny.

* Rozpoczynają się od określonej daty i godziny, a następnie uruchom i Powtórz co *n* liczbę sekund, minuty lub godziny. Za pomocą tego wyzwalacza można określić godzinę rozpoczęcia w przeszłości i uruchamia wszystkie wcześniejsze cykli.

* Opóźnienie każdy cykl o określony czas, przed uruchomieniem.

Różnice między tego wyzwalacza i wyzwalacza lub więcej informacji o planowaniu cyklicznego przepływów pracy, zobacz [harmonogramu i cyklu wykonywania zautomatyzowanego zadania, procesów i przepływów pracy za pomocą usługi Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Aby wyzwalacz aplikacji logiki, a następnie uruchomić tylko raz w przyszłości, zobacz [wykonywania zadania tylko jeden raz](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz to zrobić [Załóż bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

* Podstawową wiedzę na temat o [aplikacje logiki](../logic-apps/logic-apps-overview.md). Dowiedz się, jeśli dopiero zaczynasz pracę z usługi logic apps, [jak utworzyć swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Dodaj wyzwalacz przesuwanego okna

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Tworzenia pustej aplikacji logiki.

1. Gdy zostanie wyświetlony Projektant aplikacji logiki, w polu wyszukiwania wprowadź "przesuwającego się okna" jako filtr. Z listy wyzwalaczy wybierz wyzwalacz jako pierwszy krok w przepływie pracy aplikacji logiki: **Przesuwającego się okna**

   ![Wybieranie wyzwalacza "Przedłużanie okno"](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Ustaw odstęp czasu i częstotliwość cyklu. W tym przykładzie ustaw właściwości tak, aby uruchomić przepływ pracy, co tydzień.

   ![Ustaw interwał i częstotliwość](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Właściwość | Wymagane | Nazwa JSON | Type | Opis |
   |----------|----------|-----------|------|-------------|
   | **Interval** | Yes | interval | Integer | Dodatnia liczba całkowita, która opisuje, jak często przepływu pracy jest uruchamiana na podstawie częstotliwości. Poniżej przedstawiono minimalne i maksymalne odstępach czasu: <p>-Godzinny: 1-12 000 godzin </br>-Minutowy: 1 72,000 min </br>-Sekundowych: 1 9,999,999 sekundy<p>Na przykład jeśli interwał wynosi 6 i częstotliwość wynosi "Hour", cykl jest co 6 godzin. |
   | **Częstotliwość** | Yes | frequency | String | Jednostka czasu cyklu: **Drugi**, **minutę**, lub **godzinę** |
   ||||||

   ![Zaawansowane opcje cyklu](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Aby wyświetlić więcej opcji cyklu Otwórz **dodano nowy parametr** listy. 
   Wszystkie opcje, które można wybierać są wyświetlane w przypadku wyzwalacza po zaznaczeniu.

   | Właściwość | Wymagane | Nazwa JSON | Type | Opis |
   |----------|----------|-----------|------|-------------|
   | **Delay** | Nie | delay | String | Czas trwania opóźnienia, za pomocą każdego cyklu [specyfikacji czasu daty ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Strefa czasowa** | Nie | timeZone | String | Ma zastosowanie tylko po określeniu godziny rozpoczęcia, ponieważ ten wyzwalacz nie zaakceptuje [przesunięcie czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Wybierz strefę czasową, który chcesz zastosować. |
   | **Godzina rozpoczęcia** | Nie | startTime | String | Podaj datę i godzinę rozpoczęcia w następującym formacie: <p>RRRR-MM-Ddtgg w przypadku wybrania strefy czasowej <p>—lub— <p>RRRR-MM-Ddtgg, jeśli nie zaznaczysz strefy czasowej <p>Na przykład jeśli chcesz 18 września 2017 r. o 14:00, następnie określ "2017-09-18T14:00:00" i wybierz strefę czasową, takich jak standardowy czas pacyficzny. Alternatywnie można wskazać "2017-09-18T14:00:00Z" bez strefy czasowej. <p>**Uwaga:** Ten czas rozpoczęcia musi stosować [specyfikacji czasu daty ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [format daty i godziny UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [przesunięcie czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Jeśli nie zaznaczysz strefy czasowej, należy dodać litera "Z" na końcu bez żadnych spacji. Ta "Z" odnosi się do równowartości [morskich czasu](https://en.wikipedia.org/wiki/Nautical_time). <p>W przypadku prostych harmonogramów czas rozpoczęcia przypada po pierwszym wystąpieniu, natomiast w przypadku zaawansowanych cykli wyzwalacz nie zostanie wyzwolony wszelkie wcześniej niż czas rozpoczęcia. [*Jakie są sposoby, czy można używać daty rozpoczęcia i godzinę?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Teraz można tworzyć pozostałe przepływu pracy z innymi akcjami. Aby uzyskać więcej akcji, które można dodać, zobacz [łączniki dla usługi Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Definicja przepływu pracy — przesuwanego okna

W podstawowej definicji przepływu pracy aplikacji logiki, który używa formatu JSON, możesz wyświetlić przesuwanego okna definicji wyzwalacza z opcjami, które zostały wybrane. Aby wyświetlić tę definicję na pasku narzędzi projektanta, wybierz **widok kodu**. Aby powrócić do projektanta, wybierz na pasku narzędzi Projektanta **projektanta**.

Ten przykład pokazuje, w definicji wyzwalacza przesuwanego okna może wyglądać w podstawowej definicji przepływu pracy gdzie opóźnienie podczas każdego cyklu jest pięć sekund, co godzinę cyklu:

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>Kolejne kroki

* [Opóźnienie następnej akcji w przepływach pracy](../connectors/connectors-native-delay.md)
* [Łączniki dla usługi Logic Apps](../connectors/apis-list.md)