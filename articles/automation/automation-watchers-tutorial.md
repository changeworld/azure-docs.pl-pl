---
title: Tworzenie zadania obserwatora w ramach konta usługi Automatyzacja Azure
description: Dowiedz się, jak utworzyć zadanie obserwatora w ramach konta usługi Automatyzacja Azure, aby obejrzeć dla nowych plików utworzonych w folderze.
services: automation
ms.service: automation
ms.component: process-automation
author: eamonoreilly
ms.author: eamono
ms.topic: conceptual
ms.date: 03/19/2017
ms.openlocfilehash: 0cc215d6643c86460a1d5471aa1eed8fdf18e028
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Utwórz zadania można śledzić zmiany w pliku na komputerze lokalnym obserwatora usługi Automatyzacja Azure

Automatyzacja Azure używa zadania obserwatora, aby wyszukiwać zdarzenia i wyzwalają akcje. Ten samouczek przedstawia tworzenie zadania obserwatora do monitorowania, po dodaniu nowego pliku do katalogu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importowanie elementu runbook obserwatora
> * Utwórz zmienną automatyzacji
> * Utwórz działanie elementu runbook
> * Tworzenie zadania obserwatora
> * Wyzwalacz obserwatora
> * Sprawdź dane wyjściowe

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności przedstawionych w tym samouczku są wymagane następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto automatyzacji](automation-offering-get-started.md) do przechowywania obserwatora i działań elementów runbook i zadania obserwatora.
* A [hybrydowy proces roboczy elementu runbook](automation-hybrid-runbook-worker.md) gdzie uruchomieniu zadania obserwatora.

## <a name="import-a-watcher-runbook"></a>Importowanie elementu runbook obserwatora

W tym samouczku używana obserwatora element runbook o nazwie **NewFile czujki** wyszukuje nowe pliki w katalogu. Element runbook obserwatora pobiera czas ostatniej znanej zapisu do plików w folderze i analizuje wszystkie pliki, nowsze niż tego znaku wodnego. W tym kroku importowania tego elementu runbook na koncie automatyzacji.

