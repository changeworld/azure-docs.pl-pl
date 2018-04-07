---
title: Uwierzytelnianie i autoryzację w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Odwołanie koncepcyjne i omówienie uwierzytelniania / autoryzacji funkcji Azure App Service
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/29/2016
ms.author: mahender
ms.openlocfilehash: 342aeee25a7cb9f6a0f5af055d04e67d0c52db80
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Uwierzytelnianie i autoryzacja w usłudze Azure App Service

Usługa aplikacji Azure oferuje wbudowane uwierzytelnianie i autoryzacja obsługi, aby można było zarejestrować użytkowników i uzyskać dostęp do danych przez zapisywanie minimalnego lub Brak kodu w aplikacji sieci web, interfejsu API i przenośnych zaplecza, a także [usługi Azure Functions](../azure-functions/functions-overview.md). W tym artykule opisano, jak usługi App Service ułatwia uwierzytelniania i autoryzacji dla aplikacji. 

Bezpieczne uwierzytelnianie i autoryzacja wymaga dokładnego wiedzę na temat zabezpieczeń, w tym federacyjnych, szyfrowania, [tokenów sieci web JSON (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) zarządzania, [typy przydziałów](https://oauth.net/2/grant-types/)i tak dalej. Usługa App Service zapewnia tych narzędzi, aby poświęcić więcej czasu i pracy na zapewnienie wartość biznesową do klienta.

> [!NOTE]
> Nie masz wymaganych do używania aplikacji usługi do uwierzytelniania i autoryzacji. Wiele platform sieci web są powiązane z funkcjami zabezpieczeń i ich użycia, jeśli chcesz. Aby uzyskać większą elastyczność niż Usługa App Service zapewnia można również napisać własne narzędzia.  
>

Aby uzyskać informacje specyficzne dla natywnych aplikacji mobilnych, zobacz [uwierzytelniania i autoryzacji dla aplikacji mobilnych w usłudze aplikacji Azure użytkowników](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Jak to działa

Moduł uwierzytelniania i autoryzacji jest uruchamiany w tym samym piaskownicy jako kod aplikacji. Gdy jest włączona, co przychodzące żądanie HTTP przechodzą przez go przed obsługiwanych przez kod aplikacji.

![](media/app-service-authentication-overview/architecture.png)

Ten moduł obsługuje kilka kwestii dla aplikacji:

- Służy do uwierzytelniania użytkowników z określonym dostawcą
- Weryfikuje, są przechowywane i odświeża tokenów
- Zarządza sesja uwierzytelniona
- Injects informacji o tożsamości w nagłówki żądania

Moduł jest uruchamiana niezależnie od kodu aplikacji i jest konfigurowana przy użyciu ustawień aplikacji. Nie zestawów SDK, określonych języków lub zmiany w kodzie aplikacji są wymagane. 

### <a name="user-claims"></a>Oświadczenia użytkowników

Dla wszystkich środowisk języka App Service udostępnia oświadczenia użytkownika do kodu przez wstrzykiwanie je w nagłówku żądania. Dla aplikacji platformy ASP.NET 4.6, wypełnia App Service [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) z oświadczeń uwierzytelnionego użytkownika, aby można było wykonywać standardowy wzorzec kodu platformy .NET, w tym `[Authorize]` atrybutu. Podobnie w przypadku aplikacji PHP, usługa aplikacji wypełnia `_SERVER['REMOTE_USER']` zmiennej.

Dla [usługi Azure Functions](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` nie jest uwodniony dla kodu platformy .NET, ale nadal można znaleźć oświadczeń użytkowników w nagłówkach żądania.

Aby uzyskać więcej informacji, zobacz [dostęp do oświadczeń użytkowników](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Token magazynu

Usługa App Service zawiera wbudowane magazynu tokenów, która jest repozytorium tokenów, które są skojarzone z użytkownikami aplikacji sieci web, interfejsów API lub natywnych aplikacji mobilnych. Po włączeniu uwierzytelniania za pomocą dowolnego dostawcy tego magazynu tokenów jest natychmiast dostępna do aplikacji. Jeśli kod aplikacji musi mieć dostęp do danych z usług tych dostawców w imieniu użytkownika, takich jak: 

- ogłasza informacji pod uwierzytelnionego użytkownika usługi Facebook w osi czasu
- Odczytywanie danych firmy użytkownika z usługi Azure Active Directory interfejsu API programu Graph lub nawet Microsoft Graph

Tokeny identyfikatora, tokeny dostępu i tokenów odświeżania pamięci podręcznej dla sesja uwierzytelniona i są one dostępne tylko dla skojarzonego użytkownika.  

Zazwyczaj należy napisać kod zbierania, przechowywania i Odśwież te tokeny w aplikacji. Ze sklepem tokenu właśnie [pobrać tokenów](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) gdy są potrzebne i [Poinformuj usługi aplikacji, aby odświeżyć je](app-service-authentication-how-to.md#refresh-access-tokens) kiedy staną się nieprawidłowe. 

Jeśli nie potrzebujesz do pracy z tokenów w aplikacji, można wyłączyć magazynu tokenów.

### <a name="logging-and-tracing"></a>Rejestrowanie i śledzenie

Jeśli użytkownik [Włącz rejestrowanie aplikacji](web-sites-enable-diagnostic-log.md), zobaczysz uwierzytelnianie i autoryzacja śladów bezpośrednio w plikach dziennika. Jeśli zostanie wyświetlony błąd uwierzytelniania, który Nieoczekiwane wyrażenie, wszystkie szczegóły można znaleźć wygodnie przeszukując istniejących dzienników aplikacji. Po włączeniu [śledzenie nieudanych żądań](web-sites-enable-diagnostic-log.md), można zobaczyć dokładnie jaką rolę uwierzytelniania i autoryzacji modułu może mieć odtwarzane nieudanych żądań. Poszukaj w dziennikach śledzenia odwołania do modułu o nazwie `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Dostawcy tożsamości

Korzysta z usługi aplikacji [tożsamości federacyjnych](https://en.wikipedia.org/wiki/Federated_identity), w którym dostawca tożsamości innych firm zarządza tożsamości użytkowników i przepływ uwierzytelniania dla Ciebie. Domyślnie dostępne są pięć dostawców tożsamości: 

| Dostawca | Punkt końcowy logowania |
| - | - |
| [Azure Active Directory](../active-directory/active-directory-whatis.md) | `/.auth/login/aad` |
| [Konto Microsoft](../active-directory/develop/active-directory-appmodel-v2-overview.md) | `/.auth/login/microsoft` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/+/web/api/rest/oauth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/docs/basics/authentication) | `/.auth/login/twitter` |

Po włączeniu uwierzytelniania i autoryzacji z jednego z tych dostawców punktu końcowego logowania jest dostępna, do uwierzytelniania użytkowników i sprawdzania poprawności tokenów uwierzytelnienia od dostawcy. Z łatwością można zapewnić użytkownikom z dowolną liczbę tych opcji logowania. Można również zintegrować innego dostawcy tożsamości lub [rozwiązania tożsamość niestandardowa][custom-auth].

## <a name="authentication-flow"></a>Przepływ uwierzytelniania

Przepływ uwierzytelniania jest taka sama dla wszystkich dostawców, ale zależy od tego, czy mają być Zaloguj się przy użyciu zestawu SDK w dostawcy:

- Bez dostawcy zestawu SDK: deleguje aplikacji federacyjnej logowania do usługi App Service. Jest to zazwyczaj w przypadku aplikacji przeglądarki, które może ona dostawcy strony logowania dla użytkownika. Kod serwera zarządza procesu logowania, więc jest również nazywany _skierowane do serwera przepływ_ lub _przepływu serwera_. Przypadek ma zastosowanie do aplikacji sieci web. Ma również zastosowanie do natywnej aplikacji, które logują użytkowników przy użyciu klienta Mobile Apps SDK, ponieważ zestaw SDK spowoduje otwarcie widoku sieci web logowania użytkowników przy użyciu uwierzytelniania usługi aplikacji. 
- Z dostawcą zestawu SDK: aplikacja loguje się użytkownik ręcznie, a następnie przesyła token uwierzytelniania do aplikacji usługi do sprawdzania poprawności. Jest to zwykle z aplikacji bez przeglądarki, które użytkownik nie widzi strony logowania dla dostawcy. Kod aplikacji zarządza procesu logowania, więc jest również nazywany _klient skierowany przepływu_ lub _przepływu klienta_. Przypadek ma zastosowanie do interfejsów API REST, [usługi Azure Functions](../azure-functions/functions-overview.md)i klientów w przeglądarkach JavaScript, a także aplikacje sieci web, które muszą większą elastyczność podczas procesu logowania. Ma również zastosowanie do natywnej aplikacji mobilnych, które logują użytkowników przy użyciu zestawu SDK w dostawcy.

> [!NOTE]
> Wywołania z przeglądarki zaufanych aplikacji w usłudze App Service wymaga innego interfejsu API REST w usłudze App Service lub [usługi Azure Functions](../azure-functions/functions-overview.md) mogą być uwierzytelniane przy użyciu przepływu skierowane do serwera. Aby uzyskać więcej informacji, zobacz [uwierzytelniać użytkowników za pomocą usługi Azure App Service]().
>

W poniższej tabeli przedstawiono kroki przepływu uwierzytelniania.

| Krok | Bez dostawcy zestawu SDK | Z dostawcą zestawu SDK |
| - | - | - |
| 1. Logowania użytkownika w | Przekierowuje klienta do `/.auth/login/<provider>`. | Kod klienta loguje użytkownika bezpośrednio z zestawem SDK dostawcy i otrzymuje token uwierzytelniania. Informacje Zobacz dokumentację dostawcy. |
| 2. Uwierzytelnianie końcowe | Dostawca przekierowuje klienta do `/.auth/login/<provider>/callback`. | Kod klienta ogłoszeń tokenu od dostawcy, aby `/.auth/login/<provider>` do weryfikacji. |
| 3. Ustanowienie sesji uwierzytelnionych | Usługi aplikacji — dodaje uwierzytelnionego pliku cookie do odpowiedzi. | Usługi aplikacji — zwraca własny token uwierzytelniania do kodu klienta. |
| 4. Udostępniać zawartość uwierzytelnionego | Klient dołącza pliku cookie uwierzytelniania w kolejnych żądań (automatycznie obsługiwane przez przeglądarkę). | Kod klienta stanowi token uwierzytelniania w `X-ZUMO-AUTH` nagłówka (automatycznie obsługiwane przez klienta Mobile Apps SDK). |

Dla przeglądarki klienta usługi aplikacji może automatyczne kierowanie wszystkich nieuwierzytelnionym użytkownikom `/.auth/login/<provider>`. Można także przedstawić użytkowników z co najmniej jednym `/.auth/login/<provider>` łącza do logowania do aplikacji przy użyciu dostawcy wyboru.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Zachowanie autoryzacji

W [portalu Azure](https://portal.azure.com), można skonfigurować liczbę zachowań autoryzacja usługi App Service.

![](media/app-service-authentication-overview/authorization-flow.png)

Następujące nagłówki opisano opcje.

### <a name="allow-all-requests-default"></a>Zezwalaj na wszystkie żądania (ustawienie domyślne)

Uwierzytelnianie i autoryzacja nie jest obsługiwany przez usługę App Service (wyłączone). 

Wybierz tę opcję, jeśli nie ma potrzeby uwierzytelniania i autoryzacji, lub aby napisać własny kod uwierzytelniania i autoryzacji.

### <a name="allow-only-authenticated-requests"></a>Zezwalaj tylko uwierzytelnionego żądania.

Ta opcja jest **Zaloguj się przy użyciu \<dostawcy >**. Usługi aplikacji — wykonuje przekierowanie wszystkich żądań anonimowych do `/.auth/login/<provider>` dla dostawcy, możesz wybrać. Jeśli anonimowe żądanie pochodzi z natywnych aplikacji mobilnej, zwrócona odpowiedź jest `HTTP 401 Unauthorized`.

Po wybraniu tej opcji nie należy do pisania kodu uwierzytelniania w aplikacji. Bardziej precyzyjną autoryzacji, takich jak autoryzacja specyficzne dla roli, mogą być obsługiwane przez badanie oświadczenia użytkownika (zobacz [dostęp do oświadczeń użytkowników](app-service-authentication-how-to.md#access-user-claims)).

### <a name="allow-all-requests-but-validate-authenticated-requests"></a>Zezwalaj na wszystkie żądania, ale poprawności żądań uwierzytelnionych

Ta opcja jest **Zezwalaj na anonimowe żądania**. Ta opcja włącza opcję Uwierzytelnianie i autoryzację w usłudze App Service, ale różni się decyzji dotyczących autoryzacji w kodzie aplikacji. Dla żądań uwierzytelnionych usługi aplikacji przekazuje również informacje o uwierzytelnianiu w nagłówkach HTTP. 

Opcja ta zapewnia większą elastyczność podczas obsługi żądań anonimowych. Na przykład umożliwia [istnieje wiele opcji logowania](app-service-authentication-how-to.md#configure-multiple-sign-in-options) dla użytkowników. Niemniej jednak należy napisać kod. 

## <a name="more-resources"></a>Więcej zasobów

[Samouczek: Uwierzytelniania i autoryzacji użytkowników end-to-end w usłudze Azure App Service](app-service-web-tutorial-auth-aad.md)  
[Dostosowywanie uwierzytelniania i autoryzacji w usłudze App Service](app-service-authentication-how-to.md)

Przewodniki porad specyficznego dla dostawcy:

* [Jak skonfigurować aplikację do używania nazwy logowania w usłudze Azure Active Directory][AAD]
* [Jak skonfigurować aplikację do używania logowania usługi Facebook][Facebook]
* [Jak skonfigurować aplikację do używania logowania Google][Google]
* [Jak skonfigurować aplikację do używania Account Microsoft logowania][MSA]
* [Jak skonfigurować aplikację do używania logowania usługi Twitter][Twitter]
* [Porady: użycie uwierzytelniania niestandardowego dla aplikacji][custom-auth]

[AAD]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: app-service-mobile-how-to-configure-google-authentication.md
[MSA]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
