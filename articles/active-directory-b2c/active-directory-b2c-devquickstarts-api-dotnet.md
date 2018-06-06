---
title: Secure web API platformy ASP.NET w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak utworzyć interfejs API sieci Web programu .NET przy użyciu usługi Azure Active Directory B2C zabezpieczonej przy użyciu tokenów dostępu protokołu OAuth 2.0 na potrzeby uwierzytelniania.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 01/14/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8eb4045338476254fa9687a367d45b3646aeb9c1
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712372"
---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C: tworzenie interfejsu API sieci Web platformy .NET

Usługa Azure Active Directory (Azure AD) B2C umożliwia zabezpieczanie interfejsu API sieci Web za pomocą tokenów dostępu protokołu OAuth 2.0. Tokeny te służą aplikacjom klienckim do uwierzytelniania w obrębie interfejsu API. W tym artykule pokazano, jak utworzyć interfejs API „lista zadań do wykonania” kontrolera MVC platformy .NET umożliwiający użytkownikom aplikacji klienckiej wykonywanie zadań CRUD. Interfejs API sieci Web jest zabezpieczony za pomocą usługi Azure AD B2C i zezwala na zarządzanie listami zadań do wykonania tylko uwierzytelnionym użytkownikom.

## <a name="create-an-azure-ad-b2c-directory"></a>Tworzenie katalogu usługi Azure AD B2C

Przed rozpoczęciem korzystania z usługi Azure AD B2C należy utworzyć katalog lub dzierżawę. Katalog jest kontenerem dla wszystkich użytkowników, aplikacji, grup i innych elementów. Jeśli jeszcze go nie masz, [utwórz katalog usługi B2C](active-directory-b2c-get-started.md), zanim przejdziesz dalej.

> [!NOTE]
> Aplikacja kliencka i interfejs API sieci Web muszą korzystać z tego samego katalogu usługi Azure AD B2C.
>

## <a name="create-a-web-api"></a>Tworzenie interfejsu API sieci Web

Następnie musisz utworzyć aplikację interfejsu API sieci Web w katalogu usługi B2C. Dzięki temu informacje wymagane do bezpiecznego komunikowania się z aplikacją będą przekazywane do usługi Azure AD. Aby utworzyć aplikację, postępuj zgodnie z [tymi instrukcjami](active-directory-b2c-app-registration.md). Należy pamiętać o wykonaniu następujących czynności:

* Uwzględnij **aplikację sieci Web** lub **interfejs API sieci Web** w aplikacji.
* Użyj **identyfikatora URI przekierowania** `https://localhost:44332/` dla aplikacji sieci Web. Jest to domyślna lokalizacja klienta aplikacji sieci Web dla tego przykładu kodu.
* Skopiuj **Identyfikator aplikacji** przypisany do aplikacji. Będzie on potrzebny później.
* Wprowadź identyfikator aplikacji do **identyfikatora URI aplikacji**. Skopiuj cały **identyfikator URI aplikacji**. Będzie on potrzebny później.
* Dodaj uprawnienia za pomocą menu **Opublikowane zakresy**.

## <a name="create-your-policies"></a>Tworzenie zasad

W usłudze Azure AD B2C każde działanie użytkownika jest definiowane przy użyciu [zasad](active-directory-b2c-reference-policies.md). Należy utworzyć zasady komunikacji z usługą Azure AD B2C. Zaleca się używanie połączonych zasad tworzenia/logowania, zgodnie z opisem w [artykule Informacje ogólne o zasadach](active-directory-b2c-reference-policies.md). Podczas tworzenia zasad należy pamiętać, aby:

* Wybrać wartość **Nazwa wyświetlana** i inne atrybuty tworzenia konta w zasadach.
* Wybrać oświadczenia **Nazwa wyświetlana** i **Identyfikator obiektu** jako oświadczenia aplikacji dla wszystkich zasad. Można również wybrać inne oświadczenia.
* Skopiować każdą utworzoną wartość **Nazwa** zasad. Nazwa zasad będzie potrzebna później.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Po pomyślnym utworzeniu zasad możesz rozpocząć tworzenie aplikacji.

