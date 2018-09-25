---
title: Źródło Integracja kontroli w usłudze Azure Automation
description: W tym artykule opisano Integracja kontroli źródła z usługą GitHub w usłudze Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c2d13a409d095bca64da781e5c5ca58553f9710c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47048011"
---
# <a name="source-control-integration-in-azure-automation"></a>Integracja kontroli źródła w usłudze Automatyzacja Azure

Kontrola źródła umożliwia pamiętać elementów runbook usługi Automation konto znajdują się aktualne za pomocą skryptów w repozytorium kontroli źródła GitHub lub operacji deweloperskich platformy Azure. Kontrola źródła pozwala łatwo współpracować z zespołem, śledzenie zmian i wrócić do wcześniejszych wersji elementów runbook. Na przykład kontroli źródła umożliwia synchronizowanie różnych gałęzi w kontroli źródła do rozwoju, testów lub produkcji kont usługi Automation, co ułatwia promowanie kod, który został przetestowany w środowisku projektowym automatyzacji w środowisku produkcyjnym konto.

Usługa Azure Automation obsługuje 3 typy kontroli źródła:

* GitHub
* Visual Studio Team Services (Git)
* Visual Studio Team Services (TFVC)

## <a name="pre-requisites"></a>Wymagania wstępne

* Repozytorium kontroli źródła (GitHub lub Visual Studio Team Services)
* A [Run-As konta i połączenie](manage-runas-account.md)

> [!NOTE]
> Zadania synchronizacji kontroli źródła uruchamiane w ramach użytkownicy konta usługi Automation i są rozliczane według stawki takie same, jak inne zadania automatyzacji.

## <a name="configure-source-control"></a>Konfigurowanie kontroli źródła

W ramach konta usługi Automation wybierz **kontroli źródła (wersja zapoznawcza)** i kliknij przycisk **+ Dodaj**

![Wybierz kontrolę źródła](./media/source-control-integration/select-source-control.png)

Wybierz **typ kontroli źródła** , kliknij przycisk **Uwierzytelnij**.

Przejrzyj Strona uprawnień aplikacji żądanie i kliknij przycisk **Akceptuj**.

Na **źródło sterowania — Podsumowanie** strony, wprowadź informacje i kliknij przycisk **Zapisz**. W poniższej tabeli przedstawiono opis dostępnych pól.

|Właściwość  |Opis  |
|---------|---------|
|Nazwa kontroli źródła     | Przyjazna nazwa, do kontroli źródła        |
|Typ kontroli źródła     | Typ źródło kontroli źródła. Dostępne opcje:</br> Github</br>Visual Studio Team Services (Git)</br>Visual Studio Team Services (TFVC)        |
|Repozytorium     | Nazwa repozytorium lub projektu. To jest określany na podstawie repozytorium kontroli źródła. Przykład: $/ ContosoFinanceTFVCExample         |
|Rozgałęzienie     | Odgałęzienie do pobierania plików źródłowych z. Gałąź określania wartości docelowej nie jest dostępna dla kontrolek typu źródłowego TFVC.          |
|Ścieżka folderu     | Folder, który zawiera elementy runbook w celu synchronizacji. Przykład: /Runbooks         |
|Automatyczna synchronizacja     | Włącza lub wyłącza automatyczne synchronizacji, gdy przeprowadzane jest zatwierdzenie w repozytorium kontroli źródła         |
|Publikowanie elementu Runbook     | Jeśli ustawiono **na**po elementach runbook są synchronizowane z kontroli źródła będzie były automatycznie publikowane.         |
|Opis     | Pole tekstowe w celu dostarczenie dodatkowych szczegółów        |

![Źródło sterowania — podsumowanie](./media/source-control-integration/source-control-summary.png)

## <a name="syncing"></a>Synchronizowanie

Jeśli automatyczna synchronizacja została ustawiona podczas konfigurowania integracji kontroli źródła, synchronizacja początkowa będzie uruchamiana automatycznie. Jeśli automatyczna synchronizacja nie została ustawiona, wybierz źródło, z tabeli **(wersja zapoznawcza) kontroli źródła** strony. Kliknij przycisk **Rozpocznij synchronizację** można uruchomić procesu synchronizacji.  

Można wyświetlić stan bieżącego zadania synchronizacji lub poprzednimi, klikając **Synchronizuj zadania** kartę. Na **kontroli źródła** listę rozwijaną, wybierz kontroli źródła.

![Stan synchronizacji](./media/source-control-integration/sync-status.png)

Kliknięcie zadania na służy do wyświetlania danych wyjściowych zadania. Oto przykładowe dane wyjściowe z zadania synchronizacji kontroli źródła.

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

## <a name="disconnecting-source-control"></a>Trwa rozłączanie kontroli źródła

Aby rozłączyć repozytorium kontroli źródła, otwórz **(wersja zapoznawcza) kontroli źródła** w obszarze **ustawienia konta** na koncie usługi Automation.

Wybierz kontrolę źródła, które chcesz usunąć. Na **źródło sterowania — Podsumowanie** kliknij **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz artykuł [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
