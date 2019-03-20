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
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2a7734f729c4b1db7e8c0b4571e8792373ee11ae
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203210"
---
## <a name="set-up-your-project"></a>konfigurowanie projektu

W tej sekcji utworzysz nowy projekt w celu zademonstrowania sposobu integrowania aplikacji Windows Desktop .NET (XAML) z *Zaloguj się przy użyciu Microsoft* tak, aby aplikację można wykonać zapytanie interfejsów API sieci Web, które wymagają tokenu.

Aplikacji, utworzonej za pomocą tego przewodnika przedstawia przycisk, który służy do wywoływania wykresu, obszar, aby wyświetlić wyniki na ekranie, a przycisk wylogowania.

> [!NOTE]
> Chcesz zamiast tego Pobierz ten przykładowy projekt programu Visual Studio? [Pobieranie projektu](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip), a następnie przejdź do [kroku konfiguracji](#register-your-application) do skonfigurowania przykładowego kodu, zanim ją wykonać.
>

Aby utworzyć aplikację, wykonaj następujące czynności:

1. W programie Visual Studio wybierz pozycje **Plik** > **Nowy** > **Projekt**.
2. W obszarze **szablony**, wybierz opcję **Visual C#**.
3. Wybierz **aplikacja WPF (.NET Framework)**, w zależności od wersji programu Visual Studio w wersji korzystasz.

## <a name="add-msal-to-your-project"></a>Dodawanie biblioteki MSAL do projektu

1. W programie Visual Studio wybierz pozycję **Narzędzia** > **Menedżer pakietów NuGet**> **Konsola menedżera pakietów**.
2. W oknie Konsola Menedżera pakietów Wklej następujące polecenia programu Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client
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
        //Below is the clientId of your app registration. 
        //You have to replace the below with the Application Id for your app registration
        private static string ClientId = "your_client_id_here";

        public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

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
