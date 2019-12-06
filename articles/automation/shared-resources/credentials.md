---
title: Zasoby poświadczeń w Azure Automation
description: Zasoby poświadczeń w Azure Automation zawierają poświadczenia zabezpieczeń, których można użyć do uwierzytelniania w zasobach, do których uzyskuje się dostęp za pomocą elementu Runbook lub konfiguracji DSC. W tym artykule opisano sposób tworzenia zasobów poświadczeń i używania ich w konfiguracji elementu Runbook lub DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 582645919825c308fce4fe3211fa601955aaf37d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850180"
---
# <a name="credential-assets-in-azure-automation"></a>Zasoby poświadczeń w Azure Automation

Zasób poświadczenia usługi Automation zawiera obiekt zawierający poświadczenia zabezpieczeń, takie jak nazwa użytkownika i hasło. Elementy Runbook i konfiguracje DSC mogą używać poleceń cmdlet, które akceptują obiekt PSCredential na potrzeby uwierzytelniania, lub mogą wyodrębnić nazwę użytkownika i hasło obiektu PSCredential w celu udostępnienia aplikacji lub usługi wymagającej uwierzytelniania. Właściwości poświadczenia są bezpiecznie przechowywane w Azure Automation i dostępne w konfiguracji elementu Runbook lub DSC przy użyciu działania [Get-AutomationPSCredential](#activities) .

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Zabezpieczanie zasobów w Azure Automation obejmuje poświadczenia, certyfikaty, połączenia i zmienne zaszyfrowane. Te zasoby są szyfrowane i przechowywane w Azure Automation przy użyciu unikatowego klucza wygenerowanego dla każdego konta usługi Automation. Ten klucz jest przechowywany w Key Vault. Przed zapisaniem bezpiecznego elementu zawartości klucz jest ładowany z Key Vault a następnie używany do szyfrowania elementu zawartości.

## <a name="azure-classic-powershell-cmdlets"></a>Klasyczne polecenia cmdlet środowiska PowerShell platformy Azure

Polecenia cmdlet w poniższej tabeli służą do tworzenia zasobów poświadczeń usługi Automation i zarządzania nimi za pomocą programu Windows PowerShell.  Są one dostarczane jako część [modułu Azure PowerShell](/powershell/azure/overview), który jest dostępny do użycia w elementach Runbook usługi Automation i konfiguracjach DSC.

| Polecenia cmdlet | Opis |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/servicemanagement/azure/get-azureautomationcredential) |Pobiera informacje o zawartości poświadczeń. Możesz pobrać tylko poświadczenia z działania **Get-AutomationPSCredential** . |
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Tworzy nowe poświadczenie usługi Automation. |
| [Remove-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Usuwa poświadczenia usługi Automation. |
| [Set-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Ustawia właściwości dla istniejącego poświadczenia usługi Automation. |

## <a name="azurerm-powershell-cmdlets"></a>Polecenia cmdlet programu PowerShell AzureRM

W przypadku AzureRM polecenia cmdlet w poniższej tabeli służą do tworzenia zasobów poświadczeń usługi Automation i zarządzania nimi za pomocą programu Windows PowerShell.  Są one dostarczane jako część [modułu AzureRM. Automation](/powershell/azure/overview), który jest dostępny do użycia w elementach Runbook usługi Automation i konfiguracjach DSC.

| Polecenia cmdlet | Opis |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential) |Pobiera informacje o zawartości poświadczeń. To nie zwraca obiektu PSCredential.  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential) |Tworzy nowe poświadczenie usługi Automation. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential) |Usuwa poświadczenia usługi Automation. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential) |Ustawia właściwości dla istniejącego poświadczenia usługi Automation. |

## <a name="activities"></a>Działania

Działania w poniższej tabeli służą do uzyskiwania dostępu do poświadczeń w elementach Runbook i konfiguracjach DSC.

| Działania | Opis |
|:--- |:--- |
| Get-AutomationPSCredential |Pobiera poświadczenie do użycia w elemencie Runbook lub konfiguracji DSC. Zwraca obiekt [System. Management. Automation. PSCredential](/dotnet/api/system.management.automation.pscredential) . |

> [!NOTE]
> Należy unikać używania zmiennych w parametrze-Name polecenia Get-AutomationPSCredential, ponieważ może to spowodować skomplikowanie zależności między elementami Runbook lub konfiguracjami DSC oraz zasobami poświadczeń w czasie projektowania.

## <a name="python2-functions"></a>Funkcje python2

Funkcja w poniższej tabeli służy do uzyskiwania dostępu do poświadczeń w elemencie Runbook python2.

| Funkcja | Opis |
|:---|:---|
| automationassets.get_automation_credential | Pobiera informacje o zawartości poświadczeń. |

> [!NOTE]
> Aby uzyskać dostęp do funkcji zasobów, należy zaimportować moduł "automationassets" w górnej części elementu Runbook języka Python.

## <a name="creating-a-new-credential-asset"></a>Tworzenie nowego zasobu poświadczeń

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Aby utworzyć nowy zasób poświadczeń przy użyciu Azure Portal

