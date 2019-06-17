---
title: Tworzenie sieci web, interfejsów API i interfejsów API REST dla usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie sieci web, interfejsów API i interfejsów API REST, aby wywołać swoje interfejsy API, usług lub systemy integracji systemu w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.topic: article
ms.assetid: bd229179-7199-4aab-bae0-1baf072c7659
ms.date: 05/26/2017
ms.openlocfilehash: 620ede672d71338abeff5198fd5f94e92dc193d0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60681873"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Tworzenie niestandardowych interfejsów API można wywołać z usługi Azure Logic Apps

Mimo że usługi Azure Logic Apps oferuje [ponad 100 wbudowanych łączników](../connectors/apis-list.md) czy można używać w przepływach pracy aplikacji logiki, należy wywołać interfejsy API, systemów i usług, które nie są dostępne jako łączniki. Można tworzyć własnych interfejsów API, które zapewniają akcje i wyzwalacze do wykorzystania w aplikacjach logiki. W tym miejscu są też inne przyczyny, dlaczego warto tworzyć własnych interfejsów API, które można wywołać z przepływów pracy aplikacji logiki:

* Rozszerz bieżący system przepływy pracy integracji integracji i danych.
* Pomóż klientom Usługa do zarządzania zadaniami professional lub osobiste.
* Rozwiń zasięg, możliwości odnajdywania i użycie Twojej usługi.

