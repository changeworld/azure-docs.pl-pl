---
title: Mój pierwszy element runbook programu PowerShell w usłudze Azure Automation
description: W tym samouczku przedstawiono procesy tworzenia, testowania i publikowania prostego elementu Runbook programu PowerShell.
keywords: azure powershell, samouczek skryptu programu powershell, automatyzacja programu powershell
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2f5d2f3634545001dc6dc1419530223b5a1a85a3
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435795"
---
# <a name="my-first-powershell-runbook"></a>Mój pierwszy element Runbook programu PowerShell

> [!div class="op_single_selector"]
> * [Element graficzny](automation-first-runbook-graphical.md)
> * [Program PowerShell](automation-first-runbook-textual-powershell.md)
> * [Przepływ pracy programu PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

W tym samouczku przedstawiono proces tworzenia [elementu Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) w usłudze Azure Automation. Możesz rozpocząć od prostego elementu runbook, testowanie i publikowanie, podczas gdy dowiesz się, jak śledzić stan zadania elementu runbook. Następnie zmodyfikujesz element runbook, aby faktycznie zarządzał zasobami platformy Azure — w tym przypadku uruchamiając maszynę wirtualną platformy Azure. Na koniec należy elementu runbook bardziej niezawodne, dodając parametry elementu runbook.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Będziesz uruchamiać i zatrzymywać tę maszynę, dlatego należy użyć maszyny innej niż produkcyjna.

## <a name="step-1---create-new-runbook"></a>Krok 1. Tworzenie nowego elementu Runbook
Rozpocznij od utworzenia prostego elementu runbook służącego do wyświetlania tekstu *Witaj, świecie*.

1. W witrynie Azure Portal otwórz konto usługi Automation.

   Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość z nich to moduły, które są automatycznie uwzględniane na nowym koncie usługi Automation. Powinien istnieć również element zawartości Poświadczenie wymieniony w części dotyczącej [wymagań wstępnych](#prerequisites).

1. Kliknij przycisk **elementów Runbook** w obszarze **automatyzacji procesów** aby otworzyć listę elementów runbook.
2. Tworzenie nowego elementu runbook, klikając **+ Dodaj element runbook** przycisku i następnie **Utwórz nowy element runbook**.
3. Nadaj elementowi Runbook nazwę *MyFirstRunbook-PowerShell*.
4. W takim przypadku możesz zacząć tworzyć [element runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) dlatego wybierz **Powershell** dla **typ elementu Runbook**.
5. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor tekstów.

## <a name="step-2---add-code-to-the-runbook"></a>Krok 2. Dodawanie kodu do elementu Runbook
Możesz wpisać kod bezpośrednio w elemencie Runbook lub wybrać polecenia cmdlet, elementy Runbook i elementy zawartości, używając kontrolki Biblioteka, a następnie dodać je do elementu Runbook z powiązanymi parametrami. W ramach tego przewodnika, możesz wpisać bezpośrednio w elemencie runbook.

1. Element runbook jest obecnie pusty typ *Write-Output "Hello World".* w treści skryptu.<br><br> ![Witaj, świecie](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. Zapisz element Runbook, klikając przycisk **Zapisz**.

## <a name="step-3---test-the-runbook"></a>Krok 3. Testowanie elementu Runbook
Przed opublikowaniem elementu runbook w celu udostępnienia go w środowisku produkcyjnym warto go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementu Runbook polega na uruchomieniu jego **wersji roboczej** i interaktywnym przejrzeniu danych wyjściowych.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć okienko testowania.
2. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.
3. Zostanie utworzone [zadanie elementu Runbook](automation-runbook-execution.md) i pojawi się jego stan.

   Zadanie będzie miało początkowy stan *W kolejce*, wskazujący, że trwa oczekiwanie na udostępnienie procesu roboczego elementu runbook w chmurze. Przenosi do *od* gdy proces roboczy pobierze zadanie, a następnie *systemem* , gdy element runbook faktycznie zacznie działać.  

1. Po zakończeniu zadania elementu Runbook zostaną wyświetlone jego dane wyjściowe. W Twoim przypadku powinien zostać wyświetlony *Witaj, świecie*.<br><br> ![Dane wyjściowe w okienku testowania](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
2. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4. Publikowanie i uruchamianie elementu Runbook
Nowo utworzony element runbook nadal działa w trybie roboczym. należy ją opublikować, zanim będzie można uruchomić w środowisku produkcyjnym.  Podczas publikowania elementu Runbook można zastąpić istniejącą wersję opublikowaną wersją roboczą. W Twoim przypadku możesz nie jeszcze wersji opublikowanej został właśnie utworzony element runbook.

1. Kliknij pozycję **Opublikuj**, aby opublikować element Runbook, a następnie kliknij pozycję **Tak** po wyświetleniu monitu.
2. Jeśli przewiniesz w lewo, aby wyświetlić element runbook w programie **elementów Runbook** okienko teraz pokazuje **stan pisania przyp** z **opublikowano**.
3. Przewiń w prawo, aby wyświetlić okienko elementu **MyFirstRunbook-PowerShell**.  
   Opcje w górnej części umożliwiają uruchamianie elementu Runbook, wyświetlanie elementu Runbook, planowanie jego uruchomienia w przyszłości lub utworzenie [elementu webhook](automation-webhooks.md) w celu umożliwienia uruchamiania za pośrednictwem wywołania HTTP.
4. Chcesz uruchomić element runbook, więc klikamy **Start** a następnie kliknij przycisk **Ok** po otwarciu strony Uruchamianie elementu Runbook.
5. Strona zadania jest otwarty dla zadania elementu runbook, który został utworzony. Możesz zamknąć to okienko, ale w takim przypadku użytkownik pozostanie on otwarty, aby było obserwować postęp zadania.
6. Stan zadania jest wyświetlany w **Podsumowanie zadania** i odpowiada stanom widoczny podczas testowania elementu runbook.<br><br> ![Podsumowanie zadania](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)<br>  
7. Gdy stanem elementu runbook będzie *Ukończono*w obszarze **Przegląd** kliknij **dane wyjściowe**. Zostanie otwarte okienko danych wyjściowych i można zobaczyć swoje *Witaj, świecie*.<br><br> ![Dane wyjściowe zadania](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)<br> 
8. Zamknij stronę danych wyjściowych.
9. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. W strumieniu danych wyjściowych powinien być widoczny tylko ciąg *Witaj, świecie*, ale mogą zostać wyświetlone inne strumienie zadania elementu runbook, takie jak Pełne informacje i Błąd, jeśli element runbook wykonuje w nich operacje zapisywania.<br><br> ![Wszystkie dzienniki](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)<br>   
10. Zamknij stronę strumieni i strona zadania, aby wrócić do strony MyFirstRunbook-PowerShell.
11. W obszarze **szczegóły**, kliknij przycisk **zadania** aby otworzyć okienko zadań dla tego elementu runbook. Zawiera ono listę wszystkich zadań utworzonych przez dany element Runbook. Ponieważ uruchomiono zadanie tylko raz, powinno być widoczne tylko jedno zadanie.<br><br> ![Lista zadań](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  
12. Możesz kliknąć to zadanie, aby otworzyć okienko Zadania wyświetlone wcześniej po uruchomieniu elementu runbook. Dzięki temu możesz cofnąć się w czasie i wyświetlić szczegóły dowolnego zadania, które zostało utworzone dla określonego elementu Runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Krok 5. Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure
Element runbook został przetestowany i opublikowany, ale jak do tej pory nie wykonuje on żadnych użytecznych czynności. Powinien zarządzać zasobami platformy Azure. Nie jest w stanie to jednak, chyba że uwierzytelniania przy użyciu poświadczeń, które są określone w [wymagania wstępne](#prerequisites). Można to zrobić za pomocą **Connect-AzureRmAccount** polecenia cmdlet.

1. Otwórz Edytor tekstów, klikając **Edytuj** na stronie MyFirstRunbook-PowerShell.
2. Nie ma potrzeby **Write-Output** już wiersza, więc Przejdź dalej i usuń go.
3. Wpisz lub skopiuj i wklej poniższy kod, który obsługuje uwierzytelnianie za pomocą konta Uruchom jako w usłudze Automation:

   ```powershell
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** i **Login-AzureRmAccount** są teraz aliasów **Connect-AzureRMAccount**. Jeśli **Connect-AzureRMAccount** polecenie cmdlet nie istnieje, możesz użyć **Add-AzureRmAccount** lub **Login-AzureRmAccount**, lub można zaktualizować moduły w automatyzacji Konta do najnowszej wersji.

4. Kliknij przycisk **okienko testowania** tak, aby przetestować element runbook.
5. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu powinny pojawić się dane wyjściowe podobne do poniższych, zawierające podstawowe informacje powiązane z kontem. Będzie to potwierdzenie, że poświadczenie jest prawidłowe.<br><br> ![Uwierzytelnianie](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Krok 6. Dodawanie kodu w celu uruchomienia maszyny wirtualnej
Teraz, gdy element runbook jest uwierzytelniany w subskrypcji platformy Azure, można zarządzać zasobami. Możesz dodać polecenie do uruchomienia maszyny wirtualnej. Można wybrać dowolną maszynę wirtualną w subskrypcji platformy Azure i w przypadku, teraz umieszczona w kodzie nazwy w elemencie runbook.

1. Po *Connect-AzureRmAccount*, typ *Start-AzureRmVM-Name 'Nazwa_maszyny_wirtualnej' - ResourceGroupName 'Nazwa_grupy_zasobów'* podanie nazwy i nazwy grupy zasobów maszyny wirtualnej, aby rozpocząć.  
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```
   <br>
2. Zapisz element runbook, a następnie kliknij przycisk **okienko testowania** tak, aby można ją przetestować.
3. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu sprawdź, czy maszyna wirtualna została uruchomiona.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Krok 7. Dodawanie parametru wejściowego do elementu Runbook
Element runbook aktualnie umożliwia uruchomienie maszyny wirtualnej tego zostanie zapisane na stałe w elemencie runbook, ale będzie bardziej użyteczny, jeśli określisz maszyny wirtualnej po uruchomieniu elementu runbook. Dodanie parametrów wejściowych elementu runbook w celu zapewnienia tej funkcji.

1. Dodaj parametry *VMName* i *ResourceGroupName* do elementu runbook i użyj tych zmiennych z **Start-AzureRmVM** polecenia cmdlet, jak w poniższym przykładzie.

   ```
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```
   <br>
1. Zapisz element Runbook, a następnie otwórz okienko testowania. Teraz możesz podać wartości dwóch zmiennych wejściowych, które zostaną użyte w teście.
2. Zamknij okienko testowania.
3. Kliknij pozycję **Opublikuj**, aby opublikować nową wersję elementu Runbook.
4. Zatrzymaj maszynę wirtualną uruchomioną w poprzednim kroku.
5. Kliknij przycisk **OK** uruchomienia elementu runbook. Wpisz wartości parametrów **VMName** i **ResourceGroupName** dla maszyny wirtualnej do uruchomienia.<br><br> ![Przekazywanie parametru](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
6. Po zakończeniu działania elementu Runbook sprawdź, czy maszyna wirtualna została uruchomiona.

## <a name="differences-from-powershell-workflow"></a>Różnice w stosunku do przepływu pracy programu PowerShell
Elementy Runbook programu PowerShell mają taki sam cykl życia, możliwości i procesy zarządzania jak elementy Runbook przepływu pracy programu PowerShell, ale istnieją pewne różnice i ograniczenia:

1. Elementy Runbook programu PowerShell działają szybko w porównaniu do elementów Runbook przepływu pracy programu PowerShell, ponieważ nie obejmują kroku kompilacji.
2. Elementy Runbook przepływu pracy programu PowerShell obsługują punkty kontrolne, a dzięki temu ich działanie może być wznawiane w dowolnym punkcie elementu Runbook. Elementy Runbook programu PowerShell mogą wznawiać działanie tylko od początku.
3. Elementy Runbook przepływu pracy programu PowerShell obsługują wykonywanie równoległe i szeregowe, natomiast elementy Runbook programu PowerShell mogą wykonywać polecenia tylko szeregowo.
4. W elemencie Runbook przepływu pracy programu PowerShell działanie, polecenie lub blok skryptu może mieć własny obszar działania, a w elemencie Runbook programu PowerShell wszystkie elementy skryptu są uruchamiane w jednym obszarze działania. Istnieją również pewne [różnice składniowe](https://technet.microsoft.com/magazine/dn151046.aspx) między natywnym elementem Runbook programu PowerShell i elementem Runbook przepływu pracy programu PowerShell.

## <a name="next-steps"></a>Kolejne kroki
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell).
* Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
* Aby uzyskać więcej informacji o funkcji obsługi skryptów programu PowerShell, zobacz [Native PowerShell script support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Obsługa natywnych skryptów programu PowerShell w usłudze Azure Automation).

