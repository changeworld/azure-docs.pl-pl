---
title: Łączenie do wyszukiwania usługi Bing — Azure Logic Apps
description: Znajdź wiadomości za pomocą interfejsów API REST wyszukiwania usługi Bing i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: 7146e59eabf9e30fa263f957f1c546414ad0fe26
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60952667"
---
# <a name="find-news-with-bing-search-and-azure-logic-apps"></a>Znajdź wiadomości za pomocą wyszukiwania w usłudze Bing i Azure Logic Apps

Ten artykuł pokazuje, jak możesz znaleźć wiadomości, wideo i innych elementów za pomocą funkcji wyszukiwania usługi Bing z wewnątrz aplikacji logiki z łącznikiem usługi wyszukiwania Bing. W ten sposób można utworzyć aplikacji logiki, które automatyzują zadania i przepływy pracy do przetwarzania wyników wyszukiwania i Udostępnij te elementy do innych działań. 

Na przykład można znaleźć elementy grup dyskusyjnych, na podstawie kryteriów wyszukiwania i ma wpis te elementy, jak tweety w usłudze Twitter kanału informacyjnego usługi Twitter.

Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Utwórz swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Aby uzyskać informacje techniczne dotyczące łącznika, zobacz <a href="https://docs.microsoft.com/connectors/bingsearch/" target="blank">dokumentacja łączników wyszukiwania Bing</a>.

## <a name="prerequisites"></a>Wymagania wstępne

* A [konta usług Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md)

* A [klucz interfejsu API wyszukiwania Bing](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), który zapewnia dostęp do interfejsów API wyszukiwania Bing z aplikacji logiki

