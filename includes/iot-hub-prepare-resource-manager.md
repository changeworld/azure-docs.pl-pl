---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 4eb794fa35164e3f86a5e3d6f67d446321f91f0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67133076"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Przygotowanie do uwierzytelniania żądań usługi Azure Resource Manager
Należy uwierzytelnić wszystkie operacje wykonywane na zasobach przy użyciu [usługi Azure Resource Manager][lnk-authenticate-arm] z usługą Azure Active Directory (AD). Najprostszym sposobem skonfigurowania tego jest użycie programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Zainstaluj [polecenia cmdlet programu Azure PowerShell][lnk-powershell-install] przed kontynuowaniem.

Poniższe kroki pokazują, jak skonfigurować uwierzytelnianie hasłem dla aplikacji AD przy użyciu programu PowerShell. Polecenia te można uruchamiać w standardowej sesji programu PowerShell.

1. Zaloguj się do subskrypcji platformy Azure przy użyciu następującego polecenia:

    ```powershell
    Connect-AzAccount
    ```

1. Jeśli masz wiele subskrypcji platformy Azure, zalogowanie się na platformie Azure zapewnia dostęp do wszystkich subskrypcji platformy Azure skojarzonych z poświadczeniami. Użyj następującego polecenia, aby wyświetlić listę subskrypcji platformy Azure dostępnych do użycia:

    ```powershell
    Get-AzSubscription
    ```

    Użyj następującego polecenia, aby wybrać subskrypcję, której chcesz użyć do uruchomienia poleceń do zarządzania centrum IoT Hub. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Zanotuj identyfikator i **identyfikator subskrypcji.** **TenantId** Będą one potrzebne później.
3. Utwórz nową aplikację usługi Azure Active Directory przy użyciu następującego polecenia, zastępując posiadaczy miejsc:
   
   * **{Nazwa wyświetlana}:** nazwa wyświetlana aplikacji, taka jak **MySampleApp**
   * **{Adres URL strony głównej}:** adres URL strony głównej aplikacji, taki jak **http:\//mysampleapp/home**. Ten adres URL nie musi wskazywać prawdziwej aplikacji.
   * **{Identyfikator aplikacji}:** Unikatowy identyfikator, taki jak **http:\//mysampleapp**. Ten adres URL nie musi wskazywać prawdziwej aplikacji.
   * **{Hasło}:** Hasło używane do uwierzytelniania za pomocą aplikacji.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Zanotuj **identyfikator applicationid** utworzonej aplikacji. Potrzebujesz tego później.
5. Utwórz nową jednostkę usługi za pomocą następującego polecenia, zastępując **{MyApplicationId}** **identyfikatorem aplikacji** z poprzedniego kroku:
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Skonfiguruj przypisanie roli za pomocą następującego polecenia, zastępując **{MyApplicationId}** identyfikatorem **aplikacji**.
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Zakończono tworzenie aplikacji usługi Azure AD, która umożliwia uwierzytelnianie z niestandardowej aplikacji języka C#. Potrzebne są następujące wartości w dalszej części tego samouczka:

* TenantId
* SubscriptionId
* ApplicationId
* Hasło

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: /powershell/azure/install-az-ps
