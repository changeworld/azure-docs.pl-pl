---
title: Usługa Azure AD systemu Android wprowadzenie | Dokumentacja firmy Microsoft
description: Jak utworzyć aplikację dla systemu Android, która integruje się z usługą Azure AD, logowania i wywołania usługi Azure AD chronione interfejsów API za pomocą OAuth 2.0.
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
ms.openlocfilehash: cdd0d9ccff608d5882480d1394e2188579cefe75
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576804"
---
# <a name="azure-ad-android-getting-started"></a>Usługa Azure AD systemu Android wprowadzenie
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Jeżeli projektujesz aplikację dla systemu Android firmy Microsoft ułatwia prostym do logowania użytkowników usługi Azure Active Directory (Azure AD). Usługa Azure AD umożliwia aplikacji dostęp do danych użytkownika za pomocą programu Microsoft Graph lub własnych chroniony internetowy interfejs API. 

Biblioteki systemu Android w usłudze Azure AD Authentication Library (ADAL) umożliwia aplikacji mają być używane [chmury platformy Microsoft Azure](https://cloud.microsoft.com) & [interfejsu API Microsoft Graph](https://graph.microsoft.io) dzięki obsłudze [ Konta usługi Microsoft Azure Active Directory](https://azure.microsoft.com/services/active-directory/) przy użyciu standardowego protokołu OAuth2 i OpenID Connect. Niniejszy przykład pokazuje wszystkie cyklów normalne, których aplikacji powinni obawiać się o, w tym:

* Uzyskaj token programu Microsoft Graph
* Token odświeżania
* Wywołanie programu Microsoft Graph
* Wylogowania użytkownika

Aby rozpocząć pracę, należy dzierżawę usługi Azure AD, której tworzenie użytkowników i zarejestrowanie aplikacji. Jeśli nie masz jeszcze dzierżawy, [Dowiedz się, jak je](quickstart-create-new-tenant.md).

## <a name="scenario-sign-in-users-and-call-the-microsoft-graph"></a>Scenariusz: Logowania użytkowników i wywoływania usługi Microsoft Graph

![Topologia](./media/quickstart-v1-android/active-directory-android-topology.png)

Dla wszystkich kont usługi Azure AD, można użyć tej aplikacji. Obsługuje ona scenariusze zarówno pojedynczych i wielu organizacji (co opisano w krokach). Pokazuje, jak Deweloper tworzyć aplikacje do nawiązywania połączeń użytkowników w przedsiębiorstwach i dostęp do ich platformy Azure + usługi O365 danych za pomocą programu Microsoft Graph. Podczas przepływu autoryzacji użytkownicy końcowi będą musieli zarejestrować i zgodę na uprawnienia aplikacji, a w niektórych przypadkach mogą wymagać zgody aplikacji administratora. Większość logiki, w tym przykładzie pokazano, jak na marki i uwierzytelniania użytkownika końcowego podstawowego wywołań w programie Microsoft Graph.

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

## <a name="steps-to-run"></a>Kroki, aby uruchomić

### <a name="register-and-configure-your-app"></a>Rejestrowanie i konfigurowanie aplikacji 
Musisz mieć natywnej aplikacji klienckiej zarejestrowane w usłudze Microsoft za pomocą [witryny Azure portal](https://portal.azure.com). 

1. Wprowadzenie do Rejestracja aplikacji
    - Przejdź do witryny [Azure Portal](https://aad.portal.azure.com). 
    - Wybierz ***usługi Azure Active Directory*** > ***rejestracje aplikacji***. 

2. Tworzymy aplikację.
    - Wybierz pozycję **Rejestrowanie nowej aplikacji**. 
    - Wprowadź nazwę aplikacji w **nazwa** pola. 
    - W **typ aplikacji** wybierz **natywnych**. 
    - W **identyfikator URI przekierowania**, wprowadź `http://localhost`. 

3. Konfigurowanie programu Microsoft Graph
    - Wybierz **Ustawienia > uprawnienia wymagane**.
    - Wybierz **Dodaj**w programie **wybierz interfejs API** wybierz ***programu Microsoft Graph***. 
    - Wybierz uprawnienie **Zaloguj się i odczytuj profil użytkownika**, kliknij przycisk **wybierz** do zapisania. 
        - To uprawnienie jest mapowany na `User.Read` zakresu. 
    - Opcjonalnie: Wewnątrz **wymagane uprawnienia > Windows Azure Active Directory**, Usuń wybrane uprawnienie **Zaloguj się i odczytuj profil użytkownika**. Pozwoli to uniknąć strona zgody użytkownika, listę uprawnień dwa razy. 

4. Gratulacje! Pomyślnie skonfigurowano aplikację. W następnej sekcji będą potrzebne:
    - `Application ID`
    - `Redirect URI`

### <a name="get-the-sample-code"></a>Pobieranie przykładowego kodu

1. Klonowanie kodu.
    ```
    git clone https://github.com/Azure-Samples/active-directory-android
    ```
2. Otwórz plik w programie Android Studio.
    - Wybierz **Otwórz istniejący projekt Android Studio**.

### <a name="configure-your-code"></a>Skonfiguruj swój kod

Można znaleźć całą konfigurację dla tego przykładu kodu w ***src/main/java/com/azuresamples/azuresampleapp/MainActivity.java*** pliku. 

1. Zastąp stała `CLIENT_ID` z `ApplicationID`.
2. Zastąp stała `REDIRECT URI` z `Redirect URI` skonfigurowane wcześniej (`http://localhost`). 

### <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

1. Wybierz **kompilacji > Czyszczenie projektu**. 
2. Wybierz **Uruchom > Uruchom aplikację**. 
3. Aplikacja powinna kompilacji i pokazują niektóre podstawowe najlepsze środowisko użytkownika. Po kliknięciu `Call Graph API` przycisku, wówczas monit o podanie logowania i dyskretnie wywołania interfejsu API programu Microsoft Graph przy użyciu nowego tokenu. 

## <a name="important-info"></a>Ważne informacje

1. Wyewidencjonuj [biblioteki ADAL dla systemu Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki) uzyskać więcej informacji dotyczących mechanics biblioteki oraz sposób konfigurowania nowych scenariuszy i możliwości. 
2. W scenariuszach natywnych aplikacja będzie używać osadzonych Webview i nie spowoduje, że aplikacja. `Redirect URI` Może być dowolną. 
3. Znajdź wszelkie problemy lub masz żądania? Możesz utworzyć problem lub opublikuj na stronie Stackoverflow z tagu `azure-active-directory`. 

### <a name="cross-app-sso"></a>Logowanie Jednokrotne dla wielu aplikacji
Dowiedz się, [sposób włączenia logowania jednokrotnego dla wielu aplikacji w systemie Android przy użyciu biblioteki ADAL](howto-v1-enable-sso-on-android.md). 

### <a name="auth-telemetry"></a>Dane telemetryczne uwierzytelniania
Biblioteka ADAL przedstawia dane telemetryczne uwierzytelniania, aby ułatwić deweloperom aplikacji zrozumieć, jak zachowują się swoje aplikacje i opracować lepsze rozwiązania. Teraz można przechwytywać logowania Powodzenie, aktywnych użytkowników i kilka innych interesujących danych szczegółowych. Przy użyciu telemetrii uwierzytelniania wymagają deweloperów aplikacji, można ustanowić usługi telemetrii do zagregowania i przechowywania zdarzeń.

Aby dowiedzieć się więcej o telemetrii uwierzytelniania wyewidencjonowania [biblioteki ADAL dla systemu Android telemetrii uwierzytelniania](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Telemetry). 

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
