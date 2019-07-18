---
title: Tworzenie internetowego interfejsu API platformy .NET, który integruje się z usługą Azure AD w celu uwierzytelniania i autoryzacji | Microsoft Docs
description: Dowiedz się, jak utworzyć internetowy interfejs API MVC platformy .NET, który integruje się z usługą Azure AD w celu uwierzytelniania i autoryzacji.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5375d47c1b012a1c808a1115b7c902d99b05bf9d
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304720"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Szybki start: Tworzenie internetowego interfejsu API platformy .NET, który integruje się z usługą Azure AD w celu uwierzytelniania i autoryzacji

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Jeśli tworzysz aplikację, która zapewnia dostęp do chronionych zasobów, musisz wiedzieć, jak zapobiegać nieuprawnionemu dostępowi do tych zasobów. Usługa Azure Active Directory (Azure AD) ułatwia i upraszcza ochronę internetowego interfejsu API za pomocą tokenów dostępu elementu nośnego protokołu OAuth 2.0 przy użyciu zaledwie kilku linijek kodu.

W aplikacjach internetowych platformy ASP.NET możesz osiągnąć taki poziom ochrony, korzystając z wykonanej przez firmy Microsoft implementacji społecznościowego oprogramowania pośredniczącego OWIN dołączonej do programu .NET Framework 4.5. W tym module użyjemy interfejsu OWIN do utworzenia internetowego interfejsu API „Lista zadań do wykonania”, który:

* Określa, które interfejsy API są chronione.
* Weryfikuje, czy wywołania internetowego interfejsu API zawierają ważny token dostępu.

W tym przewodniku Szybki start utworzysz interfejsu API Lista zadań do wykonania i dowiesz się, jak wykonać następujące czynności:

1. Zarejestrować aplikację w usłudze Azure AD.
2. Skonfigurować aplikację pod kątem korzystania z potoku uwierzytelniania OWIN.
3. Skonfigurować aplikację kliencką pod kątem wywoływania internetowego interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, należy spełnić poniższe następujące wstępne:

