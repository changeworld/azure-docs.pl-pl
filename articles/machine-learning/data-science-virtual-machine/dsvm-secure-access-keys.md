---
title: Magazyn dostęp do poświadczeń na maszynie wirtualnej nauki danych bezpiecznie - Azure | Dokumentacja firmy Microsoft
description: Przechowywanie dostępu bezpiecznie poświadczeń na maszynie wirtualnej analizy danych.
keywords: bezpośrednie uczenia AI, narzędzia do analizy danych, maszyna wirtualna nauki danych, dane geograficzne analytics, proces nauki danych zespołu
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 9ec734cf456050250396b00aa09b61bace7e9aa0
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830278"
---
# <a name="store-access-credentials-on-the-data-science-virtual-machine-securely"></a>Dostęp do przechowywania poświadczeń na maszynie wirtualnej nauki danych bezpiecznie

Typowym wyzwaniem podczas kompilowania aplikacji w chmurze jest sposób zarządzania poświadczeniami, które muszą znajdować się w kodzie w przypadku uwierzytelniania przy użyciu usług w chmurze. Zabezpieczanie tych poświadczeń to ważne zadanie. W idealnej sytuacji nie będą one nigdy wyświetlane na stacjach roboczych deweloperów ani zaewidencjonowane do kontroli źródła. 

[Zarządzane tożsamości usługi (MSI)](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) sprawia, że rozwiązania tego problemu prostszy, zapewniając Azure usługi automatycznie zarządzane tożsamości w usłudze Azure Active Directory (Azure AD). Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, bez żadnych poświadczeń w kodzie. Jest jednym wspólnym wzorcem zabezpieczyć poświadczenia do użycia MSI jest w połączeniu z [Azure Keyvault](https://docs.microsoft.com/azure/key-vault/), zarządzane z usługi Azure bezpieczne przechowywanie kluczy tajnych i kluczy kryptograficznych. Umożliwia dostęp do usługi Key vault za pomocą tożsamości zarządzanych usług i pobieranie autoryzowanych kluczy tajnych i kluczy kryptograficznych z magazynu kluczy. 

Dokumentacja MSI i Key Vault jest pełne zasoby, aby uzyskać szczegółowe informacje na temat tych usług. Dalszej części tego artykułu jest podstawowe Elementarz, przedstawiający zastosowanie podstawowych MSI i Key Vault na maszynie wirtualnej nauki danych (DSVM). 

## <a name="walkthrough-of-using-msi-to-securely-access-azure-resources"></a>Wskazówki dotyczące bezpiecznego dostępu do zasobów platformy Azure przy użyciu pliku MSI

## <a name="1-create-msi-on-the-dsvm"></a>1. Utwórz MSI na DSVM 


```
# Pre-requisites: You have already created a Data Science VM in the usual way

# Create identity principal for the VM
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal id of the DSVM
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```


## <a name="2-assign-keyvault-access-permission-to-vm-principal"></a>2. Przypisz uprawnienia dostępu do parametru Keyvault do podmiotu zabezpieczeń maszyny Wirtualnej
```
# Pre-requisites: You have already create an empty Key Vault resource on Azure using Portal or Azure CLI 

# Assign only get and set permission but not the capability to list the keys
az keyvault set-policy --object-id <PrincipalID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="3-access-a-secret-in-the-keyvault-from-the-dsvm"></a>3. Dostęp do klucza tajnego w Keyvault z DSVM

```
# Get the access token for VM
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access Keyvault using access token. 
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="4-access-storage-keys-from-the-dsvm"></a>4. Dostęp do magazynu kluczy z DSVM

```
# Pre-requisites: You have granted your VM's MSI access to use storage account access keys based on instruction from this article:https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage that describes this process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

#Now you can access the data in the storage account from the retrieved Storage account keys
```
## <a name="5-access-keyvault-from-python"></a>5. Keyvault dostępu w języku Python

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a KeyVault client
key_vault_client = KeyVaultClient(
credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
key_vault_uri,  # Your KeyVault URL
"SQLPasswd",       # Name of your secret that already exists in the Key Vault
""              # The version of the secret. Empty string for latest
)
print("My secret value is {}".format(secret.value))
```

## <a name="6-azure-cli-access-from-vm"></a>6. Azure CLI dostępu z maszyny Wirtualnej

```
# With a Managed Service Identity setup on the DSVM, users on the DSVM can execute Azure CLI to perform the authorized functions. Here are commands to access Key Vault from a Azure CLI without having to login to an Azure account. 
# Pre-requisites: MSI is already setup on the DSVM as indicated earlier and specific permission like accessing storage account keys, reading specific secrets and writing new secrets is provided to the MSI . 

# Authenticate to Azure CLI without requiring an Azure Account. 
az login --msi

# Retrieve a secret from Key Vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new Secret in Key Vault
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List Storage Account Access Keys
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
