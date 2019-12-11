---
title: Integracja kontroli źródła w Azure Automation
description: W tym artykule opisano integrację kontroli źródła z usługą GitHub w Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/10/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4e5e0135e4ab56d319c78b7daeab9dd2e9d101f9
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996514"
---
# <a name="source-control-integration-in-azure-automation"></a>Integracja kontroli źródła w usłudze Automatyzacja Azure

Kontrola źródła umożliwia zachowanie aktualności elementów Runbook na koncie usługi Automation za pomocą skryptów w repozytorium usługi GitHub lub Azure Repos. Kontrola źródła pozwala łatwo współpracować z zespołem, śledzić zmiany i przywracać wcześniejsze wersje elementów Runbook. Na przykład kontrola źródła pozwala synchronizować różne gałęzie w kontroli źródła z kontami programu do tworzenia, testowania lub produkcji. Dzięki temu można łatwo podwyższyć poziom kodu, który został przetestowany w środowisku programistycznym, na konto automatyzacji produkcji. Integracja kontroli źródła z automatyzacją obsługuje synchronizację jednokierunkową z repozytorium kontroli źródła.

Azure Automation obsługuje trzy typy kontroli źródła:

* Witryna GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="pre-requisites"></a>Wymagania wstępne

* Repozytorium kontroli źródła (GitHub lub Azure Repos)
* [Konto Uruchom jako](manage-runas-account.md)
* Upewnij się, że masz [Najnowsze moduły platformy Azure](automation-update-azure-modules.md) na koncie usługi Automation, w tym moduł **AzureRM. profile** . 

> [!NOTE]
> Zadania synchronizacji kontroli źródła są uruchamiane na koncie usługi Automation użytkowników i są rozliczane według tej samej stawki co inne zadania automatyzacji.

## <a name="configure-source-control---azure-portal"></a>Konfigurowanie kontroli źródła — Azure Portal

W ramach konta usługi Automation wybierz pozycję **Kontrola źródła** , a następnie kliknij pozycję **+ Dodaj** .

![Wybierz kontrolę źródła](./media/source-control-integration/select-source-control.png)

Wybierz **Typ kontroli źródła**, a następnie kliknij przycisk **Uwierzytelnij**. Zostanie otwarte okno przeglądarki z monitem o zalogowanie się, postępuj zgodnie z monitami, aby zakończyć uwierzytelnianie.

Na stronie **Podsumowanie kontroli źródła** Wypełnij informacje i kliknij przycisk **Zapisz**. W poniższej tabeli przedstawiono opis dostępnych pól.

|Właściwość  |Opis  |
|---------|---------|
|Nazwa kontroli źródła     | Przyjazna nazwa dla kontroli źródła. *Ta nazwa może zawierać tylko litery i cyfry.*        |
|Typ kontroli źródła     | Typ źródła kontroli źródła. Dostępne opcje:</br> Witryna GitHub</br>Azure Repos (Git)</br> Azure Repos (TFVC)        |
|Repozytorium     | Nazwa repozytorium lub projektu. Zwracane są pierwsze 200 repozytoriów. Aby wyszukać repozytorium, wpisz nazwę w polu, a następnie kliknij pozycję **Wyszukaj w witrynie GitHub**.|
|Branch     | Gałąź, z której mają zostać pobrane pliki źródłowe. Funkcja określania wartości docelowej gałęzi jest niedostępna dla typu kontroli źródła TFVC.          |
|Ścieżka folderu     | Folder zawierający elementy Runbook do zsynchronizowania. Przykład:/Runbooks </br>*Synchronizowane są tylko elementy Runbook w określonym folderze. Rekursja nie jest obsługiwana.*        |
|Synchronizacja autosynchronizacji<sup>1</sup>     | Włącza lub wyłącza automatyczną synchronizację po dokonaniu zatwierdzenia w repozytorium kontroli źródła         |
|Publikowanie elementu Runbook     | W przypadku wybrania opcji **włączone**po zsynchronizowaniu elementów Runbook z kontroli źródła zostaną one automatycznie opublikowane.         |
|Opis     | Pole tekstowe umożliwiające dostarczenie dodatkowych szczegółów        |

<sup>1</sup> aby włączyć autosynchronizację podczas konfigurowania integracji kontroli źródła z Azure Repos, musisz być administratorem projektu.

