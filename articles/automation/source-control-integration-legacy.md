---
title: Integracja z kontrolą źródła w usłudze Azure Automation — starsza wersja
description: W tym artykule opisano integrację kontroli źródła z usługą GitHub w usłudze Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: 651b97dabfd3cce858ea1f905a39c10bd7d81c41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75417439"
---
# <a name="source-control-integration-in-azure-automation---legacy"></a>Integracja z kontrolą źródła w usłudze Azure Automation — starsza wersja

> [!NOTE]
> Istnieje nowe środowisko kontroli źródła. Aby dowiedzieć się więcej o nowym doświadczeniu, zobacz [Kontrola źródła (Wersja zapoznawcza)](source-control-integration.md).

Integracja kontroli źródła umożliwia skojarzenie śmigiełszków umownych na koncie automatyzacji z repozytorium kontroli źródła Usługi GitHub. Kontrola źródła umożliwia łatwą współpracę z zespołem, śledzenie zmian i przywracanie do wcześniejszych wersji śmiętków. Na przykład kontrola źródła umożliwia synchronizowanie różnych gałęzi kontroli źródła z kontami automatyzacji deweloperskich, testowych lub produkcyjnych, co ułatwia promowanie kodu, który został przetestowany w środowisku programistycznym do automatyzacji produkcji Konta.

Kontrola źródła umożliwia wypychanie kodu z usługi Azure Automation do kontroli źródła lub ściąganie uruchomieniu chów z kontroli źródła do usługi Azure Automation. W tym artykule opisano sposób konfigurowania kontroli źródła w środowisku usługi Azure Automation. Zaczynamy od skonfigurowania usługi Azure Automation, aby uzyskać dostęp do repozytorium GitHub i przejść przez różne operacje, które można wykonać za pomocą integracji kontroli źródła. 

