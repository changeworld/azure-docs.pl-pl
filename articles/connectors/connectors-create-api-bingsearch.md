---
title: Łączenie się z wyszukiwaniem Bing
description: Automatyzowanie zadań i przepływów pracy, które znajdują wyniki w wyszukiwarce Bing przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: e547ae59f7b3260f46756825bca2bef1c10bcc97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665891"
---
# <a name="find-results-in-bing-search-by-using-azure-logic-apps"></a>Znajdowanie wyników w wyszukiwarce Bing przy użyciu aplikacji Azure Logic Apps

W tym artykule pokazano, jak można znaleźć wiadomości, klipy wideo i inne elementy za pośrednictwem wyszukiwania Bing z wewnątrz aplikacji logiki z łącznikiem wyszukiwania Bing. W ten sposób można tworzyć aplikacje logiki, które automatyzują zadania i przepływy pracy do przetwarzania wyników wyszukiwania i udostępnić te elementy dla innych akcji. 

Możesz na przykład znaleźć wiadomości na podstawie kryteriów wyszukiwania i opublikować te elementy na Twitterze jako tweety w aktualnościach na Twitterze.

Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) i szybki [start: Utwórz pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Aby uzyskać informacje techniczne dotyczące łącznika, zobacz [odwołanie do łącznika wyszukiwania Bing](https://docs.microsoft.com/connectors/bingsearch/).

## <a name="prerequisites"></a>Wymagania wstępne

* [Konto usług Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md)

* [Klucz interfejsu API wyszukiwania Bing](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), który zapewnia dostęp z aplikacji logiki do interfejsów API wyszukiwania Bing

* Aplikacja logiki, w której chcesz uzyskać dostęp do Centrum zdarzeń. Aby uruchomić aplikację logiki za pomocą wyzwalacza wyszukiwania Bing, potrzebujesz [pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Dodawanie wyzwalacza wyszukiwania Bing

W usłudze Azure Logic Apps każda aplikacja logiki musi zaczynać się od [wyzwalacza,](../logic-apps/logic-apps-overview.md#logic-app-concepts)który uruchamia się, gdy występuje określone zdarzenie lub gdy zostanie spełniony określony warunek. Za każdym razem, gdy wyzwalacz jest uruchamiany, aparat aplikacji logiki tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy aplikacji.

1. W witrynie Azure portal lub visual studio utwórz pustą aplikację logiki, która otwiera projektanta aplikacji logiki. W tym przykładzie użyto witryny Azure portal.

2. W polu wyszukiwania wpisz "Wyszukiwanie Bing" jako filtr. Z listy wyzwalaczy wybierz odpowiedni wyzwalacz.

   W tym przykładzie użyto tego wyzwalacza: **Wyszukiwanie Bing — w nowym artykule z wiadomościami**

   ![Znajdź wyzwalacz wyszukiwania Bing](./media/connectors-create-api-bing-search/add-trigger.png)

3. Jeśli zostanie wyświetlony monit o podanie szczegółów połączenia, [utwórz połączenie wyszukiwania Bing teraz](#create-connection).
Lub, jeśli połączenie już istnieje, podaj niezbędne informacje dla wyzwalacza.

   W tym przykładzie podaj kryteria zwracania pasujących artykułów z wyszukiwania Bing.

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | Kwerenda wyszukiwania | Tak | <*szukaj słów*> | Wprowadź słowa kluczowe wyszukiwania, których chcesz użyć. |
   | Rynku | Tak | <*Ustawień regionalnych*> | Ustawienia regionalne wyszukiwania. Wartość domyślna to "en-US", ale można wybrać inną wartość. |
   | Bezpieczne wyszukiwanie | Tak | <*poziom wyszukiwania*> | Poziom filtru wykluczania treści dla dorosłych. Wartość domyślna to "Umiarkowane", ale wybierz inny poziom. |
   | Liczba | Nie | <*liczba wyników*> | Zwraca określoną liczbę wyników. Wartość domyślna to 20, ale można określić inną wartość. Rzeczywista liczba zwróconych wyników może być mniejsza niż określona liczba. |
   | Przesunięcie | Nie | <*wartość pomijania*> | Liczba wyników do pominięcia przed zwróceniem wyników |
   |||||

   Przykład:

   ![Konfigurowanie wyzwalacza](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Wybierz interwał i częstotliwość częstotliwości, jak często wyzwalacz ma sprawdzać wyniki.

5. Po zakończeniu na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

6. Teraz kontynuuj dodawanie jednej lub więcej akcji do aplikacji logiki dla zadań, które chcesz wykonać z wynikami wyzwalacza.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Dodawanie akcji wyszukiwania Bing

W usłudze Azure Logic Apps [akcja](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest krokiem w przepływie pracy, który następuje wyzwalacz lub innej akcji. W tym przykładzie aplikacja logiki rozpoczyna się od wyzwalacza wyszukiwania Bing, który zwraca artykuły z wiadomościami spełniające określone kryteria.

1. W witrynie Azure portal lub visual studio otwórz aplikację logiki w logice App Designer. W tym przykładzie użyto witryny Azure portal.

2. W obszarze wyzwalacza lub akcji wybierz pozycję **Nowy krok** > **Dodaj akcję**.

   W tym przykładzie użyto tego wyzwalacza:

   **Bing Search - W nowym artykule wiadomości**

   ![Dodawanie akcji](./media/connectors-create-api-bing-search/add-action.png)

   Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy na strzałkę łączącą. 
   Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

3. W polu wyszukiwania wpisz "Wyszukiwanie Bing" jako filtr.
Z listy akcji wybierz odpowiednią akcję.

   W tym przykładzie użyto tej akcji:

   **Wyszukiwanie Bing - Lista wiadomości według zapytania**

   ![Znajdź akcję wyszukiwania Bing](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Jeśli zostanie wyświetlony monit o podanie szczegółów połączenia, [utwórz połączenie wyszukiwania Bing teraz](#create-connection). Lub, jeśli połączenie już istnieje, podaj informacje niezbędne do działania.

   W tym przykładzie podaj kryteria zwracania podzbioru wyników wyzwalacza.

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | Kwerenda wyszukiwania | Tak | <*wyrażenie wyszukiwania*> | Wprowadź wyrażenie do wykonywania zapytań o wyniki wyzwalacza. Można wybrać z pól na liście zawartości dynamicznej lub utworzyć wyrażenie za pomocą konstruktora wyrażeń. |
   | Rynku | Tak | <*Ustawień regionalnych*> | Ustawienia regionalne wyszukiwania. Wartość domyślna to "en-US", ale można wybrać inną wartość. |
   | Bezpieczne wyszukiwanie | Tak | <*poziom wyszukiwania*> | Poziom filtru wykluczania treści dla dorosłych. Wartość domyślna to "Umiarkowane", ale wybierz inny poziom. |
   | Liczba | Nie | <*liczba wyników*> | Zwraca określoną liczbę wyników. Wartość domyślna to 20, ale można określić inną wartość. Rzeczywista liczba zwróconych wyników może być mniejsza niż określona liczba. |
   | Przesunięcie | Nie | <*wartość pomijania*> | Liczba wyników do pominięcia przed zwróceniem wyników |
   |||||

   Załóżmy na przykład, że mają być podane wyniki, których nazwa kategorii zawiera słowo "tech".

   1. Kliknij pole **Kwerenda wyszukiwania,** aby wyświetlić dynamiczną listę zawartości. 
   Z tej listy wybierz **opcję Wyrażenie,** aby wyświetlić konstruktora wyrażeń. 

      ![Wyzwalacz wyszukiwania Bing](./media/connectors-create-api-bing-search/bing-search-action.png)

      Teraz możesz rozpocząć tworzenie wyrażenia.

   2. Z listy funkcji wybierz funkcję **contains(),** która następnie pojawi się w polu wyrażenia. Kliknij **pozycję Zawartość dynamiczna,** aby lista pól pojawiła się ponownie, ale upewnij się, że kursor pozostaje w nawiasach.

      ![Wybieranie funkcji](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Z listy pól wybierz opcję **Kategoria**, która konwertuje na parametr. 
   Dodaj przecinek po pierwszym parametrze, a po przecinkom dodaj to słowo:`'tech'` 

      ![Wybieranie pola](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Gdy skończysz, wybierz opcję **OK**.

      Wyrażenie jest teraz wyświetlane w polu **Kwerenda wyszukiwania** w tym formacie:

      ![Gotowe wyrażenie](./media/connectors-create-api-bing-search/resolved-expression.png)

      W widoku kodu to wyrażenie pojawia się w tym formacie:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Po zakończeniu na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Łączenie się z wyszukiwaniem Bing

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Po wyświetleniu monitu o podanie informacji o połączeniu podaj następujące szczegóły:

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | Nazwa połączenia | Tak | <*nazwa połączenia*> | Nazwa do utworzenia dla połączenia |
   | Wersja interfejsu API | Tak | <*Wersja interfejsu API*> | Domyślnie wersja interfejsu API wyszukiwania Bing jest ustawiona na bieżącą wersję. W razie potrzeby można wybrać wcześniejszą wersję. |
   | Klucz interfejsu API | Tak | <*Klucz interfejsu API*> | Klucz interfejsu API wyszukiwania Bing, który został wcześniej. Jeśli nie masz klucza, pobierz [teraz klucz interfejsu API](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Przykład:

   ![Tworzenie połączenia](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger [łącznika,](/connectors/bingsearch/)zobacz stronę odwołania łącznika .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
