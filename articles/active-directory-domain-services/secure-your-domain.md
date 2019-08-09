---
title: Zabezpiecz domenę zarządzaną Azure Active Directory Domain Services | Microsoft Docs
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
ms.openlocfilehash: 923ecae9dc649b8f5cdcfd447b78fdec0805927a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879165"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Zabezpiecz domenę zarządzaną Azure AD Domain Services
Ten artykuł pomaga zabezpieczyć domenę zarządzaną. Można wyłączyć użycie słabych mechanizmów szyfrowania i wyłączyć synchronizację skrótów poświadczeń NTLM.

## <a name="install-the-required-powershell-modules"></a>Instalowanie wymaganych modułów programu PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Instalowanie i Konfigurowanie programu Azure AD PowerShell
Postępuj zgodnie z instrukcjami w artykule, aby [zainstalować moduł Azure AD PowerShell i nawiązać połączenie z usługą Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalowanie i konfigurowanie programu Azure PowerShell
Postępuj zgodnie z instrukcjami w artykule, aby [zainstalować moduł Azure PowerShell i nawiązać połączenie z subskrypcją platformy Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>Wyłącz słabe mechanizmy szyfrowania i synchronizację skrótów poświadczeń NTLM
Użyj następującego skryptu programu PowerShell, aby:

1. Wyłączając obsługę NTLM w wersji 1 w domenie zarządzanej.
2. Wyłącz synchronizację skrótów haseł NTLM z lokalnej usługi AD.
3. Wyłączając protokół TLS w wersji 1 w domenie zarządzanej.

Jeśli wystąpi błąd z `Get-AzResource` poleceniem, że zasób *Microsoft. AAD/DomainServices* nie istnieje, podnieś [poziom dostępu do zarządzania wszystkimi subskrypcjami platformy Azure i grupami zarządzania](../role-based-access-control/elevate-access-global-admin.md).

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
> Użytkownicy (i konta usług) nie mogą wykonać prostych powiązań LDAP, jeśli synchronizacja skrótów haseł NTLM została wyłączona w wystąpieniu Azure AD Domain Services.  Aby uzyskać więcej informacji na temat wyłączania synchronizacji skrótów haseł NTLM, przeczytaj artykuł [Zabezpieczanie domeny zarządzanej usług Azure AD Domain Services](secure-your-domain.md).
>
>

## <a name="next-steps"></a>Następne kroki
* [Omówienie synchronizacji w Azure AD Domain Services](synchronization.md)
