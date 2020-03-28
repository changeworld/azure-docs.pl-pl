---
title: 'Samouczek: Włączanie uwierzytelniania w aplikacji sieci Web'
titleSuffix: Azure AD B2C
description: Samouczek dotyczący sposobu użycia usługi Azure Active Directory B2C w celu określenia nazwy logowania użytkownika na potrzeby aplikacji internetowej platformy ASP.NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e4b56f18bf8a2ed1c22b00b8a57efdbf06eb7fa2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183330"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Samouczek: Włączanie uwierzytelniania w aplikacji sieci web przy użyciu usługi Azure Active Directory B2C

W tym samouczku pokazano, jak używać usługi Azure Active Directory B2C (Azure AD B2C) do logowania się i rejestrowania użytkowników w ASP.NET aplikacji sieci web. Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie się na kontach społecznościowych, kontach przedsiębiorstw i kontach usługi Azure Active Directory za pomocą otwartych standardowych protokołów.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aktualizowanie aplikacji w usłudze Azure AD B2C
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie przepływów użytkownika](tutorial-create-user-flows.md), aby umieścić platformę użytkownika w Twojej aplikacji.
* Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/downloads/) z **obciążeniem ASP.NET i tworzenia sieci Web.**

## <a name="update-the-application-registration"></a>Aktualizowanie rejestracji aplikacji

W samouczku, który został ukończony w ramach wymagań wstępnych, zarejestrowano aplikację sieci web w usłudze Azure AD B2C. Aby włączyć komunikację z przykładem w tym samouczku, należy dodać identyfikator URI przekierowania i utworzyć klucz tajny klienta (klucz) dla zarejestrowanej aplikacji.

### <a name="add-a-redirect-uri-reply-url"></a>Dodawanie identyfikatora URI przekierowania (adresu URL odpowiedzi)

