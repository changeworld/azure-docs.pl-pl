---
title: Store bezpieczny - dostęp poświadczeń na maszynie wirtualnej do nauki o danych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak bezpiecznie przechowywać poświadczenia dostępu na maszynie wirtualnej do nauki o danych. Dowiesz się jak używać tożsamości usługi zarządzanej i usługi Azure Key Vault do przechowywania poświadczeń dostępu.
keywords: głębokiego uczenia i sztucznej Inteligencji, narzędzia do analizy danych, maszyny wirtualnej do nauki o danych, geoprzestrzenna analiza, zespół danych dla celów naukowych
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 79dba586a5f7102d0012c381593551a951f1b38e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60502352"
---
# <a name="store-access-credentials-on-the-data-science-virtual-machine-securely"></a>Store dostępu bezpiecznie poświadczeń na maszynie wirtualnej do nauki o danych

Typowe wyzwania w tworzeniu aplikacji w chmurze jest sposób zarządzania poświadczeniami, które muszą być w kodzie do uwierzytelniania w usługach w chmurze. Zabezpieczanie tych poświadczeń to ważne zadanie. W idealnym przypadku one nigdy nie są wyświetlane na stanowisko pracy dewelopera lub uzyskaj zaewidencjonowane do kontroli źródła. 

[Zarządzane tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) sprawia, że rozwiązywania tego problemu, prostsze, zapewniając Azure usług automatycznie zarządzanych tożsamości w usłudze Azure Active Directory (Azure AD). Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez żadnych poświadczeń w kodzie. 

Jednym ze sposobów, aby zabezpieczyć poświadczenia jest przy użyciu pliku MSI w połączeniu z [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/), zarządzana usługa platformy Azure umożliwiająca bezpieczne przechowywanie wpisów tajnych i kluczy kryptograficznych. Można dostęp do magazynu kluczy za pomocą tożsamości zarządzanej i pobrać autoryzowanych wpisami tajnymi i kluczami szyfrowania z usługi key vault. 

Zarządzanych tożsamości dla zasobów platformy Azure i dokumentacji usługi Key Vault jest pełne zasoby, aby uzyskać szczegółowe informacje na temat tych usług. W pozostałej części tego artykułu przedstawiono podstawowe zastosowanie pakietów MSI i usługi Key Vault na maszynę wirtualną do nauki o danych (DSVM) dostęp do zasobów platformy Azure. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>Utwórz tożsamość zarządzana na maszyny DSVM 


```
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```


## <a name="assign-key-vault-access-permission-to-a-vm-principal"></a>Przypisz uprawnienia dostępu do usługi Key Vault z podmiotem zabezpieczeń maszyny Wirtualnej
```
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI. 

# Assign only get and set permission but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>Dostęp do wpisu tajnego w magazynie kluczy z maszyny DSVM

```
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token. 
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>Dostęp do magazynu kluczy z maszyny DSVM

```
# Prerequisite: You have granted your VM's MSI access to use storage account access keys based on instructions from the article at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```
## <a name="access-the-key-vault-from-python"></a>Dostęp do usługi key vault za pomocą języka Python

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials.
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a Key Vault client.
key_vault_client = KeyVaultClient(
credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
key_vault_uri,  # Your key vault URL.
"SQLPasswd",       # The name of your secret that already exists in the key vault.
""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>Dostęp do magazynu kluczy z wiersza polecenia platformy Azure

```
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. Here are commands to access the key vault from Azure CLI without having to log in to an Azure account. 
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permission, like accessing storage account keys, reading specific secrets, and writing new secrets, is provided to the MSI. 

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