## <a name="download-the-code"></a>Pobieranie kodu

Kod używany w tym samouczku [jest przechowywany w serwisie GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Przykład można sklonować, uruchamiając polecenie:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Po pobraniu przykładu kodu otwórz plik SLN programu Visual Studio, aby rozpocząć. Plik rozwiązania zawiera dwa projekty: `TaskWebApp` i `TaskService`. `TaskWebApp` to aplikacja sieci Web z architekturą MVC, z której korzysta użytkownik. `TaskService` to interfejs API zaplecza aplikacji, który przechowuje listy zadań do wykonania poszczególnych użytkowników. Ten artykuł zawiera jedynie omówienie aplikacji `TaskService`. Aby dowiedzieć się, jak utworzyć `TaskWebApp`, używając usługi Azure AD B2C, zobacz nasz [samouczek dotyczący aplikacji sieci Web platformy .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Aktualizowanie konfiguracji usługi Azure AD B2C

Nasz przykład został skonfigurowany do używania zasad i identyfikatora klienta dzierżawy pokazowej. Jeśli chcesz użyć własnej dzierżawy, wykonaj następujące czynności:

1. Otwórz plik `web.config` w projekcie `TaskService` i zastąp wartości
    * `ida:Tenant` nazwą dzierżawy
    * `ida:ClientId` identyfikatorem aplikacji interfejsu API sieci Web
    * `ida:SignUpSignInPolicyId` nazwą zasady tworzenia konta/logowania

2. Otwórz plik `web.config` w projekcie `TaskWebApp` i zastąp wartości
    * `ida:Tenant` nazwą dzierżawy
    * `ida:ClientId` identyfikatorem aplikacji sieci Web
    * `ida:ClientSecret` kluczem tajnym aplikacji sieci Web
    * `ida:SignUpSignInPolicyId` nazwą zasady tworzenia konta/logowania
    * `ida:EditProfilePolicyId` nazwą zasady edycji profilu
    * `ida:ResetPasswordPolicyId` nazwą zasady resetowania hasła
    * `api:ApiIdentifier` identyfikatorem URI aplikacji


## <a name="secure-the-api"></a>Zabezpieczanie interfejsu API

Jeśli klient wywołuje interfejs API, należy zabezpieczyć interfejs API (np. `TaskService`) za pomocą tokenów elementu nośnego OAuth 2.0. Dzięki temu każde żądanie dotyczące interfejsu API będzie poprawne, tylko jeśli zawiera token elementu nośnego. Interfejs API może akceptować i weryfikować tokeny elementu nośnego przy użyciu biblioteki interfejsu OWIN (Open Web Interface for .NET) firmy Microsoft.

### <a name="install-owin"></a>Instalowanie interfejsu OWIN

Rozpocznij od zainstalowania potoku uwierzytelniania OWIN OAuth przy użyciu konsoli Menedżera pakietów Visual Studio.

```Console
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

Spowoduje to zainstalowanie oprogramowania pośredniczącego OWIN, które będzie akceptować i sprawdzać poprawność tokenów elementu nośnego.

### <a name="add-an-owin-startup-class"></a>Dodawanie klasy początkowej OWIN

Dodaj klasę początkową OWIN do interfejsu API o nazwie `Startup.cs`.  Kliknij projekt prawym przyciskiem myszy, wybierz kolejno pozycje **Dodaj** i **Nowy element**, a następnie wyszukaj interfejs OWIN. Oprogramowanie pośredniczące OWIN wywoła metodę `Configuration(…)` podczas uruchamiania aplikacji.

W naszym przykładzie zmieniliśmy deklarację klasy na `public partial class Startup` i wdrożyliśmy drugą część klasy w `App_Start\Startup.Auth.cs`. Wewnątrz metody `Configuration` dodaliśmy wywołanie `ConfigureAuth`, które jest zdefiniowane w `Startup.Auth.cs`. Po modyfikacji `Startup.cs` wygląda podobnie do następujących:

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>Konfigurowanie uwierzytelniania OAuth 2.0

Otwórz plik `App_Start\Startup.Auth.cs` i zaimplementuj metodę `ConfigureAuth(...)`. Na przykład może ona wyglądać następująco:

```CSharp
// App_Start\Startup.Auth.cs

 public partial class Startup
    {
        // These values are pulled from web.config
        public static string AadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
        public static string Tenant = ConfigurationManager.AppSettings["ida:Tenant"];
        public static string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
        public static string SignUpSignInPolicy = ConfigurationManager.AppSettings["ida:SignUpSignInPolicyId"];
        public static string DefaultPolicy = SignUpSignInPolicy;

        /*
         * Configure the authorization OWIN middleware.
         */
        public void ConfigureAuth(IAppBuilder app)
        {
            TokenValidationParameters tvps = new TokenValidationParameters
            {
                // Accept only those tokens where the audience of the token is equal to the client ID of this app
                ValidAudience = ClientId,
                AuthenticationType = Startup.DefaultPolicy
            };

            app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
            {
                // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
                AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(AadInstance, Tenant, DefaultPolicy)))
            });
        }
    }
