---
title: Zasoby poświadczeń w usłudze Azure Automation
description: Zasoby poświadczeń w usłudze Azure Automation zawierają poświadczenia zabezpieczeń, które mogą służyć do uwierzytelniania zasobów uzyskiwał dostęp do zasobów przez konfigurację systemu runbook lub DSC. W tym artykule opisano sposób tworzenia zasobów poświadczeń i używania ich w konfiguracji żylicy lub dsc.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 767c1fddbc3d1f46d4341a70c990c2b57ad40e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252703"
---
# <a name="credential-assets-in-azure-automation"></a>Zasoby poświadczeń w usłudze Azure Automation

Zasób poświadczeń automatyzacji przechowuje obiekt, który zawiera poświadczenia zabezpieczeń, takie jak nazwa użytkownika i hasło. Runbooks i DSC konfiguracje mogą używać poleceń cmdlet, które akceptują PSCredential obiektu do uwierzytelniania lub mogą wyodrębnić nazwę użytkownika i hasło pscredential obiektu, aby zapewnić do niektórych aplikacji lub usługi wymagające uwierzytelniania. Właściwości poświadczeń są bezpiecznie przechowywane w usłudze Azure Automation i można uzyskać do nich dostęp w konfiguracji elementu runbook lub DSC za pomocą działania [Get-AutomationPSCredential.](#activities)

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Bezpieczne zasoby w usłudze Azure Automation obejmują poświadczenia, certyfikaty, połączenia i zaszyfrowane zmienne. Te zasoby są szyfrowane i przechowywane w usłudze Azure Automation przy użyciu unikatowego klucza, który jest generowany dla każdego konta automatyzacji. Ten klucz jest przechowywany w magazynie kluczy. Przed zapisaniem bezpiecznego zasobu klucz jest ładowany z usługi Key Vault, a następnie używany do szyfrowania zasobu.

## <a name="azure-powershell-az-cmdlets"></a>Polecenia cmdlet usługi Azure PowerShell Az

W przypadku modułu Az programu Azure PowerShell polecenia cmdlet w poniższej tabeli są używane do tworzenia zasobów poświadczeń automatyzacji i zarządzania nimi za pomocą programu Windows PowerShell. Są one dostarczane jako część [modułu AzureAz.Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), który jest dostępny do użycia w systemach runbook automatyzacji i konfiguracjach DSC.

| Polecenia cmdlet | Opis |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Pobiera informacje o zasobie poświadczeń. Nie zwraca to obiektu PSCredential.  |
| [Nowy-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Tworzy nowe poświadczenia automatyzacji. |
| [Usuń-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Usuwa poświadczenia automatyzacji. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Ustawia właściwości dla istniejących poświadczeń automatyzacji. |

## <a name="activities"></a>Działania

Działania w poniższej tabeli są używane do uzyskiwania dostępu do poświadczeń w konfiguracjach elementów runbook i DSC.

| Działania | Opis |
|:--- |:--- |
| Get-AutomationPSCredential |Pobiera poświadczenia do użycia w konfiguracji uruchomieniu lub DSC. Zwraca [obiekt System.Management.Automation.PSCredential.](/dotnet/api/system.management.automation.pscredential) |

> [!NOTE]
> Należy unikać używania zmiennych w parametrze –Name get-AutomationPSCredential, ponieważ może to skomplikować odnajdowanie zależności między elementami runbook lub konfiguracjami DSC i zasobami poświadczeń w czasie projektowania.

## <a name="python2-functions"></a>Funkcje Python2

Funkcja w poniższej tabeli służy do uzyskiwania dostępu do poświadczeń w uruchomieniu elementu runbook Języka Python2.

| Funkcja | Opis |
|:---|:---|
| automationassets.get_automation_credential | Pobiera informacje o zasobie poświadczeń. |

> [!NOTE]
> Aby uzyskać dostęp do funkcji zasobu, należy zaimportować moduł "automationassets" w górnej części systemu runbook języka Python.

## <a name="creating-a-new-credential-asset"></a>Tworzenie nowego zasobu poświadczeń

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Aby utworzyć nowy zasób poświadczeń za pomocą witryny Azure Portal

1. Na koncie automatyzacji wybierz pozycję **Poświadczenia** w obszarze **Zasoby udostępnione**.
1. Wybierz **pozycję Dodaj poświadczenie**.
1. Wypełnij formularz i wybierz **pozycję Utwórz,** aby zapisać nowe poświadczenia.

> [!NOTE]
> Konta użytkowników korzystające z uwierzytelniania wieloskładnikowego nie są obsługiwane do użycia w usłudze Azure Automation.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Aby utworzyć nowy zasób poświadczeń za pomocą programu Windows PowerShell

Poniższe przykładowe polecenia pokazują, jak utworzyć nowe poświadczenia automatyzacji. PsCredential obiekt jest najpierw tworzony z nazwą i hasłem, a następnie używane do tworzenia zasobu poświadczeń. Alternatywnie można użyć polecenia cmdlet **Get-Credential,** aby monitować o wpisanie nazwy i hasła.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Używanie poświadczeń programu PowerShell

Pobieranie zasobu poświadczeń w konfiguracji uruchomieniu lub DSC za pomocą działania **Get-AutomationPSCredential.** Zwraca [obiekt PSCredential,](/dotnet/api/system.management.automation.pscredential) którego można użyć z działaniem lub poleceniem cmdlet, które wymaga parametru PSCredential. Można również pobrać właściwości obiektu poświadczeń do użycia indywidualnie. Obiekt ma właściwość dla nazwy użytkownika i bezpiecznego hasła lub można użyć **GetNetworkCredential** metody do zwrócenia [NetworkCredential](/dotnet/api/system.net.networkcredential) obiektu, który zapewni niezabezpieczoną wersję hasła.

> [!NOTE]
> **Get-AzAutomationCredential** nie zwraca **PSCredential,** który może służyć do uwierzytelniania. Zawiera tylko informacje o poświadczenia. Jeśli trzeba użyć poświadczenia w obiekcie runbook należy użyć **Get-AutomationPSCredential** do pobrania **PSCredential** obiektu.

### <a name="textual-runbook-sample"></a>Przykładowy tekstowy podręcznik

W następujących przykładowych poleceniach pokazano, jak używać poświadczenia programu PowerShell w elemencie Runbook. W tym przykładzie poświadczenia są pobierane i jego nazwa użytkownika i hasło przypisane do zmiennych.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Poświadczenia można również użyć do uwierzytelniania na platformie Azure za pomocą [connect-azaccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). W większości przypadków należy użyć [konta Uruchom jako](../manage-runas-account.md) i pobrać je za pomocą pliku [Get-AzAutomationConnection](../automation-connections.md).

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Przykład graficznego podręcznika runbooka

Działanie **Get-AutomationPSCredential** można dodać do graficznego łańszania, klikając prawym przyciskiem myszy poświadczenia w okienku Biblioteka edytora graficznego i wybierając pozycję **Dodaj do obszaru roboczego**.

![Dodawanie poświadczeń do kanwy](../media/credentials/credential-add-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia poświadczeń w graficznym uruchomieniu bie. W takim przypadku jest on używany do zapewnienia uwierzytelniania dla zasobów platformy Runbook do platformy Azure, zgodnie z opisem w [Uwierzytelnianie żyła chybień za pomocą konta użytkownika usługi Azure AD.](../automation-create-aduser-account.md) Pierwsze działanie pobiera poświadczenia, które ma dostęp do subskrypcji platformy Azure. **Connect-AzureRmAccount** działania następnie używa tego poświadczenia, aby zapewnić uwierzytelnianie dla wszelkich działań, które przychodzą po nim. [Łącze potoku](../automation-graphical-authoring-intro.md#links-and-workflow) jest tutaj, ponieważ **Get-AutomationPSCredential** oczekuje jednego obiektu.  

![Dodawanie poświadczeń do kanwy](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Używanie poświadczeń programu PowerShell w dsc

Podczas gdy konfiguracje DSC w usłudze Azure Automation mogą odwoływać się do zasobów poświadczeń przy użyciu **funkcji Get-AutomationPSCredential,** zasoby poświadczeń mogą być również przekazywane za pośrednictwem parametrów, jeśli chcesz. Aby uzyskać więcej informacji, zobacz [Kompilowanie konfiguracji w usłudze Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Używanie poświadczeń w języku Python2

W poniższym przykładzie przedstawiono przykład uzyskiwania dostępu do poświadczeń w elementach runbook języka Python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o łączach w tworzenia graficznym, zobacz [Łącza w tworzenia graficznym](../automation-graphical-authoring-intro.md#links-and-workflow)
* Aby zrozumieć różne metody uwierzytelniania za pomocą automatyzacji, zobacz [Zabezpieczenia automatyzacji platformy Azure](../automation-security-overview.md)
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](../automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
* Aby rozpocząć pracę z uruchomieniu przepływem pracy programu PowerShell, zobacz [Mój pierwszy program PowerShell workflow runbook](../automation-first-runbook-textual.md)
* Aby rozpocząć korzystanie z śmięty Python2, zobacz [Mój pierwszy podręcznik Python2](../automation-first-runbook-textual-python2.md) 
