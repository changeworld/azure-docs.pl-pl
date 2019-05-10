---
title: Tworzenie jednostek usługi Windows wirtualnego pulpitu (wersja zapoznawcza) i przypisań ról za pomocą programu PowerShell — platformy Azure
description: Jak utworzyć jednostki usługi i przypisz role przy użyciu programu PowerShell w wersji zapoznawczej pulpitu wirtualnego Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: d3357cec426585ba8550301dfa703f583a930ad0
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236931"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell"></a>Samouczek: Tworzenie jednostek usługi i przypisań ról za pomocą programu PowerShell

Jednostki usługi stanowią tożsamości, utworzone w usłudze Azure Active Directory do przypisywania ról i uprawnień do określonego celu. W Windows wirtualnego pulpitu (wersja zapoznawcza) można utworzyć usługę podmiotu zabezpieczeń, aby:

- Automatyzowanie zadań zarządzania w usłudze określonych Windows pulpitu wirtualnego
- Użyj jako poświadczeń zamiast użytkowników wymagane uwierzytelnianie wieloskładnikowe, podczas uruchamiania dowolnego szablonu Windows wirtualnego pulpitu usługi Azure Resource Manager

W tym samouczku pokazano, jak:

> [!div class="checklist"]
> * Tworzenie nazwy głównej usługi w usłudze Azure Active Directory
> * Utwórz przypisanie roli w Windows pulpitu wirtualnego
> * Zaloguj się do Windows pulpitu wirtualnego przy użyciu jednostki usługi

## <a name="prerequisites"></a>Wymagania wstępne

Zanim będzie można utworzyć jednostki usługi i przypisań ról, należy wykonać trzy czynności:

1. Instalowanie modułu usługi Azure AD. Aby zainstalować moduł, uruchom program PowerShell jako administrator i uruchom następujące polecenie cmdlet:

    ```powershell
    Install-Module AzureAD
    ```

2. Uruchom następujące polecenia cmdlet przy użyciu wartości w cudzysłowie zastąpione wartościami odpowiednich do sesji. Jeśli właśnie utworzony, w dzierżawie pulpitu wirtualnego Windows z [utworzyć dzierżawę w samouczku pulpitu wirtualnego Windows](./tenant-setup-azure-active-directory.md), użycie "Domyślna dzierżawa grupy" jako nazwa grupy dzierżawy.

    ```powershell
    $myTenantGroupName = "<my-tenant-group-name>"
    $myTenantName = "<my-tenant-name>"
    ```

3. W tej samej sesji programu PowerShell, należy wykonać wszystkie instrukcje w tym artykule. Może ona nie działać po zamknięciu okna i wrócić do niego później.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Tworzenie nazwy głównej usługi w usłudze Azure Active Directory

Po wymagania wstępne zostały spełnione, w sesji programu PowerShell, uruchom następujące polecenia cmdlet programu PowerShell, aby utworzyć usługę wielodostępną na jednostkę na platformie Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Utwórz przypisanie roli w Windows wirtualnego pulpitu (wersja zapoznawcza)

Teraz, po utworzeniu usługi jednostki, można użyć go do logowania się na Windows pulpitu wirtualnego. Pamiętaj zalogować się przy użyciu konta które ma uprawnienia do utworzenia przypisania roli.

Po pierwsze, [Pobierz i zaimportuj moduł programu PowerShell pulpitu wirtualnego Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) do użycia w sesji programu PowerShell, jeśli jeszcze go.

Uruchom następujące polecenia cmdlet programu PowerShell, aby połączyć się pulpitu wirtualnego Windows i utworzyć jednostkę przypisania roli dla usługi.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsContext -TenantGroupName $myTenantGroupName
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantGroupName $myTenantGroupName -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Zaloguj się przy użyciu jednostki usługi

Po utworzeniu przypisania roli dla usługi głównej powinny teraz upewnij się, że nazwa główna usługi zalogować się do Windows pulpitu wirtualnego, uruchamiając następujące polecenie cmdlet:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Po zalogowaniu, upewnij się, że wszystko działa, w celu przetestowania kilka poleceń cmdlet programu PowerShell pulpitu wirtualnego Windows jednostki usługi.

## <a name="view-your-credentials-in-powershell"></a>Wyświetl swoje poświadczenia w programie PowerShell

Zakończyć sesję programu PowerShell, możesz wyświetlić swoje poświadczenia i zapisz je w przyszłości. Hasło jest szczególnie ważne, ponieważ nie można pobrać po zamknięciu tej sesji programu PowerShell.

Poniżej przedstawiono trzy poświadczeń, których należy zanotować i poleceń cmdlet, które należy uruchomić, aby je uzyskać:

- Hasło:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- Identyfikator dzierżawy:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- Identyfikator aplikacji:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu nazwy głównej usługi i przypisana rola w Twojej dzierżawie Windows pulpitu wirtualnego, można użyć go do utworzenia puli hosta. Aby dowiedzieć się więcej o pulach hosta, przejdź do samouczka związane z tworzeniem puli hosta w Windows pulpitu wirtualnego.

 > [!div class="nextstepaction"]
 > [Samouczek puli hosta Windows pulpitu wirtualnego](./create-host-pools-azure-marketplace.md)
