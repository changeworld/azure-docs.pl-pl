---
title: Bezpieczne usługi domenowe usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak wyłączyć słabe szyfry, stare protokoły i synchronizację skrótów haseł NTLM dla domeny zarządzanej usług domenowych Usługi domenowe Active Directory platformy Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 678f88d72511074cceddb5166fd4b0c2a4254507
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518983"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>Wyłączanie słabych szyfrów i synchronizacji skrótów haseł w celu zabezpieczenia domeny zarządzanej usług ad.in.

Domyślnie usługi domenowe Active Directory platformy Azure (Usługi Azure AD DS) umożliwiają korzystanie z szyfrów, takich jak NTLM w wersji 1 i TLS v1. Te szyfry mogą być wymagane dla niektórych starszych aplikacji, ale są uważane za słabe i można je wyłączyć, jeśli ich nie potrzebujesz. Jeśli masz lokalną łączność hybrydową przy użyciu usługi Azure AD Connect, można również wyłączyć synchronizację skrótów haseł NTLM.

W tym artykule pokazano, jak wyłączyć szyfry NTLM v1 i TLS v1 i wyłączyć synchronizację skrótów haseł NTLM.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł, potrzebne są następujące zasoby:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby [utwórz i skonfiguruj wystąpienie usług domenowych Active Directory platformy Azure][create-azure-ad-ds-instance].
* Zainstaluj i skonfiguruj program Azure PowerShell.
    * W razie potrzeby postępuj zgodnie z instrukcjami, aby [zainstalować moduł programu Azure PowerShell i połączyć się z subskrypcją platformy Azure.](/powershell/azure/install-az-ps)
    * Upewnij się, że logujesz się do subskrypcji platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount.][Connect-AzAccount]
* Instalowanie i konfigurowanie programu Azure AD PowerShell.
    * W razie potrzeby postępuj zgodnie z instrukcjami, aby [zainstalować moduł programu Azure AD PowerShell i połączyć się z usługą Azure AD](/powershell/azure/active-directory/install-adv2).
    * Upewnij się, że logujesz się do dzierżawy usługi Azure AD przy użyciu polecenia cmdlet [Connect-AzureAD.][Connect-AzureAD]

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Wyłączanie słabych szyfrów i synchronizacji skrótów haseł NTLM

Aby wyłączyć słabe mechanizmy szyfrowania i synchronizację skrótów poświadczeń NTLM, zaloguj się do konta platformy Azure, a następnie pobierz zasób usługi Azure AD DS przy użyciu polecenia cmdlet [Get-AzResource:][Get-AzResource]

> [!TIP]
> Jeśli zostanie wyświetlony błąd przy użyciu polecenia [Get-AzResource,][Get-AzResource] którego nie ma *zasób Microsoft.AAD/DomainServices,* [zwiększ poziom dostępu do zarządzania wszystkimi subskrypcjami platformy Azure i grupami zarządzania.][global-admin]

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Następnie zdefiniuj *ustawienia zabezpieczeń bezpieczeństwa domeny,* aby skonfigurować następujące opcje zabezpieczeń:

1. Wyłącz obsługę NTLM w wersji 1.
2. Wyłącz synchronizację skrótów haseł NTLM z lokalnej usługi AD.
3. Wyłącz TLS v1.

> [!IMPORTANT]
> Użytkownicy i konta usług nie mogą wykonywać prostych powiązań LDAP, jeśli wyłączysz synchronizację skrótów haseł NTLM w domenie zarządzanej usług Azure AD DS. Jeśli chcesz wykonać proste powiązania LDAP, nie ustawiaj opcji *konfiguracji zabezpieczeń "SyncNtlmPasswords"="Wyłączone";* w poniższym poleceniu.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Na koniec zastosuj zdefiniowane ustawienia zabezpieczeń do domeny zarządzanej usługi Azure AD DS przy użyciu polecenia cmdlet [Set-AzResource.][Set-AzResource] Określ zasób usługi Azure AD DS od pierwszego kroku i ustawienia zabezpieczeń z poprzedniego kroku.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Zajmuje kilka chwil, aby ustawienia zabezpieczeń, które mają być stosowane do domeny zarządzanej usługi Azure AD DS.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o procesie synchronizacji, zobacz [Jak obiekty i poświadczenia są synchronizowane w domenie zarządzanej usług Azure AD DS][synchronization].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD