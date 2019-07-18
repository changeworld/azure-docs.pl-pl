---
title: Microsoft Identity platform Windows Desktop — Szybki Start | Microsoft Docs
description: Dowiedz się, w jaki sposób aplikacja Windows Desktop .NET (XAML) może uzyskać token dostępu i wywołać interfejs API chroniony przez punkt końcowy platformy tożsamości firmy Microsoft
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe90e5a70779857f0045baf27abc59a75289c39c
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276819"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-windows-desktop-app"></a>Szybki start: Uzyskiwanie tokenu i wywoływanie interfejsu API programu Microsoft Graph z poziomu aplikacji klasycznej w systemie Windows

W tym przewodniku Szybki start dowiesz się, jak napisać aplikację klasyczną .NET (WPF) w systemie Windows umożliwiającą logowanie się na kontach osobistych i służbowych, uzyskiwanie tokenów dostępu i wywoływanie interfejsu API programu Microsoft Graph.

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start](media/quickstart-v2-windows-desktop/windesktop-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Istnieją dwie opcje uruchamiania aplikacji Szybki start:
> * [Ekspresowo] [Opcja 1: zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ręcznie] [Opcja 2: zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1: zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do nowego [Azure Portal-rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację za pomocą tylko jednego kliknięcia.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2: zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Rejestrowanie aplikacji
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
> 1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://aka.ms/MobileAppReg) .
> 1. Wybierz pozycję **Nowa rejestracja**.
>      - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `Win-App-calling-MsGraph`.
>      - W sekcji **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft (na przykład Skype, Xbox, Outlook.com)** .
>      - Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
> 1. Na liście stron dla aplikacji wybierz pozycję **Uwierzytelnianie**.
> 1. Rozwiń sekcję **Desktop + Devices (urządzenia** ).  (Jeśli **komputery stacjonarne i urządzenia** nie są widoczne, najpierw kliknij górny baner, aby zobaczyć środowisko uwierzytelniania w wersji zapoznawczej).
> 1. W sekcji **Identyfikator URI przekierowania** wybierz pozycję **Dodaj identyfikator URI**.  Typ **urn: IETF: wg: OAuth: 2.0: OOB**.
> 1. Wybierz pozycję **Zapisz**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1: konfigurowanie aplikacji w witrynie Azure Portal
> Aby kod przykładowy z tego samouczka działał, należy dodać adres URL odpowiedzi w formacie **urn:ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-windows-desktop/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-your-visual-studio-project"></a>Krok 2: pobieranie projektu programu Visual Studio

[Pobierz projekt programu Visual Studio](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)  ([Wyświetl projekt w serwisie GitHub](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/))

#### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3: konfigurowanie projektu programu Visual Studio

1. Wyodrębnij plik zip do folderu lokalnego blisko folderu głównego dysku, na przykład **C:\Azure-Samples**.
1. Otwórz projekt w programie Visual Studio.
1. Edytuj plik **App.Xaml.cs** i zastąp wartości pól `ClientId` i `Tenant` następującym kodem:

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    private static string Tenant = "Enter_the_Tenant_Info_Here";
    ```
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Ten przewodnik Szybki Start obsługuje Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> Gdzie:
> - `Enter_the_Application_Id_here` jest **identyfikatorem aplikacji (klienta)** dla zarejestrowanej aplikacji.
> - `Enter_the_Tenant_Info_Here` to wartość ustawiana na jedną z następujących opcji:
>   - Jeśli aplikacja obsługuje tryb **Konta w tym katalogu organizacyjnym**, zastąp tę wartość za pomocą wartości **Identyfikator dzierżawy** lub **Nazwa dzierżawy** (na przykład contoso.microsoft.com)
>   - Jeśli aplikacja obsługuje tryb**Konta w dowolnym katalogu organizacyjnym**, zastąp tę wartość za pomocą wartości `organizations`
>   - Jeśli aplikacja obsługuje tryb **Konta w moim katalogu organizacyjnym i osobiste konta Microsoft**, zastąp tę wartość za pomocą wartości `common`
>
> > [!TIP]
> > Aby znaleźć wartości **Identyfikator aplikacji (klienta)** , **Identyfikator katalogu (dzierżawy)** i **Obsługiwane typy kont**, przejdź do strony **Przegląd** w witrynie Azure Portal.

## <a name="more-information"></a>Więcej informacji

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) to biblioteka służąca do logowania użytkowników i żądania tokenów używanych w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Bibliotekę MSAL można zainstalować, uruchamiając następujące polecenie w **Konsoli menedżera pakietów** programu Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Aby dodać odwołanie do biblioteki MSAL, dodaj następujący kod:

```csharp
using Microsoft.Identity.Client;
```

Następnie zainicjuj bibliotekę MSAL, używając następującego kodu:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
```

> |Gdzie: ||
> |---------|---------|
> | `ClientId` | Jest **identyfikatorem aplikacji (klienta)** dla aplikacji zarejestrowanej w witrynie Azure Portal. Tę wartość można znaleźć na stronie **Przegląd** aplikacji w witrynie Azure Portal. |

### <a name="requesting-tokens"></a>Przesyłanie żądań tokenów

Biblioteka MSAL oferuje dwie metody uzyskiwania tokenów: `AcquireTokenInteractive` i `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

W niektórych sytuacjach należy wymusić, aby użytkownicy korzystali z punktu końcowego platformy tożsamości firmy Microsoft za pomocą okna podręcznego w celu sprawdzenia poprawności poświadczeń lub udzielenia zgody. Oto niektóre przykłady:

- Gdy nowi użytkownicy logują się do aplikacji po raz pierwszy.
- Gdy użytkownicy muszą ponownie wprowadzić poświadczenia, ponieważ hasło wygasło.
- Gdy aplikacja żąda dostępu do zasobów wymagającego zgody użytkownika.
- Gdy wymagane jest uwierzytelnianie dwuetapowe.

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(_scopes)
                                      .ExecuteAsync();
```

> |Gdzie:||
> |---------|---------|
> | `_scopes` | Zawiera żądane zakresy, takie jak `{ "user.read" }` dla programu Microsoft Graph lub `{ "api://<Application ID>/access_as_user" }` dla niestandardowych interfejsów internetowych API. |

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

Nie chcesz, aby użytkownik musiał weryfikować poświadczenia za każdym razem, gdy musi uzyskać dostęp do zasobu. Przez większość czasu uzyskiwanie i odnawianie tokenów powinno odbywać się bez żadnej interakcji z użytkownikiem. Możesz użyć metody `AcquireTokenSilent` do uzyskiwania tokenów umożliwiających dostęp do chronionych zasobów po początkowym skorzystaniu z metody `AcquireTokenInteractive`:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Gdzie: ||
> |---------|---------|
> | `scopes` | Zawiera żądane zakresy, takie jak `{ "user.read" }` dla programu Microsoft Graph lub `{ "api://<Application ID>/access_as_user" }` dla niestandardowych interfejsów internetowych API. |
> | `firstAccount` | Określa pierwszego użytkownika w pamięci podręcznej (biblioteka MSAL obsługuje wielu użytkowników w jednej aplikacji). |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Wypróbuj samouczek dotyczący aplikacji klasycznych systemu Windows, aby uzyskać instrukcje krok po kroku dotyczące tworzenia aplikacji i nowych funkcji, w tym pełne objaśnienie informacji zawartych w tym podręczniku Szybki start.

> [!div class="nextstepaction"]
> [Call Graph API tutorial (Samouczek dotyczący wywoływania interfejsu API programu Graph)](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-windesktop)

