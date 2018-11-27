---
title: Samouczek — korzystanie z usługi Azure Key Vault za pomocą aplikacji internetowej platformy Azure na platformie .NET | Microsoft Docs
description: Samouczek — konfigurowanie aplikacji platformy ASP.NET Core w celu odczytu wpisu tajnego z usługi Key Vault
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
ms.openlocfilehash: defe1a109381c7ee44c6fc5e5db4c6f6ecc5ac6f
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706844"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Samouczek: Korzystanie z usługi Azure Key Vault za pomocą aplikacji internetowej platformy Azure na platformie .NET

Usługa Azure Key Vault umożliwia ochronę wpisów tajnych, takich jak klucze interfejsu API i parametry połączenia bazy danych. Umożliwia dostęp do aplikacji, usług i zasobów informatycznych.

W tym samouczku dowiesz się, jak utworzyć aplikację internetową platformy Azure, która umożliwia odczytanie informacji z usługi Azure Key Vault. W tym procesie używane są tożsamości zarządzane dla zasobów platformy Azure. Aby uzyskać więcej informacji na temat aplikacji internetowych platformy Azure, zobacz [Azure Web Apps](../app-service/app-service-web-overview.md).

Ten artykuł zawiera omówienie następujących czynności:

> [!div class="checklist"]
> * Tworzenie magazynu kluczy.
> * Zapisywanie wpisu tajnego w magazynie kluczy.
> * Pobieranie wpisu tajnego z magazynu kluczy.
> * Tworzenie aplikacji internetowej platformy Azure.
> * Włączanie [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla aplikacji internetowej.
> * Przyznawanie wymaganych uprawnień w celu umożliwienia aplikacji internetowej odczytu danych z magazynu kluczy.
> * Uruchamianie aplikacji internetowej na platformie Azure.

Zanim przejdziesz dalej, zapoznaj się z [podstawowymi pojęciami dotyczącymi usługi Key Vault](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Wymagania wstępne

* W systemie Windows:
  * [Zestaw .NET Core 2.1 SDK lub nowszy](https://www.microsoft.com/net/download/windows)

* Na komputerze Mac:
  * [program Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/)

* Wszystkie platformy:
  * [Usługa Git](https://git-scm.com/downloads)
  * Subskrypcja platformy Azure <br />(Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
  * [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) w wersji 2.0.4 lub nowszej, dostępny dla systemów Windows, Mac i Linux
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="managed-service-identity-and-how-it-works"></a>Tożsamość usługi zarządzanej i jej działanie

Usługa Azure Key Vault służy do bezpiecznego przechowywania poświadczeń, tak aby nie były zawarte w kodzie. Aby jednak pobrać klucze, musisz uwierzytelnić się w usłudze Azure Key Vault. W tym celu potrzebujesz poświadczenia. To klasyczny przykład dylematu dotyczącego uruchamiania. Tożsamość usługi zarządzanej rozwiązuje ten problem poprzez zapewnienie _tożsamości uruchamiania_, która upraszcza ten proces.

Po włączeniu tożsamości usługi zarządzanej dla usługi platformy Azure (na przykład Virtual Machines, App Service lub Functions) platforma Azure tworzy [jednostkę usługi](key-vault-whatis.md#basic-concepts). Tożsamość usługi zarządzanej wykonuje tę czynność dla wystąpienia tej usługi w usłudze Azure Active Directory (Azure AD) i wprowadza poświadczenia dla jednostki usługi w tym wystąpieniu.

![Diagram dotyczący tożsamości usługi zarządzanej](media/MSI.png)

Następnie Twój kod wywołuje lokalną usługę metadanych dostępną w zasobie platformy Azure, aby uzyskać token dostępu. W celu uwierzytelniania w usłudze Azure Key Vault kod używa tokenu dostępu otrzymanego z lokalnego elementu MSI_ENDPOINT.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, wpisz:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

1. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create).
1. Wybierz nazwę grupy zasobów i wypełnij symbol zastępczy. Poniższy przykład obejmuje tworzenie grupy zasobów w lokalizacji Zachodnie stany USA:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Możesz używać tej grupy zasobów w całym samouczku.

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Aby utworzyć magazyn kluczy w grupie zasobów, należy podać następujące informacje:

* Nazwa magazynu kluczy: ciąg składający się z od 3 do 24 znaków, zawierający tylko cyfry, litery i łączniki (na przykład: 0–9, a–z, A–Z oraz „-” )
* Nazwa grupy zasobów
* Lokalizacja: **Zachodnie stany USA**

Wprowadź następujące polecenie w interfejsie wiersza polecenia platformy Azure:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania jakichkolwiek operacji na tym nowym magazynie.

## <a name="add-a-secret-to-the-key-vault"></a>Dodawanie wpisu tajnego do magazynu kluczy

Teraz możesz dodać wpis tajny. Mogą to być parametry połączenia SQL lub inne informacje, które muszą być przechowywane bezpiecznie, ale jednocześnie muszą być dostępne dla aplikacji.

Wpisz następujące polecenie, aby utworzyć wpis tajny w magazynie kluczy o nazwie **AppSecret**. Ten wpis tajny zawiera wartość **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Aby wyświetlić wartość zawartą we wpisie tajnym jako zwykły tekst, wpisz następujące polecenie:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

To polecenie wyświetla informacje o wpisie tajnym, w tym identyfikator URI. Po wykonaniu tych kroków będziesz mieć identyfikator URI wpisu tajnego w magazynie kluczy. Zanotuj te informacje. Będą one potrzebne w kolejnym kroku.

## <a name="create-a-net-core-web-app"></a>Tworzenie nowej aplikacji internetowej platformy .NET Core

Postępuj zgodnie z tym [samouczkiem](../app-service/app-service-web-get-started-dotnet.md), aby utworzyć aplikację internetową platformy .NET Core i **opublikować** ją na platformie Azure. Możesz też obejrzeć poniższe wideo:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Otwieranie i edytowanie rozwiązania

1. Przejdź kolejno do pliku **Strony** > **About.cshtml.cs**.
2. Zainstaluj następujące pakiety NuGet:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. Zaimportuj następujący kod w pliku About.cshtml.cs:

   ```
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

4. Twój kod w klasie AboutModel powinien wyglądać tak jak poniżej:

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

1. Z menu głównego programu Visual Studio 2017 wybierz kolejno pozycje **Debuguj** > **Uruchom** z debugowaniem lub bez debugowania. 
1. Gdy pojawi się przeglądarka, przejdź do strony **Informacje**.
1. Wyświetlona zostanie wartość wpisu **AppSecret**.

## <a name="enable-a-managed-identity-for-the-web-app"></a>Włączanie tożsamości zarządzanej dla aplikacji internetowej

Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń oraz innych wpisów tajnych, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. [Tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) pomagają rozwiązać ten problem, udostępniając usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD. Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności przechowywania poświadczeń w kodzie.

1. W interfejsie wiersza polecenia platformy Azure uruchom polecenie assign-identity w celu utworzenia tożsamości dla tej aplikacji:

   ```azurecli

   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"

   ```

   >[!NOTE]
   >Zastąp ciąg \<YourAppName\> nazwą opublikowanej aplikacji na platformie Azure. Jeśli na przykład nazwa Twojej opublikowanej aplikacji to **MyAwesomeapp.azurewebsites.net**, zastąp ciąg \<YourAppName\> ciągiem **MyAwesomeapp**.

1. Zapisz identyfikator `PrincipalId` po opublikowaniu aplikacji na platformie Azure. Dane wyjściowe polecenia w kroku 1 powinny być w następującym formacie:

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

Zastąp ciąg \<YourKeyVaultName\> nazwą magazynu kluczy, a identyfikator \<PrincipalId\> wartością identyfikatora **PrincipalId** w następującym poleceniu:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

To polecenie przyznaje tożsamości usługi App Service (tożsamości usługi zarządzanej) uprawnienia do wykonywania operacji **get** i **list** w usłudze Key Vault.

## <a name="publish-the-web-application-to-azure"></a>Publikowanie aplikacji internetowej na platformie Azure

Opublikuj ponownie tę aplikację internetową na platformie Azure, aby sprawdzić, czy aplikacja internetowa działająca na żywo umożliwia pobranie wartości wpisu tajnego.

1. W programie Visual Studio wybierz projekt **key-vault-dotnet-core-quickstart**.
2. Wybierz pozycje **Publikuj** > **Uruchom**.
3. Wybierz pozycję **Utwórz**.

Gdy uruchomisz aplikację, zobaczysz, że może ona pobrać wartość wpisu tajnego.

Na platformie .NET została utworzona aplikacja internetowa, która przechowuje wpisy tajne w usłudze Key Vault i pobiera je z tej usługi.

## <a name="next-steps"></a>Następne kroki

>[!div class="nextstepaction"]
>[Przewodnik dewelopera usługi Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-developers-guide)
