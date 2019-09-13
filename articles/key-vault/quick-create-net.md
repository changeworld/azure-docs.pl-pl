---
title: Szybki Start — Azure Key Vaulta Biblioteka kliencka dla platformy .NET
description: Zawiera kryteria formatowania i zawartości dotyczące tworzenia przewodników szybki start dla bibliotek klienckich zestawu Azure SDK.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 05/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: e57b5a49ac0c99fa81e54134e74964bf38418e4d
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934911"
---
# <a name="quickstart-azure-key-vault-client-library-for-net"></a>Szybki start: Azure Key Vault Biblioteka kliencka dla platformy .NET

Wprowadzenie do biblioteki klienta Azure Key Vault dla platformy .NET. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Użyj biblioteki klienta Key Vault dla platformy .NET, aby:

- Zwiększ bezpieczeństwo i kontrolę nad kluczami i hasłami.
- Utwórz i zaimportuj klucze szyfrowania w kilka minut.
- Ogranicz opóźnienia dzięki skali chmury i globalnej nadmiarowości.
- Upraszczanie i Automatyzowanie zadań związanych z certyfikatami SSL/TLS.
- Użyj zweryfikowanej sprzętowych modułów zabezpieczeń poziomu 2 trybu FIPS 140-2.

[](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | Pakiet[kodu](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/KeyVault)[](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/) źródłowego biblioteki dokumentacji interfejsu API (NuGet) | 


## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Zestaw .NET Core 2,1 SDK lub nowszy](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) lub [Azure PowerShell](/powershell/azure/overview)

W tym przewodniku Szybki Start `dotnet`założono, że korzystasz z systemu, [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)i poleceń systemu Windows w terminalu z systemem Windows (na przykład [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)lub [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-new-net-console-app"></a>Utwórz nową aplikację konsolową platformy .NET

Utwórz nową aplikację platformy .NET Core w preferowanym edytorze lub środowisku IDE.

W oknie konsoli Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie. `akv-dotnet`


```console
dotnet new console -n akvdotnet
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Zainstaluj pakiet

W oknie konsoli programu Zainstaluj Azure Key Vaultą bibliotekę kliencką dla platformy .NET:

```console
dotnet add package Microsoft.Azure.KeyVault
```

W tym przewodniku szybki start konieczne będzie zainstalowanie następujących pakietów:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

Ten przewodnik Szybki Start używa wstępnie utworzonego magazynu kluczy platformy Azure. Magazyn kluczy można utworzyć, wykonując czynności opisane w [przewodniku szybki start dotyczącego interfejsu wiersza polecenia platformy Azure](quick-create-cli.md), [Azure PowerShell szybki start](quick-create-powershell.md)lub [Azure Portal przewodniku szybki start](quick-create-portal.md). Alternatywnie możesz po prostu uruchomić poniższe polecenia interfejsu CLI platformy Azure.

> [!Important]
> Każdy Key Vault musi mieć unikatową nazwę. Poniższy przykład tworzy Key Vault o nazwie *myKV*, ale należy nazwać coś innego i użyć tej nazwy w tym przewodniku Szybki Start.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Najprostszym sposobem uwierzytelniania aplikacji .NET opartej na chmurze jest tożsamość zarządzana; Aby uzyskać szczegółowe informacje [, zobacz Uwierzytelnianie między usługami i Azure Key Vault przy użyciu platformy .NET](service-to-service-authentication.md) . W celu uproszczenia tego przewodnika Szybki Start tworzy aplikację konsolową platformy .NET. Uwierzytelnianie aplikacji klasycznej przy użyciu platformy Azure wymaga użycia jednostki usługi.
Utwórz zasadę usługi przy użyciu interfejsu wiersza polecenia platformy Azure [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

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

Zwróć uwagę na clientId i clientSecret, ponieważ będziemy z nich korzystać w powyższym kroku [uwierzytelnianie w magazynie kluczy](#authenticate-to-your-key-vault) .

Wymagany jest również identyfikator aplikacji głównej usługi. Można go znaleźć, uruchamiając [AZ AD Sp list](https://docs.microsoft.com/en-us/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) z `--show-mine` parametrem:

```azurecli
az ad sp list --show-mine
```

`appID` Pojawia się w zwróconym kodzie JSON:

```json
    "appId": "2cf5aa18-0100-445a-9438-0b93e577a3ed",
```

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Przyznaj jednostce usługi dostęp do magazynu kluczy

Utwórz zasady dostępu dla magazynu kluczy, który przyznaje uprawnienia do nazwy głównej usługi. Można to zrobić za pomocą polecenia [AZ webmagazyn Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Chcemy nadać jednostce usługi uprawnienia Get, list i Set dla kluczy i wpisów tajnych.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <appid-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>Model obiektów

Biblioteka klienta Azure Key Vault dla platformy .NET umożliwia zarządzanie kluczami i powiązanymi zasobami, takimi jak certyfikaty i wpisy tajne. Poniższe przykłady kodu pokazują, jak ustawić wpis tajny i pobrać wpis tajny.

Cała Aplikacja konsolowa jest dostępna pod https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet adresem.

## <a name="code-examples"></a>Przykłady kodu

### <a name="add-directives"></a>Dodaj dyrektywy

Dodaj następujące dyrektywy na początku kodu:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Uwierzytelnianie w magazynie kluczy

Ten przewodnik Szybki Start platformy .NET opiera się na zmiennych środowiskowych do przechowywania poświadczeń, które nie powinny być umieszczane w kodzie. 

`setx` Przed rozpoczęciem kompilowania i uruchamiania aplikacji użyj polecenia `akvClientId`, aby ustawić zmienne środowiskowe `akvTenantId`, `akvClientSecret`, `akvSubscriptionId` i na wartości zanotowane powyżej.

```console
setx akvClientId <your-clientID>

setx akvClientSecret <your-clientSecret>
````

Przy każdym wywołaniu `setx`należy uzyskać odpowiedź "sukces: Określona wartość została zapisana. "

Przypisz te zmienne środowiskowe do ciągów w kodzie, a następnie Uwierzytelnij aplikację, przekazując je do [klasy KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient):

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Zapisz klucz tajny

Teraz, gdy aplikacja jest uwierzytelniana, możesz umieścić klucz tajny w magazynie kluczy przy użyciu [metody SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) , która wymaga adresu URL Twojego magazynu, który znajduje się w formularzu `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`. Wymaga również nazwy wpisu tajnego — używamy hasła ".  Możesz przypisać te ciągi do zmiennych do ponownego użycia.

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Można sprawdzić, czy wpis tajny został ustawiony za pomocą polecenia [AZ The Secret show klucza tajnego](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Pobierz klucz tajny

Teraz można pobrać wcześniej ustawioną wartość za pomocą [metody GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Wpis tajny jest teraz zapisywany jako `keyvaultSecret.Value;`.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, możesz użyć interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby usunąć magazyn kluczy i odpowiednią grupę zasobów.

```azurecli
az group delete -g "myResourceGroup" -l "EastUS" 
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Magazyn kluczy, Zapisano wpis tajny i pobrano ten klucz tajny. Zobacz [całą aplikację konsolową w](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet)serwisie GitHub.

Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Implementowanie uwierzytelniania między usługami [w celu Azure Key Vault przy użyciu platformy .NET](service-to-service-authentication.md)
- Zapoznaj się [z omówieniem Azure Key Vault](key-vault-overview.md)
- Zobacz [przewodnik dewelopera Azure Key Vault](key-vault-developers-guide.md)
- Informacje o [kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-and-certificates.md)
- Przegląd [Azure Key Vault najlepszych](key-vault-best-practices.md) rozwiązań
