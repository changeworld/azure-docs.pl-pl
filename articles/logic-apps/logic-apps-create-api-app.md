---
title: Tworzenie sieci web API i interfejsów API REST dla usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie sieci web, interfejsów API i interfejsów API REST, zadzwoń z interfejsów API, usług lub systemów z przepływów pracy aplikacji logiki do integracji systemu
keywords: interfejsy API sieci Web, interfejsów API REST, przepływy pracy, integracji systemu
services: logic-apps
author: jeffhollan
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: bd229179-7199-4aab-bae0-1baf072c7659
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/26/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 3ca55bb0a9f4719bd2229aca626d20c53af9fd1e
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299532"
---
# <a name="create-custom-apis-that-you-can-call-from-logic-app-workflows"></a>Tworzenie niestandardowych interfejsów API, który można wywołać z przepływów pracy aplikacji logiki

Mimo że Azure Logic Apps oferuje [100 + łączników](../connectors/apis-list.md) czy można użyć w przepływach pracy aplikacji logiki, należy wywołać interfejsów API, systemów i usług, które nie są dostępne jako łączniki. Możesz utworzyć własne interfejsów API, które zapewniają akcje i wyzwalaczy do użycia w aplikacjach logiki. W tym miejscu są też inne przyczyny, dlaczego warto tworzyć własne interfejsów API, które można wywołać z przepływów pracy aplikacji logiki:

* Rozszerzanie bieżący system integracji i dane integrację przepływów pracy.
* Pomóc klientom umożliwia zarządzanie zadaniami professional lub osobiste z usługą.
* Rozwiń reach, odnajdywania i użycia dla usługi.

