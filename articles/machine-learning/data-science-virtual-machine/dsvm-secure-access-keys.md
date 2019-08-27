---
title: Store bezpieczny - dostęp poświadczeń na maszynie wirtualnej do nauki o danych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak bezpiecznie przechowywać poświadczenia dostępu na maszynie wirtualnej do nauki o danych. Dowiesz się, jak używać tożsamości usługi zarządzanej i Azure Key Vault do przechowywania poświadczeń dostępu.
keywords: głębokiego uczenia i sztucznej Inteligencji, narzędzia do analizy danych, maszyny wirtualnej do nauki o danych, geoprzestrzenna analiza, zespół danych dla celów naukowych
services: machine-learning
documentationcenter: ''
author: vijetajo
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
ms.author: vijetaj
ms.openlocfilehash: 1374cbef41f40ea270f3c4d84c68d08e7db095bc
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051621"
---
# <a name="store-access-credentials-securely-on-a-data-science-virtual-machine"></a>Bezpieczne przechowywanie poświadczeń dostępu na Data Science Virtual Machine

Często kod w aplikacjach w chmurze zawiera poświadczenia służące do uwierzytelniania w usługach w chmurze. Zarządzanie tymi poświadczeniami i ich Zabezpieczanie to dobrze znane wyzwanie w tworzeniu aplikacji w chmurze. W idealnym przypadku poświadczenia nigdy nie powinny występować na stacjach roboczych deweloperów ani nie są zaewidencjonowane do kontroli źródła.

[Zarządzane tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) ułatwiają rozwiązanie tego problemu, dając usługi platformy Azure automatycznie zarządzaną tożsamość w Azure Active Directory (Azure AD). Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD bez konieczności przechowywania poświadczeń w kodzie.

Jednym ze sposobów zabezpieczania poświadczeń jest używanie Instalator Windows (MSI) w połączeniu z [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/), zarządzaną usługą platformy Azure, która umożliwia bezpieczne przechowywanie tajemnic i kluczy kryptograficznych. Możesz uzyskać dostęp do magazynu kluczy przy użyciu tożsamości zarządzanej, a następnie pobrać wpisy tajne i klucze kryptograficzne z magazynu kluczy.

Dokumentacja dotycząca zarządzanych tożsamości dla zasobów platformy Azure i Key Vault składa się ze wszechstronnego zasobu, aby uzyskać szczegółowe informacje na temat tych usług. W pozostałej części tego artykułu przedstawiono podstawowe zastosowanie pakietów MSI i usługi Key Vault na maszynę wirtualną do nauki o danych (DSVM) dostęp do zasobów platformy Azure. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>Utwórz tożsamość zarządzana na maszyny DSVM 


```
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```


## <a name="assign-key-vault-access-permissions-to-a-vm-principal"></a>Przypisywanie uprawnień dostępu Key Vault do podmiotu maszyny wirtualnej
```
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI.

# Assign only get and set permissions but not the capability to list the keys.
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
# Prerequisite: You have granted your VMs MSI access to use storage account access keys based on instructions at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

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
    # The name of your secret that already exists in the key vault.
    "SQLPasswd",
    ""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>Dostęp do magazynu kluczy z wiersza polecenia platformy Azure

```
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. The following commands enable access to the key vault from Azure CLI without requiring login to an Azure account.
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permissions, like accessing storage account keys, reading specific secrets, and writing new secrets, are provided to the MSI.

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
