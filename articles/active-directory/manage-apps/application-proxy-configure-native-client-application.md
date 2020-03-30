---
title: Publikowanie natywnych aplikacji klienckich — Azure AD | Dokumenty firmy Microsoft
description: Obejmuje sposób włączania natywnych aplikacji klienckich do komunikowania się z usługą Azure AD Application Proxy Connector w celu zapewnienia bezpiecznego dostępu zdalnego do aplikacji lokalnych.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d2a2bb9dd543da7455a276075a829ef06032edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159288"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Jak włączyć natywne aplikacje klienckie do interakcji z aplikacjami proxy

Za pomocą serwera proxy aplikacji usługi Azure Active Directory (Azure AD) można publikować aplikacje sieci web, ale może również służyć do publikowania natywnych aplikacji klienckich skonfigurowanych za pomocą biblioteki uwierzytelniania usługi Azure AD Library (ADAL). Natywne aplikacje klienckie różnią się od aplikacji sieci Web, ponieważ są zainstalowane na urządzeniu, podczas gdy aplikacje sieci web są dostępne za pośrednictwem przeglądarki.

Aby obsługiwać natywne aplikacje klienckie, serwer proxy aplikacji akceptuje tokeny wystawione przez usługę Azure AD, które są wysyłane w nagłówku. Usługa serwera proxy aplikacji wykonuje uwierzytelnianie dla użytkowników. To rozwiązanie nie używa tokenów aplikacji do uwierzytelniania.

![Relacja między użytkownikami końcowymi, usługą Azure AD i opublikowanymi aplikacjami](./media/application-proxy-configure-native-client-application/richclientflow.png)

Aby opublikować aplikacje natywne, należy użyć biblioteki uwierzytelniania usługi Azure AD, która zajmuje się uwierzytelnianiem i obsługuje wiele środowisk klienckich. Serwer proxy aplikacji pasuje do [scenariusza interfejsu API aplikacji natywnej do sieci Web](../azuread-dev/native-app.md).

W tym artykule otrzymasz od czterech kroków, aby opublikować aplikację macierzystą za pomocą serwera proxy aplikacji i biblioteki uwierzytelniania usługi Azure AD.

## <a name="step-1-publish-your-proxy-application"></a>Krok 1: Publikowanie aplikacji proxy

