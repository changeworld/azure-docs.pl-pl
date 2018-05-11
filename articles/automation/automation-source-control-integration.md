---
title: Źródło integracji kontroli usługi Automatyzacja Azure
description: W tym artykule opisano integracji kontroli źródła z usługi GitHub automatyzacji Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 29ec32c933f3dbe07a844bd99a1f5aa3fa57d61b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="source-control-integration-in-azure-automation"></a>Integracja kontroli źródła w usłudze Automatyzacja Azure
Integracja kontroli źródła umożliwia kojarzenie elementów runbook do Twojego konta automatyzacji do repozytorium GitHub kontroli źródła. Kontroli źródła pozwala łatwo współpracować z zespołem śledzenia zmian i przywrócenie wcześniejszych wersji elementów runbook. Na przykład kontroli źródła umożliwia synchronizowanie różnych gałęziach w kontroli źródła do kont automatyzacji rozwoju, testowym lub produkcyjnym, co ułatwia promowanie kod, który był testowany w środowisku projektowania do produkcji automatyzacji konto.

Kontrola źródła umożliwia wypychanie kodu usługi Automatyzacja Azure do kontroli źródła lub ściągnąć elementy runbook z kontroli źródła do automatyzacji Azure. W tym artykule opisano sposób konfigurowania kontroli źródła w środowisku usługi Automatyzacja Azure. Rozpoczniemy konfigurując Automatyzacja Azure, aby uzyskać dostęp do repozytorium GitHub i przeprowadzenie różne operacje, które można wykonać za pomocą integracji kontroli źródła. 

