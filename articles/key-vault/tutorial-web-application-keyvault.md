---
title: Konfigurowanie aplikacji internetowej platformy Azure w celu odczytu wpisu tajnego z usługi Key Vault — samouczek | Microsoft Docs
description: Samouczek konfigurowania aplikacji platformy ASP.Net Core w celu odczytu wpisu tajnego z usługi Key Vault
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 05/17/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 91e2047998d6e743691821c631e15c94cd63cf15
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "41920093"
---
# <a name="tutorial-configure-an-azure-web-application-to-read-a-secret-from-key-vault"></a>Samouczek: konfigurowanie aplikacji internetowej platformy Azure w celu odczytu wpisu tajnego z usługi Key Vault

W tym samouczku wykonasz kroki niezbędne do skonfigurowania aplikacji internetowej platformy Azure w celu odczytu informacji z usługi Key Vault za pomocą tożsamości usługi zarządzanej. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie usługi Key Vault.
> * Przechowywanie wpisu tajnego w usłudze Key Vault.
> * Tworzenie aplikacji internetowej platformy Azure.
> * Włączanie tożsamości usługi zarządzanej
> * Przyznaj wymagane uprawnienia, aby umożliwić aplikacji odczyt danych z usługi Key Vault.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia, możesz wpisać:

```azurecli
az login
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *ContosoResourceGroup* w lokalizacji *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "ContosoResourceGroup" --location "East US"
```

Właśnie utworzona grupa zasobów jest używana w tym samouczku.

## <a name="create-an-azure-key-vault"></a>Tworzenie usługi Azure Key Vault

Następnie utworzysz usługę Key Vault w grupie zasobów utworzonej w poprzednim kroku. Chociaż w tym samouczku wartość „ContosoKeyVault” jest używana jako nazwa usługi Key Vault, musisz użyć unikatowej nazwy. Podaj następujące informacje:

* Nazwa magazynu: **ContosoKeyVault**.
* Nazwa grupy zasobów: **ContosoResourceGroup**.
* Lokalizacja: **Wschodnie stany USA**.

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East US"
```

Dane wyjściowe tego polecenia pokazują właściwości nowo utworzonej usługi Key Vault. Zanotuj dwie poniższe właściwości:

* **Nazwa magazynu**: w tym przykładzie jest to **ContosoKeyVault**. Nazwy swojej usługi Key Vault będziesz używać we wszystkich poleceniach usługi Key Vault.
* **Identyfikator URI magazynu**: w tym przykładzie jest to https://<YourKeyVaultName>.vault.azure.net/. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

>[!IMPORTANT]
> Jeśli wystąpi błąd Parametr „vault_name” musi być zgodny z następującym wzorcem: „^[a-zA-Z0-9-]{3,24}$”, wartość parametru -name nie jest unikatowa lub nie jest ciągiem składającym się ze znaków alfanumerycznych, o długości od 3 do 24 znaków.

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania jakichkolwiek operacji na tym nowym magazynie.

## <a name="add-a-secret-to-key-vault"></a>Dodawanie wpisu tajnego do usługi Key Vault

Dodajemy wpis tajny, aby zilustrować, jak to działa. Możesz zapisywać parametry połączenia SQL lub inne informacje, które muszą być przechowywane bezpiecznie, ale jednocześnie być dostępne dla aplikacji. W tym samouczku hasło będzie miało nazwę **AppSecret** i będzie w nim przechowywana wartość **MySecret**.

Wpisz poniższe polecenia, aby utworzyć wpis tajny w usłudze Key Vault o nazwie **AppSecret**, w którym będzie przechowywana wartość **MySecret**:

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "AppSecret" --value "MySecret"
```

Aby wyświetlić wartość zawartą we wpisie tajnym jako zwykły tekst:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "ContosoKeyVault"
```

To polecenie wyświetla informacje o wpisie tajnym, w tym identyfikator URI. Po wykonaniu tych kroków będziesz mieć identyfikator URI wpisu tajnego w usłudze Azure Key Vault. Zanotuj te informacje. Będą one potrzebne w kolejnym kroku.

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

W tej sekcji utworzysz aplikację platformy ASP.NET MVC i wdrożysz ją na platformie Azure jako aplikację internetową. Aby uzyskać więcej informacji o usłudze Azure Web Apps, zobacz [Przegląd usługi Web Apps](../app-service/app-service-web-overview.md).

1. W programie Visual Studio utwórz nowy projekt, wybierając pozycję **Plik > Nowy > Projekt**. 

2. W oknie dialogowym **Nowy projekt** wybierz pozycję **Visual C# &gt; Internet &gt; Aplikacja internetowa platformy ASP.NET Core**.

3. Nadaj aplikacji nazwę **WebKeyVault**, a następnie wybierz przycisk **OK**.
   >[!IMPORTANT]
   > Musisz nadać aplikacji nazwę WebKeyVault, aby kopiowany i wklejany kod był zgodny z przestrzenią nazw. Jeśli witryna została nazwana inaczej, należy zmodyfikować kod, aby był zgodny z nazwą witryny.

    ![Okno dialogowe Nowy projekt ASP.NET](media/tutorial-web-application-keyvault/aspnet-dialog.png)

4. Na platformie Azure można wdrożyć dowolny typ aplikacji internetowej ASP.NET Core. W tym przewodniku wybierz szablon **Aplikacja internetowa** i upewnij się, że uwierzytelnianie jest ustawione na wartość **Bez uwierzytelniania**.

    ![ASPNET — okno dialogowe bez uwierzytelniania](media/tutorial-web-application-keyvault/aspnet-noauth.png)

5. Kliknij przycisk **OK**.

6. Po utworzeniu projektu ASP.NET Core jest wyświetlana strona powitalna platformy ASP.NET Core zawierająca wiele linków do zasobów ułatwiających rozpoczęcie pracy.

7. Z menu wybierz pozycję **Debuguj > Uruchom bez debugowania**, aby lokalnie uruchomić aplikację internetową.

## <a name="modify-the-web-app"></a>Modyfikowanie aplikacji internetowej

Istnieją dwa pakiety NuGet, które Twoja aplikacja internetowa musi mieć zainstalowane. Aby je zainstalować, wykonaj poniższe kroki:

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy nazwę witryny internetowej.
2. Wybierz polecenie **Zarządzaj pakietami NuGet rozwiązania...**
3. Zaznacz pole wyboru obok pola wyszukiwania. **Uwzględnij wersję wstępną**
4. Wyszukaj dwa pakiety NuGet wymienione poniżej i zaakceptuj dodanie ich do Twojego rozwiązania:

    * [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) — ułatwia pobieranie tokenów dostępu w przypadku scenariuszy uwierzytelniania „usługa do usługi platformy Azure”. 
    * [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) — zawiera metody interakcji z usługą Key Vault.

5. Za pomocą Eksploratora rozwiązań otwórz plik `Program.cs` i zastąp jego zawartość następującym kodem. Zastąp zmienną ```<YourKeyVaultName>``` nazwą swojej usługi Key Vault:

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
            ).UseStartup<Startup>()
             .Build();

           private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
         }
    }
    ```

