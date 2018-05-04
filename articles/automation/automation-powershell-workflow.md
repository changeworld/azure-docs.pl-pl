---
title: Learning przepływu pracy programu PowerShell dla usługi Automatyzacja Azure
description: Ten artykuł dotyczy jako szybki lekcji dla autorów doświadczenia w obsłudze programu PowerShell zrozumienie określonych różnic między środowiska PowerShell i przepływ pracy programu PowerShell i pojęcia mające zastosowanie do elementów runbook automatyzacji.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 173d151c2b86db621ee452e68b06baa709f86cdc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Nauka podstawowych pojęć przepływu pracy środowiska Windows PowerShell dla elementów runbook automatyzacji 
Elementy Runbook automatyzacji Azure są zaimplementowane jako przepływy pracy Windows PowerShell.  Przepływu pracy środowiska Windows PowerShell jest podobny do skryptu programu Windows PowerShell, ale ma pewne istotne różnice, które mogą być mylące dla nowego użytkownika.  Gdy ten artykuł dotyczy ułatwia pisanie elementów runbook za pomocą przepływu pracy programu PowerShell, zaleca się zapisu elementów runbook za pomocą programu PowerShell, chyba że potrzebne punkty kontrolne.  Istnieje kilka różnic składni podczas tworzenia elementów runbook przepływu pracy programu PowerShell i różnice te wymaga nieco więcej pracy pisanie przepływów pracy skuteczne.  

Przepływ pracy to sekwencja zaprogramowanych, połączonych kroków służących do wykonywania długotrwałych zadań lub do zapewnienia koordynacji wielu kroków na wielu urządzeniach lub zarządzanych węzłach. Zalety przepływu pracy zamiast zwykłego skryptu obejmują możliwość jednoczesnego wykonywania akcji na różnych urządzeniach oraz zdolność usuwania skutków błędów. Przepływu pracy środowiska Windows PowerShell to skrypt programu Windows PowerShell, który używa programu Windows Workflow Foundation. Przepływ pracy jest napisany za pomocą środowiska Windows PowerShell składni i uruchamiana przez środowisko Windows PowerShell, są przetwarzane przez program Windows Workflow Foundation.

