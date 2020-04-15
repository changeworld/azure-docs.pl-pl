---
title: Samouczek dotyczący korzystania z odwołań do magazynu kluczy konfiguracji aplikacji Azure w aplikacji ASP.NET Core | Dokumenty firmy Microsoft
description: W tym samouczku dowiesz się, jak korzystać z odwołań do usługi Azure App Configuration's Key Vault z aplikacji ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 4641c50f0579e2a8db514df58c0401eb2173d793
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309046"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Samouczek: Używanie odwołań do magazynu kluczy w aplikacji ASP.NET Core

W tym samouczku dowiesz się, jak korzystać z usługi konfiguracji aplikacji platformy Azure wraz z usługą Azure Key Vault. Konfiguracja aplikacji i usługa Key Vault są usługami uzupełniającymi używanymi obok siebie w większości wdrożeń aplikacji.

Konfiguracja aplikacji ułatwia korzystanie z usług razem, tworząc klucze, które odwołują się do wartości przechowywanych w przechowalni kluczy. Gdy konfiguracja aplikacji tworzy takie klucze, przechowuje identyfikatory URI wartości magazynu kluczy, a nie same wartości.

Aplikacja używa dostawcy klienta konfiguracji aplikacji do pobierania odwołań do usługi Key Vault, podobnie jak w przypadku innych kluczy przechowywanych w konfiguracji aplikacji. W takim przypadku wartości przechowywane w konfiguracji aplikacji są identyfikatory URI, które odwołują się do wartości w magazynie kluczy. Nie są to wartości ani poświadczenia usługi Key Vault. Ponieważ dostawca klienta rozpoznaje klucze jako odwołania do magazynu kluczy, używa usługi Key Vault do pobierania ich wartości.

Aplikacja jest odpowiedzialna za prawidłowe uwierzytelnianie zarówno w konfiguracji aplikacji, jak i w magazynie kluczy. Dwie usługi nie komunikują się bezpośrednio.

W tym samouczku pokazano, jak zaimplementować odwołania do usługi Key Vault w kodzie. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem zakończ tworzenie aplikacji ASP.NET Core przy czym najpierw [zostanie utworzona konfiguracja aplikacji.](./quickstart-aspnet-core-app.md)

