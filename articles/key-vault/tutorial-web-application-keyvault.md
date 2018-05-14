---
title: Konfigurowanie aplikacji sieci web platformy Azure można odczytać klucza tajnego z magazynu kluczy | Dokumentacja firmy Microsoft
description: Samouczek konfigurowania aplikacji ASP.Net core odczytu klucza tajnego z magazynu kluczy
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 04/16/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: b4e317a82b93513c6161d9da0c55883e99580cbb
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-configure-an-azure-web-application-to-read-a-secret-from-key-vault"></a>Samouczek: Konfigurowanie aplikacji sieci web platformy Azure można odczytać klucza tajnego z magazynu kluczy

W tym samouczku przejdź przez czynności niezbędnych do pobrania odczytać informacji z usługi Key vault za pomocą tożsamości zarządzanych usług aplikacji sieci web platformy Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie magazynu kluczy.
> * Przechowywanie klucza tajnego w magazynie kluczy.
> * Tworzenie aplikacji sieci Web platformy Azure.
> * Włącz zarządzane tożsamości usługi
> * Przydziel uprawnienia wymagane dla aplikacji, które można odczytać danych z magazynu kluczy.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

Aby zalogować się przy użyciu interfejsu wiersza polecenia platformy Azure, możesz wpisać:

```azurecli
az login
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli
az group create --name ContosoResourceGroup --location eastus
```

Właśnie utworzoną grupę zasobów jest używane w tym samouczku.

## <a name="create-an-azure-key-vault"></a>Tworzenie usługi Azure Key Vault

Następnie należy utworzyć magazyn kluczy w grupie zasobów, utworzony w poprzednim kroku. Należy podać niektóre informacje:

>[!NOTE]
> Mimo że "ContosoKeyVault" jest używana jako nazwa dla naszej usługi Key Vault w tym samouczku, należy użyć unikatowe nazwy.

* Nazwa magazynu **ContosoKeyVault**.
* Nazwa grupy zasobów: **ContosoResourceGroup**.
* Lokalizacja: **Wschodnie stany USA**.

```azurecli
az keyvault create --name '<YourKeyVaultName>' --resource-group ContosoResourceGroup --location eastus
```

Dane wyjściowe tego polecenia są wyświetlane właściwości nowo utworzonego magazynu kluczy. Zanotuj dwie poniższe właściwości:

* **Nazwa magazynu**: w tym przykładzie jest to **ContosoKeyVault**. Możesz użyć nazwy magazyn kluczy dla wszystkich poleceń usługi Key Vault.
* **Identyfikator URI magazynu**: W tym przykładzie jest to https://<YourKeyVaultName>.vault.azure.net/. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

>[!IMPORTANT]
> Jeśli błąd parametru "vault_name" musi być zgodna z następującego wzorca: "^ [-zA-Z0 - 9-]{3,24}$" param name wartość nie jest unikatowy lub nie jest zgodny ze ciąg składający się z znaków alfanumerycznych od 3 do 24 długo.

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania jakichkolwiek operacji na tym nowym magazynie.

## <a name="add-a-secret-to-key-vault"></a>Dodawanie klucza tajnego do magazynu kluczy

Dodajemy klucz tajny, aby zilustrować, jak to działa. Można przechowywać, parametry połączenia SQL lub inne informacje, które należy zachować bezpieczny, ale być dostępne dla aplikacji. W tym samouczku zostanie wywołana hasło **AppSecret** i zapisze wartość **MySecret** w nim.

Wpisz poniższe polecenia, można utworzyć klucza tajnego w magazynie kluczy o nazwie **AppSecret** który będzie przechowywać wartość **MySecret**:

```azurecli
az keyvault secret set --vault-name '<YourKeyVaultName>' --name 'AppSecret' --value 'MySecret'
```

Aby wyświetlić wartość zawartą we wpisie tajnym jako zwykły tekst:

```azurecli
az keyvault secret show --name 'AppSecret' --vault-name '<YourKeyVaultName>'
```

