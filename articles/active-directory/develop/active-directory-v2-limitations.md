---
title: Ograniczenia punktu końcowego v2.0 w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Lista ograniczenia dla punktu końcowego v2.0 usługi Azure AD.
services: active-directory
documentationcenter: ''
author: dstrockis
manager: mtillman
editor: ''
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: a36f55c57a75f671b3e5eeae3d91ff60483afd37
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="should-i-use-the-v20-endpoint"></a>Należy użyć punktu końcowego v2.0?
Podczas tworzenia aplikacji, które integrują się z usługą Azure Active Directory, należy zdecydować, czy protokołów uwierzytelniania i punktu końcowego v2.0 odpowiadają Twoim potrzebom. Oryginalny punktu końcowego usługi Azure Active Directory firmy jest nadal w pełni obsługiwane i pod pewnymi względami jest więcej zaawansowanej funkcji niż wersja 2.0. Jednak punktu końcowego v2.0 [wprowadzono znaczące korzyści](active-directory-v2-compare.md) dla deweloperów.

Oto nasze uproszczony zalecenia dla deweloperów w tym momencie:

* Jeśli zachodzi potrzeba obsługi osobistego konta Microsoft w aplikacji, użyj punktu końcowego v2.0. Jednak zanim to zrobisz, należy się upewnić, że rozumiesz, ograniczenia, które omówiono w tym artykule.
* Jeśli aplikacja wymaga tylko do kont służbowych obsługuje pracy firmy Microsoft, nie używaj punktu końcowego v2.0. Zamiast tego należy odwoływać się do naszej [przewodnik dewelopera usługi Azure AD](active-directory-developers-guide.md).

Wraz z upływem czasu, aby wyeliminować ograniczenia wymienione w tym miejscu, tak aby tylko będą musieli używać punktu końcowego v2.0 wzrośnie punktu końcowego v2.0. Do tego czasu w tym artykule ma pomóc w określeniu, czy punktem końcowym v2.0 jest odpowiednie dla Ciebie. Firma Microsoft będzie zaktualizować ten artykuł, aby odzwierciedlały bieżący stan punktu końcowego v2.0. Sprawdź obliczyć ponownie wymagań możliwościami w wersji 2.0.

Jeśli masz istniejącą aplikację usługi Azure AD, która nie korzysta z punktu końcowego v2.0 jest niepotrzebna od samego początku. W przyszłości firma Microsoft zapewnia łatwy sposób można użyć istniejącej aplikacji usługi Azure AD z punktem końcowym v2.0.

## <a name="restrictions-on-app-types"></a>Ograniczenia dotyczące typów aplikacji
Obecnie następujące aplikacje nie są obsługiwane przez punkt końcowy w wersji 2.0. Opis typów obsługiwanych aplikacji, zobacz [typy aplikacji dla punktu końcowego v2.0 usługi Azure Active Directory](active-directory-v2-flows.md).

