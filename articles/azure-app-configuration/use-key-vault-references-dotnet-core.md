---
title: Samouczek dotyczący używania usługi Azure App Configuration Key Vault References w aplikacji ASP.NET Core | Microsoft Docs
description: W tym samouczku dowiesz się, jak używać odwołań Key Vault konfiguracji aplikacji platformy Azure z poziomu aplikacji ASP.NET Core
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
ms.date: 10/07/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: a14cb3035c159c82df44f686da7f7b78ef942943
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035858"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Samouczek: Używanie odwołań Key Vault w aplikacji ASP.NET Core

W tym samouczku dowiesz się, jak korzystać z usługi Azure App Configuration razem z Azure Key Vault. Są to usługi uzupełniające, które będą używane obok siebie w większości wdrożeń aplikacji. Aby ułatwić korzystanie z nich, konfiguracja aplikacji umożliwia tworzenie kluczy, które odwołują się do wartości przechowywanych w Key Vault. Po wykonaniu tej czynności konfiguracja aplikacji przechowuje identyfikator URI na wartość Key Vault, a nie samą wartość. Aplikacja pobiera wartość tego klucza przy użyciu dostawcy klienta konfiguracji aplikacji, podobnie jak w przypadku każdego innego klucza przechowywanego w konfiguracji aplikacji. Dostawca klienta rozpoznaje go jako odwołanie Key Vault i wywołuje Key Vault, aby pobrać wartość. Aplikacja jest odpowiedzialna za prawidłowe uwierzytelnianie zarówno w konfiguracji aplikacji, jak i Key Vault. Te dwie usługi nie komunikują się bezpośrednio.

W tym samouczku pokazano, jak zaimplementować Key Vault odwołania w kodzie. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem najpierw Zakończ [Tworzenie aplikacji ASP.NET Coreej z konfiguracją aplikacji](./quickstart-aspnet-core-app.md) .

