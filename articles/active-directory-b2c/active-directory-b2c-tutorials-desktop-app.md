---
title: Samouczek — włączanie uwierzytelniania w natywnej aplikacji klienckiej — Azure Active Directory B2C | Microsoft Docs
description: Samouczek dotyczący sposobu użycia usługi Azure Active Directory B2C w celu określenia nazwy logowania użytkownika na potrzeby aplikacji klasycznej platformy .NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3740a032db6ca9fd0fb88ce348610684d9f895bc
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326327"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>Samouczek: włączanie uwierzytelniania w natywnej aplikacji klienckiej przy użyciu usługi Azure Active Directory B2C

W tym samouczku pokazano, jak używać programu Azure Active Directory B2C (Azure AD B2C) do logowania się i rejestrowania użytkowników w aplikacji klasycznej Windows Presentation Foundation (WPF). Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie się na kontach społecznościowych, kontach przedsiębiorstw i kontach usługi Azure Active Directory za pomocą otwartych standardowych protokołów.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie natywnej aplikacji klienckiej
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Tworzenie przepływów użytkownika](tutorial-create-user-flows.md), aby umieścić platformę użytkownika w Twojej aplikacji.
- Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/downloads/) przy użyciu programu **.NET desktop Development** i **ASP.NET oraz obciążeń związanych z programowaniem aplikacji sieci Web** .

## <a name="add-the-native-client-application"></a>Dodawanie natywnej aplikacji klienckiej

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Zapisz **Identyfikator aplikacji** do użycia w późniejszym kroku.

## <a name="configure-the-sample"></a>Konfigurowanie przykładu

W tym samouczku skonfigurujesz przykładową aplikację, którą możesz pobrać z witryny GitHub. Przykładowa aplikacja klasyczna WPF pokazuje rejestrację, logowanie, a następnie wywołuje chroniony internetowy interfejs API w usłudze Azure AD B2C. [Pobierz plik ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [przejrzyj repozytorium](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) lub sklonuj przykład z witryny GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Aby zmienić ustawienia aplikacji, zastąp `<your-tenant-name>` nazwą swojej dzierżawy i zastąp`<application-ID`> zapisanym identyfikatorem aplikacji.

1. Otwórz rozwiązanie `active-directory-b2c-wpf` w programie Visual Studio.
2. W projekcie `active-directory-b2c-wpf` otwórz plik **App.xaml.cs** i wprowadź następujące aktualizacje:

    ```csharp
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. Zaktualizuj zmienną **PolicySignUpSignIn** za pomocą nazwy utworzonego przepływu użytkownika.

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Naciśnij klawisz **F5**, aby skompilować i uruchomić przykładową aplikację.

### <a name="sign-up-using-an-email-address"></a>Rejestrowanie się przy użyciu adresu e-mail

1. Kliknij przycisk **Zaloguj**, aby zarejestrować się jako użytkownik. Jest tu używany przepływ użytkownika **B2C_1_signupsignin1**.
2. Usługa Azure AD B2C wyświetli stronę logowania z linkiem rejestracji. Ponieważ nie masz jeszcze konta, kliknij link **Sign up now** (Zarejestruj się teraz).
3. W przepływie pracy rejestracji jest wyświetlana strona do zbierania i weryfikowania tożsamości użytkownika przy użyciu adresu e-mail. Przepływ pracy rejestracji zbiera też hasło użytkownika i żądane atrybuty zdefiniowane w przepływie użytkownika.

    Użyj prawidłowego adresu e-mail i przeprowadź weryfikację przy użyciu kodu weryfikacyjnego. Ustaw hasło. Wprowadź wartości żądanych atrybutów.

    ![Strona rejestracji wyświetlana w ramach przepływu pracy logowania/rejestrowania](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

4. Kliknij pozycję **Utwórz**, aby utworzyć konto lokalne w dzierżawie usługi Azure AD B2C.

Teraz użytkownik może logować się i korzystać z aplikacji klasycznej, używając swojego adresu e-mail.

> [!NOTE]
> Kliknięcie przycisku **Wywołaj interfejs API** spowoduje wyświetlenie błędu „Brak autoryzacji”. Ten błąd jest wyświetlany, ponieważ próbujesz uzyskać dostęp do zasobu z dzierżawy pokazowej. Ponieważ token dostępu jest prawidłowy tylko dla dzierżawy usługi Azure AD, wywołanie interfejsu API nie ma autoryzacji. Przejdź do następnego samouczka, aby utworzyć chroniony internetowy interfejs API.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie natywnej aplikacji klienckiej
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

> [!div class="nextstepaction"]
> [Samouczek: udzielanie dostępu do internetowego interfejsu API platformy Node.js z aplikacji klasycznej przy użyciu usługi Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
