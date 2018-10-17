---
title: 'Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu aplikacji internetowej Node | Microsoft Docs'
description: 'Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu aplikacji internetowej Node'
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
ms.openlocfilehash: 860294ebc7fbadd3eeefc4298ec740ca7f704587
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714398"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-a-node-web-app"></a>Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu aplikacji internetowej Node 

W tym przewodniku Szybki start przedstawiono sposób przechowywania wpisu tajnego w usłudze Key Vault oraz pobierania go przy użyciu aplikacji internetowej. Aby wyświetlić wartość wpisu tajnego, musisz uruchomić go na platformie Azure. Samouczek Szybki start używa środowiska Node.js i zarządzanych tożsamości na potrzeby zasobów platformy Azure.

> [!div class="checklist"]
> * Tworzenie usługi Key Vault.
> * Przechowywanie wpisu tajnego w usłudze Key Vault.
> * Pobieranie wpisu tajnego z usługi Key Vault.
> * Tworzenie aplikacji internetowej platformy Azure.
> * Włączanie [tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) dla aplikacji internetowej.
> * Przyznawanie wymaganych uprawnień w celu umożliwienia aplikacji internetowej odczytu danych z usługi Key Vault.

Przed kontynuowaniem upewnij się, że znasz [podstawowe pojęcia](key-vault-whatis.md#basic-concepts).

>[!NOTE]
Aby przekonać się, dlaczego poniższy samouczek jest najlepszym rozwiązaniem, najpierw należy zapoznać się z kilkoma pojęciami. Usługa Key Vault to centralne repozytorium do programistycznego przechowywania wpisów tajnych. W tym celu aplikacje i użytkownicy muszą najpierw uwierzytelnić się w usłudze Key Vault, tj. podać wpis tajny. Zgodnie z najlepszymi rozwiązaniami dotyczącymi bezpieczeństwa pierwszy wpis tajny musi być okresowo obracany. Jednak dzięki [tożsamościom zarządzanym dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) aplikacje uruchamiane na platformie Azure otrzymują tożsamość, która jest automatycznie zarządzana przez tę platformę. Dzięki temu można rozwiązać **początkowy problem dotyczący wpisu tajnego**, gdzie użytkownicy aplikacji mogą postępować zgodnie z najlepszymi wskazówkami i nie muszą pamiętać o obracaniu pierwszego wpisu tajnego

## <a name="prerequisites"></a>Wymagania wstępne

* [Node JS](https://nodejs.org/en/)
* [Usługa Git](https://www.git-scm.com/)
* [Interfejs wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) w wersji 2.0.4 lub nowszej
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="login-to-azure"></a>Logowanie na platformie Azure

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia, możesz wpisać:

```azurecli
az login
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Wybierz nazwę grupy zasobów i wypełnij symbol zastępczy.
Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *<YourResourceGroupName>* w lokalizacji *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

Właśnie utworzona grupa zasobów jest używana w tym samouczku.

## <a name="create-an-azure-key-vault"></a>Tworzenie usługi Azure Key Vault

Następnie utworzysz magazyn Key Vault w grupie zasobów utworzonej w poprzednim kroku. Chociaż w tym artykule wartość „ContosoKeyVault” jest używana jako nazwa magazynu Key Vault, musisz użyć unikatowej nazwy. Podaj następujące informacje:

* Nazwa magazynu — **wybierz tutaj nazwę magazynu Key Vault**.
* Nazwa grupy zasobów — **wybierz tutaj nazwę grupy zasobów**.
* Lokalizacja — **Wschodnie stany USA**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania jakichkolwiek operacji na tym nowym magazynie.

## <a name="add-a-secret-to-key-vault"></a>Dodawanie wpisu tajnego do usługi Key Vault

Dodajemy wpis tajny, aby zilustrować, jak to działa. Możesz zapisywać parametry połączenia SQL lub inne informacje, które muszą być przechowywane bezpiecznie, ale jednocześnie być dostępne dla aplikacji. W tym samouczku hasło będzie miało nazwę **AppSecret** i będzie w nim przechowywana wartość **MySecret**.

Wpisz poniższe polecenia, aby utworzyć wpis tajny w usłudze Key Vault o nazwie **AppSecret**, w którym będzie przechowywana wartość **MySecret**:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Aby wyświetlić wartość zawartą we wpisie tajnym jako zwykły tekst:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

To polecenie wyświetla informacje o wpisie tajnym, w tym identyfikator URI. Po wykonaniu tych kroków będziesz mieć identyfikator URI wpisu tajnego w usłudze Azure Key Vault. Zanotuj te informacje. Będą one potrzebne w kolejnym kroku.

## <a name="clone-the-repo"></a>Klonowanie repozytorium

Sklonuj repozytorium, aby utworzyć kopię lokalną umożliwiającą edytowanie źródła, przez uruchomienie następującego polecenia:

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Instalowanie zależności

W tym miejscu zainstalujemy zależności. Uruchom następujące polecenia cd key-vault-node-quickstart npm install

Ten projekt używa 2 modułów platformy node:

* [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) 
* [azure-keyvault](https://www.npmjs.com/package/azure-keyvault)

## <a name="publish-the-web-application-to-azure"></a>Publikowanie aplikacji internetowej na platformie Azure

Poniżej przedstawiono kilka wymaganych kroków

- Pierwszy krok to utworzenie planu usługi [Azure App Service](https://azure.microsoft.com/services/app-service/). Ten plan pozwala na przechowywanie wielu aplikacji internetowych.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
- Następnie utworzymy aplikację internetową. W poniższym przykładzie zastąp ciąg <nazwa_aplikacji> globalnie unikatową nazwą aplikacji (prawidłowe znaki to a–z, 0–9 i -). Środowisko uruchomieniowe ma ustawioną wartość NODE|6.9. Aby wyświetlić wszystkie obsługiwane środowiska uruchomieniowe, uruchom polecenie az webapp list-runtimes
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
    Przejdź do nowo utworzonej aplikacji internetowej — powinna zostać wyświetlona działająca aplikacja internetowa. Zastąp ciąg <nazwa aplikacji> unikatową nazwą aplikacji.

    ```
    http://<app name>.azurewebsites.net
    ```
    Powyższe polecenie tworzy również aplikację z możliwością obsługi usługi Git, co pozwala na wdrażanie na platformie Azure z lokalnego repozytorium Git. 
    Lokalne repozytorium Git jest konfigurowane z adresem URL „https://<username>@<nazwa_aplikacji>.scm.azurewebsites.net/<nazwa_aplikacji>.git”

- Tworzenie użytkownika wdrożenia Po zakończeniu działania poprzedniego polecenia można dodać zdalne środowisko platformy Azure do lokalnego repozytorium Git. Zastąp element <url> adresem URL zdalnego repozytorium Git uzyskanego po włączeniu usługi dla aplikacji.

    ```
    git remote add azure <url>
    ```

## <a name="enable-a-managed-identity-for-the-web-app"></a>Włączanie tożsamości zarządzanej dla aplikacji internetowej

Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń oraz innych kluczy i wpisów tajnych, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. [Omówienie tożsamości zarządzanych dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) ułatwia rozwiązywanie tego problemu, udostępniając usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności przechowywania poświadczeń w kodzie.

Uruchom polecenie assign-identity w celu utworzenia tożsamości dla tej aplikacji:

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

To polecenie odpowiada przejściu do portalu i przełączeniu ustawienia **Przypisana tożsamość/system** na wartość **Włączone** we właściwościach aplikacji internetowej.

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Przypisywanie do aplikacji uprawnień odczytu wpisów tajnych z usługi Key Vault

Zapisz lub skopiuj dane wyjściowe powyższego polecenia. Powinny one mieć następujący format:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Następnie uruchom to polecenie, używając nazwy magazynu Key Vault i wartości PrincipalId skopiowanej z powyższego kodu:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Wdrażanie aplikacji Node na platformie Azure i pobieranie wartości wpisu tajnego

Teraz wszystko jest gotowe. Uruchom następujące polecenie, aby wdrożyć aplikację na platformie Azure

```
git push azure master
```

Po wykonaniu tej czynności podczas przeglądania witryny https://<nazwa_aplikacji>.azurewebsites.net zobaczysz wartość wpisu tajnego.
Upewnij się, że nazwa <YourKeyVaultName> została zastąpiona nazwą magazynu

## <a name="next-steps"></a>Następne kroki

* [Strona główna usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Dokumentacja usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
* [Azure SDK For Node (Zestaw Azure SDK dla platformy Node)](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Dokumentacja interfejsu API REST platformy Azure](https://docs.microsoft.com/rest/api/keyvault/)
