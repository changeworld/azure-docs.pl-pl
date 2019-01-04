---
title: Przyznanie uprawnień do wielu aplikacjom dostęp do usługi Azure key vault — usługi Azure Key Vault | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przyznać uprawnienia do wielu aplikacjom dostęp do magazynu kluczy
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: ambapat
ms.openlocfilehash: ed0d40ab62a7d75a993ce0bbdd83a5e5ec4a633a
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998476"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>Przyznaj kilka aplikacji, dostęp do magazynu kluczy

Zasady kontroli dostępu mogą służyć do udzielenia kilka aplikacji, dostęp do magazynu kluczy. Zasady kontroli dostępu może obsłużyć maksymalnie 1024 aplikacji i jest skonfigurowany w następujący sposób:

1. Utwórz grupę zabezpieczeń usługi Azure Active Directory. 
2. Dodaj wszystkie aplikacje skojarzoną z jednostki usługi do grupy zabezpieczeń.
3. Przyznaj grupy zabezpieczeń dostęp do usługi Key Vault.

Poniżej przedstawiono wymagania wstępne:
* [Instalowanie modułu programu PowerShell usługi Azure Active Directory w wersji 2](https://www.powershellgallery.com/packages/AzureAD).
* [Zainstalowanie programu Azure PowerShell](/powershell/azure/overview).
* Aby uruchomić następujące polecenia, niezbędne są uprawnienia do tworzenia/edycji grup w dzierżawie usługi Azure Active Directory. Jeśli nie masz uprawnień, może być konieczne skontaktuj się z administratorem usługi Azure Active Directory. Zobacz [klucze, wpisy tajne i certyfikaty usługi Azure Key Vault](about-keys-secrets-and-certificates.md) szczegółowe informacje na temat usługi Key Vault zasady uprawnień dostępu.

Teraz uruchom następujące polecenia w programie PowerShell:

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
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

Jeśli musisz przyznać inny zestaw uprawnień do grupy aplikacji, należy utworzyć osobne grupy zabezpieczeń usługi Azure Active Directory na potrzeby takich aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat [zabezpieczanie własnego magazynu kluczy](key-vault-secure-your-key-vault.md).
