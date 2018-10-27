---
title: Zabezpieczanie Twojej domeny zarządzanej usługi Azure Active Directory Domain Services | Dokumentacja firmy Microsoft
description: Zabezpieczanie Twojej domeny zarządzanej
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: ergreenl
ms.openlocfilehash: 1dea3def00423ecf092562aa47c76258316194a9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50159064"
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
1. Wyłączając obsługę NTLM w wersji 1 w domenie zarządzanej.
2. Wyłączyć synchronizację skrótów haseł NTLM z lokalnej usługi AD.
3. Wyłączając protokół TLS w wersji 1 w domenie zarządzanej.

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
