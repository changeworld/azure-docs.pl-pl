---
title: Samouczek — jak używać usługi Azure Key Vault za pomocą maszyny wirtualnej platformy Azure z systemem Linux w języku Python | Microsoft Docs
description: Samouczek konfigurowania aplikacji platformy ASP.NET Core w celu odczytu wpisu tajnego z usługi Key Vault
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: acc926151e5abd1d6f9d0992591575198d1fdf44
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890538"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-linux-virtual-machine-in-python"></a>Samouczek: jak używać usługi Azure Key Vault za pomocą maszyny wirtualnej platformy Azure z systemem Linux w języku Python

Usługa Azure Key Vault umożliwia ochronę wpisów tajnych, takich jak klucze interfejsu API, parametry połączenia bazy danych potrzebne do uzyskania dostępu do aplikacji, usługi i zasoby informatyczne.

W tym samouczku wykonasz kroki niezbędne do skonfigurowania aplikacji internetowej platformy Azure pod kątem odczytu informacji z usługi Azure Key Vault za pomocą tożsamości zarządzanych dla zasobów platformy Azure. Ten samouczek opiera się na usłudze [Azure Web Apps](../app-service/app-service-web-overview.md). Z poniższego artykułu dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie magazynu kluczy.
> * Zapisywanie wpisu tajnego w magazynie kluczy.
> * Pobieranie wpisu tajnego z magazynu kluczy.
> * Tworzenie maszyny wirtualnej platformy Azure.
> * Włączanie [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla maszyny wirtualnej.
> * Przyznawanie wymaganych uprawnień w celu umożliwienia aplikacji konsolowej odczytu danych z magazynu kluczy.
> * Pobieranie wpisów tajnych z usługi Key Vault.

Zanim przejdziesz dalej, zapoznaj się z [podstawowymi pojęciami](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Wymagania wstępne
* Wszystkie platformy:
  * Git ([pobierz](https://git-scm.com/downloads)).
  * Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
  * [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) w wersji 2.0.4 lub nowszej. Jest on dostępny w systemach Windows, Mac i Linux.

W tym samouczku jest używana tożsamość usługi zarządzanej

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>Co to jest tożsamość usługi zarządzanej i jak działa?
Zanim przejdziemy dalej, omówmy tożsamość usługi zarządzanej. Usługa Azure Key Vault może bezpiecznie przechowywać poświadczenia, więc nie znajdują się w Twoim kodzie, ale w celu ich pobrania należy uwierzytelnić się w usłudze Azure Key Vault. Aby uwierzytelniać się w usłudze Key Vault, potrzebne są poświadczenia. Jest to klasyczny problem dotyczący uruchamiania. Dzięki magii platformy Azure i usługi Azure AD, tożsamość usługi zarządzanej udostępnia „tożsamość uruchamiania”, która ułatwia rozpoczęcie wykonywania czynności.

Oto jak to działa. Po włączeniu tożsamości usługi zarządzanej dla wybranej usługi platformy Azure, takiej jak Virtual Machines, App Service lub Functions, platforma Azure tworzy [jednostkę usługi](key-vault-whatis.md#basic-concepts) dla wystąpienia usługi w usłudze Azure Active Directory i wprowadza poświadczenia dla jednostki usługi w tym wystąpieniu usługi. 

![Tożsamość usługi zarządzanej](media/MSI.png)

Następnie Twój kod wywołuje lokalną usługę metadanych dostępną w zasobie platformy Azure, aby uzyskać tokenu dostępu.
W celu uwierzytelniania w usłudze Azure Key Vault kod używa tokenu dostępu otrzymanego z lokalnego elementu MSI_ENDPOINT. 

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

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create).

W poniższym przykładzie zostanie utworzona maszyna wirtualna o nazwie *myVM* i dodane konto użytkownika o nazwie *azureuser*. Parametr `--generate-ssh-keys` jest używany, aby automatycznie wygenerować klucz SSH i umieścić go w domyślnej lokalizacji klucza (*~/.ssh*). Aby zamiast niego użyć określonego zestawu kluczy, skorzystaj z opcji `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Utworzenie maszyny wirtualnej i zasobów pomocniczych potrwa kilka minut. Następujące przykładowe dane wyjściowe pokazują, że operacja utworzenia maszyny wirtualnej zakończyła się pomyślnie.

```
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

Zanotuj wartość elementu `publicIpAddress` z danych wyjściowych maszyny wirtualnej. Ten adres służy do uzyskiwania dostępu do maszyny wirtualnej w następnych krokach.

## <a name="assign-identity-to-virtual-machine"></a>Przypisywanie tożsamości do maszyny wirtualnej
W tym kroku utworzymy tożsamość przypisaną przez system do maszyny wirtualnej, uruchamiając następujące polecenie w interfejsie wiersza polecenia platformy Azure:

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Zanotuj wartość elementu systemAssignedIdentity pokazaną poniżej. Dane wyjściowe powyższego polecenia będą następujące: 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-virtual-machine-identity-permission-to-key-vault"></a>Przyznawanie tożsamości maszyny wirtualnej uprawnienia do usługi Key Vault
Teraz możemy przyznać powyżej utworzonej tożsamości uprawnienie do usługi Key Vault, uruchamiając następujące polecenie:

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="login-to-the-virtual-machine"></a>Logowanie do maszyny wirtualnej

Możesz wykonać czynności opisane w tym [samouczku](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)

## <a name="create-and-run-sample-python-app"></a>Tworzenie i uruchamianie przykładowej aplikacji w języku Python

Poniżej znajduje się przykładowy plik o nazwie „Sample.py”. Używana jest w nim biblioteka [requests](http://docs.python-requests.org/master/) do wykonywania wywołań HTTP GET.

## <a name="edit-samplepy"></a>Edytowanie pliku Sample.py
Po utworzeniu pliku Sample.py otwórz go i skopiuj poniższy kod.

Poniżej przedstawiono proces składający się z 2 kroków. 
1. Pobieranie tokenu z lokalnego punktu końcowego tożsamości usługi zarządzanej na maszynie wirtualnej, który z kolei pobiera token z usługi Azure Active Directory
2. Przekazywanie tokenu do usługi Key Vault i pobieranie wpisu tajnego 

```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets a access_token from Azure Active Directory using the local MSI endpoint
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to Key Vault
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Uruchomienie poniższego polecenia umożliwia wyświetlenie wartości wpisu tajnego 

```
python Sample.py
```

Powyższy kod pokazuje, jak wykonać operacje w usłudze Azure Key Vault na maszynie wirtualnej platformy Azure z systemem Windows. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Interfejsy API REST usługi Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
