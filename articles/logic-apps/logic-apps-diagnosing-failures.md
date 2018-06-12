---
title: Rozwiązywanie problemów i diagnozowanie błędów - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Zrozumienie, jak i dlaczego aplikacje logiki zakończyć się niepowodzeniem
services: logic-apps
documentationcenter: ''
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 10/15/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 5af99821305fe6daab8a213d0351c5a1c5936461
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298794"
---
# <a name="troubleshoot-and-diagnose-logic-app-failures"></a>Rozwiązywanie problemów i diagnozowanie błędów aplikacji logiki

Aplikację logiki generuje informacje, które pomogą zdiagnozować i debugowania problemów w aplikacji. Diagnozowanie z aplikacji logiki, przeglądając każdego kroku w przepływie pracy za pośrednictwem portalu Azure. Lub niektóre czynności można dodać do przepływu pracy środowiska uruchomieniowego debugowania.

## <a name="review-trigger-history"></a>Przejrzyj historię wyzwalacza

Każda aplikacja logiki rozpoczyna się od wyzwalacza. Jeśli nie uruchomienie wyzwalacza, sprawdź najpierw historii wyzwalacza. Historię Wyświetla wszystkie próby wyzwalacza, wprowadzone przez aplikację logiki i szczegółowe informacje o wejścia i wyjścia dla każdej próbie wyzwalacza.