To polecenie przedstawia tajnych informacji, łącznie z identyfikatora URI. Po wykonaniu tych czynności powinien mieć identyfikatora URI do klucza tajnego w magazynie kluczy Azure. Zanotuj te informacje. Będzie potrzebny w kolejnym kroku.

## <a name="create-a-web-app"></a>Tworzenie aplikacji sieci Web

W tej sekcji Tworzenie aplikacji platformy ASP.NET MVC i wdrożyć go na platformie Azure jako aplikacji sieci Web. Aby uzyskać więcej informacji dotyczących aplikacji sieci Web platformy Azure, zobacz [Omówienie aplikacji sieci Web](../app-service/app-service-web-overview.md).

1. W programie Visual Studio utwórz nowy projekt, wybierając pozycję **Plik > Nowy > Projekt**. 

2. W oknie dialogowym **Nowy projekt** wybierz pozycję **Visual C# > Sieć Web > Aplikacja sieci Web platformy ASP.NET Core**.

3. Nazwa aplikacji **WebKeyVault**, a następnie wybierz **OK**.
   >[!IMPORTANT]
   > Nazwę aplikacji WebKeyVault, skopiuj i Wklej kod będzie odpowiada przestrzeni nazw. Jeśli nazwę lokacji, inaczej należy zmodyfikować kod odpowiadać nazwie witryny.

    ![Okno dialogowe Nowy projekt ASP.NET](media/tutorial-web-application-keyvault/aspnet-dialog.png)

4. Na platformie Azure można wdrożyć dowolny typ aplikacji internetowej ASP.NET Core. W tym samouczku, wybierz **aplikacji sieci Web** szablonu i upewnij się, że ma ustawioną wartość uwierzytelnianie **bez uwierzytelniania**.

    ![ASPNET okno dialogowe nie uwierzytelniania](media/tutorial-web-application-keyvault/aspnet-noauth.png)

5. Kliknij przycisk **OK**.

6. Po utworzeniu projektu ASP.NET Core jest wyświetlana strona powitalna platformy ASP.NET Core zawierająca wiele linków do zasobów ułatwiających rozpoczęcie pracy.

7. Z menu wybierz pozycję **Debuguj > Uruchom bez debugowania**, aby lokalnie uruchomić aplikację internetową.

## <a name="modify-the-web-app"></a>Modyfikowanie aplikacji sieci web

Istnieją dwa pakiety NuGet, których aplikacji sieci web musi mieć zainstalowany. Aby zainstalować je wykonaj następujące czynności:

1. W rozwiązaniu explorer kliknij prawym przyciskiem myszy nazwę witryny sieci Web.
2. Wybierz **pakiety zarządzania pakietami NuGet rozwiązania...**
3. Zaznacz pole wyboru obok pola wyszukiwania. **Uwzględnij wersję wstępną**
4. Wyszukaj dwa pakiety NuGet wymienionych poniżej i zaakceptować dla nich mają zostać dodane do rozwiązania:

    * [Microsoft.Azure.Services.AppAuthentication (wersja zapoznawcza)](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) — można łatwo pobrać tokeny dostępu dla usługi do usługi Azure Service scenariusze uwierzytelniania. 
    * [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/2.4.0-preview) — zawiera metody interakcji z magazynu kluczy.

5. Użyj Eksploratora rozwiązań, aby otworzyć `Program.cs` i Zastąp zawartość pliku Program.cs następującym kodem. SUBSTITUTE ```<YourKeyVaultName>``` o nazwie magazynu kluczy:

    ```csharp
    
    using Microsoft.AspNetCore;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureKeyVault;
    
        namespace WebKeyVault
        {
        public class Program
        {
        public static void Main(string[] args)
        {
        BuildWebHost(args).Run();
        }
    
            public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((ctx, builder) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        builder.AddAzureKeyVault(
                            keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                }
             )
                .UseStartup<Startup>()
                .Build();
    
            private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
        }
        }
    ```

