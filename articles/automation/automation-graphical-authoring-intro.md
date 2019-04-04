---
title: Graficzny, tworzenia w usłudze Azure Automation
description: Tworzenie graficzne pozwala na tworzenie elementów runbook usługi Azure Automation bez Praca z kodem. Ten artykuł zawiera wprowadzenie do tworzenia elementów graficznych oraz wszystkie szczegóły potrzebne do rozpoczęcia tworzenia graficznego elementu runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ae732ab5c73dbec4a2aef6521b9edb490079112e
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918148"
---
# <a name="graphical-authoring-in-azure-automation"></a>Graficzny, tworzenia w usłudze Azure Automation

Tworzenie graficznego służy do tworzenia elementów runbook usługi Azure Automation bez złożonością odpowiedni kod programu Windows PowerShell lub przepływie pracy programu PowerShell. Dodawanie działań do obszaru roboczego z biblioteki poleceń cmdlet i elementów runbook, łączyć je ze sobą i skonfigurować w celu utworzenia przepływu pracy. Jeśli nigdy nie korzystano z programu System Center Orchestrator lub Service Management Automation (SMA), następnie szablon powinien wyglądać znanym

Ten artykuł zawiera wprowadzenie do tworzenia elementów graficznych i pojęcia, że chcesz rozpocząć tworzenie graficznego elementu runbook.

## <a name="graphical-runbooks"></a>Graficzne elementy runbook

Wszystkie elementy runbook w usłudze Azure Automation są przepływami pracy programu Windows PowerShell. Graficzny i przepływu pracy programu PowerShell graficznych elementów runbook do generowania kodu programu PowerShell jest uruchamiany przez pracowników automatyzacji, ale nie jest możliwe do wyświetlania go lub go bezpośrednio modyfikować. Graficzny element runbook można przekonwertować elementu runbook graficzny przepływ pracy programu PowerShell i na odwrót, ale nie można przekonwertować na tekstowy element runbook. Nie można zaimportować tekstową istniejącego elementu runbook w edytorze graficznym.

## <a name="overview-of-graphical-editor"></a>Przegląd edytora graficznego

W witrynie Azure portal można otworzyć edytor graficzny, przez utworzenie lub edycję graficznego elementu runbook.

