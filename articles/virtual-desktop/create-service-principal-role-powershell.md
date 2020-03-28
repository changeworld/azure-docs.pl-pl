---
title: Przypisanie głównej roli usługi pulpitu wirtualnego systemu Windows — Platforma Azure
description: Jak utworzyć podmioty usługi i przypisać role przy użyciu programu PowerShell na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 61b5017609d99f2f0074c67d3838cf351ea38bea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79365427"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Samouczek: Tworzenie podmiotów zabezpieczeń usługi i przypisań ról przy użyciu programu PowerShell

Jednostki usługi to tożsamości, które można utworzyć w usłudze Azure Active Directory w celu przypisania ról i uprawnień do określonego celu. Na pulpicie wirtualnym systemu Windows można utworzyć jednostkę usługi, aby:

- Automatyzacja określonych zadań zarządzania pulpitem wirtualnym systemu Windows.
- Użyj jako poświadczenia zamiast użytkowników wymaganych przez usługę MFA podczas uruchamiania dowolnego szablonu usługi Azure Resource Manager dla pulpitu wirtualnego systemu Windows.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz jednostkę usługi w usłudze Azure Active Directory.
> * Tworzenie przypisania roli na pulpicie wirtualnym systemu Windows.
> * Zaloguj się do pulpitu wirtualnego systemu Windows przy użyciu jednostki usługi.

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem podmiotów usługi i przypisań ról należy wykonać trzy czynności:

1. Zainstaluj moduł AzureAD. Aby zainstalować moduł, uruchom program PowerShell jako administrator i uruchom następujące polecenie cmdlet:

    ```powershell
    Install-Module AzureAD
    ```

2. [Pobierz i zaimportuj moduł programu Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/).

3. Postępuj zgodnie ze wszystkimi instrukcjami zawartymi w tym artykule w tej samej sesji programu PowerShell. Proces może nie działać, jeśli przerwanie sesji programu PowerShell przez zamknięcie okna i ponowne otwarcie go później.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Tworzenie nazwy głównej usługi w usłudze Azure Active Directory

Po spełnieniu wymagań wstępnych w sesji programu PowerShell uruchom następujące polecenia cmdlet programu PowerShell, aby utworzyć podmiot usługi wielodostępnej na platformie Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>Wyświetlanie poświadczeń w programie PowerShell

Przed utworzeniem przypisania roli dla jednostki usługi, należy wyświetlić poświadczenia i zapisać je do wykorzystania w przyszłości. Hasło jest szczególnie ważne, ponieważ nie będzie można go pobrać po zamknięciu tej sesji programu PowerShell.

Oto trzy poświadczenia, które należy zapisać i polecenia cmdlet, które musisz uruchomić, aby je uzyskać:

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

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Tworzenie przypisania roli w wersji Preview pulpitu wirtualnego systemu Windows

Następnie należy utworzyć przypisanie roli, aby podmiot zabezpieczeń usługi mógł zalogować się na pulpicie wirtualnym systemu Windows. Pamiętaj, aby zalogować się przy za pomocą konta, które ma uprawnienia do tworzenia przypisań ról.

Najpierw [pobierz i zaimportuj moduł programu Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) do użycia w sesji programu PowerShell, jeśli jeszcze tego nie zrobiłeś.

Uruchom następujące polecenia cmdlet programu PowerShell, aby połączyć się z pulpitem wirtualnym systemu Windows i wyświetlić dzierżawców.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

Po znalezieniu nazwy dzierżawy dzierżawy, dla której chcesz utworzyć przypisanie roli, użyj tej nazwy w następującym powiększe:

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Logowanie się za pomocą jednostki usługi

Po utworzeniu przypisania roli dla jednostki usługi upewnij się, że podmiot usługi może zalogować się na pulpicie wirtualnym systemu Windows, uruchamiając następujące polecenie cmdlet:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Po zalogowaniu się upewnij się, że wszystko działa, testując kilka poleceń cmdlet programu Windows Virtual Desktop Programu PowerShell z jednostką usługi.

## <a name="next-steps"></a>Następne kroki

Po utworzeniu jednostki usługi i przypisaniu jej roli w dzierżawie pulpitu wirtualnego systemu Windows można jej użyć do utworzenia puli hostów. Aby dowiedzieć się więcej o pulach hostów, przejdź do samouczka tworzenia puli hostów na pulpicie wirtualnym systemu Windows.

 > [!div class="nextstepaction"]
 > [Tworzenie puli hostów za pomocą witryny Azure Marketplace](./create-host-pools-azure-marketplace.md)
