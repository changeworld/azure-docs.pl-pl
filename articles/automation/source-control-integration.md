---
title: Źródło Integracja kontroli w usłudze Azure Automation
description: W tym artykule opisano Integracja kontroli źródła z usługą GitHub w usłudze Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/26/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5778c38d5a0c44e42b83fd139078be1f0bb45f7f
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023751"
---
# <a name="source-control-integration-in-azure-automation"></a>Integracja kontroli źródła w usłudze Automatyzacja Azure

Kontrola źródła pozwala na zachowanie elementów runbook w automatyzacji konta są aktualne za pomocą skryptów w repozytorium kontroli źródła GitHub lub DevOps platformy Azure. Kontrola źródła pozwala łatwo współpracować z zespołem, śledzenie zmian i wrócić do wcześniejszych wersji elementów runbook. Na przykład kontroli źródła umożliwia synchronizowanie różnych gałęzi w kontroli źródła do kont usługi Automation rozwoju, testów lub produkcji. Ułatwia promowanie kod, który został przetestowany w środowisku projektowym konta usługi Automation w środowisku produkcyjnym.

Usługa Azure Automation obsługuje 3 typy kontroli źródła:

* GitHub
* Usługa Azure DevOps (Git)
* Usługa Azure DevOps (TFVC)

## <a name="pre-requisites"></a>Wymagania wstępne

* Repozytorium kontroli źródła (GitHub lub DevOps platformy Azure)
* Poprawny [uprawnienia](#personal-access-token-permissions) do repozytorium kontroli źródła
* A [Run-As konta i połączenie](manage-runas-account.md)

> [!NOTE]
> Zadania synchronizacji kontroli źródła uruchamiane w ramach użytkownicy konta usługi Automation i są rozliczane według stawki takie same, jak inne zadania automatyzacji.

## <a name="configure-source-control"></a>Konfigurowanie kontroli źródła

W ramach konta usługi Automation wybierz **kontroli źródła (wersja zapoznawcza)** i kliknij przycisk **+ Dodaj**

![Wybierz kontrolę źródła](./media/source-control-integration/select-source-control.png)

Wybierz **typ kontroli źródła**, kliknij przycisk **Uwierzytelnij**.

Przejrzyj Strona uprawnień aplikacji żądanie i kliknij przycisk **Akceptuj**.

Na **źródło sterowania — Podsumowanie** strony, wprowadź informacje i kliknij przycisk **Zapisz**. W poniższej tabeli przedstawiono opis dostępnych pól.

|Właściwość  |Opis  |
|---------|---------|
|Nazwa kontroli źródła     | Przyjazna nazwa, do kontroli źródła        |
|Typ kontroli źródła     | Typ źródło kontroli źródła. Dostępne opcje:</br> Github</br>Usługa Azure DevOps (Git)</br> Usługa Azure DevOps (TFVC)        |
|Repozytorium     | Nazwa repozytorium lub projektu. Ta wartość jest pobierany z repozytorium kontroli źródła. Przykład: $/ ContosoFinanceTFVCExample         |
|Rozgałęzienie     | Odgałęzienie do pobierania plików źródłowych z. Gałąź określania wartości docelowej nie jest dostępna dla kontrolek typu źródłowego TFVC.          |
|Ścieżka folderu     | Folder, który zawiera elementy runbook w celu synchronizacji. Przykład: /Runbooks         |
|Automatyczna synchronizacja     | Włącza lub wyłącza automatyczne synchronizacji, gdy przeprowadzane jest zatwierdzenie w repozytorium kontroli źródła         |
|Publikowanie elementu Runbook     | Jeśli ustawiono **na**po elementach runbook są synchronizowane z kontroli źródła będzie były automatycznie publikowane.         |
|Opis     | Pole tekstowe w celu dostarczenie dodatkowych szczegółów        |

![Źródło sterowania — podsumowanie](./media/source-control-integration/source-control-summary.png)

## <a name="syncing"></a>Synchronizowanie

Konfigurowanie automatyczna synchronizacja podczas konfigurowania integracji kontroli źródła, synchronizacja początkowa jest uruchamiana automatycznie. Jeśli automatyczna synchronizacja nie została ustawiona, wybierz źródło, z tabeli **(wersja zapoznawcza) kontroli źródła** strony. Kliknij przycisk **Rozpocznij synchronizację** można uruchomić procesu synchronizacji.  

Można wyświetlić stan bieżącego zadania synchronizacji lub poprzednimi, klikając **Synchronizuj zadania** kartę. Na **kontroli źródła** listę rozwijaną, wybierz kontroli źródła.

![Stan synchronizacji](./media/source-control-integration/sync-status.png)

Kliknięcie zadania na służy do wyświetlania danych wyjściowych zadania. Poniższy przykład przedstawia dane wyjściowe z zadania synchronizacji kontroli źródła.

```output
========================================================================================================

Azure Automation Source Control Public Preview.
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

## <a name="personal-access-token-permissions"></a>Uprawnienia token pat

Kontrola źródła wymaga niektóre minimalne uprawnienia osobiste tokeny dostępu. Poniższe tabele zawierają minimalne uprawnienia wymagane dla usług GitHub i DevOps platformy Azure.

### <a name="github"></a>GitHub

|Zakres  |Opis  |
|---------|---------|
|**repozytorium**     |         |
|repozytorium: stan     | Stan zatwierdzania dostępu         |
|repo_deployment      | Stan wdrożenia dostępu         |
|public_repo     | Dostęp do publicznych repozytoriów         |
|**Admin: repo_hook**     |         |
|zapis: repo_hook     | Zapis punkty zaczepienia repozytorium         |
|Odczyt: repo_hook|Przeczytaj repozytorium punktów zaczepienia|

### <a name="azure-devops"></a>Azure DevOps

|Zakres  |
|---------|
|Kod (odczyt)     |
|Projekt i zespół (odczyt)|
|Tożsamość (odczyt)      |
|Profil użytkownika (odczyt)     |
|Elementy robocze (odczyt)    |
|Połączenia usługi (Odczyt, zapytań i zarządzanie nimi)<sup>1</sup>    |

<sup>1</sup>uprawnienia do połączenia usługi jest tylko wymagane, jeśli włączono autosync.

## <a name="disconnecting-source-control"></a>Trwa rozłączanie kontroli źródła

Aby rozłączyć repozytorium kontroli źródła, otwórz **(wersja zapoznawcza) kontroli źródła** w obszarze **ustawienia konta** na koncie usługi Automation.

Wybierz kontrolę źródła, które chcesz usunąć. Na **źródło sterowania — Podsumowanie** kliknij **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz artykuł [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
