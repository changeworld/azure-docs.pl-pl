---
title: Samouczek — Korzystanie maszyny wirtualnej systemu Linux i platformy ASP.NET konsoli aplikacji na przechowywanie wpisów tajnych w usłudze Azure Key Vault | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak skonfigurować aplikację ASP.NET Core w celu odczytu wpisu tajnego z usługi Azure Key vault.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 576ce0abc15a646738fea57dfabf43c889635a4b
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416937"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Samouczek: Umożliwia przechowywanie kluczy tajnych w usłudze Azure Key Vault maszyny Wirtualnej z systemem Linux oraz aplikacji platformy .NET

Usługa Azure Key Vault pomaga chronić klucze tajne, takie jak klucze interfejsu API i parametry połączenia bazy danych, które są niezbędne do uzyskania dostępu do aplikacji, usług i zasobów informatycznych.

W ramach tego samouczka możesz skonfigurować aplikację konsolową .NET w celu odczytywania informacji z usługi Azure Key Vault przy użyciu zarządzanych tożsamości dla zasobów platformy Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie magazynu kluczy
> * Store klucz tajny w usłudze Key Vault
> * Tworzenie maszyny wirtualnej systemu Linux platformy Azure
> * Włączanie [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla maszyny wirtualnej
> * Udzielenia wymaganych uprawnień dla aplikacji konsoli, można odczytać danych z usługi Key Vault
> * Pobieranie wpisu tajnego z usługi Key Vault

Zanim przejdziemy dalej, przeczytaj o [klucza magazynu podstawowe pojęcia](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Wymagania wstępne

* [Git](https://git-scm.com/downloads).
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Interfejs wiersza polecenia Azure 2.0 lub nowszej](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) lub usługi Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Zapoznanie się z tożsamością usługi zarządzanej

Usługa Azure Key Vault może bezpiecznie przechowywać poświadczenia, więc nie znajdują się w Twoim kodzie, ale w celu ich pobrania należy uwierzytelnić się w usłudze Azure Key Vault. Jednak aby uwierzytelniać się w usłudze Key Vault, potrzebne są poświadczenia. Jest to klasyczny problem dotyczący uruchamiania. Dzięki platformie Azure i usługi Azure Active Directory (Azure AD) tożsamość usługi zarządzanej (MSI) może zapewnić bootstrap tożsamości, która sprawia, że znacznie prostsza do rozpoczęcia wykonywania zadań.

Po włączeniu tożsamości usługi Zarządzanej dla usługi platformy Azure, takich jak maszyny wirtualne, usługi App Service lub funkcji, platforma Azure tworzy jednostki dla wystąpienia usługi usługi w usłudze Azure Active Directory. Wprowadza ona poświadczenia dla jednostki usługi do wystąpienia usługi.

![Tożsamość usługi zarządzanej](media/MSI.png)

Następnie Twój kod wywołuje lokalną usługę metadanych dostępną w zasobie platformy Azure, aby uzyskać token dostępu.
W celu uwierzytelniania w usłudze Azure Key Vault kod używa tokenu dostępu otrzymanego z lokalnego elementu MSI_ENDPOINT.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, wpisz:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą `az group create` polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów w lokalizacji zachodnie stany USA. Wybierz nazwę grupy zasobów i Zastąp `YourResourceGroupName` w następującym przykładzie:

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Możesz używać tej grupy zasobów w całym samouczku.

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Następnie należy utworzyć magazyn kluczy w grupie zasobów. Podaj następujące informacje:

* Nazwa magazynu kluczy: ciąg 3 do 24 znaków, które mogą zawierać tylko cyfry, litery i łączniki (0-9, a – z, A-Z, a \- ).
* Nazwa grupy zasobów
* Lokalizacja: **Zachodnie stany USA**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

W tym momencie tylko konta platformy Azure jest autoryzowany do wykonywania jakichkolwiek operacji na tym nowym magazynie.

## <a name="add-a-secret-to-the-key-vault"></a>Dodawanie wpisu tajnego do magazynu kluczy

Teraz możesz dodać wpis tajny. W rzeczywistych scenariuszy możesz mogą przechowywać parametry połączenia SQL lub inne informacje, który trzeba zapewnić bezpieczne, ale być dostępne dla aplikacji.

Na potrzeby tego samouczka wpisz następujące polecenia, aby utworzyć wpis tajny w usłudze key vault. Klucz tajny jest nazywany **AppSecret** i jego wartość wynosi **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Tworzenie maszyny wirtualnej z systemem Linux

Utwórz Maszynę wirtualną za pomocą `az vm create` polecenia.

W poniższym przykładzie zostanie utworzona maszyna wirtualna o nazwie **myVM** i dodane konto użytkownika o nazwie **azureuser**. `--generate-ssh-keys` Parametru nam używane do automatycznego generowania SSH klucza i umieścić go w domyślnej lokalizacji kluczy ( **~/.ssh**). Aby zamiast niego użyć określonego zestawu kluczy, skorzystaj z opcji `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Utworzenie maszyny wirtualnej i zasobów pomocniczych potrwa kilka minut. W poniższym przykładzie danych wyjściowych przedstawiono operacji tworzenia maszyny Wirtualnej zakończyło się pomyślnie.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

Zwróć uwagę na Twoje `publicIpAddress` w danych wyjściowych z maszyny Wirtualnej. Ten adres służy do uzyskiwania dostępu do maszyny wirtualnej w późniejszych krokach.

## <a name="assign-an-identity-to-the-vm"></a>Przypisywanie tożsamości do maszyny wirtualnej

Utwórz tożsamość przypisaną przez system do maszyny wirtualnej, uruchamiając następujące polecenie:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Dane wyjściowe polecenia powinny być:

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Zanotuj wartość parametru `systemAssignedIdentity`. Możesz użyć go w następnym kroku.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Przyznawanie tożsamości maszyny wirtualnej uprawnienia do usługi Key Vault

Teraz możesz przyznać utworzonej tożsamości uprawnienia do usługi Key Vault. Uruchom następujące polecenie:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Logowanie się do maszyny wirtualnej

Zaloguj się do maszyny wirtualnej za pomocą terminalu.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>Zainstaluj program .NET Core w systemie Linux

Na maszynie Wirtualnej systemu Linux:

Zarejestruj klucz produktu firmy Microsoft jako zaufany, uruchamiając następujące polecenia:

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

Skonfiguruj pakiet hosta żądanej wersji źródła danych na podstawie systemu operacyjnego:

```console
   # Ubuntu 17.10
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-artful-prod artful main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 17.04
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-zesty-prod zesty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 16.04 / Linux Mint 18
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 14.04 / Linux Mint 17
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
```

Zainstaluj program .NET i zapoznaj się z wersją:

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>Tworzenie i uruchamianie przykładowej aplikacji .NET

Uruchom następujące polecenia. Powinien pojawić się drukowane w konsoli "Hello World".

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>Edytuj aplikację konsoli, aby pobrać klucz tajny

Otwórz plik Program.cs i Dodaj te pakiety:

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

Jest procesem dwuetapowym, aby zmienić plik klasy, aby umożliwić aplikacji dostęp do klucza tajnego w magazynie kluczy.

1. Pobierz token z lokalnym punktem końcowym MSI na maszynie Wirtualnej, która z kolei pobiera token z usługi Azure Active Directory.
1. Przekaż token do usługi Key Vault i pobrać klucz tajny.

   Edytuj plik klasy zawiera następujący kod:

   ```csharp
    class Program
       {
           static void Main(string[] args)
           {
               // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
               var token = GetToken();

               // Step 2: Fetch the secret value from Key Vault
               System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
           }

           static string GetToken()
           {
               WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
               request.Headers.Add("Metadata", "true");
               WebResponse response = request.GetResponse();
               return ParseWebResponse(response, "access_token");
           }

           static string FetchSecretValueFromKeyVault(string token)
           {
               WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
               kvRequest.Headers.Add("Authorization", "Bearer "+  token);
               WebResponse kvResponse = kvRequest.GetResponse();
               return ParseWebResponse(kvResponse, "value");
           }

           private static string ParseWebResponse(WebResponse response, string tokenName)
           {
               string token = String.Empty;
               using (Stream stream = response.GetResponseStream())
               {
                   StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                   String responseString = reader.ReadToEnd();

                   JObject joResponse = JObject.Parse(responseString);
                   JValue ojObject = (JValue)joResponse[tokenName];
                   token = ojObject.Value.ToString();
               }
               return token;
           }
       }
   ```

Teraz już wiesz, jak do wykonywania operacji w usłudze Azure Key Vault w aplikacji .NET uruchomionych na maszynie wirtualnej z systemem Linux platformy Azure.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie powiązane zasoby nie będą już potrzebne, można je usunąć. W tym celu wybierz grupę zasobów maszyny wirtualnej i kliknij pozycję **Usuń**.

Usuń magazyn kluczy przy użyciu polecenia `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Interfejsy API REST usługi Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
