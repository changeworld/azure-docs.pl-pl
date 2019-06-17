---
title: Planowanie zadań cyklicznych za pomocą wyzwalacza — Azure Logic Apps
description: Planowanie i uruchamianie automatycznych powtarzających się zadań i przepływów pracy za pomocą wyzwalacza w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: f5fc778ee4d8f91232bc732cc276f642f748b29d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66297530"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Tworzenie, planowanie i uruchamianie zadań cyklicznych i przepływów pracy za pomocą wyzwalacza w usłudze Azure Logic Apps

Regularnie systemem określonego harmonogramu zadań, procesy lub zadań, można uruchomić przepływu pracy aplikacji logiki za pomocą wbudowanych **cykl - harmonogram** wyzwalacza. Można ustawić daty i godziny, a także strefę czasową dla uruchamiania przepływu pracy oraz cyklu dla powtarzających się ten przepływ pracy. Jeśli cykli zostaną pominięte jakiegokolwiek powodu, wyzwalacz będzie się powtarzać cykliczne w następnym zaplanowanym interwale. Aby uzyskać więcej informacji o wbudowanych wyzwalaczach harmonogramu i akcji, zobacz [harmonogramu i wykonywania cyklicznych automatycznych, zadań i przepływów pracy za pomocą usługi Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Poniżej przedstawiono niektóre wzorce, które ten wyzwalacz obsługuje wraz z powtórzeniami bardziej zaawansowanych i złożonych harmonogramów:

* Natychmiastowe uruchomienie i Powtórz co *n* liczbę sekund, minuty, godziny, dni, tygodnie lub miesiące.

* Rozpoczynają się od określonej daty i godziny, a następnie uruchom i Powtórz co *n* liczbę sekund, minuty, godziny, dni, tygodnie lub miesiące.

* Uruchom i powtarzania na jeden lub więcej razy każdego dnia, na przykład 8:00 do 17:00:00.

* Uruchom i Powtórz co tydzień, ale tylko dla określonych dni, takich jak soboty i niedziele.

* Uruchom i Powtórz co tydzień, ale tylko dla określonych dni i godziny, np. od poniedziałku do piątku w 8:00 do 17:00:00.

Różnice między tego wyzwalacza i wyzwalacza przesuwanego okna lub więcej informacji o planowaniu cyklicznego przepływów pracy, zobacz [harmonogramu i cyklu wykonywania zautomatyzowanego zadania, procesów i przepływów pracy za pomocą usługi Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Aby wyzwalacz aplikacji logiki, a następnie uruchomić tylko raz w przyszłości, zobacz [wykonywania zadania tylko jeden raz](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz to zrobić [Załóż bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

* Podstawową wiedzę na temat o [aplikacje logiki](../logic-apps/logic-apps-overview.md). Dowiedz się, jeśli dopiero zaczynasz pracę z usługi logic apps, [jak utworzyć swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Dodaj wyzwalacz cykliczny

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Tworzenia pustej aplikacji logiki.

1. Gdy zostanie wyświetlony Projektant aplikacji logiki, w polu wyszukiwania wprowadź ciąg "cyklicznie", jako filtr. Z listy wyzwalaczy wybierz wyzwalacz jako pierwszy krok w przepływie pracy aplikacji logiki: **Cykl**

   ![Wybieranie wyzwalacza "Cykl"](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Ustaw odstęp czasu i częstotliwość cyklu. W tym przykładzie ustaw właściwości tak, aby uruchomić przepływ pracy, co tydzień.

   ![Ustaw interwał i częstotliwość](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Właściwość | Wymagane | Nazwa JSON | Typ | Opis |
   |----------|----------|-----------|------|-------------|
   | **Interval** | Tak | interval | Integer | Dodatnia liczba całkowita, która opisuje, jak często przepływu pracy jest uruchamiana na podstawie częstotliwości. Poniżej przedstawiono minimalne i maksymalne odstępach czasu: <p>-Miesiąc: 1 – 16 miesięcy </br>-Dzień: 1 – 500 dni </br>-Godzinny: 1-12 000 godzin </br>-Minutowy: 1 72,000 min </br>-Sekundowych: 1 9,999,999 sekundy<p>Na przykład jeśli interwał wynosi 6 i częstotliwość wynosi "Month", cykl jest co 6 miesięcy. |
   | **Częstotliwość** | Yes | frequency | String | Jednostka czasu cyklu: **Drugi**, **minutę**, **godzinę**, **dzień**, **tydzień**, lub **miesiąca** |
   ||||||

   Aby uzyskać więcej opcji planowania, otwórz **dodano nowy parametr** listy. 
   Wszystkie opcje, które można wybierać są wyświetlane w przypadku wyzwalacza po zaznaczeniu.

   ![Zaawansowane opcje planowania](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Właściwość | Wymagane | Nazwa JSON | Typ | Opis |
   |----------|----------|-----------|------|-------------|
   | **Strefa czasowa** | Nie | timeZone | String | Ma zastosowanie tylko po określeniu godziny rozpoczęcia, ponieważ ten wyzwalacz nie zaakceptuje [przesunięcie czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Wybierz strefę czasową, który chcesz zastosować. |
   | **Godzina rozpoczęcia** | Nie | startTime | String | Podaj datę i godzinę rozpoczęcia w następującym formacie: <p>RRRR-MM-Ddtgg w przypadku wybrania strefy czasowej <p>—lub— <p>RRRR-MM-Ddtgg, jeśli nie zaznaczysz strefy czasowej <p>Na przykład jeśli chcesz 18 września 2017 r. o 14:00, następnie określ "2017-09-18T14:00:00" i wybierz strefę czasową, takich jak standardowy czas pacyficzny. Alternatywnie można wskazać "2017-09-18T14:00:00Z" bez strefy czasowej. <p>**Uwaga:** Ten czas rozpoczęcia musi stosować [specyfikacji czasu daty ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [format daty i godziny UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [przesunięcie czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Jeśli nie zaznaczysz strefy czasowej, należy dodać litera "Z" na końcu bez żadnych spacji. Ta "Z" odnosi się do równowartości [morskich czasu](https://en.wikipedia.org/wiki/Nautical_time). <p>W przypadku prostych harmonogramów czas rozpoczęcia przypada po pierwszym wystąpieniu, natomiast w przypadku harmonogramów złożonych wyzwalacz nie zostanie wyzwolony wszelkie wcześniej niż czas rozpoczęcia. [*Jakie są sposoby, czy można używać daty rozpoczęcia i godzinę?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **W tych dniach** | Nie | weekDays | Ciąg lub tablicę ciągów | Jeśli wybierzesz opcję "Week", można wybrać jeden lub więcej dni, gdy zachodzi potrzeba uruchomienia przepływu pracy: **Poniedziałek**, **wtorek**, **środę**, **czwartek**, **piątek**, **sobota**, i **Niedziela** |
   | **W tych godzinach** | Nie | hours | Liczba całkowita lub tablicy liczb całkowitych | Jeśli wybierzesz opcję "Day" lub "Week", można wybrać jeden lub więcej liczb całkowitych z zakresu od 0 do 23 godzin dnia, gdy chcesz uruchomić przepływ pracy. <p><p>Na przykład jeśli określisz "10", "12" i "14", możesz korzystać z 10 AM, 12 PM i 14: 00, godziny, dnia, ale są obliczane na podstawie których minutach dnia, podczas ponownego uruchamiania. Aby ustawić minutach dnia, określ wartość **w tych minutach** właściwości. |
   | **W tych minutach** | Nie | minutes | Liczba całkowita lub tablicy liczb całkowitych | Jeśli wybierzesz opcję "Day" lub "Week", można wybrać jeden lub więcej liczb całkowitych z zakresu od 0 do 59 jako minuty, godziny, kiedy chcesz uruchomić przepływ pracy. <p>Na przykład "30" można określić jako znacznik minutę i godziny, dnia, korzystając z poprzedniego przykładu, możesz uzyskać 10:30:00, 12:30:00 i 14:30:00. |
   |||||

   Na przykład załóżmy, że dziś jest poniedziałek, 4 września 2017 r. Następujący wyzwalacz cykliczny nie zostanie wyzwolony *wszelkie wcześniej* niż data rozpoczęcia i godzina, czyli każdy poniedziałek, 18 września 2017 r. o 8:00 czasu PST. Jednak harmonogram cyklu jest ustawiony na 10:30:00, 12:30 PM, a 2:30 PM tylko poniedziałek. Aby po raz pierwszy, aktywuje się i tworzy wystąpienie przepływu pracy aplikacji logiki wynosi 10:30:00. Aby dowiedzieć się więcej na temat sposobu uruchamiania godziny pracy, zobacz te [start przykłady czasu](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Przyszłych uruchomień odbywa się na 12:30:00 i 14:30:00 w dniu. Każdy cykl tworzy własne wystąpienie przepływu pracy. Po tym całego harmonogramu powtarza się przez ponownie poniedziałku. [*Jakie są inne wystąpienia przykładu?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Zaawansowany przykład planowania](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Wyzwalacz zawiera cykl określony w wersji zapoznawczej tylko w przypadku wybrania częstotliwości "Day" lub "Week".

1. Teraz można tworzyć pozostałe przepływu pracy z innymi akcjami. Aby uzyskać więcej akcji, które można dodać, zobacz [łączniki dla usługi Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Definicja przepływu pracy — cyklu

W podstawowej definicji przepływu pracy aplikacji logiki, który używa formatu JSON, możesz wyświetlić [definicji wyzwalacza cyklu](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) z opcjami, które zostały wybrane. Aby wyświetlić tę definicję na pasku narzędzi projektanta, wybierz **widok kodu**. Aby powrócić do projektanta, wybierz na pasku narzędzi Projektanta **projektanta**.

Ten przykład pokazuje, jak definicja wyzwalacza cyklu może wyglądać w podstawowej definicji przepływu pracy:

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>Kolejne kroki

* [Wstrzymywanie przepływów pracy akcji opóźnienia](../connectors/connectors-native-delay.md)
* [Łączniki dla usługi Logic Apps](../connectors/apis-list.md)
