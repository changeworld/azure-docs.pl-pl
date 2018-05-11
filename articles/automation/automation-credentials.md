---
title: Zasoby poświadczeń usługi Automatyzacja Azure
description: Zasoby poświadczeń usługi Automatyzacja Azure zawierają poświadczenia zabezpieczeń, które mogą służyć do uwierzytelniania na zasoby używane przez element runbook lub konfiguracji DSC. W tym artykule opisano sposób tworzenia zasoby poświadczeń i używać ich w element runbook lub konfiguracji DSC.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 825afb1e27dd8e2091a893aea825c0c0e54fa448
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="credential-assets-in-azure-automation"></a>Zasoby poświadczeń usługi Automatyzacja Azure

Zawiera zasób poświadczenia usługi automatyzacja [PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) obiekt zawierający poświadczenia zabezpieczeń, takie jak nazwa użytkownika i hasło. Konfiguracje elementów Runbook i DSC może używać poleceń cmdlet, które akceptuje obiekt PSCredential uwierzytelniania lub ich może wyodrębnić nazwy użytkownika i hasła obiektu PSCredential zapewnienie niektórych aplikacja lub usługa wymaga uwierzytelnienia. Właściwości dla poświadczenia są bezpiecznie przechowywane w usłudze Automatyzacja Azure i jest dostępny w element runbook lub Konfiguracja DSC o [Get-AutomationPSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) działania.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Bezpiecznych zasobów w automatyzacji Azure obejmują poświadczeń, certyfikatów, połączeń i szyfrowane zmienne. Te zasoby są szyfrowane i przechowywane w automatyzacji Azure za pomocą Unikatowy klucz, który jest generowany dla każdego konta automatyzacji. Ten klucz jest przechowywany w magazynie kluczy. Przed zapisaniem zabezpieczonym zasobem, klucz jest załadowany z magazynu kluczy i następnie używany do szyfrowania elementu zawartości.

## <a name="azure-classic-powershell-cmdlets"></a>Polecenia cmdlet systemu Azure klasycznego środowiska PowerShell

Polecenia cmdlet w poniższej tabeli służą do tworzenia i zarządzania zasobami poświadczenie automatyzacji przy użyciu programu Windows PowerShell.  One dostarczane jako część [modułu Azure PowerShell](/powershell/azure/overview) która jest dostępna na potrzeby automatyzacji elementów runbook i konfiguracji DSC.

| Polecenia cmdlet | Opis |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/azure/get-azureautomationcredential?view=azuresmps-3.7.0) |Pobiera informacje o zasób poświadczeń. Można tylko pobieranie poświadczeń się z **Get-AutomationPSCredential** działania. |
| [New-AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Tworzy nowe poświadczenie automatyzacji. |
| [Remove - AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Usuwa poświadczenie automatyzacji. |
| [Set - AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Ustawia właściwości istniejącego poświadczenia usługi Automatyzacja. |

## <a name="azurerm-powershell-cmdlets"></a>Polecenia cmdlet programu AzureRM PowerShell

Dla AzureRM poleceń cmdlet w poniższej tabeli służą do tworzenia i zarządzania zasobami poświadczenie automatyzacji przy użyciu programu Windows PowerShell.  One dostarczane jako część [modułu AzureRM.Automation](/powershell/azure/overview) która jest dostępna na potrzeby automatyzacji elementów runbook i konfiguracji DSC.

| Polecenia cmdlet | Opis |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential?view=azurermps-4.4.0) |Pobiera informacje o zasób poświadczeń.  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential?view=azurermps-4.4.0) |Tworzy nowe poświadczenie automatyzacji. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential?view=azurermps-4.4.0) |Usuwa poświadczenie automatyzacji. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential?view=azurermps-4.4.0) |Ustawia właściwości istniejącego poświadczenia usługi Automatyzacja. |

## <a name="activities"></a>Działania

Działania w poniższej tabeli umożliwiają dostęp do poświadczeń w elemencie runbook i konfiguracji DSC.

| Działania | Opis |
|:--- |:--- |
| Get-AutomationPSCredential |Pobiera poświadczenia do użycia w element runbook lub konfiguracji DSC. Zwraca [System.Management.Automation.PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) obiektu. |

> [!NOTE]
> Należy unikać używania zmiennych — nazwa parametr Get-AutomationPSCredential, ponieważ może to skomplikować wykrywanie zależności między elementami runbook lub konfiguracji DSC i poświadczeń zasobów w czasie projektowania.

## <a name="python2-functions"></a>Funkcje Python2

Funkcja w poniższej tabeli umożliwia dostęp do poświadczeń w elemencie runbook Python2.