### <a name="standalone-web-apis"></a>Interfejsy API sieci Web autonomiczny
Można użyć do punktu końcowego v2.0 [kompilacji API sieci Web chronionej OAuth 2.0](active-directory-v2-flows.md#web-apis). Jednak tego interfejsu API sieci Web może odbierać tokeny tylko z aplikacji, która ma tego samego identyfikatora aplikacji Nie masz dostępu do interfejsu API sieci Web z klienta, który ma inną nazwę aplikacji. Klient będzie mógł zażądać lub uzyskać uprawnienia do interfejsu API sieci Web.

Aby zobaczyć, jak utworzyć interfejs API sieci Web, który akceptuje tokeny od klienta, który ma ten sam identyfikator aplikacji, zobacz przykłady interfejsu API sieci Web punktu końcowego v2.0 w naszym [wprowadzenie](active-directory-appmodel-v2-overview.md#getting-started) sekcji.

## <a name="restrictions-on-app-registrations"></a>Ograniczenia dotyczące rejestracji aplikacji
Obecnie dla każdej aplikacji, który chcesz zintegrować z punktem końcowym v2.0, należy utworzyć rejestracji aplikacji w nowej [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Istniejące usługi Azure AD lub w aplikacji konta Microsoft nie są zgodne z punktem końcowym v2.0. Aplikacje, które są zarejestrowane w żadnym portalem innych niż Portal rejestracji aplikacji nie są zgodne z punktem końcowym v2.0. W przyszłości firma Microsoft planuje umożliwiają korzystanie z istniejących aplikacji jako aplikacji w wersji 2.0. Jednak nie istnieje aktualnie nie ma ścieżki migracji dla istniejącej aplikacji do pracy z punktem końcowym v2.0.

Ponadto rejestracji aplikacji, które możesz utworzyć w [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ma następujące ostrzeżenia:

* Tylko dwa klucze tajne aplikacji są dozwolone na identyfikator aplikacji.
* Można wyświetlać i zarządzać tylko konto dewelopera pojedynczego rejestracji aplikacji zarejestrowanych przez użytkownika z osobistego konta Microsoft. Nie można współużytkować między wielu deweloperów.  Jeśli chcesz udostępnić rejestracji aplikacji wśród wielu deweloperów, można utworzyć aplikacji po zalogowaniu się do portalu rejestracji przy użyciu konta usługi Azure AD.
* Istnieje kilka ograniczeń na format przekierowania URI, który jest dozwolony. Aby uzyskać więcej informacji na temat identyfikatorów URI przekierowania zobacz następną sekcję.

## <a name="restrictions-on-redirect-uris"></a>Ograniczenia dotyczące przekierowania URI
Aplikacje, które są zarejestrowane w portalu rejestracji aplikacji są obecnie ograniczone do określonych wartości identyfikatora URI przekierowania. Przekierowanie URI dla usług i aplikacji sieci web musi rozpoczynać się od systemu `https`, a wszystkie wartości identyfikatora URI przekierowania musi współdzielenie jednej domeny DNS. Na przykład nie można zarejestrować aplikacji sieci web o jednym z tych identyfikatorów URI przekierowania:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

System rejestracji porównuje nazwę DNS cały identyfikator URI przekierowania istniejącą nazwę DNS przekierowania URI, który dodajesz. Żądanie dodania nazwy DNS zakończy się niepowodzeniem, jeśli będzie spełniony jeden z następujących warunków:  

* Całą nazwę DNS nowy identyfikator URI przekierowania nie jest zgodna z nazwą DNS istniejących przekierowania URI.
* Nazwy DNS cały nowy identyfikator URI przekierowania nie jest poddomeną istniejący identyfikator URI przekierowania.

Na przykład, jeśli aplikacja ma ten identyfikator URI przekierowania:

`https://login.contoso.com`

Można dodać do niego adres w następujący sposób:

`https://login.contoso.com/new`

W takim przypadku nazwa DNS jest idealnie zgodna. Można też zrobić tak:

`https://new.login.contoso.com`

W takim przypadku przywoływana jest poddomena DNS domeny login.contoso.com. Jeśli chcesz mieć aplikację, która ma logowania east.contoso.com i west.contoso.com logowania jako identyfikator URI przekierowania, należy dodać się, że te przekierowania URI w następującej kolejności:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Ponieważ są one poddomen pierwszy przekierowania URI, można dodać tych dwóch contoso.com. To ograniczenie zostanie usunięta w przyszłych wersji.

Należy również zauważyć, może mieć tylko 20 adresy URL odpowiedzi dla określonej aplikacji.

Aby dowiedzieć się, jak zarejestrować aplikację w portalu rejestracji aplikacji, zobacz [jak zarejestrować aplikację z punktem końcowym v2.0](active-directory-v2-app-registration.md).

## <a name="restrictions-on-libraries-and-sdks"></a>Ograniczenia dotyczące biblioteki i zestawy SDK
Obecnie Obsługa bibliotek dla punktu końcowego v2.0 jest ograniczona. Jeśli chcesz używać punktu końcowego v2.0 w aplikacji produkcyjnej, masz następujące opcje:

* Jeśli tworzysz aplikację sieci web bezpiecznie służy oprogramowanie pośredniczące Microsoft ogólnie dostępna po stronie serwera do sprawdzania poprawności logowania i tokenu. Obejmują one oprogramowanie pośredniczące OWIN Open ID Connect ASP.NET i Node.js usługi Passport wtyczki. Aby uzyskać przykłady kodu, korzystających z oprogramowania pośredniczącego firmy Microsoft, zobacz nasze [wprowadzenie](active-directory-appmodel-v2-overview.md#getting-started) sekcji.
* Jeśli tworzysz aplikację desktop lub mobile można użyć jednej z naszym podglądzie biblioteki uwierzytelniania firmy Microsoft (MSAL).  Te biblioteki są w podglądzie obsługiwane w środowisku produkcyjnym, więc bezpiecznie z nich korzystać w aplikacjach produkcyjnych. Możesz przeczytać dodatkowe informacje z wersji zapoznawczej i dostępnych bibliotek w naszym [dokumentacja bibliotek uwierzytelniania](active-directory-v2-libraries.md).
* W przypadku platform nie pasuje do żadnego biblioteki Microsoft można zintegrować z punktem końcowym v2.0 przez bezpośrednie wysyłanie i odbieranie wiadomości protokołu w kodzie aplikacji. Protokoły OpenID Connect i OAuth 2.0 [są udokumentowane](active-directory-v2-protocols.md) ułatwiające wykonanie takiej integracji.
* Ponadto można użyć biblioteki Otwórz ID Connect i OAuth open source do integracji z punktem końcowym v2.0. Protokół v2.0 powinna być zgodna z wielu bibliotek protokołu open source bez istotne zmiany. Dostępność tego rodzaju biblioteki jest zależna od języka i platformy. [Open ID Connect](http://openid.net/connect/) i [OAuth 2.0](http://oauth.net/2/) listę popularnych implementacje Obsługa witryn sieci Web. Aby uzyskać więcej informacji, zobacz [biblioteki Azure Active Directory w wersji 2.0 i uwierzytelniania](active-directory-v2-libraries.md)oraz listę bibliotek klienckich open source i przykłady, które zostały przetestowane z punktem końcowym v2.0.

## <a name="restrictions-on-protocols"></a>Ograniczenia protokołów
Punktu końcowego v2.0 nie obsługuje SAML lub WS-Federation; obsługuje tylko Open ID Connect i OAuth 2.0.  Nie wszystkie funkcje i możliwości protokołów OAuth zostały włączone do punktu końcowego v2.0. Te funkcje protokołu i możliwości są obecnie *nie jest dostępny* w punkcie końcowym v2.0:

* Nie zawierają tokeny Identyfikatora, które są wydawane przez punktu końcowego v2.0 `email` oświadczenia dla użytkownika, nawet jeśli w przypadku uzyskania zgody użytkownika, aby wyświetlić swój adres e-mail.
* Informacje o użytkowniku OpenID Connect punkt końcowy nie został zaimplementowany dla punktu końcowego v2.0. Jednak wszystkie dane profilu użytkownika, które potencjalnie będą pojawi się w tym punkcie końcowym jest dostępna z programu Microsoft Graph `/me` punktu końcowego.
* Punktu końcowego v2.0 nie obsługuje wystawiającego oświadczeń roli lub grupy w tokenach identyfikator.
* [2.0 właściciela zasobów hasło poświadczeń OAuth](https://tools.ietf.org/html/rfc6749#section-4.3) nie jest obsługiwany przez punktu końcowego v2.0.

Ponadto punktu końcowego v2.0 nie obsługuje żadnych formularza protokołów języka SAML lub WS-Federation.

Aby lepiej zrozumieć zakres protokołu funkcje obsługiwane w punkcie końcowym v2.0, zapoznaj się z artykułem naszych [referencyjne protokołu OpenID Connect i OAuth 2.0](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-and-school-accounts"></a>Ograniczenia dotyczące kont służbowych
Jeśli w aplikacjach systemu Windows była używana Active Directory Authentication Library (ADAL), może miały zaletą zintegrowanego uwierzytelniania systemu Windows, które używa grant potwierdzenia Security (Assertion Markup Language SAML). Z tym grant użytkowników federacyjnych usługi Azure AD dzierżaw dyskretnie mogą uwierzytelniać się w ich lokalnym wystąpieniem usługi Active Directory bez konieczności wprowadzania poświadczeń. Obecnie grant potwierdzenia SAML nie jest obsługiwana w punkcie końcowym v2.0.
