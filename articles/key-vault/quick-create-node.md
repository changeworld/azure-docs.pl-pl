---
title: Szybki Start — Ustawianie i pobieranie klucza tajnego z Azure Key Vault przy użyciu aplikacji sieci Web w węźle | Microsoft Docs
description: W tym przewodniku szybki start ustawisz i pobrano klucz tajny z Azure Key Vault przy użyciu aplikacji sieci Web Node
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: quickstart
ms.date: 09/03/2010
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 4bea46e62f90a41b566781457a39718849ee0e15
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259245"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-node-web-app"></a>Szybki start: Ustawianie i pobieranie wpisu tajnego z Azure Key Vault przy użyciu aplikacji sieci Web Node 

W tym przewodniku szybki start przedstawiono sposób przechowywania wpisu tajnego w Azure Key Vault i sposobu pobierania go przy użyciu aplikacji sieci Web. Usługa Key Vault pomaga zabezpieczać informacje. Aby wyświetlić wartość wpisu tajnego, należy uruchomić ten przewodnik Szybki Start na platformie Azure. Samouczek Szybki start używa środowiska Node.js i zarządzanych tożsamości na potrzeby zasobów platformy Azure. Omawiane kwestie:

* Tworzenie magazynu kluczy.
* Zapisywanie wpisu tajnego w magazynie kluczy.
* Pobieranie wpisu tajnego z magazynu kluczy.
* Tworzenie aplikacji internetowej platformy Azure.
* Włączanie [tożsamości zarządzanej](../active-directory/managed-service-identity/overview.md) dla aplikacji internetowej.
* Przyznawanie wymaganych uprawnień w celu umożliwienia aplikacji internetowej odczytu danych z magazynu kluczy.

Przed kontynuowaniem upewnij się, że znasz [podstawowe pojęcia dotyczące Key Vault](key-vault-whatis.md#basic-concepts).

> [!NOTE]
> Usługa Key Vault to centralne repozytorium do programistycznego przechowywania wpisów tajnych. W tym celu aplikacje i użytkownicy muszą najpierw uwierzytelnić się w usłudze Key Vault — czyli podać wpis tajny. Aby zastosować najlepsze rozwiązania dotyczące bezpieczeństwa, pierwszy wpis tajny musi być okresowo obracany. 
>
> Dzięki [tożsamościom usługi zarządzanej dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) aplikacje uruchamiane na platformie Azure uzyskują tożsamość, która jest automatycznie zarządzana przez tę platformę. Dzięki temu można rozwiązać *początkowy problem dotyczący wpisu tajnego*, gdzie użytkownicy i aplikacje mogą postępować zgodnie z najlepszymi wskazówkami i nie muszą pamiętać o obracaniu pierwszego wpisu tajnego.

## <a name="prerequisites"></a>Wymagania wstępne

* [Node.js](https://nodejs.org/en/)
* [Usługa Git](https://www.git-scm.com/)
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 lub nowszy. Ten przewodnik Szybki Start wymaga lokalnego uruchomienia interfejsu wiersza polecenia platformy Azure. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja wiersza polecenia lub jego uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](https://review.docs.microsoft.com/en-us/cli/azure/install-azure-cli?branch=master&view=azure-cli-latest).
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, wpisz:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Wybierz nazwę grupy zasobów i wypełnij symbol zastępczy.
Poniższy przykład tworzy grupę zasobów w lokalizacji Wschodnie stany USA.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

W tym artykule jest używana właśnie utworzona grupa zasobów.

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Następnie utworzysz Magazyn kluczy za pomocą grupy zasobów utworzonej w poprzednim kroku. Chociaż w tym artykule użyto nazwy "ContosoKeyVault", musisz użyć unikatowej nazwy. Podaj następujące informacje:

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

W tym projekcie są używane dwa moduły węzłów: [MS-REST-Azure](https://www.npmjs.com/package/ms-rest-azure) i [Azure-](https://www.npmjs.com/package/azure-keyvault)kluczy.

## <a name="publish-the-web-app-to-azure"></a>Publikowanie aplikacji internetowej na platformie Azure

Utwórz plan [Azure App Service](https://azure.microsoft.com/services/app-service/) . Ten plan pozwala na przechowywanie wielu aplikacji internetowych.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
Następnie Utwórz aplikację sieci Web. W poniższym przykładzie Zastąp `<app_name>` wartość globalnie unikatową nazwą aplikacji (prawidłowe znaki to a-z, 0-9 i-). Środowisko uruchomieniowe ma ustawioną wartość NODE|6.9. Aby wyświetlić wszystkie obsługiwane środowiska uruchomieniowe, `az webapp list-runtimes`Uruchom polecenie.

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
Przejdź do nowo utworzonej aplikacji sieci Web i sprawdź, czy działa. Zastąp `<app_name>` unikatową nazwą aplikacji.

    ```
    http://<app name>.azurewebsites.net
    ```
Powyższe polecenie tworzy również aplikację obsługującą git, która umożliwia wdrażanie na platformie Azure z lokalnego repozytorium git. Lokalne repozytorium git jest skonfigurowane przy użyciu tego adresu URL `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`:.

Po zakończeniu poprzedniego polecenia możesz dodać zdalne Azure do lokalnego repozytorium git. Zamień `<url>` na adres URL repozytorium git.

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

Zanotuj dane wyjściowe poprzedniego polecenia. Powinny one mieć następujący format:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Następnie uruchom następujące polecenie, używając nazwy magazynu kluczy i wartości **principalId**:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get set
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Wdróż aplikację węzła na platformie Azure i Pobierz wartość klucza tajnego

Uruchom następujące polecenie, aby wdrożyć aplikację na platformie Azure:

```
git push azure master
```

Po przejściu do `https://<app_name>.azurewebsites.net`sekcji można zobaczyć wartość klucza tajnego. Upewnij się, że nazwa `<YourKeyVaultName>` została zamieniona na nazwę magazynu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Key Vault i Zapisano w nim wpis tajny. Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem Azure Key Vault](key-vault-overview.md)
- Zobacz [przewodnik dewelopera Azure Key Vault](key-vault-developers-guide.md)
- Informacje o [kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-and-certificates.md)
- Przegląd [Azure Key Vault najlepszych](key-vault-best-practices.md) rozwiązań