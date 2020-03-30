---
title: Nowe środowisko rejestracji aplikacji w witrynie Azure Portal
titleSuffix: Microsoft identity platform
description: Wprowadzenie do nowego środowiska rejestracji aplikacji w witrynie Azure portal
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/8/2019
ms.author: marsma
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 50c88dd1785bd9177219054fed3800ca725a5274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154597"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>Nowe środowisko rejestracji aplikacji portalu Azure

Istnieje wiele ulepszeń w nowym środowiska [rejestracji aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) w witrynie Azure portal. Jeśli jesteś bardziej zaznajomiony z portalem rejestracji aplikacji (apps.dev.microsoft.com) do rejestrowania lub zarządzania aplikacjami konwergentnymi, określanym jako stare środowisko, ten przewodnik szkoleniowy rozpocznie cię przy użyciu nowego środowiska.

## <a name="whats-not-changing"></a>Co się nie zmienia?

- Aplikacje i powiązane konfiguracje można znaleźć w stanie — w nowym doświadczeniu. Nie trzeba ponownie rejestrować aplikacji, a użytkownicy aplikacji nie będą musieli ponownie się logować.

    > [!NOTE]
    > Musisz zalogować się przy użyciu konta używanego do rejestrowania aplikacji, aby znaleźć je w witrynie Azure portal. Zalecamy sprawdzenie zalogowanego użytkownika w witrynie Azure portal pasuje do użytkownika, który został zalogowany do portalu rejestracji aplikacji, porównując adres e-mail z profilu.
    >
    > W niektórych przypadkach, zwłaszcza gdy logujesz się przy użyciu osobistych kont Microsoft (np. wiadomości e-mail w dzierżawie usługi Azure AD. Jeśli nadal uważasz, że brakuje aplikacji, wyloguj się i zaloguj się przy odpowiednim koncie.

- Aplikacje SDK na żywo utworzone przy użyciu osobistych kont Microsoft nie są jeszcze obsługiwane w witrynie Azure portal i pozostaną w starej pracy w najbliższej przyszłości.

## <a name="key-changes"></a>Kluczowe zmiany

-   W starym doświadczeniu aplikacje były domyślnie rejestrowane jako aplikacje konwergentne obsługujące wszystkie konta organizacyjne (wielodostępne) oraz osobiste konta Microsoft. Nie można tego zmodyfikować za pomocą starego środowiska, co utrudnia tworzenie aplikacji, które obsługiwały tylko konta organizacyjne (wielodostępne lub pojedyncza dzierżawa).
    Nowe środowisko umożliwia rejestrowanie aplikacji obsługujących wszystkie te opcje. [Dowiedz się więcej o typach aplikacji](active-directory-v2-registration-portal.md).

-   W nowym środowiskach, jeśli osobiste konto Microsoft jest również w dzierżawie usługi Azure AD, zobaczysz trzy karty — wszystkie aplikacje w dzierżawie, posiadanych aplikacji w dzierżawie, a także aplikacje z konta osobistego. Jeśli więc uważasz, że brakuje aplikacji zarejestrowanych na Twoim osobistym koncie Microsoft, sprawdź kartę **Aplikacje na koncie osobistym.**

-   W nowym środowiskach można łatwo przełączać się między dzierżawcami, przechodząc do profilu i wybierając katalog przełącznika.

## <a name="list-of-applications"></a>Lista wniosków

-   Nowa lista aplikacji zawiera aplikacje, które zostały zarejestrowane za pośrednictwem środowiska rejestracji starszych aplikacji w witrynie Azure portal (aplikacje, które logują się tylko na kontach usługi Azure AD), a także aplikacje zarejestrowane przez [portal rejestracji aplikacji](https://apps.dev.microsoft.com/) (aplikacje, które logują się zarówno na usługi Azure AD, jak i osobiste konta Microsoft).

-   Nowa lista aplikacji zawiera dwie dodatkowe kolumny: **utworzone w kolumnie** i **kolumnie & wpisów tajnych,** która pokazuje stan (bieżący, wygasający wkrótce lub wygasł) poświadczeń, które zostały zarejestrowane w aplikacji.

## <a name="new-app-registration"></a>Rejestrowanie nowej aplikacji

W starym doświadczeniu, aby zarejestrować zbieżną aplikację, musisz tylko podać nazwę. Utworzone aplikacje zostały zarejestrowane jako aplikacje konwergentne obsługujące wszystkie katalogi organizacyjne (wielodostępne) oraz osobiste konta Microsoft.  Nie można tego zmodyfikować za pomocą starego środowiska, co utrudnia tworzenie aplikacji, które obsługiwały tylko konta organizacyjne (wielodostępne lub pojedyncza dzierżawa). [Dowiedz się więcej o obsługiwanych typach kont](v2-supported-account-types.md)

W nowym środowiskau należy podać nazwę aplikacji i wybrać typy obsługiwanych kont. Opcjonalnie można podać identyfikator URI przekierowania.
Jeśli podasz identyfikator URI przekierowania, musisz określić, czy jest on a także w internecie/w internecie(natywnym/mobilnym i stacjonarnym). Aby uzyskać więcej informacji na temat rejestrowania aplikacji przy użyciu nowego środowiska rejestracji aplikacji, zobacz [ten szybki start](quickstart-register-app.md).

## <a name="app-management-page"></a>Strona zarządzania aplikacjami

Stare środowisko miało jedną stronę zarządzania aplikacjami dla aplikacji konwergentnych z następującymi sekcjami: Właściwości, Wpisy tajne aplikacji, Platformy, Właściciele, Uprawnienia programu Microsoft Graph, Profil i Opcje zaawansowane.

Nowe środowisko w witrynie Azure portal reprezentuje te funkcje na oddzielnych stronach. Oto, gdzie można znaleźć równoważne funkcje:

-   Właściwości — nazwa i identyfikator aplikacji znajduje się na stronie Przegląd.

-   Wpisy tajne aplikacji znajduje się na stronie Certyfikaty & wpisy tajne

-   Konfiguracja platform znajduje się na stronie Uwierzytelnianie

-   Uprawnienia programu Microsoft Graph są dostępne na stronie uprawnień interfejsu API wraz z innymi uprawnieniami

-   Profil znajduje się na stronie Znakowanie

-   Opcja zaawansowana — obsługa live SDK znajduje się na stronie Uwierzytelnianie.

## <a name="application-secretscertificates--secrets"></a>Wpisy tajne/certyfikaty aplikacji & wpisy tajne

W nowym środowiskau **wpisy tajne aplikacji** zostały zmienione na **Certyfikaty & wpisy tajne**. Ponadto **klucze publiczne** są określane jako **certyfikaty,** a hasła są określane jako **wpisy** **tajne klienta.** Zdecydowaliśmy się nie wprowadzać tej funkcji w nowe środowisko ze względów bezpieczeństwa, dlatego nie można już wygenerować nowej pary kluczy.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Platformy/uwierzytelnianie: adresy URL odpowiedzi/przekierowanie identyfikatorów URI
W starym środowisku aplikacja miała sekcję Platformy dla sieci Web, natywnego i interfejsu API sieci Web, aby skonfigurować adresy URL przekierowania, adres URL wylogowania i przepływ niejawny.

W nowym środowiskau adresy URL odpowiedzi\'można znaleźć w sekcji Uwierzytelnianie s aplikacji. Ponadto są one określane jako przekierowanie identyfikatorów URI i format przekierowania identyfikatorów URI uległ zmianie. Muszą być skojarzone z typem aplikacji (klient internetowy lub publiczny — mobilny i stacjonarny). [Dowiedz się więcej](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

Interfejsy API sieci Web są konfigurowane w uwidaczniać stronę interfejsu API.

> [!NOTE]
> Wypróbuj nowe środowisko ustawień uwierzytelniania, w którym możesz skonfigurować ustawienia aplikacji na podstawie platformy lub urządzenia, na które chcesz kierować reklamy. [Dowiedz się więcej](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Uprawnienia programu Microsoft Graph/uprawnienia interfejsu API

-   Wybierając interfejs API w starym środowiska, można wybrać tylko interfejsy API programu Microsoft Graph. W nowym środowiskach można wybierać spośród wielu interfejsów API firmy Microsoft, w tym programu Microsoft Graph, interfejsów API z organizacji i interfejsów API, co jest prezentowane na trzech kartach: Interfejsy API firmy Microsoft, interfejsy API używane przez moją organizację lub interfejsy API. Pasek wyszukiwania w interfejsach API mojej organizacji używa tabulatorów przeszukuje jednostki usługi w dzierżawie.

    > [!NOTE]
    > Ta karta nie zostanie wyświetlona, jeśli aplikacja nie jest skojarzona z dzierżawą. Aby uzyskać więcej informacji na temat żądania uprawnień przy użyciu nowego środowiska, zobacz [ten przewodnik Szybki start](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md).

-   Stare środowisko nie ma przycisku **Uprawnienia udzielić.** W nowym środowiskach istnieje sekcja Zgoda grantu z przyciskiem **Zgody administratora grantu** w sekcji uprawnień interfejsu API aplikacji. Tylko administrator może udzielić zgody, a ten przycisk jest włączony tylko dla administratorów. Gdy administrator wybierze przycisk **Zgoda administratora,** zgoda administratora jest udzielana wszystkim żądanym uprawnieniom.

## <a name="profile"></a>Profil
W starym doświadczeniu profil miał logo, adres URL strony głównej, adres URL warunków korzystania z usługi i konfigurację adresu URL zasad zachowania poufności informacji. W nowym doświadczeniu można je znaleźć na stronie Znakowanie.

## <a name="application-manifest"></a>Manifest aplikacji
W nowym środowiskau Manifest strona umożliwia edytowanie i aktualizowanie atrybutów aplikacji. Aby uzyskać więcej informacji, zobacz [Manifest aplikacji](reference-app-manifest.md).

## <a name="new-ui"></a>Nowy interfejs użytkownika
Istnieje nowy interfejs użytkownika dla właściwości, które wcześniej można było ustawić tylko przy użyciu edytora manifestów lub interfejsu API lub nie istniały.

-   Niejawny przepływ dotacji (oauth2AllowImplicitFlow) można znaleźć na stronie Uwierzytelnianie. W przeciwieństwie do starego środowiska, można włączyć tokeny dostępu lub tokeny identyfikatora lub obu.

-   Zakresy zdefiniowane przez ten interfejs API (oauth2Permissions) i autoryzowane aplikacje klienckie (preAuthorizedApplications) można skonfigurować za pośrednictwem strony Uwidacznianie interfejsu API. Aby uzyskać więcej informacji na temat konfigurowania aplikacji jako internetowego interfejsu API i ujawniania uprawnień/zakresów, zobacz [ten szybki start](quickstart-configure-app-expose-web-apis.md).

-   Domena programu Publisher (która jest wyświetlana użytkownikom w [wierszu zgody\'aplikacji)](application-consent-experience.md)można znaleźć na stronie bloku znakowanie. Aby uzyskać więcej informacji na temat konfigurowania domeny wydawcy, zobacz [ten sposób .](howto-configure-publisher-domain.md)

## <a name="limitations"></a>Ograniczenia

Nowe środowisko ma następujące ograniczenia:

-   Nowe środowisko nie obsługuje jeszcze rejestracji aplikacji dla dzierżaw usługi Azure AD B2C.

-   Nowe środowisko nie obsługuje jeszcze aplikacji Live SDK utworzonych za pomocą osobistych kont Microsoft.

-   Zmiana wartości obsługiwanych kont nie jest obsługiwana w interfejsie użytkownika. Należy użyć manifestu aplikacji,\'chyba że ponownie przełączania między usługi Azure AD jednej dzierżawy i wielu dzierżawców.

   > [!NOTE]
   > Jeśli jesteś osobistym użytkownikiem konta Microsoft w dzierżawie usługi Azure AD, a administrator dzierżawy ma ograniczony dostęp do witryny Azure portal, możesz uzyskać odmowę dostępu. Jeśli jednak przejdziesz przez skrót, wpisując rejestracje aplikacji na pasku wyszukiwania lub przypinając go, będziesz mieć dostęp do nowego środowiska.
