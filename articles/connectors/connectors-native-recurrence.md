---
title: Planowanie zadań cyklicznych i przepływów pracy
description: Planowanie i uruchamianie cyklicznych zautomatyzowanych zadań i przepływów pracy za pomocą wyzwalacza cyklu w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: a9c167c5767a4156147e13a1e4ae21162e506474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445853"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Tworzenie, planowanie i uruchamianie cyklicznych zadań i przepływów pracy za pomocą wyzwalacza cyklu w usłudze Azure Logic Apps

Aby regularnie uruchamiać zadania, procesy lub zadania zgodnie z określonym harmonogramem, można uruchomić przepływ pracy aplikacji logiki za pomocą wbudowanego wyzwalacza **cyklu — harmonogram.** Można ustawić datę i godzinę, a także strefę czasową uruchamiania przepływu pracy i cyklu powtarzania tego przepływu pracy. Jeśli cykle zostaną pominięte z jakiegokolwiek powodu, ten wyzwalacz będzie kontynuowany w następnym zaplanowanym interwale. Aby uzyskać więcej informacji na temat wbudowanych wyzwalaczy i akcji harmonogramu, zobacz [Planowanie i uruchamianie cyklicznych zautomatyzowanych, zadań i przepływów pracy za pomocą usługi Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Oto kilka wzorców, które ten wyzwalacz obsługuje wraz z bardziej zaawansowanych cykli i złożonych harmonogramów:

* Uruchom natychmiast i powtórz każdą *n* liczbę sekund, minut, godzin, dni, tygodni lub miesięcy.

* Rozpocznij od określonej daty i godziny, a następnie uruchom i powtórz co *n* liczbę sekund, minut, godzin, dni, tygodni lub miesięcy.

* Uruchamiaj i powtarzaj co najmniej raz dziennie, na przykład o 8:00 i 17:00.

* Biegaj i powtarzaj co tydzień, ale tylko w określonych dniach, takich jak sobota i niedziela.

* Biegaj i powtarzaj co tydzień, ale tylko w określonych dniach i godzinach, takich jak od poniedziałku do piątku o 8:00 i 17:00.

Aby uzyskać różnice między tym wyzwalaczem a wyzwalaczem okna przesuwnego lub uzyskać więcej informacji na temat planowania cyklicznych przepływów pracy, zobacz [Planowanie i uruchamianie cyklicznych zautomatyzowanych zadań, procesów i przepływów pracy za pomocą aplikacji Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Jeśli chcesz wyzwolić aplikację logiki i uruchomić tylko jeden raz w przyszłości, zobacz [Uruchamianie zadań tylko jeden raz](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Podstawowa wiedza na temat [aplikacji logicznych](../logic-apps/logic-apps-overview.md). Jeśli jesteś nowy w aplikacjach logiki, dowiedz się, [jak utworzyć pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Dodaj wyzwalacz cyklu

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Tworzenia pustej aplikacji logiki.

1. Po wyświetlenie projektanta aplikacji logiki `recurrence` w polu wyszukiwania wprowadź jako filtr. Z listy wyzwalaczy wybierz ten wyzwalacz jako pierwszy krok w **przepływie** pracy aplikacji logiki: Cykl

   ![Wybierz wyzwalacz "Cykl"](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Ustaw odstęp czasu i częstotliwość cyklu. W tym przykładzie ustaw te właściwości, aby uruchamiać przepływ pracy co tydzień.

   ![Ustawianie interwału i częstotliwości](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Właściwość | Nazwa JSON | Wymagany | Typ | Opis |
   |----------|-----------|----------|------|-------------|
   | **Interwał** | `interval` | Tak | Liczba całkowita | Dodatnia liczba całkowita, która opisuje, jak często przepływ pracy jest uruchamiany na podstawie częstotliwości. Oto minimalne i maksymalne odstępy czasu: <p>- Miesiąc: 1-16 miesięcy </br>- Dzień: 1-500 dni </br>- Godzina: 1-12 000 godzin </br>- Minuta: 1-72 000 minut </br>- Drugi: 1-9,999,999 sekundy<p>Na przykład, jeśli interwał wynosi 6, a częstotliwość to "Miesiąc", cykl jest co 6 miesięcy. |
   | **Częstotliwości** | `frequency` | Tak | Ciąg | Jednostka czasu dla cyklu: **Drugi**, **Minuta,** **Godzina,** **Dzień,** **Tydzień**lub **Miesiąc** |
   ||||||

   > [!IMPORTANT]
   > Gdy cykle nie określają zaawansowanych opcji planowania, przyszłe cykle są oparte na ostatnim czasie wykonywania.
   > Godziny rozpoczęcia dla tych cykli może dryfować z powodu czynników, takich jak opóźnienie podczas wywołania magazynu. Aby upewnić się, że aplikacja logiki nie przegapi cyklu, zwłaszcza gdy częstotliwość jest w dniach lub dłużej, użyj jednej z następujących opcji:
   > 
   > * Podaj godzinę rozpoczęcia dla cyklu.
   > 
   > * Określ godziny i minuty, kiedy należy uruchomić cykl przy użyciu **w tych godzinach** i w tych **minutach** właściwości.
   > 
   > * Użyj [wyzwalacza okna przesuwnego](../connectors/connectors-native-sliding-window.md)zamiast wyzwalacza cyklu.

1. Aby ustawić zaawansowane opcje planowania, otwórz listę **Dodaj nowy parametr.** Wszystkie wybrane opcje pojawiają się na wyzwalaczu po zaznaczeniu.

   ![Zaawansowane opcje planowania](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Właściwość | Nazwa JSON | Wymagany | Typ | Opis |
   |----------|-----------|----------|------|-------------|
   | **Strefa czasowa** | `timeZone` | Nie | Ciąg | Ma zastosowanie tylko wtedy, gdy określisz godzinę rozpoczęcia, ponieważ ten wyzwalacz nie akceptuje [przesunięcia UTC](https://en.wikipedia.org/wiki/UTC_offset). Wybierz strefę czasową, którą chcesz zastosować. |
   | **Godzina rozpoczęcia** | `startTime` | Nie | Ciąg | Podaj datę i godzinę rozpoczęcia w tym formacie: <p>YYYY-MM-DDThh:mm:ss po wybraniu strefy czasowej <p>— lub — <p>YYYY-MM-DDThh:mm:ssZ jeśli nie wybierzesz strefy czasowej <p>Jeśli więc chcesz, aby 18 września 2017 r. o godzinie 14:00, a następnie określić "2017-09-18T14:00:00" i wybrać strefę czasową, taką jak Pacyfik czas standardowy. Możesz też określić "2017-09-18T14:00:00Z" bez strefy czasowej. <p>**Uwaga:** Ten czas rozpoczęcia ma maksymalnie 49 lat w przyszłości i musi być zgodny ze [specyfikacją iso 8601 data time](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [formacie daty UTC,](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)ale bez [przesunięcia UTC](https://en.wikipedia.org/wiki/UTC_offset). Jeśli nie wybierzesz strefy czasowej, musisz dodać literę "Z" na końcu bez spacji. To "Z" odnosi się do równoważnego [czasu żeglarskiego](https://en.wikipedia.org/wiki/Nautical_time). <p>W przypadku prostych harmonogramów czas rozpoczęcia jest pierwszym wystąpieniem, podczas gdy w przypadku złożonych harmonogramów wyzwalacz nie uruchamia się wcześniej niż godzina rozpoczęcia. [*Jakie są sposoby używania daty i godziny rozpoczęcia?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **W tych dniach** | `weekDays` | Nie | Tablica ciągów lub ciągów | Jeśli wybierzesz "Tydzień", możesz wybrać jeden lub więcej dni, w których chcesz uruchomić przepływ pracy: **poniedziałek,** **wtorek,** **środa,** **czwartek,** **piątek,** **sobota**i **niedziela** |
   | **W tych godzinach** | `hours` | Nie | Tablica całkowita lub całkowita | Jeśli wybierzesz "Dzień" lub "Tydzień", możesz wybrać jedną lub więcej liczby całkowitych od 0 do 23 jako godziny dnia, gdy chcesz uruchomić przepływ pracy. <p><p>Na przykład, jeśli określisz "10", "12" i "14", otrzymasz 10:00, 12:00 i 14:00 dla godzin dnia, ale minuty dnia są obliczane na podstawie momentu rozpoczęcia cyklu. Aby ustawić minuty dnia, należy określić wartość właściwości **W tych minutach.** |
   | **W tych minutach** | `minutes` | Nie | Tablica całkowita lub całkowita | Jeśli wybierzesz "Dzień" lub "Tydzień", możesz wybrać jedną lub więcej liczby całkowitych od 0 do 59 jako minuty godziny, gdy chcesz uruchomić przepływ pracy. <p>Na przykład można określić "30" jako znacznik minuty i używając poprzedniego przykładu dla godzin dnia, otrzymasz 10:30, 12:30 i 14:30. |
   |||||

   Załóżmy na przykład, że dzisiaj jest poniedziałek, 4 września 2017. Następujący wyzwalacz cyklu nie uruchamia się wcześniej niż data i *godzina* rozpoczęcia, czyli poniedziałek, 18 września 2017 r. o godzinie 8:00 CZASU PST. Jednak harmonogram cyklu jest ustawiony na 10:30, 12:30 i 14:30 tylko w poniedziałki. Więc po raz pierwszy wyzwalacz uruchamia i tworzy wystąpienie przepływu pracy aplikacji logiki jest o 10:30 AM. Aby dowiedzieć się więcej o tym, jak działają godziny rozpoczęcia, zobacz [te przykłady czasu rozpoczęcia](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Kolejne biegi odbywają się o 12:30 i 14:30 tego samego dnia. Każdy cykl tworzy własne wystąpienie przepływu pracy. Po tym, cały harmonogram powtarza się od nowa w najbliższy poniedziałek. [*Jakie są inne przykładowe zdarzenia?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Przykład planowania zaawansowanego](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Wyzwalacz pokazuje podgląd dla określonego cyklu tylko wtedy, gdy wybierzesz "Dzień" lub "Tydzień" jako częstotliwość.

1. Teraz skompiluj pozostały przepływ pracy za pomocą innych akcji. Aby uzyskać więcej akcji, które można dodać, zobacz [Łączniki dla usługi Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Definicja przepływu pracy — cykl

W podstawowej definicji przepływu pracy aplikacji logiki, która używa JSON, można wyświetlić [definicję wyzwalacza cyklu](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) z wybranymi opcjami. Aby wyświetlić tę definicję, na pasku narzędzi projektanta wybierz pozycję **Widok kod**. Aby powrócić do projektanta, wybierz na pasku narzędzi **projektanta, Projektant**.

W tym przykładzie pokazano, jak definicja wyzwalacza cyklu może wyglądać w podstawowej definicji przepływu pracy:

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

## <a name="next-steps"></a>Następne kroki

* [Wstrzymywanie przepływów pracy z akcjami opóźnienia](../connectors/connectors-native-delay.md)
* [Łączniki dla usługi Logic Apps](../connectors/apis-list.md)