| Funkcja | Opis |
|:---|:---|
| automationassets.get_automation_credential | Pobiera informacje o zasób poświadczeń. |

> [!NOTE]
> Aby uzyskać dostęp do funkcji zasobów, należy zaimportować modułu "automationassets" w górnej części elementu runbook języka Python.

## <a name="creating-a-new-credential-asset"></a>Tworzenie nowego zasobu poświadczeń

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Do utworzenia nowego zasobu poświadczeń z portalu Azure

1. Twoje konto usługi Automatyzacja kliknij **zasoby** , aby otworzyć **zasoby** bloku.
2. Kliknij przycisk **poświadczenia** , aby otworzyć **poświadczenia** bloku.
3. Kliknij przycisk **Dodaj poświadczenie** w górnej części bloku.
4. Wypełnij formularz, a następnie kliknij przycisk **Utwórz** zapisać nowe poświadczenie.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Do utworzenia nowego zasobu poświadczeń przy użyciu programu Windows PowerShell

W następujących przykładowych poleceniach pokazano, jak utworzyć nowe poświadczenie automatyzacji. Obiekt PSCredential jest najpierw utworzyć przy użyciu nazwy i hasła i następnie użyte do utworzenia zasobu poświadczeń. Alternatywnie można użyć **Get-Credential** polecenia cmdlet, aby wyświetlić monit o wpisanie nazwy i hasła.

```azurepowershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Używanie poświadczenia programu PowerShell

Pobieranie zasobu poświadczeń na element runbook lub Konfiguracja DSC o **Get-AutomationPSCredential** działania. To polecenie zwróci [obiektu PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) korzystając z działania lub polecenia cmdlet, które wymaga parametru PSCredential. Można również pobrać właściwości obiektu poświadczeń można użyć pojedynczo. Obiekt ma właściwość dla nazwy użytkownika i bezpieczne hasło lub użyć **GetNetworkCredential** metodę, aby zwrócić [NetworkCredential](http://msdn.microsoft.com/library/system.net.networkcredential.aspx) obiekt, który zapewni niezabezpieczona wersji hasła.

### <a name="textual-runbook-sample"></a>Przykład tekstowy

W następujących przykładowych poleceniach pokazano, jak używać poświadczenia programu PowerShell w elemencie runbook. W tym przykładzie poświadczenie jest pobierana i swoją nazwę użytkownika i hasło są przypisane do zmiennych.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

### <a name="graphical-runbook-sample"></a>Przykładowe graficznym elementem runbook

Możesz dodać **Get-AutomationPSCredential** działanie graficzny element runbook prawym przyciskiem myszy na poświadczeń w okienku Biblioteka edytora graficznego i wybierając **Dodaj do kanwy**.

![Dodawanie poświadczeń do kanwy](media/automation-credentials/credential-add-canvas.png)

Na poniższej ilustracji przedstawiono przykład przy użyciu poświadczeń w graficznym elementem runbook.  W takim przypadku jest używany do uwierzytelniania elementu runbook do zasobów platformy Azure zgodnie z opisem w [uwierzytelniania elementy Runbook za pomocą konta usługi Azure AD](automation-create-aduser-account.md).  Pierwsze działanie pobiera poświadczenia, które ma dostęp do subskrypcji platformy Azure.  **Add-AzureAccount** działania używa tych poświadczeń do uwierzytelniania żadnych działań, znajdujące się po nim.  A [linku potoku](automation-graphical-authoring-intro.md#links-and-workflow) jest tutaj od **Get-AutomationPSCredential** oczekuje pojedynczego obiektu.  

![Dodawanie poświadczeń do kanwy](media/automation-credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Używanie poświadczenia programu PowerShell w konfiguracji DSC

Podczas konfiguracji DSC automatyzacji Azure mogą odwoływać się zasoby poświadczeń przy użyciu **Get-AutomationPSCredential**, zasoby poświadczeń może również zostać przekazane za za pomocą parametrów, w razie potrzeby. Aby uzyskać więcej informacji, zobacz [kompilowania konfiguracji DSC automatyzacji Azure](automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Przy użyciu poświadczeń w Python2

Poniżej pokazano przykład uzyskiwania dostępu do poświadczeń w elementach runbook Python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat łączy w graficznym tworzenia, zobacz [łącza w tworzeniu graficznego](automation-graphical-authoring-intro.md#links-and-workflow)
* Aby poznać różnych metod uwierzytelniania przy użyciu automatyzacji, zobacz [zabezpieczeń usługi Automatyzacja Azure](automation-security-overview.md)
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell). 
* Aby rozpocząć pracę z elementami runbook Python2, zobacz [Mój pierwszy runbook Python2](automation-first-runbook-textual-python2.md) 

