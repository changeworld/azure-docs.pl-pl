---
title: Opóźnianie następnej akcji w przepływach pracy
description: Poczekaj na uruchomienie następnej akcji w przepływach pracy aplikacji logiki przy użyciu akcji Opóźnienie lub Opóźnienie do momentu w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787340"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Opóźnianie uruchamiania następnej akcji w usłudze Azure Logic Apps

Aby aplikacja logiki czekać dużo czasu przed uruchomieniem następnej akcji, można dodać **wbudowane delay — Harmonogram** akcji przed działaniem w przepływie pracy aplikacji logiki. Lub można dodać wbudowane **opóźnienie, aż — Zaplanuj** akcję, aby poczekać do określonej daty i godziny przed uruchomieniem następnej akcji. Aby uzyskać więcej informacji na temat wbudowanych akcji i wyzwalaczy Harmonogram, zobacz [Planowanie i uruchamianie cyklicznych zautomatyzowanych, zadań i przepływów pracy za pomocą usługi Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **Opóźnienie:** Poczekaj na określoną liczbę jednostek czasu, takich jak sekundy, minuty, godziny, dni, tygodnie lub miesiące, przed rozpoczęciem następnej akcji.

* **Opóźnienie do**: Poczekaj do określonej daty i godziny przed rozpoczęciem następnej akcji.

Oto kilka przykładowych sposobów korzystania z tych akcji:

* Poczekaj do dnia tygodnia, aby wysłać aktualizację stanu za pośrednictwem poczty e-mail.

* Opóźnij przepływ pracy, dopóki połączenie HTTP nie zakończy się przed wznowieniem i pobraniem danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [założyć bezpłatne konto platformy Azure.](https://azure.microsoft.com/free/)

* Podstawowa wiedza na temat [aplikacji logicznych](../logic-apps/logic-apps-overview.md). Przed użyciem akcji aplikacja logiki musi najpierw rozpocząć się od wyzwalacza. Możesz użyć dowolnego wyzwalacza i dodać inne akcje przed dodaniem akcji opóźnienia. W tym temacie użyto wyzwalacza programu Outlook dla usługi Office 365. Jeśli jesteś nowy w aplikacjach logiki, dowiedz się, [jak utworzyć pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Dodawanie akcji Opóźnienie

1. W projektancie aplikacji logiki w kroku, w którym chcesz dodać akcję opóźnienia, wybierz pozycję **Nowy krok**.

   Aby dodać akcję opóźnienia między krokami, przesuń wskaźnik myszy na strzałkę łączącą kroki. Wybierz wyświetlony znak plus (+), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wpisz "opóźnienie" jako filtr. Z listy akcji wybierz tę akcję: **Opóźnienie**

   ![Dodaj akcję "Opóźnienie"](./media/connectors-native-delay/add-delay-action.png)

1. Określ czas oczekiwania przed rozpoczęciem następnej akcji.

   ![Ustawianie czasu opóźnienia](./media/connectors-native-delay/delay-time-intervals.png)

   | Właściwość | Nazwa JSON | Wymagany | Typ | Opis |
   |----------|-----------|----------|------|-------------|
   | Liczba | count | Tak | Liczba całkowita | Liczba jednostek czasu opóźnienia |
   | Jednostka | unit | Tak | Ciąg | Jednostka czasu, `Second`na przykład: `Minute` `Hour`, `Day` `Week`, , , , lub`Month` |
   ||||||

1. Dodaj inne akcje, które chcesz uruchomić w przepływie pracy.

1. Po zakończeniu zapisz aplikację logiki.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>Dodawanie akcji Opóźnienie do

1. W projektancie aplikacji logiki w kroku, w którym chcesz dodać akcję opóźnienia, wybierz pozycję **Nowy krok**.

   Aby dodać akcję opóźnienia między krokami, przesuń wskaźnik myszy na strzałkę łączącą kroki. Wybierz wyświetlony znak plus (+), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wpisz "opóźnienie" jako filtr. Z listy akcji wybierz tę akcję: **Opóźnij do**

   ![Dodaj akcję "Opóźnienie do"](./media/connectors-native-delay/add-delay-until-action.png)

1. Podaj datę i godzinę zakończenia, kiedy chcesz wznowić przepływ pracy.

   ![Określanie sygnatury czasowej dla czasu zakończenia opóźnienia](./media/connectors-native-delay/delay-until-timestamp.png)

   | Właściwość | Nazwa JSON | Wymagany | Typ | Opis |
   |----------|-----------|----------|------|-------------|
   | Znacznik czasu | sygnatura czasowa | Tak | Ciąg | Data zakończenia i godzina wznowienia przepływu pracy przy użyciu tego formatu: <p>YYYY-MM-DDThh:mm:ssZ <p>Jeśli więc chcesz, aby 18 września 2017 r. o godzinie 14:00 określ "2017-09-18T14:00:00Z". <p>**Uwaga:** Ten format czasu musi być zgodny [ze specyfikacją daty i godziny ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [formacie daty i czasu UTC,](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)ale bez [przesunięcia UTC](https://en.wikipedia.org/wiki/UTC_offset). Bez strefy czasowej należy dodać literę "Z" na końcu bez spacji. To "Z" odnosi się do równoważnego [czasu żeglarskiego](https://en.wikipedia.org/wiki/Nautical_time). |
   ||||||

1. Dodaj inne akcje, które chcesz uruchomić w przepływie pracy.

1. Po zakończeniu zapisz aplikację logiki.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie, planowanie i uruchamianie zadań cyklicznych i przepływów pracy za pomocą wyzwalacza cyklu](../connectors/connectors-native-recurrence.md)
* [Łączniki dla usługi Logic Apps](../connectors/apis-list.md)