> [!NOTE]
> Kontrola źródła obsługuje ściąganie i wypychanie [uruchomieniu ytyczek przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) oraz [uruchomieniu programy PowerShell.](automation-runbook-types.md#powershell-runbooks) [Graficzne programy runbook](automation-runbook-types.md#graphical-runbooks) nie są jeszcze obsługiwane.

Istnieją dwa proste kroki wymagane do skonfigurowania kontroli źródła dla konta automatyzacji i tylko jeden, jeśli masz już konto GitHub. Oto one:

## <a name="step-1--create-a-github-repository"></a>Krok 1 – Tworzenie repozytorium GitHub

Jeśli masz już konto GitHub i repozytorium, które chcesz połączyć z usługą Azure Automation, zaloguj się do istniejącego konta i zacznij od kroku 2 poniżej. W przeciwnym razie przejdź do [GitHub](https://github.com/), zarejestruj się w celu utworzenia nowego konta i [utwórz nowe repozytorium.](https://help.github.com/articles/create-a-repo/)

## <a name="step-2--set-up-source-control-in-azure-automation"></a>Krok 2 — konfigurowanie kontroli źródła w usłudze Azure Automation

1. Na stronie Konto automatyzacji w witrynie Azure portal w obszarze **Ustawienia konta**kliknij pozycję **Kontrola źródła.**

2. Zostanie otwarta strona **Kontrola źródła,** na której można skonfigurować szczegóły konta Usługi GitHub. Poniżej znajduje się lista parametrów do skonfigurowania:  

   | **Parametr** | **Opis** |
   |:--- |:--- |
   | Wybierz źródło |Wybierz źródło. Obecnie obsługiwany jest tylko **GitHub.** |
   | Autoryzacja |Kliknij przycisk **Autore,** aby udzielić usługi Azure Automation dostępu do repozytorium Usługi GitHub. Jeśli użytkownik jest już zalogowany do swojego konta Usługi GitHub w innym oknie, używane są poświadczenia tego konta. Po pomyślnym zakończeniu autoryzacji na stronie zostanie wyświetlona nazwa użytkownika Usługi GitHub w obszarze **Właściwość autoryzacji**. |
   | Wybierz repozytorium |Wybierz repozytorium GitHub z listy dostępnych repozytoriów. |
   | Wybierz gałąź |Wybierz gałąź z listy dostępnych gałęzi. Tylko gałąź **główna** jest wyświetlana, jeśli nie utworzono żadnych gałęzi. |
   | Ścieżka folderu księgi chybienia |Ścieżka folderu runbook określa ścieżkę w repozytorium GitHub, z której chcesz wypchnąć lub wyciągnąć kod. Należy go wprowadzić w formacie **/foldername/subfoldername**. Tylko elementy runbook w ścieżce folderu runbooka zostaną zsynchronizowane z kontem automatyzacji. Księgi runbook w podfolderach ścieżki folderu runbook **NIE** będą synchronizowane. Służy **/** do synchronizowania wszystkich śmięty w repozytorium. |
3. Na przykład, jeśli masz repozytorium o nazwie **PowerShellScripts,** które zawiera folder o nazwie **RootFolder**, który zawiera folder o nazwie **Podfolder**. Do zsynchronizowania każdego poziomu folderu można użyć następujących ciągów:

   1. Aby zsynchronizować runbooki z **repozytorium,** ścieżka folderu księgi runbook jest*/*
   2. Aby zsynchronizować pliki runbook z **katalogu RootFolder**, ścieżka folderu księgi runbook jest */RootFolder*
   3. Aby zsynchronizować programy runbook z **podfolderu,** ścieżka folderu księgi runbook jest */RootFolder/SubFolder*.
4. Po skonfigurowaniu parametrów są one wyświetlane na stronie **Konfigurowanie kontroli źródła.**  

    ![Strona kontrolna Źródło z ustawieniami](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. Po kliknięciu **przycisku OK**integracja kontroli źródła jest teraz skonfigurowana dla twojego konta automatyzacji i powinna zostać zaktualizowana o informacje z usługi GitHub. Teraz możesz kliknąć tę część, aby wyświetlić całą historię zadań synchronizacji kontroli źródła.  

    ![Wartości dla bieżącej skonfigurowana konfiguracji formantu źródła](media/source-control-integration-legacy/automation-RepoValues.png)
6. Po skonfigurowaniu kontroli źródła dwa [zasoby zmienne](automation-variables.md) są tworzone na koncie automatyzacji. Ponadto autoryzowana aplikacja jest dodawana do twojego konta GitHub.

   * Zmienna **Microsoft.Azure.Automation.SourceControl.Connection** zawiera wartości ciągu połączenia, jak pokazano poniżej.  

     | **Parametr** | **Wartość** |
     |:--- |:--- |
     | `Name`  |Microsoft.Azure.Automation.SourceControl.Connection |
     | `Type`  |Ciąg |
     | `Value` |{"Oddział":\<*Nazwa oddziału*>,"RunbookFolderPath":\<Ścieżka*folderu elementu runbook*>,"ProviderType":\<ma wartość*1 dla> GitHub,"Repozytorium":* \<*Nazwa repozytorium*>,"Nazwa użytkownika":\<Nazwa użytkownika usługi*GitHub*>} |

   * Zmienna **Microsoft.Azure.Automation.SourceControl.OAuthToken**zawiera bezpieczną zaszyfrowaną wartość urządzenia OAuthToken.  

     |**Parametr**            |**Wartość** |
     |:---|:---|
     | `Name`  | Microsoft.Azure.Automation.SourceControl.OAuthToken |
     | `Type`  | Nieznany(zaszyfrowany) |
     | `Value` | <*Szyfrowany OAuthToken*> |  

     ![Okno przedstawiające zmienne kontroli źródła](media/source-control-integration-legacy/automation-Variables.png)  

   * **Automation Source Control** jest dodawany jako autoryzowana aplikacja do konta GitHub. Aby wyświetlić aplikację: Na stronie głównej usługi GitHub przejdź do aplikacji**ustawień** >  **profilu** > **.** Ta aplikacja umożliwia usługi Azure Automation do synchronizacji repozytorium Usługi GitHub do konta automatyzacji.  

     ![Ustawienia aplikacji w usłudze GitHub](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="using-source-control-in-automation"></a>Korzystanie z kontroli źródła w automatyzacji

### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Zaewidencjonuj projekt runbooka z usługi Azure Automation do kontroli źródła

Zaewidencjonowanie systemu Runbook umożliwia wypychanie zmian wprowadzonych do systemu runbook w usłudze Azure Automation do repozytorium kontroli źródła. Poniżej znajdują się kroki, aby zaewidencjonować w bieśmięcie:

1. Z konta automatyzacji [utwórz nowy tekstowy element runbook](automation-first-runbook-textual.md)lub [edytuj istniejący, tekstowy element runbook](automation-edit-textual-runbook.md). Ten projekt runbook może być przepływpracy programu PowerShell lub uruchomieniu skrypt programu PowerShell.  
2. Po edycji ekstytuj go zapisz i kliknij **polecenie Zaewidencjonuj** na stronie **Edytuj.**  

    ![Okno z przyciskiem Checkin do github](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > Zaewidencjonowanie z usługi Azure Automation zastępuje kod, który obecnie istnieje w formancie źródłowym. Git równoważne instrukcji wiersza polecenia do odprawy jest **git add + git commit + git push**  

3. Po **kliknięciu przycisku zaewidencjonowania**zostanie wyświetlony monit z komunikatem potwierdzającym, kliknij przycisk **Tak,** aby kontynuować.  

    ![Okno dialogowe potwierdzające zaewidencjonowanie formantu źródła](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. Zaewidencjonowanie rozpoczyna runbook kontroli źródła: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Ten zestaw runbook łączy się z gitHub i wypycha zmiany z usługi Azure Automation do repozytorium. Aby wyświetlić zaewidencjonowany w historii zadań, wróć do **integracji kontroli źródła** kartę i kliknij, aby otworzyć stronę Synchronizacja repozytorium. Ta strona zawiera wszystkie zadania kontroli źródła.  Wybierz zadanie, które chcesz wyświetlić, i kliknij, aby wyświetlić szczegóły.  

    ![Okno z wynikami zadania synchronizacji](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > Elementy runbook kontroli źródła są specjalne elementy runbook automatyzacji, których nie można wyświetlić ani edytować. Chociaż nie są one wyświetlane na liście śmiwalnika, na liście zadań są wyświetlane zadania synchronizacji.

5. Nazwa zmodyfikowanego przewodniczącego jest wysyłana jako parametr wejściowy dla zaewidencjonowanego w bień runbook. Szczegóły zadania można [wyświetlić,](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) rozwiń pozycję runbook na stronie **Synchronizacja repozytorium.**  

    ![Okno przedstawiające dane wejściowe dla zadania synchronizacji](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Odśwież repozytorium GitHub po zakończeniu zadania, aby wyświetlić zmiany.  Powinno być zatwierdzenie w repozytorium z komunikatem zatwierdzania: **Zaktualizowana *nazwa uruchomieniu* w usłudze Azure Automation.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Synchronizowanie śmięty pomieszczeń z kontroli źródła z usługą Azure Automation

Przycisk synchronizacji na stronie Synchronizacja repozytorium umożliwia pobranie wszystkich ściągnięcia wszystkich przewodów runbook w ścieżce folderu umatora repozytorium do konta automatyzacji. To samo repozytorium można zsynchronizować z więcej niż jednym kontem automatyzacji. Poniżej znajdują się kroki, aby zsynchronizować runbook:

1. Na koncie Automatyzacja, na którym skonfigurowana jest kontrola źródła, otwórz stronę **Integracja/Synchronizacja repozytorium kontroli źródła** i kliknij przycisk **Synchronizuj**.  Zostanie wyświetlony monit z komunikatem potwierdzającym, kliknij przycisk **Tak,** aby kontynuować.  

    ![Przycisk Synchronizuj z komunikatem potwierdzającym, że wszystkie programy runbook zostaną zsynchronizowane](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. Synchronizacja uruchamia runbook: **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Ten zestaw runbook łączy się z gitHub i pobiera zmiany z repozytorium do usługi Azure Automation. Dla tej akcji powinno zostać wyświetlona nowa praca na stronie **Synchronizacja repozytorium.** Aby wyświetlić szczegółowe informacje o zadaniu synchronizacji, kliknij, aby otworzyć stronę szczegółów zadania.  

    ![Okno przedstawiające wyniki synchronizacji zadania synchronizacji w repozytorium Usługi GitHub](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > Synchronizacja z kontroli źródła zastępuje wersję roboczą śmigieł, które obecnie istnieją na koncie automatyzacji dla **wszystkich** śmigieł, które są obecnie w kontroli źródła. Git równoważne instrukcji wiersza polecenia do synchronizacji jest **git pull**

![Okno przedstawiające wszystkie dzienniki z zadania synchronizacji kontroli źródła zawieszone](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnecting-source-control"></a>Odłączanie kontrolki źródła

Aby odłączyć się od konta GitHub, otwórz stronę Synchronizacja repozytorium i kliknij przycisk **Rozłącz**. Po odłączeniu kontroli źródła, elementy runbook, które zostały zsynchronizowane wcześniej pozostają na koncie automatyzacji, ale strona Synchronizacja repozytorium nie zostanie włączona.  

  ![Okno z przyciskiem Rozłącz, aby odłączyć kontrolkę źródła](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat integracji kontroli źródła, zobacz następujące zasoby:  

* [Automatyzacja platformy Azure: integracja kontroli źródeł w usłudze Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Automatyzacja platformy Azure: integrowanie kontroli źródła uruchomieniu za pomocą usługi Azure DevOps](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  
