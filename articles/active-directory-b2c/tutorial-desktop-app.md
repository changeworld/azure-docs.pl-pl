---
title: 'Samouczek: Uwierzytelnij użytkowników w natywnej aplikacji klienckiej'
titleSuffix: Azure AD B2C
description: Samouczek dotyczący sposobu użycia usługi Azure Active Directory B2C w celu określenia nazwy logowania użytkownika na potrzeby aplikacji klasycznej platformy .NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 06d27c3a3daa4702653a2063d0ac70fd094e2d74
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78186203"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>Samouczek: uwierzytelnianie użytkowników na natywnym kliencie pulpitu przy użyciu usługi Azure Active Directory B2C

W tym samouczku pokazano, jak używać usługi Azure Active Directory B2C (Azure AD B2C) do logowania się i rejestrowania użytkowników w aplikacji klasycznej Windows Presentation Foundation (WPF). Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie się na kontach społecznościowych, kontach przedsiębiorstw i kontach usługi Azure Active Directory za pomocą otwartych standardowych protokołów.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie natywnej aplikacji klienckiej
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Tworzenie przepływów użytkownika](tutorial-create-user-flows.md), aby umieścić platformę użytkownika w Twojej aplikacji.
- Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/downloads/) za pomocą obciążeń **deweloperskich** i **ASP.NET i tworzenia sieci Web.**

## <a name="add-the-native-client-application"></a>Dodawanie natywnej aplikacji klienckiej

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Zarejestruj **identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.

## <a name="configure-the-sample"></a>Konfigurowanie przykładu

W tym samouczku skonfigurujesz przykładową aplikację, którą możesz pobrać z witryny GitHub. Przykładowa aplikacja klasyczna WPF demonstruje rejestrację, logowanie i może wywołać chroniony interfejs API sieci web w usłudze Azure AD B2C. [Pobierz plik ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip), [przejrzyj repozytorium](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) lub sklonuj przykład z witryny GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Aby zaktualizować aplikację do pracy z dzierżawą usługi Azure AD B2C i wywołać przepływy użytkowników zamiast przepływów w domyślnej dzierżawie demonstrającej:

1. Otwórz rozwiązanie **active-directory-b2c-wpf** (`active-directory-b2c-wpf.sln`) w programie Visual Studio.
2. W projekcie **active-directory-b2c-wpf** otwórz plik *App.xaml.cs* i znajdź następujące definicje zmiennych. Zamień `{your-tenant-name}` nazwę dzierżawy usługi `{application-ID}` Azure AD B2C i identyfikator aplikacji, który został nagrany wcześniej.

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. Zaktualizuj zmienne nazwy zasad o nazwy przepływów użytkownika utworzonych w ramach wymagań wstępnych. Przykład:

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Naciśnij klawisz **F5**, aby skompilować i uruchomić przykładową aplikację.

### <a name="sign-up-using-an-email-address"></a>Rejestrowanie się przy użyciu adresu e-mail

1. Wybierz **pozycję Zaloguj** się, aby zarejestrować się jako użytkownik. Jest tu używany przepływ użytkownika **B2C_1_signupsignin1**.
2. Usługa Azure AD B2C przedstawia stronę logowania z łączem **Zarejestruj się teraz.** Ponieważ nie masz jeszcze konta, wybierz link **Zarejestruj się teraz.**
3. W przepływie pracy rejestracji jest wyświetlana strona do zbierania i weryfikowania tożsamości użytkownika przy użyciu adresu e-mail. Przepływ pracy rejestracji zbiera też hasło użytkownika i żądane atrybuty zdefiniowane w przepływie użytkownika.

    Użyj prawidłowego adresu e-mail i przeprowadź weryfikację przy użyciu kodu weryfikacyjnego. Ustaw hasło. Wprowadź wartości żądanych atrybutów.

    ![Strona rejestracji wyświetlana jako część przepływu pracy logowania/rejestracji](./media/tutorial-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. Wybierz **pozycję Utwórz,** aby utworzyć konto lokalne w dzierżawie usługi Azure AD B2C.

Użytkownik może teraz używać swojego adresu e-mail do logowania się i korzystania z aplikacji komputerowej. Po pomyślnym zarejestrowaniu się lub zalogowaniu szczegóły tokenu są wyświetlane w dolnym okienku aplikacji WPF.

![Szczegóły tokenu wyświetlane w dolnym okienku aplikacji klasycznej WPF](./media/tutorial-desktop-app/desktop-app-01-post-signin.png)

Jeśli wybierzesz przycisk **Interfejsu API połączeń,** zostanie wyświetlony **komunikat o błędzie.** Napotkasz błąd, ponieważ w jego bieżącym stanie aplikacja próbuje uzyskać dostęp do `fabrikamb2c.onmicrosoft.com`interfejsu API chronionego przez dzierżawę demonstracyjną. Ponieważ token dostępu jest prawidłowy tylko dla dzierżawy usługi Azure AD B2C, wywołanie interfejsu API jest zatem nieautoryzowane.

Przejdź do następnego samouczka, aby zarejestrować chroniony internetowy interfejs API we własnej dzierżawie i włączyć funkcję **interfejsu API wywołania.**

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie natywnej aplikacji klienckiej
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

Następnie, aby włączyć funkcję przycisku **interfejsu API wywołania,** przyznaj aplikacji klasycznej WPF dostęp do internetowego interfejsu API zarejestrowanego we własnej dzierżawie usługi Azure AD B2C:

> [!div class="nextstepaction"]
> [Samouczek: Udzielanie dostępu do internetowego interfejsu API node.js z aplikacji klasycznej >](tutorial-desktop-app-webapi.md)