> [!NOTE]
> Kontrola źródła obsługuje ściąganie i wypychanie [elementach runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) oraz [elementy runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks). [Graficznych elementów runbook](automation-runbook-types.md#graphical-runbooks) nie są jeszcze obsługiwane.<br><br>
> 
> 

Istnieją dwa proste kroki wymagane do skonfigurowania kontroli źródła dla konta automatyzacji i tylko jeden z nich, jeśli masz już konto GitHub. Oto one:

## <a name="step-1--create-a-github-repository"></a>Krok 1: Tworzenie repozytorium GitHub
Jeśli masz już konto GitHub i repozytorium, który chcesz połączyć usługi Automatyzacja Azure, następnie zaloguj się do istniejącego konta i uruchomić z kroku 2 poniżej. W przeciwnym razie przejdź do [GitHub](https://github.com/), załóż nowe konto i [utworzyć nowe repozytorium](https://help.github.com/articles/create-a-repo/).

## <a name="step-2--set-up-source-control-in-azure-automation"></a>Krok 2 — Konfiguracja systemu kontroli źródła w usłudze Automatyzacja Azure
1. Z konta automatyzacji strony w portalu Azure w obszarze **ustawienia konta**, kliknij przycisk **kontroli źródła.** 
   
1. **Kontroli źródła** strona zostanie otwarta, w którym można skonfigurować szczegółowe informacje o koncie usługi GitHub. Poniżej znajduje się lista parametrów do skonfigurowania:  
   
   | **Parametr** | **Opis** |
   |:--- |:--- |
   | Wybieranie źródła |Wybierz źródło. Obecnie tylko **GitHub** jest obsługiwana. |
   | Autoryzacja |Kliknij przycisk **autoryzacji** przycisk, aby udzielić dostępu usługi Automatyzacja Azure z Twoim repozytorium GitHub. Jeśli użytkownik jest już zalogowany na koncie usługi GitHub w innym oknie, są używane poświadczenia tego konta. Po autoryzacji zakończy się pomyślnie, strony zostaną wyświetlone w obszarze nazwy użytkownika usługi GitHub **właściwości autoryzacji**. |
   | Wybierz repozytorium |Zaznacz na liście dostępnych repozytoriów repozytorium GitHub. |
   | Wybierz rozgałęzienie |Wybierz gałąź z listy dostępnych gałęzi. Tylko **wzorca** gałęzi jest wyświetlany, jeśli nie utworzono żadnych gałęziach. |
   | Ścieżka folderu Runbook |Ścieżka folderu runbook Określa ścieżkę w repozytorium GitHub, z którego chcesz wypychania lub ściągania kodu. Należy podać w formacie **/nazwa folderu/nazwa podfolderu**. Tylko elementy runbook w ścieżce folderu elementów runbook będą synchronizowane z kontem automatyzacji. Elementy Runbook w podfolderach w ścieżce folderu elementów runbook będą **nie** zsynchronizowane. Użyj **/** na zsynchronizowanie wszystkich elementów runbook w repozytorium. |
3. Na przykład, jeśli masz repozytorium o nazwie **PowerShellScripts** zawiera folder o nazwie **RootFolder**, który zawiera folder o nazwie **podfolder**. Można synchronizować z poziomu każdego folderu za pomocą następujących ciągów:
   
   1. Do synchronizacji elementów runbook **repozytorium**, ścieżka folderu runbook */*
   2. Do synchronizacji elementów runbook **RootFolder**, jest na ścieżce folderu elementów runbook */RootFolder*
   3. Do synchronizacji elementów runbook **podfolder**, jest na ścieżce folderu elementów runbook */RootFolder/podfolder*.
4. Po skonfigurowaniu parametry są wyświetlane na **ustawić kontroli źródła** strony.  
   
    ![Konfigurowanie strony kontroli źródła](media/automation-source-control-integration/automation_02_SourceControlConfigure.png)
5. Po kliknięciu **OK**, integracji kontroli źródła została skonfigurowana pod kątem Twoje konto usługi Automatyzacja i powinien zostać zaktualizowany z informacjami w witrynie GitHub. Możesz teraz kliknąć w tej części, aby wyświetlić wszystkie historię zadania synchronizacji kontroli źródła.  
   
    ![Wartości repozytorium](media/automation-source-control-integration/automation_03_RepoValues.png)
6. Po skonfigurowaniu kontroli źródła w następujących zasobach automatyzacji zostanie utworzony na Twoim koncie automatyzacji:  
   Dwa [zasoby zmiennej](automation-variables.md) są tworzone.  
   
   * Zmienna **Microsoft.Azure.Automation.SourceControl.Connection** zawiera wartości parametrów połączenia, jak pokazano poniżej.  
     
     | **Parametr** | **Wartość** |
     |:--- |:--- |
     | Name (Nazwa) |Microsoft.Azure.Automation.SourceControl.Connection |
     | Typ |Ciąg |
     | Wartość |{"Gałęzi":\<*nazwę gałęzi*>, "RunbookFolderPath":\<*ścieżce folderu elementów Runbook*>, "Typ dostawcy":\<*ma wartość 1 GitHub*>, "Repository":\<*nazwę repozytorium*>, "Nazwa_użytkownika":\<*GitHub Twoja nazwa użytkownika*>} |

    * Zmienna **Microsoft.Azure.Automation.SourceControl.OAuthToken**, zawiera bezpieczne zaszyfrowaną wartość OAuthToken Twojego.  

    |**Parametr**            |**Wartość** |
    |:---|:---|
    | Name (Nazwa)  | Microsoft.Azure.Automation.SourceControl.OAuthToken |
    | Typ | Unknown(Encrypted) |
    | Wartość | <*Zaszyfrowane OAuthToken*> |  

    ![Zmienne](media/automation-source-control-integration/automation_04_Variables.png)  

    * **Kontrola źródła automatyzacji** jest dodawana jako autoryzowanego aplikacji z kontem usługi GitHub. Aby wyświetlić aplikację: ze strony głównej GitHub, przejdź do Twojej **profilu** > **ustawienia** > **aplikacji**. Ta aplikacja umożliwia Automatyzacja Azure, aby zsynchronizować repozytorium GitHub, aby konto usługi Automatyzacja.  

    ![Git aplikacji](media/automation-source-control-integration/automation_05_GitApplication.png)


## <a name="using-source-control-in-automation"></a>Za pomocą kontroli źródła w automatyzacji
### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Ewidencjonowania elementu runbook usługi Automatyzacja Azure do kontroli źródła
Sprawdź element Runbook w umożliwia Wypchnij zmiany wprowadzone do elementu runbook automatyzacji Azure w Twoim repozytorium kontroli źródła. Poniżej przedstawiono kroki, aby zaewidencjonować elementu runbook:

1. Z Twojego konta automatyzacji [Utwórz nowy element runbook tekstową](automation-first-runbook-textual.md), lub [Edytuj istniejący, tekstowy element runbook](automation-edit-textual-runbook.md). Ten element runbook może być przepływu pracy programu PowerShell lub runbook skrypt programu PowerShell.  
2. Po przeprowadzeniu edycji elementu runbook, zapisać go i kliknij przycisk **zaewidencjonowania** z **Edytuj** strony.  
   
    ![Przycisk zaewidencjonowania](media/automation-source-control-integration/automation_06_CheckinButton.png)

     > [!NOTE] 
     > Zaewidencjonuj usługi Automatyzacja Azure zastępuje kod, który już istnieje w kontroli źródła. Jest równoważne instrukcje wiersza polecenia Git do zaewidencjonowania **git Dodaj + zatwierdzenia git + git push**  

1. Po kliknięciu **zaewidencjonowania**, zostanie wyświetlony monit z potwierdzeniem, kliknij przycisk **tak** aby kontynuować.  
   
    ![Komunikat dotyczący zaewidencjonowania](media/automation-source-control-integration/automation_07_CheckinMessage.png)
2. Zaewidencjonuj uruchamiania elementu runbook z kontroli źródła: **MicrosoftAzureAutomationAccountToGitHubV1 synchronizacji**. Ten element runbook łączy się GitHub i wypchnięcia zmian usługi Automatyzacja Azure z Twoim repozytorium. Aby wyświetlić ewidencjonowanych w historii zadania, przejdź wstecz do **integracji kontroli źródła** i kliknij, aby otworzyć stronę synchronizacji repozytorium. Ta strona przedstawia wszystkich zadań kontroli źródła.  Wybierz zadania, aby wyświetlić i kliknij, aby wyświetlić szczegóły.  
   
    ![Zaewidencjonowanie elementu Runbook](media/automation-source-control-integration/automation_08_CheckinRunbook.png)
   
   > [!NOTE]
   > Elementy runbook kontroli źródła są specjalne elementy runbook automatyzacji, który nie można wyświetlić ani edytować. W trakcie ich nie są wyświetlane na liście elementów runbook, widoczne zadania synchronizacji wyświetlane na liście zadań.
   > 
   > 
3. Nazwa zmodyfikowanego elementu runbook jest wysyłany jako parametr wejściowy zaewidencjonowanej elementu runbook. Możesz [wyświetlania szczegółów zadania](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) rozwijając elementu runbook w **synchronizacji repozytorium** strony.  
   
    ![Dane wejściowe zaewidencjonowania](media/automation-source-control-integration/automation_09_CheckinInput.png)
4. Po zakończeniu zadania, aby zobaczyć zmiany, należy odświeżyć repozytorium GitHub.  Powinien być zatwierdzenia w repozytorium z następującym komunikatem zatwierdzania: **zaktualizowane *nazwy elementu Runbook* automatyzacji Azure.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Elementy runbook synchronizacji z kontroli źródła do usługi Automatyzacja Azure
Przycisk synchronizacji na stronie synchronizacji repozytorium służy do pobierania wszystkich elementów runbook w ścieżce folderu elementów runbook Twojego repozytorium na koncie automatyzacji. Tego samego repozytorium można synchronizować na więcej niż jedno konto automatyzacji. Poniżej przedstawiono kroki, aby zsynchronizować elementu runbook:

1. Korzystając z konta automatyzacji skonfigurowanie kontroli źródła, otwórz **synchronizacji integracji/repozytorium kontroli źródła** i kliknij przycisk **synchronizacji**.  Zostanie wyświetlony monit o potwierdzenie, kliknij przycisk **tak** aby kontynuować.  
   
    ![Przycisk synchronizacji](media/automation-source-control-integration/automation_10_SyncButtonwithMessage.png)
2. Synchronizacja uruchomi element runbook: **MicrosoftAzureAutomationAccountFromGitHubV1 synchronizacji**. Ten element runbook łączy się GitHub i pobiera zmiany z repozytorium do automatyzacji Azure. Powinny pojawić się nowe zadanie na **synchronizacji repozytorium** strony dla tej akcji. Aby wyświetlić szczegóły zadania synchronizacji, kliknij, aby otworzyć stronę Szczegóły zadania.  
   
    ![Synchronizacja elementu Runbook](media/automation-source-control-integration/automation_11_SyncRunbook.png)

    > [!NOTE] 
    > Synchronizacja z kontroli źródła zastępuje wersję roboczą elementu runbook, który obecnie istnieje na koncie automatyzacji dla **wszystkie** elementy runbook, które są obecnie w kontroli źródła. Jest równoważne instrukcje wiersza polecenia Git do synchronizacji **ściągnięcia programu git**


## <a name="troubleshooting-source-control-problems"></a>Rozwiązywanie problemów z kontroli źródła
Jeśli występują błędy, ze sprawdzaniem w lub zadanie synchronizacji, powinny być zawieszone, stan zadania i więcej informacji o tym błędzie można wyświetlić na stronie zadań.  **Wszystkie dzienniki** części przedstawiono strumieni środowiska PowerShell skojarzonego z tym zadaniem. Zawiera szczegółowe informacje potrzebne do rozwiązywania wszelkich problemów z wyboru w lub synchronizacji. Ponadto przedstawiono sekwencję akcji, które wystąpiły podczas synchronizowania lub sprawdzanie w elemencie runbook.  

![Obraz AllLogs](media/automation-source-control-integration/automation_13_AllLogs.png)

## <a name="disconnecting-source-control"></a>Rozłączanie kontroli źródła
Aby odłączyć od konto GitHub, otwórz stronę synchronizacji repozytorium, a następnie kliknij przycisk **rozłączenia**. Po rozłączeniu kontroli źródła wciąż na Twoim koncie automatyzacji elementów runbook, które zostały wcześniej zsynchronizowane, ale strony narzędzia do synchronizacji repozytorium nie zostanie włączona.  

  ![Przycisk Rozłącz](media/automation-source-control-integration/automation_12_Disconnect.png)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat integracji kontroli źródła zobacz następujące zasoby:  

* [Automatyzacja Azure: Integracji kontroli źródła w usłudze Automatyzacja Azure](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Głos systemu kontroli źródła ulubionych](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d)  
* [Automatyzacja Azure: Integracja kontroli źródła elementu Runbook za pomocą programu Visual Studio Team Services](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  

