---
title: Uwierzytelnianie i autoryzacja — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Przewodnik koncepcyjny oraz omówienie uwierzytelniania / autoryzacji funkcji Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/24/2018
ms.author: mahender,cephalin
ms.custom: seodec18
ms.openlocfilehash: d914e3ad3043b2671e154d1616c6800f34415c11
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402749"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Uwierzytelnianie i autoryzacja w usłudze Azure App Service

> [!NOTE]
> W tej chwili usługi AAD w wersji 2 (w tym MSAL) nie jest obsługiwana dla usługi Azure App Services i Azure Functions. Sprawdź, czy aktualizacje.
>

Usługa Azure App Service udostępnia wbudowanego uwierzytelniania i autoryzacji pomocy technicznej, aby można było logowania użytkowników i uzyskać dostęp do danych przez zapisywanie minimalny lub Brak kodu w aplikacji sieci web, interfejsów API RESTful i zapleczem aplikacji mobilnej, a także [usługi Azure Functions](../azure-functions/functions-overview.md). W tym artykule opisano, jak usługa App Service pomaga uprościć uwierzytelniania i autoryzacji dla aplikacji. 

Bezpieczne uwierzytelnianie i autoryzacja wymagają wnikliwie poznać zabezpieczeń, w tym Federacji, szyfrowanie, [tokenów sieci web JSON (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) zarządzania [typy przydziałów](https://oauth.net/2/grant-types/)i tak dalej. Usługa App Service udostępnia te narzędzia, dzięki czemu możesz poświęcić więcej czasu i energii na zapewniającego wartość biznesową do klienta.

> [!NOTE]
> Nie masz wymagane do używania usługi App Service do uwierzytelniania i autoryzacji. Wiele środowisk dla sieci web są powiązane z funkcjami zabezpieczeń i mogą być używane, jeśli chcesz. Jeśli potrzebujesz większej elastyczności niż oferuje usługi App Service można również napisać własne narzędzia.  
>

Aby uzyskać informacje specyficzne dla natywnych aplikacji mobilnych, zobacz [uwierzytelniania i autoryzacji dla aplikacji mobilnych w usłudze Azure App Service użytkowników](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Jak to działa

Moduł uwierzytelniania i autoryzacji jest uruchamiany w tej samej piaskownicy jako kod aplikacji. Po jej włączeniu każdego przychodzącego żądania HTTP przez nią przechodzą przed obsługiwanych przez kod aplikacji.

![](media/app-service-authentication-overview/architecture.png)

Ten moduł obsługuje kilka działań dla twojej aplikacji:

- Uwierzytelnia użytkowników za pomocą określonego dostawcy
- Sprawdza poprawność, przechowuje i odświeża tokenów
- Zarządza sesja uwierzytelniona
- Wprowadza informacje o tożsamościach w nagłówki żądania

Moduł jest uruchamiana niezależnie od kodu aplikacji i jest skonfigurowany przy użyciu ustawień aplikacji. Nie zestawów SDK, określonych języków lub zmian w kodzie aplikacji są wymagane. 

### <a name="user-claims"></a>Oświadczenia użytkownika

Dla wszystkich środowisk języka usługi App Service udostępnia oświadczenia użytkownika do kodu przez iniekcję je w nagłówku żądania. W przypadku aplikacji platformy ASP.NET 4.6, wypełnia usługi App Service [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) roszczeń uwierzytelnionego użytkownika, aby można było wykonywać standardowego wzorca kodu platformy .NET, łącznie z `[Authorize]` atrybutu. Podobnie w przypadku aplikacji PHP, usługa App Service umożliwia wypełnienie `_SERVER['REMOTE_USER']` zmiennej.

Aby uzyskać [usługi Azure Functions](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` nie jest uwodniony dla kodu platformy .NET, ale można nadal znaleźć oświadczenia użytkownika w nagłówku żądania.

Aby uzyskać więcej informacji, zobacz [dostęp do oświadczenia użytkownika](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Przechowywanie tokenów

Usługa App Service zapewnia wbudowaną magazynu tokenu, która jest repozytorium tokenów, które są skojarzone z użytkownikami w Twojej aplikacji sieci web, interfejsów API lub natywnych aplikacji mobilnych. Po włączeniu uwierzytelniania za pomocą dowolnego dostawcy tego tokenu magazynu jest natychmiast dostępna dla aplikacji. Jeśli kod aplikacji wymaga dostępu do danych z tych dostawców, w imieniu użytkownika, takie jak: 

- wpis na osi czasu w serwisie Facebook uwierzytelnionego użytkownika
- Odczytywanie danych firmowych przez użytkownika z usługi Azure Active Directory interfejsu API programu Graph lub nawet programu Microsoft Graph

Zazwyczaj należy napisać kod do zbierania, przechowywania i odświeżania tych tokenów w aplikacji. Ze sklepem token został właśnie [pobieranie tokenów](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) kiedy ich potrzebują i [nakazać usłudze App Service, aby odświeżyć je](app-service-authentication-how-to.md#refresh-identity-provider-tokens) kiedy staną się nieprawidłowe. 

Tokeny identyfikatora, tokenami dostępu i tokenów odświeżania pamięci podręcznej i sesja uwierzytelniona i są one dostępne tylko dla skojarzonego użytkownika.  

Jeśli nie potrzebujesz do pracy z tokenami w swojej aplikacji, można wyłączyć magazynu tokenów.

### <a name="logging-and-tracing"></a>Rejestrowanie i śledzenie

Jeśli użytkownik [Włącz rejestrowanie aplikacji](troubleshoot-diagnostic-logs.md), zobaczysz ślady uwierzytelnianie i autoryzację bezpośrednio w plikach dziennika. Jeśli zostanie wyświetlony błąd uwierzytelniania nie oczekuje się, wszystkie szczegółowe informacje można znaleźć wygodnie przez wyszukiwanie w dziennikach istniejących aplikacji. Po włączeniu [Śledzenie żądań zakończonych niepowodzeniem](troubleshoot-diagnostic-logs.md), możesz zobaczyć dokładnie, jaką rolę uwierzytelniania i moduł autoryzacji, może być odgrywają w żądaniu nie powiodło się. Wyszukaj w dziennikach śledzenia odwołania do modułu o nazwie `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Dostawcy tożsamości

Usługa App Service używa [tożsamość federacyjna](https://en.wikipedia.org/wiki/Federated_identity), zarządza dostawcy tożsamości innej firmy tożsamości użytkowników i przepływ uwierzytelniania dla Ciebie. Domyślnie dostępne są pięciu dostawców tożsamości: 

| Dostawca | Punkt końcowy logowania |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Konto Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/+/web/api/rest/oauth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Po włączeniu uwierzytelniania i autoryzacji przy użyciu jednego z tych dostawców punktu końcowego logowania jest dostępna, do uwierzytelniania użytkowników i do weryfikacji tokenów uwierzytelniania od dostawcy. Z łatwością, można zapewnić użytkownikom z dowolnej liczby tych opcji logowania. Można również zintegrować innego dostawcy tożsamości lub [rozwiązania tożsamość niestandardowa][custom-auth].

## <a name="authentication-flow"></a>Przepływ uwierzytelniania

Przepływ uwierzytelniania jest taka sama dla wszystkich dostawców, ale różnią się zależnie od tego, czy chcesz zalogować się przy użyciu dostawcy SDK:

- Bez dostawca zestawu SDK: Aplikacja deleguje logowania federacyjnego do usługi App Service. Zazwyczaj jest to w przypadku aplikacji przeglądarki, które może powodować dostawcy strony logowania do użytkownika. Kod serwera zarządza procesem logowania, dzięki czemu jest również nazywany _skierowane do serwera usługi flow_ lub _przepływ serwera_. Ten przypadek ma zastosowanie do aplikacji przeglądarki. Ma również zastosowanie do aplikacji natywnych, które logują użytkowników przy użyciu zestawu SDK klienta funkcji Mobile Apps, ponieważ zestaw SDK spowoduje otwarcie widoku sieci web do logują użytkowników przy użyciu uwierzytelniania usługi App Service. 
- Za pomocą dostawcy SDK: Aplikacja logowania użytkowników w dostawcy ręcznie, a następnie przesyła je token uwierzytelniania w usłudze App Service do sprawdzania poprawności. Zazwyczaj jest to w przypadku aplikacji bez przeglądarki, które użytkownik nie widzi strony logowania dla dostawcy. Kod aplikacji zarządza procesem logowania, dzięki czemu jest również nazywany _skierowane do klientów usługi flow_ lub _przepływ klienta_. Ten przypadek ma zastosowanie do interfejsów API REST, [usługi Azure Functions](../azure-functions/functions-overview.md)oraz klientów języka JavaScript w przeglądarkach, jak również aplikacje przeglądarki, które muszą większą elastyczność w procesie logowania. Ma również zastosowanie do natywnych aplikacji mobilnych, które logują użytkowników przy użyciu zestawu SDK w dostawcy.

> [!NOTE]
> Wywołania z aplikacji przeglądarki zaufane w usłudze App Service wywołuje innego interfejsu API REST w usłudze App Service lub [usługi Azure Functions](../azure-functions/functions-overview.md) mogą być uwierzytelniane przy użyciu usługi flow skierowane do serwera. Aby uzyskać więcej informacji, zobacz [dostosowywać uwierzytelnianie i autoryzacja w usłudze App Service](app-service-authentication-how-to.md).
>

W poniższej tabeli przedstawiono kroki przepływu uwierzytelniania.

| Krok | Bez dostawcy zestawu SDK | Za pomocą dostawcy zestawu SDK |
| - | - | - |
| 1. Logowanie użytkownika | Przekierowuje klienta do `/.auth/login/<provider>`. | Kod klienta loguje użytkownika bezpośrednio z zestawem SDK dostawcy i odbiera token uwierzytelniania. Aby uzyskać informacje Zobacz dokumentację dostawcy. |
| 2. Uwierzytelnianie końcowe | Dostawca przekierowuje klienta do `/.auth/login/<provider>/callback`. | Kod klienta [publikuje tokenu od dostawcy](app-service-authentication-how-to.md#validate-tokens-from-providers) do `/.auth/login/<provider>` do sprawdzania poprawności. |
| 3. Ustanowić sesja uwierzytelniona | Usługa App Service dodaje uwierzytelnionego plik cookie odpowiedzi. | Usługa App Service zwraca swój własny token uwierzytelniania dla kodu klienta. |
| 4. Udostępnianie zawartości uwierzytelnionego | Klient dołącza plik cookie uwierzytelniania w kolejnych żądań (automatycznie obsługiwane przez przeglądarki). | Kod klienta stanowi token uwierzytelniania w `X-ZUMO-AUTH` nagłówka (automatycznie obsługiwane przez zestawy SDK klientów Mobile Apps). |

Do przeglądarki klienta usługi App Service można automatycznie kierować wszystkie nieuwierzytelnionym użytkownikom `/.auth/login/<provider>`. Może także prezentować użytkownikom z co najmniej `/.auth/login/<provider>` łącza do logowania do aplikacji z użyciem wybranego dostawcy.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Zachowanie autoryzacji

W [witryny Azure portal](https://portal.azure.com), można skonfigurować autoryzacji usługi App Service z liczbą zachowania.

![](media/app-service-authentication-overview/authorization-flow.png)

Następujące nagłówki opisano opcje.

### <a name="allow-all-requests-default"></a>Zezwalaj na wszystkie żądania (ustawienie domyślne)

Uwierzytelnianie i autoryzacja nie są zarządzane przez usługę App Service (wyłączone). 

Wybierz tę opcję, jeśli nie potrzebujesz, uwierzytelnianie i autoryzacja, czy ma być pisania własnego kodu uwierzytelniania i autoryzacji.

### <a name="allow-only-authenticated-requests"></a>Zezwalaj na tylko uwierzytelnionego żądania

Ta opcja jest **Zaloguj się przy użyciu \<dostawcy >**. Usługa App Service przekierowuje wszystkie żądania anonimowe `/.auth/login/<provider>` dla wybranego dostawcy. Jeśli anonimowe żądania pochodzą z natywnych aplikacji mobilnych, zwrócona odpowiedź to `HTTP 401 Unauthorized`.

Po wybraniu tej opcji nie trzeba pisać kodu uwierzytelniania w aplikacji. Bardziej precyzyjną autoryzacji, takich jak autoryzacja specyficzne dla ról mogą być obsługiwane, sprawdzając oświadczenia użytkowników (zobacz [dostęp do oświadczenia użytkownika](app-service-authentication-how-to.md#access-user-claims)).

### <a name="allow-all-requests-but-validate-authenticated-requests"></a>Zezwalaj na wszystkie żądania, ale weryfikację żądań uwierzytelnionych

Ta opcja jest **Zezwalaj na anonimowe żądania**. Ta opcja włącza opcję Uwierzytelnianie i autoryzacja w usłudze App Service, ale odracza decyzji dotyczących autoryzacji w kodzie aplikacji. Dla żądań uwierzytelnionych usługi App Service również przekazuje informacje o uwierzytelnianiu w nagłówkach HTTP. 

Ta opcja zapewnia większą elastyczność obsługi żądań anonimowych. Na przykład, pozwala on [istnieje wielu dostawców logowania](app-service-authentication-how-to.md#use-multiple-sign-in-providers) dla użytkowników. Jednakże należy napisać kod. 

## <a name="more-resources"></a>Więcej zasobów

[Samouczek: Uwierzytelnianie i autoryzowanie użytkowników end-to-end w usłudze Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)  
[Samouczek: Uwierzytelnianie i autoryzowanie użytkowników end-to-end w usłudze Azure App Service dla systemu Linux](containers/tutorial-auth-aad.md)  
[Dostosowywanie uwierzytelniania i autoryzacji w usłudze App Service](app-service-authentication-how-to.md)

Przewodniki z instrukcjami właściwe dla dostawcy:

* [Jak skonfigurować aplikację do używania logowania usługi Azure Active Directory][AAD]
* [Jak skonfigurować aplikację do używania logowania usługi Facebook][Facebook]
* [Jak skonfigurować aplikację do używania logowania usługi Google][Google]
* [Jak skonfigurować aplikację do używania Microsoft Account login][MSA]
* [Jak skonfigurować aplikację do używania logowania usługi Twitter][Twitter]
* [Instrukcje: Użyj uwierzytelniania niestandardowego dla aplikacji][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
