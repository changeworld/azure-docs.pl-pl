---
title: Połącz do wyszukiwania usługi Bing — aplikacje logiki platformy Azure | Dokumentacja firmy Microsoft
description: Znajdź wiadomości z interfejsów API REST wyszukiwania usługi Bing i usługi Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 8ac67f9df0e5baccc668c2aeb70f65d96e574df5
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021289"
---
# <a name="find-news-with-bing-search-and-azure-logic-apps"></a>Znajdź wiadomości z wyszukiwania usługi Bing i usługi Azure Logic Apps 

W tym artykule opisano, jak można znaleźć wiadomości, wideo i innych elementów za pomocą wyszukiwania usługi Bing z wewnątrz aplikacji logiki z łącznikiem wyszukiwania usługi Bing. W ten sposób można utworzyć aplikacji logiki, które automatyzują zadania i przepływy pracy do przetwarzania wyników wyszukiwania i elementy dostępne dla innych działań. 

Na przykład można znaleźć wiadomości na podstawie kryteriów wyszukiwania i mieć Twitter post tych elementów jako źródła danych tweetów w Twojej usługi Twitter.

Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. Jeśli jesteś nowym użytkownikiem aplikacji logiki, przejrzyj [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Aby uzyskać informacje techniczne dotyczące łącznika, zobacz <a href="https://docs.microsoft.com/connectors/bingsearch/" target="blank">odwołanie łącznika wyszukiwania usługi Bing</a>.

## <a name="prerequisites"></a>Wymagania wstępne

* A [kognitywnych usług konta](../cognitive-services/cognitive-services-apis-create-account.md)

* A [klucz interfejsu API wyszukiwania usługi Bing](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), która zapewnia dostęp z aplikacji logiki do interfejsy API wyszukiwania usługi Bing 

* Aplikację logiki, w którym chcesz uzyskać dostępu do Centrum zdarzeń. Aby uruchomić aplikację logiki z wyzwalaczem wyszukiwania usługi Bing, należy [aplikacji logiki puste](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Dodaj wyzwalacz wyszukiwania usługi Bing

W aplikacjach logiki platformy Azure, każda aplikacja logiki musi rozpoczynać się od [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), które odbywa się uruchamiany, gdy określonego zdarzenia lub gdy zostanie spełniony określony warunek. Zawsze uruchamiany wyzwalacza aparat Logic Apps tworzy wystąpienie aplikacji logiki i uruchamiania aplikacji przepływu pracy.

1. W portalu Azure lub programu Visual Studio tworzenie aplikacji logiki puste, który otwiera projektanta aplikacji logiki. W przykładzie użyto portalu Azure.

2. W polu wyszukiwania wprowadź "Wyszukiwania usługi Bing" jako filtr. Na liście wyzwalaczy zaznacz wyzwalacz, który ma. 

   W tym przykładzie użyto tego wyzwalacza: **wyszukiwania usługi Bing — na nowy artykuł**

   ![Znajdź wyzwalacza wyszukiwania usługi Bing](./media/connectors-create-api-bing-search/add-trigger.png)

3. Jeśli zostanie wyświetlony monit o szczegóły połączenia [utworzyć połączenie wyszukiwania usługi Bing teraz](#create-connection). Lub, jeśli połączenie już istnieje, podaj informacje niezbędne do wyzwalacza.

   Na przykład Podaj kryteria zwracanie pasującego artykuły z wyszukiwania usługi Bing.

   | Właściwość | Wymagane | Wartość | Opis | 
   |----------|----------|-------|-------------| 
   | Zapytanie wyszukiwania | Yes | <*Wyszukiwanie słów*> | Wprowadź słowa kluczowe wyszukiwania, którego chcesz użyć. |
   | Rynek | Yes | <*Ustawienia regionalne*> | Ustawienia regionalne wyszukiwania. Wartość domyślna to "pl pl", ale można wybrać inną wartość. | 
   | Bezpieczne wyszukiwania | Yes | <*poziom wyszukiwania*> | Poziom filtrów wykluczania zawartość dla dorosłych. Wartość domyślna to "Umiarkowany", ale możesz wybrać inny poziom. | 
   | Licznik | Nie | <*Liczba wyników*> | Zwraca określoną liczbę wyników. Wartość domyślna to 20, ale można określić inną wartość. Może to być mniejsza niż określona liczba rzeczywista liczba zwracanych wyników. | 
   | Przesunięcie | Nie | <*wartość Pomiń*> | Liczba wyników, aby pominąć przed zwróceniem wyników | 
   ||||| 

   Na przykład:

   ![Konfigurowanie wyzwalacza](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Wybierz interwał i częstotliwość Częstotliwość trigger, aby sprawdzić wyniki.

5. Gdy wszystko będzie gotowe, na pasku narzędzi projektanta, wybierz pozycję **zapisać**. 

6. Teraz kontynuować dodawanie co najmniej jednej akcji do aplikacji logiki dla zadań, które chcesz wykonać z wynikami wyzwalacza.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Dodaj akcję wyszukiwania usługi Bing

W aplikacjach logiki platformy Azure [akcji](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest krokiem w przepływie pracy następujący wyzwalacz inną akcję. Na przykład aplikację logiki rozpoczyna się od wyzwalacza wyszukiwania usługi Bing, która zwraca artykuły odpowiadającego określonym kryteriom. 

1. W portalu Azure lub programu Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki. W przykładzie użyto portalu Azure.

2. W ramach wyzwalacza lub akcji, wybierz **nowy krok** > **Dodaj akcję**.

   W tym przykładzie użyto tego wyzwalacza: **wyszukiwania usługi Bing — na nowy artykuł**

   ![Dodaj akcję](./media/connectors-create-api-bing-search/add-action.png)

   Aby dodać akcję między krokami istniejących, myszą strzałkę nawiązującego połączenie. 
   Wybierz znak plus (**+**) zostanie wyświetlony, a następnie wybierz **Dodaj akcję**.

3. W polu wyszukiwania wprowadź "Wyszukiwania usługi Bing" jako filtr.
Z listy akcji wybierz akcję, którą chcesz.

   W tym przykładzie użyto tej akcji: **wyszukiwania usługi Bing — lista wiadomości przez zapytanie**

   ![Znajdź akcji wyszukiwania usługi Bing](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Jeśli zostanie wyświetlony monit o szczegóły połączenia [utworzyć połączenie wyszukiwania usługi Bing teraz](#create-connection). Lub, jeśli połączenie już istnieje, podaj informacje niezbędne do działania. 

   Na przykład Podaj kryteria dla zwracania podzbiór wyniki tego wyzwalacza.

   | Właściwość | Wymagane | Wartość | Opis | 
   |----------|----------|-------|-------------| 
   | Zapytanie wyszukiwania | Yes | <*Wyrażenie wyszukiwania*> | Wprowadź wyrażenie na potrzeby zapytań o wynikach wyzwalacza. Wybierz z pól na liście zawartości dynamicznej lub utworzyć wyrażenie z Konstruktor wyrażeń. |
   | Rynek | Yes | <*Ustawienia regionalne*> | Ustawienia regionalne wyszukiwania. Wartość domyślna to "pl pl", ale można wybrać inną wartość. | 
   | Bezpieczne wyszukiwania | Yes | <*poziom wyszukiwania*> | Poziom filtrów wykluczania zawartość dla dorosłych. Wartość domyślna to "Umiarkowany", ale możesz wybrać inny poziom. | 
   | Licznik | Nie | <*Liczba wyników*> | Zwraca określoną liczbę wyników. Wartość domyślna to 20, ale można określić inną wartość. Może to być mniejsza niż określona liczba rzeczywista liczba zwracanych wyników. | 
   | Przesunięcie | Nie | <*wartość Pomiń*> | Liczba wyników, aby pominąć przed zwróceniem wyników | 
   ||||| 

   Na przykład załóżmy, że chcesz tych wyników, których nazwa kategorii zawiera słowo "pomoc". 
   
   1. Kliknij **zapytania wyszukiwania** pola, pojawi się lista zawartości dynamicznej. 
   Z tej listy, wybierz **wyrażenie** — Konstruktor wyrażeń wyświetlane. 

      ![Wyzwalacz wyszukiwania usługi Bing](./media/connectors-create-api-bing-search/bing-search-action.png)

      Teraz można przystąpić do tworzenia wyrażenia.

   2. Wybierz z listy funkcji **contains()** funkcji, która pojawia się w polu wyrażenie. Kliknij przycisk **zawartości dynamicznej** tak, aby ponownie pojawi się lista pól, ale upewnij się, że kursor pozostaje wewnątrz nawiasów.

      ![Wybierz funkcję](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Wybierz z listy pól **kategorii**, który konwertuje do parametru. 
   Dodaj przecinek po pierwszym parametrem i po przecinku, Dodaj ten wyraz: `'tech'` 

      ![Wybierz pole](./media/connectors-create-api-bing-search/expression-select-field.png)
   
   4. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

      Wyrażenie, które teraz pojawia się w **zapytania wyszukiwania** pole w następującym formacie:

      ![Zakończenie wyrażenia](./media/connectors-create-api-bing-search/resolved-expression.png)

      W widoku kodu tego wyrażenia pojawia się w następującym formacie:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Gdy wszystko będzie gotowe, na pasku narzędzi projektanta, wybierz pozycję **zapisać**.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Połącz do wyszukiwania usługi Bing

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Gdy zostanie wyświetlony monit o informacje dotyczące połączenia, podaj te szczegóły:

   | Właściwość | Wymagane | Wartość | Opis | 
   |----------|----------|-------|-------------| 
   | Nazwa połączenia | Yes | <*Nazwa połączenia*> | Nazwę można utworzyć połączenia |
   | Wersja interfejsu API | Yes | <*Wersja interfejsu API*> | Domyślnie wersja interfejsu API wyszukiwania usługi Bing jest ustawiona na bieżącą wersję. W razie potrzeby można wybrać starszej wersji. | 
   | Klucz interfejsu API | Yes | <*Klucz interfejsu API*> | Klucz interfejsu API wyszukiwania usługi Bing, który uzyskano wcześniej. Jeśli nie ma klucza, skorzystaj z [teraz klucz interfejsu API](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Na przykład:

   ![Tworzenie połączenia](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku programu Swagger łącznika, zobacz [strony odwołanie łącznika](/connectors/bingsearch/). 

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączniki Logic Apps](../connectors/apis-list.md)
