---
title: Źródło Integracja kontroli w usłudze Azure Automation
description: W tym artykule opisano Integracja kontroli źródła z usługą GitHub w usłudze Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 94912d5aa10ddd2e67c33bcbb416f007c85f105c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64574105"
---
# <a name="source-control-integration-in-azure-automation"></a>Integracja kontroli źródła w usłudze Automatyzacja Azure

Kontrola źródła pozwala na zachowanie elementów runbook w automatyzacji konta są aktualne za pomocą skryptów w repozytorium kontroli źródła GitHub i repozytoriów platformy Azure. Kontrola źródła pozwala łatwo współpracować z zespołem, śledzenie zmian i wrócić do wcześniejszych wersji elementów runbook. Na przykład kontroli źródła umożliwia synchronizowanie różnych gałęzi w kontroli źródła do kont usługi Automation rozwoju, testów lub produkcji. Ułatwia promowanie kod, który został przetestowany w środowisku projektowym konta usługi Automation w środowisku produkcyjnym. Integracja kontroli źródła z usługą automation obsługuje jeden kierunek synchronizacji z repozytorium kontroli źródła.

Usługa Azure Automation obsługuje trzy typy kontroli źródła:

* GitHub
* Repozytoriów platformy Azure (Git)
* Repozytoriów platformy Azure (TFVC)

## <a name="pre-requisites"></a>Wymagania wstępne

* Repozytorium kontroli źródła (GitHub i repozytoriów platformy Azure)
* A [konto Uruchom jako](manage-runas-account.md)
* Upewnij się, że [najnowszych modułów platformy Azure](automation-update-azure-modules.md) na koncie usługi Automation

> [!NOTE]
> Zadania synchronizacji kontroli źródła uruchamiane w ramach użytkownicy konta usługi Automation i są rozliczane według stawki takie same, jak inne zadania automatyzacji.

## <a name="configure-source-control---azure-portal"></a>Konfigurowanie kontroli źródła — witryna Azure portal

W ramach konta usługi Automation wybierz **kontroli źródła** i kliknij przycisk **+ Dodaj**

![Wybierz kontrolę źródła](./media/source-control-integration/select-source-control.png)

Wybierz **typ kontroli źródła**, kliknij przycisk **Uwierzytelnij**. Okno przeglądarki otwiera i wyświetla monit o Zaloguj się, postępuj zgodnie z instrukcjami w celu ukończenia uwierzytelniania.

Na **źródło sterowania — Podsumowanie** strony, wprowadź informacje i kliknij przycisk **Zapisz**. W poniższej tabeli przedstawiono opis dostępnych pól.

|Właściwość  |Opis  |
|---------|---------|
|Nazwa kontroli źródła     | Przyjazna nazwa dla kontroli źródła. *Ta nazwa musi zawierać tylko litery i cyfry.*        |
|Typ kontroli źródła     | Typ źródło kontroli źródła. Dostępne opcje:</br> GitHub</br>Repozytoriów platformy Azure (Git)</br> Repozytoriów platformy Azure (TFVC)        |
|Repozytorium     | Nazwa repozytorium lub projektu. Pierwszych 200 repozytoria są zwracane. Aby znaleźć repozytorium, wpisz nazwę w polu, a następnie kliknij przycisk **wyszukiwania w witrynie GitHub**.|
|Branch     | Odgałęzienie do pobierania plików źródłowych z. Przeznaczone dla gałęzi jest niedostępna dla kontrolek typu źródłowego TFVC.          |
|Ścieżka folderu     | Folder, który zawiera elementy runbook w celu synchronizacji. Przykład: /Runbooks </br>*Tylko elementy runbook w folderze określonym są synchronizowane. Rekursja nie jest obsługiwane.*        |
|Auto Sync<sup>1</sup>     | Włącza lub wyłącza automatyczne synchronizacji, gdy przeprowadzane jest zatwierdzenie w repozytorium kontroli źródła         |
|Publikowanie elementu Runbook     | Jeśli ustawiono **na**po elementach runbook są synchronizowane z kontroli źródła będzie były automatycznie publikowane.         |
|Opis     | Pole tekstowe w celu dostarczenie dodatkowych szczegółów        |

<sup>1</sup> umożliwia automatyczną synchronizację podczas konfigurowania integracji kontroli źródła korzystając z repozytoriów platformy Azure, musi być administratorem projektu.

