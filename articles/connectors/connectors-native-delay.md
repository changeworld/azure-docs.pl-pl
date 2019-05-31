---
title: Opóźnienie następnej akcji w przepływach pracy — Azure Logic Apps
description: Uruchamianie następnego działania w przepływach pracy aplikacji logiki przy użyciu akcji opóźnienia lub opóźnienie do momentu, w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
tags: connectors
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 27475fb3f086dbc5166a473e9d657d2dab723938
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297608"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Opóźnienie uruchamiania następnej akcji w usłudze Azure Logic Apps

Aby czekać ilość czasu przed uruchomieniem następnej akcji aplikacji logiki, można dodać wbudowane **opóźnienie — Planowanie** akcji przed akcji w przepływie pracy aplikacji logiki. Lub można dodać wbudowane **opóźnienie do — Planowanie** akcji czekać aż do określonej daty i godziny przed uruchomieniem następnego działania. Aby uzyskać więcej informacji na temat wbudowanych planowanie akcji i wyzwalaczy, zobacz [harmonogramu i wykonywania cyklicznych automatycznych, zadań i przepływów pracy za pomocą usługi Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **Opóźnienie**: Poczekaj, aż określoną liczbę jednostek czasu, takich jak sekundy, minuty, godziny, dni, tygodni lub miesięcy, przed uruchomieniem następnego działania.

* **Opóźnienie do**: Poczekaj, aż do określonej daty i godziny, przed uruchomieniem następnego działania.

Poniżej przedstawiono niektóre sposoby przykład za pomocą tych akcji:

* Poczekaj, aż weekday do wysyłania aktualizacji stanu za pośrednictwem poczty e-mail.

* Opóźnienie przepływu pracy, dopóki nie zakończy się przed wznowieniem i pobierania danych wywołania HTTP.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz to zrobić [Załóż bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

* Podstawową wiedzę na temat o [aplikacje logiki](../logic-apps/logic-apps-overview.md). Zanim użyjesz akcji, najpierw uruchomić aplikację logiki z wyzwalaczem. Możesz użyć dowolnego wyzwalacza i Dodaj inne akcje, przed dodaniem akcji opóźniania. Ten temat używa wyzwalacza usługi Office 365 Outlook. Dowiedz się, jeśli dopiero zaczynasz pracę z usługi logic apps, [jak utworzyć swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Dodawanie akcji opóźnienia

1. W Projektancie aplikacji logiki w ramach kroku, w której chcesz dodać akcji opóźniania wybierz **nowy krok**.

   Aby dodać akcję opóźnienie między krokami, przesuń wskaźnik nad strzałkę, która łączy się z instrukcjami. Wybierz znak plus (+), który pojawia się, a następnie wybierz **Dodaj akcję**.

1. W polu wyszukiwania wprowadź "opóźnienie" jako filtr. Z listy akcji wybierz następującą akcję: **Delay**

   ![Dodawanie akcji "Opóźnienie"](./media/connectors-native-delay/add-delay-action.png)

1. Określ ilość czasu oczekiwania przed następnym uruchomieniu akcji.

   ![Ustaw czas opóźnienia](./media/connectors-native-delay/delay-time-intervals.png)

   | Właściwość | Nazwa JSON | Wymagane | Typ | Opis |
   |----------|-----------|----------|------|-------------|
   | Count | count | Yes | Integer | Liczba jednostek czasu opóźnienia |
   | Jednostka | Jednostki | Tak | String | Jednostka czasu, na przykład: `Second`, `Minute`, `Hour`, `Day`, `Week`, lub `Month` |
   ||||||

1. Dodaj inne czynności, które chcesz uruchomić w przepływie pracy.

1. Gdy skończysz, Zapisz aplikację logiki.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>Dodawanie opóźnienia — aż do akcji

1. W Projektancie aplikacji logiki w ramach kroku, w której chcesz dodać akcji opóźniania wybierz **nowy krok**.

   Aby dodać akcję opóźnienie między krokami, przesuń wskaźnik nad strzałkę, która łączy się z instrukcjami. Wybierz znak plus (+), który pojawia się, a następnie wybierz **Dodaj akcję**.

1. W polu wyszukiwania wprowadź "opóźnienie" jako filtr. Z listy akcji wybierz następującą akcję: **Opóźnienie do**

   ![Dodawanie akcji "Opóźnienie do"](./media/connectors-native-delay/add-delay-until-action.png)

1. Podaj Data i godzina zakończenia, gdy chcesz wznowić przepływ pracy.

   ![Określ znacznik czasu: kiedy opóźnienie zakończenia](./media/connectors-native-delay/delay-until-timestamp.png)

   | Właściwość | Nazwa JSON | Wymagane | Typ | Opis |
   |----------|-----------|----------|------|-------------|
   | Timestamp | timestamp | Yes | String | Data i godzina zakończenia dla wznawianie przepływu pracy, używając następującego formatu: <p>RRRR-MM-Ddtgg <p>Na przykład jeśli chcesz 18 września 2017 r. o 14:00, określ "2017-09-18T14:00:00Z". <p>**Uwaga:** Wykonaj to format czasu [specyfikacji czasu daty ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [format daty i godziny UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [przesunięcie czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Bez strefę czasową należy dodać litera "Z" na końcu bez żadnych spacji. Ta "Z" odnosi się do równowartości [morskich czasu](https://en.wikipedia.org/wiki/Nautical_time). |
   ||||||

1. Dodaj inne czynności, które chcesz uruchomić w przepływie pracy.

1. Gdy skończysz, Zapisz aplikację logiki.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie, planowanie i uruchamianie zadań cyklicznych i przepływów pracy za pomocą wyzwalacza](../connectors/connectors-native-recurrence.md)
* [Łączniki dla usługi Logic Apps](../connectors/apis-list.md)