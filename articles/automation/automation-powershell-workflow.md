---
title: Uczenie się przepływu pracy programu PowerShell dla usługi Azure Automation
description: Ten artykuł ma jako szybkie lekcji dla autorów znanych przy użyciu programu PowerShell, aby poznać konkretne różnice między programu PowerShell i przepływie pracy programu PowerShell i pojęcia dotyczące elementów runbook usługi Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: WenJason
ms.author: v-jay
origin.date: 12/14/2018
ms.date: 04/01/2019
ms.topic: conceptual
manager: digimobile
ms.openlocfilehash: c5764c36a646b9639c0eb6463c39b9f014c4272d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60738336"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Nauka podstawowych pojęć przepływu pracy środowiska Windows PowerShell dla elementów runbook usługi Automation

Elementy Runbook w usłudze Azure Automation są zaimplementowane jako przepływy pracy programu Windows PowerShell.  Przepływ pracy Windows PowerShell jest podobny do skryptu programu Windows PowerShell, ale ma pewne istotne różnice, które mogą być mylące dla nowego użytkownika.  Chociaż ten artykuł ma na celu pomóc, dzięki czemu można tworzyć elementy runbook przy użyciu przepływu pracy programu PowerShell, zaleca się zapisu przy użyciu programu PowerShell, chyba że potrzebujesz punkty kontrolne w elementach runbook.  Istnieje kilka różnic składni, podczas tworzenia elementów runbook przepływu pracy programu PowerShell, a te różnice wymaga nieco więcej pracy pisanie przepływów pracy skuteczne.

Przepływ pracy to sekwencja zaprogramowanych, połączonych czynności, które wykonywania długotrwałych zadań lub do zapewnienia koordynacji wielu czynności należące do wielu urządzeń lub węzłach zarządzanych. Korzyści wynikające z przepływu pracy zamiast zwykłego skryptu obejmują możliwość jednoczesnego wykonywania akcji na wielu urządzeniach i możliwość automatycznego odzyskiwania po awarii. Przepływ pracy Windows PowerShell to skrypt środowiska Windows PowerShell, który używa programu Windows Workflow Foundation. Gdy przepływ pracy jest napisane przy użyciu składni programu Windows PowerShell, a następnie uruchomić program Windows PowerShell, jest on przetwarzany przez program Windows Workflow Foundation.

