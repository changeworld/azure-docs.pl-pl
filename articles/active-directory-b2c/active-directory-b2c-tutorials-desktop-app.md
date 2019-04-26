---
title: Samouczek — włączanie uwierzytelniania w natywnej aplikacji klienckiej — Azure Active Directory B2C | Microsoft Docs
description: Samouczek dotyczący sposobu użycia usługi Azure Active Directory B2C w celu określenia nazwy logowania użytkownika na potrzeby aplikacji klasycznej platformy .NET.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: a1842859723173412df2053a242ebe9ca4cf7f32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386967"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>Samouczek: włączanie uwierzytelniania w natywnej aplikacji klienckiej przy użyciu usługi Azure Active Directory B2C

W tym samouczku pokazano, jak używać usługi Azure Active Directory (Azure AD) B2C do logowania użytkowników i tworzenia nowych kont w aplikacji klasycznej systemu Windows Presentation Foundation (WPF). Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie się na kontach społecznościowych, kontach przedsiębiorstw i kontach usługi Azure Active Directory za pomocą otwartych standardowych protokołów.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie natywnej aplikacji klienckiej
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Tworzenie przepływów użytkownika](tutorial-create-user-flows.md), aby umieścić platformę użytkownika w Twojej aplikacji. 
- Zainstaluj program [Visual Studio 2017](https://www.visualstudio.com/downloads/) z obciążeniami **Programowanie aplikacji klasycznych dla platformy .NET** oraz **Tworzenie aplikacji na platformie ASP.NET i tworzenie aplikacji internetowych**.

## <a name="add-the-native-client-application"></a>Dodawanie natywnej aplikacji klienckiej

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
5. Wprowadź nazwę aplikacji. Na przykład *nativeapp1*.
6. Dla pozycji **Uwzględnij aplikację internetową/internetowy interfejs API** wybierz wartość **Nie**.
7. Dla pozycji **Uwzględnij klienta natywnego** wybierz wartość **Tak**.
8. Dla pozycji **Identyfikator URI przekierowania** wprowadź prawidłowy identyfikator URI przekierowania ze schematem niestandardowym. Istnieją dwie ważne kwestie do rozważenia podczas wybierania identyfikatora URI przekierowania:

    - **Unikatowość** — schemat identyfikatora URI przekierowania powinien być unikatowy dla każdej aplikacji. W przykładzie `com.onmicrosoft.contoso.appname://redirect/path` jest to schemat `com.onmicrosoft.contoso.appname`. Należy przestrzegać tego wzorca. Jeśli dwie aplikacje mają ten sam schemat, użytkownik ma możliwość wyboru aplikacji. Jeśli użytkownik dokona nieprawidłowego wyboru, logowanie nie powiedzie się.
    - **Kompletność** — identyfikator URI przekierowania musi mieć schemat i ścieżkę. Ścieżka musi zawierać co najmniej jeden ukośnik po nazwie domeny. Na przykład `//contoso/` działa a `//contoso` zakończy się niepowodzeniem. Upewnij się, że identyfikator URI przekierowania nie zawiera znaków specjalnych, takich jak podkreślenia.

9. Kliknij pozycję **Utwórz**.
10. Na stronie właściwości należy zapisać identyfikator aplikacji, który będzie używany podczas konfigurowania przykładowej aplikacji.

## <a name="configure-the-sample"></a>Konfigurowanie przykładu

W tym samouczku skonfigurujesz przykładową aplikację, którą możesz pobrać z witryny GitHub. Przykładowa aplikacja klasyczna WPF pokazuje rejestrację, logowanie, a następnie wywołuje chroniony internetowy interfejs API w usłudze Azure AD B2C. [Pobierz plik ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [przejrzyj repozytorium](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) lub sklonuj przykład z witryny GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Aby zmienić ustawienia aplikacji, zastąp `<your-tenant-name>` nazwą swojej dzierżawy i zastąp`<application-ID`> zapisanym identyfikatorem aplikacji.

1. Otwórz rozwiązanie `active-directory-b2c-wpf` w programie Visual Studio.
2. W projekcie `active-directory-b2c-wpf` otwórz plik **App.xaml.cs** i wprowadź następujące aktualizacje:

    ```C#
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. Zaktualizuj zmienną **PolicySignUpSignIn** za pomocą nazwy utworzonego przepływu użytkownika.

    ```C#
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Naciśnij klawisz **F5**, aby skompilować i uruchomić przykładową aplikację.

### <a name="sign-up-using-an-email-address"></a>Rejestrowanie się przy użyciu adresu e-mail

1. Kliknij przycisk **Zaloguj**, aby zarejestrować się jako użytkownik. Jest tu używany przepływ użytkownika **B2C_1_signupsignin1**.
2. Usługa Azure AD B2C wyświetli stronę logowania z linkiem rejestracji. Ponieważ nie masz jeszcze konta, kliknij link **Sign up now** (Zarejestruj się teraz). 
3. W przepływie pracy rejestracji jest wyświetlana strona do zbierania i weryfikowania tożsamości użytkownika przy użyciu adresu e-mail. Przepływ pracy rejestracji zbiera też hasło użytkownika i żądane atrybuty zdefiniowane w przepływie użytkownika.

    Użyj prawidłowego adresu e-mail i przeprowadź weryfikację przy użyciu kodu weryfikacyjnego. Ustaw hasło. Wprowadź wartości żądanych atrybutów. 

    ![Przepływ pracy rejestracji](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Kliknij pozycję **Utwórz**, aby utworzyć konto lokalne w dzierżawie usługi Azure AD B2C.

Teraz użytkownik może logować się i korzystać z aplikacji klasycznej, używając swojego adresu e-mail.

> [!NOTE]
> Kliknięcie przycisku **Wywołaj interfejs API** spowoduje wyświetlenie błędu „Brak autoryzacji”. Ten błąd jest wyświetlany, ponieważ próbujesz uzyskać dostęp do zasobu z dzierżawy pokazowej. Ponieważ token dostępu jest prawidłowy tylko dla dzierżawy usługi Azure AD, wywołanie interfejsu API nie ma autoryzacji. Przejdź do następnego samouczka, aby utworzyć chroniony internetowy interfejs API.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie natywnej aplikacji klienckiej
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

> [!div class="nextstepaction"]
> [Samouczek: udzielanie dostępu do internetowego interfejsu API platformy Node.js z aplikacji klasycznej przy użyciu usługi Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
