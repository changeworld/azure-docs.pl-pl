---
title: Planowanie zadań do obsługi ciągłych danych
description: Tworzenie i uruchamianie zadań cyklicznych obsługujących ciągłe dane przy użyciu okien przesuwnych w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: ab4bf802772c95d8c48a8cdba48def05e8a2761b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74786915"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Planowanie i uruchamianie zadań dla ciągłych danych przy użyciu wyzwalacza okna przesuwnego w usłudze Azure Logic Apps

Aby regularnie uruchamiać zadania, procesy lub zadania, które muszą obsługiwać dane w ciągłych fragmentach, można uruchomić przepływ pracy aplikacji logiki za pomocą wyzwalacza **okna przesuwnego.** Można ustawić datę i godzinę, a także strefę czasową uruchamiania przepływu pracy i cyklu powtarzania tego przepływu pracy. Jeśli cykle są pominięte z jakiegokolwiek powodu, ten wyzwalacz przetwarza te nieodebrane cykle. Na przykład podczas synchronizowania danych między bazą danych a magazynem kopii zapasowych należy użyć wyzwalacza okna przesuwnego, aby dane były synchronizowane bez ponoszenia przerw. Aby uzyskać więcej informacji na temat wbudowanych wyzwalaczy i akcji harmonogramu, zobacz [Planowanie i uruchamianie cyklicznych zautomatyzowanych, zadań i przepływów pracy za pomocą usługi Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Oto kilka wzorców, które obsługuje ten wyzwalacz:

* Uruchom natychmiast i powtórz każdą *n* liczbę sekund, minut lub godzin.

* Rozpocznij od określonej daty i godziny, a następnie uruchom i powtórz co *n* liczbę sekund, minut lub godzin. Za pomocą tego wyzwalacza można określić czas rozpoczęcia w przeszłości, który uruchamia wszystkie wcześniejsze cykle.

* Przed uruchomieniem należy opóźnić każdy cykl na określony czas.

Aby uzyskać różnice między tym wyzwalaczem a wyzwalaczem cyklu lub uzyskać więcej informacji na temat planowania cyklicznych przepływów pracy, zobacz [Planowanie i uruchamianie cyklicznych zautomatyzowanych zadań, procesów i przepływów pracy za pomocą aplikacji Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Jeśli chcesz wyzwolić aplikację logiki i uruchomić tylko jeden raz w przyszłości, zobacz [Uruchamianie zadań tylko jeden raz](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [założyć bezpłatne konto platformy Azure.](https://azure.microsoft.com/free/)

* Podstawowa wiedza na temat [aplikacji logicznych](../logic-apps/logic-apps-overview.md). Jeśli jesteś nowy w aplikacjach logiki, dowiedz się, [jak utworzyć pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Dodaj wyzwalacz okna przesuwnego

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Tworzenia pustej aplikacji logiki.

1. Po wyświetleniu projektanta aplikacji logiki w polu wyszukiwania wprowadź "okno przesuwne" jako filtr. Z listy wyzwalaczy wybierz ten wyzwalacz jako pierwszy krok w przepływie pracy aplikacji logiki: **Okno przesuwne**

   ![Wybierz wyzwalacz "Okno przesuwne"](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Ustaw odstęp czasu i częstotliwość cyklu. W tym przykładzie ustaw te właściwości, aby uruchamiać przepływ pracy co tydzień.

   ![Ustawianie interwału i częstotliwości](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Właściwość | Wymagany | Nazwa JSON | Typ | Opis |
   |----------|----------|-----------|------|-------------|
   | **Interwał** | Tak | interval | Liczba całkowita | Dodatnia liczba całkowita, która opisuje, jak często przepływ pracy jest uruchamiany na podstawie częstotliwości. Oto minimalne i maksymalne odstępy czasu: <p>- Godzina: 1-12 000 godzin </br>- Minuta: 1-72 000 minut </br>- Drugi: 1-9,999,999 sekundy<p>Na przykład, jeśli interwał wynosi 6, a częstotliwość to "Godzina", cykl jest co 6 godzin. |
   | **Częstotliwości** | Tak | frequency | Ciąg | Jednostka czasu dla cyklu: **Drugi,** **Minuta**lub **Godzina** |
   ||||||

   ![Zaawansowane opcje cyklu](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Aby uzyskać więcej opcji cyklu, otwórz listę **Dodaj nowy parametr.** 
   Wszystkie wybrane opcje pojawiają się na wyzwalaczu po zaznaczeniu.

   | Właściwość | Wymagany | Nazwa JSON | Typ | Opis |
   |----------|----------|-----------|------|-------------|
   | **Opóźnienie** | Nie | Opóźnienie | Ciąg | Czas trwania opóźnienia każdego cyklu przy użyciu [specyfikacji daty iso 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Strefa czasowa** | Nie | timeZone | Ciąg | Ma zastosowanie tylko wtedy, gdy określisz godzinę rozpoczęcia, ponieważ ten wyzwalacz nie akceptuje [przesunięcia UTC](https://en.wikipedia.org/wiki/UTC_offset). Wybierz strefę czasową, którą chcesz zastosować. |
   | **Godzina rozpoczęcia** | Nie | startTime | Ciąg | Podaj datę i godzinę rozpoczęcia w tym formacie: <p>YYYY-MM-DDThh:mm:ss po wybraniu strefy czasowej <p>— lub — <p>YYYY-MM-DDThh:mm:ssZ jeśli nie wybierzesz strefy czasowej <p>Jeśli więc chcesz, aby 18 września 2017 r. o godzinie 14:00, a następnie określić "2017-09-18T14:00:00" i wybrać strefę czasową, taką jak Pacyfik czas standardowy. Możesz też określić "2017-09-18T14:00:00Z" bez strefy czasowej. <p>**Uwaga:** Ta godzina rozpoczęcia musi być zgodna ze [specyfikacją daty iso 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [formacie daty daty UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [przesunięcia UTC](https://en.wikipedia.org/wiki/UTC_offset). Jeśli nie wybierzesz strefy czasowej, musisz dodać literę "Z" na końcu bez spacji. To "Z" odnosi się do równoważnego [czasu żeglarskiego](https://en.wikipedia.org/wiki/Nautical_time). <p>W przypadku prostych harmonogramów czas rozpoczęcia jest pierwszym wystąpieniem, podczas gdy w przypadku zaawansowanych cykli wyzwalacz nie uruchamia się wcześniej niż godzina rozpoczęcia. [*Jakie są sposoby używania daty i godziny rozpoczęcia?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Teraz skompiluj pozostały przepływ pracy za pomocą innych akcji. Aby uzyskać więcej akcji, które można dodać, zobacz [Łączniki dla usługi Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Definicja przepływu pracy — okno przesuwne

W podstawowej definicji przepływu pracy aplikacji logiki, która używa JSON, można wyświetlić definicję wyzwalacza okna przesuwnego z wybranymi opcjami. Aby wyświetlić tę definicję, na pasku narzędzi projektanta wybierz pozycję **Widok kod**. Aby powrócić do projektanta, wybierz na pasku narzędzi **projektanta, Projektant**.

W tym przykładzie pokazano, jak definicja wyzwalacza okna przesuwnego może wyglądać w podstawowej definicji przepływu pracy, gdzie opóźnienie dla każdego cyklu wynosi pięć sekund dla cyklu godzinowego:

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

## <a name="next-steps"></a>Następne kroki

* [Opóźnianie następnej akcji w przepływach pracy](../connectors/connectors-native-delay.md)
* [Łączniki dla usługi Logic Apps](../connectors/apis-list.md)