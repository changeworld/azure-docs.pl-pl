---
title: Przyznanie uprawnień do wielu aplikacjom dostęp do usługi Azure key vault | Dokumentacja firmy Microsoft
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
ms.date: 10/09/2018
ms.author: ambapat
ms.openlocfilehash: 639dfb6e3231a5eba3d6ecb9cd0198f5718b4aef
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079025"
---
# <a name="grant-permission-to-many-applications-to-access-a-key-vault"></a>Udziel uprawnień do wielu aplikacji do dostępu do magazynu kluczy

## <a name="q-i-have-several-applications-that-need-to-access-a-key-vault-how-can-i-give-these-applications-up-to-1024-access-to-key-vault"></a>Pytanie: Mam kilka aplikacji, które chcą korzystać z magazynu kluczy, jak I przyznać te aplikacje (maksymalnie 1024) prawa dostępu do usługi Key Vault?

Zasady kontroli dostępu do usługi Key Vault obsługuje maksymalnie 1024 wpisów. Można jednak utworzyć grupę zabezpieczeń usługi Azure Active Directory. Dodaj wszystkie jednostki usługi skojarzonego do tej grupy zabezpieczeń, a następnie przyznać dostęp do tej grupy zabezpieczeń w usłudze Key Vault.

Poniżej przedstawiono wymagania wstępne:
* [Instalowanie modułu programu PowerShell usługi Azure Active Directory w wersji 2](https://www.powershellgallery.com/packages/AzureAD).
* [Zainstalowanie programu Azure PowerShell](/powershell/azure/overview).
* Aby uruchomić następujące polecenia, niezbędne są uprawnienia do tworzenia/edycji grup w dzierżawie usługi Azure Active Directory. Jeśli nie masz uprawnień, może być konieczne skontaktuj się z administratorem usługi Azure Active Directory. Zobacz [klucze, wpisy tajne i certyfikaty usługi Azure Key Vault](about-keys-secrets-and-certificates.md) szczegółowe informacje na temat usługi Key Vault zasady uprawnień dostępu.

Teraz uruchom następujące polecenia w programie PowerShell.

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
