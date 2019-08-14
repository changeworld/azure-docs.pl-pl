---
title: Tworzenie zadania obserwatora na koncie Azure Automation
description: Dowiedz się, jak utworzyć zadanie obserwatora na koncie Azure Automation, aby obejrzeć nowe pliki utworzone w folderze.
services: automation
ms.service: automation
ms.subservice: process-automation
author: eamonoreilly
ms.author: eamono
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 75341fa2df6972dbf05542577d56ab35315919e6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989249"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Tworzenie zadań obserwatora Azure Automation w celu śledzenia zmian plików na komputerze lokalnym

Azure Automation używa zadań obserwatora do oglądania zdarzeń i wyzwalania akcji za pomocą elementów Runbook programu PowerShell. Ten samouczek przeprowadzi Cię przez proces tworzenia zadania obserwatora do monitorowania po dodaniu nowego pliku do katalogu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importuj element Runbook obserwatora
> * Tworzenie zmiennej automatyzacji
> * Tworzenie elementu Runbook akcji
> * Tworzenie zadania obserwatora
> * Wyzwalanie obserwatora
> * Sprawdzanie danych wyjściowych

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności przedstawionych w tym samouczku są wymagane następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do przechowywania obserwatora i elementów Runbook akcji oraz zadania obserwatora.
* [Hybrydowy proces roboczy elementu Runbook](automation-hybrid-runbook-worker.md) , w którym jest uruchamiane zadanie obserwatora.

> [!NOTE]
> Zadania obserwatora nie są obsługiwane w Chinach platformy Azure.

## <a name="import-a-watcher-runbook"></a>Importuj element Runbook obserwatora

Ten samouczek używa elementu Runbook obserwatora o nazwie **Watch-NewFile** , aby wyszukać nowe pliki w katalogu. Element Runbook obserwatora Pobiera ostatni znany czas zapisu do plików w folderze i szuka plików nowszych niż ten znak wodny.