Aby uzyskać szczegółowe informacje dotyczące tematów w tym artykule, zobacz [wprowadzenie do przepływu pracy środowiska Windows PowerShell](http://technet.microsoft.com/library/jj134242.aspx).

## <a name="basic-structure-of-a-workflow"></a>Podstawowa struktura przepływu pracy
Pierwszy krok w celu konwertowania skrypt programu PowerShell w przepływie pracy programu PowerShell jest otaczającej go przy użyciu **przepływu pracy** — słowo kluczowe.  Przepływ pracy, który rozpoczyna się od **przepływu pracy** — słowo kluczowe następuje treść skryptu ujęta w nawiasy klamrowe. Następuje nazwa przepływu pracy **przepływu pracy** — słowo kluczowe, jak pokazano w następującej składni:

    Workflow Test-Workflow
    {
       <Commands>
    }

Nazwa przepływu pracy musi odpowiadać nazwie elementu runbook automatyzacji. Jeśli element runbook jest importowany, nazwa pliku musi odpowiadać nazwie przepływu pracy i musi kończyć się *ps1*.

Aby dodać parametry do przepływu pracy, użyj **Param** — słowo kluczowe tak jak do skryptu.

## <a name="code-changes"></a>Zmiany kodu
Kod przepływu pracy programu PowerShell wygląda niemal identyczny kod skryptu programu PowerShell z wyjątkiem kilku znaczących zmian.  W poniższych sekcjach opisano zmiany, które należy do skryptu programu PowerShell na jej uruchamianie w przepływie pracy.

### <a name="activities"></a>Działania
Działanie jest konkretnym zadaniem w przepływie pracy. Tak jak skrypt składa się z co najmniej jedno polecenie, przepływ pracy składa się z jednego lub kilku działań wykonywanych w określonej kolejności. Windows PowerShell Workflow automatycznie konwertuje wiele poleceń cmdlet programu Windows PowerShell do działań po uruchomieniu przepływu pracy. Po określeniu jednej z tych poleceń cmdlet w elemencie runbook odpowiadające mu działanie jest uruchamiane przez program Windows Workflow Foundation. W przypadku poleceń cmdlet bez odpowiadającego im działania, Windows PowerShell Workflow automatycznie uruchamia polecenie cmdlet w [InlineScript](#inlinescript) działania. Istnieje zestaw poleceń cmdlet, które są wyłączone i nie można go używać w przepływie pracy, chyba że zostaną jawnie umieszczone w bloku InlineScript. Aby uzyskać więcej szczegółowych informacji dotyczących tych pojęć, zobacz [używanie działań w skryptowych przepływach pracy](http://technet.microsoft.com/library/jj574194.aspx).

Działania przepływów pracy dzielą zestaw wspólnych parametrów konfigurujących ich pracę. Aby uzyskać więcej informacji o typowych parametrach przepływu pracy, zobacz [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Parametry pozycyjne
Nie można używać parametrów pozycyjnych działań i poleceń cmdlet w przepływie pracy.  Oznacza to jest, że muszą używać nazwy parametrów.

Rozważmy na przykład następujący kod, który pobiera wszystkie uruchomione usługi.

     Get-Service | Where-Object {$_.Status -eq "Running"}

Jeśli zostanie podjęta próba uruchomienia tego samego kodu w przepływie pracy, pojawi się komunikat informujący o tym, jak "nie można rozpoznać ustawionego parametru za pomocą podanych nazwanych parametrów."  Aby rozwiązać ten problem, należy podać nazwę parametru w następującej.

    Workflow Get-RunningServices
    {
        Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
    }

### <a name="deserialized-objects"></a>Zdeserializowana obiektów
Obiekty w przepływach pracy są deserializacji.  Oznacza to, że ich właściwości są nadal dostępne, ale nie ich metod.  Rozważmy na przykład następujący kod programu PowerShell, który zatrzymuje usługę przy użyciu metody Stop obiektu usługi.

    $Service = Get-Service -Name MyService
    $Service.Stop()

Jeśli zostanie podjęta próba uruchomienia tego w przepływie pracy, wystąpi błąd informujący o tym "wywołanie metody nie jest obsługiwane w przepływie pracy Windows PowerShell."  

Jedną z opcji jest opakowywać tych dwóch wierszy kodu w [InlineScript](#inlinescript) blok w takim przypadku $Service byłoby obiektu usługi, w bloku.

    Workflow Stop-Service
    {
        InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
        }
    }

Innym rozwiązaniem jest użyć innego polecenia cmdlet, który wykonuje te same funkcje co metodę, jeśli jest dostępny.  W naszym przykładzie polecenia cmdlet Stop-Service zapewnia te same funkcje co Metoda Stop, a można użyć następującego przepływu pracy.

    Workflow Stop-MyService
    {
        $Service = Get-Service -Name MyService
        Stop-Service -Name $Service.Name
    }


## <a name="inlinescript"></a>InlineScript
**InlineScript** działanie jest przydatne, gdy należy uruchomić co najmniej jedno polecenie jako tradycyjne skrypt programu PowerShell zamiast przepływu pracy programu PowerShell.  Podczas gdy polecenia w przepływie pracy są wysyłane do programu Windows Workflow Foundation do przetwarzania, polecenia w bloku InlineScript są przetwarzane przez środowisko Windows PowerShell.

Blok InlineScript wykorzystuje podaną niżej składnię następujące.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

Zwraca z danych wyjściowych z InlineScript, przypisując dane wyjściowe do zmiennej. Poniższy przykład zatrzymuje usługę, a następnie wyświetla nazwę usługi.

    Workflow Stop-MyService
    {
        $Output = InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Można jednak przekazać wartości w bloku InlineScript, ale muszą używać **$Using** modyfikator zakresu.  Poniższy przykład jest taki sam jak w poprzednim przykładzie, ale podano nazwę usługi za pomocą zmiennej.

    Workflow Stop-MyService
    {
        $ServiceName = "MyService"

        $Output = InlineScript {
            $Service = Get-Service -Name $Using:ServiceName
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Podczas działania InlineScript mogą okazać się niezbędne w niektórych przepływach pracy, nie obsługują konstrukcje przepływu pracy i należy używać tylko w razie konieczności z następujących powodów:

* Nie można użyć [punktów kontrolnych](#checkpoints) wewnątrz bloku InlineScript. Jeśli wewnątrz bloku wystąpi błąd, musi zostać wznowiony od początku bloku.
* Nie można użyć [równoległe wykonywanie](#parallel-processing) wewnątrz InlineScriptBlock.
* InlineScript ma wpływ na skalowalność przepływu pracy, ponieważ utrzymuje sesję środowiska Windows PowerShell na całej długości tego bloku.

Aby uzyskać więcej informacji na temat używania InlineScript, zobacz [uruchamianie poleceń programu Windows PowerShell w przepływie pracy](http://technet.microsoft.com/library/jj574197.aspx) i [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Przetwarzanie równoległe
Jedną z zalet przepływów pracy programu Windows PowerShell jest możliwość wykonywania zestaw poleceń równolegle, a nie po kolei zgodnie z typowym skrypcie.

Można użyć **równoległych** — słowo kluczowe do utworzenia bloku skryptu z wieloma poleceniami, które są uruchamiane jednocześnie. Używa następującej składni, pokazano poniżej. W tym przypadku działania Activity1 i Activity2 zostanie uruchomiony w tym samym czasie. Activity3 rozpoczyna się dopiero po zakończeniu zarówno działania Activity1 i Activity2.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


Rozważmy na przykład następujących poleceń programu PowerShell, które skopiować wielu plików do lokalizacji docelowej sieci.  Te polecenia są uruchamiane po kolei, że jeden plik musi zakończyć się kopiowanie przed uruchomieniem następnego.     

    Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
    Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
    Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

Poniższy przepływ pracy wykonuje te same polecenia równolegle, dzięki czemu wszystkie rozpoczęciu kopiowania w tym samym czasie.  Tylko wtedy, gdy są kopiowane komunikat o zakończeniu wyświetleniem.

    Workflow Copy-Files
    {
        Parallel
        {
            Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
            Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
            Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
        }

        Write-Output "Files copied."
    }


Można użyć **ForEach-Parallel** konstrukcja do przetwarzania poleceń dla każdego elementu w kolekcji jednocześnie. Elementy w kolekcji są przetwarzane równolegle, podczas gdy polecenia w bloku skryptu uruchamiają się sekwencyjnie. Używa następującej składni, pokazano poniżej. W tym przypadku działania Activity1 zostanie uruchomiony w tym samym czasie dla wszystkich elementów w kolekcji. Dla każdego elementu Activity2 zostanie uruchomiony po zakończeniu działania Activity1. Activity3 rozpoczyna się dopiero po zakończeniu zarówno działania Activity1 i Activity2 dla wszystkich elementów.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

Poniższy przykład jest podobny do poprzedniego przykładu kopiowanie plików równolegle.  W takim przypadku dla każdego pliku zostanie wyświetlony komunikat, po skopiowaniu.  Tylko wtedy, gdy wszystkie zostaną całkowicie skopiowane jest zakończenia wyświetlony komunikat.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach -Parallel ($File in $Files)
        {
            Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
        }

        Write-Output "All files copied."
    }

> [!NOTE]
> Nie zaleca się działania równoległe podrzędnych elementów runbook, ponieważ to wykazało niewiarygodne wyniki.  Dane wyjściowe z podrzędnego elementu runbook czasami nie jest wyświetlany, a ustawienia w jeden element podrzędny element runbook może mieć wpływ na inne równoległych podrzędnych elementów runbook
>

## <a name="checkpoints"></a>Punkty kontrolne
A *punktu kontrolnego* jest migawką bieżącego stanu przepływu pracy, który zawiera bieżące wartości zmiennych i wszelkie dane wyjściowe wygenerowane do tego punktu. Jeśli przepływ pracy kończy się na błąd lub jest wstrzymana, następnie przy następnym uruchomieniu będzie uruchamiany z ostatniego punktu kontrolnego zamiast uruchamiania przepływu pracy.  Punkt kontrolny można ustawić w przepływie pracy z **Checkpoint-Workflow** działania.

W poniższym przykładowym kodzie wyjątek wystąpi po działaniu Activity2 powoduje przepływu pracy zakończyć. Gdy przepływ pracy jest uruchamiana ponownie, rozpoczyna się od działania Activity2, ponieważ przypadało ono zaraz po ostatnim ustawionym punkcie kontrolnym.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

Należy ustawić punkty kontrolne w przepływie pracy po działań, które mogą być podatne na wyjątek i nie powinien być powtarzany, jeśli przepływ pracy zostanie wznowione. Przepływ pracy może na przykład utworzyć maszynę wirtualną. Punkt kontrolny można ustawić przed i po poleceń, aby utworzyć maszynę wirtualną. Jeśli tworzenie zakończy się niepowodzeniem, polecenia będzie powtarzany Jeśli przepływ pracy zostanie uruchomiona ponownie. Jeśli przepływ pracy zakończy się niepowodzeniem, po utworzenie zakończy się powodzeniem, następnie maszyna wirtualna zostanie nie można utworzyć ponownie po wznowieniu przepływu pracy.

W poniższym przykładzie kopiuje pliki do lokalizacji sieciowej i ustawia punkt kontrolny po każdym pliku.  W przypadku utraty lokalizacji sieciowej przepływu pracy kończy się za błąd.  Gdy jest ona uruchamiana ponownie, zostanie wznowiona ostatniego punktu kontrolnego, co oznacza, że tylko pliki, które zostały już skopiowane są pomijane.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach ($File in $Files)
        {
            Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
            Checkpoint-Workflow
        }

        Write-Output "All files copied."
    }

Ponieważ nazwa użytkownika poświadczeń nie są zachowywane po wywołaniu metody [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) działania lub od ostatniego punktu kontrolnego, należy ustawić poświadczenia, które mają wartość null, a następnie pobrać je ponownie z magazynu trwałego po **Suspend-Workflow** lub nosi nazwę punktu kontrolnego.  W przeciwnym razie może pojawić się następujący komunikat o błędzie: *nie można wznowić zadanie przepływu pracy, albo ponieważ trwałości danych nie można zapisać całkowicie, lub zapisać trwałości danych została uszkodzona. Należy ponownie uruchomić przepływ pracy.*

Poniższy kod tego samego demonstruje sposób obsługi to w elementach runbook przepływu pracy programu PowerShell.

    workflow CreateTestVms
    {
       $Cred = Get-AzureAutomationCredential -Name "MyCredential"
       $null = Connect-AzureRmAccount -Credential $Cred

       $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

       foreach ($VmName in $VmsToCreate)
         {
          # Do work first to create the VM (code not shown)

          # Now add the VM
          New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

          # Checkpoint so that VM creation is not repeated if workflow suspends
          $Cred = $null
          Checkpoint-Workflow
          $Cred = Get-AzureAutomationCredential -Name "MyCredential"
          $null = Connect-AzureRmAccount -Credential $Cred
         }
     }


Nie jest to wymagane, jeśli są uwierzytelniani za pomocą konta Uruchom jako skonfigurowaną nazwę główną usługi.  

Aby uzyskać więcej informacji na temat punktów kontrolnych, zobacz [Dodawanie punktów kontrolnych do skryptowego przepływu pracy](http://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Kolejne kroki
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell).
