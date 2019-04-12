---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: ce95e8d0249a886e031e3ae0fe9dd8e20804f391
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59498398"
---
## <a name="set-up-your-project"></a>konfigurowanie projektu

W tej sekcji utworzysz nowy projekt w celu zademonstrowania sposobu integrowania aplikacji Windows Desktop .NET (XAML) z *Zaloguj się przy użyciu Microsoft* tak, aby aplikację można wykonać zapytanie interfejsów API sieci Web, które wymagają tokenu.

Aplikacji, utworzonej za pomocą tego przewodnika przedstawia przycisk, który służy do wywoływania wykresu, obszar, aby wyświetlić wyniki na ekranie, a przycisk wylogowania.

> [!NOTE]
> Chcesz zamiast tego Pobierz ten przykładowy projekt programu Visual Studio? [Pobieranie projektu](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip), a następnie przejdź do [kroku konfiguracji](#register-your-application) do skonfigurowania przykładowego kodu, zanim ją wykonać.
>

Aby utworzyć aplikację, wykonaj następujące czynności:

1. W programie Visual Studio wybierz pozycje **Plik** > **Nowy** > **Projekt**.
2. W obszarze **szablony**, wybierz opcję **Visual C#**.
3. Wybierz **aplikacja WPF (.NET Framework)**, w zależności od wersji programu Visual Studio w wersji korzystasz.

## <a name="add-msal-to-your-project"></a>Dodawanie biblioteki MSAL do projektu

1. W programie Visual Studio wybierz pozycję **Narzędzia** > **Menedżer pakietów NuGet**> **Konsola menedżera pakietów**.
2. W oknie Konsola Menedżera pakietów Wklej następujące polecenia programu Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > To polecenie instaluje Biblioteka Microsoft Authentication Library. Biblioteka MSAL obsługuje pobieranie, buforowanie i odświeżanie tokenów użytkownika, które umożliwiają dostęp do interfejsów API, które są chronione przez usługę Azure Active Directory w wersji 2.0
    >

## <a name="add-the-code-to-initialize-msal"></a>Dodaj kod, aby zainicjować biblioteki MSAL

W tym kroku utworzysz klasy do obsługi interakcji z biblioteki MSAL, takie jak Obsługa tokenów.

1. Otwórz *App.xaml.cs* pliku, a następnie dodaj odwołanie do biblioteki MSAL do klasy:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Aktualizacja klasy aplikacji do następujących:

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - Te content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>Tworzenie aplikacji interfejsu użytkownika

W tej sekcji przedstawiono, jak aplikacja może zapytać chronionych serwerów zaplecza, takich jak program Microsoft Graph. 

A *MainWindow.xaml* automatycznie utworzony plik jako część szablonu projektu. Otwórz ten plik, a następnie zastąp aplikacji  *\<siatki >* węzła z następującym kodem:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```
