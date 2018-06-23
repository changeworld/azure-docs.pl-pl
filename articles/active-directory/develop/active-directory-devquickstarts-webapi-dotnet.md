---
title: Azure AD .NET interfejsu API sieci Web wprowadzenie | Dokumentacja firmy Microsoft
description: Jak utworzyć .NET MVC składnika web API, która integruje się z usługą Azure AD do uwierzytelniania i autoryzacji.
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
ms.openlocfilehash: e2537ab677527555fab9801b3dc5143f749a8b57
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318762"
---
# <a name="azure-ad-net-web-api-getting-started"></a>Azure AD .NET interfejsu API sieci Web wprowadzenie
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Jeśli tworzysz aplikację, która zapewnia dostęp do chronionych zasobów, musisz wiedzieć, jak uniemożliwić nieuzasadnione dostęp do tych zasobów.
Azure Active Directory (Azure AD) powoduje, że jej proste i bezpośrednie zabezpieczyć interfejs API sieci web przy użyciu dostępu elementu nośnego OAuth 2.0 tokeny przy tylko kilku wierszy kodu.

W aplikacji sieci web ASP.NET można wykonywać tę ochronę za pomocą przez firmę Microsoft implementacją społeczność oprogramowania pośredniczącego OWIN uwzględnione w programie .NET Framework 4.5. W tym miejscu użyjemy OWIN do kompilacji "Lista zadań do wykonania" składnika web API, które:

* Określa, które interfejsy API są chronione.
* Sprawdza, czy wywołania interfejsu API sieci web zawiera tokenu dostępu prawidłowe.

Aby utworzyć do czy listy interfejsu API, należy najpierw:

1. Zarejestrować aplikację w usłudze Azure AD.
2. Skonfigurować aplikację do używania uwierzytelniania potoku OWIN.
3. Skonfigurować aplikację klienta do wywołania interfejsu API sieci web.