* [Pobranie szkieletu aplikacji](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) lub [pobranie pełnego przykładu](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Każdy z tych elementów jest rozwiązaniem programu Visual Studio 2013.
* Uzyskanie dzierżawy usługi Azure AD, w którym zostanie zarejestrowana aplikacja. Jeśli nie masz jeszcze takiej dzierżawy, [dowiedz się, jak ją uzyskać](quickstart-create-new-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>Krok 1: Rejestrowanie aplikacji w usłudze Azure AD

Aby ułatwić zabezpieczanie aplikacji, należy najpierw utworzyć aplikację w ramach dzierżawy i podać w usłudze Azure AD kilka kluczowych informacji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz dzierżawę usługi Azure AD, wybierając swoje konto w prawym górnym rogu strony, wybierz opcję nawigacji **Przełącz katalog**, a następnie wybierz odpowiednią dzierżawę.
    * Pomiń ten krok, jeśli masz tylko jedną dzierżawę usługi Azure AD w ramach konta usługi lub jeśli wybrano już odpowiednią dzierżawę usługi Azure AD.

3. W okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.
4. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja**.
5. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji. Na przykład "Usługa listy usługi do wykonania".
W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
6. Wybierz platformę **sieci Web** w sekcji **Identyfikator URI przekierowania** i ustaw wartość `https://localhost:44321/` na (lokalizacja, w której usługa Azure AD będzie zwracać tokeny).
7. Po zakończeniu wybierz pozycję **Rejestruj**. Na stronie **Przegląd** aplikacji zanotuj wartość **Identyfikator aplikacji (klienta)** .
8. Wybierz opcję **Uwidocznij interfejs API** , a następnie kliknij pozycję **Dodaj zakres**.
9. Zaakceptuj proponowany identyfikator URI aplikacji (API://{clientId}), wybierając pozycję **Zapisz i Kontynuuj**.
10. Wprowadź następujące parametry:
    1. W obszarze **Nazwa zakresu**wprowadź wartość "access_as_user".
    1. Upewnij się, że wybrano opcję **Administratorzy i użytkownicy** , dla **kogo można wyrazić zgodę?** .
    1. W polu **Nazwa wyświetlana zgody administratora**wpisz "Access TodoListService jako użytkownik".
    1. W **opisie zgody administratora** wpisz "uzyskuje dostęp do internetowego interfejsu API TodoListService jako użytkownik".
    1. W polu **Nazwa wyświetlana zgody użytkownika** wpisz "Access TodoListService jako użytkownik".
    1. W **opisie zgody użytkownika** typ "uzyskuje dostęp do internetowego interfejsu API TodoListService jako użytkownik".
    1. W obszarze **stan**wybierz pozycję **włączone**.
11. Wybierz pozycję **Dodaj zakres** , aby zapisać konfigurację. Pozostaw portal otwarty, ponieważ wkrótce konieczne będzie również zarejestrowanie aplikacji klienckiej.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Krok 2: Konfigurowanie aplikacji pod kątem korzystania z potoku uwierzytelniania OWIN

Aby zweryfikować przychodzące żądania i tokeny, należy skonfigurować aplikację pod kątem komunikacji z usługą Azure AD.

1. Aby rozpocząć, otwórz rozwiązanie, a następnie dodaj pakiety NuGet oprogramowania pośredniczącego OWIN do projektu TodoListService przy użyciu konsoli menedżera pakietów.

    ```
    Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Dodaj klasę początkową OWIN do projektu TodoListService o nazwie `Startup.cs`.  Kliknij projekt prawym przyciskiem myszy, wybierz pozycję **Dodaj > Nowy element**, a następnie wyszukaj interfejs **OWIN**. Oprogramowanie pośredniczące OWIN wywoła metodę `Configuration(…)` podczas uruchamiania aplikacji.

3. Zmień deklarację klasy na wartość `public partial class Startup`. Część tej klasy została już zaimplementowana dla Ciebie w innym pliku. W metodzie `Configuration(…)` utwórz wywołanie do metody `ConfigureAuth(…)`, aby skonfigurować uwierzytelnianie dla aplikacji internetowej.

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Otwórz plik `App_Start\Startup.Auth.cs` i zaimplementuj metodę `ConfigureAuth(…)`. Parametry podane w obiekcie `WindowsAzureActiveDirectoryBearerAuthenticationOptions` będą służyć jako współrzędne dla aplikacji na potrzeby komunikacji z usługą Azure AD. Aby korzystać z nich, należy użyć klas w `System.IdentityModel.Tokens` przestrzeni nazw.

    ```csharp
    using Microsoft.IdentityModel.Tokens;
    ```

    ```csharp
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                 Tenant = ConfigurationManager.AppSettings["ida:Tenant"],
                 TokenValidationParameters = new TokenValidationParameters
                 {
                    ValidAudience = ConfigurationManager.AppSettings["ida:Audience"]
                 }
            });
    }
    ```

5. Użyj atrybutów `[Authorize]`, aby chronić kontrolery i akcje przy użyciu uwierzytelniania elementu nośnego tokenu JSON Web Token (JWT). Oznacz klasę `Controllers\TodoListController.cs` przy użyciu tagu autoryzacji, który wymusza zalogowanie się użytkownika przed uzyskaniem dostępu do tej strony.

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    Gdy autoryzowany element wywołujący pomyślnie wywoła jednej z interfejsów API `TodoListController`, akcja może wymagać dostępu do informacji o elemencie wywołującym. Interfejs OWIN zapewnia dostęp do oświadczeń w ramach tokenu elementu nośnego za pośrednictwem obiektu `ClaimsPrincpal`.  

6. Typowym wymogiem dla internetowych interfejsów API jest weryfikacja „zakresów” istniejących w tokenie w celu zapewnienia, że użytkownik wyraził zgodę na uprawnienia wymagane do uzyskania dostępu do usługi Lista zadań do wykonania.

    ```csharp
    public IEnumerable<TodoItem> Get()
    {
        // user_impersonation is the default permission exposed by applications in Azure AD
        if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
        {
            throw new HttpResponseException(new HttpResponseMessage {
              StatusCode = HttpStatusCode.Unauthorized,
              ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
            });
        }
        ...
    }
    ```

7. Otwórz plik `web.config` w katalogu głównym projektu TodoListService i wprowadź wartości konfiguracyjne w sekcji `<appSettings>`.
    * `ida:Tenant` to nazwa dzierżawy usługi Azure AD — na przykład contoso.onmicrosoft.com.
    * `ida:Audience` to identyfikator URI aplikacji, który wprowadzono w witrynie Azure Portal.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>Krok 3: Konfigurowanie aplikacji klienckiej i uruchamianie usługi

Zanim usługa Lista zadań do wykonania zacznie działać, należy skonfigurować klienta Lista zadań do wykonania, aby mógł on pobierać tokeny z usługi Azure AD i wykonywać wywołania usługi.

1. Wróć do witryny [Azure Portal](https://portal.azure.com).
1. Utwórz nową rejestrację aplikacji w dzierżawie usługi Azure AD.  Wprowadź **nazwę** opisującą aplikację dla użytkowników, wprowadź `https://TodoListClient/` wartość **identyfikatora URI przekierowania** i wybierz pozycję **Klient publiczny (Mobile and Desktop)** na liście rozwijanej.
1. Po zakończeniu rejestracji usługa Azure AD przypisuje aplikacji unikatowy identyfikator. Ta wartość będzie potrzebna w kolejnych krokach, więc należy skopiować ją ze strony aplikacji.
1. Wybierz pozycję **uprawnienia interfejsu API**, a następnie **Dodaj uprawnienie**.  Znajdź i wybierz **usługę Lista czynności do wykonania**, Dodaj uprawnienie **user_impersonation dostęp TodoListService** w obszarze **delegowane uprawnienia**, a następnie wybierz pozycję **Dodaj uprawnienia**.
1. W programie Visual Studio otwórz plik `App.config` w projekcie TodoListClient, a następnie wprowadź wartości konfiguracyjne w sekcji `<appSettings>`.

    * `ida:Tenant` to nazwa dzierżawy usługi Azure AD, na przykład contoso.onmicrosoft.com.
    * `ida:ClientId` to identyfikator aplikacji skopiowany z witryny Azure Portal.
    * `todo:TodoListResourceId` to identyfikator URI aplikacji Usługa Lista zadań do wykonania, który wprowadzono w witrynie Azure Portal.

1. Wyczyść, skompiluj i uruchom każdy projekt.
1. Jeśli jeszcze tego nie zrobiono, utwórz nowego użytkownika w ramach swojej dzierżawy z domeną *.onmicrosoft.com.
1. Zaloguj się do klienta Lista zadań do wykonania za pomocą tego użytkownika, a następnie dodaj kilka zadań do listy zadań do wykonania użytkownika.

## <a name="next-steps"></a>Kolejne kroki

* Na potrzeby referencyjne gotowy przykład (bez wartości konfiguracyjnych) jest dostępny w repozytorium [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Możesz teraz przejść do innych scenariuszy dotyczących tożsamości.
