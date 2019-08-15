---
title: Uwierzytelnianie i autoryzacja — Azure App Service | Microsoft Docs
description: Informacje koncepcyjne i Omówienie funkcji uwierzytelniania/autoryzacji dla Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/12/2019
ms.author: cephalin
ms.reviewer: mahender
ms.custom: seodec18
ms.openlocfilehash: 12ad82b0dda628c3a8cef7712322500c7a33517c
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68953826"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Uwierzytelnianie i autoryzacja w usłudze Azure App Service

> [!NOTE]
> W tej chwili usługa AAD v2 (w tym MSAL) nie jest obsługiwana w przypadku usługi Azure App Services i Azure Functions. Sprawdź aktualizacje.
>

Azure App Service zapewnia wbudowaną obsługę uwierzytelniania i autoryzacji, dzięki czemu możesz zalogować użytkowników i uzyskać dostęp do danych, pisząc kod minimalny lub bez kodu w aplikacji sieci Web, interfejsie API RESTful i zapleczu mobilnego, a także [Azure Functions](../azure-functions/functions-overview.md). W tym artykule opisano sposób, w jaki App Service upraszczają uwierzytelnianie i autoryzację aplikacji. 

Bezpieczne uwierzytelnianie i autoryzacja wymagają dokładnego poznania zabezpieczeń, w tym Federacji, szyfrowania, zarządzania [tokenami sieci Web JSON (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) , [typów dotacji](https://oauth.net/2/grant-types/)i tak dalej. App Service udostępnia te narzędzia, dzięki czemu można poświęcać więcej czasu i energii na zapewnienie klientom wartości biznesowej.

> [!NOTE]
> Nie jest wymagane używanie App Service do uwierzytelniania i autoryzacji. Wiele platform sieci Web jest powiązanych z funkcjami zabezpieczeń i można ich użyć, jeśli chcesz. Jeśli potrzebujesz większej elastyczności niż App Service zapewniasz, możesz również napisać własne narzędzia.  
>

Aby uzyskać informacje specyficzne dla natywnych aplikacji mobilnych, zobacz [uwierzytelnianie użytkowników i autoryzacja dla aplikacji mobilnych za pomocą Azure App Service](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Jak to działa

Moduł uwierzytelniania i autoryzacji jest uruchamiany w tej samej piaskownicy, w której znajduje się kod aplikacji. Gdy jest włączona, każde przychodzące żądanie HTTP przechodzi przez niego przed przekazaniem przez kod aplikacji.

![](media/app-service-authentication-overview/architecture.png)

Ten moduł obsługuje kilka rzeczy dla aplikacji:

- Uwierzytelnia użytkowników z określonym dostawcą
- Weryfikuje, zapisuje i odświeża tokeny
- Zarządza sesją uwierzytelnioną
- Wprowadza informacje o tożsamości do nagłówków żądań

Moduł zostanie uruchomiony niezależnie od kodu aplikacji i skonfigurowany przy użyciu ustawień aplikacji. Nie są wymagane żadne zestawy SDK, określone Języki ani zmiany w kodzie aplikacji. 

### <a name="user-claims"></a>Oświadczenia użytkownika

Dla wszystkich platform językowych, App Service udostępnia oświadczenia użytkownika do kodu przez wstrzyknięcie ich do nagłówków żądania. W przypadku aplikacji ASP.NET 4,6 App Service wypełnia [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) przy użyciu oświadczeń uwierzytelnionego użytkownika, więc można przestrzegać standardowego wzorca kodu .NET, w tym `[Authorize]` atrybutu. Podobnie w przypadku aplikacji PHP App Service wypełnia `_SERVER['REMOTE_USER']` zmienną. W przypadku aplikacji Java oświadczenia są [dostępne z Tomcat serwletu](containers/configure-language-java.md#authenticate-users-easy-auth).

W [](../azure-functions/functions-overview.md)przypadku Azure Functions `ClaimsPrincipal.Current` nie jest on odwodniony dla kodu platformy .NET, ale nadal można znaleźć oświadczenia użytkownika w nagłówkach żądania.

Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu do oświadczeń użytkowników](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Magazyn tokenów

App Service udostępnia wbudowany magazyn tokenów, który jest repozytorium tokenów skojarzonych z użytkownikami aplikacji sieci Web, interfejsów API lub natywnych aplikacji mobilnych. Po włączeniu uwierzytelniania dla dowolnego dostawcy ten magazyn tokenów jest natychmiast dostępny dla aplikacji. Jeśli kod aplikacji musi uzyskać dostęp do danych od tych dostawców w imieniu użytkownika, na przykład: 

- Opublikuj na osi czasu w usłudze Facebook uwierzytelnionego użytkownika
- Odczytaj dane firmowe użytkownika z interfejs API programu Graph Azure Active Directory, a nawet Microsoft Graph

Zwykle należy napisać kod, aby zbierać, przechowywać i odświeżać te tokeny w aplikacji. W przypadku magazynu tokenów wystarczy [pobrać tokeny](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) , gdy są potrzebne, i [poinformowanie App Service o konieczności](app-service-authentication-how-to.md#refresh-identity-provider-tokens) ich odświeżenia, gdy staną się nieprawidłowe. 

Tokeny identyfikatora, tokeny dostępu i tokeny odświeżania przechowywane w pamięci podręcznej dla sesji uwierzytelnionej i są dostępne tylko dla skojarzonego użytkownika.  

Jeśli nie musisz korzystać z tokenów w aplikacji, możesz wyłączyć magazyn tokenów.

### <a name="logging-and-tracing"></a>Rejestrowanie i śledzenie

Po [włączeniu rejestrowania aplikacji](troubleshoot-diagnostic-logs.md)będą widoczne ślady uwierzytelniania i autoryzacji bezpośrednio w plikach dziennika. Jeśli zobaczysz nieoczekiwany błąd uwierzytelniania, możesz wygodnie znaleźć wszystkie szczegóły, przeglądając istniejące dzienniki aplikacji. W przypadku włączenia [śledzenia nieudanych żądań](troubleshoot-diagnostic-logs.md)można zobaczyć dokładnie rolę modułu uwierzytelnianie i autoryzacja w nieprawidłowym żądaniu. W dziennikach śledzenia poszukaj odwołań do modułu o nazwie `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Dostawcy tożsamości

App Service używa [tożsamości federacyjnej](https://en.wikipedia.org/wiki/Federated_identity), w której dostawca tożsamości innej firmy zarządza tożsamościami użytkowników i przepływem uwierzytelniania. Pięć dostawców tożsamości jest dostępnych domyślnie: 

| Dostawca | Punkt końcowy logowania |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Konto Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/+/web/api/rest/oauth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Po włączeniu uwierzytelniania i autoryzacji jednego z tych dostawców, jego punkt końcowy logowania jest dostępny do uwierzytelnienia użytkownika i do weryfikacji tokenów uwierzytelniania od dostawcy. Możesz zapewnić użytkownikom dowolną liczbę tych opcji logowania. Możesz także zintegrować innego dostawcę tożsamości lub [własne niestandardowe rozwiązanie do tworzenia tożsamości][custom-auth].

## <a name="authentication-flow"></a>Przepływ uwierzytelniania

Przepływ uwierzytelniania jest taki sam dla wszystkich dostawców, ale różni się w zależności od tego, czy chcesz zalogować się przy użyciu zestawu SDK dostawcy:

- Bez zestawu SDK dostawcy: Aplikacja deleguje logowanie federacyjne do App Service. Jest to zazwyczaj przypadek z aplikacjami przeglądarki, które mogą przedstawić użytkownikowi stronę logowania dostawcy. Kod serwera zarządza procesem logowania, więc jest również nazywany _przepływem skierowanym do serwera_ lub _przepływem serwera_. Ta sprawa dotyczy aplikacji przeglądarki. Dotyczy to również natywnych aplikacji, które logują użytkowników przy użyciu zestawu SDK klienta Mobile Apps, ponieważ zestaw SDK otwiera widok sieci Web służący do logowania użytkowników przy użyciu uwierzytelniania App Service. 
- Z zestawem SDK dostawcy: Aplikacja loguje użytkowników do dostawcy ręcznie, a następnie przesyła token uwierzytelniania do App Service na potrzeby weryfikacji. Jest to zazwyczaj przypadek z aplikacjami bez przeglądarki, które nie mogą przedstawić użytkownikowi strony logowania dostawcy. Kod aplikacji zarządza procesem logowania, więc jest również nazywany _przepływem kierowanym przez klienta_ lub _przepływem klienta_. Ten przypadek dotyczy interfejsów API REST, [Azure Functions](../azure-functions/functions-overview.md)i klientów przeglądarki JavaScript, a także aplikacji przeglądarki, które potrzebują większej elastyczności w procesie logowania. Dotyczy to również natywnych aplikacji mobilnych, które logują użytkowników przy użyciu zestawu SDK dostawcy.

> [!NOTE]
> Wywołania z aplikacji zaufanej przeglądarki w App Service wywołuje inny interfejs API REST w App Service lub [Azure Functions](../azure-functions/functions-overview.md) można uwierzytelnić przy użyciu przepływu ukierunkowanego na serwer. Aby uzyskać więcej informacji, zobacz [Dostosowywanie uwierzytelniania i autoryzacji w App Service](app-service-authentication-how-to.md).
>

W poniższej tabeli przedstawiono kroki przepływu uwierzytelniania.

| Krok | Bez zestawu SDK dostawcy | Z zestawem SDK dostawcy |
| - | - | - |
| 1. Logowanie użytkownika | Przekierowuje klienta do programu `/.auth/login/<provider>`. | Kod klienta podpisuje użytkownika bezpośrednio przy użyciu zestawu SDK dostawcy i odbiera token uwierzytelniania. Aby uzyskać więcej informacji, zobacz dokumentację dostawcy. |
| 2. Uwierzytelnianie końcowe | Dostawca przekierowuje klienta do programu `/.auth/login/<provider>/callback`. | Kod klienta [zapisuje token od dostawcy](app-service-authentication-how-to.md#validate-tokens-from-providers) do `/.auth/login/<provider>` walidacji. |
| 3. Ustanów sesję uwierzytelnioną | App Service dodaje uwierzytelniony plik cookie do odpowiedzi. | App Service zwraca swój własny token uwierzytelniania do kodu klienta. |
| 4. Obsługuj uwierzytelnioną zawartość | Klient zawiera plik cookie uwierzytelniania w kolejnych żądaniach (automatycznie obsłużonych przez przeglądarkę). | Kod klienta przedstawia token uwierzytelniania w `X-ZUMO-AUTH` nagłówku (automatycznie obsłużony przez Mobile Apps zestawy SDK klienta). |

W przypadku przeglądarek klienta App Service może automatycznie kierować wszystkich nieuwierzytelnionych użytkowników `/.auth/login/<provider>`do programu. Możesz również przedstawić użytkownikom linki do jednego lub kilku `/.auth/login/<provider>` linków, aby zalogować się do aplikacji przy użyciu wybranego przez siebie dostawcy.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Zachowanie autoryzacji

W [Azure Portal](https://portal.azure.com)można skonfigurować autoryzację App Service z liczbą zachowań, gdy żądanie przychodzące nie zostanie uwierzytelnione.

![](media/app-service-authentication-overview/authorization-flow.png)

Poniższe nagłówki opisują opcje.

### <a name="allow-anonymous-requests-no-action"></a>Zezwalaj na żądania anonimowe (brak działania)

Ta opcja umożliwia rozliczanie autoryzacji nieuwierzytelnionego ruchu do kodu aplikacji. W przypadku żądań uwierzytelnionych App Service również przekazuje informacje o uwierzytelnianiu w nagłówkach HTTP. 

Ta opcja zapewnia większą elastyczność obsługi żądań anonimowych. Na przykład umożliwia prezentowanie użytkownikom [wielu dostawców logowania](app-service-authentication-how-to.md#use-multiple-sign-in-providers) . Jednak należy napisać kod. 

### <a name="allow-only-authenticated-requests"></a>Zezwalaj tylko na uwierzytelnione żądania

Opcja ta umożliwia **zalogowanie się \<przy użyciu > dostawcy**. App Service przekierowuje wszystkie anonimowe żądania do `/.auth/login/<provider>` wybranego dostawcy. Jeśli żądanie anonimowe pochodzi z natywnej aplikacji mobilnej, zwrócona odpowiedź to `HTTP 401 Unauthorized`.

W przypadku tej opcji nie trzeba pisać kodu uwierzytelniania w aplikacji. Bardziej precyzyjne uwierzytelnianie, takie jak autoryzacja specyficzna dla ról, może być obsługiwane przez sprawdzenie oświadczeń użytkownika (zobacz [dostęp do oświadczeń użytkowników](app-service-authentication-how-to.md#access-user-claims)).

## <a name="more-resources"></a>Więcej zasobów

[Samouczek: Uwierzytelnianie i Autoryzowanie użytkowników na całym Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)  
[Samouczek: Uwierzytelnianie i Autoryzowanie użytkowników w Azure App Service dla systemu Linux](containers/tutorial-auth-aad.md)  
[Dostosuj uwierzytelnianie i autoryzację w App Service](app-service-authentication-how-to.md)

Przewodniki z poszczególnymi dostawcami:

* [Jak skonfigurować aplikację do używania logowania w usłudze Azure Active Directory][AAD]
* [Jak skonfigurować aplikację do używania logowania usługi Facebook][Facebook]
* [Jak skonfigurować aplikację do używania logowania usługi Google][Google]
* [Jak skonfigurować aplikację do używania logowania za pomocą konta Microsoft][MSA]
* [Jak skonfigurować aplikację do używania logowania usługi Twitter][Twitter]
* [Instrukcje: Używanie uwierzytelniania niestandardowego dla aplikacji][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
