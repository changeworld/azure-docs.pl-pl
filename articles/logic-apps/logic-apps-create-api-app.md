---
title: Tworzenie interfejsów API sieci Web & interfejsów API REST dla aplikacji logiki azure
description: Tworzenie interfejsów API sieci Web & interfejsów API REST w celu wywoływania interfejsów API, usług lub systemów w celu integracji systemu w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 05/26/2017
ms.openlocfilehash: bb6c99ea12e5b53631d42a04b36b7bfef2337e42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270539"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Tworzenie niestandardowych interfejsów API, które można wywołać z usługi Azure Logic Apps

Mimo że usługa Azure Logic Apps oferuje [setki łączników,](../connectors/apis-list.md) których można używać w przepływach pracy aplikacji logiki, można wywołać interfejsy API, systemy i usługi, które nie są dostępne jako łączniki. Można utworzyć własne interfejsy API, które zapewniają akcje i wyzwalacze do użycia w aplikacjach logiki. Oto inne powody, dla których warto utworzyć własne interfejsy API, które można wywołać z przepływów pracy aplikacji logiki:

* Rozszerz bieżącą integrację systemu i przepływy pracy integracji danych.
* Pomóż klientom korzystać z usługi w zarządzaniu zadaniami zawodowymi lub osobistymi.
* Zwiększ zasięg, wykrywalność i użyj do swojej usługi.

