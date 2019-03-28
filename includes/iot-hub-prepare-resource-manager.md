---
author: robinsh
ms.author: robin.shahan
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: c30f31903e59c505d778c95c58adbb48af0db96f
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505704"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Przygotowanie do uwierzytelniania żądań w usłudze Azure Resource Manager
Musisz uwierzytelnić wszystkich operacji wykonywanych na zasobach za pomocą [usługi Azure Resource Manager] [ lnk-authenticate-arm] za pomocą usługi Azure Active Directory (AD). Najprostszym sposobem skonfigurowania tego ustawienia jest użycie programu PowerShell lub wiersza polecenia platformy Azure.

Zainstaluj [poleceń cmdlet programu Azure PowerShell] [ lnk-powershell-install] przed kontynuowaniem.

Poniższe kroki pokazują sposób konfigurowania uwierzytelniania hasła dla aplikacji usługi AD przy użyciu programu PowerShell. Można uruchomić następujące polecenia w sesji programu PowerShell standardowe.

1. Zaloguj się do subskrypcji platformy Azure, używając następującego polecenia:

    ```powershell
    Connect-AzAccount
    ```

1. Jeśli masz wiele subskrypcji platformy Azure, logowanie do platformy Azure zapewnia dostęp do wszystkich subskrypcji platformy Azure skojarzonych z poświadczeniami użytkownika. Aby wyświetlić listę subskrypcji platformy Azure, która jest dostępna do użycia, użyj następującego polecenia:

    ```powershell
    Get-AzSubscription
    ```

    Użyj następującego polecenia, aby wybrać subskrypcję, której chcesz używać do uruchamiania polecenia do zarządzania Centrum IoT hub. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Zwróć uwagę na Twoje **TenantId** i **SubscriptionId**. Będą one potrzebne później.
3. Utwórz nową aplikację usługi Azure Active Directory, używając następującego polecenia, zastępując symbole zastępcze:
   
   * **{Nazwa wyświetlana}:** nazwę wyświetlaną dla swojej aplikacji, takich jak **MySampleApp**
   * **{Adres URL strony głównej}:** adres URL strony głównej aplikacji takich jak **http:\//mysampleapp/home**. Ten adres URL nie musi wskazywać rzeczywistej aplikacji.
   * **{Identyfikator aplikacji:}** Unikatowy identyfikator **http:\//mysampleapp**. Ten adres URL nie musi wskazywać rzeczywistej aplikacji.
   * **{Password}:** Hasło używane do uwierzytelniania za pomocą aplikacji.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Zwróć uwagę na **ApplicationId** z utworzonej aplikacji. Jest to potrzebne później.
5. Utwórz nową jednostkę usługi przy użyciu następującego polecenia, zastępując **{MyApplicationId}** z **ApplicationId** z poprzedniego kroku:
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Konfigurowanie przypisania roli, używając następującego polecenia, zastępując **{MyApplicationId}** za pomocą usługi **ApplicationId**.
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Możesz teraz Zakończono tworzenie aplikacji usługi Azure AD, która pozwala na uwierzytelnianie z niestandardowych C# aplikacji. W dalszej części tego samouczka potrzebne są następujące wartości:

* TenantId
* SubscriptionId
* ApplicationId
* Hasło

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/azurerm/install-Az-ps
