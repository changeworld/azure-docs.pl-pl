---
title: Samouczek — Użyj usługi Azure Key Vault z maszyną wirtualną Windows na platformie .NET | Dokumentacja firmy Microsoft
description: W tym samouczku skonfigurujesz aplikacji platformy ASP.NET core w celu odczytu wpisu tajnego z magazynu kluczy.
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: fb17afa4bfe8c00c91cc8fb33ab3326452065a9e
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885421"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Samouczek: Użyj usługi Azure Key Vault z maszyną wirtualną Windows na platformie .NET

Usługa Azure Key Vault pomaga chronić klucze tajne, takie jak klucze interfejsu API, parametry połączenia bazy danych, musisz mieć dostęp do aplikacji, usług i zasobów informatycznych.

W tym samouczku dowiesz się, jak uzyskać aplikację konsoli w celu odczytywania informacji z usługi Azure Key Vault. Aby to zrobić, należy użyć zarządzanych tożsamości dla zasobów platformy Azure. 

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Utwórz grupę zasobów.
> * Tworzenie magazynu kluczy.
> * Dodawanie wpisu tajnego do magazynu kluczy.
> * Pobieranie wpisu tajnego z magazynu kluczy.
> * Tworzenie maszyny wirtualnej platformy Azure.
> * Włączanie [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla maszyny wirtualnej.
> * Przypisywanie uprawnień do tożsamości maszyny Wirtualnej.

Przed rozpoczęciem przeczytaj [podstawowe pojęcia dotyczące usługi Key Vault](key-vault-whatis.md#basic-concepts). 

Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Dla Windows, Mac i Linux:
  * [Usługa Git](https://git-scm.com/downloads)
  * Ten samouczek wymaga uruchomienia interfejsu wiersza polecenia platformy Azure lokalnie. Musi mieć wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja wiersza polecenia lub jego uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Informacje o tożsamości usługi zarządzanej

Usługa Azure Key Vault bezpiecznie przechowuje poświadczenia, więc nie są one wyświetlane w kodzie. Aby jednak pobrać klucze, musisz uwierzytelnić się w usłudze Azure Key Vault. W tym celu potrzebujesz poświadczenia. To klasyczny przykład dylematu dotyczącego uruchamiania. Tożsamość usługi zarządzanej rozwiązuje ten problem poprzez zapewnienie _tożsamości uruchamiania_, która upraszcza ten proces.

Po włączeniu tożsamości usługi Zarządzanej dla usługi platformy Azure, takie jak maszyny wirtualne platformy Azure, Azure App Service lub usługi Azure Functions, platforma Azure tworzy [nazwy głównej usługi](key-vault-whatis.md#basic-concepts). MSI dzieje dla wystąpienia usługi Azure Active Directory (Azure AD) i wprowadza poświadczenia nazwy głównej usługi w tym wystąpieniu. 

![Tożsamość usługi zarządzanej](media/MSI.png)

Następnie do uzyskania tokenu dostępu, Twój kod wywołuje usługi metadanymi lokalnymi, które są dostępne w obszarze zasobów platformy Azure. Aby uwierzytelniać się w usłudze Azure Key Vault, kod używa tokenu dostępu, który otrzymuje od lokalny punkt końcowy MSI. 

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, wpisz:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). 

Następnie wybierz nazwę grupy zasobów i wypełnienie w miejsce symbolu zastępczego. Poniższy przykład obejmuje tworzenie grupy zasobów w lokalizacji Zachodnie stany USA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Możesz użyć swojej nowo utworzonej grupy zasobów w tym samouczku.

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Aby utworzyć magazyn kluczy w grupie zasobów, który został utworzony w poprzednim kroku, należy podać następujące informacje:

* Nazwa magazynu kluczy: ciąg 3 do 24 znaków, które mogą zawierać tylko cyfry (0 – 9) litery (a – z, A – Z) i łączniki (-)
* Nazwa grupy zasobów
* Lokalizacja: **Zachodnie stany USA**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
W tym momencie konta platformy Azure jest jedyną, która ma uprawnienia do wykonywania operacji na tym nowym magazynie kluczy.

## <a name="add-a-secret-to-the-key-vault"></a>Dodawanie wpisu tajnego do magazynu kluczy

Dodajemy wpis tajny, aby zilustrować, jak to działa. Klucz tajny może być parametry połączenia SQL lub inne informacje, który należy zachować Bezpieczni i dostępne dla aplikacji.

Aby utworzyć wpis tajny w usłudze key vault o nazwie **AppSecret**, wprowadź następujące polecenie:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Ten wpis tajny zawiera wartość **MySecret**.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Można utworzyć maszynę wirtualną przy użyciu jednej z następujących metod:

* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli)
* [PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)
* [Witryna Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## <a name="assign-an-identity-to-the-vm"></a>Przypisywanie tożsamości do maszyny wirtualnej
W tym kroku utworzysz tożsamości przypisanych przez system dla maszyny wirtualnej, uruchamiając następujące polecenie w interfejsie wiersza polecenia platformy Azure:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Należy pamiętać, tożsamości przypisanych przez system, wyświetlanego w poniższym kodzie. Będą dane wyjściowe poprzedniego polecenia: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Przypisywanie uprawnień do tożsamości maszyny Wirtualnej
Teraz można przypisać uprawnienia utworzonej wcześniej tożsamości do magazynu kluczy, uruchamiając następujące polecenie:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Logowanie do maszyny wirtualnej

Aby zalogować się do maszyny wirtualnej, postępuj zgodnie z instrukcjami [Connect i zaloguj się do platformy Azure maszynę wirtualną z systemem Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="install-net-core"></a>Instalowanie programu .NET Core

Aby zainstalować program .NET Core, przejdź do [pobiera .NET](https://www.microsoft.com/net/download) strony.

## <a name="create-and-run-a-sample-net-app"></a>Tworzenie i uruchamianie przykładowej aplikacji .NET

Otwórz wiersz polecenia.

Drukowania "Hello World" w konsoli, uruchamiając następujące polecenia:

```batch
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app"></a>Edytowanie aplikacji konsoli

Otwórz *Program.cs* pliku i Dodaj te pakiety:

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Edytuj plik klasy, który będzie zawierał kod następujący dwuetapowy proces:

1. Pobieranie tokenu z lokalnego punktu końcowego tożsamości usługi zarządzanej na maszynie wirtualnej. Również sposób pobiera token z usługi Azure AD.
1. Przekaż token do magazynu kluczy, a następnie Pobierz klucz tajny. 

```csharp
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from the local (URI) Managed Service Identity endpoint, which in turn fetches it from Azure AD
            var token = GetToken();

            // Step 2: Fetch the secret value from your key vault
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
            WebRequest kvRequest = WebRequest.Create("https://<YourVaultName>.vault.azure.net/secrets/<YourSecretName>?api-version=2016-10-01");
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

Powyższy kod pokazuje, jak wykonać operacje w usłudze Azure Key Vault na maszynie wirtualnej z systemem Windows.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń maszynę wirtualną i magazyn kluczy.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Interfejs API REST usługi Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