Aby uzyskać szczegółowe informacje dotyczące tematów, w tym artykule, zobacz [rozpoczęcie korzystania z przepływu pracy środowiska Windows PowerShell](https://technet.microsoft.com/library/jj134242.aspx).

## <a name="basic-structure-of-a-workflow"></a>Podstawowa struktura przepływu pracy

Pierwszym krokiem do konwertowania skrypt programu PowerShell do przepływu pracy programu PowerShell jest otaczający ją za pomocą **przepływu pracy** — słowo kluczowe.  Przepływ pracy zaczyna się od **przepływu pracy** — słowo kluczowe następuje treść skryptu ujęte w nawiasy klamrowe. Następuje nazwa przepływu pracy **przepływu pracy** — słowo kluczowe, jak pokazano na następującej składni:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Nazwa przepływu pracy musi odpowiadać nazwie elementu runbook usługi Automation. Jeśli element runbook jest importowany, a następnie nazwa pliku musi odpowiadać nazwie przepływu pracy i kończyć się *.ps1*.

Aby dodać parametry do przepływu pracy, użyj **Param** — słowo kluczowe tak jak do skryptu.

## <a name="code-changes"></a>Zmiany kodu

Kod przepływu pracy programu PowerShell wygląda niemal identyczny kod skryptu programu PowerShell, z wyjątkiem kilku znaczących zmian.  W poniższych sekcjach opisano zmiany, które należy wprowadzić do skryptu programu PowerShell dla niego do działania w przepływie pracy.

### <a name="activities"></a>Działania

Działanie jest konkretnym zadaniem w przepływie pracy. Tak samo, jak skrypt składa się z co najmniej jedno polecenie, przepływ pracy składa się z jednego lub więcej działań, które są wykonywane w sekwencji. Windows PowerShell Workflow automatycznie konwertuje wiele poleceń cmdlet programu Windows PowerShell do działania po uruchomieniu przepływu pracy. Po określeniu jednej z tych poleceń cmdlet w elemencie runbook odpowiadające mu działanie jest uruchamiane przez program Windows Workflow Foundation. Dla poleceń cmdlet, które nie mają odpowiadającego im działania, Windows PowerShell Workflow automatycznie uruchamia polecenie cmdlet w ramach [InlineScript](#inlinescript) działania. Istnieje zestaw poleceń cmdlet, które są wyłączone i nie można używać w przepływie pracy, chyba że zostaną jawnie umieszczone w bloku InlineScript. Aby uzyskać więcej szczegółowych informacji dotyczących tych pojęć, zobacz [używanie działań w skryptowych przepływach pracy](https://technet.microsoft.com/library/jj574194.aspx).

Działania przepływów pracy dzielą zestaw wspólnych parametrów konfigurujących ich pracę. Aby uzyskać szczegółowe informacje o typowych parametrach przepływu pracy, zobacz [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Parametry pozycyjne

Parametry pozycyjne nie można użyć z działaniami i poleceń cmdlet w przepływie pracy.  Oznacza to to, czy należy użyć nazwy parametru.

Na przykład rozważmy następujący kod, który pobiera wszystkie uruchomione usługi.

```powershell
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Jeśli spróbujesz uruchomić ten sam kod w przepływie pracy, pojawi się komunikat informujący o tym, jak "zestaw parametrów nie można rozwiązać za pomocą podanych nazwanych parametrów."  Aby rozwiązać ten problem, należy podać nazwę parametru, tak jak poniżej.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Po deserializacji obiektów

Obiekty w przepływach pracy są deserializacji.  Oznacza to, że ich właściwości są nadal dostępne, ale nie ich metod.  Na przykład rozważmy następujący kod programu PowerShell, który zatrzymuje usługę przy użyciu metody Stop obiektu usługi.

```powershell
$Service = Get-Service -Name MyService
$Service.Stop()
```

Jeśli zostanie podjęta próba uruchomienia tego przepływu pracy, pojawi się błąd informujący o "wywołanie metody nie jest obsługiwana w przepływie pracy Windows PowerShell".

Jedną z opcji jest opakowanie te dwa wiersze kodu w [InlineScript](#inlinescript) bloku, w którym to przypadku $Service będzie obiektem usługi, w bloku.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Innym rozwiązaniem jest użyć innego polecenia cmdlet, który wykonuje taką samą funkcjonalność jak metoda, jeśli jest dostępny.  W naszym przykładzie polecenie cmdlet Stop-Service udostępnia taką samą funkcjonalność jak Metoda Stop. Ponadto można użyć następującego przepływu pracy.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

**InlineScript** działanie jest przydatne w przypadku, gdy musisz uruchomić jedno lub kilka poleceń jako tradycyjne skrypt programu PowerShell, a nie przepływu pracy programu PowerShell.  Podczas gdy polecenia w przepływie pracy są wysyłane do Windows Workflow Foundation w celu przetwarzania, polecenia w bloku InlineScript są przetwarzane przez program Windows PowerShell.

Blok InlineScript wykorzystuje podaną niżej składnię następujące.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Dane wyjściowe mogą zwracać z InlineScript, przypisując dane wyjściowe do zmiennej. Poniższy przykład zatrzymuje usługę i następnie generuje nazwę usługi.

```powershell
Workflow Stop-MyService
{
    $Output = InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Można przekazać wartości w bloku InlineScript, ale muszą używać **$Using** modyfikator zakresu.  Poniższy przykład jest identyczny z poprzednim, z tą różnicą, że nazwa usługi znajduje się za pomocą zmiennej.

```powershell
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
```

Podczas działania InlineScript mogą okazać się niezbędne w niektórych przepływów pracy, nie obsługuje konstrukcje przepływu pracy i należy używać tylko w razie z następujących powodów:

* Nie można użyć [punktów kontrolnych](#checkpoints) wewnątrz bloku InlineScript. Jeśli wystąpi awaria w bloku, musi zostać wznowiony od początku bloku.
* Nie można użyć [równoległym](#parallel-processing) wewnątrz InlineScriptBlock.
* InlineScript ma wpływ na skalowalność przepływu pracy, ponieważ utrzymuje sesję środowiska Windows PowerShell na całej długości tego bloku.

Aby uzyskać więcej informacji na temat korzystania z InlineScript, zobacz [uruchamianie poleceń programu Windows PowerShell w przepływie pracy](https://technet.microsoft.com/library/jj574197.aspx) i [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Przetwarzanie równoległe

Jedną z zalet przepływów pracy programu Windows PowerShell jest możliwość wykonywania zestawu poleceń równolegle, a nie po kolei podobnie jak w typowym skrypcie.

Możesz użyć **równoległe** — słowo kluczowe do utworzenia bloku skryptu z wieloma poleceniami, które uruchamiać jednocześnie. Ta metoda korzysta z pokazanej poniżej składni następujące. W tym przypadku działania Activity1 i Activity2 rozpoczyna się w tym samym czasie. Działanie Activity3 zostanie uruchomiony tylko wtedy, gdy działania Activity1 i Activity2 została ukończona.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Na przykład należy wziąć pod uwagę następujących poleceń programu PowerShell, które skopiować wielu plików do lokalizacji docelowej sieci.  Te polecenia są uruchamiane po kolei, że jeden plik musi zakończyć kopiowanie przed rozpoczęciem następnego.

```powershell
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

Poniższy przepływ pracy wykonuje tych tych samych poleceń równolegle, dzięki czemu wszystkie one Rozpocznij kopiowanie w tym samym czasie.  Tylko wtedy, gdy są one wszystkie kopiowane jest komunikat o zakończeniu wyświetlany.

```powershell
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
```

Możesz użyć **ForEach-równoległe** konstrukcji do przetwarzania poleceń dla każdego elementu w kolekcji jednocześnie. Elementy w kolekcji są przetwarzane równolegle, podczas gdy polecenia w bloku skryptu uruchamiają się sekwencyjnie. Ta metoda korzysta z pokazanej poniżej składni następujące. W tym przypadku działania Activity1 rozpoczyna się na tym samym czasie dla wszystkich elementów w kolekcji. Dla każdego elementu Activity2 rozpoczyna się po zakończeniu działania Activity1. Działanie Activity3 rozpoczyna się dopiero po działania Activity1 i Activity2 dla wszystkich elementów. Używamy `ThrottleLimit` parametru, aby ograniczyć równoległe. Zbyt wysoki `ThrottleLimit` może powodować problemy. Idealna wartość dla `ThrottleLimit` parametru zależy od wielu czynników w danym środowisku. Należy spróbować rozpoczyna się od niskiej wartości i spróbuj różnych wartości rosnącej, dopóki nie znajdziesz taki, który działa na swoje szczególne okoliczności.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Poniższy przykład jest podobny do poprzedniego przykładu kopiowania plików równolegle.  W takim przypadku zostanie wyświetlony komunikat dla każdego pliku po skopiowaniu.  Tylko wtedy, gdy wszystkie zostaną całkowicie skopiowane jest zakończenia wyświetlony komunikat.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel -ThrottleLimit 10 ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> Firma Microsoft nie zaleca się uruchamianych równolegle podrzędnych elementów runbook, ponieważ to wykazało, że niewiarygodne wyniki. Dane wyjściowe z podrzędnego elementu runbook czasami nie są wyświetlane i ustawienia w jeden element podrzędny element runbook może mieć wpływ na inne równoległe podrzędnych elementów runbook. Zmienne, takie jak $VerbosePreference, $WarningPreference i inne osoby nie mogą propagowane do podrzędnych elementów runbook. A jeśli podrzędny element runbook zmieni się te wartości, ich mogą nie zostać prawidłowo przywrócone po wywołania.

## <a name="checkpoints"></a>Punkty kontrolne

A *punktu kontrolnego* jest migawką bieżącego stanu przepływu pracy, który zawiera bieżące wartości zmiennych i wszelkie dane wyjściowe wygenerowane do tego punktu. Jeśli przepływ pracy nazwą kończącą się błąd lub jest wstrzymana, następnie przy następnym uruchomieniu rozpocznie od jej ostatniego punktu kontrolnego, zamiast początku przepływu pracy.  Punkt kontrolny można ustawić w przepływie pracy za pomocą **Checkpoint-Workflow** działania. Usługa Azure Automation korzystają z funkcji [udział](automation-runbook-execution.md#fair-share), gdzie każdego elementu runbook, który działa przez 3 godziny zwalnianie umożliwia uruchomienie innych elementów runbook. Po pewnym czasie zwolnione element runbook zostanie załadowana ponownie, a gdy jest to, wykonanie od ostatniego punktu kontrolnego w elemencie runbook zostanie wznowione. W celu zagwarantowania, że element runbook zostanie ostatecznie zakończona, należy dodać punkty kontrolne w odstępach czasu, działające przez mniej niż 3 godziny. Jeśli podczas każdego uruchomienia jest dodawany nowy punkt kontrolny, a jeśli element runbook zostanie usunięty po 3 godziny z powodu błędu elementu runbook zostanie wznowione na czas nieokreślony.

W poniższym przykładowym kodzie wyjątek wystąpi po działaniu Activity2 powoduje zakończenia przepływu pracy. Gdy przepływ pracy zostanie uruchomiony ponownie, rozpoczyna się przez uruchomienie Activity2, ponieważ przypadało ono zaraz po, ustaw ostatniego punktu kontrolnego.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Po działań, które mogą być podatne na wyjątek, a nie powinien być powtarzany, jeśli przepływ pracy zostanie wznowione, należy ustawić punkty kontrolne w przepływie pracy. Przepływ pracy może na przykład, utwórz maszynę wirtualną. Punkt kontrolny można ustawić przed i po poleceniach służących do tworzenia maszyny wirtualnej. Jeśli tworzenie zakończy się niepowodzeniem, polecenia będzie powtarzany Jeśli przepływ pracy zostanie uruchomiony ponownie. Jeśli przepływ pracy zakończy się niepowodzeniem, po pomyślnym utworzeniu, następnie maszyna wirtualna nie zostaną utworzone ponownie po wznowieniu przepływu pracy.

Poniższy przykład kopiuje pliki do lokalizacji sieciowej i ustawia punkt kontrolny po każdym pliku.  Jeśli lokalizacja sieciowa zostaną utracone, przepływ pracy kończy się błąd.  Gdy jest ona uruchamiana ponownie, zostanie wznowione na ostatni punkt kontrolny, co oznacza, że tylko te pliki, które zostały już skopiowane zostaną pominięte.

```powershell
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
```

Ponieważ poświadczeń username nie są zachowywane po wywołaniu metody [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) działania lub od ostatniego punktu kontrolnego, należy ustawić poświadczenia, które mają wartość null, a następnie pobrać je ponownie z magazynu trwałego po  **Suspend-Workflow** lub punktu kontrolnego jest wywoływana.  W przeciwnym razie może zostać wyświetlony następujący komunikat o błędzie: *Nie można wznowić zadanie przepływu pracy, albo ponieważ trwałości danych może nie być w pełni zapisane lub zapisany stan trwały danych została uszkodzona. Należy ponownie uruchomić przepływ pracy.*

Następujące ten sam kod pokazuje, jak obsługiwać to w elementach runbook przepływu pracy programu PowerShell.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzureAutomationCredential -Name "MyCredential"
    $null = Connect-AzureRmAccount -Credential $Cred

    $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzureRmVm -VM $Vm -Location "ChinaNorth" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzureAutomationCredential -Name "MyCredential"
        $null = Connect-AzureRmAccount -EnvironmentName AzureChinaCloud -Credential $Cred
        }
}
```

> [!IMPORTANT]
> **Add-AzureRmAccount** teraz jest aliasem dla **Connect-AzureRMAccount**. Podczas wyszukiwania biblioteki elementów, jeśli nie widzisz **Connect-AzureRMAccount**, możesz użyć **Add-AzureRmAccount**, lub na koncie usługi Automation można zaktualizować moduły.

Nie jest to wymagane, jeśli używasz uwierzytelniania przy użyciu konta Uruchom jako skonfigurowane przy użyciu jednostki usługi.

Aby uzyskać więcej informacji na temat punktów kontrolnych, zobacz [Dodawanie punktów kontrolnych do skryptowego przepływu pracy](https://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Kolejne kroki

* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell).

