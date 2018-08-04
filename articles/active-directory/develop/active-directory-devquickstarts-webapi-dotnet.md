---
title: Usługa Azure AD w sieci Web interfejsu API platformy .NET wprowadzenie | Dokumentacja firmy Microsoft
description: Jak utworzyć .NET MVC internetowego interfejsu API, która integruje się z usługą Azure AD do uwierzytelniania i autoryzacji.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: ca506d821fe3534468c0d370dd51464e5df90f79
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504665"
---
# <a name="azure-ad-net-web-api-getting-started"></a>Usługa Azure AD w sieci Web interfejsu API platformy .NET wprowadzenie
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Jeśli tworzysz aplikację, która zapewnia dostęp do chronionych zasobów, musisz wiedzieć, jak zapobiegać nieuzasadnione dostęp do tych zasobów.
Usługa Azure Active Directory (Azure AD) sprawia, że jej prostym lepiej chronić interfejs API sieci web przy użyciu dostępu elementu nośnego OAuth 2.0 tokeny przy użyciu zaledwie kilku linijek kodu.

W aplikacji sieci web platformy ASP.NET można wykonać tej ochrony za pomocą implementacja firmy Microsoft społeczność oprogramowania pośredniczącego OWIN zawarte w .NET Framework 4.5. W tym miejscu użyjemy OWIN do tworzenia internetowego interfejsu API "Lista zadań do wykonania", który:

* Określa, które interfejsy API są chronione.
* Weryfikuje, że wywołania interfejsu API sieci web zawiera token dostępu nie jest ważna.

Aby skompilować do czy listy interfejsu API, najpierw musisz:

1. Zarejestrować aplikację w usłudze Azure AD.
2. Konfigurowanie aplikacji na korzystanie z potoku uwierzytelniania OWIN.
3. Skonfigurować aplikację klienta do wywołania interfejsu API sieci web.

