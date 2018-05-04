---
title: Graficzny tworzenia w programie usługi Automatyzacja Azure
description: Tworzenia graficznego służy do tworzenia elementów runbook automatyzacji Azure bez Praca z kodem. Ten artykuł zawiera wprowadzenie do tworzenia graficznego i wszelkie informacje potrzebne do uruchomienia tworzenia graficznego elementu runbook.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 42187def32ea0f0605b50e0e25b7f78ebf0c10ac
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="graphical-authoring-in-azure-automation"></a>Graficzny tworzenia w programie usługi Automatyzacja Azure
## <a name="introduction"></a>Wprowadzenie
Tworzenie graficznych służy do tworzenia elementów runbook automatyzacji Azure bez złożoności kodu programu Windows PowerShell lub przepływ pracy programu PowerShell. Dodawanie działań do obszaru roboczego z biblioteki poleceń cmdlet i elementy runbook, je połączyć ze sobą i skonfigurować w celu utworzenia przepływu pracy. Jeśli kiedykolwiek mający doświadczenie z programu System Center Orchestrator lub automatyzacji zarządzania usługi (SMA), następnie to powinna wyglądać znajomo do użytkownika

Ten artykuł zawiera wprowadzenie do tworzenia graficznego i koncepcje potrzebne do rozpoczęcia tworzenia graficznego elementu runbook.

## <a name="graphical-runbooks"></a>Graficznych elementów runbook
Wszystkie elementy runbook w automatyzacji Azure są przepływów pracy programu Windows PowerShell. Graficzne i przepływ pracy programu PowerShell graficznego elementów runbook do generowania kodu programu PowerShell jest uruchamiany przez pracowników automatyzacji, ale nie jest możliwe do wyświetlania go lub go bezpośrednio modyfikować. Graficzny element runbook można przekonwertować na element runbook przepływu pracy programu PowerShell graficznego i odwrotnie, ale nie można przekonwertować na tekstowy. Nie można zaimportować istniejący element runbook tekstowy do edytora graficznego.

## <a name="overview-of-graphical-editor"></a>Omówienie edytora graficznego usługi
Przez utworzenie lub edycję graficznego elementu runbook można otworzyć edytora graficznego w portalu Azure.