Aby wykonać kroki opisane w tym samouczku, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) jest doskonałym rozwiązaniem dostępnym na platformach Windows, MacOS i Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz klucz konfiguracji aplikacji odwołujący się do wartości przechowywanej w Key Vault
> * Uzyskaj dostęp do wartości tego klucza z aplikacji sieci Web ASP.NET Core

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten samouczek, zainstaluj [zestaw .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Tworzenie magazynu

1. Wybierz opcję **Utwórz zasób** w lewym górnym rogu witryny Azure Portal

    ![Dane wyjściowe po ukończeniu tworzenia usługi Key Vault](./media/quickstarts/search-services.png)
2. W polu wyszukiwania wpisz **Key Vault**.
3. Na liście wyników wybierz pozycję **Key Vault**.
4. W sekcji Key Vault, wybierz przycisk **Utwórz**.
5. W sekcji **Tworzenie magazynu kluczy** podaj następujące informacje:
    - **Nazwa**: wymagana jest unikatowa nazwa. W tym przewodniku szybki start użyjemy **contoso-vault2**. 
    - **Subskrypcja**: wybierz subskrypcję.
    - W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nową** , a następnie wprowadź nazwę grupy zasobów.
    - W menu rozwijanym **Lokalizacja** wybierz lokalizację.
    - Dla pozostałych opcji zostaw wartości domyślne.
6. Po podaniu powyższych informacje wybierz przycisk **Utwórz**.

W tym momencie Twoje konto platformy Azure jest jedynym autoryzowanym dostępem do tego nowego magazynu.

![Dane wyjściowe po ukończeniu tworzenia usługi Key Vault](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Dodawanie wpisu tajnego do usługi Key Vault

Aby dodać wpis tajny do magazynu, wystarczy tylko wykonać kilka dodatkowych czynności. W takim przypadku dodamy komunikat, którego możemy użyć do przetestowania Key Vault pobierania. Wiadomość jest nazywana **komunikatem** i przechowujemy wartość **Hello z Key Vault** .

1. Na stronie właściwości Key Vault wybierz pozycję wpisy **tajne**.
1. Kliknij pozycję **Wygeneruj/zaimportuj**.
1. Na ekranie **Tworzenie wpisu tajnego** wybierz następujące wartości:
    - **Opcje przekazywania**: Ręcznie.
    - **Nazwa**: komunikat
    - **Wartość**: Hello z Key Vault
    - Dla pozostałych opcji zostaw wartości domyślne. Kliknij przycisk **Utwórz**.

## <a name="add-a-key-vault-reference-to-app-config"></a>Dodaj odwołanie Key Vault do konfiguracji aplikacji

1. Zaloguj się do [portalu Azure](https://portal.azure.com). Wybierz pozycję **wszystkie zasoby**, a następnie wybierz wystąpienie magazynu konfiguracji aplikacji utworzone w ramach przewodnika Szybki Start.

1. Kliknij pozycję **Eksplorator konfiguracji**

1. Kliknij pozycję **+ Utwórz**odwołanie do**magazynu kluczy**  >  i wybierz następujące wartości:
    - **Key**: TestApp: Settings: KeyVaultMessage
    - **Etykieta**: pozostaw pustą wartość
    - **Subskrypcja**, **Grupa zasobów**, **Magazyn kluczy**: Wybierz opcje odpowiadające Key Vault utworzonej w poprzedniej sekcji.
    - **Wpis tajny**: Wybierz wpis tajny o nazwie **Message** utworzony w poprzedniej sekcji.

## <a name="connect-to-key-vault"></a>Połącz z Key Vault

1. W tym samouczku zostanie użyta nazwa główna usługi do uwierzytelniania w magazynie kluczy. Aby utworzyć tę nazwę główną usługi, użyj interfejsu wiersza polecenia platformy Azure [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Ta operacja zwróci serię par klucz/wartość. 

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Uruchom następujące polecenie, aby umożliwić jednostce usługi dostęp do magazynu kluczy:

        az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey

1. Dodaj wpisy tajne dla *clientId* i *ClientSecret* do Menedżera kluczy tajnych. Te polecenia należy wykonać w tym samym katalogu, w którym znajduje się plik *. csproj* .

        dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>        
        dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>

> [!NOTE]
> Te poświadczenia Key Vault są używane tylko w aplikacji. Aplikacja uwierzytelnia się bezpośrednio do Key Vault przy użyciu tych poświadczeń. Nie są one nigdy przesyłane do usługi konfiguracji aplikacji.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Zaktualizuj kod, aby użyć odwołania Key Vault

1. Otwórz *program.cs*i Dodaj odwołania do wymaganych pakietów.

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Zaktualizuj metodę `CreateWebHostBuilder`, aby użyć konfiguracji aplikacji przez wywołanie metody `config.AddAzureAppConfiguration()`. Uwzględnij opcję `UseAzureKeyVault`, przekazując do Key Vault nowe odwołanie `KeyVaultClient`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                KeyVaultClient kvClient = new KeyVaultClient(async (authority, resource, scope) =>
                {
                    var adCredential = new ClientCredential(settings["ConnectionStrings:KeyVaultClientId"], settings["ConnectionStrings:KeyVaultClientSecret"]);
                    var authenticationContext = new AuthenticationContext(authority, null);
                    return (await authenticationContext.AcquireTokenAsync(resource, adCredential)).AccessToken;
                });

                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .UseAzureKeyVault(kvClient); });
            })
            .UseStartup<Startup>();
    ```

1. Po przekazaniu odwołania *KeyVaultClient* do metody `UseAzureKeyVault` podczas inicjowania połączenia z konfiguracją aplikacji można uzyskać dostęp do wartości Key Vault odwołań w taki sam sposób jak w przypadku dostępu do wartości zwykłych kluczy konfiguracji aplikacji. Aby wyświetlić ten proces w działaniu, Otwórz *index. cshtml* w widokach > katalog macierzysty. Zastąp jego zawartość następującym kodem:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Dostęp do wartości Key Vault Reference *TestApp: Settings: KeyVaultMessage* w taki sam sposób, jak wartość konfiguracji *TestApp: Settings: Message*

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Aby skompilować aplikację przy użyciu interfejs wiersza polecenia platformy .NET Core, uruchom następujące polecenie w powłoce poleceń:

        dotnet build

2. Po pomyślnym zakończeniu kompilacji Uruchom następujące polecenie, aby uruchomić aplikację sieci Web lokalnie:

        dotnet run

3. Otwórz okno przeglądarki i przejdź do `http://localhost:5000`, który jest domyślnym adresem URL aplikacji sieci Web hostowanej lokalnie.

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/key-vault-reference-launch-local.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano tożsamość usługi zarządzanej przez platformę Azure, aby usprawnić dostęp do usługi App Configuration i poprawić zarządzanie poświadczeniami w Twojej aplikacji. Aby dowiedzieć się więcej na temat korzystania z konfiguracji aplikacji, przejdź do przykładów interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [Przykłady interfejsu wiersza polecenia](./cli-samples.md)