Zasadniczo łączniki są internetowych interfejsów API, które przy użyciu architektury REST dla interfejsów podłączanych [formatu metadanych struktury Swagger](https://swagger.io/specification/) na potrzeby dokumentacji i dane JSON jako ich format wymiany danych. Łączniki są interfejsy API REST, które komunikują się za pośrednictwem punktów końcowych HTTP, można użyć dowolnego języka, takich jak .NET, Java lub Node.js do tworzenia łączników. Możesz również hostować interfejsy API na [usługi Azure App Service](../app-service/overview.md)platformy jako a- usługi (PaaS) zapewniający jednym ze sposobów najlepsze, najprostszy i najbardziej skalowalny interfejs API hostingu. 

W przypadku niestandardowych interfejsów API do pracy z usługą logic apps, interfejs API może zapewnić [ *akcje* ](./logic-apps-overview.md#logic-app-concepts) , wykonywania określonych zadań w przepływach pracy aplikacji logiki. Interfejs API mogą również działać jako [ *wyzwalacza* ](./logic-apps-overview.md#logic-app-concepts) , uruchamia przepływ pracy aplikacji logiki, gdy nowe dane lub zdarzenia spełnia określony warunek. W tym temacie opisano typowe wzorce, które możesz wykonać do tworzenia akcji i wyzwalaczy w interfejsie API, na podstawie zachowania, który chcesz, aby Twój interfejs API w celu zapewnienia.

Możesz hostować swoje interfejsy API na [usługi Azure App Service](../app-service/overview.md), platformy as-a-service (PaaS) oferty, zapewniająca o wysokim stopniu skalowalności, łatwe hostowanie interfejsu API.

> [!TIP] 
> Mimo że można wdrożyć swoje interfejsy API jako aplikacje sieci web, należy wziąć pod uwagę wdrażanie interfejsów API jako aplikacji interfejsu API, które mogą ułatwić zadania podczas Utwórz, Hostuj i używanie interfejsów API w chmurze i lokalnie. Nie trzeba zmieniać żadnego kodu w Twoich interfejsów API — wystarczy go wdrożyć swój kod w aplikacji interfejsu API. Na przykład Dowiedz się, jak tworzyć aplikacje API apps utworzone za pomocą następujących języków: 
> 
> * [ASP.NET](../app-service/app-service-web-get-started-dotnet.md). 
> * [Java](../app-service/app-service-web-get-started-java.md)
> * [Node.js](../app-service/app-service-web-get-started-nodejs.md)
> * [PHP](../app-service/app-service-web-get-started-php.md)
> * [Python](../app-service/containers/quickstart-python.md)
> * [Ruby](../app-service/containers/quickstart-ruby.md)
>
> Aby uzyskać przykłady aplikacji interfejsu API, stworzona z myślą o aplikacjach logiki, odwiedź stronę [repozytorium Azure Logic Apps w witrynie GitHub](https://github.com/logicappsio) lub [blogu](https://aka.ms/logicappsblog).

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Jak niestandardowe interfejsy API, różni się od łączników niestandardowych?

Niestandardowe interfejsy API i [łączników niestandardowych](../logic-apps/custom-connector-overview.md) są internetowych interfejsów API, które przy użyciu architektury REST dla interfejsów podłączanych [formatu metadanych struktury Swagger](https://swagger.io/specification/) na potrzeby dokumentacji i dane JSON jako ich format wymiany danych. A ponieważ tych interfejsów API i łączników są interfejsy API REST, które komunikują się za pośrednictwem punktów końcowych HTTP, można użyć dowolnego języka, takich jak .NET, Java lub Node.js do tworzenia niestandardowych interfejsów API i łączników.

Niestandardowe interfejsy API umożliwiają wywoływanie interfejsów API, które nie są łączniki i podaj punkty końcowe, które można wywoływać za pomocą protokołu HTTP + Swagger, usługi Azure API Management lub App Services. Łączniki niestandardowe działają podobnie jak niestandardowe interfejsy API, ale musi też mieć następujące atrybuty:

* Zarejestrowany jako łącznik usługi Logic Apps zasobów na platformie Azure.
* Wyświetlone z ikonami obok łączników zarządzanych przez firmę Microsoft w Projektancie aplikacji logiki.
* Dostępne tylko dla łączników autorzy i użytkowników aplikacji logiki, którzy mają tę samą dzierżawę usługi Azure Active Directory i subskrypcję platformy Azure w regionie, w których aplikacje logiki są wdrażane.

Można również wyznaczyć zarejestrowanych łączników do certyfikacji firmy Microsoft. Ten proces sprawdza, czy zarejestrowanych łączników spełniają kryteria do użytku publicznego i sprawia, że te łączniki dostępne dla użytkowników, Microsoft Flow i Microsoft PowerApps.

Aby uzyskać więcej informacji na temat łączników niestandardowych zobacz 

* [Omówienie łączników niestandardowych](../logic-apps/custom-connector-overview.md)
* [Tworzenie niestandardowych łączników z interfejsów API sieci Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Rejestrowanie łączników niestandardowych w usłudze Azure Logic Apps](../logic-apps/logic-apps-custom-connector-register.md)

## <a name="helpful-tools"></a>Przydatnych narzędzi

Niestandardowy interfejs API działa najlepiej z usługą logic apps, gdy interfejs API również ma [dokument struktury Swagger](https://swagger.io/specification/) opisujący operacje i parametry interfejsu API.
Wiele bibliotek, takich jak [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle), może automatycznie wygenerować plik struktury Swagger dla Ciebie. Dodawać adnotacje do pliku struktury Swagger dla wyświetlane nazwy, typy właściwości i tak dalej, można również użyć [TRex](https://github.com/nihaue/TRex) tak, aby plik struktury Swagger działa dobrze z usługą logic apps.

<a name="actions"></a>

## <a name="action-patterns"></a>Wzorce akcji

Dla usługi logic apps do wykonywania zadań, niestandardowego interfejsu API należy podać [ *akcje*](./logic-apps-overview.md#logic-app-concepts). Każda operacja w interfejsie API mapuje akcji. Akcja podstawowa jest kontroler, który akceptuje żądania HTTP i zwraca odpowiedzi HTTP. Na przykład aplikacja logiki wysyła żądanie HTTP do aplikacji sieci web lub aplikacji interfejsu API. Aplikacja następnie zwraca odpowiedź HTTP wraz z zawartością, która może przetwarzać aplikacji logiki.

Działania standardowe można napisać metodę żądania HTTP w interfejsie API i opisz tej metody w pliku struktury Swagger. Następnie możesz wywołać bezpośrednio przy użyciu interfejsu API [Akcja HTTP](../connectors/connectors-native-http.md) lub [protokołu HTTP + Swagger](../connectors/connectors-native-http-swagger.md) akcji. Domyślnie odpowiedzi muszą zostać zwrócone w ciągu [limit czasu żądania](./logic-apps-limits-and-config.md). 

![Wzorzec standardowych działań](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a> Aby utworzyć aplikację logiki, zaczekaj, aż zakończy się dłużej trwające zadań interfejsu API, można wykonać interfejsu API [wzorca asynchronicznego sondowania](#async-pattern) lub [wzorca asynchronicznego elementu webhook](#webhook-actions) opisane w tym temacie. Sposób analogiczny, która ułatwia wizualizację różnych zachowań tych wzorców Wyobraź sobie w proces porządkowania niestandardowe Tort z dla piekarni. Wzorzec sondowania odzwierciedla zachowanie, gdzie jest wywoływana dla piekarni co 20 minut, aby sprawdzić, czy Tort jest gotowy. Wzorzec elementu webhook odzwierciedla zachowanie, gdzie dla piekarni pyta o Twój numer telefonu, ich można wywołać, gdy Tort jest gotowy.

Aby uzyskać przykłady, odwiedź stronę [repozytorium GitHub aplikacje logiki](https://github.com/logicappsio). Ponadto Dowiedz się więcej o [zbierania danych użycia dla akcji](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Wykonywania długotrwałych zadań za pomocą wzorca akcji sondowania

Aby wykonywać zadania, które mogą być uruchamiane więcej niż interfejs API [limit czasu żądania](./logic-apps-limits-and-config.md), można użyć wzorca asynchronicznego sondowania. Ten wzorzec ma Twojego Zrób interfejsu API, które działa w oddzielnym wątku, ale zachować aktywne połączenie aparat usługi Logic Apps. Dzięki temu aplikacja logiki nie podlega limitowi czasu lub kontynuować do następnego kroku w przepływie pracy, zanim interfejsu API zakończy pracę.

Poniżej przedstawiono ogólny schemat:

1. Upewnij się, czy aparat zna zaakceptowane żądanie i uruchomić pracy interfejsu API.
2. Gdy aparat sprawia, że kolejne żądania dotyczące stanu zadania, umożliwiają aparatu wiadomo, kiedy Twój interfejs API kończy zadanie.
3. Zwraca odpowiednie dane do silnika, aby kontynuować przepływ pracy aplikacji logiki.

<a name="bakery-polling-action"></a> Teraz Zastosuj poprzedniego odpowiednio dla piekarni wzorcem sondowania i imagine wywołania dla piekarni i kolejność niestandardowych Tort dostarczania. Proces składania Tort czasochłonne, a nie chcesz czekać na telefonie, natomiast dla piekarni działa we Tort. Dla piekarni potwierdzenie zamówienia, a ma możesz wywołać co 20 minut Tort stanu. Po 20 minut przekazać, należy wywołać dla piekarni, ale one informujące niegotowe je i że należy wywołać w innym 20 minut. Ten proces Wstecz i w przód jest kontynuowany, dopóki nie wywołasz, a dla piekarni wyświetla komunikat informujący o tym, że Twoje zamówienie jest gotowy i dostarcza je. 

Więc zamapować powrót tego wzorca sondowania. Dla piekarni reprezentuje niestandardowego interfejsu API, gdy użytkownik klienta Tort reprezentuje aparat usługi Logic Apps. Gdy aparat wywołuje interfejs API z żądaniem, interfejs API potwierdza żądanie i odpowiada za pomocą przedział czasu, gdy aparat można sprawdzić stan zadania. Aparat kontynuuje sprawdzanie stanu zadania, dopóki interfejsu API odpowiada, zadanie nie zostanie wykonane i zwraca dane do aplikacji logiki, który następnie będzie przepływu pracy. 

![Wzorzec akcji sondowania](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Poniżej przedstawiono kroki określone dla interfejsu API, z opisem z punktu widzenia interfejsu API:

1. Gdy interfejs API odbiera żądanie HTTP, aby rozpocząć pracę, natychmiast przywrócić HTTP `202 ACCEPTED` odpowiedzi z `location` nagłówka opisane w dalszej części tego kroku. Ta odpowiedź pozwala dowiedzieć się, że Twój interfejs API otrzymała żądanie, akceptowane ładunek żądania (dane wejściowe) aparat usługi Logic Apps i trwa jego przetwarzanie teraz. 
   
   `202 ACCEPTED` Odpowiedź powinna zawierać tych nagłówków:
   
   * *Wymagane*: A `location` nagłówek, który określa ścieżkę bezwzględną do adresu URL, w którym aparat usługi Logic Apps można sprawdzić stan zadania Twój interfejs API

   * *Opcjonalnie*: A `retry-after` nagłówek, który określa liczbę sekund, które aparat należy czekać przed sprawdzeniem `location` adres URL dla stanu zadania. 

     Domyślnie aparat sprawdza co 20 sekund. Aby określić inny interwał, należy dołączyć `retry-after` nagłówek i liczbę sekund, aż do następnego sondowania.

2. Po określonym czasie przekazuje, Logic Apps aparatu sond `location` adres URL do sprawdzenia stanu zadania. Interfejs API powinien przeprowadzać te kontrole i zwracania tych odpowiedzi:
   
   * Jeśli zadanie nie zostanie wykonane, wróć HTTP `200 OK` odpowiedzi wraz z ładunku odpowiedzi (dane wejściowe na potrzeby następnego kroku).

   * Jeśli nadal przetwarza zadanie, zwróć inny HTTP `202 ACCEPTED` odpowiedzi, ale ten sam nagłówek jako oryginalnej odpowiedzi.

Kiedy Twój interfejs API tego wzorca, nie trzeba podejmować żadnych działań w definicji przepływu pracy aplikacji logiki, aby kontynuować, sprawdzania stanu zadania. Gdy aparat pobiera HTTP `202 ACCEPTED` odpowiedzi i prawidłową `location` nagłówka, aparat uwzględnia wzorca asynchronicznego i sprawdza, czy `location` nagłówka, dopóki interfejs API zwróci odpowiedzi 202 innych.

> [!TIP]
> Przykład wzorca asynchronicznego, przejrzyj ten [kontrolera asynchronicznego przykład odpowiedzi w usłudze GitHub](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Wykonywania długotrwałych zadań za pomocą wzorca akcji elementu webhook

Jako alternatywę można użyć wzorca elementu webhook dla podzadań długotrwałych i przetwarzania asynchronicznego. Ten wzorzec ma aplikację logiki, wstrzymywanie i poczekaj, aż "wywołanie zwrotne" z interfejsu API na zakończenie przetwarzania, aby kontynuować przepływ pracy. To wywołanie zwrotne jest metodę POST protokołu HTTP, która wysyła komunikat do adresu URL w przypadku wystąpienia zdarzenia. 

<a name="bakery-webhook-action"></a> Teraz Zastosuj poprzedniego odpowiednio dla piekarni wzorcem elementu webhook i imagine wywołania dla piekarni i kolejność niestandardowych Tort dostarczania. Proces składania Tort czasochłonne, a nie chcesz czekać na telefonie, natomiast dla piekarni działa we Tort. Dla piekarni potwierdzenie zamówienia, ale tym razem nadaj im swój numer telefonu, mogą zadzwonić do Ciebie po zakończeniu Tort. Tym razem dla piekarni informuje, kiedy Twoje zamówienie jest gotowy i dostarcza je.

Gdy firma Microsoft zamapować powrót tego wzorca elementu webhook, dla piekarni reprezentuje niestandardowego interfejsu API, gdy użytkownik klienta Tort reprezentuje aparat usługi Logic Apps. Aparat wywołuje interfejs API z żądaniem i obejmuje "" adres URL wywołania zwrotnego.
Po zakończeniu zadania, interfejs API korzysta z adresu URL silnika i zwracać dane do swojej aplikacji logiki, która będzie je kontynuować przepływ pracy. 

Dla tego wzoru skonfigurować dwa punkty końcowe na kontrolerze: `subscribe` i `unsubscribe`

*  `subscribe` Punkt końcowy: Gdy wykonywanie osiągnie Twój interfejs API akcji w przepływie pracy, Logic Apps aparatu wywołania `subscribe` punktu końcowego. Ten krok powoduje, że aplikację logiki, aby utworzyć adres URL wywołania zwrotnego, która przechowuje Twój interfejs API, a następnie poczekaj wywołania zwrotnego z interfejsu API, po zakończeniu pracy. Interfejs API, a następnie wywołuje metodę POST protokołu HTTP do adresu URL i przekazuje wszelkie zwracanej zawartości i nagłówki jako dane wejściowe do aplikacji logiki.

* `unsubscribe` Punkt końcowy: Jeśli przebieg aplikacji logiki jest anulowane, Logic Apps aparatu wywołania `unsubscribe` punktu końcowego. Interfejs API można wyrejestrować adres URL wywołania zwrotnego i Zatrzymaj wszystkie procesy, zgodnie z potrzebami.

![Wzorzec akcji elementu Webhook](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> Projektant aplikacji logiki nie obsługuje obecnie, wykrywanie elementu webhook punktów końcowych za pośrednictwem struktury Swagger. Dlatego do tego wzorca należy dodać [ **elementu Webhook** akcji](../connectors/connectors-native-webhook.md) i określ adres URL, nagłówki i treść żądania. Zobacz też [wyzwalacze i akcje przepływu pracy](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Aby przekazać adres URL wywołania zwrotnego, można użyć `@listCallbackUrl()` funkcji przepływu pracy w polach poprzedniego zgodnie z potrzebami.

> [!TIP]
> Na przykład wzorzec elementu webhook, przejrzyj [przykład wyzwalacza elementu webhook w usłudze GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Wzorce wyzwalacza

Niestandardowy interfejs API może działać jako [ *wyzwalacza* ](./logic-apps-overview.md#logic-app-concepts) , uruchamia aplikację logiki, gdy nowe dane lub zdarzenia spełnia określony warunek. Tego wyzwalacza można regularnie, sprawdź lub poczekać i nasłuchiwania nowych danych lub zdarzenia w punktu końcowego usługi. Jeśli nowe dane lub zdarzenia spełnia określony warunek, aktywuje się i uruchamia aplikację logiki, które nasłuchują tego wyzwalacza. Do uruchomienia aplikacji logiki w ten sposób, można wykonać interfejsu API [ *wyzwalacza sondowania* ](#polling-triggers) lub [ *wyzwalacza elementu webhook* ](#webhook-triggers) wzorca. Te wzorce są podobne do ich odpowiedników dla [akcje odpytywania](#async-pattern) i [Akcje elementu webhook](#webhook-actions). Ponadto Dowiedz się więcej o [zbierania danych użycia dla wyzwalaczy](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Sprawdź nowe dane lub zdarzeń regularnie za pomocą wzorca wyzwalacza sondowania

A *wyzwalacza sondowania* działa podobnie do [akcji sondowania](#async-pattern) opisany wcześniej w tym temacie. Aparat usługi Logic Apps okresowo wywołuje i sprawdza, czy punkt końcowy wyzwalacza dla nowych danych lub zdarzenia. Jeśli aparat wykryje nowe dane lub zdarzenie, które spełnia określony warunek, wyzwalacza. Następnie aparat tworzy wystąpienie aplikacji logiki, który przetwarza dane jako dane wejściowe. 

![Sondowanie wzorzec wyzwalacza](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Każde żądanie sondowania jest liczone jako środowisko wykonawcze akcji nawet wtedy, gdy nie wystąpienie aplikacji logiki jest tworzony. Aby uniknąć przetwarzania wielokrotnie w tych samych danych, wyzwalacz powinien czyścić dane już zostało odczytane i przekazane do aplikacji logiki.

Oto etapy wyzwalacz sondowania opisane względem interfejsu API:

| Znaleziono nowe dane lub zdarzenia?  | Odpowiedź interfejsu API | 
| ------------------------- | ------------ |
| Znaleziono | Zwróć HTTP `200 OK` stan ładunek odpowiedzi (dane wejściowe na potrzeby następnego kroku). <br/>Ta odpowiedź tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy. | 
| Nie można odnaleźć | Zwróć HTTP `202 ACCEPTED` stan `location` nagłówka i `retry-after` nagłówka. <br/>Wyzwalacze `location` nagłówek powinien również zawierać `triggerState` parametr zapytania, który jest zazwyczaj "timestamp". Interfejs API może użyć tego identyfikatora do śledzenia Data i godzina ostatniego została wyzwolona aplikacja logiki. | 
||| 

Na przykład mogą okresowo sprawdzać dostępność nowych plików usługi, możesz utworzyć wyzwalacz sondowania, który ma następujące zachowania:

| Żądanie zawiera `triggerState`? | Odpowiedź interfejsu API | 
| -------------------------------- | -------------| 
| Nie | Zwróć HTTP `202 ACCEPTED` stanu oraz `location` nagłówek o `triggerState` wartość bieżącej godziny i `retry-after` interwał 15 sekund. | 
| Yes | Sprawdź usługi pliki dodane po `DateTime` dla `triggerState`. | 
||| 

| Liczba znalezionych plików | Odpowiedź interfejsu API | 
| --------------------- | -------------| 
| Pojedynczy plik | Zwróć HTTP `200 OK` aktualizacji stanu i ładunek zawartości `triggerState` do `DateTime` zwróconego pliku i zestawu `retry-after` interwał 15 sekund. | 
| Wiele plików | Zwraca jeden plik w czasie i HTTP `200 OK` stan, aktualizacja `triggerState`i ustaw `retry-after` interwał 0 sekund. </br>Poniższe kroki umożliwiają aparatu wiedzieć, że dostępnych jest więcej danych i że aparat powinien natychmiast prosić o danych z adresu URL w `location` nagłówka. | 
| Brak plików | Zwróć HTTP `202 ACCEPTED` stan, nie należy zmieniać `triggerState`i ustaw `retry-after` interwał 15 sekund. | 
||| 

> [!TIP]
> Na przykład sondowania wzorzec wyzwalacza, przejrzyj ten [sondowania wyzwalacza kontrolera przykładowych w usłudze GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Zaczekaj i nasłuchuje nowych danych lub zdarzeń za pomocą wzorca wyzwalacza elementu webhook

Wyzwalacza elementu webhook jest *wyzwalacza wypychania* który czeka i będzie nasłuchiwać pod kątem nowych danych lub zdarzenia w punktu końcowego usługi. Jeśli nowe dane lub zdarzenia spełnia określony warunek, wyzwalacz jest uruchamiany i tworzy wystąpienie aplikacji logiki, która następnie przetwarza dane jako dane wejściowe.
Wyzwalaczy elementu Webhook działają podobnie jak [Akcje elementu webhook](#webhook-actions) wcześniej opisane w tym temacie i są konfigurowane przy użyciu `subscribe` i `unsubscribe` punktów końcowych. 

* `subscribe` Punkt końcowy: Kiedy dodajesz i zapisać wyzwalacza elementu webhook w aplikacji logiki, aplikacje logiki aparatu wywołania `subscribe` punktu końcowego. Ten krok powoduje, że aplikacja logiki utworzyć adres URL wywołania zwrotnego, która przechowuje Twój interfejs API. Jeśli nowe dane lub zdarzenie, które spełnia określony warunek, interfejs API wywołuje za pomocą metody POST protokołu HTTP do adresu URL. Ładunek zawartości i nagłówki przekazać jako dane wejściowe do aplikacji logiki.

* `unsubscribe` Punkt końcowy: Usunięcie wyzwalacza elementu webhook lub całą logikę aplikacji Logic Apps aparatu wywołania `unsubscribe` punktu końcowego. Interfejs API można wyrejestrować adres URL wywołania zwrotnego i Zatrzymaj wszystkie procesy, zgodnie z potrzebami.

![Wzorzec wyzwalacza elementu Webhook](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> Projektant aplikacji logiki nie obsługuje obecnie, wykrywanie elementu webhook punktów końcowych za pośrednictwem struktury Swagger. Dlatego do tego wzorca należy dodać [ **elementu Webhook** wyzwalacza](../connectors/connectors-native-webhook.md) i określ adres URL, nagłówki i treść żądania. Zobacz też [wyzwalacza HTTPWebhook](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Aby przekazać adres URL wywołania zwrotnego, można użyć `@listCallbackUrl()` funkcji przepływu pracy w polach poprzedniego zgodnie z potrzebami.
>
> Aby uniknąć przetwarzania wielokrotnie w tych samych danych, wyzwalacz powinien czyścić dane już zostało odczytane i przekazane do aplikacji logiki.

> [!TIP]
> Na przykład wzorzec elementu webhook, przejrzyj [przykład kontrolera wyzwalacza elementu webhook w usłudze GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

## <a name="secure-calls-to-your-apis-from-logic-apps"></a>Bezpieczne wywołania interfejsów API z aplikacji logiki

Po utworzeniu niestandardowych interfejsów API, należy skonfigurować uwierzytelnianie dla interfejsów API, dzięki czemu można je będzie bezpiecznie wywoływać z aplikacji logiki. Dowiedz się, [jak bezpieczne wywołania niestandardowych interfejsów API z aplikacji logiki](../logic-apps/logic-apps-custom-api-authentication.md).

## <a name="deploy-and-call-your-apis"></a>Wdrażanie i wywoływanie interfejsów API

Po skonfigurowaniu uwierzytelniania należy skonfigurować wdrożenie dla interfejsów API. Dowiedz się, [sposób wdrażania i wywoływanie niestandardowych interfejsów API z aplikacji logiki](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Publikowanie niestandardowych interfejsów API na platformie Azure

Aby udostępnić niestandardowe interfejsy API innym użytkownikom usługi Logic Apps na platformie Azure, możesz zwiększyć bezpieczeństwo i zarejestrować je jako łączniki aplikacji logiki. Aby uzyskać więcej informacji, zobacz [Omówienie łączników niestandardowych](../logic-apps/custom-connector-overview.md). 

Aby niestandardowe interfejsy API dostępne dla wszystkich użytkowników w Logic Apps, Microsoft Flow i Microsoft PowerApps, należy zwiększyć bezpieczeństwo, Zarejestruj swoje interfejsy API jako łączniki aplikacji logiki i wyznaczyć łączniki do [programu certyfikat platformy Microsoft Azure](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/). 

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Aby uzyskać pomoc przy użyciu niestandardowych interfejsów API, skontaktuj się z pomocą [ customapishelp@microsoft.com ](mailto:customapishelp@microsoft.com).

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Aby pomóc w ulepszaniu usługi Logic Apps, przesyłaj pomysły lub głosuj na nie w [witrynie opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Kolejne kroki

* [Obsługa błędów i wyjątków](../logic-apps/logic-apps-exception-handling.md)
* [Wywoływanie, wyzwalacza lub zagnieżdżanie aplikacji logiki za pomocą punktów końcowych HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Dla akcji i wyzwalaczy zbierania danych użycia](../logic-apps/logic-apps-pricing.md)