* Aplikacja logiki gdzie mają dostęp do Centrum zdarzeń. Aby uruchomić aplikację logiki z wyzwalaczem wyszukiwania Bing, musisz mieć [pusta aplikacja logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Dodawanie wyzwalacza wyszukiwanie Bing

W usłudze Azure Logic Apps, każda aplikacja logiki musi rozpoczynać się [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), który jest aktywowany wystąpienia, gdy zajdzie określone zdarzenie lub po spełnieniu określonego warunku. Każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki i uruchamiania przepływu pracy Twojej aplikacji.

1. W witrynie Azure portal lub programu Visual Studio należy utworzyć pustej aplikacji logiki, który zostanie otwarty projektant aplikacji logiki. W tym przykładzie użyto portalu Azure.

2. W polu wyszukiwania wprowadź "Wyszukiwanie Bing" jako filtr. Z listy wyzwalaczy wybierz wyzwalacz, który ma.

   W tym przykładzie użyto tego wyzwalacza: **Wyszukiwanie Bing — na nowych artykułów**

   ![Znajdź wyzwalacz wyszukiwanie Bing](./media/connectors-create-api-bing-search/add-trigger.png)

3. Jeśli zostanie wyświetlony monit, aby uzyskać informacje dotyczące połączenia, [utworzyć połączenie wyszukiwania Bing teraz](#create-connection).
Lub, jeśli istnieje już połączenie, podaj informacje niezbędne dla wyzwalacza.

   W tym przykładzie należy podać kryteria dla zwracania artykuły z wiadomościami zgodnego z wyszukiwania Bing.

   | Właściwość | Wymagany | Value | Opis |
   |----------|----------|-------|-------------|
   | Search Query | Yes | <*search-words*> | Wprowadź słowa kluczowe wyszukiwania, których chcesz użyć. |
   | Market | Yes | <*locale*> | Ustawienia regionalne wyszukiwania. Wartość domyślna to "en US", ale można wybrać inną wartość. |
   | Safe Search | Yes | <*search-level*> | Poziom filtrowania wykluczania treści dla dorosłych. Wartość domyślna to "Średni", ale możesz wybrać inny poziom. |
   | Count | Nie | <*results-count*> | Zwróć określoną liczbę wyników. Wartość domyślna to 20, ale można określić inną wartość. Może to być mniejsza niż określona liczba rzeczywista liczba zwróconych wyników. |
   | Offset | Nie | <*skip-value*> | Liczba wyników do pominięcia przed zwróceniem wyników |
   |||||

   Na przykład:

   ![Konfigurowanie wyzwalacza](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Wybierz interwał i częstotliwość jak często ma wyzwalacz, aby sprawdzić, czy wyniki.

5. Gdy wszystko będzie gotowe, na pasku narzędzi Projektanta wybierz pozycję **Zapisz**.

6. Teraz kontynuowana, dodając jedną lub więcej akcji do aplikacji logiki dla zadań, które mają być wykonywane z wynikami wyzwalacza.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Dodawanie akcji wyszukiwania Bing

W usłudze Azure Logic Apps [akcji](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest to krok w przepływie pracy, występującego wyzwalacza lub innej akcji. W tym przykładzie aplikacja logiki rozpoczyna się od wyzwalacza wyszukiwania Bing, zwracające artykuły z wiadomościami odpowiadającego określonym kryteriom.

1. W witrynie Azure portal lub programu Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki. W tym przykładzie użyto portalu Azure.

2. W obszarze wyzwalacza lub akcji, wybierz opcję **nowy krok** > **Dodaj akcję**.

   W tym przykładzie użyto tego wyzwalacza:

   **Wyszukiwanie Bing — na nowych artykułów**

   ![Dodaj akcję](./media/connectors-create-api-bing-search/add-action.png)

   Aby dodać akcję między krokami istniejących, myszą strzałkę nawiązującego połączenie. 
   Wybierz znak plus ( **+** ) pojawia się, a następnie wybierz **Dodaj akcję**.

3. W polu wyszukiwania wprowadź "Wyszukiwanie Bing" jako filtr.
Z listy akcji wybierz akcję, którą chcesz.

   W tym przykładzie użyto tej akcji:

   **Wyszukiwanie Bing — lista wiadomości przez zapytanie**

   ![Znajdź działania wyszukiwania Bing](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Jeśli zostanie wyświetlony monit, aby uzyskać informacje dotyczące połączenia, [utworzyć połączenie wyszukiwania Bing teraz](#create-connection). Lub, jeśli istnieje już połączenie, podaj informacje niezbędne do działania.

   W tym przykładzie należy podać kryteria dla zwracania podzbiór wyników tego wyzwalacza.

   | Właściwość | Wymagany | Value | Opis |
   |----------|----------|-------|-------------|
   | Search Query | Yes | <*search-expression*> | Wprowadź wyrażenie dotyczące zapytaniach dotyczących wyników wyzwalacza. Wybierz z pól na liście zawartości dynamicznej lub utwórz wyrażenie z konstruktorem wyrażeń. |
   | Market | Yes | <*locale*> | Ustawienia regionalne wyszukiwania. Wartość domyślna to "en US", ale można wybrać inną wartość. |
   | Safe Search | Yes | <*search-level*> | Poziom filtrowania wykluczania treści dla dorosłych. Wartość domyślna to "Średni", ale możesz wybrać inny poziom. |
   | Count | Nie | <*results-count*> | Zwróć określoną liczbę wyników. Wartość domyślna to 20, ale można określić inną wartość. Może to być mniejsza niż określona liczba rzeczywista liczba zwróconych wyników. |
   | Offset | Nie | <*skip-value*> | Liczba wyników do pominięcia przed zwróceniem wyników |
   |||||

   Na przykład załóżmy, że chcesz użyć tych wyników, których nazwa kategorii zawiera wyraz "pomoc".

   1. Kliknij w **zapytania wyszukiwania** polu, aby wyświetlić listę zawartości dynamicznej. 
   Na tej liście, wybierz **wyrażenie** więc pojawia się konstruktorem wyrażeń. 

      ![Wyzwalacz wyszukiwanie Bing](./media/connectors-create-api-bing-search/bing-search-action.png)

      Teraz możesz rozpocząć tworzenie w wyrażeniu.

   2. Wybierz z listy funkcji **contains()** funkcji, która następnie zostanie wyświetlony w polu wyrażenie. Kliknij przycisk **zawartości dynamicznej** tak, aby ponownie pojawi się lista pól, ale upewnij się, że kursor pozostaje wewnątrz nawiasów.

      ![Wybierz funkcję](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Wybierz z listy pola **kategorii**, która konwertuje parametr. 
   Dodaj przecinek, po upływie pierwszego parametru i po przecinku, Dodaj ten wyraz: `'tech'` 

      ![Wybierz pole](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

      Teraz wyrażenie pojawia się w **zapytania wyszukiwania** polu w następującym formacie:

      ![Zakończono wyrażenia](./media/connectors-create-api-bing-search/resolved-expression.png)

      W widoku kodu to wyrażenie pojawia się w następującym formacie:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Gdy wszystko będzie gotowe, na pasku narzędzi Projektanta wybierz pozycję **Zapisz**.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Łączenie do wyszukiwania w usłudze Bing

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Po wyświetleniu monitu, aby uzyskać informacje o połączeniu, należy podać następujące dane:

   | Właściwość | Wymagany | Value | Opis |
   |----------|----------|-------|-------------|
   | Nazwa połączenia | Yes | <*connection-name*> | Nazwa do utworzenia połączenia |
   | Wersja interfejsu API | Yes | <*Wersja interfejsu API*> | Domyślnie wersja interfejsu API wyszukiwania Bing jest ustawiona na bieżącą wersję. Wybierz z wcześniejszej wersji, zgodnie z potrzebami. |
   | Klucz interfejsu API | Yes | <*API-key*> | Klucz interfejsu API wyszukiwania Bing, uzyskany wcześniej. Jeśli nie masz klucza usługi [klucz interfejsu API teraz](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Na przykład:

   ![Tworzenie połączenia](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegóły techniczne, takich jak wyzwalacze, akcje i ograniczeń, zgodnie z opisem w łącznika interfejsu OpenAPI (dawniej Swagger) plików, zobacz [strona referencyjna łącznika](/connectors/bingsearch/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
