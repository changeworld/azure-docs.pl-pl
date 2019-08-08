---
title: Mój pierwszy element Runbook programu PowerShell w Azure Automation
description: W tym samouczku przedstawiono procesy tworzenia, testowania i publikowania prostego elementu Runbook programu PowerShell.
keywords: azure powershell, samouczek skryptu programu powershell, automatyzacja programu powershell
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 11/27/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9718185b41795da6d95486972441ee20bc250316
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850703"
---
# <a name="my-first-powershell-runbook"></a>Mój pierwszy element Runbook programu PowerShell

> [!div class="op_single_selector"]
> * [Element graficzny](automation-first-runbook-graphical.md)
> * [Program PowerShell](automation-first-runbook-textual-powershell.md)
> * [Przepływ pracy programu PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

W tym samouczku przedstawiono proces tworzenia [elementu Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) w usłudze Azure Automation. Rozpoczynasz od prostego elementu Runbook, który można testować i publikować podczas nauczenia się, jak śledzić stan zadania elementu Runbook. Następnie zmodyfikujesz element runbook, aby faktycznie zarządzał zasobami platformy Azure — w tym przypadku uruchamiając maszynę wirtualną platformy Azure. W końcu element Runbook jest bardziej niezawodny przez dodanie parametrów elementu Runbook.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-quickstart-create-account.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Ta maszyna zostanie zatrzymana i uruchomiona, dlatego nie powinna być produkcyjną maszyną wirtualną.
* Może być konieczne [zaktualizowanie modułów platformy Azure](automation-update-azure-modules.md) na podstawie używanych przez Ciebie poleceń cmdlet.

## <a name="create-new-runbook"></a>Utwórz nowy element Runbook

Zacznij od utworzenia prostego elementu Runbook, który będzie wyprowadzał tekst *Hello World*.

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Kliknij pozycję **elementy Runbook** w obszarze **Automatyzacja procesów** , aby otworzyć listę elementów Runbook.
3. Utwórz nowy element Runbook, klikając przycisk **+ Dodaj element Runbook** , a następnie **Utwórz nowy element Runbook**.
4. Nadaj elementowi Runbook nazwę *MyFirstRunbook-PowerShell*.
5. W takim przypadku należy utworzyć [element Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) , dlatego wybierz opcję **PowerShell** dla **typu elementu Runbook**.
6. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor tekstów.

## <a name="add-code-to-the-runbook"></a>Dodawanie kodu do elementu Runbook

Możesz wpisać kod bezpośrednio w elemencie Runbook lub wybrać polecenia cmdlet, elementy Runbook i elementy zawartości, używając kontrolki Biblioteka, a następnie dodać je do elementu Runbook z powiązanymi parametrami. W tym instruktażu wpiszesz bezpośrednio w elemencie Runbook.

1. Element Runbook jest obecnie pusty, wpisz *Write-output "Hello World".* w treści skryptu.

   ![Witaj, świecie](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. Zapisz element Runbook, klikając przycisk **Zapisz**.

## <a name="step-3---test-the-runbook"></a> Testowanie elementu Runbook

Przed opublikowaniem elementu runbook w celu udostępnienia go w środowisku produkcyjnym warto go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementu Runbook polega na uruchomieniu jego **wersji roboczej** i interaktywnym przejrzeniu danych wyjściowych.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć okienko testowania.
2. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.
3. Zostanie utworzone [zadanie elementu Runbook](automation-runbook-execution.md) i pojawi się jego stan.

   Zadanie jest uruchamiane jako *kolejkowane* , wskazując, że oczekuje na dostępność procesu roboczego elementu Runbook w chmurze. Przenosi się do *początku* , gdy proces roboczy pozyskuje zadanie, a następnie *uruchamia* się, gdy element Runbook faktycznie zacznie działać.

4. Po zakończeniu zadania elementu Runbook zostaną wyświetlone jego dane wyjściowe. W Twoim przypadku powinna zostać wyświetlona *Hello World*.

   ![Dane wyjściowe w okienku testowania](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="publish-and-start-the-runbook"></a>Publikowanie i Uruchamianie elementu Runbook

Nowo utworzony element runbook nadal działa w trybie roboczym. Należy ją opublikować, aby można było uruchomić ją w środowisku produkcyjnym.  Podczas publikowania elementu Runbook można zastąpić istniejącą wersję opublikowaną wersją roboczą. W Twoim przypadku nie masz jeszcze opublikowanej wersji, ponieważ element Runbook został właśnie utworzony.

1. Kliknij pozycję **Opublikuj**, aby opublikować element Runbook, a następnie kliknij pozycję **Tak** po wyświetleniu monitu.
1. Jeśli przewiniesz w lewo, aby wyświetlić element Runbook w okienku **elementy Runbook** , zostanie wyświetlony **Stan tworzenia** **opublikowanych**.
1. Przewiń w prawo, aby wyświetlić okienko elementu **MyFirstRunbook-PowerShell**.
   Opcje w górnej części umożliwiają uruchamianie elementu Runbook, wyświetlanie elementu Runbook, planowanie jego uruchomienia w przyszłości lub utworzenie [elementu webhook](automation-webhooks.md) w celu umożliwienia uruchamiania za pośrednictwem wywołania HTTP.
1. Chcesz uruchomić element Runbook, a następnie kliknij przycisk **Start** , a następnie kliknij przycisk **OK** , gdy zostanie otwarta strona Uruchamianie elementu Runbook.
1. Zostanie otwarta strona zadania dla utworzonego zadania elementu Runbook. Można zamknąć to okienko, ale w tym przypadku pozostawić otwarte, aby można było obserwować postęp zadania.
1. Stan zadania jest pokazywany w **podsumowaniu zadania** i jest zgodny z Stanami wyświetlonymi podczas testowania elementu Runbook.

   ![Podsumowanie zadania](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Po *zakończeniu*wyświetlania stanu elementu Runbook w obszarze **Przegląd** kliknij pozycję **dane wyjściowe**. Zostanie otwarte okienko danych wyjściowych i będzie można zobaczyć *Hello World*.

   ![Dane wyjściowe zadania](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Zamknij stronę wyjściową.
1. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. W strumieniu danych wyjściowych powinien być widoczny tylko *Hello World* , ale dane wyjściowe mogą zawierać inne strumienie zadania elementu Runbook, takie jak pełne informacje i błąd, jeśli element Runbook wykonuje w nich operacje zapisu.

   ![Wszystkie dzienniki](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Zamknij stronę strumienie i stronę zadania, aby powrócić do strony MyFirstRunbook-PowerShell.
1. W obszarze **szczegóły**kliknij pozycję **zadania** , aby otworzyć okienko zadania dla tego elementu Runbook. Ta strona zawiera listę wszystkich zadań utworzonych przez ten element Runbook. Ponieważ uruchomiono zadanie tylko raz, powinno być widoczne tylko jedno zadanie.

   ![Lista zadań](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Możesz kliknąć to zadanie, aby otworzyć okienko Zadania wyświetlone wcześniej po uruchomieniu elementu runbook. Ta akcja pozwala wrócić w czasie i wyświetlić szczegóły każdego zadania, które zostało utworzone dla określonego elementu Runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure

Element runbook został przetestowany i opublikowany, ale jak do tej pory nie wykonuje on żadnych użytecznych czynności. Powinien zarządzać zasobami platformy Azure. Nie można tego zrobić, jeśli nie jest on uwierzytelniany przy użyciu połączenia Uruchom jako, które jest tworzone automatycznie podczas tworzenia konta usługi Automation. Użyj połączenia Uruchom jako z poleceniem cmdlet **Connect-AzureRmAccount** . Jeśli zarządzasz zasobami w wielu subskrypcjach, musisz użyć parametru **-AzureRmContext** wraz z poleceniem [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. Otwórz Edytor tekstów, klikając pozycję **Edytuj** na stronie MyFirstRunbook-PowerShell.
1. Nie jest już potrzebny wiersz **Write-Output** , dlatego należy go usunąć.
1. Wpisz lub skopiuj i wklej poniższy kod, który obsługuje uwierzytelnianie za pomocą konta Uruchom jako w usłudze Automation:

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   ```

   > [!IMPORTANT]
   > Do programu **Connect-AzureRmAccount**są teraz aliasy **Add-AzureRmAccount** i **login-AzureRmAccount** . Jeśli polecenie cmdlet **Connect-AzureRMAccount** nie istnieje, możesz użyć polecenia **Add-AzureRMAccount** lub **login-AzureRMAccount**lub zaktualizować moduły na koncie usługi Automation do najnowszych wersji.

1. Kliknij pozycję **okienko testowania** , aby umożliwić przetestowanie elementu Runbook.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu powinny pojawić się dane wyjściowe podobne do poniższych, zawierające podstawowe informacje powiązane z kontem. Dane wyjściowe potwierdzają, że konto Uruchom jako jest prawidłowe.

   ![Uwierzytelnij](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="add-code-to-start-a-virtual-machine"></a>Dodawanie kodu w celu uruchomienia maszyny wirtualnej

Teraz, gdy element Runbook jest uwierzytelniany w ramach subskrypcji platformy Azure, możesz zarządzać zasobami. Dodaj polecenie, aby uruchomić maszynę wirtualną. Możesz wybrać dowolną maszynę wirtualną w ramach subskrypcji platformy Azure, a teraz umieszczaj tę nazwę w elemencie Runbook.

1. Po *nawiązaniu połączenia AzureRmAccount*wpisz polecenie *Start-AzureRmVM-Name "VMName"-ResourceGroupName "NameofResourceGroup"* , podając nazwę i grupę zasobów maszyny wirtualnej, która ma zostać uruchomiona.

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Zapisz element Runbook, a następnie kliknij pozycję **okienko testowania** , aby można było go przetestować.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu sprawdź, czy maszyna wirtualna została uruchomiona.

## <a name="add-an-input-parameter"></a>Dodaj parametr wejściowy

Element Runbook aktualnie uruchamia maszynę wirtualną, która została stałe w elemencie Runbook, ale będzie bardziej przydatna, jeśli określisz maszynę wirtualną, gdy element Runbook zostanie uruchomiony. Dodaj parametry wejściowe do elementu Runbook, aby zapewnić tę funkcjonalność.

1. Dodaj parametry dla *VMName* i *ResourceGroupName* do elementu Runbook, a następnie użyj tych zmiennych za pomocą polecenia cmdlet **Start-AzureRmVM** , jak w poniższym przykładzie.

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Zapisz element Runbook, a następnie otwórz okienko testowania. Teraz możesz podać wartości dwóch zmiennych wejściowych, które zostaną użyte w teście.
1. Zamknij okienko testowania.
1. Kliknij pozycję **Opublikuj**, aby opublikować nową wersję elementu Runbook.
1. Zatrzymaj maszynę wirtualną uruchomioną w poprzednim kroku.
1. Kliknij przycisk **OK** , aby uruchomić element Runbook. Wpisz wartości parametrów **VMName** i **ResourceGroupName** dla maszyny wirtualnej do uruchomienia.<br><br> ![Przekazywanie parametru](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Po zakończeniu działania elementu Runbook sprawdź, czy maszyna wirtualna została uruchomiona.

## <a name="differences-from-powershell-workflow"></a>Różnice w stosunku do przepływu pracy programu PowerShell

Elementy Runbook programu PowerShell mają taki sam cykl życia, możliwości i procesy zarządzania jak elementy Runbook przepływu pracy programu PowerShell, ale istnieją pewne różnice i ograniczenia:

1. Elementy Runbook programu PowerShell działają szybko w porównaniu do elementów Runbook przepływu pracy programu PowerShell, ponieważ nie obejmują kroku kompilacji.
2. Elementy Runbook przepływu pracy programu PowerShell obsługują punkty kontrolne, przy użyciu punktów kontrolnych, elementy Runbook przepływu pracy programu PowerShell można wznawiać z dowolnego punktu w elemencie Runbook. Elementy Runbook programu PowerShell można wznawiać tylko od początku.
3. Elementy Runbook przepływu pracy programu PowerShell obsługują wykonywanie równoległe i szeregowe. Elementy Runbook programu PowerShell umożliwiają wykonywanie poleceń tylko szeregowo.
4. W elemencie Runbook przepływu pracy programu PowerShell działanie, polecenie lub blok skryptu może mieć własny obszar działania. W elemencie Runbook programu PowerShell wszystko w skrypcie działa w jednym obszarze działania. Istnieją również pewne [różnice składniowe](https://technet.microsoft.com/magazine/dn151046.aspx) między natywnym elementem Runbook programu PowerShell i elementem Runbook przepływu pracy programu PowerShell.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat programu PowerShell, w tym modułów dokumentacji i uczenia dotyczącej języka, zapoznaj się z dokumentacją programu [PowerShell](/powershell/scripting/overview).
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell).
* Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
* Aby uzyskać więcej informacji o funkcji obsługi skryptów programu PowerShell, zobacz [Native PowerShell script support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Obsługa natywnych skryptów programu PowerShell w usłudze Azure Automation).
