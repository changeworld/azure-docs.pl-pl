---
title: Dodawanie i uruchamianie kodu niestandardowego w usłudze Azure Logic Apps z usługą Azure Functions | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodawanie i uruchamianie niestandardowych fragmentach kodu w usłudze Azure Logic Apps z usługą Azure Functions
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/25/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 20ad738541554279ff9fd6dd6babe90a38676c00
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263194"
---
# <a name="add-and-run-custom-code-snippets-in-azure-logic-apps-with-azure-functions"></a>Dodawanie i uruchamianie niestandardowych fragmentach kodu w usłudze Azure Logic Apps z usługą Azure Functions

Gdy chcesz utworzyć i uruchomić wystarczającą ilość kodu, odnoszący się do konkretnego problemu w aplikacjach logiki, możesz utworzyć własne funkcje przy użyciu [usługi Azure Functions](../azure-functions/functions-overview.md). Ta usługa zapewnia możliwości tworzenia i wykonywania niestandardowych fragmentach kodu napisanej dla platformy Node.js lub C# w aplikacjach logiki bez konieczności martwienia się o tworzeniu całą aplikację ani infrastrukturę do uruchamiania kodu. Usługa Azure Functions zapewnia bezserwerowych obliczeń w chmurze i jest przydatne w przypadku wykonywania zadania, takie jak te przykłady:

* Rozszerzanie zachowania aplikacji logiki przy użyciu funkcji obsługiwanych przez Node.js lub C#.
* Wykonywanie obliczeń w przepływie pracy aplikacji logiki.
* Zastosuj zaawansowane formatowanie lub obliczeniowych pól w aplikacjach logiki.

