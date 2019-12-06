---
title: Tworzenie graficzne w Azure Automation
description: Tworzenie graficzne pozwala tworzyć elementy Runbook dla Azure Automation bez pracy z kodem. Ten artykuł zawiera wprowadzenie do tworzenia graficznego oraz wszystkie szczegóły, które trzeba wykonać, aby rozpocząć tworzenie graficznego elementu Runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 82a06510bd9d1e0de2b38260773cb4848156bf12
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850299"
---
# <a name="graphical-authoring-in-azure-automation"></a>Tworzenie graficzne w Azure Automation

Tworzenie graficzne pozwala tworzyć elementy Runbook dla Azure Automation bez złożoności podstawowego kodu przepływu pracy programu Windows PowerShell lub programu PowerShell. Możesz dodać działania do kanwy z biblioteki poleceń cmdlet i elementów Runbook, połączyć je ze sobą i skonfigurować w celu utworzenia przepływu pracy. Jeśli kiedykolwiek pracujesz z programem System Center Orchestrator lub Service Management Automation (SMA), powinien wyglądać znajomo.

Ten artykuł zawiera wprowadzenie do tworzenia graficznego i pojęć potrzebnych do tworzenia graficznego elementu Runbook.

## <a name="graphical-runbooks"></a>Graficzne elementy Runbook

Wszystkie elementy Runbook w Azure Automation są przepływami pracy programu Windows PowerShell. Graficzne i graficzne elementy Runbook przepływu pracy programu PowerShell generują kod programu PowerShell, który jest uruchamiany przez pracowników automatyzacji, ale nie można go wyświetlać ani bezpośrednio modyfikować. Graficzny element Runbook można przekonwertować na graficzny element Runbook przepływu pracy programu PowerShell i na odwrót, ale nie można go przekonwertować na tekstowy element Runbook. Istniejący tekstowy element Runbook nie może zostać zaimportowany do edytora graficznego.

## <a name="overview-of-graphical-editor"></a>Przegląd edytora graficznego

Możesz otworzyć Edytor graficzny w Azure Portal, tworząc lub edytując graficzny element Runbook.