```

### <a name="secure-the-task-controller"></a>Zabezpieczanie kontrolera zadań

Po skonfigurowaniu aplikacji do używania uwierzytelniania OAuth 2.0 możesz zabezpieczyć interfejs API sieci Web, dodając tag `[Authorize]` do kontrolera zadań. Jest to kontroler, w obrębie którego odbywają się wszystkie operacje edytowania listy zadań do wykonania, dlatego należy zabezpieczyć cały kontroler na poziomie klasy. Można również dodać tag `[Authorize]` do poszczególnych czynności w celu uzyskania ściślejszej kontroli.

```CSharp
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Uzyskiwanie informacji o użytkowniku z tokenu

`TasksController` przechowuje zadania w bazie danych. Z każdym zadaniem jest skojarzony użytkownik będący jego „właścicielem”. Właściciel jest identyfikowany za pomocą **identyfikatora obiektu** użytkownika. (Dlatego trzeba było dodać identyfikator obiektu jako oświadczenie aplikacji we wszystkich zasadach).

```CSharp
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

### <a name="validate-the-permissions-in-the-token"></a>Weryfikowanie uprawnień w tokenie

Typowym wymogiem dla interfejsów API sieci Web jest weryfikacja „zakresów” występujących w tokenie. Daje to gwarancję, że użytkownik wyraził zgodę na uprawnienia wymagane do uzyskania dostępu do usługi listy zadań do wykonania.

```CSharp
public IEnumerable<Models.Task> Get()
{
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "read")
    {
        throw new HttpResponseException(new HttpResponseMessage {
            StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = "The Scope claim does not contain 'read' or scope claim not found"
        });
    }
    ...
}
```

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

Na koniec skompiluj i uruchom oba projekty: `TaskWebApp` i `TaskService`. Utwórz kilka zadań na liście użytkownika i zwróć uwagę na to, że zostają one zachowane w interfejsie API nawet po zatrzymaniu i ponownym uruchomieniu klienta.

## <a name="edit-your-policies"></a>Edytowanie zasad

Po zabezpieczeniu interfejsu API za pomocą usługi Azure AD B2C możesz eksperymentować z zasadą tworzenia konta/logowania i sprawdzać skutki (lub ich brak) powiązane z interfejsem API. Możesz manipulować oświadczeniami aplikacji w zasadach i zmieniać informacje o użytkowniku dostępne w interfejsie API sieci Web. Wszystkie dodane oświadczenia będą dostępne dla interfejsu API sieci Web w architekturze MVC programu .NET w obiekcie `ClaimsPrincipal`, zgodnie z opisem we wcześniejszej części tego artykułu.