Możesz również [wywoływanie aplikacji logiki z wewnątrz usługi Azure functions](#call-logic-app).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z tego artykułu, Oto elementy, które są potrzebne:

* Jeśli nie masz jeszcze subskrypcji platformy Azure <a href="https://azure.microsoft.com/free/" target="_blank">Załóż bezpłatne konto platformy Azure</a>. 

* Aplikacja logiki, w której chcesz dodać funkcję

  Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* A [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako pierwszy krok w aplikacji logiki 

  Przed dodaniem akcji dla działających funkcji, aplikacja logiki musi rozpoczynać wyzwalacza.

* Aplikacja funkcji platformy Azure, który jest kontenerem dla funkcji platformy Azure i funkcji platformy Azure. Jeśli nie masz aplikacji funkcji, musisz najpierw [najpierw utworzyć aplikację funkcji](../azure-functions/functions-create-first-azure-function.md). Następnie można utworzyć funkcję albo [oddzielnie poza aplikację logiki](#create-function-external), lub [z wewnątrz aplikacji logiki](#create-function-designer) w Projektancie aplikacji logiki.

  Zarówno aplikacje nowych i istniejących funkcji platformy Azure, jak i funkcji mają te same wymagania dotyczące pracy z aplikacjami logiki:

  * Aplikacja funkcji musi należeć do tej samej subskrypcji platformy Azure jako swoją aplikację logiki.

  * Należy użyć funkcji **ogólny element webhook** szablonu funkcji dla dowolnego **JavaScript** lub **C#**. Ten szablon może akceptować zawartość, która ma `application/json` typu z aplikacji logiki. Te szablony są również pomóc w Projektancie aplikacji logiki, znajdowanie i wyświetlanie niestandardowych funkcji, utworzonych za pomocą tych szablonów po dodaniu tych funkcji do aplikacji logiki.

  * Sprawdź, czy szablon funkcji **tryb** właściwość jest ustawiona na **elementu Webhook** i **typu elementu Webhook** właściwość jest ustawiona na **ogólnego JSON**.

    1. Zaloguj się w <a href="https://portal.azure.com" target="_blank">Portalu Azure</a>.
    2. W głównym menu platformy Azure, wybierz **aplikacje funkcji**. 
    3. W **aplikacje funkcji** listy, wybierz aplikację funkcji, rozwiń swoją funkcję i wybierz **integracja**. 
    4. Sprawdź swoje szablon **tryb** właściwość jest ustawiona na **elementu Webhook** i **typu elementu Webhook** właściwość jest ustawiona na **ogólnego JSON**. 

  * Jeśli funkcja [definicji interfejsu API](../azure-functions/functions-openapi-definition.md), wcześniej znany jako [plik struktury Swagger](http://swagger.io/), Projektant aplikacji usługi Logic Apps oferuje więcej możliwości for work za pomocą parametrów funkcji. 
  Zanim aplikacja logiki można znaleźć i dostęp do funkcji, które mają opisy struktury Swagger [Konfigurowanie aplikacji funkcji, wykonując następujące czynności](#function-swagger).

<a name="create-function-external"></a>

## <a name="create-functions-outside-logic-apps"></a>Tworzenie funkcji aplikacji logiki poza

W <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a>, Utwórz aplikację funkcji platformy Azure, która musi mieć tej samej subskrypcji platformy Azure jako swoją aplikację logiki, a następnie utwórz funkcji platformy Azure. Jeśli jesteś nowym użytkownikiem usługi Azure Functions, Dowiedz się, jak [tworzenie pierwszej funkcji w witrynie Azure portal](../azure-functions/functions-create-first-azure-function.md), ale należy pamiętać, następujące wymagania w celu tworzenia funkcji platformy Azure, które można dodać, a także wywoływać z aplikacji logiki.

* Upewnij się, możesz wybrać **ogólny element webhook** szablonu funkcji dla dowolnego **JavaScript** lub **C#**.

  ![Ogólny element webhook — JavaScript i C#](./media/logic-apps-azure-functions/generic-webhook.png)

* Po utworzeniu funkcji platformy Azure, sprawdź, czy szablon **tryb** i **typu elementu Webhook** właściwości są ustawione poprawnie.

  1. W **aplikacje funkcji** listy, rozwiń swoją funkcję i wybierz **integracja**. 

  2. Sprawdź, czy w szablonie **trybu** właściwość jest ustawiona na **elementu Webhook** i **typu elementu Webhook** właściwość jest ustawiona na **ogólnego JSON**. 

     ![Szablon funkcji "Integracja" właściwości](./media/logic-apps-azure-functions/function-integrate-properties.png)

<a name="function-swagger"></a>

* Opcjonalnie Jeśli użytkownik [Generowanie definicji interfejsu API](../azure-functions/functions-openapi-definition.md), wcześniej znany jako [plik struktury Swagger](http://swagger.io/), funkcji, można uzyskać więcej możliwości podczas pracy z parametrów funkcji w Projektancie aplikacji logiki. Aby skonfigurować aplikację funkcji, dzięki czemu Twoja aplikacja logiki może znajdować i dostęp do funkcji, które mają opisy struktury Swagger:

  * Upewnij się, że aplikacja funkcji jest aktywnie uruchomiona.

  * W aplikacji funkcji, skonfiguruj [udostępniania zasobów między źródłami (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) więc wszystkie źródła są dozwolone:

    1. Począwszy od **aplikacje funkcji** , wybierz aplikację funkcji na liście > **funkcje platformy** > **CORS**.

       ![Wybierz aplikację funkcji > "Funkcje platformy" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

    2. W obszarze **CORS**, Dodaj `*` symboli wieloznacznych znaków, ale Usuń wszystkie inne źródła z listy, a następnie wybierz **Zapisz**.

       ![Wybierz aplikację funkcji > "Funkcje platformy" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

### <a name="access-property-values-inside-http-requests"></a>Dostęp do wartości właściwości wewnątrz żądania HTTP

Funkcji elementu Webhook można zaakceptować żądania HTTP jako dane wejściowe i przekazać te żądania do innych funkcji. Na przykład mimo że ma Logic Apps [funkcje, które konwertują wartości daty/godziny](../logic-apps/workflow-definition-language-functions-reference.md), ta funkcja JavaScript podstawowy przykład pokazuje, jak dostęp można uzyskać właściwości wewnątrz żądanie obiektu, który jest przekazywany do funkcji i wykonywać operacje na wartość tej właściwości. Aby uzyskać dostęp do właściwości obiektów, w tym przykładzie użyto [kropka (.)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors): 

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Oto, co się stanie, wewnątrz tej funkcji:

1. Funkcja tworzy `data` zmienną i przypisuje `body` wewnątrz `request` obiekt do tej zmiennej. Funkcja używa operatora kropki (.), aby odwołać się do `body` wewnątrz `request` obiektu: 

   ```javascript
   var data = request.body;
   ```

2. Funkcja jest dostępna `date` właściwości, za pośrednictwem `data` zmienną i konwersję, która wartość właściwości z typu DateTime żądaniu typ, wywołując `ToDateString()` funkcji. Funkcja zwraca również wartość wyniku za pomocą `body` właściwości w odpowiedzi przez funkcję: 

   ```javascript
   body: data.date.ToDateString();
   ```

Teraz, po utworzeniu funkcji platformy Azure, postępuj zgodnie z instrukcjami, aby uzyskać instrukcje [dodawać funkcje do aplikacji logiki](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Tworzenie funkcji w aplikacji logiki

Zanim będzie można utworzyć funkcję platformy Azure, począwszy od wewnątrz aplikacji logiki w Projektancie aplikacji logiki, musisz najpierw mieć aplikację funkcji platformy Azure, który jest kontenerem dla funkcji. Jeśli masz aplikację funkcji, należy najpierw utworzyć aplikację funkcji. Zobacz [tworzenie pierwszej funkcji w witrynie Azure portal](../azure-functions/functions-create-first-azure-function.md). 

1. W <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a>, Otwórz aplikację logiki w Projektancie aplikacji logiki. 

2. W obszarze danego kroku, w którym chcesz utworzyć i Dodaj funkcję, wybierz opcję **nowy krok** > **Dodaj akcję**. 

3. W polu wyszukiwania wprowadź "azure functions" jako filtr.
Z listy akcji wybierz następującą akcję: **wybierz funkcję platformy Azure — usłudze Azure Functions** 

   ![Znajdź "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Z listy aplikacji funkcji wybierz aplikację funkcji. Po zostanie otwarta lista akcji, wybierz następującą akcję: **usługi Azure Functions — Tworzenie nowej funkcji**

   ![Wybierz aplikację funkcji](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. W edytorze definicji funkcji należy zdefiniować funkcję:

   1. W **nazwy funkcji** Podaj nazwę funkcji. 

   2. W **kodu** Dodaj kod funkcji w szablonie, łącznie z odpowiedzi i ładunku ma zwrócony do aplikacji logiki po zakończeniu funkcji. 
   Obiekt kontekstu w kodzie szablonu w tym artykule opisano wiadomości i zawartość, która przekazuje do funkcji, na przykład Twoja aplikacja logiki:

      ![Definiowanie funkcji](./media/logic-apps-azure-functions/function-definition.png)

      Wewnątrz funkcji możesz się odwoływać do właściwości w obiekcie kontekstu, używając następującej składni:

      ```text
      context.<token-name>.<property-name>
      ```
      Na przykład poniżej przedstawiono składnię, z której należy użyć:

      ```text
      context.body.content
      ```

   3. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

6. W **treść żądania** należy określić obiekt kontekstu do przekazania jako wejściowych funkcji, które muszą być sformatowane w JavaScript Object Notation (JSON). Po kliknięciu **treść żądania** , lista zawartości dynamicznej zostanie otwarte okno, można wybrać tokenów dla właściwości dostępne z poprzednich kroków. 

   Ten przykład przekazuje obiekt w **treści** tokenu z wyzwalacza poczty e-mail:  

   !["Treść żądania" przykład — kontekst obiekt ładunku](./media/logic-apps-azure-functions/function-request-body-example.png)

   Na podstawie zawartości w obiekt kontekstu, Projektant aplikacji logiki generuje szablon funkcji, następnie można edytować wbudowanego. 
   Usługa Logic Apps tworzy także zmienne w oparciu o obiekt kontekstu danych wejściowych.

   W tym przykładzie obiekt kontekstu nie jest rzutowanie jako ciąg znaków, więc zawartość pobiera bezpośrednio dodać do ładunku JSON. 
   Jednakże jeśli obiekt nie jest token JSON, który musi być ciąg, obiekt JSON lub tablicę JSON, wystąpi błąd. 
   Można rzutować obiektu context w postaci ciągu, Dodaj w podwójny cudzysłów, na przykład:

   ![Rzutowanie obiektu jako ciąg](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

7. Aby określić inne szczegóły, takie jak metoda do użycia, nagłówki żądania lub parametrów zapytania, wybierz **Pokaż opcje zaawansowane**.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Dodaj istniejące funkcje do usługi logic apps

Wywołać istniejących funkcji platformy Azure z poziomu aplikacji usługi logic apps, można dodać usługi Azure functions, takich jak każdą inną czynność w Projektancie aplikacji logiki. 

1. W <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a>, Otwórz aplikację logiki w Projektancie aplikacji logiki. 

2. W obszarze danego kroku, w której chcesz dodać funkcję, wybierz opcję **nowy krok** > **Dodaj akcję**. 

3. W polu wyszukiwania wprowadź "azure functions" jako filtr.
Z listy akcji wybierz następującą akcję: **wybierz funkcję platformy Azure — usłudze Azure Functions** 

   ![Znajdź "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Z listy aplikacji funkcji wybierz aplikację funkcji. Gdy zostanie wyświetlony na liście funkcji, wybierz funkcję. 

   ![Wybierz aplikację funkcji i funkcji platformy Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Dla funkcji, w tym definicji interfejsu API (opisy struktury Swagger), które są [Konfigurowanie, dzięki czemu Twoja aplikacja logiki może znajdować i dostęp do tych funkcji](#function-swagger), możesz wybrać **akcje programu Swagger**:

   ![Wybierz Twojej aplikacji funkcji, "Swagger akcje" "i funkcji platformy Azure](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

5. W **treść żądania** należy określić obiekt kontekstu do przekazania jako wejściowych funkcji, które muszą być sformatowane w JavaScript Object Notation (JSON). Ten obiekt kontekstu w tym artykule opisano wiadomości i zawartość, która aplikacja logiki wyśle do funkcji. 

   Po kliknięciu **treść żądania** , lista zawartości dynamicznej zostanie otwarte okno, można wybrać tokenów dla właściwości dostępne z poprzednich kroków. 
   Ten przykład przekazuje obiekt w **treści** tokenu z wyzwalacza poczty e-mail:

   !["Treść żądania" przykład — kontekst obiekt ładunku](./media/logic-apps-azure-functions/function-request-body-example.png)

   W tym przykładzie obiekt kontekstu nie jest rzutowanie jako ciąg znaków, więc zawartość pobiera bezpośrednio dodać do ładunku JSON. 
   Jednakże jeśli obiekt nie jest token JSON, który musi być ciąg, obiekt JSON lub tablicę JSON, wystąpi błąd. 
   Można rzutować obiektu context w postaci ciągu, Dodaj w podwójny cudzysłów, na przykład:

   ![Rzutowanie obiektu jako ciąg](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. Aby określić inne szczegóły, takie jak metoda do użycia, nagłówki żądania lub parametrów zapytania, wybierz **Pokaż opcje zaawansowane**.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Wywoływanie aplikacji logiki z funkcji

Aby wyzwolić aplikacji logiki od wewnątrz funkcji platformy Azure, ta aplikacja logiki musi mieć wywoływalnej punktu końcowego, lub dokładniej, **żądania** wyzwalacza. Następnie z wewnątrz funkcji, Wyślij żądanie HTTP POST na adres URL dla tego **żądania** wyzwalacza i Uwzględnij ładunek, chcesz, aby ta aplikacja logiki przetwarzania. Aby uzyskać więcej informacji, zobacz [wywołania wyzwalacza lub zagnieżdżanie aplikacji logiki](../logic-apps/logic-apps-http-endpoint.md). 

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [łączników Logic Apps](../connectors/apis-list.md)
