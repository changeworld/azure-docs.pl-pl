---
title: Mój pierwszy element Runbook przepływu pracy programu PowerShell w Azure Automation
description: Samouczek, który przeprowadzi Cię przez procesy tworzenia, testowania i publikowania prostego tekstowego elementu Runbook przy użyciu przepływu programu PowerShell.
keywords: przepływ pracy programu powershell, przykłady przepływu pracy programu powershell, program powershell przepływu pracy
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: cdaadcfa3108ca847443e100bc624c2458c34115
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365958"
---
# <a name="my-first-powershell-workflow-runbook"></a>Mój pierwszy element Runbook przepływu pracy programu PowerShell

> [!div class="op_single_selector"]
> * [Element graficzny](automation-first-runbook-graphical.md)
> * [Program PowerShell](automation-first-runbook-textual-powershell.md)
> * [Przepływ pracy programu PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Ten samouczek przeprowadzi Cię przez proces tworzenia [elementu Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) w usłudze Azure Automation. Rozpoczynasz od prostego elementu Runbook, który można testować i publikować wraz z wyjaśnieniem, jak śledzić stan zadania elementu Runbook. Następnie zmodyfikujesz element runbook, aby faktycznie zarządzał zasobami platformy Azure — w tym przypadku uruchamiając maszynę wirtualną platformy Azure. Ostatnim elementem elementu Runbook jest bardziej niezawodny przez dodanie parametrów elementu Runbook.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure.  To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Ta maszyna zostanie zatrzymana i uruchomiona, dlatego nie powinna być produkcyjną maszyną wirtualną.

## <a name="step-1---create-new-runbook"></a>Krok 1. Tworzenie nowego elementu Runbook

Zacznij od utworzenia prostego elementu Runbook, który będzie wyprowadzał tekst *Hello World*.

1. W witrynie Azure Portal otwórz konto usługi Automation.

   Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość z nich to moduły, które są automatycznie uwzględniane na nowym koncie usługi Automation. Powinien istnieć również element zawartości Poświadczenie wymieniony w części dotyczącej [wymagań wstępnych](#prerequisites).

1. Kliknij pozycję **elementy Runbook** w obszarze **Automatyzacja procesów** , aby otworzyć listę elementów Runbook.
1. Utwórz nowy element Runbook, klikając przycisk **+ Dodaj element Runbook** , a następnie **Utwórz nowy element Runbook**.
1. Nadaj elementowi Runbook nazwę *MyFirstRunbook-Workflow*.
1. W tym przypadku utworzysz [element Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) , dlatego wybierz pozycję **przepływ pracy programu PowerShell** dla **typu elementu Runbook**.
1. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor tekstów.

## <a name="step-2---add-code-to-the-runbook"></a>Krok 2. Dodawanie kodu do elementu Runbook

Możesz wpisać kod bezpośrednio w elemencie Runbook lub wybrać polecenia cmdlet, elementy Runbook i elementy zawartości, używając kontrolki Biblioteka, a następnie dodać je do elementu Runbook z powiązanymi parametrami. W tym instruktażu wpiszesz bezpośrednio w elemencie Runbook.

1. Element Runbook jest obecnie pusty tylko za pomocą wymaganego słowa kluczowego *przepływu pracy* , nazwy elementu Runbook i nawiasów klamrowych, które encases cały przepływ pracy.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Wpisz ciąg *Write-Output „Witaj, świecie”* między nawiasami klamrowymi.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Zapisz element Runbook, klikając przycisk **Zapisz**.

## <a name="step-3---test-the-runbook"></a>Krok 3. Testowanie elementu Runbook

Przed opublikowaniem elementu runbook w celu udostępnienia go w środowisku produkcyjnym warto go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementu Runbook polega na uruchomieniu jego **wersji roboczej** i interaktywnym przejrzeniu danych wyjściowych.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć okienko testowania.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Ta opcja powinna być jedyną włączoną opcją.
1. Zostanie utworzone [zadanie elementu Runbook](automation-runbook-execution.md) i pojawi się jego stan.

   Stan zadania zostanie uruchomiony jako *umieszczony w kolejce* , wskazując, że oczekuje na udostępnienie procesu roboczego elementu Runbook w chmurze. Przenosi się do *początku* , gdy proces roboczy pozyskuje zadanie, a następnie *uruchamia* się, gdy element Runbook faktycznie zacznie działać.

1. Po zakończeniu zadania elementu Runbook zostaną wyświetlone jego dane wyjściowe. W Twoim przypadku powinna zostać wyświetlona *Hello World*.

   ![Witaj, świecie](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4. Publikowanie i uruchamianie elementu Runbook

Nowo utworzony element runbook nadal działa w trybie roboczym. Należy opublikować ją przed uruchomieniem jej w środowisku produkcyjnym. Podczas publikowania elementu Runbook można zastąpić istniejącą wersję opublikowaną wersją roboczą. W Twoim przypadku nie masz jeszcze opublikowanej wersji, ponieważ element Runbook został właśnie utworzony.

1. Kliknij przycisk **Opublikuj**, aby opublikować element Runbook, a następnie kliknij opcję **Tak** po wyświetleniu monitu.
1. Jeśli przewiniesz w lewo, aby wyświetlić element Runbook w okienku **elementy Runbook** , zostanie wyświetlony **Stan tworzenia** **opublikowanych**.
1. Przewiń w prawo, aby wyświetlić okienko elementu **MyFirstRunbook-Workflow**.
   Opcje w górnej części umożliwiają nam uruchamianie elementu Runbook, planowanie jego uruchomienia w przyszłości lub utworzenie [elementu webhook](automation-webhooks.md) w celu umożliwienia uruchamiania za pośrednictwem wywołania HTTP.
1. po prostu chcesz uruchomić element Runbook, a następnie kliknij przycisk **Start** , a następnie pozycję **tak** po wyświetleniu monitu.

   ![Uruchamianie elementu Runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Zostanie otwarte okienko zadań dla utworzonego zadania elementu Runbook. Można zamknąć to okienko, ale w tym przypadku pozostawić otwarte, aby można było obserwować postęp zadania.
1. Stan zadania jest pokazywany w **podsumowaniu zadania** i jest zgodny z Stanami wyświetlonymi podczas testowania elementu Runbook.

   ![Podsumowanie zadania](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Gdy stanem elementu Runbook będzie *Ukończono*, kliknij pozycję **Dane wyjściowe**. Zostanie otwarte okienko danych wyjściowych i będzie można zobaczyć *Hello World*.

   ![Podsumowanie zadania](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Zamknij okienko danych wyjściowych.
1. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. w strumieniu danych wyjściowych powinien być widoczny tylko *Hello World* , ale w tym widoku można wyświetlić inne strumienie zadania elementu Runbook, takie jak pełne informacje i błąd, jeśli element Runbook zapisuje do nich.

   ![Podsumowanie zadania](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Zamknij stronę strumienie i stronę zadania, aby powrócić do strony MyFirstRunbook.
1. Kliknij pozycję **zadania** , aby otworzyć stronę zadania dla tego elementu Runbook. Ta strona zawiera listę wszystkich zadań utworzonych przez ten element Runbook. tylko jedno zadanie jest widoczne na liście, ponieważ uruchomiono zadanie tylko raz.

   ![Zadania](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Możesz kliknąć to zadanie, aby otworzyć stronę zadania, która była wyświetlana podczas uruchamiania elementu Runbook. Ta akcja pozwala wrócić w czasie i wyświetlić szczegóły każdego zadania, które zostało utworzone dla określonego elementu Runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Krok 5. Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure

Element runbook został przetestowany i opublikowany, ale jak do tej pory nie wykonuje on żadnych użytecznych czynności. Powinien zarządzać zasobami platformy Azure. Nie można tego zrobić, jeśli nie masz uwierzytelnienia przy użyciu poświadczeń, które są określone w [wymaganiach wstępnych](#prerequisites). Można to zrobić za pomocą polecenia cmdlet **Connect-AzAccount** .

1. Otwórz edytor tekstów, klikając pozycję **Edytuj** w okienku MyFirstRunbook-Workflow.
2. nie jest już potrzebny wiersz **Write-Output** , dlatego należy go usunąć.
3. Umieść kursor w pustym wierszu między nawiasami klamrowymi.
4. Wpisz lub skopiuj i wklej poniższy kod, który będzie obsługiwać uwierzytelnianie za pomocą konta Uruchom jako w usłudze Automation:

   ```powershell-interactive
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

   > [!IMPORTANT]
   > Do programu **Connect-AzAccount**są teraz aliasy **Add-AzAccount** i **login-AzAccount** . Jeśli polecenie cmdlet **Connect-AzAccount** nie istnieje, możesz użyć polecenia **Add-AzAccount** lub **login-AzAccount**lub [zaktualizować moduły](automation-update-azure-modules.md) na koncie usługi Automation do najnowszych wersji.

> [!NOTE]
> Może być konieczne [zaktualizowanie modułów](automation-update-azure-modules.md) , nawet jeśli utworzono nowe konto usługi Automation.

1. Kliknij pozycję **okienko testowania** , aby umożliwić przetestowanie elementu Runbook.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu powinny pojawić się dane wyjściowe podobne do poniższych, zawierające podstawowe informacje powiązane z kontem. Ta akcja potwierdza, że poświadczenie jest prawidłowe.

   ![Uwierzytelnianie](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Krok 6. Dodawanie kodu w celu uruchomienia maszyny wirtualnej

Teraz, gdy element Runbook jest uwierzytelniany w ramach subskrypcji platformy Azure, możesz zarządzać zasobami. Dodaj polecenie, aby uruchomić maszynę wirtualną. Możesz wybrać dowolną maszynę wirtualną w ramach subskrypcji platformy Azure, a teraz zakodowana tę nazwę w elemencie Runbook. Jeśli zarządzasz zasobami w wielu subskrypcjach, musisz użyć parametru **-AzContext** wraz z poleceniem [Get-AzContext](/powershell/module/az.accounts/get-azcontext).

1. Po *nawiązaniu połączenia AzAccount*wpisz polecenie *Start-AzVM-Name "VMName"-ResourceGroupName "NameofResourceGroup"* , podając nazwę i grupę zasobów maszyny wirtualnej, która ma zostać uruchomiona.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Zapisz element Runbook, a następnie kliknij pozycję **okienko testowania** , aby można było go przetestować.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu sprawdź, czy maszyna wirtualna została uruchomiona.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Krok 7. Dodawanie parametru wejściowego do elementu Runbook

element Runbook aktualnie uruchamia maszynę wirtualną, która została stałe w elemencie Runbook, ale będzie bardziej przydatna, jeśli można określić maszynę wirtualną, gdy element Runbook zostanie uruchomiony. Dodaj parametry wejściowe do elementu Runbook, aby zapewnić tę funkcjonalność.

1. Dodaj parametry dla *VMName* i *ResourceGroupName* do elementu Runbook, a następnie użyj tych zmiennych za pomocą polecenia cmdlet **Start-AzVM** , jak w poniższym przykładzie.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Zapisz element Runbook, a następnie otwórz okienko testowania. Teraz możesz podać wartości dwóch zmiennych wejściowych, które są w teście.
3. Zamknij okienko testowania.
4. Kliknij pozycję **Opublikuj**, aby opublikować nową wersję elementu Runbook.
5. Zatrzymaj maszynę wirtualną uruchomioną w poprzednim kroku.
6. Kliknij pozycję **Uruchom**, aby uruchomić element Runbook. Wpisz wartości parametrów **VMName** i **ResourceGroupName** dla maszyny wirtualnej do uruchomienia.

   ![Uruchamianie elementu Runbook](media/automation-first-runbook-textual/automation-pass-params.png)

7. Po zakończeniu działania elementu Runbook sprawdź, czy maszyna wirtualna została uruchomiona.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat programu PowerShell, w tym modułów dokumentacji i uczenia dotyczącej języka, zapoznaj się z dokumentacją programu [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz artykuł [My first PowerShell runbook](automation-first-runbook-textual-powershell.md) (Mój pierwszy element Runbook programu PowerShell).
* Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz artykuł [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
* Aby uzyskać więcej informacji o funkcji obsługi skryptów programu PowerShell, zobacz artykuł [Native PowerShell script support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Obsługa natywnych skryptów programu PowerShell w usłudze Azure Automation).
