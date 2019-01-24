---
title: Samouczek — używanie usługi Azure Key Vault za pomocą maszyny wirtualnej platformy Azure w języku Python | Microsoft Docs
description: W ramach tego samouczka skonfigurujesz aplikację w języku Python, aby odczytać wpis tajny z magazynu kluczy
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
ms.openlocfilehash: 1e364003093d5e37a75830386cafe855b0bdcad2
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467405"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-virtual-machine-in-python"></a>Samouczek: używanie usługi Azure Key Vault za pomocą maszyny wirtualnej platformy Azure w języku Python

Usługa Azure Key Vault umożliwia ochronę wpisów tajnych, takich jak klucze interfejsu API i parametry połączenia bazy danych potrzebne do uzyskania dostępu do aplikacji, usługi oraz zasoby informatyczne.

W tym samouczku wykonasz kroki umożliwiające skonfigurowanie aplikacji internetowej platformy Azure pod kątem odczytu informacji z usługi Azure Key Vault za pomocą tożsamości zarządzanych dla zasobów platformy Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie magazynu kluczy.
> * Zapisywanie wpisu tajnego w magazynie kluczy.
> * Tworzenie maszyny wirtualnej platformy Azure.
> * Włączanie [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla maszyny wirtualnej.
> * Przyznawanie wymaganych uprawnień w celu umożliwienia aplikacji konsolowej odczytu danych z magazynu kluczy.
> * Pobieranie wpisu tajnego z magazynu kluczy.

Zanim przejdziesz dalej, zapoznaj się z [podstawowymi pojęciami dotyczącymi usługi Key Vault](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Wymagania wstępne
W przypadku wszystkich platform potrzebne są następujące elementy:

* Git ([pobierz](https://git-scm.com/downloads)).
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) w wersji 2.0.4 lub nowszej. Jest on dostępny w systemach Windows, Max i Linux.

### <a name="managed-service-identity-and-how-it-works"></a>Tożsamość usługi zarządzanej i jej działanie
W tym samouczku jest używana tożsamość usługi zarządzanej (MSI).

Usługa Azure Key Vault umożliwia bezpieczne przechowywanie poświadczeń, tak aby nie były zawarte w kodzie. Aby je pobrać, musisz uwierzytelnić się w usłudze Key Vault. W tym celu potrzebujesz poświadczenia. Jest to klasyczny problem dotyczący uruchamiania. Dzięki platformie Azure i usłudze Azure Active Directory (Azure AD) tożsamość usługi zarządzanej udostępnia „tożsamość uruchamiania”, która ułatwia rozpoczęcie wykonywania czynności.

Po włączeniu tożsamości usługi zarządzanej dla usługi platformy Azure, takiej jak Virtual Machines, App Service lub Functions, platforma Azure tworzy [jednostkę usługi](key-vault-whatis.md#basic-concepts) dla wystąpienia usługi w usłudze Azure AD. Platforma Azure wprowadza poświadczenia dla jednostki usługi do wystąpienia usługi. 

![Tożsamość usługi zarządzanej](media/MSI.png)

Następnie Twój kod wywołuje lokalną usługę metadanych, która jest dostępna w zasobie platformy Azure, aby uzyskać token dostępu.
W celu uwierzytelnienia w usłudze Azure Key Vault kod używa tokenu dostępu, który otrzymuje z lokalnego punktu końcowego tożsamości usługi zarządzanej. 

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

* Nazwa magazynu kluczy: nazwa musi być ciągiem od 3 do 24 znaków i może zawierać tylko znaki 0–9, a–z, A–Z, i myślniki (-).
* Nazwa grupy zasobów.
* Lokalizacja: **Zachodnie stany USA**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania jakichkolwiek operacji na tym nowym magazynie.

## <a name="add-a-secret-to-the-key-vault"></a>Dodawanie wpisu tajnego do magazynu kluczy

Dodajemy wpis tajny, aby zilustrować, jak to działa. Możesz zapisywać parametry połączenia SQL lub inne informacje, które muszą być przechowywane bezpiecznie, ale jednocześnie być dostępne dla aplikacji.

Wpisz następujące polecenia, aby utworzyć wpis tajny w magazynie kluczy o nazwie *AppSecret*. Ten wpis tajny będzie przechowywał wartość **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm).

W poniższym przykładzie zostanie utworzona maszyna wirtualna o nazwie *myVM* i dodane konto użytkownika o nazwie *azureuser*. Parametr `--generate-ssh-keys` automatycznie generuje klucz SSH i umieszcza go w domyślnej lokalizacji klucza (*~/.ssh*). Aby zamiast niego użyć określonego zestawu kluczy, skorzystaj z opcji `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Utworzenie maszyny wirtualnej i zasobów pomocniczych potrwa kilka minut. Następujące przykładowe dane wyjściowe pokazują, że tworzenie maszyny wirtualnej zakończyło się pomyślnie:

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

## <a name="assign-an-identity-to-the-virtual-machine"></a>Przypisywanie tożsamości do maszyny wirtualnej
W tym kroku utworzymy tożsamość przypisaną przez system do maszyny wirtualnej. Uruchom następujące polecenie w interfejsie wiersza polecenia platformy Azure:

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Dane wyjściowe polecenia wyglądają tak jak poniżej. Zanotuj wartość elementu **systemAssignedIdentity**. 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-the-virtual-machine-identity-permission-to-the-key-vault"></a>Przyznawanie uprawnień tożsamości maszyny wirtualnej do magazynu kluczy
Teraz możemy przyznać uprawnienia tożsamości do magazynu kluczy. Uruchom następujące polecenie:

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-virtual-machine"></a>Logowanie do maszyny wirtualnej

Zaloguj się do maszyny wirtualnej, postępując zgodnie z [tym samouczkiem](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="create-and-run-the-sample-python-app"></a>Tworzenie i uruchamianie przykładowej aplikacji w języku Python

Poniższy przykładowy plik nosi nazwę *Sample.py*. Używa on biblioteki [requests](https://pypi.org/project/requests/2.7.0/) do wykonywania wywołań HTTP GET.

## <a name="edit-samplepy"></a>Edytowanie pliku Sample.py
Po utworzeniu pliku Sample.py otwórz go i skopiuj następujący kod. Kod jest procesem dwuetapowym: 
1. Pobieranie tokenu z lokalnego punktu końcowego tożsamości usługi zarządzanej na maszynie wirtualnej. Punkt końcowy pobiera następnie token z usługi Azure Active Directory.
2. Przekazywanie tokenu do magazynu kluczy i pobieranie wpisu tajnego. 

```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from an MSI-enabled Azure resource      
    # Note that the resource here is https://vault.azure.net for the public cloud, and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # Extracting data in JSON format 
    # This request gets an access token from Azure Active Directory by using the local MSI endpoint
    data = r.json() 
    
    # Step 2: Pass the access token received from the previous HTTP GET call to the key vault
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Uruchomienie następującego polecenia umożliwia wyświetlenie wartości wpisu tajnego: 

```
python Sample.py
```

Powyższy kod pokazuje, jak wykonać operacje w usłudze Azure Key Vault na maszynie wirtualnej z systemem Windows. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Interfejsy API REST usługi Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
