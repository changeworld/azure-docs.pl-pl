---
title: Troubleshoot and diagnose workflow failures (Rozwiązywanie problemów i diagnozowanie błędów przepływu pracy)
description: Dowiedz się, jak rozwiązywać i diagnozować problemy, błędy i błędy w przepływach pracy w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904994"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Rozwiązywanie problemów i diagnozowanie błędów przepływów pracy w usłudze Azure Logic Apps

Aplikacja logiki generuje informacje, które mogą pomóc w diagnozowaniu i debugowaniu problemów w aplikacji. Aplikację logiki można zdiagnozować, przeglądając każdy krok w przepływie pracy za pośrednictwem witryny Azure portal. Lub można dodać kilka kroków do przepływu pracy do debugowania środowiska uruchomieniowego.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>Sprawdzanie historii wyzwalaczy

Każde uruchomienie aplikacji logiki rozpoczyna się od próby wyzwalania, więc jeśli wyzwalacz nie zostanie uruchomiony, wykonaj następujące kroki:

1. Sprawdź stan wyzwalacza, [sprawdzając historię wyzwalaczy](../logic-apps/monitor-logic-apps.md#review-trigger-history). Aby wyświetlić więcej informacji o próbie wyzwalacza, wybierz zdarzenie wyzwalające, na przykład:

   ![Wyświetlanie stanu i historii wyzwalacza](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. Sprawdź dane wejściowe wyzwalacza, aby potwierdzić, że są wyświetlane zgodnie z oczekiwaniami. W obszarze **Wejścia łącze**wybierz łącze, które pokazuje okienko **Wejścia.**

   Wyzwalacze dane wejściowe obejmują dane, które wyzwalacz oczekuje i wymaga, aby uruchomić przepływ pracy. Przeglądanie tych danych wejściowych może pomóc w określeniu, czy dane wejściowe wyzwalacza są poprawne i czy warunek został spełniony, aby przepływ pracy mógł być kontynuowany.

   Na przykład `feedUrl` właściwość w tym miejscu ma niepoprawną wartość źródła danych RSS:

   ![Przeglądanie danych wejściowych wyzwalacza pod kątem błędów](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. Sprawdź wyjścia wyzwalaczy, jeśli istnieją, aby potwierdzić, że są one wyświetlane zgodnie z oczekiwaniami. W obszarze **Wyjścia łącze**wybierz łącze, które pokazuje okienko **Wyjścia.**

   Wyjścia wyzwalania obejmują dane, które wyzwalacz przekazuje do następnego kroku w przepływie pracy. Przeglądanie tych danych wyjściowych może pomóc w określeniu, czy poprawne lub oczekiwane wartości zostały przekazane do następnego kroku w przepływie pracy, na przykład:

   ![Przeglądanie wyjść wyzwalaczy pod kątem błędów](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > Jeśli znajdziesz dowolną zawartość, której nie rozpoznajesz, dowiedz się więcej o [różnych typach zawartości](../logic-apps/logic-apps-content-type.md) w usłudze Azure Logic Apps.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>Sprawdzanie historii przebiegów

Za każdym razem, gdy wyzwalacz jest uruchamiany dla elementu lub zdarzenia, aparat aplikacji logiki tworzy i uruchamia oddzielne wystąpienie przepływu pracy dla każdego elementu lub zdarzenia. Jeśli uruchomienie nie powiedzie się, wykonaj następujące kroki, aby przejrzeć, co się stało podczas tego uruchomienia, w tym stan dla każdego kroku w przepływie pracy oraz dane wejściowe i wyjściowe dla każdego kroku.

1. Sprawdź stan uruchomienia przepływu pracy, [sprawdzając historię przebiegów](../logic-apps/monitor-logic-apps.md#review-runs-history). Aby wyświetlić więcej informacji na temat nieudanego uruchomienia, w tym wszystkie kroki w tym uruchomieniu w ich stanie, wybierz przebieg nie powiodło się.

   ![Wyświetlanie historii uruchamiania i wybieranie nieudanego przebiegu](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. Po wyświetleniach wszystkich kroków w biegu rozwiń pierwszy krok nie powiódł się.

   ![Rozwiń pierwszy krok nie powiodły się](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. Sprawdź dane wejściowe nieudanego kroku, aby potwierdzić, czy są one wyświetlane zgodnie z oczekiwaniami.

1. Przejrzyj szczegóły dotyczące każdego kroku w określonym przebiegu. W obszarze **Historia przebiegów**wybierz przebieg, który chcesz sprawdzić.

   ![Historia przebiegów recenzji](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![Wyświetlanie szczegółów uruchomienia aplikacji logiki](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. Aby sprawdzić dane wejściowe, wyjściowe i wszelkie komunikaty o błędach dla określonego kroku, wybierz ten krok, tak aby kształt rozszerzał i pokazywać szczegóły. Przykład:

   ![Wyświetlanie szczegółów kroku](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Wykonywanie debugowania środowiska uruchomieniowego

Aby ułatwić debugowanie, można dodać kroki diagnostyczne do przepływu pracy aplikacji logiki, wraz z przeglądaniem wyzwalacza i historii uruchamiania. Na przykład można dodać kroki, które korzystają z usługi [Tester elementu webhook,](https://webhook.site/) dzięki czemu można sprawdzić żądania HTTP i określić ich dokładny rozmiar, kształt i format.

1. Przejdź do witryny [testera elementu webhook](https://webhook.site/) i skopiuj wygenerowany unikatowy adres URL.

1. W aplikacji logiki dodaj akcję HTTP POST oraz zawartość treści, którą chcesz przetestować, na przykład wyrażenie lub inny krok wyjściowy.

1. Wklej adres URL z testu elementu webhook do akcji HTTP POST.

1. Aby przejrzeć sposób tworzenia żądania po wygenerowaniu z aparatu aplikacji logiki, uruchom aplikację logiki i ponownie odwiedź witrynę testera elementu webhook, aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie aplikacji logiki](../logic-apps/monitor-logic-apps.md)
