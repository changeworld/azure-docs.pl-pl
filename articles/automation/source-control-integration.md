---
title: Integracja kontroli źródła w usłudze Automatyzacja Azure
description: W tym artykule opisano integrację kontroli źródła z usługą GitHub w usłudze Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 166902978d1641458f18aeee6269c8d819e85233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132925"
---
# <a name="source-control-integration-in-azure-automation"></a>Integracja kontroli źródła w usłudze Automatyzacja Azure

 Integracja kontroli źródła w usłudze Azure Automation obsługuje synchronizację jednokierunkową z repozytorium kontroli źródła. Kontrola źródła umożliwia przestrzeganie śmigiełych śmigieł na koncie automatyzacji za pomocą skryptów w repozytorium kontroli źródła repozytorium źródła usługi GitHub lub usługi Azure Repozytorium. Ta funkcja ułatwia promowanie kodu, który został przetestowany w środowisku deweloperskim do konta automatyzacji produkcji.
 
 Integracja z kontrolą źródła umożliwia łatwą współpracę z zespołem, śledzenie zmian i przywracanie do wcześniejszych wersji śmięty. Na przykład kontrola źródła umożliwia synchronizowanie różnych gałęzi w kontroli źródła z kont automatyzacji rozwoju, testu i produkcji. 

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="source-control-types"></a>Typy formantów źródła

Usługa Azure Automation obsługuje trzy typy kontroli źródła:

* GitHub
* Repozytorium platformy Azure (Git)
* Repozytorium platformy Azure (TFVC)

## <a name="prerequisites"></a>Wymagania wstępne

* Repozytorium kontroli źródła (GitHub lub Repozytorium platformy Azure)
* [Konto Uruchom jako](manage-runas-account.md)
* [Najnowsze moduły platformy Azure](automation-update-azure-modules.md) na koncie automatyzacji, w tym `Az.Accounts` moduł (odpowiednik modułu `AzureRM.Profile`Az )

> [!NOTE]
> Zadania synchronizacji kontroli źródła są uruchamiane na koncie automatyzacji użytkownika i są rozliczane z taką samą szybkością jak inne zadania automatyzacji.

## <a name="configuring-source-control"></a>Konfigurowanie formantu źródła

W tej sekcji opisano, jak skonfigurować formant źródła dla konta automatyzacji. Można użyć witryny Azure portal lub programu PowerShell.

### <a name="configure-source-control-in-azure-portal"></a>Konfigurowanie kontroli źródła w witrynie Azure portal

Ta procedura służy do konfigurowania kontroli źródła przy użyciu witryny Azure portal.

1. Na koncie automatyzacji wybierz pozycję **Formant źródła** i kliknij przycisk **Dodaj**.

    ![Wybierz kontrolka źródła](./media/source-control-integration/select-source-control.png)

2. Wybierz **pozycję Typ formantu źródła**, a następnie kliknij pozycję **Uwierzytelnij**. 

3. Otworzy się okno przeglądarki z monitem o zalogowanie się. Postępuj zgodnie z instrukcjami, aby zakończyć uwierzytelnianie.

