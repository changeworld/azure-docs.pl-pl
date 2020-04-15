---
title: Logowanie użytkowników w aplikacjach jednostronicowych angular — Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak aplikacja Angular może wywołać interfejs API, który wymaga tokenów dostępu przy użyciu platformy tożsamości firmy Microsoft.
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: 4b6a2481c18314a44470a020033ffdc4ba1d7259
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380029"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Szybki start: logowanie użytkowników i uzyskiwanie tokenu dostępu w aplikacji jednostronicowej angular

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre aspekty tej funkcji mogą ulec zmianie przed ogólną dostępnością (GA).

W tym przewodniku Szybki start można użyć przykładu kodu, aby dowiedzieć się, jak aplikacja jednostronicowa angular (SPA) może logować się do użytkowników, którzy mają osobiste konta Microsoft, konta służbowe lub konta szkolne. Angular SPA można również uzyskać token dostępu do wywołania interfejsu API programu Microsoft Graph lub dowolnego interfejsu API sieci web.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.js](https://nodejs.org/en/download/).
* [Visual Studio Code](https://code.visualstudio.com/download) do edycji plików projektu lub [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) do uruchomienia projektu.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>Zarejestruj się i pobierz aplikację Szybki start
> Aby uruchomić aplikację Szybki start, użyj jednej z następujących opcji.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Opcja 1 (ekspresowa): Zarejestruj się i automatycznie skonfiguruj aplikację, a następnie pobierz przykładowy kod
>
> 1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
> 1. Jeśli twoje konto ma dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na dzierżawę usługi Azure Active Directory (Azure AD), której chcesz użyć.
> 1. Otwórz nowe okienko [rejestracji aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) w witrynie Azure portal.
> 1. Wprowadź nazwę aplikacji, a następnie wybierz pozycję **Zarejestruj**.
> 1. Przejdź do okienka Szybki start i wyświetl szybki start kątowy. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Opcja 2 (ręczna): Zarejestruj się i ręcznie skonfiguruj przykład aplikacji i kodu
>
> #### <a name="step-1-register-the-application"></a>Krok 1: Zarejestruj aplikację
>
> 1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
> 1. Jeśli Twoje konto ma dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu i ustaw sesję portalu na dzierżawę usługi Azure AD, której chcesz użyć.
> 1. Postępuj zgodnie z instrukcjami, aby [zarejestrować aplikację jednostronicową](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) w witrynie Azure portal.
> 1. Dodaj nową platformę w okienku **uwierzytelniania** rejestracji aplikacji i `http://localhost:4200/`zarejestruj identyfikator URI przekierowania: .
> 1. Ten szybki start używa [niejawnego przepływu dotacji](v2-oauth2-implicit-grant-flow.md). Wybierz ustawienia **niejawnej dotacji** dla **tokenów identyfikatorów** i **tokenów dostępu**. Tokeny identyfikatorów i tokeny dostępu są wymagane, ponieważ ta aplikacja loguje się do użytkowników i wywołuje interfejs API.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Krok 1: Konfigurowanie aplikacji w witrynie Azure portal
> Aby przykładowy kod dla tego szybkiego startu działał, należy **http://localhost:4200/** dodać identyfikator URI przekierowania jako i włączyć **niejawne przyznanie**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-javascript/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-code-sample"></a>Krok 2: Pobierz przykładowy kod
>[!div renderon="docs"]
>Aby uruchomić projekt z serwerem sieci web przy użyciu pliku Node.js, [sklonuj przykładowe repozytorium](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) lub [pobierz podstawowe pliki projektu](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Otwórz pliki przy użyciu edytora, takiego jak Visual Studio Code.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Pobierz przykładowy kod](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>Krok 3: Konfigurowanie aplikacji JavaScript
>
> W folderze *src/app* edytuj *plik app.module.ts* i ustaw wartości `clientId` w `authority` obszarze `MsalModule.forRoot`.
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here


> [!div renderon="docs"]
>
> Zastąp następujące wartości:
>
>|Nazwa wartości|Opis|
>|---------|---------|
>|Enter_the_Application_Id_Here|Na **stronie Przegląd** rejestracji aplikacji jest to wartość **identyfikatora aplikacji(klienta).** |
>|Enter_the_Cloud_Instance_Id_Here|Jest to wystąpienie chmury platformy Azure. W przypadku głównej lub globalnej chmury platformy Azure wprowadź plik **https://login.microsoftonline.com**. Zobacz też: Chmury narodowe [.](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)|
>|Enter_the_Tenant_Info_Here| Ustaw jedną z następujących opcji: Jeśli aplikacja obsługuje *konta w tym katalogu organizacyjnym,* zastąp tę wartość identyfikatorem katalogu (dzierżawcy) lub nazwą dzierżawy (na przykład **contoso.microsoft.com**). Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym,* zastąp tę wartość **organizacjami**. Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft,* zastąp tę wartość **wspólną**. Aby ograniczyć obsługę tylko do *osobistych kont Microsoft,* zastąp tę wartość **konsumentami.** |
>|Enter_the_Redirect_Uri_Here|Zamień na **http://localhost:4200**.|
>|Alokacja pamięci podręcznej  | (Opcjonalnie) Ustaw magazyn przeglądarki dla stanu uwierzytelniania. Wartość domyślna to **sessionStorage**.   |
>|storeAuthStateInCookie  | (Opcjonalnie) Zidentyfikuj bibliotekę, w których jest przechowywane stan żądania uwierzytelnienia. Ten stan jest wymagany do sprawdzania poprawności przepływów uwierzytelniania w plikach cookie przeglądarki. Ten plik cookie jest ustawiony dla programów Internet Explorer i Edge, aby pomieścić te dwie przeglądarki. Aby uzyskać więcej informacji, zobacz [znane problemy](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
> > [!TIP]
> > Aby znaleźć wartości **Identyfikator aplikacji (klienta)**, **Identyfikator katalogu (dzierżawy)** i **Obsługiwane typy kont**, przejdź do strony **Przegląd** w witrynie Azure Portal.

Aby uzyskać więcej informacji na temat dostępnych opcji konfigurowalnych, zobacz [Inicjowanie aplikacji klienckich](msal-js-initializing-client-applications.md). 

Kod źródłowy biblioteki MSAL.js można znaleźć w repozytorium [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) w usłudze GitHub.

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>Krok 3: Uruchom projekt

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Krok 4: Uruchom projekt

Jeśli używasz środowiska Node.js:

1. Uruchom serwer, uruchamiając następujące polecenia z katalogu projektu:

   ```console
   npm install
   npm start
   ```

1. Przejdź do **http://localhost:4200/** pliku .
1. Wybierz **pozycję Zaloguj**się .
1. Wybierz **pozycję Profil,** aby wywołać program Microsoft Graph.

Po załadowaniu aplikacji przez przeglądarkę wybierz pozycję **Zaloguj**. Przy pierwszym logowaniu zostanie wyświetlony monit o wyrażenie zgody na umożliwienie aplikacji dostępu do twojego profilu i zalogowania się. Po pomyślnym zalogowaniu wybierz **pozycję Profil**, a informacje o profilu użytkownika zostaną wyświetlone na stronie.

## <a name="how-the-sample-works"></a>Jak działa próbka

![Diagram przedstawiający, jak działa przykładowa aplikacja w tym przewodniku Szybki start](media/quickstart-v2-javascript/javascriptspa-intro.svg)


## <a name="next-steps"></a>Następne kroki

Następnie dowiedz się, jak zalogować się do użytkownika i uzyskać tokeny w samouczku Angular:

> [!div class="nextstepaction"]
> [Samouczek kątowy](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular)