1. Z poziomu konta usługi Automation wybierz pozycję **poświadczenia** w obszarze **udostępnione zasoby**.
1. Kliknij pozycję **+ Dodaj poświadczenie**.
1. Wypełnij formularz i kliknij przycisk **Utwórz** , aby zapisać nowe poświadczenie.

> [!NOTE]
> Konta użytkowników korzystające z uwierzytelniania wieloskładnikowego nie są obsługiwane w programie Azure Automation.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Aby utworzyć nowy zasób poświadczeń przy użyciu programu Windows PowerShell

Następujące przykładowe polecenia pokazują, jak utworzyć nowe poświadczenie usługi Automation. Obiekt PSCredential jest najpierw tworzony przy użyciu nazwy i hasła, a następnie używany do tworzenia zasobu poświadczenia. Alternatywnie możesz użyć polecenia cmdlet **Get-Credential** , aby uzyskać monit o wpisanie nazwy i hasła.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Korzystanie z poświadczeń programu PowerShell

Zasób poświadczeń jest pobierany w ramach elementu Runbook lub konfiguracji DSC przy użyciu działania **Get-AutomationPSCredential** . Zwraca [obiekt PSCredential](/dotnet/api/system.management.automation.pscredential) , którego można użyć w przypadku działania lub polecenia cmdlet, które wymaga parametru PSCredential. Możesz również pobrać właściwości obiektu Credential, aby użyć go pojedynczo. Obiekt ma właściwość dla nazwy użytkownika i bezpiecznego hasła lub można użyć metody **GetNetworkCredential** , aby zwrócić obiekt [NetworkCredential](/dotnet/api/system.net.networkcredential) , który zapewni niezabezpieczoną wersję hasła.

> [!NOTE]
> **Get-AzureRmAutomationCredential** nie zwraca **PSCredential** , którego można użyć do uwierzytelniania. Zawiera on tylko informacje o poświadczeniu. Jeśli musisz użyć poświadczeń w elemencie Runbook, musisz użyć **Get-AutomationPSCredential** , aby pobrać obiekt **PSCredential** .

### <a name="textual-runbook-sample"></a>Przykład tekstu elementu Runbook

Następujące przykładowe polecenia pokazują, jak używać poświadczenia programu PowerShell w elemencie runbook. W tym przykładzie poświadczenie jest pobierane, a jego nazwa użytkownika i hasło są przypisane do zmiennych.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Za pomocą poświadczeń można także uwierzytelniać się na platformie Azure za pomocą programu [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount). W większości przypadków należy użyć [konta Uruchom jako](../manage-runas-account.md) i pobrać je z poleceniem [Get-AutomationConnection](../automation-connections.md).

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Przykład graficzny elementu Runbook

Aby dodać działanie **Get-AutomationPSCredential** do graficznego elementu Runbook, kliknij prawym przyciskiem myszy poświadczenie w okienku Biblioteka w edytorze graficznym i wybierz polecenie **Dodaj do kanwy**.

![Dodaj poświadczenie do kanwy](../media/credentials/credential-add-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia poświadczeń w graficznym elemencie Runbook.  W takim przypadku jest używany do zapewnienia uwierzytelniania dla elementu Runbook dla zasobów platformy Azure zgodnie z opisem w artykule [uwierzytelnianie elementów Runbook przy użyciu konta użytkownika usługi Azure AD](../automation-create-aduser-account.md).  Pierwsze działanie Pobiera poświadczenia, które mają dostęp do subskrypcji platformy Azure.  Działanie **Add-AzureAccount** korzysta z tego poświadczenia, aby zapewnić uwierzytelnianie dla wszystkich działań, które są po niej dostępne.  [Link potoku](../automation-graphical-authoring-intro.md#links-and-workflow) jest tutaj, ponieważ **Get-AutomationPSCredential** oczekuje pojedynczego obiektu.  

![Dodaj poświadczenie do kanwy](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Używanie poświadczeń programu PowerShell w usłudze DSC

Konfiguracje DSC w Azure Automation mogą odwoływać się do zasobów poświadczeń przy użyciu polecenia **Get-AutomationPSCredential**, więc zasoby poświadczeń można także przekazywać przez parametry, jeśli są potrzebne. Aby uzyskać więcej informacji, zobacz [Kompilowanie konfiguracji w Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Używanie poświadczeń w programie python2

Poniższy przykład pokazuje przykład uzyskiwania dostępu do poświadczeń w elementach Runbook python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat linków w tworzeniu grafiki, zobacz [linki w temacie Tworzenie graficzne](../automation-graphical-authoring-intro.md#links-and-workflow)
* Aby poznać różne metody uwierzytelniania z automatyzacją, zobacz [Azure Automation Security](../automation-security-overview.md)
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](../automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](../automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell).
* Aby rozpocząć pracę z elementami Runbook python2, zobacz [mój pierwszy python2 Runbook](../automation-first-runbook-textual-python2.md) 