6. Użyj Eksploratora rozwiązań, aby przejść do **stron** sekcji i Otwórz `About.cshtml`. Zastąp zawartość **About.cshtml.cs** przy użyciu poniższego kodu:

    ```csharp
    
    using Microsoft.AspNetCore.Mvc.RazorPages;
    using Microsoft.Extensions.Configuration;
    
    namespace WebKeyVault.Pages
    {
        public class AboutModel : PageModel
        {
            public AboutModel(IConfiguration configuration)
            {
                _configuration = configuration;
            }
    
            private readonly IConfiguration _configuration = null;
            public string Message { get; set; }
    
            public void OnGet()
            {
                Message = "My key val = " +  _configuration["AppSecret"];
            }
        }
    }
    
    ```

7. W menu głównym wybierz **debugowania** > **uruchomić bez debugowania**. Gdy pojawi się przeglądarkę, przejdź do **o** strony. Wartość AppSecret jest wyświetlany.

>[!IMPORTANT]
> Jeśli otrzymasz 502.5 błąd HTTP - komunikat o błędzie procesu Sprawdź nazwę magazynu kluczy określona w `Program.cs`

## <a name="publish-the-web-application-to-azure"></a>Publikowanie aplikacji sieci web na platformie Azure

1. Powyżej Edytor wybierz **WebKeyVault**.
2. Wybierz **publikowania**.
3. Wybierz **publikowania** ponownie.
4. Wybierz **utworzyć**.

>[!IMPORTANT]
> Zostanie otwarte okno przeglądarki i zostanie wyświetlony 502.5 - komunikat błędu procesu. Taki stan jest oczekiwany. Należy przyznać prawa tożsamości aplikacji na odczyt kluczy tajnych z magazynu kluczy.

## <a name="enable-managed-service-identity"></a>Włącz tożsamość usługi zarządzanej

Usługa Azure Key Vault zapewnia bezpieczne przechowywanie poświadczeń i innych kluczy i kluczy tajnych, ale kodu musi zostać uwierzytelniona Key Vault w celu ich pobrania. Tożsamość usługi zarządzanej (MSI) powoduje, że rozwiązania tego problemu prostszy, zapewniając usług Azure automatycznie zarządzane tożsamości w usłudze Azure Active Directory (Azure AD). Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, w tym usługi Key Vault bez żadnych poświadczeń w kodzie.

1. Wróć do interfejsu wiersza polecenia platformy Azure
2. Uruchom polecenie Przypisz tożsamości w celu utworzenia tożsamości dla tej aplikacji:

```azurecli
az webapp assign-identity --name WebKeyVault --resource-group ContosoResourcegroup
```

>[!NOTE]
>Jest to równoważne przechodzi do portalu i przełączania **tożsamość usługi zarządzanej** do **na** we właściwościach aplikacji sieci web.

## <a name="grant-rights-to-the-application-identity"></a>Udziel uprawnień do tożsamości aplikacji

Przy użyciu portalu Azure, przejdź do zasad dostępu do magazynu kluczy i udzielić sobie dostępu do funkcji zarządzania klucza tajnego w magazynie kluczy. Umożliwi to uruchamianie aplikacji na komputerze deweloperskim lokalnego.

1. Wyszukaj magazyn kluczy w **Wyszukaj zasoby** okno dialogowe w portalu Azure.
2. Wybierz **zasady dostępu**.
3. Wybierz **Dodaj nowy**w **tajny uprawnienia** sekcji wybierz **uzyskać** i **listy**.
4. Wybierz **wybierz główny**i Dodaj tożsamość aplikacji. Będzie mieć taką samą nazwę co aplikacja.
5. Wybierz **Ok**

Teraz konto Azure i tożsamość aplikacji ma uprawnienia do odczytu informacji z magazynu kluczy. Po odświeżeniu strony powinna zostać wyświetlona strona docelowa witryny. W przypadku wybrania **o**. Zostanie wyświetlony wartości przechowywane w magazynie kluczy.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć grupę zasobów i wszystkie jego zasoby, używać **usunięcie grupy az** polecenia.

  ```azurecli
  az group delete -n ContosoResourceGroup
  ```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przewodnik dewelopera usługi Azure Key Vault](key-vault-developers-guide.md)