![Graficzny obszaru roboczego](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

W poniższych sekcjach opisano formantów w edytorze graficznym.

### <a name="canvas"></a>Kanwy

Obszar roboczy jest, gdzie projektowanie elementu runbook. Dodawanie działań z węzłów w kontrolce Biblioteka do elementu runbook i łączyć je z łączami do definiowania logiki elementu runbook.

Formanty w dolnej części kanwy umożliwia powiększać i pomniejszać.

### <a name="library-control"></a>Formant biblioteki

W kontrolce biblioteka jest wybierania [działania](#activities) do dodania do elementu runbook. Możesz je dodać do kanwy, w którym połączenia do innych działań. Zawiera cztery sekcje opisane w poniższej tabeli:

| Sekcja | Opis |
|:--- |:--- |
| Polecenia cmdlet |Obejmuje wszystkie polecenia cmdlet, których można użyć w elemencie runbook. Polecenia cmdlet są uporządkowane według modułu. Dostępne są wszystkie moduły, które zostały zainstalowane na koncie usługi automation. |
| Elementy Runbook |Zawiera elementy runbook na Twoim koncie usługi automation. Tych elementów runbook można dodać do kanwy, aby służyć jako podrzędne elementy runbook. Wyświetlane są tylko elementy runbook typu core jako elementu runbook, edytowany; Graficzny tylko opartego na programie PowerShell elementów runbook w elementach runbook są pokazane, gdy dla elementów runbook graficzny przepływ pracy programu PowerShell są wyświetlane tylko przepływu pracy — opartego na programie PowerShell elementów runbook. |
| Elementy zawartości |Obejmuje [elementów zawartości usługi automation](/previous-versions/azure/dn939988(v=azure.100)) na koncie usługi automation, które mogą być używane w elemencie runbook. Po dodaniu elementu zawartości w elemencie runbook dodaje do działania przepływu pracy, który pobiera wybranego zasobu. W przypadku zmiennych elementów zawartości można wybrać, czy należy dodać działania pobierania zmiennej lub ustawiania zmiennej. |
| Sterowanie elementem Runbook |Obejmuje działania sterowania elementu runbook, które mogą być używane w bieżącym elemencie runbook. A *Rozgałęzienie* przyjmuje wielu danych wejściowych i czeka, aż wszystkie została ukończona przed kontynuowaniem przepływu pracy. A *kodu* działanie jest uruchamiane co najmniej jeden wiersz kodu programu PowerShell lub przepływie pracy programu PowerShell w zależności od typu graficzny element runbook. To działanie można użyć niestandardowego kodu lub funkcji, który jest trudny do osiągnięcia z innymi działaniami. |

### <a name="configuration-control"></a>Kontrola konfiguracji

Kontrola konfiguracji jest, gdzie należy podać szczegóły dotyczące obiektu wybranego w obszarze roboczym. Właściwości dostępne w tym elemencie sterującym zależy od rodzaju wybrany obiekt. Po wybraniu opcji w kontrolce konfiguracji otwiera bloków w witrynie dodatkowej w celu zapewnienia dodatkowych informacji.

### <a name="test-control"></a>Testowanie formantu

Formant testu nie jest wyświetlany, podczas pierwszego uruchomienia edytora graficznego. Po otwarciu możesz interaktywnie [test graficznego elementu runbook](#graphical-runbook-procedures).

## <a name="graphical-runbook-procedures"></a>Procedury graficznego elementu runbook

### <a name="exporting-and-importing-a-graphical-runbook"></a>Eksportowanie i importowanie graficznego elementu runbook

Można eksportować tylko opublikowanej wersji graficznego elementu runbook. Jeśli element runbook nie został opublikowany, a następnie **wyeksportować** przycisk jest wyłączony. Po kliknięciu **wyeksportować** button, element runbook zostanie pobrana do komputera lokalnego. Nazwa pliku jest zgodna z nazwą elementu runbook z *graphrunbook* rozszerzenia.

Można zaimportować plik elementu runbook graficzny lub graficzny przepływ pracy programu PowerShell, wybierając **zaimportować** opcji podczas dodawania elementu runbook. Po wybraniu plik do zaimportowania, możesz używać tego samego **nazwa** lub podać nowe hasło. W polu Typ elementu Runbook zostanie wyświetlona typ elementu runbook, po jego ocenia wybrano plik i próbie innego typu, który nie jest poprawną zostanie wyświetlony komunikat biorąc pod uwagę istnieją potencjalne konflikty i podczas konwersji, może być składni błędy.

![Importowanie elementu runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Testowanie graficznego elementu runbook

Możesz przetestować wersję roboczą elementu runbook w witrynie Azure portal, pozostawiając opublikowaną wersję elementu runbook bez zmian lub nowy element runbook można przetestować, zanim został opublikowany. Dzięki temu można sprawdzić, czy element runbook działa poprawnie przed zastąpieniem opublikowanej wersji. Podczas testowania elementu runbook, wersja robocza elementu runbook jest wykonywane, i zostały wykonane wszystkie akcje, które wykonuje. Historia zadań nie zostanie utworzony, ale dane wyjściowe są wyświetlane w okienku danych wyjściowych testu.

Otwieranie kontroli testu elementu runbook, otwierając element runbook do edycji, a następnie kliknij polecenie **okienko testowania** przycisku.

Formant testu wyświetla monit dla danych wejściowych parametrów i można uruchomić elementu runbook, klikając **Start** przycisku.

### <a name="publishing-a-graphical-runbook"></a>Publikowanie graficznego elementu runbook

Każdy element runbook w usłudze Azure Automation ma wersje roboczą i opublikowaną. Opublikowana wersja jest dostępna do uruchomienia, a tylko wersję roboczą można edytować. Wersję opublikowaną nie mają wpływu żadne zmiany wprowadzone w wersji roboczej. Gdy wersja robocza jest gotowy do udostępnienia, należy ją opublikować, co powoduje nadpisanie wersji opublikowanej wersją roboczą.

Graficzny element runbook można opublikować, otwierając element runbook do edycji, a następnie klikając **Publikuj** przycisku.

Gdy element runbook nie został opublikowany, ma ona stan **New**. Po opublikowaniu, ma ona stan **opublikowano**. Jeśli po zostało już opublikowane i są różne wersje roboczą i opublikowaną edytować element runbook, element runbook ma stan **w trakcie edycji**.

![Stany elementów Runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Masz również opcję Przywróć opublikowaną wersję elementu runbook. Natychmiast zgłasza wszelkie zmiany wprowadzone od czasu ostatniej publikacji elementu runbook wraz ze zastępuje wersję opublikowaną wersję roboczą elementu runbook.

## <a name="activities"></a>Działania

Działania są blokami konstrukcyjnymi elementu runbook. Działanie może być polecenia cmdlet programu PowerShell, podrzędnego elementu runbook lub działania przepływu pracy. Dodaj działanie elementu runbook, klikając prawym przyciskiem myszy w kontrolce Biblioteka i wybierając **Dodaj do kanwy**. Można następnie kliknij i przeciągnij działanie, aby umieścić w dowolnym miejscu na kanwie, który chcesz. Lokalizacja działania na kanwie nie wpływa na działania elementu runbook w dowolny sposób. Układ element runbook jednak jest on najbardziej odpowiedni do jego działania wizualizacji.

![Dodaj do kanwy](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Wybierz działania na kanwę, aby skonfigurować jej właściwości i parametrów w bloku konfiguracji. Możesz zmienić **etykiety** działania na coś, który ma charakter opisowy dla Ciebie. Oryginalne polecenie cmdlet jest nadal uruchomione, są po prostu zmienić jego nazwę wyświetlaną, która jest używana w edytorze graficznym. Etykieta musi być unikatowa w obrębie elementu runbook.

### <a name="parameter-sets"></a>Zestawy parametrów

Zestaw parametrów definiuje obowiązkowych i opcjonalnych parametrów, które akceptują wartości dla określonego polecenia cmdlet. Wszystkie polecenia cmdlet mają co najmniej jeden zestaw parametrów, a niektóre mają wiele. Jeśli polecenie cmdlet ma wiele zestawów parametrów, należy wybrać który z nich korzystać, aby można było skonfigurować parametry. Parametry, które można skonfigurować, zależy od zestaw parametrów, które można wybrać. Możesz zmienić zestaw parametrów, używane przez działanie, wybierając **zestaw parametrów** i wybierając inny zestaw. W takim przypadku wszystkie wartości parametrów, które skonfigurowano zostaną utracone.

W poniższym przykładzie polecenie cmdlet Get-AzureRmVM ma trzy zestawów parametrów. Nie można skonfigurować wartości parametrów, dopóki nie wybierze jeden z zestawów parametrów. Zestaw parametrów ListVirtualMachineInResourceGroupParamSet służy do zwracania wszystkich maszyn wirtualnych w grupie zasobów i ma jeden parametr opcjonalny. **GetVirtualMachineInResourceGroupParamSet** służy do określania maszyny wirtualnej, aby wrócić i ma dwa obowiązkowe i jeden parametr opcjonalny.

![Zestaw parametrów](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Wartości parametrów

Po określeniu wartości dla parametru, wybierasz źródła danych, aby określić, jak określona jest wartość. Źródła danych, które są dostępne dla określonego parametru jest zależna od prawidłowe wartości dla tego parametru. Na przykład wartość Null nie jest dostępną opcją parametru, która nie zezwala na wartości null.

| Źródło danych | Opis |
|:--- |:--- |
| Wartość stałej |Wpisz wartość dla parametru. To jest dostępna tylko dla następujących typów danych: Int32, Int64, String, Boolean, DateTime, przełącz się. |
| Wyjście działania |Dane wyjściowe z działania, które poprzedza bieżącego działania w przepływie pracy. Wszystkie prawidłowe działania są wymienione. Wybierz tylko działania Użyj jego danych wyjściowych dla wartości parametru. Jeśli obiekt z wieloma właściwościami wyjściem działania, można wpisać nazwę właściwości po wybraniu działania. |
| Dane wejściowe elementu Runbook |Wybierz parametr wejściowy elementu runbook jako dane wejściowe dla parametru działania. |
| Zasób zmiennej |Zmienna usługi Automation wybierz jako dane wejściowe. |
| Zasób poświadczeń |Wybierz poświadczenie automatyzacji jako dane wejściowe. |
| Zasób certyfikatu |Wybierz certyfikat usługi Automation jako dane wejściowe. |
| Zasób połączenia |Wybierz połączenie automatyzacji jako dane wejściowe. |
| Wyrażenie programu PowerShell |Określ prosty [wyrażenie programu PowerShell](#powershell-expressions). Wyrażenie jest obliczane przed działania i wynik używany dla wartości parametru. Można używać zmiennych do odwoływania się do danych wyjściowych działania lub parametr wejściowy elementu runbook. |
| Nieskonfigurowane |Czyści dowolnej wartości, który został wcześniej skonfigurowany. |

#### <a name="optional-additional-parameters"></a>Opcjonalne parametry dodatkowe

Wszystkie polecenia cmdlet mają opcję, aby zapewnić dodatkowe parametry. Są to typowe parametry programu PowerShell lub inne parametry niestandardowe. Zostanie wyświetlona pole tekstowe, w którym można podać parametry przy użyciu składni programu PowerShell. Na przykład, aby użyć **pełne** typowy parametr należy określić **"-Verbose: $True"**.

### <a name="retry-activity"></a>Ponów próbę wykonania działania

**Zachowanie przy ponowieniu próby** umożliwia działanie, które ma być uruchamiane wielokrotnie, aż określony warunek jest spełniony, podobnie jak w pętli. Ta funkcja służy do działań, które należy uruchamiać wielokrotnie, są podatne i może być więcej niż jeden próba do poprawnego działania, lub testowanie informacji wyjściowych działania na prawidłowe dane.

Po włączeniu ponawiania prób dla działania, można ustawić opóźnienie i warunku. Opóźnienie to czas (w sekundach lub minutach), element runbook czeka przed jej uruchomieniem działania. Jeśli opóźnienie nie jest określona, działanie zostanie uruchomione ponownie natychmiast, po jej zakończeniu.

![Opóźnienie ponawiania działania](media/automation-graphical-authoring-intro/retry-delay.png)

Warunku ponawiania prób to wyrażenie programu PowerShell, które jest oceniane po każdym razem, gdy działanie zostanie uruchomione. Jeśli wyrażenie jest rozpoznawana jako wartość True, następnie działanie zostanie uruchomione ponownie. Jeśli wynikiem rozpoznania wyrażenia jest wartość False, działanie nie uruchomi ponownie, a element runbook przechodzi do następnego działania.

![Opóźnienie ponawiania działania](media/automation-graphical-authoring-intro/retry-condition.png)

Warunku ponawiania, można użyć zmiennej o nazwie $RetryData zapewniający dostęp do informacji na temat działania liczbę ponownych prób. Ta zmienna ma właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| NumberOfAttempts |Liczba przypadków, które zostało uruchomione. |
| Dane wyjściowe |Dane wyjściowe z ostatniego uruchomienia działania. |
| TotalDuration |Upłynął limit czasu, który upłynął od czasu uruchomienia działania po raz pierwszy. |
| StartedAt |Godzina w formacie UTC, najpierw uruchomienia działania. |

Poniżej przedstawiono przykładowe działanie ponów warunków.

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

Po skonfigurowaniu warunku ponawiania prób dla działania, działanie obejmuje dwa podpowiedzi wizualne przypomnienia. Jeden są prezentowane w działaniu, a drugi to, gdy przejrzeć konfigurację działania.

![Wskaźniki wizualne ponownych prób działań](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Kontrolka skryptu przepływu pracy

Kontrola kodu jest specjalnym działaniu, które akceptuje skrypt programu PowerShell lub przepływie pracy programu PowerShell, w zależności od typu graficzny element runbook, które są tworzone w celu udostępniają funkcje, które w innym scenariuszu mogą nie być dostępne. Nie można zaakceptować, parametry, ale może użyć zmiennych dla działania danych wyjściowych i elementu runbook parametry wejściowe. Wszelkie dane wyjściowe działania jest dodawany w magistrali danych, jeśli nie ma żadnych wychodzące łącze w tym przypadku jest dodawany do danych wyjściowych elementu runbook.

Na przykład poniższy kod wykonuje obliczenia daty przy użyciu zmiennej wejściowe elementu runbook o nazwie $NumberOfDays. Wysyła następnie czasu data obliczona jako dane wyjściowe, który będzie używany przez kolejne działania w elemencie runbook.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Łącza i przepływu pracy

A **łącze** w graficzny element runbook nawiązuje połączenie dwóch działań. Jest on wyświetlany na kanwie jako strzałka z działania źródłowego na działanie docelowe. Działania Uruchom w kierunku strzałkę z działanie docelowe uruchamiania po zakończeniu działania źródłowego.

### <a name="create-a-link"></a>Utwórz łącze

Utwórz łącze między dwoma działaniami, wybierając działanie źródłowego i klikając koło w dolnej części kształtu. Przeciągnij strzałkę do działanie docelowe i wersji.

![Utwórz łącze](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Wybierz link, aby skonfigurować jej właściwości w bloku konfiguracji. Obejmuje to typ łącza, który jest opisany w poniższej tabeli:

| Typ linku | Opis |
|:--- |:--- |
| Potok |Działanie docelowe jest uruchamiane raz dla każdego obiektu danych wyjściowych działania źródłowego. Działanie docelowe nie jest uruchamiany, jeśli działania źródłowego nie powoduje żadnych danych wyjściowych. Dane wyjściowe działania źródłowego jest dostępna jako obiekt. |
| Sekwencja |Działanie docelowe jest uruchamiane tylko jeden raz. Tablica obiektów otrzymuje z działania źródłowego. Dane wyjściowe działania źródłowego jest dostępna jako tablica obiektów. |

### <a name="starting-activity"></a>Działanie początkowe

Graficzny element runbook rozpoczyna się od żadnych działań, które nie mają łącza przychodzącego. Często jest tylko jedno działanie, które będzie pełnić rolę działanie początkowe dla elementu runbook. Jeśli wiele działań nie mają łącza przychodzącego, element runbook rozpoczyna się przez uruchomienie ich równolegle. Jest zgodna z łącza do uruchamiania innych działań, zgodnie z każdym zakończeniu.

### <a name="conditions"></a>Warunki

Po określeniu warunku łącza, działanie docelowe jest uruchamiane tylko, gdy warunek jest rozpoznawana jako wartość true. Zazwyczaj używa się zmienną $ActivityOutput w stanie, aby pobrać dane wyjściowe działania źródłowego

Linku potoku należy określić warunek dla pojedynczego obiektu, a warunek będzie oceniana dla każdego obiektu danych wyjściowych przez działania źródłowego. Działanie docelowe następnie jest uruchamiana dla każdego obiektu, który spełnia warunek. Na przykład przy użyciu działania programu źródłowego Get-AzureRmVm, następująca składnia może służyć łącza warunkowe potoku można pobrać tylko maszyny wirtualne w grupie zasobów o nazwie *grupa1*.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Łącza sekwencji warunek jest oceniane tylko raz, ponieważ zwracany jest pojedynczą tablicę zawierającą wszystkie obiekty dane wyjściowe działania źródłowego. W związku z tym łącze sekwencji nie można użyć do filtrowania takich jak linku potoku, ale po prostu określić, czy jest uruchamianie następnego działania. Skorzystaj na przykład następujący zestaw działań w nasz element runbook uruchamianie maszyny Wirtualnej.

![Połączenie warunkowe z sekwencjami](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Brak łącza różnych sekwencji, które jest sprawdzana podano wartości do dwóch parametrów wejściowych elementu runbook, które reprezentującą nazwę maszyny Wirtualnej i nazwę grupy zasobów w celu ustalenia, które jest odpowiednie akcję do wykonania — uruchamianie pojedynczej maszyny Wirtualnej, uruchom wszystkie maszyny wirtualne w zasobach Grupa lub wszystkich maszyn wirtualnych w ramach subskrypcji. Dla sekwencji łącza między Połącz z platformą Azure i Get pojedynczej maszyny Wirtualnej Oto logiki warunkowej:

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

Korzystając z łączy warunkowych, dostępne działania źródłowego do innych działań w tej gałęzi dane są filtrowane według warunku. Działanie w przypadku źródła do wielu łączy, dane dostępne dla działań w każdej gałęzi są zależne od warunku łącza nawiązywania połączenia z tego oddziału.

Na przykład **Start-AzureRmVm** działania w elemencie runbook poniżej uruchamia wszystkie maszyny wirtualne. Posiada dwie łączy warunkowych. Pierwszy link warunkowego używa wyrażenia *$ActivityOutput ['Start-AzureRmVM']. IsSuccessStatusCode - eq $true* do filtrowania, jeśli działanie polecenia Start-AzureRmVm została ukończona pomyślnie. W drugiej zastosowano wyrażenie *$ActivityOutput ['Start-AzureRmVM']. IsSuccessStatusCode - ne $true* do filtrowania, jeśli działanie polecenia Start-AzureRmVm, nie można uruchomić maszyny wirtualnej.

![Przykład łączy warunkowych](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Każda czynność następujący pierwszy link i używa działania dane wyjściowe z polecenia Get-AzureVM pobierze tylko maszyn wirtualnych, które zostały uruchomione w momencie uruchomienia polecenia Get-AzureVM. Wszelkie działania drugi Link znajdujący się pobiera tylko maszyny wirtualne, które zostały zatrzymane w momencie uruchomienia polecenia Get-AzureVM. Dowolne działanie, trzeci łącze pobiera wszystkie maszyny wirtualne, niezależnie od ich stan działania.

### <a name="junctions"></a>Punktach transferu

Połączenie jest specjalnym działaniu, które czeka, aż wszystkie gałęzie przychodzących została ukończona. Dzięki temu można do równoległego uruchamiania wielu działań i upewnij się, wszystkie została ukończona przed kontynuowaniem.

Gdy połączenie może mieć dowolną liczbę linków przychodzących, nie więcej niż jeden z tych linków można potoku. Liczba łączy przychodzących sekwencji nie jest ograniczona. Możesz utworzyć połączenie z wieloma łączami potoku przychodzących i Zapisz element runbook, ale zakończy się niepowodzeniem po jej uruchomieniu.

W poniższym przykładzie jest częścią elementu runbook, który uruchamia zestaw maszyn wirtualnych podczas pobierania jednocześnie poprawek, które mają być stosowane do tych maszyn. Połączenie jest używany do zapewnienia, że oba procesy są wykonywane przed kontynuacją elementu runbook.

![Rozgałęzienie](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cykle

Cykl jest, gdy łączy działania docelowego do jego działania źródłowego lub inne działanie, które ostatecznie łącza z powrotem do źródła. Obecnie cykle nie są dozwolone w tworzenia elementów graficznych. Jeśli element runbook zawiera cykl, zapisuje prawidłowo, ale odbiera błąd, po jego uruchomieniu.

![Cykliczny](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Udostępnianie danych między działaniami

Wszelkie dane, które są danymi wyjściowymi działania łącza wychodzącego są zapisywane do *magistrali danych* dla elementu runbook. Wszelkie działania w elemencie runbook można użyć danych na magistrali danych do wypełniania wartości parametrów, lub Uwzględnij w kodzie skryptu. Działania mogą uzyskać dostęp do danych wyjściowych dowolnego poprzedniego działania w przepływie pracy.

Jak dane są zapisywane w magistrali danych zależy od typu łącza w ramach działania. Aby uzyskać **potoku**, dane znajdują się dane wyjściowe jako wielokrotności obiekty. Aby uzyskać **sekwencji** łącza, dane są dane wyjściowe w postaci tablicy. Jeśli istnieje tylko jedna wartość, jest dane wyjściowe jako tablica pojedynczy element.

Możesz uzyskać dostęp do danych na magistrali danych przy użyciu jednej z dwóch metod. Najpierw używa **wyjście działania** źródła danych, aby wypełnić parametr kolejnego działania. Jeśli dane wyjściowe jest obiektem, możesz określić jedną właściwość.

![Wyjście działania](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Możesz również pobrać dane wyjściowe działania w **wyrażenie programu PowerShell** źródła danych lub z **skrypt przepływu pracy** działanie przy użyciu zmiennej ActivityOutput. Jeśli dane wyjściowe jest obiektem, możesz określić jedną właściwość. Zmienne ActivityOutput należy użyć następującej składni.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Punkty kontrolne

Możesz ustawić [punktów kontrolnych](automation-powershell-workflow.md#checkpoints) w elemencie runbook graficzny przepływ pracy programu PowerShell, wybierając *element runbook punktu kontrolnego* dla każdego działania. Powoduje to, że punkt kontrolny można ustawić, po uruchomieniu działania.

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

Punkty kontrolne są tylko włączone w elementach runbook graficzny przepływ pracy programu PowerShell, nie jest dostępna w graficznych elementach runbook. Jeśli element runbook używa poleceń cmdlet platformy Azure, należy wykonać żadnych działań utworzono punkt kontrolny przy użyciu Connect-AzureRmAccount w przypadku, gdy element runbook jest wstrzymana, a także ponowne uruchomienie z tego punktu kontrolnego w ramach innego procesu roboczego.

## <a name="authenticating-to-azure-resources"></a>Uwierzytelniania do zasobów platformy Azure

Elementy Runbook w usłudze Azure Automation, zarządzania zasobami platformy Azure, które wymagają uwierzytelniania na platformie Azure. [Konta Uruchom jako](automation-create-runas-account.md) (nazywane również nazwy głównej usługi) jest to domyślna metoda dostępu do zasobów usługi Azure Resource Manager w subskrypcji przy użyciu elementów runbook usługi Automation. Można dodać tę funkcjonalność do graficznego elementu runbook, dodając **AzureRunAsConnection** trwałego połączenia, który jest przy użyciu programu PowerShell [Get AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) polecenia cmdlet i [ Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) polecenia cmdlet do kanwy. Jest to zilustrowane w poniższym przykładzie:

![Uruchom jako działania do uwierzytelniania](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

Działanie Uzyskaj połączenie Uruchom jako (oznacza to, Get-AutomationConnection), jest skonfigurowany z wartości stałej źródła danych o nazwie AzureRunAsConnection.

![Konfiguracja połączenia Uruchom jako](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Następne działanie Connect-AzureRmAccount dodaje uwierzytelnionego konta Uruchom jako do użycia w elemencie runbook.

![Zestaw parametrów Connect-AzureRmAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

> [!IMPORTANT]
> **Add-AzureRmAccount** teraz jest aliasem dla **Connect-AzureRMAccount**. Podczas wyszukiwania biblioteki elementów, jeśli nie widzisz **Connect-AzureRMAccount**, możesz użyć **Add-AzureRmAccount**, lub na koncie usługi Automation można zaktualizować moduły.

Dla parametrów **APPLICATIONID**, **CERTIFICATETHUMBPRINT**, i **TENANTID** należy określić nazwę właściwości ścieżki pola, ponieważ działania wyjściem jest obiekt z wieloma właściwościami. W przeciwnym razie podczas wykonywania elementu runbook nie jest on próby uwierzytelnienia. Jest to, co jest potrzebne co najmniej do uwierzytelnienia elementu runbook przy użyciu konta Uruchom jako.

Aby zachować zgodność z poprzednimi wersjami dla subskrybentów, którzy utworzyli konto usługi Automation przy użyciu [konta użytkownika usługi Azure AD](automation-create-aduser-account.md) Zarządzanie klasycznym wdrożeniu platformy Azure lub zasobów usługi Azure Resource Manager, metody uwierzytelniania Dodaj-AzureAccount użyj polecenia cmdlet z [zasób poświadczeń](automation-credentials.md) reprezentujący użytkownika usługi Active Directory z dostępem do konta platformy Azure.

Aby dodać tę funkcjonalność do graficznego elementu runbook, dodając zasób poświadczeń do kanwy, następuje działanie Add-AzureAccount. Dodaj-AzureAccount używa działania poświadczeń dla danych wejściowych. Jest to zilustrowane w poniższym przykładzie:

![Działania uwierzytelniania](media/automation-graphical-authoring-intro/authentication-activities.png)

Musisz uwierzytelnić się na początku elementu runbook i po nim każdy punkt kontrolny. Oznacza to, dodając działanie dodawania Add-AzureAccount po dowolnej działanie Checkpoint-Workflow. Nie ma potrzeby działanie dodawania poświadczeń, ponieważ może używać tego samego

![Wyjście działania](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Element Runbook, danych wejściowych i wyjściowych

### <a name="runbook-input"></a>Dane wejściowe elementu Runbook

Element runbook może wymagać dane wejściowe od użytkownika podczas uruchamiania elementu runbook za pomocą witryny Azure portal lub z innego elementu runbook, jeśli bieżący klucz jest używany jako element podrzędny.
Na przykład jeśli element runbook, który umożliwia utworzenie maszyny wirtualnej, konieczne może być udostępniają informacje takie jak nazwa maszyny wirtualnej i inne właściwości każdorazowo, uruchamianie elementu runbook.

Akceptuje dane wejściowe dla elementu runbook przez zdefiniowanie jednego lub więcej parametrów wejściowych. Możesz podać wartości dla parametrów każdorazowo, gdy element runbook jest uruchomiony. Po uruchomieniu elementu runbook za pomocą witryny Azure portal, monituje o podanie wartości dla każdego elementu runbook parametry wejściowe.

Parametry wejściowe elementu runbook można skorzystać, klikając **dane wejściowe i wyjściowe** przycisk na pasku narzędzi elementu runbook.

Spowoduje to otwarcie **danych wejściowych i wyjściowych** formant, który umożliwia edytowanie istniejącego parametr wejściowy lub Utwórz nowe, klikając **Dodaj dane wejściowe**.

![Dodaj dane wejściowe](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Każdy parametr wejściowy jest zdefiniowana za pomocą właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| Name (Nazwa) |Unikatowa nazwa parametru. To może zawierać tylko alfanumeryczne znaki numeryczne i nie może zawierać spacji. |
| Opis |Opcjonalny opis dla parametru wejściowego. |
| Type |Oczekiwano wartości parametru typu danych. Azure portal udostępnia kontrolkę, która jest odpowiednie dla typu danych dla każdego parametru przy monitowaniu o dane wejściowe. |
| Obowiązkowy |Określa, czy należy podać wartość dla parametru. Nie można uruchomić elementu runbook, jeśli nie zostanie określona wartość dla każdego obowiązkowy parametr, który nie ma wartości domyślnej. |
| Wartość domyślna |Określa, jaka wartość jest używana dla parametru, jeśli nie zostało ono określone. Może to być wartość Null lub określoną wartość. |

### <a name="runbook-output"></a>Wynik uruchomienia elementu Runbook

Dane utworzone przez wszystkie działania, które nie mają łącza wychodzącego są zapisywane do [dane wyjściowe elementu runbook](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). Dane wyjściowe są zapisywane przy użyciu zadania elementu runbook i jest dostępny dla nadrzędnego elementu runbook, gdy element runbook jest używany jako element podrzędny.

## <a name="powershell-expressions"></a>Wyrażenia programu PowerShell

Jedną z zalet tworzenia elementów graficznych oferuje Ci możliwość tworzenia elementu runbook przy minimalnej znajomości tworzonych programu PowerShell. Obecnie trzeba wiedzieć bitowej programu PowerShell, jednak w celu wypełnienia pewnych [wartości parametrów](#activities) i ustawienia [połączonych warunków](#links-and-workflow). Ta sekcja zawiera krótkie wprowadzenie do wyrażenia programu PowerShell dla tych użytkowników, którzy nie mogą już być znane go. Pełne informacje na temat programu PowerShell są dostępne pod adresem [skryptów za pomocą programu Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Źródło danych wyrażenia programu PowerShell
Można użyć wyrażenia programu PowerShell jako źródło danych do wypełnienia wartości [parametru działania](#activities) z wynikami kodu programu PowerShell. Może to być pojedynczy wiersz kodu, który wykonuje niektóre prostej funkcji lub wiele wierszy, które wykonują niektórych złożonej logiki. Wszelkie dane wyjściowe z polecenia, który nie jest przypisany do zmiennej są kierowane do wartości parametru.

Na przykład następujące polecenie spowoduje Wyświetla bieżącą datę.

Na przykład następujące polecenie spowoduje Wyświetla bieżącą datę.

```powershell-interactive
Get-Date
```

Następujące polecenia kompilacji ciąg z bieżącą datą i przypisz go do zmiennej. Zawartość zmiennej są następnie wysyłane do wyjścia

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Następujące polecenia oceń bieżącą datę i zwraca ciąg wskazujący, czy bieżący dzień jest weekend lub dnia tygodnia.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Wyjście działania

Aby użyć danych wyjściowych z wcześniejszego działania w elemencie runbook, należy użyć zmiennej $ActivityOutput przy użyciu następującej składni.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Na przykład masz działanie z właściwością, która wymaga nazwy maszyny wirtualnej, w którym to przypadku można użyć następującego wyrażenia:

```powershell-interactive
$ActivityOutput['Get-AzureVm'].Name
```

Zamiast tylko właściwości obiektu właściwość, która jest wymagana maszyna wirtualna będzie zwracać cały obiekt przy użyciu następującej składni.

```powershell-interactive
$ActivityOutput['Get-AzureVm']
```

Dane wyjściowe działania można również użyć w bardziej złożonych wyrażeń podobny do następującego łączącej tekst nazwę maszyny wirtualnej.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVm'].Name
```

### <a name="conditions"></a>Warunki

Użyj [operatory porównania](https://technet.microsoft.com/library/hh847759.aspx) do porównywania wartości lub określić, czy wartość odpowiada określonemu wzorcowi. Porównanie zwraca wartość $true lub $false.

Na przykład, następujący warunek określa, czy maszyna wirtualna z działania o nazwie *Get-AzureVM* jest obecnie *zatrzymana*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

Następujące warunek sprawdza, czy tej samej maszyny wirtualnej jest inny niż w dowolnym stanie *zatrzymana*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Możesz dołączyć wiele warunków przy użyciu [operatora logicznego](https://technet.microsoft.com/library/hh847789.aspx) takich jak **— i** lub **— lub**. Na przykład, następujące warunek sprawdza, czy tej samej maszyny wirtualnej w poprzednim przykładzie, jest w stanie *zatrzymana* lub *zatrzymywanie*.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Tabele

[Tabele](https://technet.microsoft.com/library/hh847780.aspx) są pary nazwa/wartość, które przydają się do zwracania zestaw wartości. Właściwości dla pewnych działań może oczekiwać hashtable zamiast prostych wartości. Może być też widoczny jako tablica skrótów, określany jako słownik.

Można utworzyć obiektu hashtable przy użyciu następującej składni. Tablica skrótów może zawierać dowolną liczbę wpisów, ale każdy jest definiowana przez nazwę i wartość.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Na przykład poniższe wyrażenie tworzy tablicę skrótów do użytku w źródle danych parametru działania, oczekiwany hashtable wartościami dla wyszukiwania w Internecie.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

W poniższym przykładzie użyto danych wyjściowe działania o nazwie *Uzyskaj połączenie w usłudze Twitter* do wypełnienia tablicy skrótów.

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
* Aby zrozumieć sposób uwierzytelniania przy użyciu konta Uruchom jako usługi Automation, zobacz [skonfigurować konto platformy Azure Uruchom jako](automation-sec-configure-azure-runas-account.md)
