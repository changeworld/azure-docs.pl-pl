---
title: Samouczek — Włączanie uwierzytelniania w aplikacji sieci Web — Azure Active Directory B2C
description: Samouczek dotyczący sposobu użycia usługi Azure Active Directory B2C w celu określenia nazwy logowania użytkownika na potrzeby aplikacji internetowej platformy ASP.NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 09/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: f6fa48ea1d98c8bc6e6a6bcdea7c0f1083952e49
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064707"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Samouczek: włączanie uwierzytelniania w aplikacji internetowej przy użyciu usługi Azure Active Directory B2C

W tym samouczku pokazano, jak używać programu Azure Active Directory B2C (Azure AD B2C) do logowania się i rejestrowania użytkowników w aplikacji sieci Web ASP.NET. Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie się na kontach społecznościowych, kontach przedsiębiorstw i kontach usługi Azure Active Directory za pomocą otwartych standardowych protokołów.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aktualizowanie aplikacji w usłudze Azure AD B2C
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie przepływów użytkownika](tutorial-create-user-flows.md), aby umieścić platformę użytkownika w Twojej aplikacji.
* Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/downloads/) przy użyciu obciążeń **ASP.NET i Web Development** .

## <a name="update-the-application"></a>Aktualizowanie aplikacji

W samouczku ukończonym jako część wymagań wstępnych została dodana aplikacja internetowa w usłudze Azure AD B2C. Aby umożliwić komunikację z przykładową aplikacją w tym samouczku, musisz dodać do aplikacji w usłudze Azure AD B2C identyfikator URI przekierowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **Aplikacje**, a następnie wybierz aplikację *webapp1*.
5. W obszarze **Adres URL odpowiedzi** dodaj `https://localhost:44316`.
6. Wybierz pozycję **Zapisz**.
7. Na stronie właściwości zapisz identyfikator aplikacji, który będzie używany podczas konfigurowania aplikacji internetowej.
8. Wybierz pozycję **Klucze**, wybierz opcję **Wygeneruj klucz**, a następnie wybierz pozycję **Zapisz**. Zapisz klucz, którego będziesz używać podczas konfigurowania aplikacji internetowej.

## <a name="configure-the-sample"></a>Konfigurowanie przykładu

W tym samouczku skonfigurujesz przykładową aplikację, którą możesz pobrać z witryny GitHub. W przykładowej aplikacji użyto platformy ASP.NET, aby udostępnić prostą listę zadań do wykonania. W przykładowej aplikacji użyto [składników oprogramowania pośredniczącego Microsoft OWIN](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Pobierz plik ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) lub sklonuj przykład z usługi GitHub. Upewnij się, że przykładowy plik wyodrębniono w folderze, którego łączna liczba znaków ścieżki jest mniejsza niż 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Przykładowe rozwiązanie zawiera następujące dwa projekty:

- **TaskWebApp** — tworzenie i edytowanie listy zadań. Przykład korzysta z przepływu logowania **lub logowania** użytkownika w celu rejestracji i logowania użytkowników.
- **TaskService** — obsługuje następujące funkcje listy zadań: tworzenie, odczytywanie, aktualizowanie i usuwanie. Interfejs API jest chroniony przez usługę Azure AD B2C i wywoływany przez aplikację TaskWebApp.

Należy zmienić przykładową aplikację tak, aby korzystała z aplikacji zarejestrowanej w Twojej dzierżawie. Obejmuje to zanotowane wcześniej identyfikator aplikacji i klucz. Skonfiguruj też utworzone przepływy użytkownika. Przykład definiuje wartości konfiguracji jako ustawienia w pliku *Web. config* .

Zaktualizuj ustawienia w pliku Web. config, aby współdziałać z przepływem użytkownika:

1. Otwórz rozwiązanie **B2C-WebAPI-DotNet** w programie Visual Studio.
1. W projekcie **TaskWebApp** otwórz plik **Web.config**.
    1. Zastąp wartość `ida:Tenant` i `ida:AadInstance` nazwą utworzonego dzierżawcy.
    1. Zastąp wartość `ida:ClientId` identyfikatorem aplikacji, która została zarejestrowana.
    1. Zastąp wartość elementu `ida:ClientSecret` zanotowanym kluczem. Przed dodaniem do pliku Web. config należy kodować kod tajny klienta.
    1. Zastąp wartość wartością `ida:SignUpSignInPolicyId`. `b2c_1_signupsignin1`
    1. Zastąp wartość wartością `ida:EditProfilePolicyId`. `b2c_1_profileediting1`
    1. Zastąp wartość wartością `ida:ResetPasswordPolicyId`. `b2c_1_passwordreset1`

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **TaskWebApp**, a następnie kliknij pozycję **Ustaw jako projekt startowy**.
2. Naciśnij klawisz **F5**. Zostanie uruchomiona domyślna przeglądarka z adresem lokalnej witryny internetowej `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Rejestrowanie się przy użyciu adresu e-mail

1. Kliknij pozycję **Zarejestruj się/zaloguj**, aby zarejestrować się jako użytkownik aplikacji. Jest używany przepływ użytkownika **b2c_1_signupsignin1**.
2. Usługa Azure AD B2C wyświetli stronę logowania z linkiem rejestracji. Ponieważ nie masz jeszcze konta, wybierz pozycję **Zarejestruj się teraz**. W przepływie pracy rejestracji jest wyświetlana strona do zbierania i weryfikowania tożsamości użytkownika przy użyciu adresu e-mail. Przepływ pracy rejestracji zbiera też hasło użytkownika i żądane atrybuty zdefiniowane w przepływie użytkownika.
3. Użyj prawidłowego adresu e-mail i przeprowadź weryfikację przy użyciu kodu weryfikacyjnego. Ustaw hasło. Wprowadź wartości żądanych atrybutów.

    ![Strona rejestracji wyświetlana w ramach przepływu pracy logowania/rejestrowania](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.PNG)

4. Kliknij pozycję **Utwórz**, aby utworzyć konto lokalne w dzierżawie usługi Azure AD B2C.

Teraz użytkownik może logować się i korzystać z aplikacji internetowej, używając swojego adresu e-mail.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aktualizowanie aplikacji w usłudze Azure AD B2C
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

> [!div class="nextstepaction"]
> [Samouczek: chronienie internetowego interfejsu API platformy ASP.NET za pomocą usługi Azure Active Directory B2C](active-directory-b2c-tutorials-web-api.md)
