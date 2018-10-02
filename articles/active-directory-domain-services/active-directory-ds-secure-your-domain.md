---
title: Zabezpieczanie Twojej domeny zarządzanej usługi Azure Active Directory Domain Services | Dokumentacja firmy Microsoft
description: Zabezpieczanie Twojej domeny zarządzanej
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: maheshu
ms.openlocfilehash: 9dbdb5569a6b5718f01b82661f9323812ab16c27
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019826"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Zabezpieczanie Twojej domeny zarządzanej usług domenowych Azure AD
Ten artykuł ułatwia zabezpieczanie Twojej domeny zarządzanej. Można wyłączyć opcję użycia mechanizmów szyfrowania słabe i wyłączyć synchronizację skrótów poświadczeń NTLM.

## <a name="install-the-required-powershell-modules"></a>Zainstaluj wymagane moduły programu PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Instalowanie i konfigurowanie programu Azure AD PowerShell
Postępuj zgodnie z instrukcjami w artykule, aby [Instalowanie modułu Azure AD PowerShell i łączenie z usługą Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalowanie i konfigurowanie programu Azure PowerShell
Postępuj zgodnie z instrukcjami w artykule, aby [Instalowanie modułu Azure PowerShell i nawiązać połączenie z subskrypcją platformy Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>Wyłączyć słaby mechanizmy szyfrowania i synchronizacji skrótów poświadczeń NTLM
Użyj następującego skryptu programu PowerShell do:
1. Wyłączanie protokołu NTLM v1 obsługi w domenie zarządzanej.
2. Wyłączyć synchronizację skrótów haseł NTLM z lokalnej usługi AD.
3. Wyłączenie protokołu TLS w wersji 1 w domenie zarządzanej.

```powershell
// Login to your Azure AD tenant
Login-AzureRmAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzureRmResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzureRmResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

## <a name="next-steps"></a>Kolejne kroki
* [Omówienie synchronizacji w usługach domenowych Azure AD](active-directory-ds-synchronization.md)