6. Za pomocą Eksploratora rozwiązań przejdź do sekcji **Strony** i otwórz plik `About.cshtml`. Zastąp zawartość pliku **About.cshtml.cs** poniższym kodem:

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

7. W menu głównym wybierz pozycję **Debuguj** > **Uruchom bez debugowania**. Gdy pojawi się przeglądarka, przejdź do strony **Informacje**. Wyświetlana jest wartość wpisu AppSecret.

>[!IMPORTANT]
> Jeśli zostanie wyświetlony komunikat o błędzie protokołu HTTP 502.5 — niepowodzenie procesu,
> > sprawdź nazwę magazynu Key Vault określoną w elemencie `Program.cs`

## <a name="publish-the-web-application-to-azure"></a>Publikowanie aplikacji internetowej na platformie Azure

1. Powyżej edytora wybierz pozycję **WebKeyVault**.
2. Wybierz pozycję **Publikowanie**, a następnie **Uruchom**.
3. Utwórz nową usługę **App Service** i wybierz pozycję **Opublikuj**.
4. Wybierz pozycję **Utwórz**.

>[!IMPORTANT]
> Zostanie otwarte okno przeglądarki i wyświetlony komunikat 502.5 — błąd procesu. Jest to oczekiwane. Należy przyznać tożsamości aplikacji prawo odczytu wpisów tajnych z usługi Key Vault.

## <a name="enable-managed-service-identity"></a>Włączanie tożsamości usługi zarządzanej

Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń oraz innych kluczy i wpisów tajnych, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. Tożsamość usługi zarządzanej (MSI) ułatwia rozwiązywanie tego problemu, udostępniając usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności przechowywania poświadczeń w kodzie.

1. Powrót do interfejsu wiersza polecenia platformy Azure
2. Uruchom polecenie assign-identity w celu utworzenia tożsamości dla tej aplikacji:

```azurecli
az webapp identity assign --name "WebKeyVault" --resource-group "ContosoResourcegroup"
```

>[!NOTE]
>To polecenie odpowiada przejściu do portalu i przełączeniu ustawienia **Tożsamość usługi zarządzanej** na wartość **Włączone** we właściwościach aplikacji internetowej.

## <a name="grant-rights-to-the-application-identity"></a>Przyznawanie uprawnień tożsamości aplikacji

Przy użyciu witryny Azure Portal przejdź do zasad dostępu usługi Key Vault i przyznaj sobie dostęp do zarządzania wpisem tajnym w usłudze Key Vault. Umożliwi to uruchamianie aplikacji na lokalnym komputerze deweloperskim.

1. Wyszukaj swoją usługę Key Vault w oknie dialogowym **Wyszukiwanie zasobów** w witrynie Azure Portal.
2. Wybierz pozycję **Zasady dostępu**.
3. Wybierz pozycję **Dodaj nowe** i w sekcji **Uprawnienia wpisu tajnego** wybierz opcje **Pobierz** i **Lista**.
4. Wybierz pozycję **Wybierz podmiot zabezpieczeń**, a następnie dodaj tożsamość aplikacji. Będzie mieć ona taką samą nazwę jak aplikacja.
5. Wybierz pozycję **OK**.

Teraz Twoje konto na platformie Azure oraz tożsamość aplikacji mają uprawnienia do odczytu informacji z usługi Key Vault. Po odświeżeniu strony powinna zostać wyświetlona strona docelowa witryny. Jeśli wybierzesz pozycję **Informacje**, zobaczysz wartości przechowywane w usłudze Key Vault.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć grupę zasobów i wszystkie jej zasoby, użyj polecenia **az group delete**.

  ```azurecli
  az group delete -n "ContosoResourceGroup"
  ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przewodnik dewelopera usługi Azure Key Vault](key-vault-developers-guide.md)