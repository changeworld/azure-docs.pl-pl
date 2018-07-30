---
title: Szybki start na platformie Azure — konfigurowanie aplikacji internetowej platformy Azure w celu odczytu wpisu tajnego z usługi Key Vault | Microsoft Docs
description: Przewodnik Szybki start przedstawiający sposób konfigurowania aplikacji platformy ASP.Net Core w celu odczytu wpisu tajnego z usługi Key Vault
services: key-vault
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 07a7bc5713f093e34a775aacab27094780ac6c7e
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247829"
---
# <a name="quickstart-set-and-read-a-secret-from-key-vault-in-a-net-web-app"></a>Szybki start: ustawianie i odczytywanie wpisu tajnego z usługi Key Vault w aplikacji internetowej platformy .NET

W tym przewodniku Szybki start wykonasz kroki niezbędne do skonfigurowania aplikacji internetowej platformy Azure w celu odczytu informacji z usługi Key Vault za pomocą tożsamości usługi zarządzanej. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie usługi Key Vault.
> * Przechowywanie wpisu tajnego w usłudze Key Vault.
> * Pobieranie wpisu tajnego z usługi Key Vault.
> * Tworzenie aplikacji internetowej platformy Azure.
> * [Włączanie tożsamości usługi zarządzanej](../active-directory/managed-service-identity/overview.md).
> * Przyznawanie wymaganych uprawnień w celu umożliwienia aplikacji internetowej odczytu danych z usługi Key Vault.

Zanim przejdziesz do następnych kroków, przeczytaj [podstawowe pojęcia](key-vault-whatis.md#basic-concepts), szczególnie informacje dotyczące [tożsamości usługi zarządzanej](../active-directory/managed-service-identity/overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* W systemie Windows:
  * [Visual Studio 2017 w wersji 15.7.3 lub nowszej](https://www.microsoft.com/net/download/windows) z następującymi pakietami roboczymi:
    * Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
    * Tworzenie aplikacji dla wielu platform w środowisku .NET Core
  * [Zestaw .NET Core 2.1 SDK lub nowszy](https://www.microsoft.com/net/download/windows)

* Na komputerze Mac:
  * https://visualstudio.microsoft.com/vs/mac/

* Wszystkie platformy:
  * Pobierz narzędzie GIT z [tej strony](https://git-scm.com/downloads).
  * Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
  * [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) — wymagany jest wiersz polecenia platformy Azure w wersji 2.0.4 lub nowszej. Jest on dostępny w systemach Windows, Mac i Linux

## <a name="login-to-azure"></a>Logowanie na platformie Azure

   Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia, możesz wpisać:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Wybierz nazwę grupy zasobów i wypełnij symbol zastępczy.
Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *<YourResourceGroupName>* w lokalizacji *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

Właśnie utworzona grupa zasobów jest używana w tym artykule.

## <a name="create-an-azure-key-vault"></a>Tworzenie usługi Azure Key Vault

Następnie utworzysz usługę Key Vault w grupie zasobów utworzonej w poprzednim kroku. Podaj następujące informacje:

* Nazwa magazynu — **wybierz tutaj nazwę magazynu Key Vault**. Nazwa magazynu Key Vault musi być ciągiem o długości 3–24 znaków, zawierającym tylko znaki 0–9, a–z, A–Z i -.
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
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>Otwieranie i edytowanie rozwiązania

Edytuj plik program.cs, aby uruchomić przykład z określoną nazwą magazynu Key Vault.

1. Przejdź do folderu key-vault-dotnet-core-quickstart
2. Otwórz plik key-vault-dotnet-core-quickstart.sln w programie Visual Studio 2017
3. Otwórz plik Program.cs i zaktualizuj symbol zastępczy <YourKeyVaultName> przy użyciu nazwy utworzonego wcześniej magazynu Key Vault.

To rozwiązanie używa bibliotek NuGet [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) i [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

## <a name="run-the-app"></a>Uruchamianie aplikacji

Z menu głównego programu Visual Studio 2017 wybierz kolejno pozycje Debuguj > Uruchom bez debugowania. Gdy pojawi się przeglądarka, przejdź do strony Informacje. Wyświetlana jest wartość wpisu AppSecret.

## <a name="publish-the-web-application-to-azure"></a>Publikowanie aplikacji internetowej na platformie Azure

Publikujemy tę aplikację na platformie Azure, aby sprawdzić, jak działa na żywo jako aplikacja internetowa, oraz aby sprawdzić, czy jest pobierana wartość wpisu tajnego

1. W programie Visual Studio wybierz projekt **key-vault-dotnet-core-quickstart**.
2. Wybierz pozycję **Publikowanie**, a następnie **Uruchom**.
3. Utwórz nową usługę **App Service** i wybierz pozycję **Opublikuj**.
4. Zmień nazwę aplikacji na „keyvaultdotnetcorequickstart”.
5. Wybierz pozycję **Utwórz**.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-managed-service-identities-msi"></a>Włączanie tożsamości usługi zarządzanej (MSI)

Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń oraz innych kluczy i wpisów tajnych, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Azure Key Vault. Tożsamość usługi zarządzanej (MSI) ułatwia to, udostępniając usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności przechowywania poświadczeń w kodzie.

1. Powrót do interfejsu wiersza polecenia platformy Azure
2. Uruchom polecenie assign-identity w celu utworzenia tożsamości dla tej aplikacji:

```azurecli
az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
```

>[!NOTE]
>To polecenie odpowiada przejściu do portalu i przełączeniu ustawienia **Tożsamość usługi zarządzanej** na wartość **Włączone** we właściwościach aplikacji internetowej.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Przypisywanie do aplikacji uprawnień odczytu wpisów tajnych z usługi Key Vault

Zapisz dane wyjściowe po [opublikowaniu aplikacji na platformie Azure][]. Powinny one mieć następujący format:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Następnie uruchom to polecenie, używając nazwy magazynu Key Vault i wartości PrincipalId skopiowanej z powyższego kodu:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get

```

## <a name="next-steps"></a>Następne kroki

* [Strona główna usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Dokumentacja usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
* [Zestaw Azure SDK dla platformy .NET](https://github.com/Azure/azure-sdk-for-net)
* [Dokumentacja interfejsu API REST platformy Azure](https://docs.microsoft.com/rest/api/keyvault/)
