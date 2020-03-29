---
title: Testowanie aplikacji logiki za pomocą danych testowych
description: Konfigurowanie wyników statycznych do testowania aplikacji logicznych z makietami danych bez wpływu na środowiska produkcyjne
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: b71aae91f4a065b70537a300aa0bd7016edfd4b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790284"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Testowanie aplikacji logiki z makietami danych przez konfigurowanie wyników statycznych

Podczas testowania aplikacji logiki, może nie być gotowy do faktycznie wywołać lub uzyskać dostęp do aplikacji, usług i systemów z różnych powodów. Zazwyczaj w tych scenariuszach może być trzeba uruchomić różne ścieżki warunku, wymusić błędy, podać określone treści odpowiedzi wiadomości, a nawet spróbować pominąć niektóre kroki. Konfiguruj wyniki statyczne dla akcji w aplikacji logiki, można makiety danych wyjściowych z tej akcji. Włączenie wyników statycznych w akcji nie uruchamia akcji, ale zamiast tego zwraca dane makiety.

Na przykład jeśli skonfigurujesz wyniki statyczne dla akcji wysyłania poczty w programie Outlook 365, aparat aplikacji logiki zwraca po prostu dane makiety określone jako wyniki statyczne, zamiast wywoływać program Outlook i wysyłać wiadomości e-mail.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz skonfigurować wyniki statyczne

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Konfigurowanie wyników statycznych

1. Jeśli jeszcze tego nie zrobiłeś, w [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W przypadku akcji, w której chcesz skonfigurować wyniki statyczne, wykonaj następujące kroki: 

   1. W prawym górnym rogu akcji wybierz przycisk elipsy (*...*) i wybierz **wynik statyczny**, na przykład:

      ![Wybierz "Wynik statyczny" > "Włącz wynik statyczny"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Wybierz **pozycję Włącz wynik statyczny**. Dla wymaganych (*) właściwości, określ makiety wartości wyjściowych, które mają zostać zwracane dla odpowiedzi akcji.

      Na przykład oto wymagane właściwości akcji HTTP:

      | Właściwość | Opis |
      |----------|-------------|
      | **Stan** | Status akcji do powrotu |
      | **Kod stanu** | Określony kod stanu do zwrócenia |
      | **Nagłówki** | Zawartość nagłówka do zwrócenia |
      |||

      ![Wybierz "Włącz wynik statyczny"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Aby wprowadzić makiety danych w formacie Notacji obiektów JavaScript (JSON), wybierz pozycję **Przełącz na tryb JSON** (![Wybierz "Przełącz do trybu JSON").](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)

   1. W przypadku właściwości opcjonalnych otwórz listę **Wybierz pola opcjonalne** i wybierz właściwości, które mają być nachylione.

      ![Wybierz właściwości opcjonalne](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Gdy wszystko będzie gotowe do zapisania, wybierz pozycję **Gotowe**.

   W prawym górnym rogu akcji pasek tytułu pokazuje teraz ikonę zlewki testowej (Ikona![dla wyników](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)statycznych), która wskazuje, że włączono wyniki statyczne.

   ![Ikona z włączonymi wynikami statycznymi](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Aby znaleźć poprzednie przebiegi, które używają danych makiety, zobacz [Znajdowanie uruchomień, które używają wyników statycznych](#find-runs-mock-data) w dalszej części tego tematu.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Ponowne używanie poprzednich wyjść

Jeśli aplikacja logiki ma poprzednie uruchomienie z wyjść można użyć ponownie jako makiety wyjść, można skopiować i wkleić dane wyjściowe z tego uruchomienia.

1. Jeśli jeszcze tego nie zrobiłeś, w [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu głównym aplikacji logiki wybierz pozycję **Przegląd**.

1. W sekcji **Historia przebiegów** wybierz odpowiednią aplikację logiki.

1. W przepływie pracy aplikacji logiki znajdź i rozwiń akcję, która ma żądane dane wyjściowe.

1. Wybierz **łącze Pokaż nieprzetworzone dane wyjściowe.**

1. Skopiuj kompletny obiekt Notacji obiektów JavaScript (JSON) lub określoną podsekcję, której chcesz użyć, na przykład sekcję wyjścia, a nawet tylko sekcję nagłówków.

1. Postępuj zgodnie z instrukcjami otwierania pola **wyników statycznych** dla działania w [polu Konfigurowanie wyników statycznych](#set-up-static-results).

1. Po otwarciu **pola Wynik statyczny** wybierz jeden z tych kroków:

   * Aby wkleić kompletny obiekt JSON, wybierz![ **pozycję Przełącz na tryb JSON** ( wybierz "Przełącz do trybu JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)): ):

     ![Wybierz "Przełącz do trybu JSON", aby uzyskać pełny obiekt](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Aby wkleić tylko sekcję JSON, obok etykiety tej sekcji wybierz pozycję **Przełącz na tryb JSON** dla tej sekcji, na przykład:

     ![Wybierz "Przełącz do trybu JSON" dla wyjść](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. W edytorze JSON wklej wcześniej skopiowany JSON.

   ![Tryb JSON](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Po zakończeniu wybierz pozycję **Gotowe**. Lub, aby powrócić do projektanta,![wybierz **tryb przełączania edytora** ( Wybierz "Tryb przełączania edytora").](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Znajdź przebiegi, które używają wyników statycznych

Historia przebiegów aplikacji logiki identyfikuje przebiegi, w których akcje używają wyników statycznych. Aby znaleźć następujące przebiegi, wykonaj następujące kroki:

1. W menu głównym aplikacji logiki wybierz pozycję **Przegląd**. 

1. W prawym okienku w obszarze **Historia przebiegów**znajdź kolumnę **Wyniki statyczne.** 

   Każde uruchomienie, które zawiera akcje z wynikami, ma kolumnę **Wyniki statyczne** ustawione na **Włączone**, na przykład:

   ![Historia uruchamiania — kolumna wyników statycznych](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Aby wyświetlić akcje korzystające z wyników statycznych, wybierz odpowiedni bieg, w którym kolumna **Wyniki statyczne** jest **ustawiona**na Włączone .

   Akcje korzystające z wyników statycznych pokazują![ikonę zlewki testowej (Ikona dla wyników](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)statycznych), na przykład:

   ![Historia uruchamiania — akcje korzystające z wyników statycznych](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Wyłączanie wyników statycznych

Wyłączenie wyników statycznych nie powoduje wyrzucenia wartości z ostatniej konfiguracji. Tak więc po włączeniu wyników statycznych następnym razem można kontynuować korzystanie z poprzednich wartości.

1. Znajdź akcję, w której chcesz wyłączyć wyjścia statyczne. W prawym górnym rogu akcji wybierz ikonę zlewki![testowej (Ikona dla wyników](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)statycznych).

   ![Wyłączanie wyników statycznych](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Wybierz **pozycję Wyłącz wynik** > **statyczny gotowy**.

   ![Wyłączanie wyników statycznych](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Tematy pomocy

Aby uzyskać więcej informacji na temat tego ustawienia w podstawowych definicjach przepływu pracy, zobacz [Wyniki statyczne — odwołanie do schematu języka definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md#static-results) i środowiska [uruchomieniowegoConfiguration.staticResult — Ustawienia konfiguracji środowiska uruchomieniowego](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [usłudze Azure Logic Apps](../logic-apps/logic-apps-overview.md)