Aby rozpocząć pracę, [pobrać szkielet aplikacji](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) lub [Pobierz przykładowe ukończone](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Każdy jest rozwiązaniem programu Visual Studio 2013. Należy również dzierżawy usługi Azure AD w taki sposób, w której chcesz zarejestrować aplikację. Jeśli nie masz jeszcze takiego, [Dowiedz się, jak je](quickstart-create-new-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>Krok 1: Rejestrowanie aplikacji w usłudze Azure AD
Aby zabezpieczyć aplikację, należy najpierw utworzyć aplikację w dzierżawie i udostępnia usługi Azure AD z kilku kluczowych informacji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz dzierżawę usługi Azure AD, klikając swoje konto w prawym górnym rogu strony, a następnie klikając **Przełącz katalog** nawigacji, a następnie wybierz odpowiednią dzierżawy.
 * Pomiń ten krok, jeśli masz tylko jedną dzierżawą usługi Azure AD w ramach konta usługi lub został już wybrany odpowiedni dzierżawy usługi Azure AD.

3. W okienku nawigacji po lewej stronie kliknij pozycję **Azure Active Directory**.

4. Kliknij przycisk **rejestracje aplikacji**, a następnie wybierz pozycję **Dodaj**.

5. Postępuj zgodnie z monitami i Utwórz nowy **aplikacji sieci Web i/lub interfejs API sieci Web**.
  * **Nazwa** opisywać aplikację użytkownikom. Wprowadź **Lista zadań do wykonania usługi**.
  * **Identyfikator Uri przekierowania** jest połączeniem schemat i parametry, które używa usługi Azure AD, aby zwracać wszystkie tokeny zażądał aplikacji. Wprowadź `https://localhost:44321/` dla tej wartości.

6. Z **ustawienia** -> **właściwości** stronie aplikacji, zaktualizuj identyfikator URI Identyfikatora aplikacji. Wprowadź identyfikator specyficznym dla dzierżawy. Na przykład wprowadź wartość `https://contoso.onmicrosoft.com/TodoListService`.

7. Zapisz konfigurację. Zamykaj portalu, ponieważ będzie również trzeba zarejestrować aplikację kliencką wkrótce.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Krok 2: Konfigurowanie aplikacji na korzystanie z potoku uwierzytelniania OWIN
Aby sprawdzić poprawność przychodzącego żądania i tokenów, musisz skonfigurować aplikację do komunikacji z usługą Azure AD.

1. Aby rozpocząć, otwórz rozwiązanie, a następnie dodaj pakiety NuGet oprogramowanie pośredniczące OWIN do projektu TodoListService przy użyciu konsoli Menedżera pakietów.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Dodaj klasę początkową OWIN do projektu TodoListService o nazwie `Startup.cs`.  Kliknij prawym przyciskiem myszy projekt, wybierz **Dodaj** > **nowy element**, a następnie wyszukaj **OWIN**. Oprogramowanie pośredniczące OWIN wywoła metodę `Configuration(…)` podczas uruchamiania aplikacji.

3. Zmień deklarację klasy na `public partial class Startup`. Już zaimplementowano część tej klasy dla Ciebie w innym pliku. W `Configuration(…)` metody, Utwórz wywołanie `ConfgureAuth(…)` Aby skonfigurować uwierzytelnianie dla aplikacji sieci web.

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Otwórz plik `App_Start\Startup.Auth.cs` i zaimplementować `ConfigureAuth(…)` metody. Parametry, które podano w `WindowsAzureActiveDirectoryBearerAuthenticationOptions` będzie służyć jako współrzędnych dla aplikacji do komunikowania się z usługą Azure AD.

    ```csharp
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                Audience = ConfigurationManager.AppSettings["ida:Audience"],
                Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
            });
    }
    ```

5. Teraz możesz używać `[Authorize]` atrybutów, które mają pomóc w ochronie kontrolerów i akcji przy użyciu uwierzytelniania elementu nośnego tokenu sieci Web JSON (JWT). Dekoracji `Controllers\TodoListController.cs` klasy przy użyciu tagu autoryzacji. Spowoduje to wymuszenie użytkownikowi zalogowanie się przed uzyskaniem dostępu do tej strony.

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    Kiedy wywołujący autoryzowanych pomyślnie wywołuje jedną z `TodoListController` interfejsów API, działania mogą wymagać dostępu do informacji o obiekcie wywołującym. OWIN zapewnia dostęp do oświadczeń wewnątrz tokenu elementu nośnego za pośrednictwem `ClaimsPrincpal` obiektu.  

6. Typowym wymogiem dla interfejsów API sieci Web jest weryfikacja „zakresów” występujących w tokenie. Daje to gwarancję, że użytkownik wyraził zgodę na uprawnienia wymagane do uzyskania dostępu do czy listy usługi.

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

7. Otwórz `web.config` plików w katalogu głównym projektu TodoListService, a następnie wprowadź wartości konfiguracji w `<appSettings>` sekcji.
  * `ida:Tenant` jest nazwą dzierżawy usługi Azure AD — na przykład contoso.onmicrosoft.com.
  * `ida:Audience` to identyfikator URI aplikacji w aplikacji, które wprowadziłeś w witrynie Azure portal.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>Krok 3: Skonfigurować aplikację klienta i uruchom usługę
Zanim zobaczysz do czy listy działanie usługi, musisz skonfigurować klienta listy zadań do wykonania, dzięki czemu można uzyskać tokenów z usługi Azure AD i wykonywania wywołań do usługi.

1. Wróć do [witryny Azure portal](https://portal.azure.com).

2. Utwórz nową aplikację w dzierżawie usługi Azure AD, a następnie wybierz pozycję **natywną aplikację kliencką** wynikowego wiersza.
  * **Nazwa** opisywać aplikację użytkownikom.
  * Wprowadź `http://TodoListClient/` dla **identyfikator Uri przekierowania** wartość.

3. Po zakończeniu rejestracji usługi Azure AD przypisuje unikatowy identyfikator aplikacji. Ta wartość będzie potrzebna w następnych krokach, więc skopiuj ją ze strony aplikacji.

4. Z **ustawienia** wybierz opcję **wymagane uprawnienia**, a następnie wybierz pozycję **Dodaj**. Zlokalizuj i wybierz do czy listy usługi, dodać **TodoListService dostępu** uprawnień w ramach **delegowane uprawnienia**, a następnie kliknij przycisk **gotowe**.

5. W programie Visual Studio, otwórz `App.config` w TodoListClient projektu, a następnie wprowadź wartości konfiguracji w `<appSettings>` sekcji.

  * `ida:Tenant` jest nazwą dzierżawy usługi Azure AD — na przykład contoso.onmicrosoft.com.
  * `ida:ClientId` jest identyfikator aplikacji, który został skopiowany z witryny Azure portal.
  * `todo:TodoListResourceId` to identyfikator URI aplikacji w aplikacji do usługi listy czy wprowadzony w witrynie Azure portal.

## <a name="next-steps"></a>Kolejne kroki
Na koniec należy wyczyścić, skompiluj i uruchom każdego projektu. Jeśli jeszcze nie, nadszedł czas, który można utworzyć nowego użytkownika w dzierżawie za pomocą *. onmicrosoft.com domeny. Zaloguj się do klienta listy zadań do wykonania z tym użytkownikiem, a następnie dodać niektórych zadań do listy zadań do wykonania użytkownika.

Odwołanie, jest dostępna w ukończonej próbka (bez wartości konfiguracji) [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Możesz teraz przejść do dalszych scenariuszach tożsamości.

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
