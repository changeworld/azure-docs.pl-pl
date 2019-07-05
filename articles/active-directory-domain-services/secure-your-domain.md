---
title: Zabezpieczanie Twojej domeny zarządzanej usługi Azure Active Directory Domain Services | Dokumentacja firmy Microsoft
description: Zabezpieczanie domeny zarządzanej
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: e94cd9ca049cfdfd2321ce046714506ed1f23390
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483274"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Zabezpieczanie Twojej domeny zarządzanej usług domenowych Azure AD
Ten artykuł ułatwia zabezpieczanie Twojej domeny zarządzanej. Można wyłączyć opcję użycia mechanizmów szyfrowania słabe i wyłączyć synchronizację skrótów poświadczeń NTLM.

## <a name="install-the-required-powershell-modules"></a>Zainstaluj wymagane moduły programu PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Instalowanie i konfigurowanie programu Azure AD PowerShell
Postępuj zgodnie z instrukcjami w artykule, aby [Instalowanie modułu Azure AD PowerShell i łączenie z usługą Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalowanie i konfigurowanie programu Azure PowerShell
Postępuj zgodnie z instrukcjami w artykule, aby [Instalowanie modułu Azure PowerShell i nawiązać połączenie z subskrypcją platformy Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>Wyłączyć słaby mechanizmy szyfrowania i synchronizacji skrótów poświadczeń NTLM
Użyj następującego skryptu programu PowerShell do:
1. Wyłączając obsługę NTLM w wersji 1 w domenie zarządzanej.
2. Wyłączyć synchronizację skrótów haseł NTLM z lokalnej usługi AD.
3. Wyłączając protokół TLS w wersji 1 w domenie zarządzanej.

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

> [!IMPORTANT]
> Użytkowników (i kont usług) nie można wykonać prostych powiązań LDAP, wyłączenie synchronizacji skrótów haseł NTLM w wystąpieniu usługi Azure AD Domain Services.  Aby uzyskać więcej informacji na temat wyłączenie synchronizacji skrótów haseł NTLM, przeczytaj [zabezpieczanie Twojej domeny zarządzanej usług domenowych Azure AD](secure-your-domain.md).
>
>

## <a name="next-steps"></a>Kolejne kroki
* [Omówienie synchronizacji w usługach domenowych Azure AD](synchronization.md)
