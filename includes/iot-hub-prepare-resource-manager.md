---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: 2eacb55eaf355a4eef17b9e16075d8d12167266d
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165723"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Przygotowanie do uwierzytelniania żądań w usłudze Azure Resource Manager
Musisz uwierzytelnić wszystkich operacji wykonywanych na zasobach za pomocą [usługi Azure Resource Manager] [ lnk-authenticate-arm] za pomocą usługi Azure Active Directory (AD). Najprostszym sposobem skonfigurowania tego ustawienia jest użycie programu PowerShell lub wiersza polecenia platformy Azure.

Zainstaluj [poleceń cmdlet programu Azure PowerShell] [ lnk-powershell-install] przed kontynuowaniem.

Poniższe kroki pokazują sposób konfigurowania uwierzytelniania hasła dla aplikacji usługi AD przy użyciu programu PowerShell. Można uruchomić następujące polecenia w sesji programu PowerShell standardowe.

1. Zaloguj się do subskrypcji platformy Azure, używając następującego polecenia:

    ```powershell
    Connect-AzureRmAccount
    ```

1. Jeśli masz wiele subskrypcji platformy Azure, logowanie do platformy Azure zapewnia dostęp do wszystkich subskrypcji platformy Azure skojarzonych z poświadczeniami użytkownika. Aby wyświetlić listę subskrypcji platformy Azure, która jest dostępna do użycia, użyj następującego polecenia:

    ```powershell
    Get-AzureRMSubscription
    ```

    Użyj następującego polecenia, aby wybrać subskrypcję, której chcesz używać do uruchamiania polecenia do zarządzania Centrum IoT hub. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Zwróć uwagę na Twoje **TenantId** i **SubscriptionId**. Będą one potrzebne później.
3. Utwórz nową aplikację usługi Azure Active Directory, używając następującego polecenia, zastępując symbole zastępcze:
   
   * **{Nazwa wyświetlana}:** nazwę wyświetlaną dla swojej aplikacji, takich jak **MySampleApp**
   * **{Adres URL strony głównej}:** adres URL strony głównej aplikacji takich jak **http://mysampleapp/home**. Ten adres URL nie musi wskazywać rzeczywistej aplikacji.
   * **{Identyfikator aplikacji}:** Unikatowy identyfikator **http://mysampleapp**. Ten adres URL nie musi wskazywać rzeczywistej aplikacji.
   * **{Password}:** hasła, który jest używany do uwierzytelniania z aplikacji.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Zwróć uwagę na **ApplicationId** z utworzonej aplikacji. Jest to potrzebne później.
5. Utwórz nową jednostkę usługi przy użyciu następującego polecenia, zastępując **{MyApplicationId}** z **ApplicationId** z poprzedniego kroku:
   
    ```powershell
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Konfigurowanie przypisania roli, używając następującego polecenia, zastępując **{MyApplicationId}** za pomocą usługi **ApplicationId**.
   
    ```powershell
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Możesz teraz Zakończono tworzenie aplikacji usługi Azure AD, która pozwala na uwierzytelnianie z niestandardowych C# aplikacji. W dalszej części tego samouczka potrzebne są następujące wartości:

* TenantId
* SubscriptionId
* ApplicationId
* Hasło

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
