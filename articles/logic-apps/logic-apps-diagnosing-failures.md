---
title: Rozwiązywanie problemów i diagnozowanie błędów — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywanie problemów i diagnozowanie błędów przepływu pracy w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.topic: article
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.date: 10/15/2017
ms.openlocfilehash: 62a74364939fffb6e06f51f1c0cabb6cce8c10e1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60999810"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Rozwiązywanie problemów i diagnozowanie błędów przepływu pracy w usłudze Azure Logic Apps

Twoja aplikacja logiki generuje informacje, które mogą ułatwić diagnozowanie i debugowanie problemów w aplikacji. Przeglądając każdy krok w przepływie pracy za pośrednictwem witryny Azure portal, można zdiagnozować aplikację logiki. Lub można dodać kilka kroków do przepływu pracy dla debugowania w czasie wykonywania.

## <a name="review-trigger-history"></a>Przeglądanie historii wyzwalacza

Każda aplikacja logiki rozpoczyna się od wyzwalacza. Jeśli wyzwalacz nie zostanie wyzwolony, sprawdź najpierw historii wyzwalacza. Ta historia Wyświetla wszystkie próby wyzwalacza, wprowadzone w aplikacji logiki i szczegółowe informacje o dane wejściowe i wyjściowe dla każdej próby wyzwalacza.