4. Na stronie Podsumowanie kontroli źródła użyj pól, aby wypełnić właściwości formantu źródła zdefiniowane poniżej. Kliknij **przycisk Zapisz** po zakończeniu. 

    |Właściwość  |Opis  |
    |---------|---------|
    |Nazwa formantu źródła     | Przyjazna nazwa formantu źródłowego. Nazwa ta musi zawierać tylko litery i cyfry.        |
    |Typ formantu źródła     | Typ mechanizmu kontroli źródła. Dostępne opcje:</br> * GitHub</br>* Repozytorium platformy Azure (Git)</br> * Repozytoria platformy Azure (TFVC)        |
    |Repozytorium     | Nazwa repozytorium lub projektu. Pierwsze 200 repozytoriów są pobierane. Aby wyszukać repozytorium, wpisz nazwę w polu i kliknij przycisk **Wyszukaj w usłudze GitHub**.|
    |Odgałęzienie     | Gałąź, z której ma być pobierana liczba plików źródłowych. Kierowanie gałęzi nie jest dostępne dla typu formantu źródła TFVC.          |
    |Ścieżka folderu     | Folder zawierający elementy runbook do synchronizacji, na przykład **/Runbooks**. Synchronizowane są tylko programy runbook w określonym folderze. Rekursja nie jest obsługiwana.        |
    |Automatyczna synchronizacja<sup>1</sup>     | Ustawienie włączania lub wyłączania automatycznej synchronizacji podczas zatwierdzania w repozytorium kontroli źródła.        |
    |Publikowanie eks-u     | Ustawienie On, jeśli runbooks są automatycznie publikowane po synchronizacji z kontroli źródła i Off w przeciwnym razie.           |
    |Opis     | Tekst określający dodatkowe szczegóły dotyczące formantu źródłowego.        |

    <sup>1</sup> Aby włączyć automatyczną synchronizację podczas konfigurowania integracji kontroli źródła z repozytorium platformy Azure, musisz być administratorem projektu.

   ![Podsumowanie kontroli źródła](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Identyfikator logowania do repozytorium kontroli źródła może się różnić od logowania do witryny Azure portal. Upewnij się, że użytkownik jest zalogowany przy prawidłowym koncie repozytorium kontroli źródła podczas konfigurowania formantu źródła. W razie wątpliwości otwórz nową kartę w przeglądarce, wyloguj się z **dev.azure.com** **, visualstudio.com**lub **github.com**i spróbuj ponownie połączyć się z kontrolą źródła.

### <a name="configure-source-control-in-powershell"></a>Konfigurowanie kontroli źródła w programie PowerShell

Za pomocą programu PowerShell można również skonfigurować kontrolę źródła w usłudze Azure Automation. Aby użyć poleceń cmdlet programu PowerShell dla tej operacji, potrzebujesz tokenu dostępu osobistego (PAT). Użyj polecenia cmdlet [New-AzAutomationSourceControl,](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) aby utworzyć połączenie kontroli źródła. To polecenie cmdlet wymaga bezpiecznego ciągu dla PAT. Aby dowiedzieć się, jak utworzyć bezpieczny ciąg znaków, zobacz [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

Poniższe podsekcje ilustrują tworzenie programu PowerShell połączenia kontroli źródła dla usługi GitHub, Usługi Azure Repos (Git) i Azure Repos (TFVC). 

#### <a name="create-source-control-connection-for-github"></a>Tworzenie połączenia kontroli źródła dla usługi GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Tworzenie połączenia kontroli źródła dla usługi Azure Repos (Git)

> [!NOTE]
> Usługa Azure Repos (Git) używa adresu URL, który uzyskuje dostęp do **dev.azure.com** zamiast **visualstudio.com**, używane we wcześniejszych formatach. Starszy format `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` adresu URL jest przestarzały, ale nadal obsługiwany. Preferowany jest nowy format.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Tworzenie połączenia kontroli źródła dla repozytorium platformy Azure (TFVC)

> [!NOTE]
> Usługa Azure Repos (TFVC) używa adresu URL, który uzyskuje dostęp do **dev.azure.com** zamiast **visualstudio.com**, używane we wcześniejszych formatach. Starszy format `https://<accountname>.visualstudio.com/<projectname>/_versionControl` adresu URL jest przestarzały, ale nadal obsługiwany. Preferowany jest nowy format.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Uprawnienia tokenu dostępu osobistego (PAT)

Kontrola źródła wymaga pewnych minimalnych uprawnień dla PACT. Poniższe podsekcje zawierają minimalne uprawnienia wymagane dla witryn GitHub i usługi Azure Repos.

##### <a name="minimum-pat-permissions-for-github"></a>Minimalne uprawnienia PAT dla usługi GitHub

W poniższej tabeli zdefiniowano minimalne uprawnienia PAT wymagane dla usługi GitHub. Aby uzyskać więcej informacji na temat tworzenia pat w usłudze GitHub, zobacz [Tworzenie osobistego tokenu dostępu dla wiersza polecenia](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Zakres  |Opis  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Dostęp do stanu zatwierdzenia         |
|`repo_deployment`      | Stan wdrożenia dostępu         |
|`public_repo`     | Dostęp do publicznych repozytoriów         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Haki repozytorium zapisu         |
|`read:repo_hook`|Odczyt haków repozytorium|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Minimalne uprawnienia PAT dla repozytorium platformy Azure

Poniższa lista określa minimalne uprawnienia PAT wymagane dla repozytorium platformy Azure. Aby uzyskać więcej informacji na temat tworzenia pat w repo platformy Azure, zobacz [Uwierzytelnianie dostępu za pomocą tokenów dostępu osobistego](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

| Zakres  |  Typ dostępu  |
|---------| ----------|
| `Code`      | Odczyt  |
| `Project and team` | Odczyt |
| `Identity` | Odczyt     |
| `User profile` | Odczyt     |
| `Work items` | Odczyt    |
| `Service connections` | Odczyt, zapytanie,<sup>zarządzanie 1</sup>    |

<sup>1</sup> `Service connections` Uprawnienie jest wymagane tylko wtedy, gdy włączono autosync.

## <a name="synchronizing"></a>Synchronizowanie

Wykonaj następujące kroki, aby zsynchronizować z kontrolą źródła. 

1. Wybierz źródło z tabeli na stronie Formantuj źródło. 

2. Kliknij **przycisk Rozpocznij synchronizację,** aby rozpocząć proces synchronizacji. 

3. Wyświetl stan bieżącego zadania synchronizacji lub poprzednich, klikając kartę **Synchronizuj zadania.** 

4. W menu **rozwijanym Kontrola źródła** wybierz mechanizm kontroli źródła.

    ![Stan synchronizacji](./media/source-control-integration/sync-status.png)

5. Kliknięcie zadania umożliwia wyświetlenie danych wyjściowych zadania. Poniższy przykład jest dane wyjściowe z zadania synchronizacji kontroli źródła.

    ```output
    ===================================================================

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

    ==================================================================

    ```

6. Dodatkowe rejestrowanie jest dostępne po wybraniu **opcji Wszystkie dzienniki** na stronie Podsumowanie zadania synchronizacji kontroli źródła. Te dodatkowe wpisy dziennika może pomóc w rozwiązywaniu problemów, które mogą pojawić się podczas korzystania z kontroli źródła.

## <a name="disconnecting-source-control"></a>Odłączanie kontrolki źródła

Aby odłączyć się od repozytorium formantu źródła:

1. Kontrola open **source** w obszarze **Ustawienia konta** na koncie Automatyzacja.

2. Wybierz mechanizm kontroli źródła do usunięcia. 

3. Na stronie Podsumowanie kontroli źródła kliknij pozycję **Usuń**.

## <a name="handling-encoding-issues"></a>Obsługa problemów z kodowaniem

Jeśli wiele osób edytuje runbooki w repozytorium kontroli źródła przy użyciu różnych edytorów, mogą wystąpić problemy z kodowaniem. Aby dowiedzieć się więcej o tej sytuacji, zobacz [Typowe przyczyny problemów z kodowaniem](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues).

## <a name="updating-the-pat"></a>Aktualizacja pat

Obecnie nie można użyć witryny Azure portal, aby zaktualizować PAT w formancie źródła. Po wygaśnięciu lub odwołaniu pat, można zaktualizować formant źródła za pomocą nowego tokenu dostępu w jeden z następujących sposobów:

* Użyj [interfejsu API REST](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Użyj polecenia cmdlet [Update-AzAutomationSourceControl.](/powershell/module/az.automation/update-azautomationsourcecontrol)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o typach elementów runbook oraz ich zaletach i ograniczeniach, zobacz [Typy elementów runbook usługi Azure Automation](automation-runbook-types.md).
