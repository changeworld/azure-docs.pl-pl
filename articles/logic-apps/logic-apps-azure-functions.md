---
title: Dodawanie i uruchamianie kodu w usłudze Azure Logic Apps z usługą Azure Functions
description: Dodawanie i uruchamianie kodu w usłudze Azure Logic Apps z usługą Azure Functions
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.topic: article
ms.date: 08/20/2018
ms.reviewer: klam, LADocs
ms.openlocfilehash: e371a6abe32a1a41d3babeaa27aaec3e30bd3323
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142320"
---
# <a name="add-and-run-code-by-using-azure-functions-in-azure-logic-apps"></a>Dodawanie i uruchamianie kodu za pomocą usługi Azure Functions w usłudze Azure Logic Apps

Gdy użytkownik chce uruchomić kod, który wykonuje określone zlecenie w aplikacjach logiki, można utworzyć funkcji za pomocą [usługi Azure Functions](../azure-functions/functions-overview.md). Usługa ta pomaga w tworzeniu platformy Node.js i C#, i F# kodu, dzięki czemu nie trzeba utworzyć pełną aplikację lub infrastrukturę do uruchamiania kodu. Możesz również [wywoływanie aplikacji logiki z wewnątrz usługi Azure functions](#call-logic-app).
Usługa Azure Functions zapewnia bezserwerowych obliczeń w chmurze i jest przydatne w przypadku wykonywania zadania, takie jak te przykłady:

* Rozszerz zachowanie aplikacji logiki z usługą functions w środowisku Node.js lub C#.
* Wykonywanie obliczeń w przepływie pracy aplikacji logiki.
* Zastosuj zaawansowane formatowanie lub obliczeniowych pól w aplikacjach logiki.