1. Aby sprawdzić, czy wyzwalacz zadziałał w menu aplikacji logiki, wybierz **Przegląd**. W obszarze **historii wyzwalania**, sprawdź stan wyzwalacza.

   > [!TIP]
   > Jeśli nie widzisz menu aplikacji logiki, spróbuj wrócić do pulpitu nawigacyjnego platformy Azure i ponownie otworzyć aplikację logiki.

   ![Przeglądanie historii wyzwalacza](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Jeśli nie widzisz danych, których oczekujesz, spróbuj wybrać **Odśwież** na pasku narzędzi.
   > * Jeśli lista zawiera wiele wyzwolić prób i nie można odnaleźć wpisu, który ma, spróbuj użyć filtru, na liście.

   Poniżej przedstawiono możliwe stany próba wyzwalacza:

   | Stan | Opis | 
   | ------ | ----------- | 
   | **Powodzenie** | Wyzwalacz zaznaczone punkt końcowy, a znaleziono dostępnych danych. Zazwyczaj stan "Fired" pojawia się również wraz z tego stanu. Jeśli nie, definicji wyzwalacza może mieć warunku lub `SplitOn` polecenia, który nie został spełniony. <p>Ten stan może dotyczyć wyzwalacza ręcznego, wyzwalacza lub wyzwalacza sondowania. Pomyślnie uruchomić wyzwalacz, ale akcje generować błędy nieobsługiwany nadal może spowodować błąd samego przebiegu. | 
   | **Pominięto** | Wyzwalacz zaznaczone punkt końcowy, ale można znaleźć żadnych danych. | 
   | **Niepowodzenie** | Wystąpił błąd. Aby przejrzeć komunikaty o błędach wygenerowane dla wyzwalacza nie powiodło się, zaznaczyć taka próba wyzwalacza, a następnie wybierz **dane wyjściowe**. Na przykład może się okazać dane wejściowe, które nie są prawidłowe. | 
   ||| 

   Może mieć wiele wpisów wyzwalacza z tę samą datę i czas, który się dzieje, gdy Twoja aplikacja logiki znajdzie wiele elementów. 
   Każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki, aby uruchomić przepływ pracy. Domyślnie każde wystąpienie jest uruchamiane równolegle, aby nie przepływ pracy ma czekać przed rozpoczęciem przebiegu.

   > [!TIP]
   > Zostanie sprawdzona wyzwalacz, bez czekania na następne wystąpienie. Na pasku narzędzi Przegląd wybierz **wyzwalacz przebiegu**i wybierz wyzwalacz, który wymusza to sprawdzenie. Lub wybierz **Uruchom** na pasku narzędzi Projektanta aplikacji logiki.

3. Aby zbadać szczegóły próbę wyzwalacza, w obszarze **historii wyzwalania**, wybierz taka próba wyzwalacza. 

   ![Wybierz próba wyzwalacza](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Przejrzyj wejść i wyjść wygenerowanych przez wyzwalacz. Dane wyjściowe wyzwalacza wyświetlić dane, które pochodzą z wyzwalacza. Te dane wyjściowe mogą pomóc w określeniu, czy wszystkie właściwości zwracane zgodnie z oczekiwaniami.

   > [!NOTE]
   > Jeśli okaże się zawartość, która nie rozumiesz, Dowiedz się, jak usługa Azure Logic Apps [obsługuje różne typy zawartości](../logic-apps/logic-apps-content-type.md).

   ![Dane wyjściowe wyzwalacza](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Przeglądać historię uruchamiania

Każdy aktywowany wyzwalacz jest uruchamiany przebiegu przepływu pracy. Możesz sprawdzić, co się stało podczas uruchomienia, stan każdego kroku w tym w przepływie pracy, oraz dane wejściowe i wyjściowe dla każdego kroku.

1. Z menu aplikacji logiki wybierz pozycję **Przegląd**. W obszarze **Historia przebiegów**, przejrzyj uruchamia aktywowany wyzwalacz.

   > [!TIP]
   > Jeśli nie widzisz menu aplikacji logiki, spróbuj wrócić do pulpitu nawigacyjnego platformy Azure i ponownie otworzyć aplikację logiki.

   ![Historia przebiegów przeglądu](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Jeśli nie widzisz danych, których oczekujesz, spróbuj wybrać **Odśwież** na pasku narzędzi.
   > * Jeśli lista zawiera wiele przebiegów i nie można odnaleźć wpisu, który ma, spróbuj użyć filtru, na liście.

   Poniżej przedstawiono możliwe stany uruchomienia:

   | Stan | Opis | 
   | ------ | ----------- | 
   | **Powodzenie** | Wszystkie akcje zakończyło się pomyślnie. <p>Jeśli zakończą się niepowodzeniem w określonej akcji, przeprowadź następujące działania w przepływie pracy obsługiwane tego błędu. | 
   | **Niepowodzenie** | Co najmniej jedna akcja nie powiodła się, a nie późniejsze akcje w przepływie pracy były ustawić, aby obsłużyć błąd. | 
   | **Anulowane** | Przepływ pracy był uruchomiony, ale Odebrano żądanie anulowania. | 
   | **Uruchomiono** | Przepływ pracy jest obecnie uruchomiony. <p>Ten stan może się zdarzyć, ograniczone przepływów pracy lub z powodu bieżącego planu cenowego. Aby uzyskać więcej informacji, zobacz [limity akcji na stronie cennika](https://azure.microsoft.com/pricing/details/logic-apps/). Jeśli skonfigurowano [rejestrowania diagnostycznego](../logic-apps/logic-apps-monitor-your-logic-apps.md), można także uzyskać informacje dotyczące ograniczania zdarzeń, które odbywa się. | 
   ||| 

2. Przejrzyj szczegóły dla każdego kroku w określonym przebiegu. W obszarze **Historia przebiegów**, wybierz polecenie Uruchom, który chcesz zbadać.

   ![Historia przebiegów przeglądu](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   Czy przebieg, sama zakończonych powodzeniem lub niepowodzeniem, widok szczegółów uruchomienia przedstawia każdego kroku, i czy zakończonych powodzeniem lub niepowodzeniem.

   ![Wyświetlanie szczegółów uruchomienia aplikacji logiki](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. Badanie danych wejściowych, dane wyjściowe i komunikaty o błędach dotyczących określonego etapu, wybrać ten krok co rozszerza się i pokazuje szczegóły kształtu. Na przykład:

   ![Wyświetlanie szczegółów kroku](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Wykonaj debugowania w czasie wykonywania

Aby pomóc w debugowaniu, można dodać diagnostycznych kroków do przepływu pracy, oraz przeglądanie wyzwalacza i Historia przebiegów. Na przykład można dodać kroki, które używają [Tester elementu Webhook](https://webhook.site/) usługi, dzięki czemu mogą sprawdzić żądań HTTP i określić ich określić dokładny rozmiar, kształt i format.

1. Odwiedź stronę [Tester elementu Webhook](https://webhook.site/) i skopiuj unikatowy adres URL utworzony

2. W aplikacji logiki Dodaj akcję POST protokołu HTTP, za pomocą treść, która ma zostać przetestowana, na przykład, wyrażenie lub innym kroku danych wyjściowych.

3. Wklej adres URL dla usługi elementu Webhook Tester akcję POST protokołu HTTP.

4. Aby sprawdzić, jak żądania został utworzony podczas generowania ze aparat usługi Logic Apps, uruchom aplikację logiki, a następnie Zobacz Tester elementu Webhook, aby uzyskać szczegółowe informacje.

## <a name="next-steps"></a>Kolejne kroki

[Monitorowanie aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md)