![Graficzny obszaru roboczego](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

W poniższych sekcjach opisano formantów w edytorze graficznego.

### <a name="canvas"></a>Kanwy
Obszar roboczy jest gdzie projektowania elementu runbook. Dodaj działania z węzłów w formancie biblioteki do elementu runbook i połączyć je z łączami do definiowania logiki elementu runbook.

Formanty w dolnej części obszaru roboczego umożliwia powiększać i pomniejszać.

### <a name="library-control"></a>Formant biblioteki
Formant biblioteki to, gdzie wybierz [działania](#activities) można dodać do elementu runbook. Dodaj je do obszaru roboczego, w którym podłącz je do innych działań. Zawiera cztery sekcje opisane w poniższej tabeli:

| Sekcja | Opis |
|:--- |:--- |
| Polecenia cmdlet |Obejmuje wszystkie polecenia cmdlet, które mogą być używane w elemencie runbook. Polecenia cmdlet są zorganizowane według modułu. Dostępne są wszystkie moduły, które zostały zainstalowane na Twoim koncie automatyzacji. |
| Elementy Runbook |Zawiera elementy runbook na Twoim koncie automatyzacji. Te elementy runbook można dodać do obszaru roboczego ma być używany jako podrzędne elementy runbook. Wyświetlane są tylko elementy runbook tego samego typu core jako runbook edytowany; Graficzne elementy runbook tylko opartych na środowisku PowerShell w elementach runbook są pokazane, gdy przepływ pracy programu PowerShell graficznego elementów runbook są wyświetlane tylko przepływ pracy opartych na środowisku PowerShell elementów runbook. |
| Elementy zawartości |Obejmuje [zasoby automatyzacji](http://msdn.microsoft.com/library/dn939988.aspx) na koncie automatyzacji, który może być używana w elemencie runbook. Po dodaniu zasobów do elementu runbook dodaje działania przepływu pracy, który pobiera wybranych zasobów. W przypadku aktywów zmiennej można wybrać, czy dodać działanie do pobierania zmiennej lub ustaw zmienną. |
| Sterowanie elementem Runbook |Obejmuje działania kontroli elementu runbook, które mogą być używane w bieżącym elemencie runbook. A *Rozgałęzienie* przyjmuje wielu danych wejściowych i czeka, aż wszystkie została ukończona przed kontynuowaniem przepływ pracy. A *kod* działanie jest uruchamiane jeden lub więcej wierszy kodu programu PowerShell lub przepływ pracy programu PowerShell, w zależności od typu graficzny element runbook. To działanie można użyć niestandardowego kodu lub funkcje, które są trudne do osiągnięcia z innymi działaniami. |

### <a name="configuration-control"></a>Kontrola konfiguracji
Kontrola konfiguracji jest, gdzie podać szczegóły dla obiekt wybrany w obszarze roboczym. Właściwości dostępne w tym formancie, zależy od typu wybranego obiektu. Po wybraniu opcji w formancie konfiguracji otwiera dodatkowe bloki w celu zapewnienia dodatkowych informacji.

### <a name="test-control"></a>Formant testu
Formant testu nie jest wyświetlany po pierwszym uruchomieniu edytora graficznego. Po otwarciu możesz interaktywnie [test graficznym elementem runbook](#graphical-runbook-procedures).

## <a name="graphical-runbook-procedures"></a>Procedury graficznym elementem runbook
### <a name="exporting-and-importing-a-graphical-runbook"></a>Eksportowanie i importowanie graficznego elementu runbook
Można wyeksportować tylko opublikowaną wersję elementu graficznego elementu runbook. Jeśli element runbook nie został opublikowany, a następnie **wyeksportować** przycisk jest niedostępny. Po kliknięciu **wyeksportować** przycisku, element runbook zostanie pobrana do komputera lokalnego. Nazwa pliku odpowiada nazwie elementu runbook z *graphrunbook* rozszerzenia.

Możesz zaimportować plik elementu runbook graficzny lub graficzny przepływ pracy programu PowerShell, wybierając **zaimportować** podczas dodawania elementu runbook. Po wybraniu plik do zaimportowania, można używać tego samego **nazwa** lub podaj nową. W polu Typ elementu Runbook zostanie wyświetlona typu element runbook, po jego ocenia wybrany plik i przy próbie wybierz inny typ, który nie jest prawidłowa, komunikat zostanie wyświetlone biorąc pod uwagę, istnieje ryzyko potencjalnych konfliktów i podczas konwersji, mogą wystąpić błędy składniowe.

![Importowanie elementu runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Testowanie graficznego elementu runbook
Można przetestować wersję roboczą elementu runbook w portalu Azure, pozostawiając opublikowaną wersję elementu runbook bez zmian lub można przetestować nowy element runbook, zanim został opublikowany. Dzięki temu można sprawdzić, czy element runbook działa poprawnie przed zastąpieniem opublikowanej wersji. Podczas testowania elementu runbook, wykonywane jest wersja robocza elementu runbook, a wszystkie akcje, które wykonuje zostały zakończone. Historia zadań nie zostało utworzone, ale dane wyjściowe są wyświetlane w okienku danych wyjściowych testu.

Otwórz kontroli testu dla elementu runbook, otwierając element runbook do edycji, a następnie kliknij polecenie **okienku testu** przycisku.

Monity kontroli testu dla parametrów wejściowych, a element runbook można uruchomić, klikając **Start** przycisku.

### <a name="publishing-a-graphical-runbook"></a>Publikowanie graficznego elementu runbook
Każdy element runbook automatyzacji Azure ma wersję roboczą i opublikowaną wersję. Opublikowana wersja jest dostępna do uruchamiania i tylko wersję roboczą można edytowane. Wersję opublikowaną nie mają wpływu żadne zmiany wprowadzone w wersji roboczej. Gdy wersja robocza jest gotowa do udostępnienia, należy ją opublikować, co powoduje nadpisanie wersji opublikowanej wersją roboczą.

Możesz opublikować graficznego elementu runbook, otwierając element runbook w celu edycji, a następnie klikając **publikowania** przycisku.

Element runbook nie został opublikowany, ma stan **nowy**. Po opublikowaniu ma stan **opublikowano**. Jeśli po opublikowaniu i są różne wersje roboczą i opublikowaną można edytować element runbook, element runbook ma stan **w edycji**.

![Stany elementu Runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Masz również opcję, aby przywrócić opublikowanej wersji elementu runbook. Optymalizacji zgłasza wszystkie zmiany wprowadzone od ostatniej publikacji elementu runbook i zastępuje wersję opublikowaną wersję roboczą elementu runbook.

## <a name="activities"></a>Działania
Działania są blokami konstrukcyjnymi elementu runbook. Działanie może być polecenia cmdlet programu PowerShell, podrzędnego elementu runbook lub działania przepływu pracy. Dodawanie działania elementu runbook przez kliknięcie prawym przyciskiem myszy w formancie biblioteki i wybierając **Dodaj do kanwy**. Można następnie kliknij i przeciągnij działanie, aby umieścić w dowolnym miejscu w obszarze roboczym, który chcesz. Lokalizacja działania na kanwie nie ma wpływu na działanie elementu runbook w dowolny sposób. Układ z elementem runbook jednak jest on najbardziej odpowiedni do wizualizacji jej działania.

![Dodaj do kanwy](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Wybierz działanie, w obszarze roboczym, aby skonfigurować ich właściwości i parametrów w bloku konfiguracji. Możesz zmienić **etykiety** aktywności inny, która ma charakter opisowy dla Ciebie. Nadal trwa oryginalnego polecenia cmdlet, po prostu zmieniasz jego nazwę wyświetlaną, która jest używana w edytorze graficznego. Etykieta musi być unikatowa w obrębie elementu runbook.

### <a name="parameter-sets"></a>Zestawy parametrów
Zestaw parametrów definiuje parametry obowiązkowe i opcjonalne, które akceptują wartości dla określonego polecenia cmdlet. Wszystkie polecenia cmdlet zawierać co najmniej jeden zestaw parametrów, a niektóre wiele. Jeśli polecenie cmdlet ma wiele zestawów parametrów, następnie należy wybrać która z nich korzystać, aby można było skonfigurować parametrów. Parametry, które można skonfigurować zależy od możesz wybrać zestaw parametrów. Można zmienić zestaw parametrów, używany w działaniu, wybierając **ustawić parametr** i wybierając inny zestaw. W takim przypadku wszystkie wartości parametrów, które skonfigurowano zostaną utracone.

W poniższym przykładzie polecenia cmdlet Get-AzureRmVM ma trzy zestawów parametrów. Nie można skonfigurować wartości parametrów, dopiero po wybraniu zestawów parametrów. Zestaw parametrów ListVirtualMachineInResourceGroupParamSet służy do zwracania wszystkich maszyn wirtualnych w grupie zasobów i ma jeden parametr opcjonalny. **GetVirtualMachineInResourceGroupParamSet** służy do określania maszyny wirtualnej, aby wrócić i ma dwa obowiązkowe i jeden parametr opcjonalny.

![Zestaw parametrów](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Wartości parametrów
Podczas określania wartości dla parametru wybraniu źródła danych, aby określić, jak określona wartość. Źródła danych, które są dostępne dla określonego parametru zależy prawidłowe wartości tego parametru. Na przykład wartość Null nie jest dostępna opcja dla parametru, który nie zezwala na wartości null.

| Źródło danych | Opis |
|:--- |:--- |
| Stała wartość |Wpisz wartość dla parametru. To jest dostępna tylko dla następujących typów danych: Int32, Int64, String, Boolean, DateTime, przełącznika. |
| Dane wyjściowe działania |Dane wyjściowe działania poprzedzający bieżące działanie w przepływie pracy. Wszystkie prawidłowe działania są wyświetlane. Wybierz tylko działanie, aby użyć jego dane wyjściowe dla wartości parametru. Jeśli działanie generuje obiekt o wiele właściwości, można wpisać nazwę właściwości po wybraniu działania. |
| Dane wejściowe elementu Runbook |Wybierz parametr wejściowy elementu runbook jako dane wejściowe do parametru działania. |
| Zasób zmiennej |Wybierz zmiennej automatyzacji jako dane wejściowe. |
| Zasób poświadczeń |Wybierz poświadczenie automatyzacji jako dane wejściowe. |
| Zasób certyfikatu |Wybierz certyfikat usługi Automatyzacja jako dane wejściowe. |
| Zasób połączenia |Wybierz połączenie automatyzacji jako dane wejściowe. |
| Wyrażenie programu PowerShell |Określ prosty [wyrażenie programu PowerShell](#powershell-expressions). Wyrażenie jest obliczane przed działania i wynik używany dla wartości parametru. Aby odwołać się do danych wyjściowych działania lub parametr wejściowy elementu runbook można używać zmiennych. |
| Nieskonfigurowane |Czyści żadnej wartości, który został wcześniej skonfigurowany. |

#### <a name="optional-additional-parameters"></a>Dodatkowe parametry opcjonalne
Wszystkie polecenia cmdlet mieć opcję, aby zapewnić dodatkowe parametry. Są to typowe parametry programu PowerShell lub inne parametry niestandardowe. Jest wyświetlane pole tekstowe, w którym można podać parametry, używając składni programu PowerShell. Na przykład, aby użyć **pełne** wspólnego parametru należy określić **"-Verbose: $True"**.

### <a name="retry-activity"></a>Ponów próbę wykonania działania
**Sposób ponawiania próby** umożliwia działanie należy uruchamiać wielokrotnie, dopóki nie zostanie spełniony określony warunek, podobnie jak pętli. Tej funkcji można używać działań, które należy uruchamiać wielokrotnie, są podatne na błąd i może wymagają więcej niż jeden prób w przypadku powodzenia lub testowanie informacji wyjściowych działania na prawidłowe dane.

Po włączeniu retry działania można ustawić opóźnienie i warunek. Opóźnienie to czas (mierzony w sekundach lub minutach) czy element runbook czeka przed jej uruchomieniem działania. Jeśli opóźnienie nie zostanie określony, działanie zostanie uruchomione ponownie natychmiast po jej zakończeniu.

![Opóźnienie ponownych prób działań](media/automation-graphical-authoring-intro/retry-delay.png)

Warunek ponownych prób jest wyrażenie programu PowerShell, które jest oceniane po każdym uruchomieniu działania. Jeśli wyrażenie jest rozpoznawana jako wartość True, następnie działanie zostanie uruchomione ponownie. Jeśli wyrażenie jest rozpoznawana jako wartość False, działanie nie uruchomi ponownie, a element runbook przechodzi do następnego działania.

![Opóźnienie ponownych prób działań](media/automation-graphical-authoring-intro/retry-condition.png)

Warunku ponawiania można użyć zmiennej o nazwie $RetryData zapewniający dostęp do informacji na temat działania liczbę ponownych prób. Ta zmienna ma właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| NumberOfAttempts |Liczba przypadków systemem działania. |
| Dane wyjściowe |Dane wyjściowe z ostatniego uruchomienia działania. |
| TotalDuration |Przekroczono czasu, jaki upłynął od czasu uruchomienia działania po raz pierwszy. |
| StartedAt |Godzina w formacie UTC, najpierw uruchomienia działania. |

Poniżej przedstawiono przykłady działania ponawiania próby warunki.

    # Run the activity exactly 10 times.
    $RetryData.NumberOfAttempts -ge 10

    # Run the activity repeatedly until it produces any output.
    $RetryData.Output.Count -ge 1

    # Run the activity repeatedly until 2 minutes has elapsed.
    $RetryData.TotalDuration.TotalMinutes -ge 2

Po skonfigurowaniu warunek ponów próbę wykonania działania zawiera dwa wizualnych przypomnienia. Jeden są prezentowane w działaniu, a drugi to, gdy Sprawdź konfigurację działania.

![Wskaźniki Visual ponów próbę wykonania działania](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Sterowanie przepływem pracy skryptu
Kontroli kodu jest specjalnym działaniu, które akceptuje skrypt programu PowerShell lub przepływ pracy programu PowerShell w zależności od typu graficzny element runbook jest utworzone w celu zapewnienia funkcji, które w przeciwnym razie jest dostępna. Nie można zaakceptować parametrów, ale może użyć zmiennych dla działania danych wyjściowych i runbook parametrów wejściowych. Wszystkie dane wyjściowe działania jest dodawany w magistrali danych, jeśli nie ma on nie wychodzące link w takim przypadku jest ona dodawana do danych wyjściowych elementu runbook.

Na przykład następujący kod wykonuje obliczenia daty przy użyciu zmiennej wejściowe elementu runbook o nazwie $NumberOfDays. Wysyła następnie obliczeniowej godzina jako dane wyjściowe mają być używane przez kolejne działania w elemencie runbook.

    $DateTimeNow = (Get-Date).ToUniversalTime()
    $DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
    $DateTimeStart


## <a name="links-and-workflow"></a>Łącza i przepływu pracy
A **łącze** w graficznym elementem runbook łączy dwa działania. Jest on wyświetlany na kanwie jako strzałka z działania źródłowego do działania docelowego. Działania Uruchom w kierunku strzałkę z działania docelowego uruchamianie po zakończeniu działania źródłowego.

### <a name="create-a-link"></a>Utwórz łącze
Utwórz łącze między dwa działania, wybierając działania źródłowego, a kliknięcie okręgu w dolnej części kształtu. Przeciągnij strzałkę do działania docelowego i wersji.

![Utwórz łącze](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Wybierz łącze, aby skonfigurować jego właściwości w bloku konfiguracji. Obejmuje to typ łącza, które zostało opisane w poniższej tabeli:

| Typ łącza | Opis |
|:--- |:--- |
| Potok |Działanie docelowe jest uruchamiane raz dla każdej dane wyjściowe obiektu z działania źródłowego. Działanie docelowe nie jest uruchamiany, jeśli działania źródłowego nie powoduje żadnych danych wyjściowych. Dane wyjściowe z działania źródłowego jest dostępna jako obiekt. |
| Sekwencja |Działanie docelowe jest uruchamiane tylko jeden raz. Odbiera tablicę obiektów z działania źródłowego. Dane wyjściowe z działania źródłowego jest dostępna jako tablica obiektów. |

### <a name="starting-activity"></a>Działanie początkowe
Graficznego elementu runbook rozpoczyna się od żadnych działań, które nie mają łączy przychodzących. Często jest tylko jedno działanie, które będą działać jako działanie początkowe dla elementu runbook. Jeśli wielu działań nie ma łącze przychodzących, element runbook zostanie uruchomiony uruchamiając równolegle. Wynika z tego łącza, aby uruchomić inne działania, po zakończeniu każdej.

### <a name="conditions"></a>Warunki
Po określeniu warunku łącza, działania docelowego jest uruchamiane tylko, gdy warunek jest rozpoznawana jako true. Zwykle użyć zmiennej $ActivityOutput w stanie pobrać dane wyjściowe z działania źródłowego.

Linku potoku należy określić warunek dla pojedynczego obiektu, a obliczania warunku dla każdego obiektu danych wyjściowych przez działania źródłowego. Działanie docelowe jest następnie uruchom dla każdego obiektu, który spełnia warunek. Na przykład z działania źródłowego z Get AzureRmVm, następującej składni może służyć do łącza warunkowe potoku można pobrać tylko maszyny wirtualne w grupie zasobów o nazwie *grupa1*.

    $ActivityOutput['Get Azure VMs'].Name -match "Group1"

Łącza sekwencji jest tylko wynikiem obliczania warunku raz od pojedynczej tablicy jest zwracany zawierający wszystkie obiekty dane wyjściowe działania źródłowego. W związku z tym łącze sekwencji nie może służyć do filtrowania jak łącze potoku, ale po prostu określić, czy jest uruchomienie następnego działania. Przyjmować na przykład następujący zestaw działań w naszym runbook uruchamianie maszyny Wirtualnej.<br> ![Łączy warunkowych sekwencja](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)<br>
Istnieją trzy łącza inną sekwencję, które sprawdzania dostarczonych wartości do dwóch parametrów wejściowych runbook reprezentujący nazwę maszyny Wirtualnej i nazwa grupy zasobów w celu ustalenia, które jest odpowiednie działanie podejmowane — uruchamianie jednej maszyny Wirtualnej, uruchom wszystkie maszyny wirtualne w zasobie Grupa, lub wszystkich maszyn wirtualnych w ramach subskrypcji. Łącza sekwencji między Connect do platformy Azure i Get jednej maszyny Wirtualnej w tym miejscu jest logiki warunek:

    <#
    Both VMName and ResourceGroupName runbook input parameters have values
    #>
    (
    (($VMName -ne $null) -and ($VMName.Length -gt 0))
    ) -and (
    (($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
    )

Korzystając z łączy warunkowych, dostępne z działania źródłowego do innych działań w oddziale dane są filtrowane według warunku. Działanie w przypadku źródła do wielu łączy, dane dostępne do działań w każdej gałęzi są zależne od warunku łącza nawiązywania połączenia z tym gałęzi.

Na przykład **Start AzureRmVm** działanie w elemencie runbook poniżej uruchamiania wszystkich maszyn wirtualnych. Składa się z dwóch łączy warunkowych. Pierwszy łączy warunkowych używa wyrażenia *$ActivityOutput ["AzureRmVM Start"]. IsSuccessStatusCode - eq $true* do filtrowania, jeśli działanie Start AzureRmVm ukończone pomyślnie. Drugi używa wyrażenia *$ActivityOutput ["AzureRmVM Start"]. IsSuccessStatusCode - ne $true* do filtrowania, jeśli działanie Start AzureRmVm nie można uruchomić maszyny wirtualnej.

![Przykład łączy warunkowych](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Wszystkie działania następujący pierwszy link i używa dane wyjściowe działania Get-AzureVM pobierze tylko maszyn wirtualnych, które zostały uruchomione w momencie uruchomienia Get AzureVM. Wszystkie działania drugi link pobiera tylko maszyn wirtualnych, które zostały zatrzymane w momencie uruchomienia Get AzureVM. Wszystkie działania trzeci łącze pobiera wszystkich maszyn wirtualnych niezależnie od ich uruchomiona.

### <a name="junctions"></a>Skrzyżowania
Połączenie jest specjalnym działaniu, które oczekuje, aż wszystkie gałęzie przychodzące została ukończona. Dzięki temu można równolegle wielu działań i upewnij się, zostały ukończone wszystkie zmiany przed kontynuowaniem.

Natomiast Rozgałęzienie może mieć dowolną liczbę linki przychodzące, nie więcej niż jeden z tych linków można potoku. Nie jest ograniczona liczba łączy przychodzących sekwencji. Możesz utworzyć połączenie z wielu łączy przychodzących potoku i zapisać elementu runbook, ale ulegnie awarii, gdy jest uruchomiony.

W poniższym przykładzie jest częścią elementu runbook, który uruchamia zestaw maszyn wirtualnych podczas pobierania jednocześnie poprawki, które ma zostać zastosowany do tych maszyn. Rozgałęzienie służy do upewnij się, że oba procesy zostały zakończone przed kontynuowaniem elementu runbook.

![Rozgałęzienie](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cykle
Cykl jest podczas działania docelowego w łącza do jego działania źródłowego lub innego działania, który ostatecznie łącza z powrotem do źródła. Cykle obecnie nie są dozwolone w tworzenia graficznego. Jeśli element runbook ma cykl, zapisuje właściwie, ale odbiera wystąpił błąd podczas uruchamiania.

![Cykl](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Udostępnianie danych między działaniami
Wszystkie dane, które są danymi wyjściowymi działania łącza wychodzące są zapisywane do *magistrali danych* dla elementu runbook. Wszystkie działania w elemencie runbook można użyć danych na magistrali danych lub wypełnić wartości parametrów w kodzie skryptu. Działanie ma dostęp do danych wyjściowych wszelkie poprzednie działanie w przepływie pracy.

Jak dane są zapisywane w magistrali danych zależy od typu łącza w działaniu. Aby uzyskać **potoku**, dane są dane wyjściowe jako obiekty wielokrotności. Aby uzyskać **sekwencji** przekazywane jako tablica są łącza, dane. Jeśli istnieje tylko jedna wartość, jest wyświetlany jako pojedynczy element tablicy.

Można uzyskać dostępu do danych na magistrali danych, przy użyciu jednej z dwóch metod. Najpierw używa **dane wyjściowe działania** źródła danych, aby wypełnić parametr innego działania. Dane wyjściowe w przypadku obiektu, można określić jedną właściwość.

![Dane wyjściowe działania](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Można również pobierać dane wyjściowe działania w **wyrażenie programu PowerShell** źródła danych lub **skrypt przepływu pracy** działania ze zmienną ActivityOutput. Dane wyjściowe w przypadku obiektu, można określić jedną właściwość. Zmienne ActivityOutput należy użyć następującej składni.

    $ActivityOutput['Activity Label']
    $ActivityOutput['Activity Label'].PropertyName

### <a name="checkpoints"></a>Punkty kontrolne
Można ustawić [punktów kontrolnych](automation-powershell-workflow.md#checkpoints) w elemencie runbook graficzny przepływ pracy programu PowerShell, wybierając *runbook punktu kontrolnego* na żadnych działań. Powoduje to, że punkt kontrolny można ustawić po uruchomieniu działania.

![Punkt kontrolny](media/automation-graphical-authoring-intro/set-checkpoint.png)

Punkty kontrolne są włączyć tylko w elementach runbook graficzny przepływ pracy programu PowerShell, nie jest dostępna w graficznych elementów runbook. Jeśli element runbook używa poleceń cmdlet systemu Azure, należy wykonać czynność użyciu o AzureRmAccount Connect w przypadku, gdy element runbook został wstrzymany i uruchamia ponownie z tego punktu kontrolnego na inny proces roboczy.

## <a name="authenticating-to-azure-resources"></a>Uwierzytelniania do zasobów platformy Azure
Elementy Runbook automatyzacji Azure, które zarządzania zasobami Azure wymaga uwierzytelniania na platformie Azure. [Konta Uruchom jako](automation-offering-get-started.md#creating-an-automation-account) (określane również jako nazwy głównej usługi) jest domyślna metoda dostępu do zasobów usługi Azure Resource Manager w ramach subskrypcji z elementu runbook usługi Automatyzacja. Tę funkcję można dodać do graficznego elementu runbook, dodając **AzureRunAsConnection** trwałego połączenia, który używa programu PowerShell [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) polecenia cmdlet i [ Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) polecenia cmdlet do obszaru roboczego. Jest to zilustrowane w poniższym przykładzie:<br>![Uruchom jako działania uwierzytelniania](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)<br>
Działanie Pobierz Uruchom jako połączenia (to znaczy Get-AutomationConnection), jest skonfigurowany z wartości stałej źródła danych o nazwie AzureRunAsConnection.<br>![Konfiguracja połączenia Uruchom jako](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)<br>
Następne działanie Connect-AzureRmAccount dodaje uwierzytelnionego konta Uruchom jako do użycia w elemencie runbook.<br>
![Zestaw parametrów połączenia AzureRmAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)<br>
Dla parametrów **APPLICATIONID**, **CERTIFICATETHUMBPRINT**, i **TENANTID** należy określić nazwę właściwości dla ścieżki pola, ponieważ działanie generuje obiekt o wiele właściwości. W przeciwnym razie podczas wykonywania elementu runbook nie jest on próby uwierzytelnienia. Jest to, co jest potrzebne co najmniej do uwierzytelniania przy użyciu konta Uruchom jako element runbook.

Aby zachować zgodność z poprzednimi wersjami dla subskrybentów, którzy utworzyli konto automatyzacji za pomocą [konta usługi Azure AD](automation-create-aduser-account.md) Aby zarządzać wdrażaniem klasycznego Azure lub dla zasobów usługi Azure Resource Manager metody uwierzytelniania to polecenie cmdlet Add-AzureAccount z [zasób poświadczeń](automation-credentials.md) reprezentujący użytkownika usługi Active Directory z dostępem do konta platformy Azure.

Tę funkcję można dodać do graficznego elementu runbook, dodając zasób poświadczeń do obszaru roboczego następuje działanie Add-AzureAccount. Dodaj AzureAccount używa działania poświadczeń dla jej danych wejściowych. Jest to zilustrowane w poniższym przykładzie:

![Działania uwierzytelniania](media/automation-graphical-authoring-intro/authentication-activities.png)

Masz uwierzytelniania na początku elementu runbook i po każdym punktu kontrolnego. Oznacza to, dodawanie działanie dodania Add-AzureAccount po żadnego działania Checkpoint-Workflow. Nie ma potrzeby działanie dodawania poświadczeń, ponieważ można używać tego samego

![Dane wyjściowe działania](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Element Runbook wejściowe i wyjściowe
### <a name="runbook-input"></a>Dane wejściowe elementu Runbook
Element runbook może wymagać danych wejściowych od użytkownika podczas uruchamiania elementu runbook za pomocą portalu Azure lub z innego elementu runbook, jeśli bieżący jest używany jako element podrzędny.
Na przykład jeśli element runbook, który tworzy maszynę wirtualną, może być konieczne Podaj informacje takie jak nazwa maszyny wirtualnej i innych właściwości każdego czas uruchamiania elementu runbook.

Akceptuje dane wejściowe dla elementu runbook przez zdefiniowanie jednego lub więcej parametrów wejściowych. Należy podać wartości parametrów każdym uruchomieniu elementu runbook. Po uruchomieniu elementu runbook w portalu Azure monituje o podanie wartości dla każdego parametry wejściowe elementu runbook.

Parametry wejściowe elementu runbook można uzyskać, klikając **dane wejściowe i wyjściowe** przycisk na pasku narzędzi elementu runbook.

Spowoduje to otwarcie **wejściowa i wyjściowa** kontrolki, w którym można edytować istniejące parametr wejściowy lub Utwórz nowy, klikając **dodać dane wejściowe**.

![Dodawanie danych wejściowych](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Każdy parametr wejściowy jest zdefiniowane przez właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| Name (Nazwa) |Unikatowa nazwa parametru. To może zawierać tylko znaków alfanumerycznych i nie może zawierać spacji. |
| Opis |Opcjonalny opis parametru wejściowego. |
| Typ |Oczekiwano wartości parametru typu danych. Azure portal udostępnia właściwej opcji kontroli dla typu danych dla każdego parametru monitując o dane wejściowe. |
| Obowiązkowy |Określa, czy należy podać wartość parametru. Nie można uruchomić elementu runbook, jeśli nie zostanie określona wartość dla każdego obowiązkowy parametr, który nie ma zdefiniowanej wartości domyślnej. |
| Wartość domyślna |Określa, jakie korzyści jest używane dla parametru, jeśli nie podano. To może być wartością Null lub określoną wartość. |

### <a name="runbook-output"></a>Wynik uruchomienia elementu Runbook
Dane utworzone przez działalności, która nie ma łącze wychodzących [danych wyjściowych elementu runbook](http://msdn.microsoft.com/library/azure/dn879148.aspx). Dane wyjściowe z zadania elementu runbook jest zapisywana i jest dostępny dla nadrzędnego elementu runbook, gdy element runbook jest używany jako element podrzędny.

## <a name="powershell-expressions"></a>Wyrażenia programu PowerShell
Jedną z zalet tworzenia graficznego jest zapewnia możliwość tworzenia elementu runbook przy minimalnej znajomości programu PowerShell. Obecnie trzeba wiedzieć bitowej programu PowerShell, chociaż w celu wypełnienia pewnych [wartości parametrów](#activities) i ustawienia [połączonych warunków](#links-and-workflow). W tej sekcji przedstawiono krótkie wprowadzenie do programu PowerShell wyrażeń dla tych użytkowników, którzy mogą nie być zapoznać się z nim. Szczegółowe informacje dotyczące programu PowerShell są dostępne pod adresem [skryptów programu Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Źródło danych wyrażenie programu PowerShell
Wyrażenie programu PowerShell jako źródło danych służy do wypełniania wartość [parametru działania](#activities) z wynikami kodu programu PowerShell. Może to być pojedynczy wiersz kodu, który wykonuje niektórych funkcji prostego lub wiele wierszy, które wykonują niektóre złożonej logiki. Wszystkie dane wyjściowe polecenia, który nie jest przypisany do zmiennej przekazywane są do wartości parametru.

Na przykład następujące polecenie spowoduje Wyświetla bieżącą datę.

    Get-Date

Poniższe polecenia Tworzenie ciągu z bieżącą datą i przypisz go do zmiennej. Zawartość zmiennej są następnie wysyłane do danych wyjściowych

    $string = "The current date is " + (Get-Date)
    $string

Poniższe polecenia oceny aktualnej daty i zwraca ciąg wskazujący, czy bieżący dzień weekendowy lub dzień tygodnia.

    $date = Get-Date
    if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
    else { "Weekday" }


### <a name="activity-output"></a>Dane wyjściowe działania
Aby użyć danych wyjściowych z wcześniejszego działania w elemencie runbook, należy użyć zmiennej $ActivityOutput przy użyciu następującej składni.

    $ActivityOutput['Activity Label'].PropertyName

Na przykład może mieć działania o właściwości, która wymaga nazwy maszyny wirtualnej w takim przypadku można użyć następującego wyrażenia:

    $ActivityOutput['Get-AzureVm'].Name

Właściwość, która jest wymagana maszyny wirtualnej obiekt zamiast tylko właściwości, czy zwracać całego obiektu, używając następującej składni.

    $ActivityOutput['Get-AzureVm']

Umożliwia także dane wyjściowe działania w wyrażeniu bardziej złożonych, takie jak wymienione poniżej, który łączy tekst, który ma nazwę maszyny wirtualnej.

    "The computer name is " + $ActivityOutput['Get-AzureVm'].Name


### <a name="conditions"></a>Warunki
Użyj [operatory porównania](https://technet.microsoft.com/library/hh847759.aspx) do porównywania wartości lub określić, jeśli wartość jest zgodna z określonym wzorcem. Porównanie zwraca wartość $true lub $false.

Na przykład następujący warunek określa, czy maszyny wirtualnej z działania o nazwie *Get-AzureVM* jest obecnie *zatrzymana*.

    $ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"

Następujące warunku sprawdza, czy w tej samej maszyny wirtualnej jest w stanie żadnych innych niż *zatrzymana*.

    $ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"

Można połączyć wiele warunków za pomocą [operatora logicznego](https://technet.microsoft.com/library/hh847789.aspx) takich jak **- i** lub **- lub**. Na przykład następujące warunek sprawdza, czy tę samą maszynę wirtualną w poprzednim przykładzie jest w stanie *zatrzymana* lub *zatrzymywanie*.

    ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")


### <a name="hashtables"></a>Obiektach HashTable
[Obiektach HashTable](http://technet.microsoft.com/library/hh847780.aspx) są pary nazwa/wartość, które są przydatne w przypadku zwracanie zestawu wartości. Właściwości dla niektórych działań może oczekiwać hashtable zamiast prostą wartością. Może również zostać wyświetlony jako hashtable określone w formie słownika.

Można utworzyć obiektu hashtable przy użyciu następującej składni. Tablica skrótów może zawierać dowolną liczbę wpisów, ale każda jest definiowana za pomocą nazwy i wartości.

    @{ <name> = <value>; [<name> = <value> ] ...}

Na przykład poniższe wyrażenie tworzy hashtable do użycia w źródle danych dla parametru działania oczekiwano hashtable z wartościami wyszukiwaniem w Internecie.

    $query = "Azure Automation"
    $count = 10
    $h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
    $h

W poniższym przykładzie użyto wyjściowymi działania o nazwie *uzyskać połączenia w usłudze Twitter* do wypełniania obiektu hashtable.

    @{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
      'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
      'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
      'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}



## <a name="next-steps"></a>Następne kroki
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell).
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
* Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
* Aby zrozumieć sposób uwierzytelniać za pomocą konta Uruchom jako automatyzacji, zobacz [skonfigurować Azure konta Uruchom jako](automation-sec-configure-azure-runas-account.md)