Zasadniczo łączniki są interfejsami API sieci web, które używają REST dla interfejsów podłączanych, [format metadanych Swagger](https://swagger.io/specification/) dla dokumentacji i JSON jako format wymiany danych. Ponieważ łączniki są interfejsami API REST, które komunikują się za pośrednictwem punktów końcowych HTTP, można użyć dowolnego języka, takiego jak .NET, Java, Python lub Node.js, do tworzenia łączników. Interfejsy API można również hostować w [usłudze Azure App Service](../app-service/overview.md)— ofercie platformy jako usługi (PaaS), która zapewnia jeden z najlepszych, najłatwiejszych i najbardziej skalowalnych sposobów hostingu interfejsu API. 

Aby niestandardowe interfejsy API działały z aplikacjami logiki, interfejs API może udostępniać [*akcje*](./logic-apps-overview.md#logic-app-concepts) wykonujące określone zadania w przepływach pracy aplikacji logiki. Interfejs API może również działać jako [*wyzwalacz,*](./logic-apps-overview.md#logic-app-concepts) który uruchamia przepływ pracy aplikacji logiki, gdy nowe dane lub zdarzenie spełnia określony warunek. W tym temacie opisano typowe wzorce, które można wykonać do tworzenia akcji i wyzwalaczy w interfejsie API, na podstawie zachowania, które ma zapewnić interfejs API.

Interfejsy API można hostować w [usłudze Azure App Service](../app-service/overview.md)— ofercie platformy jako usługi (PaaS), która zapewnia wysoce skalowalny, łatwy hosting interfejsu API.

> [!TIP] 
> Mimo że interfejsy API można wdrażać jako aplikacje sieci Web, należy rozważyć wdrożenie interfejsów API jako aplikacji interfejsu API, co może ułatwić zadanie podczas tworzenia, hostowania i korzystania z interfejsów API w chmurze i lokalnie. Nie trzeba zmieniać żadnego kodu w interfejsach API — wystarczy wdrożyć kod do aplikacji interfejsu API. Na przykład dowiedz się, jak tworzyć aplikacje interfejsu API utworzone za pomocą następujących języków: 
> 
> * [ASP.NET](../app-service/app-service-web-get-started-dotnet.md). 
> * [Java](../app-service/app-service-web-get-started-java.md)
> * [Node.js](../app-service/app-service-web-get-started-nodejs.md)
> * [PHP](../app-service/app-service-web-get-started-php.md)
> * [Python](../app-service/containers/quickstart-python.md)
> * [Ruby](../app-service/containers/quickstart-ruby.md)
>
> Aby uzyskać przykłady aplikacji interfejsu API utworzone dla aplikacji logiki, [blog](https://aka.ms/logicappsblog)odwiedź [repozytorium lub blog aplikacji Usługi Azure Logic Apps GitHub.](https://github.com/logicappsio)

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Czym różnią się niestandardowe interfejsy API od łączników niestandardowych?

Niestandardowe interfejsy API i [łączniki niestandardowe](../logic-apps/custom-connector-overview.md) są interfejsami API sieci Web, które używają REST dla interfejsów podłączanych, [formatu metadanych Swagger](https://swagger.io/specification/) dla dokumentacji i JSON jako formatu wymiany danych. A ponieważ te interfejsy API i łączniki są interfejsami API REST, które komunikują się za pośrednictwem punktów końcowych HTTP, można użyć dowolnego języka, takiego jak .NET, Java, Python lub Node.js, do tworzenia niestandardowych interfejsów API i łączników.

Niestandardowe interfejsy API umożliwiają wywoływanie interfejsów API, które nie są łącznikami, i zapewniają punkty końcowe, które można wywołać za pomocą protokołu HTTP + Swagger, usługi Azure API Management lub usług app services. Łączniki niestandardowe działają jak niestandardowe interfejsy API, ale mają również następujące atrybuty:

* Zarejestrowane jako zasoby łącznika aplikacji logiki na platformie Azure.
* Pojawiają się z ikonami obok łączników zarządzanych przez firmę Microsoft w Projektancie aplikacji logiki.
* Dostępne tylko dla autorów łączników i użytkowników aplikacji logiki, którzy mają tę samą dzierżawę usługi Azure Active Directory i subskrypcję platformy Azure w regionie, w którym są wdrażane aplikacje logiki.

Można również nominować zarejestrowane łączniki do certyfikacji firmy Microsoft. Ten proces sprawdza, czy zarejestrowane łączniki spełniają kryteria użytku publicznego i udostępnia te łączniki użytkownikom w programach Power Automate i Microsoft Power Apps.

Aby uzyskać więcej informacji na temat łączników niestandardowych, zobacz 

* [Omówienie łączników niestandardowych](../logic-apps/custom-connector-overview.md)
* [Tworzenie łączników niestandardowych z interfejsów API sieci Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Rejestrowanie łączników niestandardowych w usłudze Azure Logic Apps](../logic-apps/logic-apps-custom-connector-register.md)

## <a name="helpful-tools"></a>Pomocne narzędzia

Niestandardowy interfejs API działa najlepiej z aplikacjami logiki, gdy interfejs API ma również [dokument Swagger,](https://swagger.io/specification/) który opisuje operacje i parametry interfejsu API.
Wiele bibliotek, takich jak [Swashbuckle,](https://github.com/domaindrivendev/Swashbuckle)może automatycznie wygenerować plik Swagger dla Ciebie. Aby dodawać adnotacje do pliku Swagger dla nazw wyświetlanych, typów właściwości i tak dalej, można również użyć [TRex,](https://github.com/nihaue/TRex) dzięki czemu plik Swagger działa dobrze z aplikacjami logiki.

<a name="actions"></a>

## <a name="action-patterns"></a>Wzorce akcji

Aby aplikacje logiki wykonywały zadania, niestandardowy interfejs API powinien udostępniać [*akcje*](./logic-apps-overview.md#logic-app-concepts). Każda operacja w interfejsie API jest mapowana na akcję. Podstawową akcją jest kontroler, który akceptuje żądania HTTP i zwraca odpowiedzi HTTP. Na przykład aplikacja logiki wysyła żądanie HTTP do aplikacji sieci web lub aplikacji interfejsu API. Następnie aplikacja zwraca odpowiedź HTTP wraz z zawartością, którą aplikacja logiki może przetwarzać.

Dla akcji standardowej można napisać metodę żądania HTTP w interfejsie API i opisać tę metodę w pliku Swagger. Następnie można wywołać interfejs API bezpośrednio z [akcji HTTP](../connectors/connectors-native-http.md) lub HTTP [+ Swagger](../connectors/connectors-native-http-swagger.md) akcji. Domyślnie odpowiedzi muszą być zwracane w [terminie żądania](./logic-apps-limits-and-config.md). 

![Standardowy wzorzec akcji](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a>Aby aplikacja logiki czekać, gdy interfejs API kończy zadania o dłuższym uruchomieniu, interfejs API można wykonać [wzorzec sondowania asynchronicznie](#async-pattern) lub [wzorzec asynchroniczne gołągówka](#webhook-actions) sieci Web opisane w tym temacie. Dla analogii, która pomaga wizualizować różne zachowania tych wzorców, wyobraź sobie proces zamawiania niestandardowego ciasta z piekarni. Wzorzec sondowania odzwierciedla zachowanie, w którym dzwonisz do piekarni co 20 minut, aby sprawdzić, czy ciasto jest gotowe. Wzór webhook odzwierciedla zachowanie, w którym piekarnia prosi o numer telefonu, aby mogli zadzwonić do Ciebie, gdy ciasto jest gotowe.

Aby uzyskać przykłady, odwiedź [repozytorium Aplikacji logiki GitHub](https://github.com/logicappsio). Dowiedz się również więcej o [pomiarze użycia dla działań](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Wykonywanie długotrwałych zadań za pomocą wzorca akcji sondowania

Aby interfejs API wykonywał zadania, które mogą być uruchamiane dłużej niż [limit czasu żądania,](./logic-apps-limits-and-config.md)można użyć wzorca sondowania asynchroniczne. Ten wzorzec ma interfejs API do pracy w oddzielnym wątku, ale zachować aktywne połączenie z aparatem aplikacji logiki. W ten sposób aplikacja logiki nie limit czasu lub kontynuować następny krok w przepływie pracy przed zakończeniem pracy interfejsu API.

Oto ogólny wzór:

1. Upewnij się, że aparat wie, że interfejs API zaakceptował żądanie i rozpoczął pracę.
2. Gdy aparat sprawia, że kolejne żądania o stan zadania, poinformuj aparat wiedzieć, po zakończeniu zadania interfejsu API.
3. Zwraca odpowiednie dane do aparatu, aby przepływ pracy aplikacji logiki można kontynuować.

<a name="bakery-polling-action"></a>Teraz zastosuj poprzednią analogię piekarni do wzoru sondowania i wyobraź sobie, że dzwonisz do piekarni i zamawiasz niestandardowe ciasto do dostawy. Proces tworzenia ciasta wymaga czasu, a ty nie chcesz czekać przez telefon, podczas gdy piekarnia pracuje na torcie. Piekarnia potwierdza twoje zamówienie i dzwoni co 20 minut, aby uzyskać status ciasta. Po 20 minutach dzwonisz do piekarni, ale mówią ci, że twoje ciasto nie jest zrobione i że powinieneś zadzwonić w ciągu kolejnych 20 minut. Ten proces w tył i w tył trwa do momentu połączenia, a piekarnia informuje, że twoje zamówienie jest gotowe i dostarcza ciasto. 

Więc zamapujmy ten wzorzec sondowania z powrotem. Piekarnia reprezentuje niestandardowy interfejs API, podczas gdy ty, klient ciasta, reprezentuje aparat aplikacji logiki. Gdy aparat wywołuje interfejs API z żądaniem, interfejs API potwierdza żądanie i odpowiada z interwałem czasu, kiedy aparat może sprawdzić stan zadania. Aparat kontynuuje sprawdzanie stanu zadania, dopóki interfejs API nie odpowie, że zadanie zostało wykonane i zwraca dane do aplikacji logiki, która następnie kontynuuje przepływ pracy. 

![Wzorzec akcji sondowania](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Oto konkretne kroki, które należy wykonać w interfejsie API, opisane z perspektywy interfejsu API:

1. Gdy interfejs API pobiera żądanie HTTP, aby `202 ACCEPTED` rozpocząć pracę, natychmiast zwrócić odpowiedź HTTP z nagłówkiem opisanym `location` w dalszej części tego kroku. Ta odpowiedź pozwala aparat aplikacji logiki wiedzieć, że interfejs API dostał żądanie, zaakceptował ładunek żądania (dane wejściowe) i jest teraz przetwarzania. 
   
   Odpowiedź `202 ACCEPTED` powinna zawierać następujące nagłówki:
   
   * *Wymagane:* `location` Nagłówek określający ścieżkę bezwzględną do adresu URL, w którym aparat aplikacji logiki może sprawdzić stan zadania interfejsu API

   * *Opcjonalnie:* `retry-after` Nagłówek określający liczbę sekund, przez jaką aparat `location` powinien czekać przed sprawdzeniem adresu URL pod kątem stanu zadania. 

     Domyślnie aparat sprawdza co 20 sekund. Aby określić inny interwał, należy uwzględnić `retry-after` nagłówek i liczbę sekund do następnej ankiety.

2. Po upływie określonego czasu aparat aplikacji `location` logiki sonduje adres URL, aby sprawdzić stan zadania. Interfejs API powinien przeprowadzać te kontrole i zwracać te odpowiedzi:
   
   * Jeśli zadanie zostało wykonane, `200 OK` zwróć odpowiedź HTTP wraz z ładunkiem odpowiedzi (dane wejściowe dla następnego kroku).

   * Jeśli zadanie jest nadal przetwarzane, `202 ACCEPTED` zwróć inną odpowiedź HTTP, ale z tymi samymi nagłówkami co oryginalna odpowiedź.

Gdy interfejs API jest zgodny z tym wzorcem, nie trzeba nic robić w definicji przepływu pracy aplikacji logiki, aby kontynuować sprawdzanie stanu zadania. Gdy aparat pobiera `202 ACCEPTED` odpowiedź HTTP `location` i prawidłowy nagłówek, aparat jest zgodny z `location` wzorcem asynchronii i sprawdza nagłówek, dopóki interfejs API nie zwróci odpowiedzi innych niż 202.

> [!TIP]
> Na przykład wzorzec asynchroniczne, przejrzyj ten [przykład odpowiedzi kontrolera asynchronii w usłudze GitHub.](https://github.com/logicappsio/LogicAppsAsyncResponseSample)

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Wykonywanie długotrwałych zadań za pomocą wzorca akcji elementu webhook

Alternatywnie można użyć wzorca elementu webhook do długotrwałych zadań i przetwarzania asynchroniczne. Ten wzorzec ma wstrzymanie aplikacji logiki i czekać na "wywołania zwrotnego" z interfejsu API, aby zakończyć przetwarzanie przed kontynuowaniem przepływu pracy. To wywołanie zwrotne jest wpisem HTTP, który wysyła wiadomość do adresu URL, gdy zdarzenie się dzieje. 

<a name="bakery-webhook-action"></a>Teraz zastosuj poprzednią analogię piekarni do wzoru webhook i wyobraź sobie, że dzwonisz do piekarni i zamawiasz niestandardowe ciasto do dostawy. Proces tworzenia ciasta wymaga czasu, a ty nie chcesz czekać przez telefon, podczas gdy piekarnia pracuje na torcie. Piekarnia potwierdza twoje zamówienie, ale tym razem podajesz im swój numer telefonu, aby mogli zadzwonić do Ciebie po zakończeniu ciasta. Tym razem piekarnia informuje, kiedy zamówienie jest gotowe i dostarcza ciasto.

Gdy mapujemy ten wzorzec elementu webhook z powrotem, piekarnia reprezentuje niestandardowy interfejs API, podczas gdy Ty, klient ciasta, reprezentuje aparat aplikacji logiki. Aparat wywołuje interfejs API z żądaniem i zawiera adres URL "wywołania zwrotnego".
Po zakończeniu zadania interfejs API używa adresu URL do powiadamiania aparatu i zwracania danych do aplikacji logiki, która następnie kontynuuje przepływ pracy. 

W przypadku tego wzorca należy skonfigurować `subscribe` dwa punkty końcowe na kontrolerze:`unsubscribe`

*  `subscribe`punkt końcowy: Gdy wykonanie osiągnie akcję interfejsu API w przepływie pracy, aparat aplikacji logiki wywołuje punkt `subscribe` końcowy. Ten krok powoduje, że aplikacja logiki, aby utworzyć adres URL wywołania zwrotnego, który przechowuje interfejs API, a następnie czekać na wywołanie zwrotne z interfejsu API po zakończeniu pracy. Interfejs API następnie wywołuje z powrotem z HTTP POST do adresu URL i przekazuje wszystkie zwrócone treści i nagłówki jako dane wejściowe do aplikacji logiki.

* `unsubscribe`punkt końcowy: Jeśli uruchomienie aplikacji logiki zostanie anulowane, aparat aplikacji logiki wywołuje punkt `unsubscribe` końcowy. Interfejs API może następnie wyrejestrować adres URL wywołania zwrotnego i zatrzymać wszystkie procesy w razie potrzeby.

![Wzorzec akcji elementu webhook](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> Obecnie projektant aplikacji logiki nie obsługuje odnajdywania punktów końcowych elementu webhook za pośrednictwem funkcji Swagger. Dlatego w przypadku tego wzorca należy dodać akcję [ **elementu Webhook** ](../connectors/connectors-native-webhook.md) i określić adres URL, nagłówki i treść żądania. Zobacz też [Akcje przepływu pracy i wyzwalacze](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Aby przekazać adres URL wywołania zwrotnego, można użyć funkcji przepływu `@listCallbackUrl()` pracy w dowolnym z poprzednich pól w razie potrzeby.

> [!TIP]
> Przykładowy wzorzec elementu webhook przejrzyj ten [przykład wyzwalacza elementu webhook w usłudze GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Wzorce wyzwalania

Niestandardowy interfejs API może działać jako [*wyzwalacz,*](./logic-apps-overview.md#logic-app-concepts) który uruchamia aplikację logiki, gdy nowe dane lub zdarzenie spełnia określony warunek. Ten wyzwalacz można regularnie sprawdzać lub czekać i nasłuchiwają na nowe dane lub zdarzenia w punkcie końcowym usługi. Jeśli nowe dane lub zdarzenie spełnia określony warunek, wyzwalacz uruchamia i uruchamia aplikację logiki, która nasłuchuje tego wyzwalacza. Aby uruchomić aplikacje logiki w ten sposób, interfejs API można wykonać [*wyzwalacza sondowania*](#polling-triggers) lub wzorzec [*wyzwalacza elementu webhook.*](#webhook-triggers) Te wzorce są podobne do ich odpowiedników dla [akcji sondowania](#async-pattern) i [akcji webhook](#webhook-actions). Dowiedz się więcej o [pomiarze użycia wyzwalaczy.](logic-apps-pricing.md)

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Regularnie sprawdzaj nowe dane lub zdarzenia za pomocą wzorca wyzwalacza sondowania

*Wyzwalacz sondowania* działa podobnie jak [akcja sondowania](#async-pattern) wcześniej opisane w tym temacie. Aparat aplikacji logiki okresowo wywołuje i sprawdza punkt końcowy wyzwalacza pod kątem nowych danych lub zdarzeń. Jeśli aparat znajdzie nowe dane lub zdarzenie, które spełnia określony warunek, wyzwalacz zostanie uruchomiony. Następnie aparat tworzy wystąpienie aplikacji logiki, która przetwarza dane jako dane wejściowe. 

![Sondowanie wzorca wyzwalacza](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Każde żądanie sondowania liczy się jako wykonanie akcji, nawet wtedy, gdy nie jest tworzone żadne wystąpienie aplikacji logiki. Aby zapobiec przetwarzaniu tych samych danych wiele razy, wyzwalacz należy oczyścić dane, które zostały już odczytane i przekazane do aplikacji logiki.

Oto konkretne kroki wyzwalacza sondowania, opisane z perspektywy interfejsu API:

| Znaleziono nowe dane lub zdarzenie?  | Odpowiedź interfejsu API | 
| ------------------------- | ------------ |
| Znaleziono | Zwraca stan `200 OK` HTTP z ładunkiem odpowiedzi (dane wejściowe dla następnego kroku). <br/>Ta odpowiedź tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy. | 
| Nie znaleziono | Zwraca stan `202 ACCEPTED` HTTP `location` z nagłówkiem i nagłówkiem. `retry-after` <br/>W przypadku wyzwalaczy `location` nagłówek powinien `triggerState` również zawierać parametr zapytania, który jest zwykle "sygnaturą czasową". Interfejs API można użyć tego identyfikatora do śledzenia ostatniego czasu, że aplikacja logiki został wyzwolony. | 
||| 

Na przykład, aby okresowo sprawdzać usługę pod kątem nowych plików, można utworzyć wyzwalacz sondowania, który ma następujące zachowania:

| Wniosek `triggerState`zawiera ? | Odpowiedź interfejsu API | 
| -------------------------------- | -------------| 
| Nie | Zwróć stan `202 ACCEPTED` HTTP `location` oraz `triggerState` nagłówek z ustawionym `retry-after` na bieżący czas i interwałem do 15 sekund. | 
| Tak | Sprawdź usługę pod kątem `DateTime` plików `triggerState`dodanych po for . | 
||| 

| Liczba znalezionych plików | Odpowiedź interfejsu API | 
| --------------------- | -------------| 
| Pojedynczy plik | Zwróć stan `200 OK` HTTP i ładunek `triggerState` zawartości, `DateTime` zaktualizuj `retry-after` do zwróconego pliku i ustaw interwał na 15 sekund. | 
| Wiele plików | Zwróć jeden plik naraz `200 OK` i stan `triggerState`HTTP, `retry-after` zaktualizuj i ustaw interwał na 0 sekund. </br>Te kroki pozwalają aparat wiedzieć, że więcej danych jest dostępna i że aparat `location` powinien natychmiast zażądać danych z adresu URL w nagłówku. | 
| Brak plików | Zwróć stan `202 ACCEPTED` HTTP, nie `triggerState`zmieniaj i `retry-after` ustaw interwał na 15 sekund. | 
||| 

> [!TIP]
> Przykładowy wzorzec wyzwalacza sondowania przejrzyj przykład [tego przykładu wyzwalacza sondy w usłudze GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Czekaj i nasłuchuj nowych danych lub zdarzeń za pomocą wzorca wyzwalacza elementu webhook

Wyzwalacz elementu webhook jest *wyzwalaczem wypychaniem,* który czeka i nasłuchuje nowych danych lub zdarzeń w punkcie końcowym usługi. Jeśli nowe dane lub zdarzenie spełnia określony warunek, wyzwalacz uruchamia i tworzy wystąpienie aplikacji logiki, które następnie przetwarza dane jako dane wejściowe.
Wyzwalacze elementu webhook działają podobnie do [akcji elementu webhook opisanych](#webhook-actions) `subscribe` wcześniej `unsubscribe` w tym temacie i są skonfigurowane z punktami końcowymi i punktami końcowymi. 

* `subscribe`punkt końcowy: Po dodaniu i zapisaniu wyzwalacza elementu webhook `subscribe` w aplikacji logiki aparat aplikacji logiki wywołuje punkt końcowy. Ten krok powoduje, że aplikacja logiki, aby utworzyć adres URL wywołania zwrotnego, który przechowuje interfejs API. Gdy pojawią się nowe dane lub zdarzenie, które spełnia określony warunek, interfejs API wywołuje z adresem URL wpis http. Ładunek zawartości i nagłówki przechodzą jako dane wejściowe do aplikacji logiki.

* `unsubscribe`punkt końcowy: Jeśli wyzwalacz elementu webhook lub cała aplikacja `unsubscribe` logiki zostanie usunięta, aparat aplikacji logiki wywołuje punkt końcowy. Interfejs API może następnie wyrejestrować adres URL wywołania zwrotnego i zatrzymać wszystkie procesy w razie potrzeby.

![Wzorzec wyzwalacza elementu Webhook](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> Obecnie projektant aplikacji logiki nie obsługuje odnajdywania punktów końcowych elementu webhook za pośrednictwem funkcji Swagger. Dlatego w przypadku tego wzorca należy dodać [wyzwalacz **elementu webhook** ](../connectors/connectors-native-webhook.md) i określić adres URL, nagłówki i treść żądania. Zobacz też [wyzwalacz HTTPWebhook](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Aby przekazać adres URL wywołania zwrotnego, można użyć funkcji przepływu `@listCallbackUrl()` pracy w dowolnym z poprzednich pól w razie potrzeby.
>
> Aby zapobiec przetwarzaniu tych samych danych wiele razy, wyzwalacz należy oczyścić dane, które zostały już odczytane i przekazane do aplikacji logiki.

> [!TIP]
> Przykładowy wzorzec elementu webhook przejrzyj ten [przykład kontrolera wyzwalacza elementu webhook w usłudze GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

## <a name="improve-security-for-calls-to-your-apis-from-logic-apps"></a>Zwiększ bezpieczeństwo połączeń z interfejsami API z aplikacji logiki

Po utworzeniu niestandardowych interfejsów API skonfiguruj uwierzytelnianie dla interfejsów API, aby można było bezpiecznie wywoływać je z aplikacji logiki. Dowiedz [się, jak zwiększyć bezpieczeństwo połączeń z niestandardowymi interfejsami API z aplikacji logiki.](../logic-apps/logic-apps-custom-api-authentication.md)

## <a name="deploy-and-call-your-apis"></a>Wdrażanie i wywoływanie interfejsów API

Po skonfigurowaniu uwierzytelniania skonfiguruj wdrożenie interfejsów API. Dowiedz [się, jak wdrażać i wywoływać niestandardowe interfejsy API z aplikacji logiki](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Publikowanie niestandardowych interfejsów API na platformie Azure

Aby udostępnić niestandardowe interfejsy API dla innych użytkowników aplikacji logiki na platformie Azure, należy dodać zabezpieczenia i zarejestrować je jako łączniki aplikacji logiki. Aby uzyskać więcej informacji, zobacz [Omówienie łączników niestandardowych](../logic-apps/custom-connector-overview.md). 

Aby niestandardowe interfejsy API były dostępne dla wszystkich użytkowników w aplikacjach Logika, Automatyzacja zasilania i Microsoft Power Apps, należy dodać zabezpieczenia, zarejestrować interfejsy API jako łączniki aplikacji logiki i wyznaczyć łączniki do [programu z certyfikatem platformy Microsoft Azure](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/). 

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Aby uzyskać konkretną pomoc dotyczącą [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com)niestandardowych interfejsów API, skontaktuj się z firmą .

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Aby pomóc w ulepszaniu usługi Logic Apps, przesyłaj pomysły lub głosuj na nie w [witrynie opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Następne kroki

* [Obsługa błędów i wyjątków](../logic-apps/logic-apps-exception-handling.md)
* [Wywoływanie, wyzwalanie lub zagęszanie aplikacji logiki z punktami końcowymi HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Pomiar użycia dla akcji i wyzwalaczy](../logic-apps/logic-apps-pricing.md)
