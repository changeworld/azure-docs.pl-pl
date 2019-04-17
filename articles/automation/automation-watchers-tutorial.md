---
title: Tworzenie zadania obserwatora w ramach konta usługi Azure Automation
description: Dowiedz się, jak utworzyć zadanie obserwatora w ramach konta usługi Azure Automation, aby zobaczyć, czy nowe pliki utworzone w folderze.
services: automation
ms.service: automation
ms.subservice: process-automation
author: eamonoreilly
ms.author: eamono
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: bee414ada61e2cfcf7609b02ef1da7323a0fe0e3
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2019
ms.locfileid: "59606926"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Tworzenie zadania do śledzenia zmian w plikach na komputerze lokalnym obserwatora usługi Azure Automation

Usługa Azure Automation używa zadania obserwatora obserwowanie zdarzeń i wyzwalanie akcji za pomocą elementów runbook programu PowerShell. Ten samouczek przeprowadzi Cię przez tworzenie zadania obserwatora w celu monitorowania, gdy nowy plik zostanie dodany do katalogu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importowanie elementu runbook obserwatora
> * Tworzenie zmiennej automatyzacji
> * Tworzenie elementu runbook akcji
> * Tworzenie zadania obserwatora
> * Wyzwalacz obserwatora
> * Sprawdzanie danych wyjściowych

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności przedstawionych w tym samouczku są wymagane następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do przechowywania obserwatora i elementów runbook akcji oraz zadania obserwatora.
* A [hybrydowego procesu roboczego runbook](automation-hybrid-runbook-worker.md) którym działa zadanie obserwatora.

> [!NOTE]
> Zadania obserwatora nie są obsługiwane w chińskiej wersji platformy Azure.

## <a name="import-a-watcher-runbook"></a>Importowanie elementu runbook obserwatora

W tym samouczku korzysta z elementu runbook obserwatora o nazwie **NewFile Obejrzyj** aby wyszukać nowe pliki w katalogu. Runbook obserwatora pobiera podczas ostatniego znanego zapisu do plików w folderze i analizuje wszystkie pliki, które są nowsze niż tego limitu. W tym kroku możesz zaimportować ten element runbook do konta usługi automation.