![Graficzny obszar roboczy](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

W poniższych sekcjach opisano kontrolki w edytorze graficznym.

### <a name="canvas"></a>Kanwa

Na kanwie znajduje się projekt elementu Runbook. Należy dodać działania z węzłów w kontrolce biblioteki do elementu Runbook i połączyć je z linkami, aby zdefiniować logikę elementu Runbook.

Możesz użyć kontrolek w dolnej części kanwy, aby powiększyć i pomniejszyć.

### <a name="library-control"></a>Kontrolka biblioteki

Kontrolka Biblioteka służy do wybierania [działań](#activities) , które mają zostać dodane do elementu Runbook. Należy je dodać do kanwy, w której zostaną połączone z innymi działaniami. Zawiera cztery sekcje opisane w poniższej tabeli:

| Sekcja | Opis |
|:--- |:--- |
| Polecenia cmdlet |Zawiera wszystkie polecenia cmdlet, które mogą być używane w elemencie Runbook. Polecenia cmdlet są zorganizowane według modułu. Dostępne są wszystkie moduły zainstalowane na koncie usługi Automation. |
| Elementy Runbook |Zawiera elementy Runbook na koncie usługi Automation. Te elementy Runbook można dodać do kanwy, aby można było ich używać jako podrzędnych elementów Runbook. Wyświetlane są tylko elementy Runbook tego samego typu podstawowego co edytowany element Runbook; w przypadku graficznych elementów Runbook są wyświetlane tylko elementy Runbook oparte na programie PowerShell, natomiast w przypadku graficznych elementów Runbook przepływu pracy programu PowerShell są wyświetlane tylko elementy Runbook oparte na przepływie pracy. |
| Elementy zawartości |Obejmuje [zasoby usługi Automation](/previous-versions/azure/dn939988(v=azure.100)) na koncie usługi Automation, które mogą być używane w elemencie Runbook. Dodanie elementu zawartości do elementu Runbook powoduje dodanie działania przepływu pracy, które pobiera wybrany zasób. W przypadku zmiennych zasobów można wybrać, czy dodać działanie, aby uzyskać zmienną, czy ustawić zmienną. |
| Kontrola elementu Runbook |Zawiera działania kontroli elementu Runbook, które mogą być używane w bieżącym elemencie Runbook. *Połączenie* pobiera wiele danych wejściowych i czeka, aż wszystkie ukończone przed kontynuowaniem przepływu pracy. Działanie *Code* uruchamia jeden lub więcej wierszy programu PowerShell lub kodu przepływu pracy programu PowerShell w zależności od graficznego typu elementu Runbook. To działanie służy do niestandardowego kodu lub dla funkcjonalności, która jest trudna do osiągnięcia z innymi działaniami. |

### <a name="configuration-control"></a>Kontrola konfiguracji

W kontrolce konfiguracja jest podano szczegółowe informacje dotyczące obiektu zaznaczonego na kanwie. Właściwości dostępne w tym formancie zależą od wybranego typu obiektu. Po wybraniu opcji w kontrolce konfiguracja spowoduje to otwarcie dodatkowych bloków w celu zapewnienia dodatkowych informacji.

### <a name="test-control"></a>Kontrola testu

Kontrolka testu nie jest wyświetlana, gdy edytor graficzny jest uruchamiany po raz pierwszy. Jest ona otwierana w przypadku interaktywnego [testowania graficznego elementu Runbook](#graphical-runbook-procedures).

## <a name="graphical-runbook-procedures"></a>Procedury graficznego elementu Runbook

### <a name="exporting-and-importing-a-graphical-runbook"></a>Eksportowanie i importowanie graficznego elementu Runbook

Można wyeksportować tylko opublikowaną wersję graficznego elementu Runbook. Jeśli element Runbook nie został jeszcze opublikowany, przycisk **Eksportuj** jest wyłączony. Po kliknięciu przycisku **Eksportuj** element Runbook zostanie pobrany na komputer lokalny. Nazwa pliku jest zgodna z nazwą elementu Runbook z rozszerzeniem *graphrunbook* .

Możesz zaimportować graficzny lub graficzny plik Runbook przepływu pracy programu PowerShell, wybierając opcję **importowania** podczas dodawania elementu Runbook. Po wybraniu pliku do zaimportowania można zachować tę samą **nazwę** lub podać nową. W polu Typ elementu Runbook zostanie wyświetlony typ elementu Runbook po dokonaniu oceny wybranego pliku, a jeśli zostanie podjęta próba wybrania innego typu, który nie jest poprawny, zostanie wyświetlony komunikat z uwzględnieniem potencjalnych konfliktów i podczas konwersji może być składnia Błędy.

![Importuj element Runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Testowanie graficznego elementu Runbook

Możesz przetestować wersję roboczą elementu Runbook w Azure Portal, pozostawiając opublikowaną wersję elementu Runbook bez zmian lub testować nowy element Runbook przed jego opublikowaniem. Dzięki temu można sprawdzić, czy element runbook działa poprawnie przed zastąpieniem opublikowanej wersji. Podczas testowania elementu runbook, wersja robocza elementu runbook jest wykonywane, i zostały wykonane wszystkie akcje, które wykonuje. Nie utworzono historii zadań, ale dane wyjściowe są wyświetlane w okienku danych wyjściowych testu.

Otwórz kontrolkę test dla elementu Runbook, otwierając element Runbook do edycji, a następnie klikając przycisk **okienko testowania** .

Kontrolka testu monituje o wszelkie parametry wejściowe i można uruchomić element Runbook, klikając przycisk **Start** .

### <a name="publishing-a-graphical-runbook"></a>Publikowanie graficznego elementu Runbook

Każdy element Runbook w Azure Automation ma wersję roboczą i opublikowaną. Opublikowana wersja jest dostępna do uruchomienia, a tylko wersję roboczą można edytować. Wersję opublikowaną nie mają wpływu żadne zmiany wprowadzone w wersji roboczej. Gdy wersja robocza jest gotowa do udostępnienia, zostanie opublikowana, która zastępuje opublikowaną wersję wersją roboczą.

Można opublikować graficzny element Runbook, otwierając element Runbook do edycji, a następnie klikając przycisk **Publikuj** .

Gdy element Runbook nie został jeszcze opublikowany, ma status **New**. Po opublikowaniu jego stan jest **publikowany**. Jeśli edytujesz element Runbook po jego opublikowaniu, a wersja robocza i opublikowana są różne, element Runbook ma stan **w obszarze Edycja**.

![Stany elementów Runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Istnieje również możliwość przywrócenia opublikowanej wersji elementu Runbook. Spowoduje to wyrzucanie wszelkich zmian wprowadzonych od czasu ostatniej publikacji elementu Runbook i zastąpienie wersji roboczej elementu Runbook wersją opublikowaną.

## <a name="activities"></a>Działania

Działania są blokami konstrukcyjnymi elementu Runbook. Działanie może być poleceniem cmdlet programu PowerShell, podrzędnym elementem Runbook lub działaniem przepływu pracy. Aby dodać działanie do elementu Runbook, kliknij je prawym przyciskiem myszy w kontrolce biblioteka i wybierz pozycję **Dodaj do kanwy**. Następnie możesz kliknąć i przeciągnąć działanie, aby umieścić je w dowolnym miejscu na kanwie. Lokalizacja działania na kanwie nie ma wpływu na działanie elementu Runbook w jakikolwiek sposób. Możesz określić układ elementu Runbook, który jest najbardziej odpowiedni do wizualizacji swojej operacji.

![Dodaj do kanwy](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Wybierz działanie na kanwie, aby skonfigurować jego właściwości i parametry w bloku konfiguracja. Możesz zmienić **etykietę** działania na coś, co jest opisem. Oryginalne polecenie cmdlet jest nadal uruchamiane, po prostu zmieniasz jego nazwę wyświetlaną używaną w edytorze graficznym. Etykieta musi być unikatowa w obrębie elementu Runbook.

### <a name="parameter-sets"></a>Zestawy parametrów

Zestaw parametrów definiuje obowiązkowe i opcjonalne parametry, które akceptują wartości dla konkretnego polecenia cmdlet. Wszystkie polecenia cmdlet mają co najmniej jeden zestaw parametrów, a kilka z nich ma wiele. Jeśli polecenie cmdlet ma wiele zestawów parametrów, należy wybrać, których z nich użyć przed skonfigurowaniem parametrów. Parametry, które można skonfigurować, zależą od wybranego zestawu parametrów. Zestaw parametrów używany przez działanie można zmienić, wybierając pozycję **zestaw parametrów** i wybierając inny zestaw. W takim przypadku wszystkie skonfigurowane wartości parametrów zostaną utracone.

W poniższym przykładzie polecenie cmdlet Get-AzureRmVM ma trzy zestawy parametrów. Wartości parametrów nie można skonfigurować do momentu wybrania jednego z zestawów parametrów. Zestaw parametrów ListVirtualMachineInResourceGroupParamSet służy do zwracania wszystkich maszyn wirtualnych w grupie zasobów i ma jeden opcjonalny parametr. **GetVirtualMachineInResourceGroupParamSet** służy do określania maszyny wirtualnej, która ma zostać zwrócona, i ma dwa obowiązkowe i jeden opcjonalny parametr.

![Zestaw parametrów](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Wartości parametru

Po określeniu wartości parametru, należy wybrać źródło danych w celu określenia sposobu określania wartości. Źródła danych dostępne dla określonego parametru są zależne od prawidłowych wartości dla tego parametru. Na przykład wartość null nie jest dostępna dla parametru, który nie zezwala na wartości null.

| Źródło danych | Opis |
|:--- |:--- |
| Stała wartość |Wpisz wartość parametru. Jest to możliwe tylko dla następujących typów danych: Int32, Int64, String, Boolean, DateTime, Switch. |
| Dane wyjściowe działania |Dane wyjściowe działania poprzedzającego bieżące działanie w przepływie pracy. Wszystkie prawidłowe działania są wymienione na liście. Wybierz tylko działanie, aby użyć jego danych wyjściowych dla wartości parametru. Jeśli działanie wyprowadza obiekt z wieloma właściwościami, można wpisać nazwę właściwości po zaznaczeniu działania. |
| Dane wejściowe elementu Runbook |Wybierz parametr wejściowy elementu Runbook jako dane wejściowe do parametru Activity. |
| Zasób zmiennej |Wybierz zmienną automatyzacji jako dane wejściowe. |
| Zasób poświadczenia |Wybierz poświadczenia automatyzacji jako dane wejściowe. |
| Zasób certyfikatu |Wybierz certyfikat automatyzacji jako dane wejściowe. |
| Zasób połączenia |Wybierz połączenie automatyzacji jako dane wejściowe. |
| Wyrażenie programu PowerShell |Określ proste [wyrażenie programu PowerShell](#powershell-expressions). Wyrażenie jest oceniane przed działaniem i wynikiem użytym dla wartości parametru. Możesz użyć zmiennych, aby odwołać się do danych wyjściowych działania lub parametru wejściowego elementu Runbook. |
| Nie skonfigurowano |Czyści wszystkie wcześniej skonfigurowane wartości. |

#### <a name="optional-additional-parameters"></a>Opcjonalne dodatkowe parametry

Wszystkie polecenia cmdlet mają opcję dostarczania dodatkowych parametrów. Są to typowe parametry programu PowerShell lub inne parametry niestandardowe. Zostanie wyświetlone pole tekstowe, w którym można podać parametry za pomocą składni programu PowerShell. Aby na przykład użyć parametru **verbose** Common, należy określić **wartość "-verbose: $true"** .

### <a name="retry-activity"></a>Działanie ponawiania

**Zachowanie ponowień** umożliwia uruchamianie działania wiele razy, aż do spełnienia określonego warunku, podobnie jak pętla. Tej funkcji można użyć w przypadku działań, które powinny być uruchamiane wiele razy, są podatne na błędy i mogą być potrzebne więcej niż jedna próba sukcesu lub przetestować informacje wyjściowe działania dotyczące prawidłowych danych.

Po włączeniu opcji ponów dla działania można ustawić opóźnienie i warunek. Opóźnienie to czas (w sekundach lub minutach), przez jaki element Runbook czeka przed ponownym uruchomieniem działania. Jeśli opóźnienie nie zostanie określone, działanie zostanie uruchomione ponownie natychmiast po jego zakończeniu.

![Opóźnienie ponowienia działania](media/automation-graphical-authoring-intro/retry-delay.png)

Warunek ponawiania jest wyrażeniem programu PowerShell, które jest oceniane po każdym uruchomieniu działania. Jeśli wyrażenie ma wartość true, działanie zostanie uruchomione ponownie. Jeśli wyrażenie ma wartość false, działanie nie zostanie uruchomione ponownie, a element Runbook przejdzie do następnego działania.

![Opóźnienie ponowienia działania](media/automation-graphical-authoring-intro/retry-condition.png)

Warunek ponawiania może używać zmiennej o nazwie $RetryData, która zapewnia dostęp do informacji o ponownych próbach działania. Ta zmienna ma właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| NumberOfAttempts |Liczba uruchomień działania. |
| Dane wyjściowe |Dane wyjściowe z ostatniego uruchomienia działania. |
| TotalDuration |Upłynęło czasu, gdy działanie zostało uruchomione po raz pierwszy. |
| StartedAt |Godzina w formacie UTC pierwszego uruchomienia działania. |

Poniżej przedstawiono przykłady warunków ponowienia działania.

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

Po skonfigurowaniu warunku ponawiania dla działania działanie obejmuje dwie podpowiedzi wizualne do przypominania. Jedna z nich jest wyświetlana w działaniu, a druga — podczas przeglądania konfiguracji działania.

![Wskaźniki wizualizacji ponowienia działania](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Kontrolka skryptu przepływu pracy

Kontrolka kodu to specjalne działanie, które akceptuje skrypt programu PowerShell lub przepływu pracy programu PowerShell w zależności od typu graficznego elementu Runbook, który jest tworzony w celu zapewnienia funkcjonalności, która w przeciwnym razie może być niedostępna. Nie może akceptować parametrów, ale może używać zmiennych dla danych wyjściowych działań i parametrów wejściowych elementu Runbook. Wszystkie dane wyjściowe działania są dodawane do magistrali danych, chyba że nie ma żadnego linku wychodzącego, w takim przypadku jest dodawane do danych wyjściowych elementu Runbook.

Na przykład poniższy kod wykonuje obliczenia dat przy użyciu zmiennej wejściowej elementu Runbook o nazwie $NumberOfDays. Następnie wysyła obliczaną datę i godzinę jako dane wyjściowe, które będą używane przez kolejne działania w elemencie Runbook.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Linki i przepływ pracy

**Link** w graficznym elemencie Runbook łączy dwa działania. Jest wyświetlana na kanwie jako strzałka wskazująca aktywność źródłową do działania docelowego. Działania są uruchamiane w kierunku strzałki z działaniem docelowym rozpoczynającym się po zakończeniu działania źródłowego.

### <a name="create-a-link"></a>Utwórz łącze

Utwórz łącze między dwoma działaniami, wybierając aktywność źródłową i klikając okrąg u dołu kształtu. Przeciągnij strzałkę do działania docelowego i zwolnij.

![Utwórz łącze](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Wybierz link, aby skonfigurować jego właściwości w bloku konfiguracja. Obejmuje to typ łącza, który jest opisany w poniższej tabeli:

| Typ łącza | Opis |
|:--- |:--- |
| Potok |Działanie docelowe jest uruchamiane jeden raz dla każdego obiektu wyjściowego z działania źródłowego. Działanie docelowe nie działa, jeśli działanie źródłowe nie powoduje żadnych danych wyjściowych. Dane wyjściowe z działania źródłowego są dostępne jako obiekt. |
| Sekwencja |Działanie docelowe jest uruchamiane tylko raz. Odbierze tablicę obiektów od działania źródłowego. Dane wyjściowe działania źródłowego są dostępne jako tablica obiektów. |

### <a name="starting-activity"></a>Działanie uruchamiania

Graficzny element Runbook rozpoczyna się od wszystkich działań, które nie mają linku przychodzącego. Jest to często tylko jedno działanie, które będzie pełnić rolę uruchomienia dla elementu Runbook. Jeśli wiele działań nie ma linku przychodzącego, element Runbook uruchamia się równolegle. Poniższe linki umożliwiają uruchomienie innych działań w miarę ich ukończenia.

### <a name="conditions"></a>Warunki

Gdy określisz warunek dla linku, działanie docelowe jest uruchamiane tylko wtedy, gdy warunek zostanie spełniony. Zwykle używasz zmiennej $ActivityOutput w warunku, aby pobrać dane wyjściowe z działania źródłowego

Dla łącza potoku należy określić warunek dla pojedynczego obiektu, a warunek jest obliczany dla każdego obiektu wyjściowego przez działanie źródłowe. Działanie docelowe jest następnie uruchamiane dla każdego obiektu, który spełnia warunek. Na przykład za pomocą działania źródłowego polecenia Get-AzureRmVm można użyć następującej składni dla linku warunkowego potoku, aby pobrać tylko maszyny wirtualne w grupie zasobów o nazwie *grupa1*.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

W przypadku linku sekwencji warunek jest obliczany tylko raz, ponieważ zwracana jest pojedyncza tablica zawierająca wszystkie obiekty wyjściowe z działania źródłowego. Z tego powodu nie można użyć linku sekwencji do filtrowania, takiego jak link potoku, ale po prostu określi, czy następne działanie jest uruchamiane. Wykonaj na przykład następujący zestaw działań w naszym elemencie Runbook startowej maszyny wirtualnej.

![Link warunkowy z sekwencjami](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Istnieją trzy różne linki sekwencji, które sprawdzają, czy zostały podane dwa parametry wejściowe elementu Runbook reprezentujące nazwę maszyny wirtualnej i nazwę grupy zasobów, aby określić, która jest odpowiednią akcją do uruchamiania pojedynczej maszyny wirtualnej, a następnie uruchomić wszystkie maszyny wirtualne w zasobie Grupa lub wszystkie maszyny wirtualne w ramach subskrypcji. W przypadku linku sekwencji między nawiązaniem połączenia z platformą Azure i uzyskaniem pojedynczej maszyny wirtualnej jest to logika warunku:

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

Korzystając z linku warunkowego, dane dostępne z działania źródłowego do innych działań w tej gałęzi są filtrowane według warunku. Jeśli działanie jest źródłem dla wielu linków, dane dostępne dla działań w każdej gałęzi zależą od warunku linku łączącego się z tą gałęzią.

Na przykład działanie **Start-AzureRmVm** w elemencie Runbook poniżej uruchamia wszystkie maszyny wirtualne. Ma dwie linki warunkowe. Pierwsze łącze warunkowe używa wyrażenia *$ActivityOutput ["Start-AzureRmVM"]. IsSuccessStatusCode-EQ $true* , aby przefiltrować, czy działanie Start-AzureRmVm zostało ukończone pomyślnie. Druga używa wyrażenia *$ActivityOutput ["Start-AzureRmVM"]. IsSuccessStatusCode-ne $true* , aby odfiltrować, czy uruchomienie maszyny wirtualnej przez działanie Start-AzureRmVm nie powiodło się.

![Przykład łącza warunkowego](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Każde działanie, które następuje po pierwszym łączu i używa danych wyjściowych działania z polecenia Get-AzureVM, spowoduje uzyskanie tylko tych maszyn wirtualnych, które zostały uruchomione w momencie uruchomienia polecenia Get-AzureVM. Każde działanie, które następuje po drugim linku, pobiera tylko maszyny wirtualne, które zostały zatrzymane w momencie uruchomienia Get-AzureVM. Każda aktywność po trzecim łączu pobiera wszystkie maszyny wirtualne niezależnie od ich stanu uruchomienia.

### <a name="junctions"></a>Połączenia

Skrzyżowanie to specjalne działanie, które czeka na zakończenie wszystkich rozgałęzień przychodzących. Pozwala to na równoległe uruchamianie wielu działań i upewnienie się, że wszystkie zadania zostały ukończone przed przechodzeniem.

Chociaż połączenie może mieć nieograniczoną liczbę linków przychodzących, nie więcej niż jedno z tych linków może być potokiem. Liczba linków sekwencji przychodzących nie jest ograniczona. Można utworzyć połączenie z wieloma przychodzącymi łączami potokowymi i zapisać element Runbook, ale kończy się niepowodzeniem, gdy zostanie uruchomiony.

Poniższy przykład jest częścią elementu Runbook, który uruchamia zestaw maszyn wirtualnych, jednocześnie pobierając poprawki do zastosowania do tych maszyn. Połączenie jest używane w celu zapewnienia, że oba procesy są wykonywane przed kontynuowaniem elementu Runbook.

![Połączeniem](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cykle

Cykl polega na tym, że działanie docelowe łączy się z powrotem z jego aktywnością źródłową lub innym działaniem, które ostatecznie łączy z powrotem ze źródłem. Cykle nie są obecnie dozwolone w tworzeniu graficznym. Jeśli element Runbook ma cykl, jest on prawidłowo zapisywany, ale po jego uruchomieniu otrzymuje błąd.

![Cykliczny](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Udostępnianie danych między działaniami

Wszystkie dane wyjściowe przez działanie z linkiem wychodzącym są zapisywane do *magistrali danych* dla elementu Runbook. Wszystkie działania w elemencie Runbook mogą używać danych z magistrali danych do wypełniania wartości parametrów lub dołączać do kodu skryptu. Działanie może uzyskać dostęp do danych wyjściowych poprzedniego działania w przepływie pracy.

Sposób zapisywania danych w magistrali danych zależy od typu łącza w działaniu. W przypadku **potoku**dane są wyprowadzane jako wiele obiektów. W przypadku linku **sekwencji** dane są wyprowadzane jako tablica. Jeśli istnieje tylko jedna wartość, jest ona wyjściowa jako tablica pojedynczego elementu.

Dostęp do danych można uzyskać na magistrali danych przy użyciu jednej z dwóch metod. Najpierw używa źródła danych **wyjściowych działania** do wypełnienia parametru innego działania. Jeśli dane wyjściowe są obiektem, możesz określić jedną właściwość.

![Dane wyjściowe działania](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Możesz również pobrać dane wyjściowe działania w źródle danych **wyrażenia programu PowerShell** lub z działania **skryptu przepływu pracy** ze zmienną ActivityOutput. Jeśli dane wyjściowe są obiektem, możesz określić jedną właściwość. Zmienne ActivityOutput używają następującej składni.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Punkty kontrolne

[Punkty kontrolne](automation-powershell-workflow.md#checkpoints) można ustawić w graficznym elemencie Runbook przepływu pracy programu PowerShell, wybierając *element Runbook Checkpoint* w dowolnym działaniu. Powoduje to, że punkt kontrolny zostanie ustawiony po uruchomieniu działania.

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

Punkty kontrolne są włączone tylko w graficznych elementach Runbook przepływu pracy programu PowerShell, ale nie są dostępne w graficznych elementach Runbook. Jeśli element Runbook używa poleceń cmdlet platformy Azure, należy wykonać wszystkie działania z punktem kontrolnym za pomocą polecenia Connect-AzureRmAccount w przypadku zawieszenia elementu Runbook i ponownego uruchomienia z tego punktu kontrolnego w innym procesie roboczym.

## <a name="authenticating-to-azure-resources"></a>Uwierzytelnianie w zasobach platformy Azure

Elementy Runbook w Azure Automation zarządzające zasobami platformy Azure wymagają uwierzytelniania na platformie Azure. [Konto Uruchom jako](automation-create-runas-account.md) (nazywane również jednostką usługi) jest domyślną metodą uzyskiwania dostępu do Azure Resource Manager zasobów w ramach subskrypcji za pomocą elementów Runbook usługi Automation. Tę funkcję można dodać do graficznego elementu Runbook, dodając zasób połączenia **AzureRunAsConnection** , który korzysta z polecenia cmdlet [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) programu PowerShell, a następnie polecenie cmdlet [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) do kanwy. Jest to zilustrowane w poniższym przykładzie:

![Uruchom jako działania uwierzytelniania](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

Działanie Get as Connection (to polecenie Get-AutomationConnection) jest skonfigurowane za pomocą źródła danych o stałej wartości o nazwie AzureRunAsConnection.

![Konfiguracja połączenia Uruchom jako](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Następne działanie Connect-AzureRmAccount dodaje uwierzytelnione konto Uruchom jako do użycia w elemencie Runbook.

![Zestaw parametrów Connect-AzureRmAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

> [!IMPORTANT]
> **Add-AzureRmAccount** jest teraz aliasem dla polecenia **Connect-AzureRmAccount**. Podczas wyszukiwania elementów biblioteki, jeśli nie widzisz pozycji **Connect-AzureRMAccount**, możesz użyć polecenie **Add-AzureRMAccount**lub zaktualizować moduły na koncie usługi Automation.

Dla parametrów identyfikator **aplikacji**, **CERTIFICATETHUMBPRINT**i **TENANTID** należy określić nazwę właściwości dla ścieżki pola, ponieważ działanie wyprowadza obiekt z wieloma właściwościami. W przeciwnym razie podczas wykonywania elementu Runbook próba uwierzytelnienia nie powiedzie się. Jest to wymaganie co najmniej minimalne uwierzytelnianie elementu Runbook za pomocą konta Uruchom jako.

Aby zachować zgodność z poprzednimi wersjami dla subskrybentów, którzy utworzyli konto usługi Automation przy użyciu [konta użytkownika usługi Azure AD](automation-create-aduser-account.md) do zarządzania klasycznym wdrożeniem platformy Azure lub dla zasobów Azure Resource Manager, metoda uwierzytelniania to polecenie cmdlet Add-AzureAccount z [zasobem Credential](automation-credentials.md) , który reprezentuje Active Directory użytkownika z dostępem do konta platformy Azure.

Tę funkcję można dodać do graficznego elementu Runbook poprzez dodanie zasobu poświadczeń do kanwy, a następnie działania Add-AzureAccount. Add-AzureAccount używa działania Credential dla jego danych wejściowych. Jest to zilustrowane w poniższym przykładzie:

![Działania uwierzytelniania](media/automation-graphical-authoring-intro/authentication-activities.png)

Musisz uwierzytelnić się na początku elementu Runbook i po każdym punkcie kontrolnym. Oznacza to dodanie dodawania działania Add-AzureAccount po każdym działaniu punktu kontrolnego. Nie potrzebujesz działania dodawania poświadczeń, ponieważ możesz użyć tego samego

![Dane wyjściowe działania](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Dane wejściowe i wyjściowe elementu Runbook

### <a name="runbook-input"></a>Dane wejściowe elementu Runbook

Element Runbook może wymagać wprowadzenia danych przez użytkownika podczas uruchamiania elementu Runbook za pośrednictwem Azure Portal lub z innego elementu Runbook, jeśli jest on używany jako element podrzędny.
Jeśli na przykład masz element Runbook, który tworzy maszynę wirtualną, może być konieczne podanie informacji, takich jak nazwa maszyny wirtualnej i inne właściwości przy każdym uruchomieniu elementu Runbook.

Dane wejściowe dla elementu Runbook są akceptowane przez zdefiniowanie jednego lub kilku parametrów wejściowych. Wartości tych parametrów są podane za każdym razem, gdy element Runbook zostanie uruchomiony. Po uruchomieniu elementu Runbook za pomocą Azure Portal zostanie wyświetlony monit o podanie wartości dla każdego z parametrów wejściowych elementu Runbook.

Aby uzyskać dostęp do parametrów wejściowych dla elementu Runbook, kliknij przycisk **dane wejściowe i wyjściowe** na pasku narzędzi elementu Runbook.

Spowoduje to otwarcie kontrolki **dane wejściowe i wyjściowe** , w której można edytować istniejący parametr wejściowy lub utworzyć nowy, klikając przycisk **Dodaj dane wejściowe**.

![Dodawanie danych wejściowych](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Każdy parametr wejściowy jest definiowany przez właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| Nazwa |Unikatowa nazwa parametru. Może zawierać tylko znaki alfanumeryczne i nie może zawierać spacji. |
| Opis |Opcjonalny opis parametru wejściowego. |
| Typ |Oczekiwano typu danych dla wartości parametru. Azure Portal zapewnia odpowiednią kontrolę dla typu danych dla każdego parametru podczas monitowania o dane wejściowe. |
| Obowiązkowy |Określa, czy należy podać wartość parametru. Nie można uruchomić elementu Runbook, jeśli nie podasz wartości dla każdego obowiązkowego parametru, który nie ma zdefiniowanej wartości domyślnej. |
| Default Value |Określa, która wartość jest używana dla parametru, jeśli nie została podana. Może to być wartość zerowa lub określona. |

### <a name="runbook-output"></a>Wynik uruchomienia elementu Runbook

Dane utworzone przez dowolne działanie, które nie ma linku wychodzącego, są zapisywane w danych [wyjściowych elementu Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). Dane wyjściowe są zapisywane wraz z zadaniem elementu Runbook i są dostępne dla nadrzędnego elementu Runbook, gdy element Runbook jest używany jako element podrzędny.

## <a name="powershell-expressions"></a>Wyrażenia programu PowerShell

Jedną z zalet tworzenia elementów graficznych jest umożliwienie tworzenia elementu Runbook przy minimalnej znajomości programu PowerShell. Obecnie trzeba znać bit programu PowerShell, aby wypełnić pewne [wartości parametrów](#activities) i ustawić [warunki łącza](#links-and-workflow). Ta sekcja zawiera krótkie wprowadzenie do wyrażeń programu PowerShell dla tych użytkowników, którzy mogą nie mieć do nich znać. Pełne szczegóły programu PowerShell są dostępne w [skryptach przy użyciu programu Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Źródło danych wyrażenia programu PowerShell
Możesz użyć wyrażenia programu PowerShell jako źródła danych, aby wypełnić wartość [parametru działania](#activities) wynikami niektórych kodów programu PowerShell. Może to być pojedynczy wiersz kodu, który wykonuje prostą funkcję lub wiele wierszy, które wykonują pewne skomplikowane logiki. Wszystkie dane wyjściowe polecenia, które nie są przypisane do zmiennej, są wyprowadzane do wartości parametru.

Na przykład następujące polecenie spowoduje wyjście z bieżącej daty.

Na przykład następujące polecenie spowoduje wyjście z bieżącej daty.

```powershell-interactive
Get-Date
```

Następujące polecenia kompilują ciąg z bieżącej daty i przypisujemy go do zmiennej. Zawartość zmiennej jest następnie wysyłana do danych wyjściowych

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Następujące polecenia szacują bieżącą datę i zwracają ciąg wskazujący, czy bieżący dzień to weekend czy dzień tygodnia.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Dane wyjściowe działania

Aby użyć danych wyjściowych z poprzedniego działania w elemencie Runbook, użyj zmiennej $ActivityOutput z następującą składnią.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Na przykład może istnieć działanie z właściwością wymagającą nazwy maszyny wirtualnej, w tym przypadku można użyć następującego wyrażenia:

```powershell-interactive
$ActivityOutput['Get-AzureVm'].Name
```

Jeśli właściwość, która wymagała obiektu maszyny wirtualnej, a nie tylko właściwość, zostanie zwrócony cały obiekt przy użyciu następującej składni.

```powershell-interactive
$ActivityOutput['Get-AzureVm']
```

Możesz również użyć danych wyjściowych działania w bardziej złożonych wyrażeniach, takich jak następujące, które łączą tekst do nazwy maszyny wirtualnej.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVm'].Name
```

### <a name="conditions"></a>Warunki

[Operatory porównania](https://technet.microsoft.com/library/hh847759.aspx) służą do porównywania wartości lub określania, czy wartość jest zgodna z określonym wzorcem. Porównanie zwraca wartość $true lub $false.

Na przykład poniższy warunek określa, czy maszyna wirtualna z działania o nazwie *Get-AzureVM* jest aktualnie *zatrzymana*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

Poniższy warunek sprawdza, czy ta sama maszyna wirtualna znajduje się w stanie innym niż *zatrzymano*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Można sprzęgać wiele warunków przy użyciu [operatora logicznego](https://technet.microsoft.com/library/hh847789.aspx) , takiego jak **-i** lub **-lub**. Na przykład, poniższe warunki sprawdzają, czy ta sama maszyna wirtualna w poprzednim przykładzie jest w stanie *zatrzymania* lub *zatrzymywania*.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Tabelami skrótów

[Hashtable](https://technet.microsoft.com/library/hh847780.aspx) są parami nazw/wartości, które są przydatne do zwracania zestawu wartości. Właściwości niektórych działań mogą oczekiwać znaku Hashtable zamiast prostej wartości. Możesz również zobaczyć jako tablicę skrótów, która jest nazywana słownikiem.

Utworzysz tablicę skrótów z poniższą składnią. Tablica skrótów może zawierać dowolną liczbę wpisów, ale każda z nich jest definiowana przez nazwę i wartość.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Na przykład następujące wyrażenie tworzy tablicę skrótów, która ma zostać użyta w źródle danych dla parametru działania, który oczekuje obiektu Hashtable z wartościami dla wyszukiwania internetowego.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

Poniższy przykład używa danych wyjściowych działania o nazwie *Pobierz połączenie Twitter* , aby wypełnić tablicę skrótów.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell).
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
* Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
* Aby zrozumieć sposób uwierzytelniania przy użyciu konta Uruchom jako usługi Automation, zobacz [Konfigurowanie konta Uruchom jako platformy Azure](automation-sec-configure-azure-runas-account.md)
