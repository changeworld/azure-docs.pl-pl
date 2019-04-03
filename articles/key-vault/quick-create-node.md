---
title: Przewodnik Szybki Start — zestaw i pobierania klucza tajnego z usługi Azure Key Vault za pomocą aplikacji internetowej w języku Node | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki Start ustaw i pobierania klucza tajnego z usługi Azure Key Vault za pomocą aplikacji internetowej w języku Node
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.workload: identity
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: c7bf6a22da89f0bf1e3897ec8fc30238b86b7b75
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882754"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-node-web-app"></a>Szybki start: Ustawianie i pobieranie wpisu tajnego z usługi Azure Key Vault przy użyciu aplikacji sieci web Node 

Ten przewodnik Szybki Start pokazano, jak przechowywać klucz tajny w usłudze Azure Key Vault oraz jak pobierać je za pomocą aplikacji sieci web. Usługa Key Vault pomaga zabezpieczać informacje. Aby wyświetlić wartość wpisu tajnego, trzeba uruchamiać ten przewodnik Szybki Start na platformie Azure. Samouczek Szybki start używa środowiska Node.js i zarządzanych tożsamości na potrzeby zasobów platformy Azure. Omawiane kwestie:

* Tworzenie magazynu kluczy.
* Zapisywanie wpisu tajnego w magazynie kluczy.
* Pobieranie wpisu tajnego z magazynu kluczy.
* Tworzenie aplikacji internetowej platformy Azure.
* Włączanie [tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) dla aplikacji internetowej.
* Przyznawanie wymaganych uprawnień w celu umożliwienia aplikacji internetowej odczytu danych z magazynu kluczy.

