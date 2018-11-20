---
title: Samouczek — jak używać usługi Azure Key Vault za pomocą usługi Azure Web App na platformie .NET | Microsoft Docs
description: Samouczek konfigurowania aplikacji platformy ASP.NET Core w celu odczytu wpisu tajnego z usługi Key Vault
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 6e30a181bc69678051e6d6469a2f1c533de811df
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686215"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-web-app-in-net"></a>Samouczek: jak używać usługi Azure Key Vault za pomocą usługi Azure Web App na platformie .NET

Usługa Azure Key Vault umożliwia ochronę wpisów tajnych, takich jak klucze interfejsu API, parametry połączenia bazy danych potrzebne do uzyskania dostępu do aplikacji, usługi i zasoby informatyczne.

W tym samouczku wykonasz kroki niezbędne do skonfigurowania aplikacji internetowej platformy Azure pod kątem odczytu informacji z usługi Azure Key Vault za pomocą tożsamości zarządzanych dla zasobów platformy Azure. Ten samouczek opiera się na usłudze [Azure Web Apps](../app-service/app-service-web-overview.md). Z poniższego artykułu dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie magazynu kluczy.
> * Zapisywanie wpisu tajnego w magazynie kluczy.
> * Pobieranie wpisu tajnego z magazynu kluczy.
> * Tworzenie aplikacji internetowej platformy Azure.
> * Włączanie [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla aplikacji internetowej.
> * Przyznawanie wymaganych uprawnień w celu umożliwienia aplikacji internetowej odczytu danych z magazynu kluczy.
> * Uruchamianie aplikacji internetowej na platformie Azure

Zanim przejdziesz dalej, zapoznaj się z [podstawowymi pojęciami](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Wymagania wstępne

* W systemie Windows:
  * [Zestaw .NET Core 2.1 SDK lub nowszy](https://www.microsoft.com/net/download/windows)

* Na komputerze Mac:
  * Zobacz [Co nowego w programie Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).

* Wszystkie platformy:
  * Git ([pobierz](https://git-scm.com/downloads)).
  * Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
  * [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) w wersji 2.0.4 lub nowszej. Jest on dostępny w systemach Windows, Mac i Linux.
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>Co to jest tożsamość usługi zarządzanej i jak działa?
Zanim przejdziemy dalej, omówmy tożsamość usługi zarządzanej. Usługa Azure Key Vault może bezpiecznie przechowywać poświadczenia, więc nie znajdują się w Twoim kodzie, ale w celu ich pobrania należy uwierzytelnić się w usłudze Azure Key Vault. Aby uwierzytelniać się w usłudze Key Vault, potrzebne są poświadczenia. Jest to klasyczny problem dotyczący uruchamiania. Dzięki magii platformy Azure i usługi Azure AD, tożsamość usługi zarządzanej udostępnia „tożsamość uruchamiania”, która ułatwia rozpoczęcie wykonywania czynności.

Oto jak to działa. Po włączeniu tożsamości usługi zarządzanej dla wybranej usługi platformy Azure, takiej jak Virtual Machines, App Service lub Functions, platforma Azure tworzy [jednostkę usługi](key-vault-whatis.md#basic-concepts) dla wystąpienia usługi w usłudze Azure Active Directory i wprowadza poświadczenia dla jednostki usługi w tym wystąpieniu usługi. 

![Tożsamość usługi zarządzanej](media/MSI.png)

Następnie Twój kod wywołuje lokalną usługę metadanych dostępną w zasobie platformy Azure, aby uzyskać tokenu dostępu.
W celu uwierzytelniania w usłudze Azure Key Vault kod używa tokenu dostępu otrzymanego z lokalnego elementu MSI_ENDPOINT. 

Rozpocznijmy teraz samouczek.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, wpisz:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Wybierz nazwę grupy zasobów i wypełnij symbol zastępczy.
Poniższy przykład obejmuje tworzenie grupy zasobów w lokalizacji Zachodnie stany USA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

W tym artykule jest używana właśnie utworzona grupa zasobów.

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Następnie utworzysz magazyn kluczy w grupie zasobów utworzonej w poprzednim kroku. Podaj następujące informacje:

* Nazwa magazynu kluczy: nazwa musi być ciągiem od 3 do 24 znaków i może zawierać tylko znaki (0-9, a – z, A-Z, i -).
* Nazwa grupy zasobów.
* Lokalizacja: **Zachodnie stany USA**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania jakichkolwiek operacji na tym nowym magazynie.

## <a name="add-a-secret-to-the-key-vault"></a>Dodawanie wpisu tajnego do magazynu kluczy

Dodajemy wpis tajny, aby zilustrować, jak to działa. Możesz zapisywać parametry połączenia SQL lub inne informacje, które muszą być przechowywane bezpiecznie, ale jednocześnie być dostępne dla aplikacji.

Wpisz następujące polecenia, aby utworzyć wpis tajny w magazynie kluczy o nazwie **AppSecret**. Ten wpis tajny będzie przechowywał wartość **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Aby wyświetlić wartość zawartą we wpisie tajnym jako zwykły tekst:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

To polecenie wyświetla informacje o wpisie tajnym, w tym identyfikator URI. Po wykonaniu tych kroków będziesz mieć identyfikator URI wpisu tajnego w magazynie kluczy. Zanotuj te informacje. Będą one potrzebne w kolejnym kroku.

## <a name="create-a-net-core-web-app"></a>Tworzenie nowej aplikacji internetowej platformy .NET Core

Postępuj zgodnie z tym [samouczkiem](../app-service/app-service-web-get-started-dotnet.md), aby utworzyć aplikację internetową platformy .NET Core i **opublikować** ją na platformie Azure, **albo** obejrzyj poniższe wideo
> [!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Otwieranie i edytowanie rozwiązania

1. Przejdź do pliku Strony > About.cshtml.cs.
2. Zainstaluj te 2 pakiety NugGet
    - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
    - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. Zaimportuj następujący kod do pliku About.cshtml.cs

    ```
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
    ```
4. Twój kod w klasie AboutModel powinien wyglądać tak jak poniżej
    ```
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The below 4 lines of code shows you how to use AppAuthentication library to fetch secrets from your Key Vault*/
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;             

                /* The below do while logic is to handle throttling errors thrown by Azure Key Vault. It shows how to do exponential backoff which is the recommended client side throttling*/
                do
                {
                    long waitTime = Math.Min(getWaitTime(retries), 2000000);
                    secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                    retry = false;
                } 
                while(retry && (retries++ < 10));
            }
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
                if((int)keyVaultException.Response.StatusCode == 429)
                    retry = true;
            }            
        }

        // This method implements exponential backoff incase of 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```


## <a name="run-the-app"></a>Uruchamianie aplikacji

Z menu głównego programu Visual Studio 2017 wybierz kolejno pozycje **Debuguj** > **Uruchom** z debugowaniem/bez debugowania. Gdy pojawi się przeglądarka, przejdź do strony **Informacje**. Wyświetlona zostanie wartość wpisu **AppSecret**.

## <a name="enable-a-managed-identity-for-the-web-app"></a>Włączanie tożsamości zarządzanej dla aplikacji internetowej

Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń oraz innych kluczy i wpisów tajnych, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. [Tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) ułatwiają rozwiązanie tego problemu, udostępniając usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności przechowywania poświadczeń w kodzie.

1. Powrót do interfejsu wiersza polecenia platformy Azure.
2. Uruchom polecenie assign-identity w celu utworzenia tożsamości dla tej aplikacji: 

   ```azurecli
   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
   ```
   Zwróć uwagę, że musisz zamienić nazwę <YourAppName> na nazwę opublikowanej aplikacji na platformie Azure, tzn.jeśli nazwa opublikowanej aplikacji to MyAwesomeapp.azurewebsites.net, zamień nazwę <YourAppName> na MyAwesomeapp
 
 Dane wyjściowe powyższego polecenia wyglądają następująco. Podczas publikowania aplikacji na platformie Azure zanotuj identyfikator PrincipalId. Powinny one mieć następujący format:
   ```
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
  ```
>[!NOTE]
>Polecenie w tej procedurze odpowiada przejściu do [portalu](https://portal.azure.com) i przełączeniu ustawienia **Tożsamość/Przypisana przez system** na wartość **Włączone** we właściwościach aplikacji internetowej.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Przypisywanie do aplikacji uprawnień odczytu wpisów tajnych z usługi Key Vault
        
Następnie uruchom to polecenie, używając nazwy magazynu kluczy i wartości **PrincipalId**:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

## <a name="publish-the-web-application-to-azure"></a>Publikowanie aplikacji internetowej na platformie Azure

Opublikuj ponownie tę aplikację na platformie Azure, aby zobaczyć, jak działa na żywo jako aplikacja internetowa, oraz aby sprawdzić, czy możesz pobrać wartość wpisu tajnego.

1. W programie Visual Studio wybierz projekt **key-vault-dotnet-core-quickstart**.
2. Wybierz pozycje **Publikuj** > **Uruchom**.
3. Wybierz pozycję **Utwórz**.

W powyższym poleceniu określasz tożsamość (tożsamość usługi zarządzanej) uprawnień usługi App Service w celu wykonania operacji **get** i **list** dla usługi Key Vault. <br />
Teraz, po uruchomieniu aplikacji, powinna pojawić się pobrana wartość wpisu tajnego. 

I to wszystko. Na platformie .NET została utworzona aplikacja internetowa, która przechowuje oraz pobiera jej wpisy tajne z usługi Key Vault.
