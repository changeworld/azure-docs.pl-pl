---
title: Rozwiązywanie problemów i Diagnozowanie błędów przepływu pracy
description: Dowiedz się, jak rozwiązywać problemy, błędy i błędy w przepływach pracy w programie Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 79cc9d1bf7aa9e8848197525646b0a3646a558d2
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666809"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Rozwiązywanie problemów i Diagnozowanie błędów przepływu pracy w Azure Logic Apps

Aplikacja logiki generuje informacje, które mogą pomóc zdiagnozować i debugować problemy w aplikacji. Aplikację logiki można zdiagnozować, przeglądając każdy krok w przepływie pracy za pomocą Azure Portal. Można też dodać kilka kroków do przepływu pracy w celu debugowania środowiska uruchomieniowego.

## <a name="review-trigger-history"></a>Przejrzyj historię wyzwalacza

Każda aplikacja logiki rozpoczyna się od wyzwalacza. Jeśli wyzwalacz nie jest wyzwalany, należy najpierw sprawdzić historię wyzwalacza. Ta historia zawiera wszystkie próby uruchomienia przez aplikację logiki oraz szczegóły dotyczące wejść i wyjść dla każdej próby wyzwalacza.

1. Aby sprawdzić, czy wyzwalacz został wyzwolony, w menu aplikacji logiki wybierz pozycję **Przegląd**. W obszarze **historia wyzwalacza**Przejrzyj stan wyzwalacza.

   > [!TIP]
   > Jeśli nie widzisz menu aplikacji logiki, spróbuj wrócić do pulpitu nawigacyjnego platformy Azure i ponownie otworzyć aplikację logiki.

   ![Przejrzyj historię wyzwalacza](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Jeśli nie znajdziesz danych, których oczekujesz, spróbuj wybrać pozycję **Odśwież** na pasku narzędzi.
   > * Jeśli lista zawiera wiele prób wyzwalacza i nie można znaleźć żądanego wpisu, spróbuj przefiltrować listę.

   Oto możliwe stany próby uruchomienia wyzwalacza:

   | Stan | Opis | 
   | ------ | ----------- | 
   | **Powodzenie** | Wyzwalacz sprawdzał punkt końcowy i znalazł dostępne dane. Zwykle pojawia się również stan "uruchomił" obok tego stanu. W przeciwnym razie definicja wyzwalacza może mieć warunek lub `SplitOn` polecenie, które nie zostało spełnione. <p>Ten stan może dotyczyć wyzwalacza ręcznego, wyzwalacza cyklu lub wyzwalacza sondowania. Wyzwalacz może zostać uruchomiony pomyślnie, ale uruchomienie może zakończyć się niepowodzeniem, gdy akcje generują nieobsłużone błędy. | 
   | **Pominięto** | Wyzwalacz sprawdzał punkt końcowy, ale nie znalazł danych. | 
   | **Niepowodzenie** | Wystąpił błąd. Aby przejrzeć wszystkie wygenerowane komunikaty o błędach dla wyzwalacza, wybierz tę próbę, a następnie wybierz pozycję dane **wyjściowe**. Na przykład mogą znajdować się dane wejściowe, które nie są prawidłowe. | 
   ||| 

   Może istnieć wiele wpisów wyzwalacza o tej samej dacie i godzinie, co się dzieje, gdy aplikacja logiki znajdzie wiele elementów. 
   Za każdym razem, gdy wyzwala wyzwalacz, aparat Logic Apps tworzy wystąpienie aplikacji logiki do uruchomienia przepływu pracy. Domyślnie każde wystąpienie jest uruchamiane równolegle, aby nie czekać na uruchomienie przepływu pracy.

   > [!TIP]
   > Wyzwalacz można ponownie sprawdzić bez czekania na następny cykl. Na pasku narzędzi przegląd wybierz **Uruchom wyzwalacz**, a następnie wybierz wyzwalacz, który wymusza sprawdzenie. Lub zaznacz opcję **Uruchom** na pasku narzędzi projektanta Logic Apps.

3. Aby przejrzeć szczegóły próby wyzwalacza, w obszarze **historia wyzwalacza**wybierz tę próbę uruchomienia. 

   ![Wybierz próbkę wyzwalacza](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Przejrzyj wejścia i wszystkie dane wyjściowe generowane przez wyzwalacz. Wyjściowe wyzwalacze pokazują dane, które pochodzą z wyzwalacza. Te dane wyjściowe mogą pomóc w ustaleniu, czy wszystkie właściwości zostały zwrócone zgodnie z oczekiwaniami.

   > [!NOTE]
   > Jeśli znajdziesz zawartość, której nie znasz, Dowiedz się, jak Azure Logic Apps [obsługuje różne typy zawartości](../logic-apps/logic-apps-content-type.md).

   ![Wyjściowe wyzwalacza](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Przeglądać historię uruchamiania

Każdy uruchamiany wyzwalacz uruchamia przebieg przepływu pracy. Możesz sprawdzić, co się stało w tym przebiegu, w tym o stanie dla każdego kroku w przepływie pracy oraz dane wejściowe i wyjściowe dla każdego kroku.

1. Z menu aplikacji logiki wybierz pozycję **Przegląd**. W obszarze **historia uruchamiania**Przejrzyj przebieg uruchomienia wyzwalacza.

   > [!TIP]
   > Jeśli nie widzisz menu aplikacji logiki, spróbuj wrócić do pulpitu nawigacyjnego platformy Azure i ponownie otworzyć aplikację logiki.

   ![Przejrzyj historię uruchomień](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Jeśli nie znajdziesz danych, których oczekujesz, spróbuj wybrać pozycję **Odśwież** na pasku narzędzi.
   > * Jeśli lista zawiera wiele przebiegów i nie można znaleźć żądanego wpisu, spróbuj przefiltrować listę.

   Oto możliwe stany przebiegu:

   | Stan | Opis | 
   | ------ | ----------- | 
   | **Powodzenie** | Wszystkie akcje zostały wykonane pomyślnie. <p>Jeśli wystąpią jakieś błędy w określonej akcji, w przepływie pracy został obsłużony następujący błąd. | 
   | **Niepowodzenie** | Co najmniej jedna akcja nie powiodła się i nie skonfigurowano żadnych późniejszych akcji w przepływie pracy w celu obsługi błędu. | 
   | **Zerwan** | Przepływ pracy został uruchomiony, ale Otrzymano żądanie anulowania. | 
   | **Uruchomiono** | Przepływ pracy jest obecnie uruchomiony. <p>Ten stan może wystąpić w przypadku przepływów pracy z ograniczeniami lub z powodu bieżącego planu cenowego. Aby uzyskać więcej informacji, zobacz [limity akcji na stronie cennika](https://azure.microsoft.com/pricing/details/logic-apps/). W przypadku skonfigurowania [rejestrowania diagnostycznego](../logic-apps/logic-apps-monitor-your-logic-apps.md)można także uzyskać informacje o wszelkich zdarzeniach związanych z ograniczeniami. | 
   ||| 

2. Przejrzyj szczegóły każdego kroku w określonym przebiegu. W obszarze **historia uruchamiania**wybierz przebieg, który chcesz przejrzeć.

   ![Przejrzyj historię uruchomień](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   Niezależnie od tego, czy uruchomienie samego przebiegu zakończyło się powodzeniem, czy niepowodzeniem, widok Szczegóły przebiegu przedstawia każdy krok i czy zakończyło się pomyślnie

   ![Wyświetlanie szczegółów uruchomienia aplikacji logiki](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. Aby przeanalizować dane wejściowe, wyjściowe i wszystkie komunikaty o błędach dla określonego kroku, należy wybrać ten krok, aby kształt rozszerzał i pokazywał szczegóły. Przykład:

   ![Wyświetlanie szczegółów kroku](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Wykonaj debugowanie środowiska uruchomieniowego

Aby ułatwić debugowanie, można dodać kroki diagnostyczne do przepływu pracy, a także przejrzeć wyzwalacz i historię uruchomienia. Można na przykład dodać kroki, które korzystają z usługi [webhook testera](https://webhook.site/) , aby móc sprawdzać żądania HTTP i określać ich dokładny rozmiar, kształt i format.

1. Odwiedź [testera elementu webhook](https://webhook.site/) i Skopiuj utworzony unikatowy adres URL

2. W aplikacji logiki Dodaj akcję HTTP POST z zawartością treści, którą chcesz przetestować, na przykład wyrażeniem lub innym wyjściem kroku.

3. Wklej adres URL testera elementu webhook do akcji POST protokołu HTTP.

4. Aby sprawdzić, w jaki sposób jest tworzone żądanie w przypadku wygenerowania z aparatu Logic Apps, uruchom aplikację logiki i zobacz Tester elementu webhook, aby uzyskać szczegółowe informacje.

## <a name="next-steps"></a>Następne kroki

[Monitorowanie aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md)
