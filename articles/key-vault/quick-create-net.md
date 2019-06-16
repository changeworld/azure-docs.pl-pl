---
title: 'Szybki start: Ustawianie i pobieranie wpisu tajnego z usługi Azure Key Vault za pomocą aplikacji internetowej platformy .NET — usługa Azure Key Vault | Dokumentacja firmy Microsoft'
description: W tym przewodniku Szybki start ustawisz i pobierzesz wpis tajny z usługi Azure Key Vault przy użyciu aplikacji internetowej .NET
services: key-vault
author: msmbaldwin
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 01/02/2019
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 4f9fff41e4b9043c271d656583fb8b9a11ff3a7a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052788"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-net-web-app"></a>Szybki start: Konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu aplikacji internetowej .NET

W tym przewodniku Szybki start wykonasz kroki konfigurowania aplikacji internetowej platformy Azure pod kątem odczytu informacji z usługi Azure Key Vault za pomocą tożsamości zarządzanych dla zasobów platformy Azure. Usługa Key Vault pomaga zabezpieczać informacje. Omawiane kwestie:

* Tworzenie magazynu kluczy.
* Zapisywanie wpisu tajnego w magazynie kluczy.
* Pobieranie wpisu tajnego z magazynu kluczy.
* Tworzenie aplikacji internetowej platformy Azure.
* Włączanie [tożsamości usługi zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla aplikacji internetowej.
* Przyznawanie wymaganych uprawnień w celu umożliwienia aplikacji internetowej odczytu danych z magazynu kluczy.

Zanim przejdziesz dalej, zapoznaj się z [podstawowymi pojęciami dotyczącymi usługi Key Vault](key-vault-whatis.md#basic-concepts).

>[!NOTE]
>Usługa Key Vault to centralne repozytorium do programistycznego przechowywania wpisów tajnych. W tym celu aplikacje i użytkownicy muszą najpierw uwierzytelnić się w usłudze Key Vault — czyli podać wpis tajny. Aby zastosować najlepsze rozwiązania dotyczące bezpieczeństwa, pierwszy wpis tajny musi być okresowo obracany. 
>
>Dzięki [tożsamościom usługi zarządzanej dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) aplikacje uruchamiane na platformie Azure uzyskują tożsamość, która jest automatycznie zarządzana przez tę platformę. Dzięki temu można rozwiązać *początkowy problem dotyczący wpisu tajnego*, gdzie użytkownicy i aplikacje mogą postępować zgodnie z najlepszymi wskazówkami i nie muszą pamiętać o obracaniu pierwszego wpisu tajnego.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* W systemie Windows:
  * [Visual Studio 2019](https://www.microsoft.com/net/download/windows) z następującymi pakietami roboczymi:
    * ASP.NET i tworzenie aplikacji internetowych
    * Programowanie dla wielu platform .NET core
  * [Zestaw .NET Core 2.1 SDK lub nowszy](https://www.microsoft.com/net/download/windows)

* Na komputerze Mac:
  * Zobacz [Co nowego w programie Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).

* Wszystkie platformy:
  * Git ([pobierz](https://git-scm.com/downloads)).
  * Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
  * [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) w wersji 2.0.4 lub nowszej. Jest on dostępny w systemach Windows, Mac i Linux.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, wpisz:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Wybierz nazwę grupy zasobów i wypełnij symbol zastępczy.
Poniższy przykład obejmuje tworzenie grupy zasobów w lokalizacji Wschodnie stany USA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

W tym artykule jest używana właśnie utworzona grupa zasobów.

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Następnie utworzysz magazyn kluczy w grupie zasobów utworzonej w poprzednim kroku. Podaj następujące informacje:

* Nazwa magazynu kluczy: nazwa musi być ciągiem od 3 do 24 znaków i może zawierać tylko znaki 0–9, a–z, A–Z i myślnik (-).
* Nazwa grupy zasobów.
* Lokalizacja: **Wschodnie stany USA**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
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

## <a name="clone-the-repo"></a>Klonowanie repozytorium

Sklonuj repozytorium tak, aby utworzyć kopię lokalną, gdzie możesz edytować źródło. Uruchom następujące polecenie:

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>Otwieranie i edytowanie rozwiązania

Edytuj plik program.cs, aby uruchomić przykład z określoną nazwą magazynu kluczy:

1. Przejdź do folderu key-vault-dotnet-core-quickstart.
2. Otwórz plik klucz magazynu dotnet-core-quickstart.sln w Visual Studio 2019 r.
3. Otwórz plik Program.cs i zaktualizuj symbol zastępczy *YourKeyVaultName* przy użyciu nazwy utworzonego wcześniej magazynu kluczy.

To rozwiązanie używa bibliotek NuGet [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) i [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

## <a name="run-the-app"></a>Uruchamianie aplikacji

Wybierz z menu głównego programu Visual Studio 2019 **debugowania** > **Uruchom bez debugowania**. Gdy pojawi się przeglądarka, przejdź do strony **Informacje**. Wyświetlona zostanie wartość wpisu **AppSecret**.

## <a name="publish-the-web-application-to-azure"></a>Publikowanie aplikacji internetowej na platformie Azure

Opublikuj tę aplikację na platformie Azure, aby sprawdzić, jak działa na żywo jako aplikacja internetowa, oraz aby sprawdzić, czy możesz pobrać wartość wpisu tajnego:

1. W programie Visual Studio wybierz projekt **key-vault-dotnet-core-quickstart**.
2. Wybierz pozycje **Publikuj** > **Uruchom**.
3. Utwórz nową usługę **App Service**, a następnie wybierz pozycję **Publikuj**.
4. Zmień nazwę aplikacji na **keyvaultdotnetcorequickstart**.
5. Wybierz pozycję **Utwórz**.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-a-managed-identity-for-the-web-app"></a>Włączanie tożsamości zarządzanej dla aplikacji internetowej

Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń oraz innych kluczy i wpisów tajnych, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. [Tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) ułatwiają rozwiązanie tego problemu, udostępniając usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności przechowywania poświadczeń w kodzie.

W interfejsie wiersza polecenia platformy Azure uruchom polecenie assign-identity w celu utworzenia tożsamości dla tej aplikacji:

   ```azurecli
   az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
   ```

>[!NOTE]
>Polecenie w tej procedurze odpowiada przejściu do portalu i przełączeniu ustawienia **Przypisana tożsamość/system** na wartość **Włączone** we właściwościach aplikacji internetowej.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Przypisywanie do aplikacji uprawnień odczytu wpisów tajnych z usługi Key Vault

Zapisz dane wyjściowe po opublikowaniu aplikacji na platformie Azure. Powinny one mieć następujący format:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Następnie uruchom to polecenie, używając nazwy magazynu kluczy i wartości **PrincipalId**:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

Teraz, po uruchomieniu aplikacji, powinna pojawić się pobrana wartość wpisu tajnego. W poprzednim poleceniu przygotowujesz tożsamość aplikacji usługi uprawnień do wykonywania **uzyskać** i **listy** operacji na magazynie kluczy.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy grupa zasobów, maszyna wirtualna i wszystkie powiązane zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów dla usługi key vault, a następnie wybierz **Usuń**.

Usuń magazyn kluczy przy użyciu polecenia [az keyvault delete](https://docs.microsoft.com/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-delete):

```azurecli
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o usłudze Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)