1. Otwórz konto automatyzacji, a następnie kliknij polecenie **elementów Runbook** strony.
1. Polecenie **galerii przeglądania** przycisku.
1. Wyszukaj "Obserwatora elementu runbook", wybierz **runbook obserwatora, który wyszukuje nowe pliki w katalogu** i wybierz **importu**.
  ![Importowanego elementu runbook automatyzacji interfejsu użytkownika](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Nadaj elementu runbook, nazwę i opis i wybierz **OK** można zaimportować elementu runbook na koncie automatyzacji.
1. Wybierz **Edytuj** , a następnie kliknij przycisk **publikowania**. Po wyświetleniu monitu wybierz **tak** opublikować elementu runbook.

## <a name="create-an-automation-variable"></a>Utwórz zmienną automatyzacji

[Zmiennej automatyzacji](automation-variables.md) jest używany do przechowywania sygnatury czasowe, który poprzedniego elementu runbook odczytuje i zapisuje dla każdego pliku. 

1. Wybierz **zmienne** w obszarze **UDOSTĘPNIONE zasoby** i wybierz **+ Dodaj zmienną**.
1. Wprowadź nazwę "Watch-NewFileTimestamp"
1. Wybierz typ DateTime.
1. Polecenie **Utwórz** przycisku. Spowoduje to utworzenie zmiennej automatyzacji.

## <a name="create-an-action-runbook"></a>Utwórz działanie elementu runbook

Działania elementu runbook jest używany podczas zadania obserwatora do działania na dane przekazywane do niego z elementu runbook obserwatora. W tym kroku zaktualizować Importuj element runbook wstępnie zdefiniowanej akcji o nazwie "Proces NewFile".

1. Przejdź do swojego konta automatyzacji i wybierz **Runbook** w obszarze **AUTOMATYZACJI procesu** kategorii.
1. Polecenie **galerii przeglądania** przycisku.
1. Wyszukaj "Obserwatora Akcja" i wybierz **akcji obserwatora, który przetwarza zdarzenia wyzwolone przez element runbook obserwatora** i wybierz **importu**.
  ![Importuj działania elementu runbook z interfejsu użytkownika](media/automation-watchers-tutorial/importsourceaction.png)
1. Nadaj elementu runbook, nazwę i opis i wybierz **OK** można zaimportować elementu runbook na koncie automatyzacji.
1. Wybierz **Edytuj** , a następnie kliknij przycisk **publikowania**. Po wyświetleniu monitu wybierz **tak** opublikować elementu runbook.

## <a name="create-a-watcher-task"></a>Tworzenie zadania obserwatora

Zadania obserwatora zawiera dwie części. Obserwator i akcji. Monitor jest uruchamiane w odstępach czasu zdefiniowanych w ramach zadania obserwatora. Dane z elementu runbook obserwatora są przekazywane na działania elementu runbook. W tym kroku skonfigurujesz zadania obserwatora odwołujące się do elementów runbook obserwatora i akcji zdefiniowane w powyższych kroków.

1. Przejdź do swojego konta automatyzacji i wybierz **zadania obserwatora** w obszarze **AUTOMATYZACJI procesu** kategorii.
1. Wybierz stronę zadania obserwatora i kliknij polecenie **+ Dodaj zadanie obserwatora** przycisku.
1. Wprowadź "WatchMyFolder" jako nazwy.

1. Wybierz **obserwatora Konfiguruj** i wybierz **NewFile czujki** elementu runbook.

1. Wprowadź następujące wartości parametrów:

   * **FOLDERPATH** -folderu na hybrydowy proces roboczy, w której tworzone nowe pliki. d:\examplefiles
   * **ROZSZERZENIE** -pozostaw pole puste, aby przetworzyć wszystkie rozszerzenia pliku.
   * **RECURSE** -pozostaw tę wartość jako domyślny.
   * **Uruchom ustawienia** — wybierz hybrydowy proces roboczy.

1. Kliknij przycisk OK, a następnie wybierz, aby powrócić do strony obserwatora.
1. Wybierz **skonfigurowana Akcja** i wybierz element runbook "Proces NewFile".
1. Wpisz następujące wartości parametrów:

   *    **EVENTDATA** -pozostaw to pole puste. Dane są przekazywane w z elementu runbook obserwatora.  
   *    **Ustawienia uruchomienia** -pozostaw Azure podczas działania tego elementu runbook usługi Automatyzacja.

1. Kliknij przycisk **OK**, a następnie wybierz pozycję, aby powrócić do strony obserwatora.
1. Kliknij przycisk **OK** można utworzyć zadania obserwatora.

![Konfigurowanie akcji obserwatora z poziomu interfejsu użytkownika](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Wyzwalacz obserwatora

Aby przetestować obserwatora działa zgodnie z oczekiwaniami, należy utworzyć plik testu.

Zdalne w hybrydowy proces roboczy. Otwórz **PowerShell** i Utwórz plik testu w folderze.
  
   ```PowerShell-interactive
   New-Item -Name ExampleFile1.txt
   ```

Poniższy przykład przedstawia oczekiwane dane wyjściowe.

```
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Sprawdź dane wyjściowe

1. Przejdź do swojego konta automatyzacji i wybierz **zadania obserwatora** w obszarze **AUTOMATYZACJI procesu** kategorii.
1. Wybierz zadanie obserwatora "WatchMyFolder".
1. Polecenie **wyświetlić strumieni obserwatora** w obszarze **strumieni** czy obserwatora znaleziono nowy plik i uruchomić akcję elementu runbook.
1. Aby wyświetlić zadania elementu runbook akcji, kliknij na **wyświetlić obserwatora akcji zadania**. Każde zadanie może być wybrany widok Szczegóły zadania.

   ![Obserwator akcji zadania z poziomu interfejsu użytkownika](media/automation-watchers-tutorial/WatcherActionJobs.png)

Oczekiwane dane wyjściowe, gdy znajduje się nowy plik widać w poniższym przykładzie:

```
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importowanie elementu runbook obserwatora
> * Utwórz zmienną automatyzacji
> * Utwórz działanie elementu runbook
> * Tworzenie zadania obserwatora
> * Wyzwalacz obserwatora
> * Sprawdź dane wyjściowe

Wykonaj to łącze, aby dowiedzieć się więcej na temat tworzenia własnych elementów runbook.

> [!div class="nextstepaction"]
> [Moje pierwszego elementu runbook programu PowerShell](automation-first-runbook-textual-powershell.md).
