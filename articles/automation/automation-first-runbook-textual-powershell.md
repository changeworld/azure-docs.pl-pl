---
title: Mój pierwszy element runbook programu PowerShell w usłudze Azure Automation
description: W tym samouczku przedstawiono procesy tworzenia, testowania i publikowania prostego elementu Runbook programu PowerShell.
keywords: azure powershell, samouczek skryptu programu powershell, automatyzacja programu powershell
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 11/27/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 752aca68d1a95734bcefbd0c92d4cfc1e6fade7c
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303890"
---
# <a name="my-first-powershell-runbook"></a>Mój pierwszy element Runbook programu PowerShell

> [!div class="op_single_selector"]
> * [Element graficzny](automation-first-runbook-graphical.md)
> * [Program PowerShell](automation-first-runbook-textual-powershell.md)
> * [Przepływ pracy programu PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

W tym samouczku przedstawiono proces tworzenia [elementu Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) w usłudze Azure Automation. Możesz rozpocząć od prostego elementu runbook, testowanie i publikowanie, podczas gdy dowiesz się, jak śledzić stan zadania elementu runbook. Następnie zmodyfikujesz element runbook, aby faktycznie zarządzał zasobami platformy Azure — w tym przypadku uruchamiając maszynę wirtualną platformy Azure. Na koniec należy elementu runbook bardziej niezawodne, dodając parametry elementu runbook.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-quickstart-create-account.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Zatrzymaj i uruchom tę maszynę, aby nie powinien być to produkcyjna maszyna wirtualna.
* Konieczne może być [Aktualizuj moduły platformy Azure](automation-update-azure-modules.md) poleceń cmdlet, możesz użyć.

## <a name="create-new-runbook"></a>Tworzenie nowego elementu runbook

Rozpocznij od utworzenia prostego elementu runbook służącego do wyświetlania tekstu *Witaj, świecie*.

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Kliknij przycisk **elementów Runbook** w obszarze **automatyzacji procesów** aby otworzyć listę elementów runbook.
3. Tworzenie nowego elementu runbook, klikając **+ Dodaj element runbook** przycisku i następnie **Utwórz nowy element runbook**.
4. Nadaj elementowi Runbook nazwę *MyFirstRunbook-PowerShell*.
5. W takim przypadku możesz zacząć tworzyć [element runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) dlatego wybierz **Powershell** dla **typ elementu Runbook**.
6. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor tekstów.

## <a name="add-code-to-the-runbook"></a>Dodaj kod do elementu runbook

Możesz wpisać kod bezpośrednio w elemencie Runbook lub wybrać polecenia cmdlet, elementy Runbook i elementy zawartości, używając kontrolki Biblioteka, a następnie dodać je do elementu Runbook z powiązanymi parametrami. W ramach tego przewodnika, możesz wpisać bezpośrednio w elemencie runbook.

1. Element runbook jest obecnie pusty typ *Write-Output "Hello World".* w treści skryptu.

   ![Witaj, świecie](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  

2. Zapisz element Runbook, klikając przycisk **Zapisz**.

## <a name="step-3---test-the-runbook"> </a> Testowanie elementu runbook

Przed opublikowaniem elementu runbook w celu udostępnienia go w środowisku produkcyjnym warto go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementu Runbook polega na uruchomieniu jego **wersji roboczej** i interaktywnym przejrzeniu danych wyjściowych.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć okienko testowania.
2. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.
3. Zostanie utworzone [zadanie elementu Runbook](automation-runbook-execution.md) i pojawi się jego stan.

   Początkowy stan zadania *kolejce* wskazujący, że trwa oczekiwanie procesu roboczego elementu runbook w chmurze na udostępnienie. Przenosi do *od* gdy proces roboczy pobierze zadanie, a następnie *systemem* , gdy element runbook faktycznie zacznie działać.  

4. Po zakończeniu zadania elementu Runbook zostaną wyświetlone jego dane wyjściowe. W Twoim przypadku powinien zostać wyświetlony *Witaj, świecie*.

   ![Dane wyjściowe w okienku testowania](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  

5. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="publish-and-start-the-runbook"></a>Publikowanie i uruchamianie elementu runbook

Nowo utworzony element runbook nadal działa w trybie roboczym. Należy opublikować, zanim będzie można uruchomić w środowisku produkcyjnym.  Podczas publikowania elementu Runbook można zastąpić istniejącą wersję opublikowaną wersją roboczą. W Twoim przypadku możesz nie jeszcze wersji opublikowanej został właśnie utworzony element runbook.

1. Kliknij pozycję **Opublikuj**, aby opublikować element Runbook, a następnie kliknij pozycję **Tak** po wyświetleniu monitu.
1. Jeśli przewiniesz w lewo, aby wyświetlić element runbook w programie **elementów Runbook** okienko teraz pokazuje **stan pisania przyp** z **opublikowano**.
1. Przewiń w prawo, aby wyświetlić okienko elementu **MyFirstRunbook-PowerShell**.  
   Opcje w górnej części umożliwiają uruchamianie elementu Runbook, wyświetlanie elementu Runbook, planowanie jego uruchomienia w przyszłości lub utworzenie [elementu webhook](automation-webhooks.md) w celu umożliwienia uruchamiania za pośrednictwem wywołania HTTP.
1. Chcesz uruchomić element runbook, więc klikamy **Start** a następnie kliknij przycisk **Ok** po otwarciu strony Uruchamianie elementu Runbook.
1. Strona zadania jest otwarty dla zadania elementu runbook, który został utworzony. Możesz zamknąć to okienko, ale w takim przypadku użytkownik pozostanie on otwarty, aby było obserwować postęp zadania.
1. Stan zadania jest wyświetlany w **Podsumowanie zadania** i odpowiada stanom widoczny podczas testowania elementu runbook.

   ![Podsumowanie zadania](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Gdy stanem elementu runbook będzie *Ukończono*w obszarze **Przegląd** kliknij **dane wyjściowe**. Zostanie otwarte okienko danych wyjściowych i można zobaczyć swoje *Witaj, świecie*.

   ![Dane wyjściowe zadania](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Zamknij stronę danych wyjściowych.
1. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. Powinien być widoczny tylko *Witaj, świecie* w danych wyjściowych strumienia, ale te dane wyjściowe mogą zostać wyświetlone inne strumienie zadania elementu runbook, takie jak pełne informacje i błąd jeśli element runbook zapisuje do nich.

   ![Wszystkie dzienniki](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Zamknij stronę strumieni i strona zadania, aby wrócić do strony MyFirstRunbook-PowerShell.
1. W obszarze **szczegóły**, kliknij przycisk **zadania** aby otworzyć okienko zadań dla tego elementu runbook. Ta strona zawiera listę wszystkich zadań utworzonych przez ten element runbook. Ponieważ uruchomiono zadanie tylko raz, powinno być widoczne tylko jedno zadanie.

   ![Lista zadań](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  

1. Możesz kliknąć to zadanie, aby otworzyć okienko Zadania wyświetlone wcześniej po uruchomieniu elementu runbook. Ta akcja umożliwia przechodzenie wstecz w czasie i wyświetlić szczegóły dowolnego zadania, które zostało utworzone dla określonego elementu runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure

Element runbook został przetestowany i opublikowany, ale jak do tej pory nie wykonuje on żadnych użytecznych czynności. Powinien zarządzać zasobami platformy Azure. Nie jest w stanie to jednak jeśli nie masz uwierzytelnienia go przy użyciu połączenie Uruchom jako, które są tworzone automatycznie podczas tworzenia konta usługi automation. Użyj połączenie Uruchom jako przy użyciu **Connect-AzureRmAccount** polecenia cmdlet. W przypadku zarządzania zasobami w wielu subskrypcjach należy użyć **- AzureRmContext** parametru wraz z [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process
   
   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. Otwórz Edytor tekstów, klikając **Edytuj** na stronie MyFirstRunbook-PowerShell.
1. Nie ma potrzeby **Write-Output** już wiersza, więc Przejdź dalej i usuń go.
1. Wpisz lub skopiuj i wklej poniższy kod, który obsługuje uwierzytelnianie za pomocą konta Uruchom jako w usłudze Automation:

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** i **Login-AzureRmAccount** są teraz aliasów **Connect-AzureRMAccount**. Jeśli **Connect-AzureRMAccount** polecenie cmdlet nie istnieje, możesz użyć **Add-AzureRmAccount** lub **Login-AzureRmAccount**, lub można zaktualizować moduły w automatyzacji Konta do najnowszej wersji.

1. Kliknij przycisk **okienko testowania** tak, aby przetestować element runbook.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu powinny pojawić się dane wyjściowe podobne do poniższych, zawierające podstawowe informacje powiązane z kontem. Te dane wyjściowe potwierdzają, że konto Uruchom jako jest prawidłowe.

   ![Uwierzytelnianie](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="add-code-to-start-a-virtual-machine"></a>Dodaj kod, aby uruchomić maszynę wirtualną

Teraz, gdy element runbook jest uwierzytelniany w subskrypcji platformy Azure, można zarządzać zasobami. Możesz dodać polecenie do uruchomienia maszyny wirtualnej. Można wybrać dowolną maszynę wirtualną w subskrypcji platformy Azure i w przypadku, teraz umieszczona w kodzie nazwy w elemencie runbook.

1. Po *Connect-AzureRmAccount*, typ *Start-AzureRmVM-Name 'Nazwa_maszyny_wirtualnej' - ResourceGroupName 'Nazwa_grupy_zasobów'* podanie nazwy i nazwy grupy zasobów maszyny wirtualnej, aby rozpocząć.  

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Zapisz element runbook, a następnie kliknij przycisk **okienko testowania** tak, aby można ją przetestować.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu sprawdź, czy maszyna wirtualna została uruchomiona.

## <a name="add-an-input-parameter"></a>Dodawanie parametru wejściowego

Element runbook aktualnie umożliwia uruchomienie maszyny wirtualnej tego zostanie zapisane na stałe w elemencie runbook, ale będzie bardziej użyteczny, jeśli określisz maszyny wirtualnej po uruchomieniu elementu runbook. Dodanie parametrów wejściowych elementu runbook w celu zapewnienia tej funkcji.

1. Dodaj parametry *VMName* i *ResourceGroupName* do elementu runbook i użyj tych zmiennych z **Start-AzureRmVM** polecenia cmdlet, jak w poniższym przykładzie.

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
1. Kliknij przycisk **OK** uruchomienia elementu runbook. Wpisz wartości parametrów **VMName** i **ResourceGroupName** dla maszyny wirtualnej do uruchomienia.<br><br> ![Przekazywanie parametru](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
1. Po zakończeniu działania elementu Runbook sprawdź, czy maszyna wirtualna została uruchomiona.

## <a name="differences-from-powershell-workflow"></a>Różnice w stosunku do przepływu pracy programu PowerShell

Elementy Runbook programu PowerShell mają taki sam cykl życia, możliwości i procesy zarządzania jak elementy Runbook przepływu pracy programu PowerShell, ale istnieją pewne różnice i ograniczenia:

1. Elementy Runbook programu PowerShell działają szybko w porównaniu do elementów Runbook przepływu pracy programu PowerShell, ponieważ nie obejmują kroku kompilacji.
2. Elementy runbook przepływu pracy programu PowerShell obsługują punkty kontrolne, przy użyciu punktów kontrolnych, elementów runbook przepływu pracy programu PowerShell można wznowić w dowolnym momencie w elemencie runbook. Elementy runbook programu PowerShell mogą wznawiać działanie tylko od samego początku.
3. Elementy runbook przepływu pracy programu PowerShell obsługują wykonywanie równoległe i szeregowe. Elementy runbook programu PowerShell mogą wykonywać polecenia tylko szeregowo.
4. Przepływ pracy programu PowerShell, działanie, polecenie lub skrypt bloku mogą mieć własny obszar działania. W elemencie runbook programu PowerShell wszystkie elementy skryptu są uruchamiane w jednym obszarze działania. Istnieją również pewne [różnice składniowe](https://technet.microsoft.com/magazine/dn151046.aspx) między natywnym elementem Runbook programu PowerShell i elementem Runbook przepływu pracy programu PowerShell.

## <a name="next-steps"></a>Kolejne kroki

* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell).
* Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
* Aby uzyskać więcej informacji o funkcji obsługi skryptów programu PowerShell, zobacz [Native PowerShell script support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Obsługa natywnych skryptów programu PowerShell w usłudze Azure Automation).