1. Otwórz konto usługi Automation, a następnie kliknij **elementów Runbook** strony.
2. Kliknij pozycję **Przeglądaj galerię** przycisku.
3. Wyszukaj "Runbook obserwatora" Wybierz **elementu runbook obserwatora, który wyszukuje nowe pliki w katalogu** i wybierz **importu**.
  ![Importowanie elementu runbook usługi automation z poziomu interfejsu użytkownika](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Nadaj elementowi runbook nazwę i opis oraz wybierz **OK** Aby zaimportować element runbook do konta usługi Automation.
1. Wybierz **Edytuj** a następnie kliknij przycisk **Publikuj**. Po wyświetleniu monitu wybierz **tak** opublikować elementu runbook.

## <a name="create-an-automation-variable"></a>Tworzenie zmiennej automatyzacji

[Zmienna usługi automation](automation-variables.md) służy do przechowywania sygnatury czasowe, który poprzedniego elementu runbook odczytuje i zapisuje z każdego pliku.

1. Wybierz **zmienne** w obszarze **zasoby UDOSTĘPNIONE** i wybierz **+ Dodaj zmienną**.
1. Wprowadź nazwę "Watch-NewFileTimestamp"
1. Wybierz typ daty/godziny.
1. Kliknij pozycję **Utwórz** przycisku. Spowoduje to utworzenie zmiennej automatyzacji.

## <a name="create-an-action-runbook"></a>Tworzenie elementu runbook akcji

Element runbook akcji jest używany w ramach zadania obserwatora zajmującym się dane przekazane do niej z poziomu elementu runbook obserwatora. Elementy runbook przepływu pracy programu PowerShell nie są obsługiwane przez zadania obserwatora, należy użyć elementy runbook programu PowerShell. W tym kroku należy zaktualizować importu predefiniowanych akcji elementu runbook o nazwie "Proces NewFile".

1. Przejdź do swojego konta usługi automation, a następnie wybierz pozycję **elementów Runbook** w obszarze **AUTOMATYZACJI procesów** kategorii.
1. Kliknij pozycję **Przeglądaj galerię** przycisku.
1. Wyszukaj frazę "Akcji obserwatora" i wybierz **akcji obserwatora, który przetwarza zdarzenia wyzwolone przez element runbook obserwatora** i wybierz **importu**.
  ![Importowanie elementu runbook akcji z poziomu interfejsu użytkownika](media/automation-watchers-tutorial/importsourceaction.png)
1. Nadaj elementowi runbook nazwę i opis oraz wybierz **OK** Aby zaimportować element runbook do konta usługi Automation.
1. Wybierz **Edytuj** a następnie kliknij przycisk **Publikuj**. Po wyświetleniu monitu wybierz **tak** opublikować elementu runbook.

## <a name="create-a-watcher-task"></a>Tworzenie zadania obserwatora

Zadanie obserwatora zawiera dwie części. Obserwator i akcji. Obserwator jest uruchamiane w odstępach czasu zdefiniowanych w ramach zadania obserwatora. Dane z elementu runbook obserwatora są przekazywane do elementu runbook akcji. W tym kroku skonfigurujesz odwołujące się do elementów runbook obserwatora i akcji zdefiniowanych w poprzednich krokach zadania obserwatora.

1. Przejdź do swojego konta usługi automation, a następnie wybierz pozycję **zadania obserwatora** w obszarze **AUTOMATYZACJI procesów** kategorii.
1. Wybierz stronę zadania obserwatora, a następnie kliknij pozycję **+ Dodaj zadanie obserwatora** przycisku.
1. Wprowadź "WatchMyFolder" jako nazwę.

1. Wybierz **Konfiguruj obserwatora** i wybierz **NewFile Obejrzyj** elementu runbook.

1. Wprowadź następujące wartości parametrów:

   * **FOLDERPATH** -folder w hybrydowym procesie roboczym, gdy utworzone nowe pliki. d:\examplefiles
   * **ROZSZERZENIE** — pozostaw puste, aby przetwarzać wszystkie rozszerzenia pliku.
   * **RECURSE** — pozostaw tę wartość jako domyślny.
   * **Parametry URUCHOMIENIOWE** — wybierz hybrydowy proces roboczy.

1. Kliknij przycisk OK, a następnie wybierz pozycję Tak, aby wrócić do strony obserwatora.
1. Wybierz **Skonfiguruj akcję** i wybierz element runbook "Proces NewFile".
1. Wprowadź następujące wartości parametrów:

   * **EVENTDATA** — pozostaw to pole puste. Dane są przekazywane w z elementu runbook obserwatora.  
   * **Parametry uruchomieniowe** — pozostaw Azure zgodnie z tego elementu runbook jest uruchamiany w usłudze Automation.

1. Kliknij przycisk **OK**, a następnie wybierz pozycję Tak, aby wrócić do strony obserwatora.
1. Kliknij przycisk **OK** do tworzenia zadania obserwatora.

![Konfigurowanie akcji obserwatora z poziomu interfejsu użytkownika](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Wyzwalacz obserwatora

Aby przetestować obserwatora działa zgodnie z oczekiwaniami, należy utworzyć plik testowy.

Nawiąż połączenie zdalne hybrydowy proces roboczy. Otwórz **PowerShell** i Utwórz plik testowy w folderze.
  
```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

Poniższy przykład pokazuje oczekiwanych danych wyjściowych.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Sprawdzanie danych wyjściowych

1. Przejdź do swojego konta usługi automation, a następnie wybierz pozycję **zadania obserwatora** w obszarze **AUTOMATYZACJI procesów** kategorii.
1. Wybierz zadania obserwatora "WatchMyFolder".
1. Kliknij pozycję **Wyświetl strumienie obserwatora** w obszarze **strumieni** czy obserwatora znaleziono nowy plik i że uruchomieniu elementu runbook akcji.
1. Aby wyświetlić zadania elementów runbook akcji, kliknij **Wyświetl zadania akcji obserwatora**. Każde zadanie może zostać wybrany widok szczegółów zadania.

   ![Zadania akcji obserwatora z poziomu interfejsu użytkownika](media/automation-watchers-tutorial/WatcherActionJobs.png)

Oczekiwane dane wyjściowe, gdy nowy plik zostanie znaleziony, są widoczne w następującym przykładzie:

```output
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importowanie elementu runbook obserwatora
> * Tworzenie zmiennej automatyzacji
> * Tworzenie elementu runbook akcji
> * Tworzenie zadania obserwatora
> * Wyzwalacz obserwatora
> * Sprawdzanie danych wyjściowych

Kliknij ten link, aby dowiedzieć się więcej na temat tworzenia własnego elementu runbook.

> [!div class="nextstepaction"]
> [Mój pierwszy element runbook programu PowerShell](automation-first-runbook-textual-powershell.md).