Aby rozpocząć, [pobrać szkielet aplikacji](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) lub [Pobieranie ukończone próbki](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Każdy jest rozwiązanie Visual Studio 2013. Należy również dzierżawa usługi Azure AD, w którym można zarejestrować aplikacji. Jeśli nie masz już, [Dowiedz się, jak kupić](active-directory-howto-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>Krok 1: Rejestrowanie aplikacji w usłudze Azure AD
Aby ułatwić zabezpieczanie aplikacji, należy najpierw utworzyć aplikację w dzierżawie i udostępnia usługi Azure AD z kilka kluczowych informacji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Na górnym pasku kliknij konto. W **katalogu** wybierz dzierżawy usługi Azure AD, które chcesz zarejestrować aplikację.

3. Kliknij przycisk **więcej usług** w okienku po lewej stronie, a następnie wybierz **usługi Azure Active Directory**.

4. Kliknij przycisk **rejestracji aplikacji**, a następnie wybierz **Dodaj**.

5. Postępuj zgodnie z monitami i utworzyć nową **aplikacji sieci Web i/lub interfejs API sieci Web**.
  * **Nazwa** opisuje aplikacji dla użytkowników. Wprowadź **Lista zadań do wykonania usługi**.
  * **Identyfikator Uri przekierowania** jest kombinacją schemat i ciąg, korzystającą z usługi Azure AD w celu zwracać wszystkie tokeny, których aplikacja zażądała. Wprowadź `https://localhost:44321/` dla tej wartości.

6. Z **ustawienia** -> **właściwości** strony dla aplikacji, zaktualizuj identyfikator URI aplikacji. Wprowadź identyfikator specyficznego dla dzierżawy. Na przykład wprowadź wartość `https://contoso.onmicrosoft.com/TodoListService`.

7. Zapisz konfigurację. Pozostaw portalu otwarty, ponieważ należy również zarejestrować aplikację klienta wkrótce.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Krok 2: Konfigurowanie aplikacji do używania uwierzytelniania potoku OWIN
Można sprawdzić poprawności przychodzących żądań i tokenów, należy skonfigurować aplikację do komunikacji z usługą Azure AD.

1. Aby rozpocząć, otwórz rozwiązanie i dodawanie pakietów NuGet oprogramowanie pośredniczące OWIN do projektu TodoListService przy użyciu konsoli Menedżera pakietów.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Dodawanie klasy początkowej OWIN do projektu TodoListService o nazwie `Startup.cs`.  Kliknij prawym przyciskiem myszy projekt, wybierz **Dodaj** > **nowy element**, a następnie wyszukaj **OWIN**. Oprogramowanie pośredniczące OWIN wywoła metodę `Configuration(…)` podczas uruchamiania aplikacji.

3. Deklaracja klasy, aby zmienić `public partial class Startup`. Już zaimplementowano część tej klasy dla Ciebie w innym pliku. W `Configuration(…)` utworzyć wywołanie metody `ConfgureAuth(…)` Aby skonfigurować uwierzytelnianie dla aplikacji sieci web.

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Otwórz plik `App_Start\Startup.Auth.cs` i wdrożenie `ConfigureAuth(…)` metody. Parametry, które należy podać w `WindowsAzureActiveDirectoryBearerAuthenticationOptions` będzie służyć jako współrzędnych dla aplikacji w celu komunikowania się z usługą Azure AD.

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

5. Teraz można używać `[Authorize]` atrybutów, aby lepiej chronić Twoje kontrolerów i akcji przy użyciu uwierzytelniania elementu nośnego tokenu Web JSON (JWT). Dekoracji `Controllers\TodoListController.cs` klasy z tagiem autoryzacji. Spowoduje to wymuszenie użytkownika do logowania przed uzyskaniem dostępu do tej strony.

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    Gdy Autoryzowany rozmówca pomyślnie wywołuje jeden z `TodoListController` interfejsów API, działania mogą wymagać dostępu do informacji o elemencie wywołującym. OWIN zapewnia dostęp do oświadczeń wewnątrz tokenu elementu nośnego za pośrednictwem `ClaimsPrincpal` obiektu.  

6. Typowym wymogiem dla interfejsów API sieci Web jest weryfikacja „zakresów” występujących w tokenie. Dzięki temu, że użytkownik zgodził się na uprawnienia wymagane do uzyskania dostępu do czy listy usługi.

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

7. Otwórz `web.config` plików w folderze głównym projektu TodoListService, a następnie wprowadź wartości konfiguracji w `<appSettings>` sekcji.
  * `ida:Tenant` to nazwa dzierżawy usługi Azure AD — na przykład contoso.onmicrosoft.com.
  * `ida:Audience` to identyfikator URI aplikacji w aplikacji, którą wprowadzono w portalu Azure.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>Krok 3: Konfigurowanie aplikacji klienta i uruchamiania usługi
Zanim zobaczysz do czy listy działanie usługi, należy skonfigurować klienta listy zadań do wykonania, może uzyskać tokenów z usługi Azure AD i wykonywania wywołań do usługi.

1. Wróć do [portalu Azure](https://portal.azure.com).

2. Utwórz nową aplikację w dzierżawie usługi Azure AD, a następnie wybierz **natywną aplikację kliencką** wynikowego wiersza.
  * **Nazwa** opisuje aplikacji dla użytkowników.
  * Wprowadź `http://TodoListClient/` dla **identyfikator Uri przekierowania** wartość.

3. Po zakończeniu rejestracji usługi Azure AD przypisuje unikatowy identyfikator aplikacji do aplikacji. Ta wartość jest potrzebny w następnych krokach, dlatego skopiuj go ze strony aplikacji.

4. Z **ustawienia** wybierz pozycję **wymagane uprawnienia**, a następnie wybierz **Dodaj**. Zlokalizuj i wybierz do czy listy usługi, dodać **TodoListService dostępu** uprawnienie w obszarze **delegowane uprawnienia**, a następnie kliknij przycisk **gotowe**.

5. W programie Visual Studio Otwórz `App.config` w TodoListClient projektu, a następnie wprowadź wartości konfiguracji w `<appSettings>` sekcji.

  * `ida:Tenant` to nazwa dzierżawy usługi Azure AD — na przykład contoso.onmicrosoft.com.
  * `ida:ClientId` jest to identyfikator aplikacji skopiowany z portalu Azure.
  * `todo:TodoListResourceId` to identyfikator URI aplikacji w aplikacji do usługi listy czy wprowadzony w portalu Azure.

## <a name="next-steps"></a>Kolejne kroki
Ponadto wyczyścić, skompilować i uruchomić każdy projekt. Jeśli nie jest jeszcze nadszedł czas, aby utworzyć nowy użytkownik w dzierżawie z *. onmicrosoft.com domeny. Zaloguj się do klienta listy zadań do wykonania z użytkownikiem, a następnie dodać niektóre zadania do listy zadań do wykonania przez użytkownika.

Odwołanie, ukończonych próbka (bez wartości konfiguracji) są dostępne w [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Możesz teraz przejść do scenariuszy tożsamości.

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
