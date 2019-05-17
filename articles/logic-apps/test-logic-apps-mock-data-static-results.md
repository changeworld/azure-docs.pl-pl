---
title: Testowanie aplikacji logiki za pomocą makiety danych — Azure Logic Apps
description: Konfigurowanie statycznych wyniki testowanie aplikacji logiki za pomocą danych testowych bez wpływu na środowiska produkcyjne
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: 45eeb20e5c572ddd98244b2e751322fcce1d4b76
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597197"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Testowanie aplikacji logiki za pomocą danych testowych, konfigurując wyniki statycznej

Podczas testowania aplikacji usługi logic apps, może nie być gotowy do rzeczywistego wywołania lub dostęp do aplikacji, usługami i systemami z różnych powodów. Zazwyczaj w tych scenariuszach może być konieczne uruchomić inny warunek ścieżki, wymusić błędy, zapewniają szczegółowy komunikat o błędzie ciała odpowiedzi lub nawet spróbuj pomijanie kilka kroków. Konfigurując statyczne wyniki akcji w aplikacji logiki można testowanie danych wyjściowych z tej akcji. Zwracanie wyników statycznych w akcji nie zostanie uruchomiona akcja, ale zamiast tego zwraca danych testowych.

Na przykład jeśli skonfigurowano statyczne wyniki dla usługi Outlook 365 wysłanie wiadomości e-mail, aparat usługi Logic Apps, po prostu zwraca makiety dane, które określono jako statyczny wynik, zamiast wywoływać programu Outlook i Wyślij wiadomość e-mail.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz skonfigurować statyczne wyników

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Konfigurowanie statycznych wyników

1. Jeśli nie jest już w [witryny Azure portal](https://portal.azure.com), Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W przypadku akcji, której chcesz skonfigurować statyczne wyniki wykonaj następujące kroki: 

   1. W akcji w prawym górnym rogu wybierz wielokropek (*...* ) i wybierz **statyczny wynik**, na przykład:

      ![Wybierz pozycję "Statyczny wynik" > "Włącz statyczny wynik"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Wybierz **Włącz statyczny wynik**. W przypadku właściwości wymagane (*) określ wartości makiety danych wyjściowych, które mają być zwracane dla danej akcji odpowiedzi.

      Na przykład poniżej przedstawiono wymagane właściwości dla akcji HTTP:

      | Właściwość | Opis |
      |----------|-------------|
      | **Stan** | Stan akcji do zwrócenia |
      | **Kod stanu** | Kod stanu określonego do zwrócenia |
      | **Nagłówki** | Zawartość nagłówka, aby zwrócić |
      |||

      ![Wybierz pozycję "Włącz statyczny wynik"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Aby wprowadzić makiety dane w formacie JavaScript Object Notation (JSON), wybierz opcję **Przełącz do trybu JSON** (![wybierz "Przełącz na tryb JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)).

   1. Opcjonalne właściwości, można otworzyć **wybierz opcjonalne pola** listy i wybierz właściwości, które mają być testowanie.

      ![Wybierz opcjonalne właściwości](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Gdy wszystko będzie gotowe zapisać, wybierz pozycję **gotowe**.

   W akcji w prawym górnym rogu paska tytułu pojawią się ikony zlewce test (![ikonę wyniki statycznej](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)), co oznacza, że włączono wyniki statycznej.

   ![Ikona przedstawiająca włączone wyniki statycznej](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Aby znaleźć poprzednie przebiegi, które używają danych testowych, zobacz [znajdowanie, które używają statycznych wyniki](#find-runs-mock-data) w dalszej części tego tematu.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Ponowne użycie poprzednich danych wyjściowych

Jeśli Twoja aplikacja logiki ma poprzedniej Uruchom z danych wyjściowych, który można wykorzystać w formie makiety danych wyjściowych, możesz można skopiować i wkleić dane wyjściowe z uruchamianą w.

1. Jeśli nie jest już w [witryny Azure portal](https://portal.azure.com), Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu głównym aplikację logiki, wybierz **Przegląd**.

1. W **Historia przebiegów** zaznacz ma możesz uruchamiania aplikacji logiki.

1. W przepływie pracy aplikacji logiki Znajdź i rozwiń akcję, która zawiera dane wyjściowe, które chcesz.

1. Wybierz **Pokaż nieprzetworzone dane wyjściowe** łącza.

1. Kopiuj kompletnego obiektu JavaScript Object Notation (JSON) lub określonej podsekcji, którego chcesz użyć, na przykład, sekcję danych wyjściowych lub nawet tylko sekcję nagłówków.

1. Postępuj zgodnie z instrukcjami otwierania **statyczny wynik** okno akcji w [skonfigurować statyczne wyniki](#set-up-static-results).

1. Po **statyczny wynik** otwiera sie okno, wybierz albo krok:

   * Aby wkleić kompletnym obiektem JSON, wybierz opcję **Przełącz do trybu JSON** (![wybierz "Przełącz na tryb JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)):

     ![Wybierz obiekt kompletny "Przełącz na tryb JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Aby wkleić tylko sekcję JSON, obok etykiety w tej sekcji, wybierz polecenie **Przełącz do trybu JSON** dla tej sekcji, na przykład:

     ![Wybierz pozycję "Przełącz na tryb JSON" dla danych wyjściowych](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. W edytorze JSON Wklej skopiowany wcześniej kod JSON.

   ![Tryb JSON](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Po zakończeniu wybierz pozycję **Gotowe**. Lub, aby powrócić do projektanta, wybierz **tryb przełącznika edytora** (![wybierz pozycję "Tryb edytora przełącznika"](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)).

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Znajdowanie, które używają statycznych wyników

Historia uruchomień aplikacji logiki identyfikuje przebiegów, gdzie akcje wykorzystują wyniki statycznej. Aby znaleźć te przebiegi, wykonaj następujące kroki:

1. W menu głównym aplikację logiki, wybierz **Przegląd**. 

1. W okienku po prawej stronie w obszarze **Historia przebiegów**, Znajdź **wyniki statycznej** kolumny. 

   Każde uruchomienie zawierające akcji z wynikami ma **wyniki statycznej** kolumna ustawiona **włączone**, na przykład:

   ![Historia - uruchamiania kolumnie wyników statyczne](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Aby wyświetlić akcje, które Użyj statycznych wyników, wybierz działanie, które mają miejsce **wyniki statycznej** kolumny ustawiono **włączone**.

   Akcje, które Użyj statycznych wyników pokazują zlewce testu (![ikonę wyniki statycznej](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)) ikona, na przykład:

   ![Historia - uruchamiania akcje, które Użyj statycznych wyników](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Wyłącz wyniki statycznej

Wyłączenie wyniki statycznej nie Pozbywać się wartości z ostatniej konfiguracji. Tak po włączeniu wyniki statycznej następnym razem, możesz kontynuować używanie poprzedniej wartości.

1. Znajdź akcji, w której chcesz wyłączyć statyczne dane wyjściowe. W akcji w prawym górnym rogu wybierz ikonę zlewce testu (![ikonę wyniki statycznej](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)).

   ![Wyłącz wyniki statycznej](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Wybierz **Wyłącz statyczny wynik** > **gotowe**.

   ![Wyłącz wyniki statycznej](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Odwołanie

Aby uzyskać więcej informacji na temat tego ustawienia w podstawowej definicji przepływu pracy, zobacz [wyniki statycznej — odwołanie do schematu dla języka definicji programu Workflow](../logic-apps/logic-apps-workflow-definition-language.md#static-results) i [runtimeConfiguration.staticResult — środowisko uruchomieniowe ustawienia konfiguracji](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md)