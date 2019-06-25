---
title: Przyznanie uprawnień do wielu aplikacjom dostęp do usługi Azure key vault — usługi Azure Key Vault | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przyznać uprawnienia do wielu aplikacjom dostęp do magazynu kluczy
services: key-vault
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: b1d0b0948e089d41f460ac2a54150ee51333f87c
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "64721983"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>Przyznaj kilka aplikacji, dostęp do magazynu kluczy

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zasady kontroli dostępu mogą służyć do udzielenia kilka aplikacji, dostęp do magazynu kluczy. Zasady kontroli dostępu może obsłużyć maksymalnie 1024 aplikacji i jest skonfigurowany w następujący sposób:

1. Utwórz grupę zabezpieczeń usługi Azure Active Directory. 
2. Dodaj wszystkie aplikacje skojarzoną z jednostki usługi do grupy zabezpieczeń.
3. Przyznaj grupy zabezpieczeń dostęp do usługi Key Vault.

## <a name="prerequisites"></a>Wymagania wstępne

Poniżej przedstawiono wymagania wstępne:
* [Zainstalowanie programu Azure PowerShell](/powershell/azure/overview).
* [Instalowanie modułu programu PowerShell usługi Azure Active Directory w wersji 2](https://www.powershellgallery.com/packages/AzureAD).
* Uprawnienia do tworzenia/edycji grup w dzierżawie usługi Azure Active Directory. Jeśli nie masz uprawnień, może być konieczne skontaktuj się z administratorem usługi Azure Active Directory. Zobacz [klucze, wpisy tajne i certyfikaty usługi Azure Key Vault](about-keys-secrets-and-certificates.md) szczegółowe informacje na temat usługi Key Vault zasady uprawnień dostępu.

## <a name="granting-key-vault-access-to-applications"></a>Udzielanie dostępu do magazynu kluczy do aplikacji

W programie PowerShell, uruchom następujące polecenia:

```powershell
# Connect to Azure AD 
Connect-AzureAD 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

Jeśli musisz przyznać inny zestaw uprawnień do grupy aplikacji, należy utworzyć osobne grupy zabezpieczeń usługi Azure Active Directory na potrzeby takich aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat [zabezpieczanie własnego magazynu kluczy](key-vault-secure-your-key-vault.md).
