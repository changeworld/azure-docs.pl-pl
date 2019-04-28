---
title: Mój pierwszy graficzny element runbook w usłudze Azure Automation
description: Samouczek, który przeprowadzi Cię przez procesy tworzenia, testowania i publikowania prostego graficznego elementu Runbook.
keywords: runbook, szablon runbook, automatyzacja runbook, azure runbook
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/13/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: be811d0dc2ce2eca0b20ca12165eaf0799bd6b5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61078107"
---
# <a name="my-first-graphical-runbook"></a>Mój pierwszy graficzny element Runbook

> [!div class="op_single_selector"]
> * [Element graficzny](automation-first-runbook-graphical.md)
> * [Program PowerShell](automation-first-runbook-textual-powershell.md)
> * [Przepływ pracy programu PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Ten samouczek przeprowadzi Cię przez proces tworzenia [graficznego elementu Runbook](automation-runbook-types.md#graphical-runbooks) w usłudze Azure Automation. Na początek utworzysz prosty element runbook służący do testowania i publikowania, a równocześnie dowiesz się, jak śledzić stan zadania elementu runbook. Następnie zmodyfikujesz element runbook, aby faktycznie zarządzał zasobami platformy Azure — w tym przypadku uruchamiając maszynę wirtualną platformy Azure. Następnie zakończysz pracę z samouczkiem, udoskonalając element runbook przez dodanie do niego parametrów i połączeń warunkowych.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Będziesz uruchamiać i zatrzymywać tę maszynę, dlatego należy użyć maszyny innej niż produkcyjna.

## <a name="create-runbook"></a>Tworzenie elementu runbook

Na początek utworzysz prosty element runbook służący do wyświetlania tekstu *Witaj, świecie*.

1. W witrynie Azure Portal otwórz konto usługi Automation.

   Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość z nich to moduły, które są automatycznie uwzględniane na nowym koncie usługi Automation. Powinien istnieć również element zawartości Poświadczenie wymieniony w części dotyczącej [wymagań wstępnych](#prerequisites).

2. Wybierz pozycję **Elementy runbook** w obszarze **ZARZĄDZANIE PROCESAMI**, aby otworzyć listę elementów runbook.
3. Tworzenie nowego elementu runbook, wybierając **+ Dodaj element runbook**, następnie kliknij przycisk **Utwórz nowy element runbook**.
4. Nadaj elementowi Runbook nazwę *MyFirstRunbook-Graphical*.
5. W tym przypadku utworzysz [graficzny element runbook](automation-graphical-authoring-intro.md), dlatego wybierz wartość **Element graficzny** parametru **Typ elementu runbook**.<br> ![Nowy element Runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor graficzny.

## <a name="add-activities"></a>Dodawanie działań

Kontrolka Biblioteka po lewej stronie edytora umożliwia wybranie działań do dodania do elementu Runbook. Teraz dodasz polecenie cmdlet **Write-Output**, aby element runbook wyprowadzał tekst.

1. W kontrolce Biblioteka kliknij pole tekstowe wyszukiwania i wpisz **Write-Output**. Poniższa ilustracja przedstawia wyniki wyszukiwania: <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Przewiń listę w dół. Kliknij prawym przyciskiem myszy pozycję **Write-Output** i wybierz polecenie **Dodaj do kanwy** lub kliknij wielokropek obok polecenia cmdlet, a następnie wybierz pozycję **Dodaj do kanwy**.
1. Kliknij pozycję **Write-Output** na kanwie. Spowoduje to otwarcie strony Kontrola konfiguracji, który umożliwia skonfigurowanie działania.
1. Wartość pola **Etykieta** jest domyślnie ustawiana na nazwę polecenia cmdlet, ale można zmienić ją na bardziej opisową. Zmień nazwę na *Zapisz ciąg Witaj, świecie w danych wyjściowych*.
1. Kliknij pozycję **Parametry**, aby podać wartości parametrów polecenia cmdlet.

   Niektóre polecenia cmdlet mają wiele zestawów parametrów i konieczne jest wybranie zestawu do użycia. W tym przypadku polecenie **Write-Output** ma tylko jeden zestaw parametrów, więc nie musisz go wybierać.

1. Wybierz parametr **InputObject**. Jest to parametr służący do określania tekstu, który zostanie wysłany do strumienia wyjściowego.
1. Z listy rozwijanej **Źródło danych** wybierz pozycję **Wyrażenie programu PowerShell**. Lista rozwijana **Źródło danych** zawiera różne źródła używane do wypełniania wartości parametrów.

   Możesz używać danych wyjściowych ze źródeł, takich jak inne działanie, element zawartości usługi Automation lub wyrażenie programu PowerShell. W tym przypadku dane wyjściowe to po prostu tekst *Witaj, świecie*. Możesz użyć wyrażenia programu PowerShell i wprowadzić ciąg.<br>

1. W polu **Wyrażenie** wpisz *„Witaj, świecie”* i kliknij dwukrotnie przycisk **OK**, aby wrócić do kanwy.
1. Zapisz element Runbook, klikając przycisk **Zapisz**.

## <a name="test-the-runbook"></a>Testowanie elementu runbook

Przed opublikowaniem elementu runbook w celu udostępnienia go w środowisku produkcyjnym warto go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementu Runbook polega na uruchomieniu jego **wersji roboczej** i interaktywnym przejrzeniu danych wyjściowych.

1. Wybierz **okienko testowania** aby otworzyć stronę testową.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.
1. Zostanie utworzone [zadanie elementu Runbook](automation-runbook-execution.md) i w okienku pojawi się jego stan.

   Początkowy stan zadania to *W kolejce*, który wskazuje, że trwa oczekiwanie na udostępnienie procesu roboczego elementu runbook w chmurze. Następnym stanem jest *Uruchamianie*, gdy proces roboczy pobiera zadanie, a następnie *Uruchomiono*, gdy element runbook faktycznie zacznie działać.

1. Po zakończeniu zadania elementu Runbook zostaną wyświetlone jego dane wyjściowe. W tym przypadku zostanie wyświetlony tekst *Witaj, świecie*.<br> ![Witaj, świecie](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Zamknij stronę Test, aby wrócić do kanwy.

## <a name="publish-and-start-the-runbook"></a>Publikowanie i uruchamianie elementu runbook

Nowo utworzony element runbook nadal działa w trybie roboczym. Aby można było go uruchomić w środowisku produkcyjnym, należy go opublikować. Podczas publikowania elementu Runbook można zastąpić istniejącą wersję opublikowaną wersją roboczą. W tym przypadku nie ma jeszcze wersji opublikowanej, ponieważ element runbook został dopiero utworzony.

1. Wybierz **Publikuj** opublikować elementu runbook i następnie **tak** po wyświetleniu monitu.
1. Jeśli przewiniesz w lewo, aby wyświetlić element runbook w programie **elementów Runbook** stronie pokazuje **stan pisania przyp** z **opublikowano**.
1. Przewiń w prawo, aby wyświetlić stronę **MyFirstRunbook-Graphical**.

   Opcje w górnej części umożliwiają nam uruchamianie elementu Runbook, planowanie jego uruchomienia w przyszłości lub utworzenie [elementu webhook](automation-webhooks.md) w celu umożliwienia uruchamiania za pośrednictwem wywołania HTTP.

1. Wybierz **Start** i następnie **tak** po wyświetleniu monitu o uruchomienie elementu runbook.
1. Strona zadania jest otwarty dla zadania elementu runbook, który został utworzony. Sprawdź, czy w polu **Stan zadania** jest widoczna informacja **Ukończono**.
1. Gdy stanem elementu Runbook będzie *Ukończono*, kliknij pozycję **Dane wyjściowe**. **Dane wyjściowe** zostanie otwarta strona i będzie widoczny *Witaj, świecie* w okienku.
1. Zamknij stronę danych wyjściowych.
1. Kliknij przycisk **wszystkie dzienniki** aby otworzyć stronę strumieni dla zadania elementu runbook. W strumieniu danych wyjściowych powinien być widoczny tylko ciąg *Witaj, świecie*, ale mogą zostać wyświetlone inne strumienie zadania elementu runbook, takie jak Pełne informacje i Błąd, jeśli element runbook wykonuje w nich operacje zapisywania.
1. Zamknij stronę wszystkie dzienniki i strona zadania, aby wrócić do strony MyFirstRunbook-Graphical.
1. Aby wyświetlić wszystkie zadania elementu runbook, Zamknij **zadania** strony i wybierz **zadania** w obszarze **zasobów**. Zawiera on listę wszystkich zadań utworzonych przez ten element runbook. Ponieważ uruchomiono zadanie tylko raz, powinno być widoczne tylko jedno zadanie.
1. Możesz kliknąć to zadanie, aby otworzyć okienko Zadania wyświetlone wcześniej po uruchomieniu elementu runbook. Dzięki temu możesz cofnąć się w czasie i wyświetlić szczegóły dowolnego zadania, które zostało utworzone dla określonego elementu Runbook.

## <a name="create-variable-assets"></a>Tworzenie zmiennych elementów zawartości

Element runbook został przetestowany i opublikowany, ale jak do tej pory nie wykonuje on żadnych użytecznych czynności. Powinien zarządzać zasobami platformy Azure. Przed skonfigurowaniem elementu runbook do uwierzytelniania należy utworzyć zmienną na potrzeby przechowywania identyfikatora subskrypcji i odwołać się do niej po skonfigurowaniu działania uwierzytelniania w poniższym kroku 6. Uwzględnienie odwołania do kontekstu subskrypcji ułatwia pracę z wieloma subskrypcjami. Przed kontynuowaniem skopiuj identyfikator subskrypcji z opcji Subskrypcje w okienku Nawigacja.

1. Na stronie konta usługi Automation wybierz **zmienne** w obszarze **zasoby UDOSTĘPNIONE**.
1. Wybierz **Dodaj zmienną**.
1. W nowej zmiennej strony w **nazwa** wprowadź **AzureSubscriptionId** i **wartość** wprowadź identyfikator subskrypcji. Zachowaj *ciąg* jako wartość pola **Typ** i wartość domyślną pola **Szyfrowanie**.
1. Kliknij pozycję **Utwórz**, aby utworzyć zmienną.

## <a name="add-authentication"></a>Dodawanie uwierzytelniania

Teraz masz zmienną służącą do przechowywania identyfikatora subskrypcji i możesz skonfigurować element runbook do uwierzytelniania przy użyciu poświadczeń Uruchom jako, które określono w części dotyczącej [wymagań wstępnych](#prerequisites). Można to zrobić, dodając połączenie Uruchom jako platformy Azure **zasobów** i **Connect-AzureRmAccount** polecenia cmdlet do kanwy.

1. Przejdź z powrotem do elementu runbook i wybierz **Edytuj** na stronie MyFirstRunbook-Graphical.
1. Nie ma potrzeby **Zapisz ciąg Hello World w danych wyjściowych** , więc kliknij wielokropek (...) i wybierz pozycję **Usuń**.
1. W kontrolce Biblioteka rozwiń pozycję **ZASOBY** i **Połączenia**, a następnie dodaj pozycję **AzureRunAsConnection** do kanwy, wybierając pozycję **Dodaj do kanwy**.
1. W kontrolce Biblioteka wpisz **Connect-AzureRmAccount** w polu tekstowym wyszukiwania.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** teraz jest aliasem dla **Connect-AzureRMAccount**. Podczas wyszukiwania biblioteki elementów, jeśli nie widzisz **Connect-AzureRMAccount**, możesz użyć **Add-AzureRmAccount**, lub na koncie usługi Automation można zaktualizować moduły.

1. Dodaj **Connect-AzureRmAccount** do kanwy.
1. Umieść kursor nad pozycją **Uzyskaj połączenie Uruchom jako** i zaczekaj, aż w dolnej części kształtu pojawi się okrąg. Kliknij okrąg i przeciągnij strzałkę do **Connect-AzureRmAccount**. Utworzona strzałka to *link*. Element runbook, który rozpoczyna się od **Uzyskaj połączenie Uruchom jako** , a następnie uruchom **Connect-AzureRmAccount**.<br> ![Tworzenie połączenia między działaniami](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. W obszarze roboczym wybierz **Connect-AzureRmAccount** w okienku kontrolki Konfiguracja wpisz **logowania do platformy Azure** w **etykiety** pole tekstowe.
1. Kliknij przycisk **parametry** i zostanie wyświetlona strona Konfiguracja parametru działania.
1. **Connect-AzureRmAccount** ma wiele zestawów parametrów, dlatego należy wybrać jeden z nich przed podaniem wartości parametrów. Kliknij pozycję **Zestaw parametrów**, a następnie wybierz zestaw parametrów **ServicePrincipalCertificate**.
1. Po wybraniu pozycji zestawu parametrów parametry zostaną wyświetlone na stronie Konfiguracja parametru działania. Kliknij pozycję **APPLICATIONID**.<br> ![Dodawanie parametrów konta usługi Azure RM](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Na stronie wartość parametru wybierz **wyjście działania** dla **źródła danych** i wybierz **Uzyskaj połączenie Uruchom jako** z listy w **ścieżka pola** typu pole tekstowe **ApplicationId**, a następnie kliknij przycisk **OK**. Określasz nazwę właściwości ścieżki pola, ponieważ wyjściem działania jest obiekt z wieloma właściwościami.
1. Kliknij przycisk **CERTIFICATETHUMBPRINT**, a na stronie wartość parametru wybierz **wyjście działania** dla **źródła danych**. Wybierz z listy pozycję **Uzyskaj połączenie Uruchom jako**, w polu tekstowym **Ścieżka pola** wpisz ciąg **CertificateThumbprint**, a następnie kliknij przycisk **OK**.
1. Kliknij przycisk **SERVICEPRINCIPAL**, a na stronie wartość parametru wybierz **ConstantValue** dla **źródła danych**, kliknij opcję **True**, a następnie kliknij przycisk **OK**.
1. Kliknij przycisk **TENANTID**, a na stronie wartość parametru wybierz **wyjście działania** dla **źródła danych**. Wybierz pozycję **Uzyskaj połączenie Uruchom jako** z listy, w polu tekstowym **Ścieżka pola** wpisz ciąg **TenantId**, a następnie dwukrotnie kliknij przycisk **OK**.
1. W kontrolce Biblioteka wpisz ciąg **Set-AzureRmContext** w polu tekstowym wyszukiwania.
1. Dodaj pozycję **Set-AzureRmContext** do kanwy.
1. W kanwie wybierz pozycję **Set-AzureRmContext** i w okienku kontrolki Konfiguracja wpisz ciąg **Określ identyfikator subskrypcji** w polu tekstowym **Etykieta**.
1. Kliknij przycisk **parametry** i zostanie wyświetlona strona Konfiguracja parametru działania.
1. Polecenie **Set-AzureRmContext** ma wiele zestawów parametrów, dlatego należy wybrać jeden z nich przed podaniem wartości parametrów. Kliknij pozycję **Zestaw parametrów**, a następnie wybierz zestaw parametrów **SubscriptionId**.
1. Po wybraniu pozycji zestawu parametrów parametry zostaną wyświetlone na stronie Konfiguracja parametru działania. Kliknij pozycję **SubscriptionID**.
1. Na stronie wartość parametru wybierz **zasób zmiennej** dla **źródła danych** i wybierz **AzureSubscriptionId** z listy, a następnie kliknij przycisk **OK** dwa razy.
1. Umieść kursor nad pozycją **Zaloguj się do platformy Azure** i zaczekaj, aż w dolnej części kształtu pojawi się okrąg. Kliknij okrąg i przeciągnij strzałkę do pozycji **Określ identyfikator subskrypcji**.

Element Runbook powinien na tym etapie wyglądać następująco: <br>![Konfiguracja uwierzytelniania elementu Runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="add-activity-to-start-a-vm"></a>Dodaj działanie uruchamiania maszyny Wirtualnej

Teraz dodasz działanie **Start-AzureRmVM** w celu uruchomienia maszyny wirtualnej. Możesz wybrać dowolną maszynę wirtualną w ramach subskrypcji Azure. Wybrana nazwa zostanie na razie umieszczona w kodzie polecenia cmdlet.

1. W kontrolce Biblioteka wpisz ciąg **Start-AzureRm** w polu tekstowym wyszukiwania.
2. Dodaj polecenie **Start-AzureRmVM** do kanwy, a następnie kliknij i przeciągnij go poniżej obszaru **Określ identyfikator subskrypcji**.
1. Umieść kursor nad pozycją **Określ identyfikator subskrypcji** i zaczekaj, aż w dolnej części kształtu pojawi się okrąg. Kliknij okrąg i przeciągnij strzałkę do pozycji **Start-AzureRmVM**.
1. Wybierz pozycję **Start-AzureRmVM**. Kliknij pozycję **Parametry**, a następnie pozycję **Zestaw parametrów**, aby wyświetlić zestawy dla pozycji **Start-AzureRmVM**. Wybierz zestaw parametrów **ResourceGroupNameParameterSetName**. Obok parametrów **ResourceGroupName** i **Name** znajdują się wykrzykniki. Oznacza to, że te parametry są wymagane. Oczekiwane wartości obu parametrów to ciągi.
1. Wybierz pozycję **Nazwa**. Wybierz pozycję **Wyrażenie programu PowerShell** jako **Źródło danych**, a następnie wpisz ujętą w cudzysłów nazwę maszyny wirtualnej, która zostanie uruchomiona przy użyciu tego elementu runbook. Kliknij przycisk **OK**.
1. Wybierz pozycję **ResourceGroupName**. Użyj pozycji **Wyrażenie programu PowerShell** jako **źródła danych** i wpisz nazwę grupy zasobów ujętą w podwójne cudzysłowy. Kliknij przycisk **OK**.
1. Kliknij pozycję Okienko testowania, aby umożliwić przetestowanie elementu runbook.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu sprawdź, czy maszyna wirtualna została uruchomiona.

Element Runbook powinien na tym etapie wyglądać następująco: <br>![Konfiguracja uwierzytelniania elementu Runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="add-additional-input-parameters"></a>Dodawanie dodatkowych parametrów wejściowych

Element runbook aktualnie umożliwia uruchomienie maszyny wirtualnej w grupie zasobów określonej w poleceniu cmdlet **Start-AzureRmVM**. Byłby jednak bardziej użyteczny, gdyby można było podać obydwie wartości podczas jego uruchamiania. Aby to umożliwić, dodasz teraz do elementu runbook parametry wejściowe.

1. Otwórz edytor graficzny, klikając **Edytuj** na **MyFirstRunbook-Graphical** okienka.
1. Wybierz **dane wejściowe i wyjściowe** i następnie **Dodaj dane wejściowe** aby otworzyć okienko parametru wejściowego elementu Runbook.
1. Wprowadź wartość *VMName* parametru **Name**. Zachowaj *ciąg* jak wartość pola **Typ**, ale zmień ustawienie pozycji **Obowiązkowy** na *Tak*. Kliknij przycisk **OK**.
1. Utwórz drugi obowiązkowy parametr wejściowy o nazwie *ResourceGroupName*, a następnie kliknij przycisk **OK**, aby zamknąć okienko **Wejście i wyjście**.<br> ![Parametry wejściowe elementu Runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Wybierz działanie **Start-AzureRmVM**, a następnie kliknij pozycję **Parametry**.
1. Zmień **źródło danych** dla pozycji **Name** na **Wejście elementu Runbook**, a następnie wybierz pozycję **VMName**.
1. Zmień **źródło danych** dla pozycji **ResourceGroupName** na **Wejście elementu Runbook**, a następnie wybierz pozycję **ResourceGroupName**.<br> ![Parametry polecenia Start-AzureVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Zapisz element Runbook, a następnie otwórz okienko testowania. Teraz możesz podać wartości dwóch zmiennych wejściowych, które zostaną użyte w teście.
1. Zamknij okienko testowania.
1. Kliknij pozycję **Opublikuj**, aby opublikować nową wersję elementu Runbook.
1. Zatrzymaj maszynę wirtualną uruchomioną w poprzednim kroku.
1. Kliknij pozycję **Uruchom**, aby uruchomić element Runbook. Wpisz wartości parametrów **VMName** i **ResourceGroupName** dla maszyny wirtualnej do uruchomienia.
1. Po zakończeniu działania elementu Runbook sprawdź, czy maszyna wirtualna została uruchomiona.

## <a name="create-a-conditional-link"></a>Tworzenie połączenia warunkowego

Teraz zmodyfikujesz element runbook w taki sposób, aby podejmował próbę uruchomienia maszyny wirtualnej tylko pod warunkiem, że nie została jeszcze uruchomiona. W tym celu do elementu runbook dodamy polecenie cmdlet **Get-AzureRmVM** służące do pobrania stanu poziomu wystąpienia maszyny wirtualnej. Następnie dodamy moduł kodu przepływu pracy programu PowerShell o nazwie **Pobieranie stanu** przy użyciu fragmentu kodu programu PowerShell, aby określić, czy stan maszyny wirtualnej wskazuje na jej uruchomienie, czy też na jej zatrzymanie. Połączenie warunkowe z modułu **Pobieranie stanu** będzie powodować uruchomienie polecenia **Start-AzureRmVM** tylko w sytuacji, gdy bieżący stan działania wskazuje na zatrzymanie. Na koniec za pomocą polecenia cmdlet programu PowerShell Write-Output utworzysz komunikat wyjściowy informujący o tym, czy maszyna wirtualna została pomyślnie uruchomiona.

1. Otwórz **MyFirstRunbook-Graphical** w edytorze graficznym.
1. Usuń połączenie między elementami **Określ identyfikator subskrypcji** i **Start -AzureRmVM**, klikając je i naciskając klawisz *Delete*.
1. W kontrolce Biblioteka wpisz ciąg **Get-AzureRm** w polu tekstowym wyszukiwania.
1. Dodaj pozycję **Get-AzureRmVM** do kanwy.
1. Wybierz polecenie **Get-AzureRmVM**, a następnie pozycję **Zestaw parametrów**, aby wyświetlić zestawy polecenia **Get-AzureRmVM**. Wybierz zestaw parametrów **GetVirtualMachineInResourceGroupNameParamSet**. Obok parametrów **ResourceGroupName** i **Name** znajdują się wykrzykniki. Oznacza to, że te parametry są wymagane. Oczekiwane wartości obu parametrów to ciągi.
1. W obszarze **Źródło danych** dla pozycji **Name** wybierz pozycję **Wejście elementu Runbook**, a następnie wybierz pozycję **VMName**. Kliknij przycisk **OK**.
1. W obszarze **Źródło danych** dla pozycji **ResourceGroupName** wybierz pozycję **Wejście elementu Runbook**, a następnie wybierz pozycję **ResourceGroupName**. Kliknij przycisk **OK**.
1. W obszarze **Źródło danych** dla pozycji **Stan** wybierz opcję **Stała wartość**, a następnie kliknij pozycję **Prawda**. Kliknij przycisk **OK**.
1. Utwórz połączenie pozycji **Określ identyfikator subskrypcji** z poleceniem **Get-AzureRmVM**.
1. W kontrolce biblioteki rozwiń pozycję **Kontrola elementu Runbook** i dodaj pozycję **Kod** do kanwy.  
1. Utwórz połączenie między poleceniem **Get-AzureRmVM** i elementem **Kod**.  
1. Kliknij pozycję **Kod** i w okienku Konfiguracja zmień etykietę na **Pobieranie stanu**.
1. Wybierz **kodu** parametru i **Edytor kodu** zostanie wyświetlona strona.  
1. W edytorze kodu wklej poniższy fragment kodu:

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Utwórz połączenie elementu **Pobieranie stanu** z poleceniem **Start-AzureRmVM**.<br> ![Element Runbook z modułem kodu](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Wybierz połączenie, a następnie w okienku Konfiguracja zmień ustawienie pozycji **Zastosuj warunek** na **Tak**. Zauważ, że połączenie zmieni się w linię przerywaną, co oznacza, że działanie docelowe będzie uruchamiane tylko w sytuacji, gdy warunek będzie spełniony.  
1. Dla **warunku wyrażenia** wpisz *$ActivityOutput [’Pobieranie stanu’] - eq "Zatrzymano"*. Polecenie **Start-AzureRmVM** będzie teraz uruchamiane tylko wtedy, gdy maszyna wirtualna jest zatrzymana.
1. W kontrolce Biblioteka rozwiń pozycję **Polecenia cmdlet**, a następnie pozycję **Microsoft.PowerShell.Utility**.
1. Dwukrotnie dodaj pozycję **Write-Output** do kanwy.
1. W pierwszej kontrolce **Write-Output** kliknij pozycję **Parametry** i zmień wartość pola **Etykieta** na *Powiadamianie o uruchomieniu maszyny wirtualnej*.
1. Dla pozycji **InputObject** zmień **źródło danych** na **Wyrażenie programu PowerShell** i wpisz wyrażenie *„Maszyna wirtualna $VMName została pomyślnie uruchomiona”*.
1. W drugiej kontrolce **Write-Output** kliknij pozycję **Parametry** i zmień wartość pola **Etykieta** na *Powiadamianie o błędzie uruchomienia maszyny wirtualnej*.
1. Dla pozycji **InputObject** zmień **źródło danych** na **Wyrażenie programu PowerShell** i wpisz wyrażenie *„Nie można uruchomić maszyny wirtualnej $VMName”*.
1. Utwórz połączenie między poleceniem **Start-AzureRmVM** z wartościami **Powiadamianie o uruchomieniu maszyny wirtualnej** i **Powiadamianie o nieudanym uruchomieniu maszyny wirtualnej**.
1. Wybierz połączenie z wartością **Powiadamianie o uruchomieniu maszyny wirtualnej** i zmień ustawienie pozycji **Zastosuj warunek** na **True**.
1. W polu **Warunek wyrażenia** wpisz ciąg *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*. Ta kontrolka Write-Output będzie teraz uruchamiana tylko w sytuacji, gdy maszyna wirtualna zostanie pomyślnie uruchomiona.
1. Wybierz połączenie z wartością **Powiadamianie o nieudanym uruchomieniu maszyny wirtualnej** i zmień ustawienie pozycji **Zastosuj warunek** na **True**.
1. W polu **Warunek wyrażenia** wpisz ciąg *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*. Ta kontrolka Write-Output będzie teraz uruchamiana tylko w sytuacji, gdy maszyna wirtualna nie zostanie pomyślnie uruchomiona. Element runbook powinien wyglądać podobnie do następującego: <br> ![Element Runbook z pozycją Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Zapisz element Runbook, a następnie otwórz okienko testowania.
1. Uruchom element Runbook przy zatrzymanej maszynie wirtualnej. Powinno to spowodować jej uruchomienie.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat tworzenia elementów graficznych, zobacz [Graphical authoring in Azure Automation](automation-graphical-authoring-intro.md) (Tworzenie elementów graficznych w usłudze Azure Automation).
* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz artykuł [My first PowerShell runbook](automation-first-runbook-textual-powershell.md) (Mój pierwszy element Runbook programu PowerShell).
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell).