Zasadniczo łączników są interfejsów API REST na użytek podłączany interfejsów sieci web [format metadanych struktury Swagger](http://swagger.io/specification/) dla dokumentacji i dane JSON jako ich format wymiany danych. Ponieważ łączniki są interfejsów API REST, które komunikują się za pośrednictwem punktów końcowych HTTP, można użyć dowolnego języka, takich jak .NET, Java lub Node.js do tworzenia łączników. Można również udostępnić swoje interfejsy API na [usłudze Azure App Service](../app-service/app-service-web-overview.md), platformy jako — usługa (PaaS) oferta zapewnia w jednym ze sposobów najlepsze najprostszym i najbardziej skalowalny hosting interfejsu API. 

W przypadku niestandardowych interfejsów API do pracy z usługą logic apps, interfejs API może zapewnić [ *akcje* ](./logic-apps-overview.md#logic-app-concepts) który wykonywania określonych zadań w przepływach pracy aplikacji logiki. Interfejs API mogą również działać jako [ *wyzwalacza* ](./logic-apps-overview.md#logic-app-concepts) zaczynającym się przepływu pracy aplikacji logiki w przypadku nowych danych lub zdarzeń spełnia określony warunek. W tym temacie opisano typowe wzorce, które możesz wykonać tworzenia działania i jest wyzwalane w interfejsie API, na podstawie zachowania, które Twój interfejs API w celu zapewnienia.

Swoje interfejsy API mogą być hostowane na [usłudze Azure App Service](../app-service/app-service-web-overview.md), platformy jako — usługa (PaaS) oferta zapewnia wysoce skalowalną, prosty hosting interfejsu API.

> [!TIP] 
> Mimo że można wdrożyć swoje interfejsy API jako aplikacje sieci web, należy wziąć pod uwagę wdrażanie swoje interfejsy API jako aplikacji interfejsu API, które mogą ułatwić zadania podczas kompilacji, hosta i korzystanie z interfejsów API w chmurze i lokalnie. Nie trzeba zmieniać żadnego kodu w swoje interfejsy API — wystarczy go wdrożyć kod w aplikacji interfejsu API. Na przykład informacje o sposobie tworzenia aplikacji API apps utworzone za pomocą tych języków: 
> 
> * [ASP.NET](../app-service/app-service-web-get-started-dotnet.md). 
> * [Java](../app-service/app-service-web-get-started-java.md)
> * [Node.js](../app-service/app-service-web-get-started-nodejs.md)
> * [PHP](../app-service/app-service-web-get-started-php.md)
> * [Python](../app-service/app-service-web-get-started-python.md)
>
> Przykłady aplikacji interfejsu API utworzony dla usługi logic apps, można znaleźć [repozytorium GitHub aplikacje logiki platformy Azure](http://github.com/logicappsio) lub [blogu](http://aka.ms/logicappsblog).

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Jak niestandardowych interfejsów API, różnią się od łączników niestandardowych?

Niestandardowych interfejsów API i [łączników niestandardowych](../logic-apps/custom-connector-overview.md) są sieci web interfejsów API REST na użytek podłączany interfejsów [format metadanych struktury Swagger](http://swagger.io/specification/) dla dokumentacji i dane JSON jako ich format wymiany danych. I te interfejsy API i łączniki są interfejsów API REST, które komunikują się za pośrednictwem punktów końcowych HTTP, można użyć dowolnego języka, takich jak .NET, Java lub Node.js do tworzenia niestandardowych interfejsów API i łączniki.

Niestandardowych interfejsów API umożliwiają wywoływania interfejsów API, które nie są łączniki i udostępnić punkty końcowe, które można wywołać metodę HTTP + Swagger, zarządzanie interfejsami API Azure lub usługi aplikacji. Niestandardowe łączniki działają podobnie niestandardowych interfejsów API, ale także mieć następujące atrybuty:

* Zarejestrowany jako łącznik aplikacji logiki zasobów platformy Azure.
* Są wyświetlane z ikonami obok zarządzany przez firmę Microsoft łączników w Projektancie aplikacji logiki.
* Dostępne tylko dla autorów i logiki aplikacji użytkownicy, którzy mają tej samej dzierżawy usługi Azure Active Directory i subskrypcji platformy Azure w regionie, w których są wdrożone aplikacje logiki działania łączników.

Istnieje możliwość nominowania zarejestrowanych łączników do certyfikacji firmy Microsoft. Ten proces sprawdza, czy zarejestrowanych łączników spełniają kryteria do użytku publicznego i udostępnia łączniki dla użytkowników w Flow firmy Microsoft i PowerApps firmy Microsoft.

Aby uzyskać więcej informacji na temat łączników niestandardowych zobacz 

* [Omówienie łączników niestandardowych](../logic-apps/custom-connector-overview.md)
* [Tworzenie niestandardowych łączniki z interfejsów API sieci Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Zarejestruj łączników niestandardowych w aplikacjach logiki platformy Azure](../logic-apps/logic-apps-custom-connector-register.md)

## <a name="helpful-tools"></a>Przydatne tools

Niestandardowy interfejs API, najlepiej z usługą logic apps ma również interfejs API [dokumentu Swagger](http://swagger.io/specification/) opisujący operacje interfejsu API i parametry.
Wiele bibliotek, takich jak [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle), można automatycznie wygenerować pliku programu Swagger dla Ciebie. Adnotować pliku programu Swagger dla nazw wyświetlanych, typy właściwości i tak dalej, można również użyć [TRex](https://github.com/nihaue/TRex) tak, aby plik struktury Swagger działa poprawnie z usługą logic apps.

<a name="actions"></a>

## <a name="action-patterns"></a>Wzorce akcji

W przypadku aplikacji logiki do wykonywania zadań powinno dostarczyć niestandardowego interfejsu API [ *akcje*](./logic-apps-overview.md#logic-app-concepts). Mapuje każdej operacji interfejsu API do operacji. Podstawowe akcja jest kontrolera, który akceptuje żądania HTTP i zwraca odpowiedzi HTTP. Tak na przykład aplikacji logiki wysyła żądanie HTTP do aplikacji sieci web lub aplikacji interfejsu API. Aplikacja zwraca odpowiedź HTTP wraz z zawartością, który może przetwarzać aplikacji logiki.

Działania standardowe możesz zapisać metoda żądania HTTP w interfejsie API i opis tej metody w pliku programu Swagger. Następnie można wywołać interfejsu API bezpośrednio z [akcji HTTP](../connectors/connectors-native-http.md) lub [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) akcji. Domyślnie odpowiedzi musi zostać zwrócona w [limit czasu żądania](./logic-apps-limits-and-config.md). 

![Działania standardowe wzorca](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a> Aby utworzyć aplikację logiki, zaczekaj, aż interfejs API zakończy dłużej uruchomione zadania, można wykonać interfejsu API [wzorca asynchronicznego sondowania](#async-pattern) lub [wzorca asynchronicznego elementu webhook](#webhook-actions) opisanych w tym temacie. Dla odpowiednio, która pomaga zwizualizować różne zachowania tych wzorców Załóżmy proces porządkowania niestandardowych ciasto z bakery. Wzorzec sondowania odzwierciedla zachowanie, gdzie należy wywołać bakery co 20 minut, aby sprawdzić, czy ciasto jest gotowy. Wzorzec elementu webhook odzwierciedla zachowanie, w którym bakery monituje o numer telefonu, ich można wywołać, gdy ciasto jest gotowy.

Przykłady, można znaleźć [repozytorium GitHub aplikacje logiki](https://github.com/logicappsio). Ponadto Dowiedz się więcej o [zbierania danych użycia dla akcji](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Wykonywania długotrwałych zadań za pomocą wzorca akcji sondowania

Aby wykonywać zadania, które można uruchomić więcej niż interfejs API [limit czasu żądania](./logic-apps-limits-and-config.md), można używać wzorca asynchronicznego sondowania. Ten wzorzec ma użytkownika do interfejsu API, pracy w oddzielnym wątku, ale zachować aktywnego połączenia z aparatem Logic Apps. W ten sposób aplikacji logiki nie podlega limitowi czasu lub kontynuować do następnego kroku w przepływie pracy, zanim interfejsu API zakończy pracę.

Poniżej przedstawiono ogólny wzór:

1. Upewnij się, że aparat wie, że Twój interfejs API zaakceptowano żądanie i zaczął działać.
2. Gdy aparat sprawia, że kolejne żądania dotyczące stanu zadania, umożliwiają aparat wiedzieć, kiedy interfejsu API kończy zadanie.
3. Zwraca odpowiednie dane do aparatu, aby kontynuować przepływ pracy aplikacji logiki.

<a name="bakery-polling-action"></a> Teraz zastosować poprzedniej odpowiednio bakery do wzorca sondowania i Załóżmy, że należy wywołać bakery i kolejność ciasto niestandardowych w celu dostarczania. Czas procesu dokonywania ciasto, a nie chce czekać na telefonie, podczas gdy bakery działa na ciasto. Bakery potwierdzenie zamówienia i ma należy wywołać co 20 minut ciasto stanu. Po 20 minut przekazać, należy wywołać bakery, ale one informujące, że nie jest wykonywane je i że należy wywołać w innym 20 minut. Ten proces Wstecz i w przód jest kontynuowany do momentu wywołania i bakery informuje, że Twoje zamówienie jest gotowy, a następnie dostarcza je. 

Więc warto Mapuj ponownie ten wzorzec sondowania. Bakery reprezentuje niestandardowego interfejsu API, gdy użytkownik klienta ciasto reprezentują aparat Logic Apps. Gdy aparat wywołuje interfejs API żądanie, interfejs API potwierdza żądanie i odpowiada przedział czasu, gdy aparat można sprawdzić stan zadania. Aparat kontynuuje sprawdzanie stanu zadania, dopóki Twój interfejs API odpowiada, że zadanie jest wykonywane i zwraca dane do aplikacji logiki, który następnie jest kontynuowana przepływu pracy. 

![Wzorzec akcji sondowania](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Poniżej przedstawiono kolejne kroki do interfejsu API, które należy wykonać, opisane względem interfejsu API:

1. Interfejs API pobiera żądania HTTP do rozpoczęcia pracy, natychmiast powrócić HTTP `202 ACCEPTED` odpowiedzi z `location` nagłówka opisane w dalszej części tego kroku. Ta odpowiedź umożliwia aparat Logic Apps wiedzieć, że Twój interfejs API Otrzymano żądanie, zaakceptowane ładunku żądania (dane wejściowe), a teraz przetwarza. 
   
   `202 ACCEPTED` Odpowiedź powinna zawierać te nagłówki:
   
   * *Wymagane*: A `location` nagłówek, który określa ścieżkę bezwzględną do adresu URL, gdy aparat Logic Apps można sprawdzić stan zadania Twój interfejs API

   * *Opcjonalne*: A `retry-after` nagłówek, który określa liczbę sekund, które aparat powinien zaczekać na sprawdzanie `location` adres URL dla stanu zadania. 

     Domyślnie aparat sprawdza co 20 sekund. Aby określić inny interwał, obejmują `retry-after` nagłówka i liczbę sekund, aż do następnego sondowania.

2. Po pomyślnej określony czas, aplikacje logiki aparatu sond `location` adres URL do sprawdzenia stanu zadania. Interfejs API należy te sprawdzania instalacji i zwracać tych odpowiedzi:
   
   * Jeśli zadanie jest wykonywane, zwróć HTTP `200 OK` odpowiedzi, wraz z ładunku odpowiedzi (dane wejściowe dla następnego kroku).

   * Jeśli nadal przetwarza zadanie, zwróć inny HTTP `202 ACCEPTED` odpowiedzi, ale z tej samej nagłówki odpowiedzi oryginalnego.

Kiedy interfejsu API ten wzorzec, nie trzeba wykonywać żadnych czynności w definicji przepływu pracy aplikacji logiki, aby kontynuować, sprawdzania stanu zadania. Gdy aparat pobiera HTTP `202 ACCEPTED` odpowiedzi i prawidłową `location` nagłówek, aparat uwzględnia wzorca asynchronicznego i sprawdza `location` nagłówka, dopóki Twój interfejs API zwraca odpowiedź z systemem innym niż 202.

> [!TIP]
> Na przykład wzorca asynchronicznego, przejrzyj [próbki odpowiedzi kontrolera asynchronicznego w serwisie GitHub](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Wykonywania długotrwałych zadań za pomocą wzorca akcji elementu webhook

Alternatywnie można użyć wzorzec elementu webhook dla długotrwałych zadań i przetwarzania asynchronicznego. Ten wzorzec ma aplikacji logiki wstrzymać i poczekaj, aż "wywołania zwrotnego" z interfejsu API na zakończenie przetwarzania, aby kontynuować przepływ pracy. To wywołanie zwrotne jest POST protokołu HTTP, który wysyła wiadomość na adres URL, gdy zdarzenie. 

<a name="bakery-webhook-action"></a> Teraz zastosować poprzedniej odpowiednio bakery do wzorca elementu webhook i Załóżmy, że należy wywołać bakery i kolejność ciasto niestandardowych w celu dostarczania. Czas procesu dokonywania ciasto, a nie chce czekać na telefonie, podczas gdy bakery działa na ciasto. Bakery potwierdzenie zamówienia, ale teraz, możesz nadać im numer telefonu, ich można wywołać, gdy jest wykonywane ciasto. Tym razem bakery informuje, kiedy Twoje zamówienie jest gotowy, a następnie dostarcza je.

Gdy firma Microsoft mapować tego elementu webhook wzorca ponownie, bakery reprezentuje niestandardowego interfejsu API, gdy użytkownik klienta ciasto reprezentują aparat Logic Apps. Aparat wywołanie interfejsu API z żądaniem i zawiera adres URL "wywołania zwrotnego".
Po zakończeniu zadania, interfejs API korzysta z adresu URL aparat i zwrócić dane do aplikacji logiki, który następnie jest kontynuowana przepływu pracy. 

Dla tego wzorca zdefiniować dwa punkty końcowe na kontrolerze: `subscribe` i `unsubscribe`

*  `subscribe` punkt końcowy: podczas wykonywania osiągnie Twój interfejs API działań w przepływie pracy, aplikacje logiki aparatu wywołania `subscribe` punktu końcowego. Ten krok powoduje, że aplikację logiki, aby utworzyć adres URL wywołania zwrotnego, który przechowuje interfejsu API i poczekaj na wywołanie zwrotne z interfejsu API po zakończeniu pracy. Interfejs API, a następnie wywołuje akcję POST protokołu HTTP do adresu URL i przekazuje wszystkie zwrócone zawartości i nagłówków jako dane wejściowe do aplikacji logiki.

* `unsubscribe` punkt końcowy: Jeśli anulowania Uruchom aplikację logiki aplikacji logiki aparat wywołania `unsubscribe` punktu końcowego. Interfejs API można wyrejestrować adres URL wywołania zwrotnego i Zatrzymaj wszystkie procesy w razie potrzeby.

![Wzorzec akcji elementu Webhook](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> Obecnie usługa projektanta aplikacji logiki nie obsługuje odnajdowania elementu webhook punktów końcowych za pośrednictwem struktury Swagger. Dlatego dla tego wzorca należy dodać [ **Webhook** akcji](../connectors/connectors-native-webhook.md) i określ adres URL, nagłówki i treści żądania. Zobacz też [działania przepływu pracy i wyzwalaczy](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Aby przekazać w adresie URL wywołania zwrotnego, można użyć `@listCallbackUrl()` funkcji przepływu pracy w poprzedniej polach odpowiednio do potrzeb.

> [!TIP]
> Na przykład elementu webhook wzorzec, przejrzyj [próbki wyzwalacza elementu webhook w serwisie GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Wzorce wyzwalacza

Niestandardowy interfejs API może działać jako [ *wyzwalacza* ](./logic-apps-overview.md#logic-app-concepts) zaczynającym się aplikacji logiki w przypadku nowych danych lub zdarzeń spełnia określony warunek. Wyzwalacz można regularnie, sprawdź lub poczekać i nasłuchiwania nowych danych lub zdarzeń na punkt końcowy usługi. Jeśli nowe dane lub zdarzenia spełnia określony warunek, wyzwalacz generowane i uruchamia aplikację logiki, która nasłuchuje do tego wyzwalacza. Aby uruchomić aplikacje logiki w ten sposób, można wykonać interfejsu API [ *wyzwalacza sondowania* ](#polling-triggers) lub [ *wyzwalacza elementu webhook* ](#webhook-triggers) wzorca. Te wzorce są podobne do ich odpowiedniki dla [sondowania akcje](#async-pattern) i [Akcje elementu webhook](#webhook-actions). Ponadto Dowiedz się więcej o [wyzwalaczy zbierania danych użycia](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Sprawdź, czy nowych danych lub zdarzeń regularnie z wzorcem wyzwalacza sondowania

A *wyzwalacza sondowania* działa podobnie [akcji sondowania](#async-pattern) opisanych wcześniej w tym temacie. Aparat Logic Apps okresowo wywołuje i sprawdza końcowy wyzwalacza dla nowych danych lub zdarzeń. Jeśli aparat znajdzie nowych danych lub zdarzenia spełniającego określony warunek, wyzwalacza. Następnie aparat tworzy wystąpienie aplikacji logiki, który przetwarza dane jako dane wejściowe. 

![Sondowanie wzorzec wyzwalacza](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Każde żądanie sondowania jest traktowana jako wykonanie akcji, nawet wtedy, gdy jest tworzone żadne wystąpienie aplikacji logiki. Aby uniemożliwić przetwarzanie wielokrotnie tych samych danych, wyzwalacz należy wyczyścić dane, które już zostało odczytane i przekazane do aplikacji logiki.

Oto etapy wyzwalacz sondowania opisem z punktu widzenia interfejsu API:

| Znaleziono nowych danych lub zdarzeń?  | Odpowiedzi interfejsu API | 
| ------------------------- | ------------ |
| Znaleziono | Zwraca HTTP `200 OK` stan ładunek odpowiedzi (dane wejściowe dla następnego kroku). <br/>Ta odpowiedź tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy. | 
| Nie znaleziono | Zwraca HTTP `202 ACCEPTED` stan o `location` nagłówka i `retry-after` nagłówka. <br/>Wyzwalaczy `location` nagłówka powinien również zawierać `triggerState` parametru zapytania, który zazwyczaj jest to "sygnatura czasowa". Interfejs API za pomocą tego identyfikatora możesz śledzić ostatniego aplikacji logiki zostało wyzwolone. | 
||| 

Na przykład mogą okresowo sprawdzać usługi dla nowych plików, można utworzyć wyzwalacza sondowania, który ma następujące zachowania:

| Żądanie zawiera `triggerState`? | Odpowiedzi interfejsu API | 
| -------------------------------- | -------------| 
| Nie | Zwraca HTTP `202 ACCEPTED` stanu oraz `location` nagłówek o `triggerState` ustawioną bieżący czas i `retry-after` interwał na 15 sekund. | 
| Yes | Sprawdź usługi plików dodane po `DateTime` dla `triggerState`. | 
||| 

| Liczba znalezionych plików | Odpowiedzi interfejsu API | 
| --------------------- | -------------| 
| Pojedynczy plik | Zwraca HTTP `200 OK` aktualizacji stanu i ładunek zawartości `triggerState` do `DateTime` zwróconego pliku oraz zestaw `retry-after` interwał na 15 sekund. | 
| Wiele plików | Zwróć jeden plik w czasie i HTTP `200 OK` stan, aktualizacja `triggerState`i ustaw `retry-after` interwał 0 sekund. </br>Te kroki let aparat dowiedzieć się, że dostępnych jest więcej danych, i że aparat powinien natychmiast dane żądania z adresu URL w `location` nagłówka. | 
| Brak plików | Zwraca HTTP `202 ACCEPTED` stanu, nie zmieniaj `triggerState`i ustaw `retry-after` interwał na 15 sekund. | 
||| 

> [!TIP]
> Na przykład sondowania wzorzec wyzwalacza, przejrzyj to [próbki kontrolera wyzwalacza sondowania w serwisie GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Zaczekaj i nasłuchiwania nowych danych lub zdarzeń za pomocą wzorca wyzwalacza elementu webhook

Wyzwalacz elementu webhook jest *wyzwalacz wypychania* czeka i nasłuchuje nowych danych lub zdarzeń na punktu końcowego usługi. Jeśli nowe dane lub zdarzenia spełnia określony warunek, wyzwalacz wyzwala i tworzy wystąpienie aplikacji logiki, który następnie przetwarza dane jako dane wejściowe.
Wyzwalacze elementu Webhook działanie podobne jak w przypadku [Akcje elementu webhook](#webhook-actions) wcześniej w tym temacie opisano i skonfigurowano `subscribe` i `unsubscribe` punktów końcowych. 

* `subscribe` punkt końcowy: podczas dodawania i zapisać wyzwalacza elementu webhook w aplikacji logiki aplikacji logiki aparatu wywołania `subscribe` punktu końcowego. Ten krok powoduje, że aplikację logiki, aby utworzyć adres URL wywołania zwrotnego, który przechowuje interfejsu API. Jeśli wystąpi nowych danych lub zdarzenia spełniającego określony warunek, interfejsu API wywołuje z HTTP POST do adresu URL. Ładunek zawartości i nagłówki są przekazywane jako dane wejściowe do aplikacji logiki.

* `unsubscribe` punkt końcowy: usunięcie elementu webhook wyzwalacza lub całą logikę aplikacji Logic Apps aparat wywołania `unsubscribe` punktu końcowego. Interfejs API można wyrejestrować adres URL wywołania zwrotnego i Zatrzymaj wszystkie procesy w razie potrzeby.

![Wzorzec wyzwalacza elementu Webhook](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> Obecnie usługa projektanta aplikacji logiki nie obsługuje odnajdowania elementu webhook punktów końcowych za pośrednictwem struktury Swagger. Dlatego dla tego wzorca należy dodać [ **Webhook** wyzwalacza](../connectors/connectors-native-webhook.md) i określ adres URL, nagłówki i treści żądania. Zobacz też [wyzwalacza HTTPWebhook](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Aby przekazać w adresie URL wywołania zwrotnego, można użyć `@listCallbackUrl()` funkcji przepływu pracy w poprzedniej polach odpowiednio do potrzeb.
>
> Aby uniemożliwić przetwarzanie wielokrotnie tych samych danych, wyzwalacz należy wyczyścić dane, które już zostało odczytane i przekazane do aplikacji logiki.

> [!TIP]
> Na przykład elementu webhook wzorzec, przejrzyj [próbki kontrolera wyzwalacza elementu webhook w serwisie GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

## <a name="secure-calls-to-your-apis-from-logic-apps"></a>Bezpieczne wywołania do swoje interfejsy API z aplikacji logiki

Po utworzeniu sieci niestandardowych interfejsów API, należy skonfigurować uwierzytelnianie dla interfejsów API, dzięki czemu można wywołać je bezpiecznie z aplikacji logiki. Dowiedz się [jak zabezpieczyć wywołań niestandardowych interfejsów API z aplikacji logiki](../logic-apps/logic-apps-custom-api-authentication.md).

## <a name="deploy-and-call-your-apis"></a>Wdrażanie i wywoływać interfejsy API

Po skonfigurowaniu uwierzytelniania, należy skonfigurować wdrożenie dla interfejsów API. Dowiedz się [sposób wdrażania i wywoływania niestandardowych interfejsów API z aplikacji logiki](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Publikowanie niestandardowych interfejsów API na platformie Azure

Aby udostępnić swoje niestandardowe interfejsy API dla innych użytkowników Logic Apps w usłudze Azure, musisz dodać zabezpieczeń i zarejestruj je łączników aplikacji logiki. Aby uzyskać więcej informacji, zobacz [Omówienie łączników niestandardowych](../logic-apps/custom-connector-overview.md). 

Aby udostępnić swoje niestandardowe interfejsy API dla wszystkich użytkowników w aplikacji logiki, Flow firmy Microsoft i PowerApps firmy Microsoft, należy dodać zabezpieczeń, Zarejestruj swoje interfejsy API łączników aplikacji logiki i wyznaczyć łączniki dla [programu Microsoft Azure certyfikowane](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/). 

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Aby uzyskać pomoc przy niestandardowych interfejsów API, skontaktuj się z [ customapishelp@microsoft.com ](mailto:customapishelp@microsoft.com).

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Aby pomóc w ulepszaniu usługi Logic Apps, przesyłaj pomysły lub głosuj na nie w [witrynie opinii użytkowników usługi Logic Apps](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Kolejne kroki

* [Obsługa błędów i wyjątków](../logic-apps/logic-apps-exception-handling.md)
* [Wywołaj wyzwalacz, lub zagnieżdżania logiki aplikacji za pomocą punktów końcowych HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Akcje i wyzwalaczy zbierania danych użycia](../logic-apps/logic-apps-pricing.md)
