---
title: Tworzenie głównych i przypisań ról usługi Windows Virtual Desktop w wersji zapoznawczej przy użyciu programu PowerShell — Azure
description: Jak tworzyć jednostki usługi i przypisywać role przy użyciu programu PowerShell w wersji zapoznawczej pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 3e9ee3f5dd04ef838f78b9731885b7ea48e6c99d
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811327"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Samouczek: Tworzenie jednostek usługi i przypisań ról za pomocą programu PowerShell

Nazwy główne usług są tożsamościami, które można utworzyć w Azure Active Directory, aby przypisać role i uprawnienia do określonego celu. W wersji zapoznawczej pulpitu wirtualnego systemu Windows można utworzyć jednostkę usługi, aby:

- Automatyzowanie określonych zadań zarządzania pulpitem wirtualnym systemu Windows.
- Użyj jako poświadczeń zamiast użytkowników wymaganych przez uwierzytelnianie wieloskładnikowe podczas uruchamiania dowolnego szablonu Azure Resource Manager dla pulpitu wirtualnego systemu Windows.

W tym samouczku pokazano, jak:

> [!div class="checklist"]
> * Utwórz nazwę główną usługi w Azure Active Directory.
> * Utwórz przypisanie roli na pulpicie wirtualnym systemu Windows.
> * Zaloguj się do pulpitu wirtualnego systemu Windows przy użyciu nazwy głównej usługi.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było tworzyć jednostki usługi i przypisania ról, należy wykonać trzy czynności:

1. Zainstaluj moduł AzureAD. Aby zainstalować moduł, uruchom program PowerShell jako administrator i uruchom następujące polecenie cmdlet:

    ```powershell
    Install-Module AzureAD
    ```

2. [Pobieranie i importowanie modułu programu PowerShell dla pulpitu wirtualnego systemu Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)

3. Wykonaj wszystkie instrukcje zawarte w tym artykule w tej samej sesji programu PowerShell. Może nie zadziałało, jeśli zamkniesz okno i wrócisz do niego później.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Tworzenie nazwy głównej usługi w usłudze Azure Active Directory

Po spełnieniu wymagań wstępnych w sesji programu PowerShell uruchom następujące polecenia cmdlet programu PowerShell, aby utworzyć nazwę główną usługi wielodostępnej na platformie Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```

## <a name="view-your-credentials-in-powershell"></a>Wyświetlanie poświadczeń w programie PowerShell

Przed zakończeniem sesji programu PowerShell Sprawdź swoje poświadczenia i Zapisz je w celu uwzględnienia w przyszłości. Hasło jest szczególnie ważne, ponieważ nie będzie można go pobrać po zamknięciu sesji programu PowerShell.

Poniżej przedstawiono trzy poświadczenia, które należy napisać, i polecenia cmdlet, które należy uruchomić, aby je pobrać:

- Hasło

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

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Tworzenie przypisania roli w wersji zapoznawczej pulpitu wirtualnego systemu Windows

Następnie utworzysz przypisanie roli RDS na pulpicie wirtualnym systemu Windows dla jednostki usługi, co umożliwi jednostce usługi Logowanie się do pulpitu wirtualnego systemu Windows. Upewnij się, że używasz konta z uprawnieniami do tworzenia przypisań ról RDS.

Uruchom następujące polecenia cmdlet programu PowerShell, aby połączyć się z pulpitem wirtualnym systemu Windows i wyświetlić dzierżawy usług pulpitu zdalnego.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant | FL
```

Użyj nazwy dzierżawca dla poprawnej dzierżawy i uruchom następujące polecenia cmdlet programu PowerShell, aby utworzyć przypisanie roli dla jednostki usługi w określonej dzierżawie.

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName "<my-rds-tenantname>"
```

## <a name="sign-in-with-the-service-principal"></a>Zaloguj się przy użyciu nazwy głównej usługi

Po utworzeniu przypisania roli dla jednostki usługi upewnij się, że jednostka usługi może się zalogować do pulpitu wirtualnego systemu Windows, uruchamiając następujące polecenie cmdlet:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Po zalogowaniu się upewnij się, że wszystko działa, testując kilka poleceń cmdlet programu PowerShell dla pulpitu wirtualnego systemu Windows z jednostką usługi.

## <a name="next-steps"></a>Następne kroki

Po utworzeniu jednostki usługi i przypisaniu jej do roli w dzierżawie pulpitu wirtualnego systemu Windows możesz użyć jej do utworzenia puli hostów. Aby dowiedzieć się więcej na temat pul hostów, przejdź do samouczka dotyczącego tworzenia puli hostów w programie Virtual Desktop systemu Windows.

 > [!div class="nextstepaction"]
 > [Samouczek puli hostów usług pulpitu wirtualnego systemu Windows](./create-host-pools-azure-marketplace.md)
