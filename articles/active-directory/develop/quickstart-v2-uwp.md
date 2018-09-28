---
title: Przewodnik Szybki start dotyczący usługi Azure AD w wersji 2.0 dla platformy UWP w systemie Windows | Microsoft Docs
description: Dowiedz się, w jaki sposób aplikacja platformy UWP (XAML) w systemie Windows może uzyskiwać tokeny dostępu i wywoływać interfejs API chroniony przez punkt końcowy usługi Azure Active Directory w wersji 2.0.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 5241df87297fb2e293e2cc828821e66d6f2837b0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970830"
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Wywoływanie interfejsu API programu Microsoft Graph z poziomu aplikacji platformy UWP

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Ten przewodnik Szybki start zawiera przykładowy kod, który pokazuje, w jaki sposób można logować się na kontach osobistych i służbowych, uzyskiwać tokeny dostępu i wywoływać interfejs API programu Microsoft Graph z poziomu aplikacji platformy uniwersalnej systemu Windows (UWP).

![Jak działa przykładowa aplikacja wygenerowana w tym przewodniku Szybki start](media/quickstart-v2-uwp/uwp-intro.png)

> [!div renderon="docs"]
> ## <a name="register-and-download"></a>Rejestrowanie i pobieranie
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Rejestrowanie i konfigurowanie aplikacji i przykładowego kodu
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> Aby zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
> 1. Przejdź do [portalu rejestracji aplikacji firmy Microsoft](https://apps.dev.microsoft.com/portal/register-app), aby zarejestrować aplikację.
> 1. W polu **Application Name** (Nazwa aplikacji) wprowadź nazwę aplikacji.
> 1. Upewnij się, że pole **Guided Setup** (Konfiguracja z przewodnikiem) jest wyczyszczone, a następnie wybierz pozycję **Create** (Utwórz).
> 1. Wybierz kolejno pozycje **Add platform** (Dodaj platformę), **Native Application** (Aplikacja natywna) i **Save** (Zapisz).

> [!div renderon="portal" class="sxs-lookup alert alert-info"]
> #### <a name="step-1-configure-your-application"></a>Krok 1. Konfigurowanie aplikacji
> Aby kod przykładowy z tego samouczka działał, należy dodać adres URL przekierowania w formacie **urn:ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already configured](media/quickstart-v2-uwp/green-check.png) (Już skonfigurowano) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów

#### <a name="step-2-download-your-visual-studio-project"></a>Krok 2. Pobieranie projektu programu Visual Studio

 - [Pobierz projekt programu Visual Studio 2017](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3. Konfigurowanie projektu programu Visual Studio

1. Wyodrębnij plik zip do folderu lokalnego (na przykład **C:\Azure-Samples**)
1. Otwórz projekt w programie Visual Studio.
1. Edytuj ciąg **App.Xaml.cs** i zastąp wiersz rozpoczynający się od ciągu `private static string ClientId` ciągiem:

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="more-information"></a>Więcej informacji

Poniżej znajduje się omówienie tego przewodnika Szybki start:

### <a name="msalnet"></a>MSAL.NET

Biblioteka MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) to biblioteka umożliwiająca logowanie użytkowników i przesyłanie żądań tokenów używanych do uzyskiwania dostępu do interfejsu API chronionego przez usługę Azure Active Directory. Można ją zainstalować, uruchamiając następujące polecenie w *Konsoli menedżera pakietów* programu Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Możesz dodać odwołanie dla biblioteki MSAL, dodając poniższy wiersz:

```csharp
using Microsoft.Identity.Client;
```

Następnie zainicjuj bibliotekę MSAL, używając następującego wiersza:

```csharp
public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
```

> |Gdzie: ||
> |---------|---------|
> |ClientId | Identyfikator aplikacji zarejestrowanej w witrynie *portal.microsoft.com* |

### <a name="requesting-tokens"></a>Przesyłanie żądań tokenów

Biblioteka MSAL oferuje dwie metody uzyskiwania tokenów: `AcquireTokenAsync` i `AcquireTokenSilentAsync`:

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

 W niektórych sytuacjach trzeba wymusić interakcję użytkowników z punktem końcowym usługi Azure Active Directory w wersji 2.0 za pośrednictwem wyskakującego okienka w celu weryfikacji poświadczeń lub wyrażenia zgody —oto niektóre przykłady:

- Nowi użytkownicy logują się do aplikacji po raz pierwszy.
- Użytkownicy muszą ponownie wprowadzić poświadczenia, ponieważ hasło wygasło.
- Aplikacja żąda dostępu do zasobów wymagającego zgody użytkownika.
- Wymagane jest uwierzytelnianie dwuetapowe.

```csharp
authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
```

> |Gdzie:||
> |---------|---------|
> |scopes | Zawiera żądane zakresy (czyli `{ "user.read" }` dla programu Microsoft Graph lub `{ "api://<Application ID>/access_as_user" }` dla niestandardowych interfejsów internetowych API) |

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

Nie chcesz, aby użytkownik musiał weryfikować poświadczenia za każdym razem, gdy musi uzyskać dostęp do zasobu— przez większość czasu uzyskiwanie i odnawianie tokenów powinno odbywać się bez żadnej interakcji z użytkownikiem. Metoda `AcquireTokenSilentAsync` jest powszechnie używana do uzyskiwania tokenów dostępu do chronionych zasobów po początkowym zastosowaniu metody `AcquireTokenAsync`:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, accounts.FirstOrDefault());
```

> |Gdzie: ||
> |---------|---------|
> |scopes | Zawiera żądane zakresy (czyli `{ "user.read" }` dla programu Microsoft Graph lub `{ "api://<Application ID>/access_as_user" }` dla niestandardowych interfejsów internetowych API) |
> |accounts.FirstOrDefault() | Pierwszy użytkownik w pamięci podręcznej (biblioteka MSAL obsługuje wielu użytkowników w jednej aplikacji) |

## <a name="next-steps"></a>Następne kroki

Wypróbuj samouczek dotyczący aplikacji klasycznych systemu Windows, aby uzyskać instrukcje krok po kroku dotyczące tworzenia aplikacji i tworzenia nowych funkcji, w tym pełne objaśnienie informacji zawartych w tym przewodniku Szybki start:

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Dowiedz się więcej na temat czynności, które są wymagane to utworzenia aplikacji użytej w tym przewodniku Szybki start

> [!div class="nextstepaction"]
> [UWP — Call Graph API tutorial (Samouczek dotyczący platformy UWP i wywoływania interfejsu API programu Graph)](tutorial-v2-windows-uwp.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]