Ten proces importowania można wykonać za pomocą [Galeria programu PowerShell](https://www.powershellgallery.com).

1. Przejdź do strony Galeria dla [Watch-NewFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Na karcie **Azure Automation** kliknij pozycję **wdróż, aby Azure Automation**.

Możesz również zaimportować ten element Runbook do konta usługi Automation z portalu, wykonując poniższe kroki.

1. Otwórz konto usługi Automation, a następnie kliknij stronę **elementy Runbook** .
2. Kliknij przycisk **Przeglądaj Galerię** .
3. Wyszukaj frazę "element Runbook obserwatora", wybierz **element Runbook obserwatora, który wyszukuje nowe pliki w katalogu** , a następnie wybierz pozycję **Importuj**.
  ![Importuj element Runbook usługi Automation z interfejsu użytkownika](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Nadaj elementowi Runbook nazwę i opis, a następnie wybierz pozycję **OK** , aby zaimportować element Runbook do konta usługi Automation.
1. Wybierz pozycję **Edytuj** , a następnie kliknij pozycję **Publikuj**. Po wyświetleniu monitu wybierz pozycję **tak** , aby opublikować element Runbook.

## <a name="create-an-automation-variable"></a>Tworzenie zmiennej automatyzacji

[Zmienna automatyzacji](automation-variables.md) służy do przechowywania sygnatur czasowych, które poprzedni element Runbook odczytuje i zapisuje z każdego pliku.

1. Wybierz **zmienne** w obszarze **zasoby udostępnione** i wybierz pozycję **+ Dodaj zmienną**.
1. Wprowadź wartość "Watch-NewFileTimestamp" dla nazwy
1. Wybierz pozycję DateTime dla typu.
1. Kliknij przycisk **Utwórz** . Spowoduje to utworzenie zmiennej automatyzacji.

## <a name="create-an-action-runbook"></a>Tworzenie elementu Runbook akcji

Element Runbook akcji jest używany w zadaniu obserwatora do działania na danych przesłanych do niego z elementu Runbook obserwatora. Elementy Runbook przepływu pracy programu PowerShell nie są obsługiwane przez zadania obserwatora, należy użyć elementów Runbook programu PowerShell. Należy zaimportować wstępnie zdefiniowany element Runbook Action o nazwie **Process-NewFile**.

Ten proces importowania można wykonać za pomocą [Galeria programu PowerShell](https://www.powershellgallery.com).

1. Przejdź do strony Galeria dla [Process-NewFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Na karcie **Azure Automation** kliknij pozycję **wdróż, aby Azure Automation**.

Możesz również zaimportować ten element Runbook do konta usługi Automation z portalu, wykonując poniższe kroki.

1. Przejdź do konta usługi Automation i wybierz pozycję **elementy Runbook** w kategorii **Automatyzacja procesu** .
1. Kliknij przycisk **Przeglądaj Galerię** .
1. Wyszukaj frazę "Akcja obserwatora" i wybierz **akcję obserwatora, która przetwarza zdarzenia wyzwalane przez element Runbook obserwatora** , a następnie wybierz pozycję **Importuj**.
  ![Importuj element Runbook akcji z interfejsu użytkownika](media/automation-watchers-tutorial/importsourceaction.png)
1. Nadaj elementowi Runbook nazwę i opis, a następnie wybierz pozycję **OK** , aby zaimportować element Runbook do konta usługi Automation.
1. Wybierz pozycję **Edytuj** , a następnie kliknij pozycję **Publikuj**. Po wyświetleniu monitu wybierz pozycję **tak** , aby opublikować element Runbook.

## <a name="create-a-watcher-task"></a>Tworzenie zadania obserwatora

Zadanie obserwatora zawiera dwie części. Obserwator i akcja. Obserwator działa w interwale zdefiniowanym w zadaniu obserwatora. Dane z elementu Runbook obserwatora są przesyłane do elementu Runbook akcji. W tym kroku należy skonfigurować zadanie obserwatora odwołujące się do obserwatora i elementów Runbook akcji zdefiniowanych w powyższych krokach.

1. Przejdź do konta usługi Automation i wybierz pozycję **zadania obserwatora** w kategorii **Automatyzacja procesu** .
1. Wybierz stronę zadania obserwatora i kliknij pozycję **+ Dodaj przycisk zadania obserwatora** .
1. Wprowadź wartość "WatchMyFolder" jako nazwę.

1. Wybierz pozycję **Konfiguruj obserwatora** i wybierz element Runbook **Watch-NewFile** .

1. Wprowadź następujące wartości parametrów:

   * **FOLDERPATH** — folder w hybrydowym procesie roboczym, w którym zostaną utworzone nowe pliki. d:\examplefiles
   * **Rozszerzenie** — pozostaw puste, aby przetworzyć wszystkie rozszerzenia plików.
   * **Powtórz** — pozostaw tę wartość jako domyślną.
   * **Parametry uruchomieniowe** — Wybieranie hybrydowego procesu roboczego.

1. Kliknij przycisk OK, a następnie wybierz opcję powrotu do strony obserwatora.
1. Wybierz pozycję **Konfiguruj akcję** i wybierz element Runbook "Process-NewFile".
1. Wprowadź następujące wartości parametrów:

   * **EVENTDATA** — pozostaw puste. Dane są przesyłane z elementu Runbook obserwatora.
   * **Parametry uruchomieniowe** — pozostaw jako platformę Azure, ponieważ ten element Runbook jest uruchamiany w usłudze Automation.

1. Kliknij przycisk **OK**, a następnie wybierz opcję powrotu do strony obserwatora.
1. Kliknij przycisk **OK** , aby utworzyć zadanie obserwatora.

![Skonfiguruj akcję obserwatora z poziomu interfejsu użytkownika](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Wyzwalanie obserwatora

Aby przetestować obserwatora działa zgodnie z oczekiwaniami, należy utworzyć plik testowy.

Zdalna w hybrydowym procesie roboczym. Otwórz program **PowerShell** i Utwórz plik testowy w folderze.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

W poniższym przykładzie pokazano oczekiwane dane wyjściowe.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Sprawdzanie danych wyjściowych

1. Przejdź do konta usługi Automation i wybierz pozycję **zadania obserwatora** w kategorii **Automatyzacja procesu** .
1. Wybierz zadanie obserwatora "WatchMyFolder".
1. Kliknij pozycję **Wyświetl strumienie obserwatora** w obszarze **strumienie** , aby zobaczyć, że obserwator znalazł nowy plik i uruchomił element Runbook akcji.
1. Aby wyświetlić zadania akcji elementu Runbook, kliknij przycisk **Wyświetl zadania akcji obserwatora**. Każde zadanie można wybrać, aby wyświetlić szczegółowe informacje o zadaniu.

   ![Zadania akcji obserwatora z interfejsu użytkownika](media/automation-watchers-tutorial/WatcherActionJobs.png)

Oczekiwane dane wyjściowe po znalezieniu nowego pliku można zobaczyć w następującym przykładzie:

```output
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importuj element Runbook obserwatora
> * Tworzenie zmiennej automatyzacji
> * Tworzenie elementu Runbook akcji
> * Tworzenie zadania obserwatora
> * Wyzwalanie obserwatora
> * Sprawdzanie danych wyjściowych

Skorzystaj z tego linku, aby dowiedzieć się więcej na temat tworzenia własnego elementu Runbook.

> [!div class="nextstepaction"]
> [Mój pierwszy element Runbook programu PowerShell](automation-first-runbook-textual-powershell.md).