![Źródło sterowania — podsumowanie](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Twoje dane logowania dla repozytorium kontroli źródła może być inna niż nazwa logowania do witryny Azure portal. Upewnij się, że zalogowano się za pomocą prawidłowe konto dla repozytorium kontroli źródła podczas konfigurowania kontroli źródła. W przypadku wątpliwości, otwórz nową kartę w przeglądarce Wyloguj się z witryną visualstudio.com lub github.com i spróbuj ponownie nawiązującego połączenie kontroli źródła.

## <a name="configure-source-control---powershell"></a>Konfigurowanie kontroli źródła — PowerShell

Program PowerShell umożliwia również konfigurowanie kontroli źródła w usłudze Azure Automation. Aby skonfigurować kontrolę źródła za pomocą poleceń cmdlet programu PowerShell, osobistego tokenu dostępu (PAT) jest wymagana. Możesz użyć [New AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) do utworzenia połączenia z kontrolą źródła. Polecenie cmdlet wymaga bezpiecznego ciągu osobisty Token dostępu, aby dowiedzieć się, jak utworzyć bezpieczny ciąg, zobacz [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Repozytoriów platformy Azure (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Repozytoriów platformy Azure (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Uprawnienia token pat

Kontrola źródła wymaga niektóre minimalne uprawnienia osobiste tokeny dostępu. Poniższe tabele zawierają minimalne uprawnienia wymagane dla usług GitHub i repozytoriów platformy Azure.

#### <a name="github"></a>GitHub

Aby uzyskać więcej informacji na temat tworzenia osobistego tokenu dostępu w usłudze GitHub, odwiedź stronę [Tworzenie osobistego tokenu dostępu dla wiersza polecenia](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Zakres  |Opis  |
|---------|---------|
|**repozytorium**     |         |
|repozytorium: stan     | Stan zatwierdzania dostępu         |
|repo_deployment      | Stan wdrożenia dostępu         |
|public_repo     | Dostęp do publicznych repozytoriów         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Zapis punkty zaczepienia repozytorium         |
|read:repo_hook|Przeczytaj repozytorium punktów zaczepienia|

#### <a name="azure-repos"></a>Azure Repos

Aby uzyskać więcej informacji o tworzeniu osobistego tokenu dostępu w repozytoriach usługi Azure, odwiedź stronę [uwierzytelniania dostępu za pomocą osobiste tokeny dostępu](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

|Zakres  |
|---------|
|Kod (odczyt)     |
|Projekt i zespół (odczyt)|
|Tożsamość (odczyt)      |
|Profil użytkownika (odczyt)     |
|Elementy robocze (odczyt)    |
|Połączenia usługi (Odczyt, zapytań i zarządzanie nimi)<sup>1</sup>    |

<sup>1</sup> uprawnień połączenia usługi jest tylko wymagane, jeśli włączono autosync.

## <a name="syncing"></a>Synchronizowanie

Wybierz źródło z tabeli na **kontroli źródła** strony. Kliknij przycisk **Rozpocznij synchronizację** można uruchomić procesu synchronizacji.

Można wyświetlić stan bieżącego zadania synchronizacji lub poprzednimi, klikając **Synchronizuj zadania** kartę. Na **kontroli źródła** listę rozwijaną, wybierz kontroli źródła.

![Stan synchronizacji](./media/source-control-integration/sync-status.png)

Kliknięcie zadania na służy do wyświetlania danych wyjściowych zadania. Poniższy przykład przedstawia dane wyjściowe z zadania synchronizacji kontroli źródła.

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

Dodatkowe rejestrowanie jest dostępna, wybierając **wszystkie dzienniki** na **Podsumowanie zadania synchronizacji kontroli źródła** strony. Te wpisy w dzienniku dodatkowe mogą pomóc rozwiązać problemy, które mogą wystąpić w przypadku korzystania z kontroli źródła.

## <a name="disconnecting-source-control"></a>Trwa rozłączanie kontroli źródła

Aby rozłączyć repozytorium kontroli źródła, otwórz **kontroli źródła** w obszarze **ustawienia konta** na koncie usługi Automation.

Wybierz kontrolę źródła, które chcesz usunąć. Na **źródło sterowania — Podsumowanie** kliknij **Usuń**.

## <a name="encoding"></a>Kodowanie

Wiele osób edytowania elementów runbook w Twoim repozytorium kontroli źródła z różnymi edytorami, istnieje możliwość napotkania problemów kodowania. Taka sytuacja może prowadzić do nieprawidłowych znaków w elemencie runbook. Aby dowiedzieć się więcej na ten temat, zobacz [typowe przyczyny problemów z kodowaniem](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz artykuł [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