Przed kontynuowaniem upewnij się, że znasz [podstawowe pojęcia usługi Key Vault](key-vault-whatis.md#basic-concepts).

> [!NOTE]
> Usługa Key Vault to centralne repozytorium do programistycznego przechowywania wpisów tajnych. W tym celu aplikacje i użytkownicy muszą najpierw uwierzytelnić się w usłudze Key Vault — czyli podać wpis tajny. Aby zastosować najlepsze rozwiązania dotyczące bezpieczeństwa, pierwszy wpis tajny musi być okresowo obracany. 
>
> Dzięki [tożsamościom usługi zarządzanej dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) aplikacje uruchamiane na platformie Azure uzyskują tożsamość, która jest automatycznie zarządzana przez tę platformę. Dzięki temu można rozwiązać *początkowy problem dotyczący wpisu tajnego*, gdzie użytkownicy i aplikacje mogą postępować zgodnie z najlepszymi wskazówkami i nie muszą pamiętać o obracaniu pierwszego wpisu tajnego.

## <a name="prerequisites"></a>Wymagania wstępne

* [Node.js](https://nodejs.org/en/)
* [Usługa Git](https://www.git-scm.com/)
* [Interfejs wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 lub nowszej. Ten przewodnik Szybki Start będzie wymagał interfejsu wiersza polecenia platformy Azure lokalnie. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja wiersza polecenia lub jego uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](https://review.docs.microsoft.com/en-us/cli/azure/install-azure-cli?branch=master&view=azure-cli-latest).
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, wpisz:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Wybierz nazwę grupy zasobów i wypełnij symbol zastępczy.
Poniższy przykład tworzy grupę zasobów w lokalizacji wschodnie stany USA.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

W tym artykule jest używana właśnie utworzona grupa zasobów.

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Następnie należy utworzyć magazyn kluczy przy użyciu grupy zasobów, który został utworzony w poprzednim kroku. Mimo że w tym artykule używa "ContosoKeyVault" jako nazwy, musisz Użyj unikatowej nazwy. Podaj następujące informacje:

* Nazwa magazynu kluczy.
* Nazwa grupy zasobów. nazwa musi być ciągiem od 3 do 24 znaków i może zawierać tylko znaki 0–9, a–z, A–Z i myślnik (-).
* Lokalizacja: **Wschodnie stany USA**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania jakichkolwiek operacji na tym nowym magazynie.

## <a name="add-a-secret-to-the-key-vault"></a>Dodawanie wpisu tajnego do magazynu kluczy

Dodajemy wpis tajny, aby zilustrować, jak to działa. Możesz zapisywać parametry połączenia SQL lub inne informacje, które muszą być przechowywane bezpiecznie, ale jednocześnie być dostępne dla aplikacji. W tym samouczku hasło będzie miało nazwę **AppSecret** i będzie w nim przechowywana wartość **MySecret**.

Wpisz następujące polecenia, aby utworzyć wpis tajny w magazynie kluczy o nazwie **AppSecret**. Ten wpis tajny będzie przechowywał wartość **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Aby wyświetlić wartość zawartą we wpisie tajnym jako zwykły tekst:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

To polecenie wyświetla informacje o wpisie tajnym, w tym identyfikator URI. Po wykonaniu tych kroków będziesz mieć identyfikator URI wpisu tajnego w magazynie kluczy. Zanotuj te informacje. Będą one potrzebne w kolejnym kroku.

## <a name="clone-the-repo"></a>Klonowanie repozytorium

Sklonuj repozytorium tak, aby utworzyć kopię lokalną, gdzie możesz edytować źródło. Uruchom następujące polecenie:

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Instalowanie zależności

Uruchom następujące polecenia, aby zainstalować zależności:

```
cd key-vault-node-quickstart
npm install
```

Ten projekt używa dwóch modułów węzła: [ms rest platformy azure](https://www.npmjs.com/package/ms-rest-azure) i [azure keyvault](https://www.npmjs.com/package/azure-keyvault).

## <a name="publish-the-web-app-to-azure"></a>Publikowanie aplikacji internetowej na platformie Azure

Tworzenie [usługi Azure App Service](https://azure.microsoft.com/services/app-service/) planu. Ten plan pozwala na przechowywanie wielu aplikacji internetowych.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
Następnie należy utworzyć aplikację sieci web. W poniższym przykładzie Zastąp `<app_name>` globalnie unikatową nazwą aplikacji (prawidłowe znaki to a-z, 0-9 i -). Środowisko uruchomieniowe ma ustawioną wartość NODE|6.9. Aby wyświetlić wszystkie obsługiwane środowiska uruchomieniowe, uruchom `az webapp list-runtimes`.

    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    ```
Po utworzeniu aplikacji internetowej w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone dane wyjściowe podobne do następujących:

    ```
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "<app_name>.azurewebsites.net",
      "enabled": true,
      "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
      < JSON data removed for brevity. >
    }
    ```
Przejdź do swojej nowo utworzoną aplikację internetową, a zobaczysz, że działa on. Zastąp `<app_name>` unikatową nazwą aplikacji.

    ```
    http://<app name>.azurewebsites.net
    ```
Poprzednie polecenie tworzy również włączone Git aplikacji, która umożliwia wdrażanie na platformie Azure ze swojego lokalnego repozytorium Git. Lokalne repozytorium Git jest skonfigurowana z tym adresem URL: https://<username>@ .git.scm.azurewebsites.net/ < nazwa_aplikacji > < nazwa_aplikacji >.

Po zakończeniu wprowadzania zmian poprzednim poleceniu, możesz Dodaj zdalną platformę Azure do lokalnego repozytorium Git. Zastąp `<url>` adres URL repozytorium Git.

    ```
    git remote add azure <url>
    ```

## <a name="enable-a-managed-identity-for-the-web-app"></a>Włączanie tożsamości zarządzanej dla aplikacji internetowej

Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń oraz innych kluczy i wpisów tajnych, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. [Tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) ułatwiają rozwiązanie tego problemu, udostępniając usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności przechowywania poświadczeń w kodzie.

Uruchom polecenie assign-identity w celu utworzenia tożsamości dla tej aplikacji:

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

To polecenie odpowiada przejściu do portalu i przełączeniu ustawienia **Przypisana tożsamość/system** na wartość **Włączone** we właściwościach aplikacji internetowej.

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Przypisywanie do aplikacji uprawnień odczytu wpisów tajnych z usługi Key Vault

Zanotuj danych wyjściowych poprzedniego polecenia. Powinny one mieć następujący format:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Następnie uruchom następujące polecenie przy użyciu nazwy magazynu kluczy i wartości **principalId**:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get set
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Wdrażanie aplikacji węzłów na platformie Azure i pobrać wartość wpisu tajnego

Uruchom następujące polecenie, aby wdrożyć aplikację na platformie Azure:

```
git push azure master
```

Później po przejściu do https://<app_name>.azurewebsites.net, zobaczysz wartość wpisu tajnego. Upewnij się, że został zastąpiony nazwą <YourKeyVaultName> nazwą magazynu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zestaw Azure SDK dla oprogramowania Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
