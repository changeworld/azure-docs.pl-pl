---
title: Azure AD systemu Android wprowadzenie | Dokumentacja firmy Microsoft
description: Sposób tworzenia aplikacji systemu Android, która integruje się z usługą Azure AD, logowania i wywołania usługi Azure AD chronione przy użyciu OAuth2.0 interfejsów API.
services: active-directory
documentationcenter: android
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 20618fff8d253bfab195ce2847a8848a28960ae4
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="azure-ad-android-getting-started"></a>Azure AD systemu Android wprowadzenie
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Jeśli projektujesz aplikacji systemu Android firmy Microsoft ułatwia proste i bezpośrednie do logowania użytkowników usługi Azure Active Directory (Azure AD). Usługi Azure AD umożliwia aplikacji dostęp do danych użytkownika za pomocą programu Microsoft Graph lub własne chronionego interfejsu API w sieci web. 

Biblioteka systemu Android Azure AD Authentication Library (ADAL) daje aplikacji możliwość rozpocząć korzystanie z [firmy Microsoft w chmurze Azure](https://cloud.microsoft.com) & [interfejsu API programu Microsoft Graph](https://graph.microsoft.io) dzięki obsłudze [ Konta Microsoft Azure Active Directory](https://azure.microsoft.com/services/active-directory/) przy użyciu branżowy standard OAuth2 i OpenID Connect. W tym przykładzie przedstawiono wszystkie cykle normalne, które aplikacja powinna występować w tym:

* Uzyskaj token programu Microsoft Graph
* Token odświeżania
* Wywołania programu Microsoft Graph
* Wyloguj użytkownika

Aby rozpocząć pracę, musisz dzierżawa usługi Azure AD, gdy tworzenie użytkowników i zarejestrowanie aplikacji. Jeśli nie masz już dzierżawę, [Dowiedz się, jak kupić](active-directory-howto-tenant.md).

## <a name="scenario-sign-in-users-and-call-the-microsoft-graph"></a>Scenariusz: Zaloguj się użytkowników i Wywołaj Microsoft Graph

![Topologia](./media/active-directory-android-topology.png)

Można użyć tej aplikacji, dla wszystkich kont usługi Azure AD. Obsługuje scenariusze pojedyncze i wielu organizacji (omówione w krokach). Pokazuje, jak deweloper może tworzyć aplikacje do łączenia z użytkownikami w przedsiębiorstwie i dostęp do ich Azure i usługi Office 365 danych za pomocą programu Microsoft Graph. Podczas przepływu uwierzytelniania użytkownicy końcowi będą musieli zarejestrować i zgody na uprawnienia aplikacji, a w niektórych przypadkach mogą wymagać administratora, aby wyrazić zgodę na aplikację. Większość logiki w tym przykładzie pokazano, jak do uwierzytelniania użytkownika końcowego i udostępnić podstawowego wywołania do programu Microsoft Graph.

## <a name="example-code"></a>Przykładowy kod

Kod pełny przykład można znaleźć [w serwisie Github](https://github.com/Azure-Samples/active-directory-android). 

```Java
// Initialize your app with MSAL
AuthenticationContext mAuthContext = new AuthenticationContext(
        MainActivity.this, 
        AUTHORITY, 
        false);


// Perform authentication requests
mAuthContext.acquireToken(
    getActivity(), 
    RESOURCE_ID, 
    CLIENT_ID, 
    REDIRECT_URI,  
    PromptBehavior.Auto, 
    getAuthInteractiveCallback());

// ...

// Get tokens to call APIs like the Microsoft Graph
mAuthResult.getAccessToken()
```

## <a name="steps-to-run"></a>Czynności, aby uruchomić

### <a name="register-and-configure-your-app"></a>Zarejestruj i konfigurowanie aplikacji 
Musisz mieć aplikację native client, w zarejestrowany z firmą Microsoft przy użyciu [portalu Azure](https://portal.azure.com). 

1. Wprowadzenie do rejestracji aplikacji
    - Przejdź do witryny [Azure Portal](https://aad.portal.azure.com). 
    - Wybierz ***usługi Azure Active Directory*** > ***rejestracji aplikacji***. 

2. Tworzymy aplikację.
    - Wybierz pozycję **Rejestrowanie nowej aplikacji**. 
    - Wprowadź nazwę aplikacji w **nazwa** pola. 
    - W **typu aplikacji** wybierz **natywnego**. 
    - W **identyfikator URI przekierowania**, wprowadź `http://localhost`. 

3. Konfigurowanie programu Microsoft Graph
    - Wybierz **Ustawienia > wymagane uprawnienia**.
    - Wybierz **Dodaj**w **wybierz interfejs API** wybierz ***Microsoft Graph***. 
    - Wybierz uprawnienie **Zaloguj się i odczytuj profil użytkownika**, następnie kliknij przycisk **wybierz** do zapisania. 
        - To uprawnienie jest mapowany na `User.Read` zakresu. 
    - Opcjonalnie: Wewnątrz **wymagane uprawnienia > Windows Azure Active Directory**, Usuń wybrane uprawnienie **Zaloguj się i odczytuj profil użytkownika**. Pozwoli to uniknąć wyświetlania dwukrotnie uprawnienie strony zgody użytkownika. 

4. Gratulacje! Pomyślnie skonfigurowano aplikację. W następnej sekcji potrzebne są:
    - `Application ID`
    - `Redirect URI`

### <a name="get-the-sample-code"></a>Pobieranie przykładowego kodu

1. Klonowanie kodu.
    ```
    git clone https://github.com/Azure-Samples/active-directory-android
    ```
2. Otwórz próbki w programie Android Studio.
    - Wybierz **otworzyć istniejący projekt Android Studio**.

### <a name="configure-your-code"></a>Skonfiguruj swój kod

Możesz znaleźć wszystkie konfiguracje dla tego przykładu kodu w ***src/main/java/com/azuresamples/azuresampleapp/MainActivity.java*** pliku. 

1. Zastąp stała `CLIENT_ID` z `ApplicationID`.
2. Zastąp stała `REDIRECT URI` z `Redirect URI` skonfigurowane wcześniej (`http://localhost`). 

### <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

1. Wybierz **kompilacji > Czyszczenie projektu**. 
2. Wybierz **Uruchom > wykonywania aplikacji**. 
3. Aplikację należy skompilować i Pokaż niektóre podstawowe UX. Po kliknięciu `Call Graph API` przycisku, zostanie monit o podanie logowania i dyskretnej wywołania interfejsu API programu Microsoft Graph z nowy token. 

## <a name="important-info"></a>Ważne informacje

1. Wyewidencjonowanie [ADAL dla systemu Android typu Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki) uzyskać więcej informacji dotyczących sposobu biblioteki oraz sposób konfigurowania nowych scenariuszy i możliwości. 
2. W scenariuszach macierzystego aplikacja będzie używać osadzonych widoku sieci Web i nie pozostawi aplikacji. `Redirect URI` Mogą być dowolnego. 
3. Znajdź wszystkie problemy lub żądania? Możesz utworzyć problemu lub blogu w witrynie Stackoverflow znacznikiem `azure-active-directory`. 

### <a name="cross-app-sso"></a>Usługa rejestracji Jednokrotnej w wielu aplikacji
Dowiedz się [jak włączyć logowanie Jednokrotne wielu aplikacji w systemie Android przy użyciu biblioteki ADAL](active-directory-sso-android.md). 

### <a name="auth-telemetry"></a>Dane telemetryczne uwierzytelniania
Biblioteka ADAL przedstawia uwierzytelniania telemetrię, aby pomóc deweloperom aplikacji zrozumienie zachowania swoje aplikacje i lepszą obsługę kompilacji. Teraz można przechwytywać logowania sukces, aktywnych użytkowników i kilka innych interesujące szczegółowych informacji. Przy użyciu uwierzytelniania telemetrii wymagać od deweloperów aplikacji do ustanawiania telemetrii usługi do agregacji i przechowywania zdarzeń.

Aby dowiedzieć się więcej o telemetrii uwierzytelniania wyewidencjonowania [telemetrii uwierzytelnianie ADAL dla systemu Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Telemetry). 

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
