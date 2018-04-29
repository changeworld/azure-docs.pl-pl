---
title: Moje pierwszy element runbook przepływu pracy programu PowerShell w usłudze Automatyzacja Azure
description: Samouczek, który przeprowadzi Cię przez procesy tworzenia, testowania i publikowania prostego tekstowego elementu Runbook przy użyciu przepływu programu PowerShell.
keywords: przepływ pracy programu powershell, przykłady przepływu pracy programu powershell, program powershell przepływu pracy
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 33e9b68973aa399123fa9e62a2d0eea77c55add0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="my-first-powershell-workflow-runbook"></a>Mój pierwszy element Runbook przepływu pracy programu PowerShell

> [!div class="op_single_selector"]
> * [Element graficzny](automation-first-runbook-graphical.md)
> * [Program PowerShell](automation-first-runbook-textual-powershell.md)
> * [Przepływ pracy programu PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Ten samouczek przeprowadzi Cię przez proces tworzenia [elementu Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) w usłudze Azure Automation. Rozpoczyna prosty element runbook, przetestować i opublikować podczas wyjaśniający, jak śledzić stan zadania elementu runbook. Następnie zmodyfikujesz element runbook, aby faktycznie zarządzał zasobami platformy Azure — w tym przypadku uruchamiając maszynę wirtualną platformy Azure. Na koniec należy elementu runbook bardziej niezawodne, dodając parametry elementu runbook.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure.  To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Zatrzymać i uruchomić tej maszyny, więc nie powinno być produkcji maszyny Wirtualnej.

## <a name="step-1---create-new-runbook"></a>Krok 1. Tworzenie nowego elementu Runbook
Rozpocznij od utworzenia prosty element runbook, która wyświetla tekst *Hello World*.

1. W witrynie Azure Portal otwórz konto usługi Automation.

   Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość z nich to moduły, które są automatycznie uwzględniane na nowym koncie usługi Automation. Powinien istnieć również element zawartości Poświadczenie wymieniony w części dotyczącej [wymagań wstępnych](#prerequisites).

1. Kliknij przycisk **Runbook** w obszarze **automatyzacji procesu** aby otworzyć listę elementów runbook.
2. Utwórz nowy element runbook, klikając **+ Dodaj element runbook** przycisk, a następnie **Utwórz nowy element runbook**.
3. Nadaj elementowi Runbook nazwę *MyFirstRunbook-Workflow*.
4. W takim przypadku użytkownik chce utworzyć [runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) tak wybierz **przepływu pracy programu Powershell** dla **typ elementu Runbook**.
5. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor tekstów.

## <a name="step-2---add-code-to-the-runbook"></a>Krok 2. Dodawanie kodu do elementu Runbook
Możesz wpisać kod bezpośrednio w elemencie Runbook lub wybrać polecenia cmdlet, elementy Runbook i elementy zawartości, używając kontrolki Biblioteka, a następnie dodać je do elementu Runbook z powiązanymi parametrami. W ramach tego przewodnika wpisz bezpośrednio do elementu runbook.

1. Element runbook jest obecnie pusta z tylko wymagane *przepływu pracy* — słowo kluczowe, nazwa elementu runbook i nawiasy klamrowe, który encases całego przepływu pracy.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```
2. Wpisz ciąg *Write-Output „Witaj, świecie”* między nawiasami klamrowymi.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```
3. Zapisz element Runbook, klikając przycisk **Zapisz**.

## <a name="step-3---test-the-runbook"></a>Krok 3. Testowanie elementu Runbook
Przed opublikowaniem elementu runbook w celu udostępnienia go w środowisku produkcyjnym warto go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementu Runbook polega na uruchomieniu jego **wersji roboczej** i interaktywnym przejrzeniu danych wyjściowych.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć okienko testowania.
2. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.
3. Zostanie utworzone [zadanie elementu Runbook](automation-runbook-execution.md) i pojawi się jego stan.

   Zadanie będzie miało początkowy stan *W kolejce*, wskazujący, że trwa oczekiwanie na udostępnienie procesu roboczego elementu Runbook w chmurze. Powoduje przeniesienie do *uruchamianie* gdy pracownik oświadczeń zadania, a następnie *systemem* , gdy element runbook faktycznie zacznie działać.  

1. Po zakończeniu zadania elementu Runbook zostaną wyświetlone jego dane wyjściowe. W Twoim przypadku powinna zostać wyświetlona *Hello World*.<br><br> ![Witaj, świecie](media/automation-first-runbook-textual/test-output-hello-world.png)
2. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4. Publikowanie i uruchamianie elementu Runbook
Nowo utworzony element runbook nadal działa w trybie roboczym. Należy opublikować przed uruchomieniem jej w środowisku produkcyjnym. Podczas publikowania elementu Runbook można zastąpić istniejącą wersję opublikowaną wersją roboczą. W Twoim przypadku nie ma wersję opublikowaną jeszcze ponieważ właśnie utworzony element runbook.

1. Kliknij pozycję **Opublikuj**, aby opublikować element Runbook, a następnie kliknij pozycję **Tak** po wyświetleniu monitu.
2. Podczas przewijania po lewej, aby wyświetlić element runbook w programie **elementów Runbook** okienko teraz, będzie wyświetlana **stan pisania przyp** z **opublikowano**.
3. Przewiń w prawo, aby wyświetlić okienko elementu **MyFirstRunbook-Workflow**.  
   Opcje w górnej części umożliwiają nam uruchamianie elementu Runbook, planowanie jego uruchomienia w przyszłości lub utworzenie [elementu webhook](automation-webhooks.md) w celu umożliwienia uruchamiania za pośrednictwem wywołania HTTP.
4. po prostu chcesz uruchomić element runbook więc klikamy **Start** , a następnie **tak** po wyświetleniu monitu.<br><br> ![Uruchamianie elementu Runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)
5. Okienko zadania jest otwarty dla zadania elementu runbook, który został utworzony. Możesz zamknąć to okienko, ale w takim przypadku można zostawić otwarty, możesz obserwować postęp zadania.
6. Stan zadania jest wyświetlany w **Podsumowanie zadania** i odpowiada stany pokazaliśmy, gdy przetestować element runbook.<br><br> ![Podsumowanie zadania](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)
7. Gdy stanem elementu Runbook będzie *Ukończono*, kliknij pozycję **Dane wyjściowe**. W okienku danych wyjściowych jest otwarty, aby zobaczyć Twojej *Hello World*.<br><br> ![Podsumowanie zadania](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)  
8. Zamknij okienko danych wyjściowych.
9. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. Powinny pojawić tylko *Hello World* w danych wyjściowych strumienia, ale mogą być prezentowane z innych strumieni dla zadania elementu runbook, takie jak Verbose i błąd jeśli element runbook zapisuje do nich.<br><br> ![Podsumowanie zadania](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)
10. Zamknij okienko strumieni i okienko zadania, aby wrócić do okienka MyFirstRunbook.
11. Kliknij pozycję **Zadania**, aby otworzyć okienko zadań dla tego elementu Runbook. Zawiera ono listę wszystkich zadań utworzonych przez dany element Runbook. Powinny pojawić tylko jedno zadanie na liście od czasu tylko uruchomienia zadania raz.<br><br> ![Zadania](media/automation-first-runbook-textual/runbook-control-job-tile.png)
12. Kliknięcie tego zadania, aby otworzyć okienko zadania tego samego wyświetlane przy uruchamianiu elementu runbook. Dzięki temu możesz cofnąć się w czasie i wyświetlić szczegóły dowolnego zadania, które zostało utworzone dla określonego elementu Runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Krok 5. Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure
Zostały przetestowane i publikowane elementu runbook, ale do tej pory nie niczego przydatne. Ma ona zarządzania zasobami Azure. Nie będzie można to zrobić, jeśli w przypadku braku uwierzytelniania przy użyciu poświadczeń, które są określone w [wymagania wstępne](#prerequisites). Można to zrobić z **Connect-AzureRmAccount** polecenia cmdlet.

1. Otwórz edytor tekstów, klikając pozycję **Edytuj** w okienku MyFirstRunbook-Workflow.
2. nie ma potrzeby **Write-Output** wiersz już, więc Przejdź dalej i usuń go.
3. Umieść kursor w pustym wierszu między nawiasami klamrowymi.
4. Wpisz lub skopiuj i wklej poniższy kod, który będzie obsługiwać uwierzytelnianie za pomocą konta Uruchom jako w usłudze Automation:

   ```powershell-interactive
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
5. Kliknij przycisk **okienku testu** tak, aby przetestować element runbook.
6. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu powinny pojawić się dane wyjściowe podobne do poniższych, zawierające podstawowe informacje powiązane z kontem. Będzie to potwierdzenie, że poświadczenie jest prawidłowe.<br><br> ![Uwierzytelnianie](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Krok 6. Dodawanie kodu w celu uruchomienia maszyny wirtualnej
Teraz, gdy element runbook jest uwierzytelniania do subskrypcji platformy Azure, mogą zarządzać zasobami. Możesz dodać polecenie można uruchomić maszyny wirtualnej. Można wybrać żadnej maszyny wirtualnej w ramach subskrypcji platformy Azure, a obecnie są hardcoding, czy nazwa elementu runbook.

1. Po *Connect-AzureRmAccount*, typu *Start AzureRmVM-Name "VMName" - ResourceGroupName "NameofResourceGroup"* podanie nazwy i nazwy grupy zasobów można uruchomić maszyny wirtualnej.  

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   }
   ```
2. Zapisz element runbook, a następnie kliknij przycisk **okienku testu** tak, aby można go sprawdzić.
3. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu sprawdź, czy maszyna wirtualna została uruchomiona.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Krok 7. Dodawanie parametru wejściowego do elementu Runbook
Element runbook aktualnie uruchamia maszynę wirtualną tego zostanie zapisane na stałe w elemencie runbook, ale byłoby bardziej użyteczna w maszynie wirtualnej można określić po uruchomieniu elementu runbook. Należy dodać parametry wejściowe elementu runbook w celu obsługi tej funkcjonalności.

1. Dodaj parametry *VMName* i *ResourceGroupName* do elementu Runbook i użyj tych zmiennych z poleceniem cmdlet **Start AzureRmVM**, tak jak w przykładzie poniżej.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )  
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```
2. Zapisz element Runbook, a następnie otwórz okienko testowania. Teraz można podać wartości dla dwie zmienne wejściowe, które znajdują się w teście.
3. Zamknij okienko testowania.
4. Kliknij pozycję **Opublikuj**, aby opublikować nową wersję elementu Runbook.
5. Zatrzymaj maszynę wirtualną uruchomioną w poprzednim kroku.
6. Kliknij pozycję **Uruchom**, aby uruchomić element Runbook. Wpisz wartości parametrów **VMName** i **ResourceGroupName** dla maszyny wirtualnej do uruchomienia.<br><br> ![Uruchamianie elementu Runbook](media/automation-first-runbook-textual/automation-pass-params.png)<br>  
7. Po zakończeniu działania elementu Runbook sprawdź, czy maszyna wirtualna została uruchomiona.  

## <a name="next-steps"></a>Kolejne kroki
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz artykuł [My first PowerShell runbook](automation-first-runbook-textual-powershell.md) (Mój pierwszy element Runbook programu PowerShell).
* Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz artykuł [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
* Aby uzyskać więcej informacji o funkcji obsługi skryptów programu PowerShell, zobacz artykuł [Native PowerShell script support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Obsługa natywnych skryptów programu PowerShell w usłudze Azure Automation).