Aby zaktualizować aplikację, można użyć bieżącego środowiska **aplikacji** lub naszego nowego środowiska rejestracji aplikacji (Wersja zapoznawcza) w wersji 100 000 000 000 000 000 000 000 000 000 **000** 000 [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **Aplikacje**, a następnie wybierz aplikację *webapp1*.
1. W obszarze **Adres URL odpowiedzi** dodaj `https://localhost:44316`.
1. Wybierz **pozycję Zapisz**.
1. Na stronie właściwości należy zarejestrować identyfikator aplikacji do użycia w późniejszym kroku podczas konfigurowania aplikacji sieci web.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. Wybierz **pozycję Rejestracje aplikacji (Wersja zapoznawcza),** wybierz kartę **Posiadane aplikacje,** a następnie wybierz aplikację *webapp1.*
1. Wybierz **pozycję Uwierzytelnianie**, a następnie wybierz pozycję **Wypróbuj nowe środowisko** (jeśli jest to wyświetlane).
1. W **obszarze Sieć Web**zaznacz łącze Dodaj identyfikator **URI,** wprowadź , `https://localhost:44316`a następnie wybierz pozycję **Zapisz**.
1. Wybierz pozycję **Przegląd**.
1. Podczas konfigurowania aplikacji sieci web należy **zarejestrować identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.

* * *

### <a name="create-a-client-secret"></a>Tworzenie klucza tajnego klienta

Następnie utwórz klucz tajny klienta dla zarejestrowanej aplikacji sieci web. Przykładowy kod aplikacji sieci web używa tego, aby udowodnić swoją tożsamość podczas żądania tokenów.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-the-sample"></a>Konfigurowanie przykładu

W tym samouczku skonfigurujesz przykładową aplikację, którą możesz pobrać z witryny GitHub. W przykładowej aplikacji użyto platformy ASP.NET, aby udostępnić prostą listę zadań do wykonania. W przykładowej aplikacji użyto [składników oprogramowania pośredniczącego Microsoft OWIN](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Pobierz plik ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) lub sklonuj przykład z usługi GitHub. Upewnij się, że przykładowy plik wyodrębniono w folderze, którego łączna liczba znaków ścieżki jest mniejsza niż 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Przykładowe rozwiązanie zawiera następujące dwa projekty:

* **TaskWebApp** — tworzenie i edytowanie listy zadań. W przykładzie użyto przepływu użytkownika **rejestracji lub logowania,** aby zarejestrować się i zalogować użytkowników.
* **TaskService** — obsługuje następujące funkcje listy zadań: tworzenie, odczytywanie, aktualizowanie i usuwanie. Interfejs API jest chroniony przez usługę Azure AD B2C i wywoływany przez aplikację TaskWebApp.

Należy zmienić przykładową aplikację tak, aby korzystała z aplikacji zarejestrowanej w Twojej dzierżawie. Obejmuje to zanotowane wcześniej identyfikator aplikacji i klucz. Skonfiguruj też utworzone przepływy użytkownika. Przykład definiuje wartości konfiguracji jako ustawienia w pliku *Web.config.*

Zaktualizuj ustawienia w pliku Web.config, aby działały z przepływem użytkownika:

1. Otwórz rozwiązanie **B2C-WebAPI-DotNet** w programie Visual Studio.
1. W projekcie **TaskWebApp** otwórz plik **Web.config**.
    1. Zaktualizuj wartość `ida:Tenant` i `ida:AadInstance` nazwę utworzonej dzierżawy usługi Azure AD B2C. Na przykład `fabrikamb2c` zastąp . `contoso`
    1. Zastąp `ida:ClientId` wartość zarejestrowanego identyfikatora aplikacji.
    1. Zastąp wartość elementu `ida:ClientSecret` zanotowanym kluczem. Jeśli klucz tajny klienta zawiera wstępnie zdefiniowane jednostki XML, na przykład mniej niż`<`( ), większe niż (`>`), ampersand (`&`) lub podwójną wycenę (`"`), należy uniknąć tych znaków przez kodowanie XML klucza tajnego klienta przed dodaniem go do pliku Web.config.
    1. Zastąp `ida:SignUpSignInPolicyId` `b2c_1_signupsignin1`wartość na .
    1. Zastąp `ida:EditProfilePolicyId` `b2c_1_profileediting1`wartość na .
    1. Zastąp `ida:ResetPasswordPolicyId` `b2c_1_passwordreset1`wartość na .

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **TaskWebApp**, a następnie kliknij pozycję **Ustaw jako projekt startowy**.
1. Naciśnij klawisz **F5**. Zostanie uruchomiona domyślna przeglądarka z adresem lokalnej witryny internetowej `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Rejestrowanie się przy użyciu adresu e-mail

1. Wybierz **zarejestruj się / Zaloguj się,** aby zarejestrować się jako użytkownik aplikacji. Jest używany przepływ użytkownika **b2c_1_signupsignin1**.
1. Usługa Azure AD B2C wyświetli stronę logowania z linkiem rejestracji. Ponieważ nie masz jeszcze konta, wybierz pozycję **Zarejestruj się teraz**. W przepływie pracy rejestracji jest wyświetlana strona do zbierania i weryfikowania tożsamości użytkownika przy użyciu adresu e-mail. Przepływ pracy rejestracji zbiera też hasło użytkownika i żądane atrybuty zdefiniowane w przepływie użytkownika.
1. Użyj prawidłowego adresu e-mail i przeprowadź weryfikację przy użyciu kodu weryfikacyjnego. Ustaw hasło. Wprowadź wartości żądanych atrybutów.

    ![Strona rejestracji wyświetlana jako część przepływu pracy logowania/rejestracji](./media/tutorial-web-app-dotnet/sign-up-workflow.PNG)

1. Wybierz **pozycję Utwórz,** aby utworzyć konto lokalne w dzierżawie usługi Azure AD B2C.

Użytkownik aplikacji może teraz używać swojego adresu e-mail do logowania się i korzystania z aplikacji sieci web.

Jednak funkcja **listy zadań do wykonania** nie będzie działać, dopóki nie ukończysz następnego samouczka z serii, [Samouczek: Użyj usługi Azure AD B2C do ochrony ASP.NET interfejsu API sieci Web.](tutorial-web-api-dotnet.md)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aktualizowanie aplikacji w usłudze Azure AD B2C
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

Teraz przejdź do następnego samouczka, aby włączyć funkcję **Lista zadań do wykonania** aplikacji sieci web. W nim można zarejestrować aplikację interfejsu API sieci web we własnej dzierżawie usługi Azure AD B2C, a następnie zmodyfikować przykład kodu do użycia dzierżawy do uwierzytelniania interfejsu API.

> [!div class="nextstepaction"]
> [Samouczek: Ochrona ASP.NET >interfejsu API sieci Web za pomocą usługi Azure Active Directory B2C](tutorial-web-api-dotnet.md)
