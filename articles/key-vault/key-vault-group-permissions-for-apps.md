---
title: Udziel uprawnień do wielu aplikacjom dostępu do magazynu kluczy platformy Azure — Azure Key Vault | Microsoft Docs
description: Dowiedz się, jak udzielić uprawnienia wielu aplikacjom dostępu do magazynu kluczy
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 07ee544057ffeb0a5859cc771b124523ec79c9c0
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976399"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>Udzielanie wielu aplikacjom dostępu do magazynu kluczy

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zasady kontroli dostępu mogą służyć do udzielania dostępu do magazynu kluczy kilku aplikacjom. Zasady kontroli dostępu mogą obsługiwać do 1024 aplikacji i konfiguruje się w następujący sposób:

1. Utwórz Azure Active Directory grupę zabezpieczeń. 
2. Dodaj wszystkie skojarzone jednostki usługi aplikacji do grupy zabezpieczeń.
3. Przyznaj grupie zabezpieczeń dostęp do Key Vault.

## <a name="prerequisites"></a>Wymagania wstępne

Poniżej przedstawiono wymagania wstępne:
* [Zainstalowanie programu Azure PowerShell](/powershell/azure/overview).
* [Zainstaluj moduł Azure Active Directory v2 PowerShell](https://www.powershellgallery.com/packages/AzureAD).
* Uprawnienia do tworzenia/edytowania grup w dzierżawie Azure Active Directory. Jeśli nie masz uprawnień, może być konieczne skontaktowanie się z administratorem Azure Active Directory. Aby uzyskać szczegółowe informacje o uprawnieniach dostępu Key Vault [, zobacz temat informacje o kluczach, wpisach tajnych i certyfikatach Azure Key Vault](about-keys-secrets-and-certificates.md) .

## <a name="granting-key-vault-access-to-applications"></a>Udzielanie Key Vault dostępu do aplikacji

Uruchom następujące polecenia w programie PowerShell:

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

Jeśli musisz udzielić różnego zestawu uprawnień dla grupy aplikacji, utwórz oddzielną Azure Active Directory grupę zabezpieczeń dla takich aplikacji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [zabezpieczania magazynu kluczy](key-vault-secure-your-key-vault.md).