![Podsumowanie kontroli źródła](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Twoje dane logowania dla repozytorium kontroli źródła mogą być inne niż Twoje dane logowania dla Azure Portal. Upewnij się, że logujesz się przy użyciu odpowiedniego konta dla repozytorium kontroli źródła podczas konfigurowania kontroli źródła. W razie wątpliwości Otwórz nową kartę w przeglądarce i wyloguj się z visualstudio.com lub github.com i spróbuj ponownie połączyć kontrolę źródła.

## <a name="configure-source-control---powershell"></a>Konfigurowanie kontroli źródła — PowerShell

Za pomocą programu PowerShell można także skonfigurować kontrolę źródła w Azure Automation. Aby skonfigurować kontrolę źródła za pomocą poleceń cmdlet programu PowerShell, wymagany jest osobisty token dostępu. Aby utworzyć połączenie kontroli źródła, należy użyć [New-AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) . Polecenie cmdlet wymaga bezpiecznego ciągu osobistego tokenu dostępu, aby dowiedzieć się, jak utworzyć bezpieczny ciąg, zobacz [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Azure Repos (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure Repos (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>Witryna GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Uprawnienia osobistego tokenu dostępu

Kontrola źródła wymaga pewnych minimalnych uprawnień do osobistych tokenów dostępu. Poniższe tabele zawierają minimalne uprawnienia wymagane w serwisie GitHub i Azure Repos.

#### <a name="github"></a>Witryna GitHub

Aby uzyskać więcej informacji na temat tworzenia osobistego tokenu dostępu w usłudze GitHub, odwiedź stronę [Tworzenie osobistego tokenu dostępu dla wiersza polecenia](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Zakres  |Opis  |
|---------|---------|
|**repozytorium**     |         |
|repozytorium: stan     | Stan zatwierdzenia dostępu         |
|repo_deployment      | Stan wdrożenia dostępu         |
|public_repo     | Dostęp do publicznych repozytoriów         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Zapisz punkty zaczepienia repozytorium         |
|read:repo_hook|Odczytaj punkty zaczepienia repozytorium|

#### <a name="azure-repos"></a>Azure Repos

Aby uzyskać więcej informacji na temat tworzenia osobistego tokenu dostępu w Azure Repos, odwiedź stronę [uwierzytelnianie dostępu przy użyciu osobistych tokenów dostępu](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

|Zakres  |
|---------|
|Kod (odczyt)     |
|Projekt i zespół (odczyt)|
|Tożsamość (odczyt)      |
|Profil użytkownika (odczyt)     |
|Elementy robocze (odczyt)    |
|Połączenia usług (odczyt, wykonywanie zapytań i zarządzanie)<sup>1</sup>    |

<sup>1</sup> uprawnienie połączenia usługi jest wymagane tylko wtedy, gdy włączono AutoSync.

## <a name="syncing"></a>Synchronizowanie

Wybierz źródło z tabeli na stronie **kontroli źródła** . Kliknij przycisk **Rozpocznij synchronizację** , aby rozpocząć proces synchronizacji.

Aby wyświetlić stan bieżącego zadania synchronizacji lub poprzednich, kliknij kartę **zadania synchronizacji** . Na liście rozwijanej **Kontrola źródła** wybierz kontrolę źródła.

![Stan synchronizacji](./media/source-control-integration/sync-status.png)

Kliknięcie zadania umożliwia wyświetlenie danych wyjściowych zadania. Poniższy przykład jest wyjściem z zadania synchronizacji kontroli źródła.

```output
========================================================================================================

Azure Automation Source Control.
Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

Setting AzureRmEnvironment.

Getting AzureRunAsConnection.

Logging in to Azure...

Source control information for syncing:

[Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

Connecting to VSTS...


Source Control Sync Summary:


2 files synced:
 - ExampleRunbook1.ps1
 - ExampleRunbook2.ps1



========================================================================================================
```

Dodatkowe rejestrowanie jest dostępne po wybraniu opcji **wszystkie dzienniki** na stronie **Podsumowanie zadania synchronizacji kontroli źródła** . Te dodatkowe wpisy dziennika mogą pomóc w rozwiązywaniu problemów, które mogą wystąpić podczas korzystania z kontroli źródła.

## <a name="disconnecting-source-control"></a>Odłączanie kontroli źródła

Aby rozłączyć się z repozytorium kontroli źródła, Otwórz **kontrolę źródła** w obszarze **Ustawienia konta** na koncie usługi Automation.

Wybierz kontrolę źródła, którą chcesz usunąć. Na stronie **Podsumowanie kontroli źródła** kliknij pozycję **Usuń**.

## <a name="encoding"></a>Encoding

Jeśli wiele osób edytuje elementy Runbook w repozytorium kontroli źródła przy użyciu różnych edytorów, istnieje możliwość uruchomienia w nich problemów z kodowaniem. Ta sytuacja może prowadzić do niepoprawnych znaków w elemencie Runbook. Aby dowiedzieć się więcej na ten temat, zobacz [typowe przyczyny problemów z kodowaniem](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="updating-the-access-token"></a>Aktualizowanie tokenu dostępu

Obecnie nie ma możliwości aktualizacji tokenu dostępu w kontroli źródła z portalu. Po wygaśnięciu lub odwołaniu osobistego tokenu dostępu można zaktualizować kontrolę źródła przy użyciu nowego tokenu dostępu w następujący sposób:

* Za pomocą [interfejsu API REST](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Za pomocą polecenia cmdlet [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz artykuł [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