Aby uruchomić fragmenty kodu, bez tworzenia usługi Azure functions, Dowiedz się, jak [Dodawanie i uruchamianie kodu wbudowanego](../logic-apps/logic-apps-add-run-inline-code.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja funkcji platformy Azure, który jest kontenerem dla funkcji platformy Azure i funkcji platformy Azure. Jeśli masz aplikację funkcji [najpierw utworzyć aplikację funkcji](../azure-functions/functions-create-first-azure-function.md). Następnie można utworzyć funkcję albo [oddzielnie poza aplikację logiki](#create-function-external), lub [z wewnątrz aplikacji logiki](#create-function-designer) w Projektancie aplikacji logiki.

  Nowych i istniejących aplikacji funkcji i funkcji mają te same wymagania dotyczące pracy z usługą logic apps:

  * Aplikacja funkcji musi mieć tej samej subskrypcji platformy Azure jako swoją aplikację logiki.

  * Na przykład wyzwalacz HTTP używa funkcji, **wyzwalacza HTTP** szablon funkcji **JavaScript** lub **C#**. 

    Szablon wyzwalacza HTTP może akceptować zawartość, która ma `application/json` typu z aplikacji logiki. 
    Po dodaniu funkcji platformy Azure do aplikacji logiki Projektant aplikacji logiki zawiera funkcje niestandardowe utworzone na podstawie tego szablonu w ramach subskrypcji platformy Azure. 

  * Funkcja nie używa trasy niestandardowe, chyba że zostały zdefiniowane [definicji interfejsu OpenAPI](../azure-functions/functions-openapi-definition.md), wcześniej znany jako [plik struktury Swagger](https://swagger.io/). 
  
  * Jeśli zdefiniowano definicji interfejsu OpenAPI dla funkcji, Projektant aplikacji logiki zapewnia bardziej rozbudowane środowisko do pracy z parametrów funkcji. Zanim aplikacja logiki można znaleźć i dostęp do funkcji, które mają definicji interfejsu OpenAPI [Konfigurowanie aplikacji funkcji, wykonując następujące czynności](#function-swagger).

* Aplikacja logiki gdzie chcesz dodać funkcję, w tym [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako pierwszy krok w aplikacji logiki 

  Przed dodaniem akcje, które można uruchomić funkcji, aplikacja logiki musi rozpoczynać wyzwalacza.

  Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Utwórz swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-function-external"></a>

## <a name="create-functions-outside-logic-apps"></a>Tworzenie funkcji aplikacji logiki poza

W [witryny Azure portal](https://portal.azure.com), Utwórz aplikację funkcji platformy Azure, która musi mieć tej samej subskrypcji platformy Azure jako swoją aplikację logiki, a następnie utwórz funkcji platformy Azure.
Jeśli dopiero zaczynasz Tworzenie funkcji platformy Azure, Dowiedz się, jak [tworzenie pierwszej funkcji w witrynie Azure portal](../azure-functions/functions-create-first-azure-function.md), ale należy pamiętać, następujące wymagania w celu tworzenia funkcji, które można wywoływać z aplikacji logiki:

* Upewnij się, możesz wybrać **wyzwalacza HTTP** szablonu funkcji dla dowolnego **JavaScript** lub **C#**.

  ![Wyzwalacz HTTP - JavaScript i C#](./media/logic-apps-azure-functions/http-trigger-function.png)

<a name="function-swagger"></a>

* Opcjonalnie Jeśli użytkownik [Generowanie definicji interfejsu API](../azure-functions/functions-openapi-definition.md), wcześniej znany jako [plik struktury Swagger](https://swagger.io/), funkcji, można uzyskać więcej możliwości podczas pracy z parametrów funkcji w Projektancie aplikacji logiki. Aby skonfigurować aplikację funkcji, aplikacja logiki może znaleźć i użyć funkcji, które mają opisy struktury Swagger, wykonaj następujące kroki:

  1. Upewnij się, że aplikacja funkcji jest aktywnie uruchomiona.

  2. W aplikacji funkcji, skonfiguruj [udostępniania zasobów między źródłami (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) więc wszystkie źródła są dozwolone, wykonując następujące czynności:

     1. Z **aplikacje funkcji** , wybierz aplikację funkcji na liście > **funkcje platformy** > **CORS**.

        ![Wybierz aplikację funkcji > "Funkcje platformy" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

     2. W obszarze **CORS**, Dodaj `*` symboli wieloznacznych znaków, ale Usuń wszystkie inne źródła z listy, a następnie wybierz **Zapisz**.

        ![Ustaw "CORS * znak symbolu wieloznacznego" * "](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

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

1. W [witryny Azure portal](https://portal.azure.com), Otwórz aplikację logiki w Projektancie aplikacji logiki. 

2. Aby utworzyć i dodać funkcję, wykonaj krok, który ma zastosowanie do danego scenariusza:

   * W obszarze ostatni krok w przepływie pracy aplikacji logiki, wybierz opcję **nowy krok**.

   * Istniejące kroków w przepływie pracy aplikacji logiki, przesuń wskaźnik myszy nad strzałkę, wybierz znak plus (+) Zaloguj się, a następnie wybierz **Dodaj akcję**.

3. W polu wyszukiwania wprowadź "azure functions" jako filtr.
Z listy akcji wybierz następującą akcję: **Wybierz funkcję platformy Azure — Azure Functions** 

   ![Znajdź "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Z listy aplikacji funkcji wybierz aplikację funkcji. Po otwarciu listy akcji wybierz następującą akcję: **Usługa Azure Functions — Tworzenie nowej funkcji**

   ![Wybierz aplikację funkcji](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. W edytorze definicji funkcji należy zdefiniować funkcję:

   1. W **nazwy funkcji** Podaj nazwę funkcji. 

   2. W **kodu** Dodaj swój kod szablonu funkcji, łącznie z odpowiedzi i ładunku ma zwrócony do aplikacji logiki po zakończeniu funkcji. 

      ![Definiowanie funkcji](./media/logic-apps-azure-functions/function-definition.png)

      W kodzie szablonu  *`context` obiektu* odwołuje się do wiadomości, że Twoja aplikacja logiki wysyła za pośrednictwem **treść żądania** pola w późniejszym kroku. 
      Aby uzyskać dostęp do `context` obiektu właściwości z wewnątrz funkcji, należy użyć następującej składni: 

      `context.body.<property-name>`

      Na przykład, aby dokumentacja `content` właściwości wewnątrz `context` obiektu, należy użyć następującej składni: 

      `context.body.content`

      Zawiera również kod szablonu `input` zmienną, która przechowuje wartość z `data` parametru, więc funkcja mogą wykonywać operacje na tę wartość. 
      Wewnątrz funkcji JavaScript `data` zmienna jest również skrót dla `context.body`.

      > [!NOTE]
      > `body` Tutaj właściwość ma zastosowanie do `context` obiektu i nie jest taka sama jak **treści** token z akcji danych wyjściowych, która również może zostać przekazany do funkcji. 
 
   3. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

6. W **treść żądania** Podaj wejściowych funkcji, które muszą być sformatowane jako obiekt JavaScript Object Notation (JSON). 

   Jest to wejściowy *obiektu kontekstu* lub wiadomości, które Twoja aplikacja logiki wysyła do funkcji. Po kliknięciu **treść żądania** pola listy zawartości dynamicznej pojawia się, można wybrać tokenów dla dane wyjściowe z poprzednich kroków. W tym przykładzie określa, że ładunek kontekst zawiera właściwość o nazwie `content` zawierający **z** tokenu użytkownika wartości dla wyzwalacza poczty e-mail:

   !["Treść żądania" przykład — kontekst obiekt ładunku](./media/logic-apps-azure-functions/function-request-body-example.png)

   W tym miejscu jako ciąg nie jest rzutowanie obiektu context, aby zawartości obiektu pobiera dodać bezpośrednio do ładunku JSON. Jednakże gdy obiekt kontekstu nie jest token JSON, który przekazuje ciąg, obiekt JSON lub tablicę JSON, wystąpi błąd. Dlatego w tym przykładzie użyto **Received Time** tokenu zamiast tego można rzutować obiektu context jako ciąg znaków, dodając w podwójny cudzysłów:  

   ![Rzutowanie obiektu jako ciąg](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

7. Aby określić inne szczegóły, takie jak metoda do użycia, nagłówki żądania lub parametrów zapytania, wybierz **Pokaż opcje zaawansowane**.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Dodaj istniejące funkcje do usługi logic apps

Wywołać istniejących funkcji platformy Azure z poziomu aplikacji usługi logic apps, można dodać usługi Azure functions, takich jak każdą inną czynność w Projektancie aplikacji logiki. 

1. W [witryny Azure portal](https://portal.azure.com), Otwórz aplikację logiki w Projektancie aplikacji logiki. 

2. W obszarze danego kroku, w której chcesz dodać funkcję, wybierz opcję **nowy krok** > **Dodaj akcję**. 

3. W polu wyszukiwania wprowadź "azure functions" jako filtr.
Z listy akcji wybierz następującą akcję: **Wybierz funkcję platformy Azure — Azure Functions** 

   ![Znajdź "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Z listy aplikacji funkcji wybierz aplikację funkcji. Gdy zostanie wyświetlony na liście funkcji, wybierz funkcję. 

   ![Wybierz aplikację funkcji i funkcji platformy Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Dla funkcji, definicji interfejsu API (opisy struktury Swagger), które zostały [Konfigurowanie, dzięki czemu Twoja aplikacja logiki może znajdować i dostęp do tych funkcji](#function-swagger), możesz wybrać **akcje programu Swagger**:

   ![Wybierz Twojej aplikacji funkcji, "Swagger akcje" "i funkcji platformy Azure](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

5. W **treść żądania** Podaj wejściowych funkcji, które muszą być sformatowane jako obiekt JavaScript Object Notation (JSON). 

   Jest to wejściowy *obiektu kontekstu* lub wiadomości, które Twoja aplikacja logiki wysyła do funkcji. Po kliknięciu **treść żądania** pola listy zawartości dynamicznej pojawia się, można wybrać tokenów dla dane wyjściowe z poprzednich kroków. W tym przykładzie określa, że ładunek kontekst zawiera właściwość o nazwie `content` zawierający **z** tokenu użytkownika wartości dla wyzwalacza poczty e-mail:

   !["Treść żądania" przykład — kontekst obiekt ładunku](./media/logic-apps-azure-functions/function-request-body-example.png)

   W tym miejscu jako ciąg nie jest rzutowanie obiektu context, aby zawartości obiektu pobiera dodać bezpośrednio do ładunku JSON. Jednakże gdy obiekt kontekstu nie jest token JSON, który przekazuje ciąg, obiekt JSON lub tablicę JSON, wystąpi błąd. Dlatego w tym przykładzie użyto **Received Time** tokenu zamiast tego można rzutować obiektu context jako ciąg znaków, dodając w podwójny cudzysłów: 

   ![Rzutowanie obiektu jako ciąg](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. Aby określić inne szczegóły, takie jak metoda do użycia, nagłówki żądania lub parametrów zapytania, wybierz **Pokaż opcje zaawansowane**.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Wywoływanie aplikacji logiki z funkcji

Chcesz wyzwalacza aplikacji logiki od wewnątrz funkcji platformy Azure, aplikacja logiki musi rozpoczynać wyzwalacz, który udostępnia punkt końcowy, możliwy do wywołania. Na przykład, można uruchomić aplikacji logiki z **HTTP**, **żądania**, **Azure Queues**, lub **usługi Event Grid** wyzwalacza. Wewnątrz funkcji Wyślij żądanie HTTP POST do adresu URL wyzwalacza i Uwzględnij ładunek, chcesz, aby ta aplikacja logiki przetwarzania. Aby uzyskać więcej informacji, zobacz [wywołania wyzwalacza lub zagnieżdżanie aplikacji logiki](../logic-apps/logic-apps-http-endpoint.md). 

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [łączników Logic Apps](../connectors/apis-list.md)