Opublikuj aplikację serwera proxy tak, jak każdą inną aplikację i przypisz użytkowników do dostępu do aplikacji. Aby uzyskać więcej informacji, zobacz [Publikowanie aplikacji za pomocą serwera proxy aplikacji](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Krok 2: Zarejestruj swoją aplikację natywną

Teraz musisz zarejestrować aplikację w usłudze Azure AD w następujący sposób:

1. Zaloguj się do [portalu usługi Azure Active Directory](https://aad.portal.azure.com/). Pojawi się **pulpit nawigacyjny** **centrum administracyjnego usługi Azure Active Directory.**
1. Na pasku bocznym wybierz pozycję **Azure Active Directory**. Zostanie wyświetlona strona przeglądu **usługi Azure Active Directory.**
1. Na pasku bocznym przeglądu usługi Azure AD wybierz pozycję **Rejestracje aplikacji**. Zostanie wyświetlona lista wszystkich rejestracji aplikacji.
1. Wybierz **pozycję Nowa rejestracja**. Zostanie wyświetlona strona **Zarejestruj aplikację.**

   ![Tworzenie nowej rejestracji aplikacji w witrynie Azure portal](./media/application-proxy-configure-native-client-application/create.png)

1. W nagłówku **Nazwa** określ nazwę wyświetlaną aplikacji skierowaną do użytkownika.
1. W nagłówku **Obsługiwane typy kont** wybierz poziom dostępu, korzystając z następujących wskazówek:

   - Aby kierować reklamy tylko na konta wewnętrzne organizacji, wybierz opcję **Konta tylko w tym katalogu organizacyjnym**.
   - Aby kierować reklamy tylko do klientów biznesowych lub edukacyjnych, wybierz pozycję **Konta w dowolnym katalogu organizacyjnym**.
   - Aby kierować reklamy na najszerszy zestaw tożsamości firmy Microsoft, wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft**.

1. W nagłówku **Przekierowanie identyfikatora URI** wybierz pozycję **Klient publiczny (mobilny & pulpit),** a następnie wpisz identyfikator URI przekierowania dla aplikacji.
1. Wybierz i przeczytaj **zasady platformy Microsoft**, a następnie wybierz pozycję **Zarejestruj**. Zostanie utworzona i wyświetlona strona przeglądu dla nowej rejestracji aplikacji.

Aby uzyskać bardziej szczegółowe informacje dotyczące tworzenia nowej rejestracji aplikacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](../develop/quickstart-register-app.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Krok 3: Udziel dostępu do aplikacji proxy

Po zarejestrowaniu aplikacji macierzystej można nadać jej dostęp do innych aplikacji w katalogu, w tym przypadku w celu uzyskania dostępu do aplikacji proxy. Aby włączyć nanatywną aplikację, która ma być narażona na aplikację proxy:

1. Na pasku bocznym nowej strony rejestracji aplikacji wybierz pozycję **Uprawnienia interfejsu API**. Zostanie wyświetlona strona **uprawnień interfejsu API** dla nowej rejestracji aplikacji.
1. Wybierz **pozycję Dodaj uprawnienie**. Zostanie wyświetlona strona **Żądania uprawnień interfejsu API.**
1. W obszarze **Wybierz interfejs API** wybierz pozycję **Interfejsy API**używane przez moją organizację . Pojawi się lista zawierająca aplikacje w katalogu, które udostępniają interfejsy API.
1. Wpisz pole wyszukiwania lub przewiń, aby znaleźć aplikację serwera proxy opublikowaną w [kroku 1: Opublikuj aplikację serwera proxy,](#step-1-publish-your-proxy-application)a następnie wybierz aplikację serwera proxy.
1. W **nagłówku Jakiego typu uprawnień wymaga aplikacja?** Jeśli aplikacja natywna musi uzyskać dostęp do interfejsu API aplikacji proxy jako zalogowany użytkownik, wybierz pozycję **Uprawnienia delegowane**.
1. W nagłówku **Wybierz uprawnienia** wybierz żądane uprawnienie i wybierz pozycję **Dodaj uprawnienia**. Strona **uprawnień interfejsu API** dla aplikacji macierzystej pokazuje teraz dodaną aplikację serwera proxy i interfejs API uprawnień.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Krok 4: Edytowanie biblioteki uwierzytelniania usługi Active Directory

Edytuj kod aplikacji natywnej w kontekście uwierzytelniania biblioteki uwierzytelniania usługi Active Directory (ADAL), aby uwzględnić następujący tekst:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

Wymagane informacje w przykładowym kodzie można znaleźć w portalu usługi Azure AD w następujący sposób:

| Wymagane informacje | Jak go znaleźć w portalu usługi Azure AD |
| --- | --- |
| \<identyfikator dzierżawy> | **Azure Active Directory** > **Identyfikator katalogu** właściwości usługi Azure Active**Directory** >  |
| \<Zewnętrzny adres URL aplikacji proxy> | **Aplikacje korporacyjne** > *serwera proxy* > serwera**proxy** > **External Url** aplikacji proxy |
| \<Identyfikator aplikacji aplikacji natywnej> | **Aplikacje** > dla przedsiębiorstw identyfikator aplikacji*natywnej* > **Properties** > **Application ID** właściwości |
| \<Przekieruj identyfikator URI aplikacji macierzystej> | **Rejestracje** > aplikacji usługi Azure Active Directory*natywnej* > **App registrations** > aplikacji**Przekierowanie identyfikatorów URI** |
| \<adres URL interfejsu API aplikacji proxy> | **Rejestracja** > aplikacji usługi Azure Active Directory**uprawnienia** > interfejsu**API / NAZWA UPRAWNIEŃ** interfejsu**API** > *aplikacji* >  |

Po edycji usługi ADAL z tymi parametrami użytkownicy mogą uwierzytelniać się w natywnych aplikacjach klienckich, nawet jeśli są poza siecią firmową.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat przepływu aplikacji natywnych, zobacz [Aplikacje natywne w usłudze Azure Active Directory](../azuread-dev/native-app.md).

Dowiedz się więcej o [konfigurowaniu logowania jednokrotnego w aplikacjach usługi Azure Active Directory](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
