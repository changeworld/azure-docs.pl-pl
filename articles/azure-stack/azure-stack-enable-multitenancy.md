---
title: Wielodostępność w usłudze Azure Stack
description: Dowiedz się, jak obsługiwać wiele katalogów usługi Azure Active Directory w usłudze Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2019
ms.author: patricka
ms.reviewer: bryanr
ms.lastreviewed: 03/04/2019
ms.openlocfilehash: 78370dba4c54df5636e6d17d23ce7cc9d7671c15
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487706"
---
# <a name="multi-tenancy-in-azure-stack"></a>Wielodostępność w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Można skonfigurować w usłudze Azure Stack w celu obsługi użytkowników z wielu dzierżaw usługi Azure Active Directory (Azure AD) do korzystania z usług w usłudze Azure Stack. Na przykład rozważmy następujący scenariusz:

- Jesteś administratorem usługi domeny contoso.onmicrosoft.com, gdzie zainstalowano usługi Azure Stack.
- Jan jest administratorem Directory fabrikam.onmicrosoft.com, gdzie znajdują się użytkownicy-goście.
- Firma firmy Mary odbiera IaaS i PaaS z Twojej firmy i wymaga umożliwić użytkownikom z katalogu gościa (fabrikam.onmicrosoft.com) Zaloguj się i korzystać z zasobów usługi Azure Stack w contoso.onmicrosoft.com.

Ten przewodnik zawiera kroki wymagane w kontekście tego scenariusza, aby skonfigurować wielu dzierżawców w usłudze Azure Stack. W tym scenariuszu możesz i Mary należy wykonać czynności, aby umożliwić użytkownikom z firmy Fabrikam logować się i korzystać z usług z wdrożenia usługi Azure Stack w firmie Contoso.  

## <a name="enable-multi-tenancy"></a>Włączanie wielodostępu

Istnieje kilka wymagań wstępnych na wypadek, przed rozpoczęciem konfigurowania wielu dzierżawców w usłudze Azure Stack:
  
 - Użytkownik i Mary skoordynować czynności administracyjne w katalogu w którym zainstalowano usługi Azure Stack (contoso) i katalog gości (Fabrikam).  
 - Upewnij się, że masz [zainstalowane](azure-stack-powershell-install.md) i [skonfigurowane](azure-stack-powershell-configure-admin.md) programu PowerShell dla usługi Azure Stack.
 - [Pobierz narzędzia usługi Azure Stack](azure-stack-powershell-download.md)i zaimportować moduły Connect i tożsamości:

    ```powershell  
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

### <a name="configure-azure-stack-directory"></a>Skonfiguruj katalog usługi Azure Stack

W tej sekcji służy do konfigurowania usługi Azure Stack, aby umożliwić logowania z firmy Fabrikam w usłudze Azure AD directory dzierżaw.

Dołączanie dzierżawy katalogu gościa (Fabrikam) do usługi Azure Stack przez skonfigurowanie usługi Azure Resource Manager, aby akceptować użytkowników i jednostki z gościa dzierżawy katalogu usług.

Administrator usługi domeny contoso.onmicrosoft.com uruchom następujące polecenia.

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group. 
$location = "local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName
```

### <a name="configure-guest-directory"></a>Skonfiguruj katalog gości

Raz administratora platformy Azure Stack / operator ma włączoną katalogu firmy Fabrikam do użycia z usługą Azure Stack, Joanna należy zarejestrować Fabrikam katalogu dzierżawy usługi Azure Stack.

#### <a name="registering-azure-stack-with-the-guest-directory"></a>Rejestrowanie usługi Azure Stack przy użyciu katalog gości

Joanna administratorem katalogu Fabrikam uruchom następujące polecenia w fabrikam.onmicrosoft.com katalogu gościa.

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
```

> [!IMPORTANT]
> Administrator usługi Azure Stack instalacji aktualizacji lub nowych usług w przyszłości może być konieczne ponownie uruchom ten skrypt.
>
> Ten skrypt należy uruchomić ponownie w dowolnym momencie, aby sprawdzić stan aplikacji usługi Azure Stack w Twoim katalogu.
>
> Zauważyliście problemy z tworzeniem maszyn wirtualnych na dyski zarządzane (zostanie wprowadzony w aktualizacji 1808) nowej **dostawcy zasobów dysku** dodano, co wymaga tego skryptu do ponownego uruchomienia.

### <a name="direct-users-to-sign-in"></a>Bezpośrednie użytkownikom na logowanie

Teraz, gdy użytkownik i Mary zostały wykonane kroki, aby dołączyć Mary katalogu, Joanna można kierować Fabrikam użytkownikom zalogowanie się.  Firma Fabrikam (oznacza to, że użytkownicy z sufiksem fabrikam.onmicrosoft.com) logowania, odwiedzając stronę https://portal.local.azurestack.external.  

Joanna skieruje dowolne [obce podmioty zabezpieczeń](../role-based-access-control/rbac-and-directory-admin-roles.md) w katalogu firmy Fabrikam (czyli użytkownicy w katalogu firmy Fabrikam przyrostka fabrikam.onmicrosoft.com) do logowania za pomocą https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.  Jeśli nie korzystają z tego adresu URL są wysyłane do ich katalog domyślny (Fabrikam) i komunikat o błędzie stwierdzający, że ich administrator nie wyraził zgodę.

## <a name="disable-multi-tenancy"></a>Wyłączanie obsługi wielu dzierżawców

Jeśli nie chcesz już wielu dzierżaw w usłudze Azure Stack, można wyłączyć obsługi wielu dzierżawców, wykonując następujące kroki w kolejności:

1. Jako administrator katalogu gościa (Mary w tym scenariuszu), uruchom *AzsWithMyDirectoryTenant Wyrejestruj*. Polecenie cmdlet powoduje odinstalowanie wszystkich aplikacji usługi Azure Stack z nowego katalogu.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Jako administrator usługi Azure Stack, (możesz w tym scenariuszu), uruchom *AzSGuestDirectoryTenant Wyrejestruj*. 

    ``` PowerShell  
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
    $ResourceGroupName = "system.local"
    
    Unregister-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
     -DirectoryTenantName $azureStackDirectoryTenant `
     -GuestDirectoryTenantName $guestDirectoryTenantToBeDecommissioned `
     -ResourceGroupName $ResourceGroupName
    ```

    > [!WARNING]
    > Wyłącz kroki wielodostępu muszą być wykonywane w kolejności. Krok #1 kończy się niepowodzeniem, jeśli najpierw wykonaniu kroku #2.

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie delegowanych dostawców](azure-stack-delegated-provider.md)
- [Kluczowe pojęcia usługi Azure Stack](azure-stack-key-features.md)
- [Zarządzanie użyciem i rozliczeniami usługi Azure Stack jako dostawca usług w chmurze](azure-stack-add-manage-billing-as-a-csp.md)
- [Dodawanie dzierżawy na potrzeby użycia i rozliczeń w usłudze Azure Stack](azure-stack-csp-howto-register-tenants.md)