1. Aby sprawdzić, czy wyzwalacz uruchamiany w menu aplikacji logiki, wybierz **omówienie**. W obszarze **historii wyzwalacza**, sprawdź stan tego wyzwalacza.

   > [!TIP]
   > Jeśli nie widzisz menu aplikacji logiki, spróbuj wrócić do pulpitu nawigacyjnego platformy Azure i ponownie otworzyć aplikację logiki.

   ![Przejrzyj historię wyzwalacza](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Jeśli nie możesz znaleźć dane, które spodziewasz się, spróbuj wybrać **Odśwież** na pasku narzędzi.
   > * Jeśli lista zawiera wiele wyzwolenia prób i nie można odnaleźć wpisu, który ma, spróbuj filtrowanie listy.

   Poniżej przedstawiono możliwe stany próbę wyzwalacz:

   | Stan | Opis | 
   | ------ | ----------- | 
   | **Powiodło się.** | Wyzwalacz zaznaczony punkt końcowy, a znaleziono dostępnych danych. Zwykle stan "Fired" pojawia się również z tego stanu. Jeśli nie, definicję wyzwalacza może mieć elementu condition lub `SplitOn` polecenia, który nie został spełniony. <p>Ten stan można stosować do ręcznego wyzwalacza, wyzwalacz cyklu lub wyzwalacza sondowania. Wyzwalacz można uruchomić pomyślnie, ale akcji generują błędy nieobsługiwany nadal może spowodować błąd samego przebiegu. | 
   | **Pominięto** | Wyzwalacz zaznaczone punktu końcowego, ale znaleziono żadnych danych. | 
   | **Nie powiodło się** | Wystąpił błąd. Aby przejrzeć wygenerowane komunikaty wyzwalacz nie powiodło się, zaznacz taka próba wyzwalacza i wybierz polecenie **dane wyjściowe**. Na przykład może znaleźć danych wejściowych, które nie są prawidłowe. | 
   ||| 

   Może mieć wiele wpisów wyzwalacza z tej samej daty i czasu, który się stanie w przypadku aplikacji logiki znajduje się wiele elementów. 
   Zawsze uruchamiany wyzwalacza aparat Logic Apps tworzy wystąpienie aplikacji logiki, aby uruchomić przepływ pracy. Domyślnie każde wystąpienie uruchamia się równolegle tak, aby nie przepływ pracy ma oczekiwania przed uruchomieniem do uruchomienia.

   > [!TIP]
   > Można ponownie sprawdzić wyzwalacza bez oczekiwanie na następne wystąpienie. Na pasku narzędzi omówienie wybierz **uruchomić wyzwalacz**i wybierz wyzwalacz, który wymusza sprawdzania. Lub wybierz **Uruchom** na pasku narzędzi Projektanta aplikacji logiki.

3. Aby sprawdzić szczegóły próbę wyzwalacza, w obszarze **historii wyzwalacza**, wybierz taka próba wyzwalacza. 

   ![Wybierz próba wyzwalacza](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Przejrzyj dane wejściowe i wyjść wygenerowanych przez wyzwalacz. Dane wyjściowe wyzwalacza Pokaż dane, które nadeszły z wyzwalacza. Te dane wyjściowe może pomóc w określeniu, czy wszystkie właściwości zwracane zgodnie z oczekiwaniami.

   > [!NOTE]
   > Jeśli okaże się zawartość, która nie rozumie, Dowiedz się, jak Azure Logic Apps [obsługuje różne typy zawartości](../logic-apps/logic-apps-content-type.md).

   ![Dane wyjściowe wyzwalacza](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Przeglądać historię uruchamiania

Każdy wypalane wyzwalacz uruchamia uruchomić przepływ pracy. Możesz przejrzeć, co się stało podczas uruchomienia, w tym stan każdego kroku w przepływie pracy, oraz wejścia i wyjścia dla każdego kroku.

1. Z menu aplikacji logiki wybierz pozycję **Przegląd**. W obszarze **uruchamia historii**, przejrzyj Uruchom wypalane wyzwalacza.

   > [!TIP]
   > Jeśli nie widzisz menu aplikacji logiki, spróbuj wrócić do pulpitu nawigacyjnego platformy Azure i ponownie otworzyć aplikację logiki.

   ![Przejrzyj uruchamia historii](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Jeśli nie możesz znaleźć dane, które spodziewasz się, spróbuj wybrać **Odśwież** na pasku narzędzi.
   > * Jeśli lista zawiera wiele działa, a nie można odnaleźć wpisu, który ma, spróbuj filtrowanie listy.

   Poniżej przedstawiono możliwe stany dla Uruchom:

   | Stan | Opis | 
   | ------ | ----------- | 
   | **Powiodło się.** | Wszystkie akcje zakończyło się pomyślnie. <p>Jeśli miejsce awarii w określonej akcji, następujące działania w przepływie pracy obsługi tego błędu. | 
   | **Nie powiodło się** | Nie powiodło się co najmniej jedną akcję, a nie późniejsze akcje w przepływie pracy skonfigurowanych do obsługi błędu. | 
   | **Anulowane** | Przepływ pracy był uruchomiony, ale Odebrano żądanie anulowania. | 
   | **Uruchomiona** | Przepływ pracy jest uruchomiony. <p>Ten stan może się zdarzyć, dla przepływów pracy z ograniczeniem przepustowości lub z powodu bieżącego planu cenowego. Aby uzyskać więcej informacji, zobacz [limity akcji na stronie cen](https://azure.microsoft.com/pricing/details/logic-apps/). Po skonfigurowaniu [rejestrowania diagnostyki](../logic-apps/logic-apps-monitor-your-logic-apps.md), można także uzyskać informacji o żadnych zdarzeń ograniczania zależnych. | 
   ||| 

2. Przejrzyj szczegóły dla każdego kroku w określonym przebiegu. W obszarze **uruchamia historii**, wybierz polecenie Uruchom, który chcesz zbadać.

   ![Przejrzyj uruchamia historii](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   Określa, czy uruchomienie się powodzeniem lub niepowodzeniem, w widoku szczegółów Uruchom Pokazuje każdy krok i czy powodzeniem lub niepowodzeniem.

   ![Wyświetlanie szczegółów uruchomienia aplikacji logiki](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. Zbadanie wejść, wyjść i komunikaty o błędach do określonego kroku wybierz tego kroku, aby kształtu rozszerza i szczegóły. Na przykład:

   ![Wyświetlanie szczegółów kroku](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Wykonaj profilowanie środowiska uruchomieniowego

Aby pomóc w debugowaniu, można dodać diagnostycznych kroki do przepływu pracy, oraz przeglądanie wyzwalacza i uruchamia historii. Na przykład można dodać kroki, które używają [RequestBin](http://requestb.in) usługi, dzięki czemu można sprawdzić żądań HTTP i określić ich dokładny rozmiar, kształt i format.

1. Utwórz RequestBin, co może uniemożliwić prywatne i możliwych do wyświetlenia tylko w przeglądarce.

2. W aplikacji logiki Dodaj akcję POST protokołu HTTP z zawartości w treści, która ma zostać przetestowana, na przykład, wyrażenie lub inny krok danych wyjściowych.

3. Wklej adres URL dla Twojego RequestBin do akcji POST protokołu HTTP.

4. Aby sprawdzić, jak żądanie powstaje podczas generowania z aparatu Logic Apps, uruchom aplikację logiki i Odśwież RequestBin Twojego.

## <a name="next-steps"></a>Kolejne kroki

[Monitorowanie aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md)