Aby wykonać czynności opisane w tym samouczku, można użyć dowolnego edytora kodu. Na przykład [Visual Studio Code](https://code.visualstudio.com/) to wieloplatformowy edytor kodu, który jest dostępny dla systemów operacyjnych Windows, macOS i Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz klucz konfiguracji aplikacji, który odwołuje się do wartości przechowywanej w magazynie kluczy.
> * Dostęp do wartości tego klucza z aplikacji sieci web ASP.NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy zainstalować [pakiet .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Tworzenie magazynu

1. Wybierz opcję **Utwórz zasób** w lewym górnym rogu witryny Azure Portal:

    ![Dane wyjściowe po zakończeniu tworzenia magazynu kluczy](./media/quickstarts/search-services.png)
1. W polu wyszukiwania wprowadź **przechowalnię kluczy**.
1. Z listy wyników wybierz **pozycję Przechowalnia kluczy** po lewej stronie.
1. W **obszarze Przechowalnia kluczy**wybierz pozycję **Dodaj**.
1. Po prawej stronie w **programie Utwórz magazyn kluczy**podaj następujące informacje:
    - Wybierz **subskrypcję,** aby wybrać subskrypcję.
    - W **obszarze Grupa zasobów**wybierz pozycję **Utwórz nowy** i wprowadź nazwę grupy zasobów.
    - W **nazwie magazynu kluczy**wymagana jest unikatowa nazwa. W tym samouczku wprowadź **Contoso-vault2**.
    - Z listy rozwijanej **Region** wybierz lokalizację.
1. Pozostaw inne opcje **tworzenia magazynu kluczy** z wartościami domyślnymi.
1. Wybierz **pozycję Utwórz**.

W tym momencie twoje konto platformy Azure jest jedynym autoryzowanym do uzyskania dostępu do tego nowego magazynu.

![Dane wyjściowe po zakończeniu tworzenia magazynu kluczy](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Dodawanie wpisu tajnego do usługi Key Vault

Aby dodać klucz tajny do magazynu, musisz wykonać tylko kilka dodatkowych kroków. W takim przypadku należy dodać komunikat, którego można użyć do przetestowania pobierania usługi Key Vault. Wiadomość nosi nazwę **Message**i przechowujesz w niej wartość "Hello from Key Vault".

1. Na stronach właściwości magazynu kluczy wybierz pozycję **Wpisy tajne**.
1. Wybierz **pozycję Generuj/Importuj**.
1. W okienku **Utwórz klucz tajny** wprowadź następujące wartości:
    - **Opcje przesyłania**: Wprowadź **instrukcję**.
    - **Nazwa**: Wprowadź **wiadomość**.
    - **Wartość**: Wprowadź **Hello z przechowalni kluczy**.
1. Pozostaw inne Tworzenie właściwości **tajnych** z ich wartościami domyślnymi.
1. Wybierz **pozycję Utwórz**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Dodawanie odwołania do usługi Key Vault do konfiguracji aplikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **pozycję Wszystkie zasoby**, a następnie wybierz wystąpienie sklepu konfiguracja aplikacji utworzone w przewodniku Szybki start.

1. Wybierz **Pozycję Eksplorator konfiguracji**.

1. Wybierz **+ Utwórz** > **odwołanie do przechowalni kluczy,** a następnie określ następujące wartości:
    - **Klucz**: Wybierz **TestApp:Ustawienia:KeyVaultMessage**.
    - **Etykieta**: Pozostaw tę wartość pustą.
    - **Subskrypcja,** **Grupa zasobów**i **Magazyn kluczy**: Wprowadź wartości odpowiadające wartościom w magazynie kluczy utworzonym w poprzedniej sekcji.
    - **Klucz tajny:** Wybierz klucz tajny o nazwie **Wiadomość** utworzona w poprzedniej sekcji.

## <a name="connect-to-key-vault"></a>Łączenie się z przechowalnią kluczy

1. W tym samouczku używasz jednostki usługi do uwierzytelniania do usługi Key Vault. Aby utworzyć tego podmiotu usługi, użyj polecenia Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Ta operacja zwraca serię par klucz/wartość:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Uruchom następujące polecenie, aby umożliwić podmiotowi usługi dostęp do magazynu kluczy:

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Dodaj zmienne środowiskowe do przechowywania wartości *clientId*, *clientSecret*i *tenantId*.

    #### <a name="windows-command-prompt"></a>[Wiersz polecenia systemu Windows](#tab/cmd)

    ```cmd
    setx AZURE_CLIENT_ID <clientId-of-your-service-principal>
    setx AZURE_CLIENT_SECRET <clientSecret-of-your-service-principal>
    setx AZURE_TENANT_ID <tenantId-of-your-service-principal>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    $Env:AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    $Env:AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    export AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    export AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    ---

    > [!NOTE]
    > Te poświadczenia usługi Key Vault są używane tylko w aplikacji. Aplikacja uwierzytelnia się bezpośrednio w ucho. Nigdy nie są one przekazywane do usługi konfiguracji aplikacji.

1. Uruchom ponownie terminal, aby załadować te nowe zmienne środowiskowe.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Aktualizowanie kodu w celu użycia odwołania do usługi Key Vault

1. Dodaj odwołanie do wymaganych pakietów NuGet, uruchamiając następujące polecenie:

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. Otwórz *Program.cs*i dodaj odwołania do następujących wymaganych pakietów:

    ```csharp
    using Azure.Identity;
    ```

1. Zaktualizuj metodę, `CreateWebHostBuilder` aby `config.AddAzureAppConfiguration` użyć konfiguracji aplikacji, wywołując metodę. Dołącz `ConfigureKeyVault` tę opcję i przekaż poprawne poświadczenia do magazynu kluczy.

    #### <a name="net-core-2x"></a>[.NET Rdzeń 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Rdzeń 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

1. Podczas inicjowania połączenia z konfiguracją aplikacji można skonfigurować `ConfigureKeyVault` połączenie z usługą Key Vault, wywołując tę metodę. Po inicjalizacji można uzyskać dostęp do wartości odwołań do usługi Key Vault w taki sam sposób, w jaki uzyskuje się dostęp do wartości zwykłych kluczy konfiguracji aplikacji.

    Aby wyświetlić ten proces w akcji, otwórz *plik Index.cshtml* w folderze**Narzędzia macierzyste** **widoków.** >  Zastąp jego zawartość następującym kodem:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Dostęp do wartości odwołania Do skarbca kluczy **TestApp:Settings:KeyVaultMessage** w taki sam sposób, jak dla wartości konfiguracji **TestApp:Settings:Message**.

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Aby utworzyć aplikację przy użyciu interfejsu wiersza polecenia .NET Core, uruchom następujące polecenie w powłoce poleceń:

    ```dotnetcli
    dotnet build
    ```

1. Po zakończeniu kompilacji użyj następującego polecenia, aby uruchomić aplikację sieci web lokalnie:

    ```dotnetcli
    dotnet run
    ```

1. Otwórz okno przeglądarki i `http://localhost:5000`przejdź do , który jest domyślnym adresem URL aplikacji internetowej hostowanego lokalnie.

    ![Uruchamianie aplikacji lokalnej Szybki start](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono klucz konfiguracji aplikacji, który odwołuje się do wartości przechowywanej w magazynie kluczy. Aby dowiedzieć się, jak dodać tożsamość usługi zarządzanej przez platformę Azure, która usprawnia dostęp do konfiguracji aplikacji i usługi Key Vault, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)
