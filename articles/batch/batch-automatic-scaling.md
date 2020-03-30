---
title: Automatyczne skalowanie węzłów obliczeniowych w puli usługi Azure Batch | Dokumenty firmy Microsoft
description: Włącz automatyczne skalowanie w puli chmury, aby dynamicznie dostosowywać liczbę węzłów obliczeniowych w puli.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: multiple
ms.date: 10/24/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017,fasttrack-edit
ms.openlocfilehash: 398b6d9c3fc05a6cf164b4003f57b94ecd6c1972
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054016"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Tworzenie automatycznej formuły skalowania węzłów obliczeniowych w puli wsadowej

Usługa Azure Batch może automatycznie skalować pule na podstawie parametrów zdefiniowanych. Dzięki automatycznemu skalowaniu usługa Batch dynamicznie dodaje węzły do puli w miarę zwiększania zapotrzebowania na zadania i usuwa węzły obliczeniowe wraz z ich zmniejszaniem. Można zaoszczędzić czas i pieniądze, automatycznie dostosowując liczbę węzłów obliczeniowych używanych przez aplikację Batch.

Automatyczne skalowanie puli węzłów obliczeniowych umożliwia automatyczne skalowanie przez skojarzenie z nią *zdefiniowanej formuły skalowania automatycznego.* Usługa Batch używa formuły skalowania automatycznego do określenia liczby węzłów obliczeniowych, które są potrzebne do wykonania obciążenia. Węzły obliczeniowe mogą być węzłami dedykowanymi lub [węzłami o niskim priorytecie.](batch-low-pri-vms.md) Batch odpowiada na dane metryki usługi, które są zbierane okresowo. Korzystając z tych danych metryk, usługa Batch dostosowuje liczbę węzłów obliczeniowych w puli na podstawie formuły i w konfigurowalnym odstępie czasu.

Skalowanie automatyczne można włączyć podczas tworzenia puli lub w istniejącej puli. Można również zmienić istniejącą formułę w puli skonfigurowanej do skalowania automatycznego. Usługa Batch umożliwia ocenę formuł przed przypisaniem ich do pul i monitorowanie stanu automatycznych przebiegów skalowania.

W tym artykule omówiono różne jednostki, które tworzą formuły skalowania automatycznego, w tym zmienne, operatory, operacje i funkcje. Omówimy sposób uzyskiwania różnych zasobów obliczeniowych i metryk zadań w ramach usługi Batch. Za pomocą tych metryk można dostosować liczbę węzłów puli na podstawie użycia zasobów i stanu zadania. Następnie opisano, jak skonstruować formułę i włączyć automatyczne skalowanie w puli przy użyciu interfejsów API batch REST i .NET. Na koniec kończymy z kilkoma przykładowymi formułami.

> [!IMPORTANT]
> Podczas tworzenia konta usługi Batch można określić [konfigurację konta,](batch-api-basics.md#account)która określa, czy pule są przydzielane w subskrypcji usługi wsadowej (domyślnie) lub w subskrypcji użytkownika. Jeśli konto usługi Batch zostało utworzone z domyślną konfiguracją usługi wsadowej, konto jest ograniczone do maksymalnej liczby rdzeni, które mogą być używane do przetwarzania. Usługa Batch skaluje węzły obliczeniowe tylko do tego limitu rdzenia. Z tego powodu usługa Batch może nie osiągnąć docelowej liczby węzłów obliczeniowych określonej przez formułę skalowania automatycznego. Aby uzyskać informacje na temat wyświetlania i zwiększania przydziałów konta, zobacz [Przydziały i limity dla usługi Azure Batch.](batch-quota-limit.md)
>
>Jeśli konto utworzono w konfiguracji Subskrypcja użytkownika, twoje konto współudzieli przydział podstawowy subskrypcji. Aby uzyskać więcej informacji, zobacz sekcję [Virtual Machines limits (Limity maszyn wirtualnych)](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) w artykule [Azure subscription and service limits, quotas, and constraints (Ograniczenia, przydziały i limity usług i subskrypcji platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md).
>
>

## <a name="automatic-scaling-formulas"></a>Automatyczne skalowanie formuł

Formuła skalowania automatycznego to zdefiniowana wartość ciągu zawierająca jedną lub więcej instrukcji. Formuła skalowania automatycznego jest przypisywana do elementu [autoscaleformula][rest_autoscaleformula] puli (Batch REST) lub [CloudPool.AutoScaleFormula(Batch][net_cloudpool_autoscaleformula] .NET). Usługa Batch używa formuły do określenia docelowej liczby węzłów obliczeniowych w puli dla następnego interwału przetwarzania. Ciąg formuły nie może przekraczać 8 KB, może zawierać maksymalnie 100 instrukcji, które są oddzielone średnikami i mogą zawierać podziały wierszy i komentarze.

Możesz myśleć o automatycznym skalowaniu formuł jako o "języku" w programie Batch w skalowaniu automatycznym. Instrukcje formuły to wyrażenia formowane bezpłatnie, które mogą zawierać zarówno zmienne zdefiniowane przez usługę (zmienne zdefiniowane przez usługę Batch), jak i zmienne zdefiniowane przez użytkownika (zdefiniowane zmienne). Mogą wykonywać różne operacje na tych wartościach przy użyciu wbudowanych typów, operatorów i funkcji. Na przykład instrukcja może przyjąć następującą formę:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formuły zazwyczaj zawierają wiele instrukcji, które wykonują operacje na wartościach, które są uzyskiwane w poprzednich instrukcjach. Na przykład najpierw uzyskujemy `variable1`wartość dla , a następnie przekazujemy ją do funkcji, aby wypełnić: `variable2`

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Dołącz te instrukcje do formuły skalowania automatycznego, aby dotrzeć do docelowej liczby węzłów obliczeniowych. Dedykowane węzły i węzły o niskim priorytecie mają własne ustawienia docelowe, dzięki czemu można zdefiniować miejsce docelowe dla każdego typu węzła. Formuła skalowania automatycznego może zawierać wartość docelową dla dedykowanych węzłów, wartość docelową dla węzłów o niskim priorytecie lub obie te te wartości.

Docelowa liczba węzłów może być wyższa, niższa lub taka sama jak bieżąca liczba węzłów tego typu w puli. Batch ocenia formułę skalowania automatycznego puli w określonym przedziale czasu (patrz [automatyczne interwały skalowania).](#automatic-scaling-interval) Partia dostosowuje numer docelowy każdego typu węzła w puli do liczby, którą formuła skalowania automatycznego określa w czasie oceny.

### <a name="sample-autoscale-formulas"></a>Przykładowe formuły skalowania automatycznego

Poniżej znajdują się przykłady dwóch formuł skalowania automatycznego, które można dostosować do pracy w większości scenariuszy. Zmienne `startingNumberOfVMs` i `maxNumberofVMs` w przykładowych formułach można dostosować do twoich potrzeb.

#### <a name="pending-tasks"></a>Zadania oczekujące

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

Za pomocą tej formuły skalowania automatycznego pula jest początkowo tworzona przy jednej maszynie wirtualnej. `$PendingTasks` Metryka definiuje liczbę zadań, które są uruchomione lub umieszczone w kolejce. Formuła znajduje średnią liczbę oczekujących zadań w ciągu ostatnich `$TargetDedicatedNodes` 180 sekund i odpowiednio ustawia zmienną. Formuła gwarantuje, że docelowa liczba dedykowanych węzłów nigdy nie przekracza 25 maszyn wirtualnych. W miarę przesyłania nowych zadań pula automatycznie rośnie. Po zakończeniu zadań maszyny wirtualne stają się bezpłatne jeden po drugim, a formuła skalowania automatycznego zmniejsza pulę.

Ta formuła skaluje dedykowane węzły, ale można ją zmodyfikować w celu zastosowania do skalowania węzłów o niskim priorytecie.

#### <a name="preempted-nodes"></a>Wstępnie wywłaszczone węzły 

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

W tym przykładzie tworzy pulę, która rozpoczyna się od 25 węzłów o niskim priorytecie. Za każdym razem, gdy węzeł o niskim priorytecie jest wywłaszczony, jest zastępowany dedykowanym węzłem. Podobnie jak w pierwszym `maxNumberofVMs` przykładzie zmienna zapobiega puli przekraczającej 25 maszyn wirtualnych. W tym przykładzie jest przydatne do korzystania z maszyn wirtualnych o niskim priorytecie, a także zapewnienie, że tylko stała liczba wywłaszczeń wystąpi przez cały okres istnienia puli.

## <a name="variables"></a>Zmienne

W formułach skalowania automatycznego można używać zarówno zmiennych **zdefiniowanych przez usługę,** jak i **zdefiniowanych przez użytkownika.** Zmienne zdefiniowane przez usługę są wbudowane w usługę Batch. Niektóre zmienne zdefiniowane przez usługę są odczytu i zapisu, a niektóre są tylko do odczytu. Zmienne zdefiniowane przez użytkownika są zmiennymi, które definiujesz. W przykładowej formule pokazanej w poprzedniej sekcji `$TargetDedicatedNodes` są `$PendingTasks` zmienne zdefiniowane przez usługę. Zmienne `startingNumberOfVMs` `maxNumberofVMs` i są zmiennymi zdefiniowanymi przez użytkownika.

> [!NOTE]
> Zmienne zdefiniowane przez usługę są zawsze poprzedzone znakiem dolara ($). W przypadku zmiennych zdefiniowanych przez użytkownika znak dolara jest opcjonalny.
>
>

W poniższych tabelach przedstawiono zmienne tylko do odczytu i tylko do odczytu, które są zdefiniowane przez usługę Batch.

Można uzyskać i ustawić wartości tych zmiennych zdefiniowanych przez usługę, aby zarządzać liczbą węzłów obliczeniowych w puli:

| Zmienne zdefiniowane przez usługę odczytu i zapisu | Opis |
| --- | --- |
| $TargetDedicatedNodes |Docelowa liczba dedykowanych węzłów obliczeniowych dla puli. Liczba dedykowanych węzłów jest określona jako miejsce docelowe, ponieważ pula nie zawsze może osiągnąć żądaną liczbę węzłów. Na przykład jeśli liczba docelowa dedykowanych węzłów jest modyfikowana przez ocenę skalowania automatycznego, zanim pula osiągnie początkowy cel docelowy, pula może nie osiągnąć obiektu docelowego. <br /><br /> Pula na koncie utworzonym za pomocą konfiguracji usługi wsadowej może nie osiągnąć swojego obiektu docelowego, jeśli obiekt docelowy przekracza węzeł konta usługi Batch lub przydział podstawowy. Pula na koncie utworzonym za pomocą konfiguracji subskrypcja użytkownika może nie osiągnąć swojego celu, jeśli obiekt docelowy przekroczy udostępniony przydział podstawowy dla subskrypcji.|
| $TargetLowPriorityNodes |Docelowa liczba węzłów obliczeniowych o niskim priorytecie dla puli. Liczba węzłów o niskim priorytecie jest określona jako miejsce docelowe, ponieważ pula nie zawsze może osiągnąć żądaną liczbę węzłów. Na przykład jeśli liczba docelowa węzłów o niskim priorytecie jest modyfikowana przez ocenę skalowania automatycznego, zanim pula osiągnie początkowy cel docelowy, pula może nie osiągnąć obiektu docelowego. Pula może również nie osiągnąć swój cel, jeśli obiekt docelowy przekracza węzeł konta usługi Batch lub przydział podstawowy. <br /><br /> Aby uzyskać więcej informacji na temat węzłów obliczeniowych o niskim priorytecie, zobacz [Używanie maszyn wirtualnych o niskim priorytecie z partią](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Akcja, która występuje, gdy węzły obliczeniowe są usuwane z puli. Możliwe wartości:<ul><li>**requeue**-- Wartość domyślna. Natychmiast kończy zadania i umieszcza je z powrotem w kolejce zadań, tak aby zostały przełożone. Ta akcja zapewnia docelową liczbę węzłów jest osiągany tak szybko, jak to możliwe, ale może być mniej wydajne, jak wszystkie uruchomione zadania zostaną przerwane i muszą zostać ponownie uruchomione, marnując wszelkie prace, które już wykonano. <li>**zakończyć**--Kończy zadania natychmiast i usuwa je z kolejki zadań.<li>**taskcompletion**--Czeka na aktualnie uruchomione zadania do zakończenia, a następnie usuwa węzeł z puli. Użyj tej opcji, aby uniknąć przerywania i ponownego wysuwania zadań w kolejce, marnując każdą pracę wykonaną przez zadanie. <li>**retaineddata**--Czeka na wszystkie lokalne dane zachowane w węźle do oczyszczenia przed usunięciem węzła z puli.</ul> |

> [!NOTE]
> Zmienną `$TargetDedicatedNodes` można również określić `$TargetDedicated`za pomocą aliasu . Podobnie zmienną `$TargetLowPriorityNodes` można określić za `$TargetLowPriority`pomocą aliasu . Jeśli zarówno w pełni nazwana zmienna, jak i jej alias są ustawiane przez formułę, wartość przypisana do w pełni nazwanej zmiennej będzie mieć pierwszeństwo.
>
>

Można uzyskać wartość tych zmiennych zdefiniowanych przez usługę, aby wprowadzić korekty, które są oparte na metrykach z usługi Batch:

| Zmienne zdefiniowane tylko do odczytu | Opis |
| --- | --- |
| $CPUPercent |Średni procent użycia procesora CPU. |
| $WallClockSeconds |Liczba zużytych sekund. |
| $MemoryBytes |Średnia liczba używanych megabajtów. |
| $DiskBytes |Średnia liczba gigabajtów używanych na dyskach lokalnych. |
| $DiskReadBytes |Liczba odczytanych bajtów. |
| $DiskWriteBytes |Liczba bajtów zapisanych. |
| $DiskReadOps |Liczba wykonanych operacji dysku odczytu. |
| $DiskWriteOps |Liczba wykonanych operacji na dysku zapisu. |
| $NetworkInBytes |Liczba bajtów przychodzących. |
| $NetworkOutBytes |Liczba bajtów wychodzących. |
| $SampleNodeCount |Liczba węzłów obliczeniowych. |
| $ActiveTasks |Liczba zadań, które są gotowe do wykonania, ale nie są jeszcze wykonywane. Liczba $ActiveTasks obejmuje wszystkie zadania, które są w stanie aktywnym i których zależności zostały spełnione. Wszystkie zadania, które są w stanie aktywnym, ale których zależności nie zostały spełnione, są wykluczone z $ActiveTasks liczby. W przypadku zadania z wieloma wystąpieniami $ActiveTasks będzie zawierać liczbę wystąpień ustawionych w zadaniu.|
| $RunningTasks |Liczba zadań w stanie uruchomionym. |
| $PendingTasks |Suma $ActiveTasks i $RunningTasks. |
| $SucceededTasks |Liczba zadań, które zakończyły się pomyślnie. |
| $FailedTasks |Liczba zadań, które nie powiodły się. |
| $CurrentDedicatedNodes |Bieżąca liczba dedykowanych węzłów obliczeniowych. |
| $CurrentLowPriorityNodes |Bieżąca liczba węzłów obliczeniowych o niskim priorytecie, w tym wszystkie węzły, które zostały wywłaszczone. |
| $PreemptedNodeCount | Liczba węzłów w puli, które są w stanie wywłaszczonych. |

> [!TIP]
> Zmienne tylko do odczytu, zdefiniowane przez usługę, które są wyświetlane w poprzedniej tabeli są *obiekty,* które zapewniają różne metody dostępu do danych skojarzonych z każdym. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie przykładowych danych](#getsampledata) w dalszej części tego artykułu.
>
>

## <a name="types"></a>Types

Te typy są obsługiwane w formule:

* double
* doubleVec (doubleVec)
* doubleVecList
* ciąg
* sygnatura czasowa - sygnatura czasowa jest strukturą złożoną, która zawiera następujące elementy członkowskie:

  * rok
  * miesiąc (1-12)
  * dzień (1-31)
  * w dni powszednie (w formacie liczby; na przykład 1 na poniedziałek)
  * godzina (w formacie 24-godzinnym; na przykład 13 oznacza 13:00)
  * minuta (00-59)
  * drugi (00-59)
* timeinterval

  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

## <a name="operations"></a>Operacje

Te operacje są dozwolone na typy, które są wymienione w poprzedniej sekcji.

| Operacja | Obsługiwane operatory | Typ wyniku |
| --- | --- | --- |
| podwójny *operator* podwójny |+, -, *, / |double |
| *dwukrotna* wyinterwal operatora |* |timeinterval |
| doubleVec *operator* podwójny |+, -, *, / |doubleVec (doubleVec) |
| doubleVec *operator* doubleVec |+, -, *, / |doubleVec (doubleVec) |
| *operator* timeinterval dwukrotnie |*, / |timeinterval |
| timeinterval *operatora* timeinterval |+, - |timeinterval |
| sygnatura *czasowa operatora* w czasie |+ |sygnatura czasowa |
| wyinterwal *operatora sygnatury* czasowej |+ |sygnatura czasowa |
| *sygnatura czasowa operatora sygnatury czasowej* |- |timeinterval |
| *operator*dwukrotnie |-, ! |double |
| *czas operatorainterwal* |- |timeinterval |
| podwójny *operator* podwójny |<, <=, ==, >=, >, != |double |
| ciąg *operatora* ciągu |<, <=, ==, >=, >, != |double |
| *sygnatura czasowa operatora sygnatury czasowej* |<, <=, ==, >=, >, != |double |
| timeinterval *operatora* timeinterval |<, <=, ==, >=, >, != |double |
| podwójny *operator* podwójny |&&  &#124;&#124; |double |

Podczas testowania podwójnego z operatorem`double ? statement1 : statement2`trójskładnikowym ( ), nonzero jest **prawdą,** a zero jest **fałszywe.**

## <a name="functions"></a>Funkcje
Te wstępnie **zdefiniowane funkcje** są dostępne do użycia podczas definiowania formuły skalowania automatycznego.

| Funkcja | Zwracany typ | Opis |
| --- | --- | --- |
| avg(doubleVecList) |double |Zwraca średnią wartość dla wszystkich wartości w doubleVecList. |
| len(doubleVecList) |double |Zwraca długość wektora, który jest tworzony z doubleVecList. |
| lg (podwójna) |double |Zwraca podstawę dziennika 2 podwójnego. |
| lg (doubleVecList) |doubleVec (doubleVec) |Zwraca bazę dziennika 2 doubleVecList. Vec(double) musi być jawnie przekazywane dla parametru. W przeciwnym razie zakłada się podwójną wersję lg(double). |
| ln(podwójny) |double |Zwraca dziennik naturalny double. |
| ln(doubleVecList) |doubleVec (doubleVec) |Zwraca dziennik naturalny double. |
| dziennik (podwójny) |double |Zwraca podstawę dziennika 10 podwójnego. |
| dziennik (doubleVecList) |doubleVec (doubleVec) |Zwraca bazę dziennika 10 doubleVecList. Vec(double) musi być jawnie przekazywane dla pojedynczego double parametru. W przeciwnym razie zakłada się wersję double log(double). |
| max(doubleVecList) |double |Zwraca maksymalną wartość w doubleVecList. |
| min(doubleVecList) |double |Zwraca wartość minimalną w doubleVecList. |
| norm(doubleVecList) |double |Zwraca dwie normy wektora, który jest tworzony z doubleVecList. |
| percentyl(doubleVec v, double p) |double |Zwraca element percentylu wektora v. |
| Rand() |double |Zwraca wartość losową z 0,0 a 1,0. |
| zakres (doubleVecList) |double |Zwraca różnicę między wartościami min i max w doubleVecList. |
| std(doubleVecList) |double |Zwraca przykładowe odchylenie standardowe wartości w doubleVecList. |
| stop() | |Zatrzymuje ocenę wyrażenia skalowania automatycznego. |
| suma(doubleVecList) |double |Zwraca sumę wszystkich składników doubleVecList. |
| time(ciąg dateTime=")) |sygnatura czasowa |Zwraca sygnaturę czasową bieżącego czasu, jeśli nie są przekazywane żadne parametry, lub sygnaturę czasową ciągu dateTime, jeśli jest przekazywana. Obsługiwane formaty dateTime to W3C-DTF i RFC 1123. |
| val(doubleVec v, double i) |double |Zwraca wartość elementu, który znajduje się w lokalizacji i w vector v, z indeksem początkowym zero. |

Niektóre funkcje, które są opisane w poprzedniej tabeli można zaakceptować listę jako argument. Lista oddzielona przecinkami jest dowolną kombinacją *double* i *doubleVec*. Przykład:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

*DoubleVecList* wartość jest konwertowana na pojedynczy *doubleVec* przed oceną. Na przykład, `v = [1,2,3]`jeśli `avg(v)` wywołanie jest `avg(1,2,3)`równoznaczne z wywołaniem . Wywołanie `avg(v, 7)` jest `avg(1,2,3,7)`równoznaczne z wywołaniem .

## <a name="obtain-sample-data"></a><a name="getsampledata"></a>Uzyskiwanie przykładowych danych

Formuły skalowania automatycznego działają na dane metryk (przykłady), które są dostarczane przez usługę Batch. Formuła zwiększa lub zmniejsza rozmiar puli na podstawie wartości, które uzyskuje z usługi. Zmienne zdefiniowane przez usługę, które zostały opisane wcześniej, są obiektami, które zapewniają różne metody dostępu do danych skojarzonych z tym obiektem. Na przykład następujące wyrażenie pokazuje żądanie, aby uzyskać ostatnie pięć minut użycia procesora CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Metoda | Opis |
| --- | --- |
| GetSample() |Metoda `GetSample()` zwraca wektor próbek danych.<br/><br/>Przykład jest 30 sekund wartości danych metryk. Innymi słowy próbki są uzyskiwane co 30 sekund. Ale jak wspomniano poniżej, występuje opóźnienie między kiedy próbka jest pobierana i kiedy jest dostępna dla formuły. W związku z tym nie wszystkie próbki w danym okresie mogą być dostępne do oceny według wzoru.<ul><li>`doubleVec GetSample(double count)`<br/>Określa liczbę próbek do uzyskania z najnowszych próbek, które zostały zebrane.<br/><br/>`GetSample(1)`zwraca ostatnią dostępną próbkę. W przypadku metryk, takich jak `$CPUPercent`, jednak nie należy tego używać, ponieważ nie można wiedzieć, *kiedy* próbka została pobrana. Może to być ostatnie, lub, z powodu problemów z systemem, może być znacznie starszy. Lepiej jest w takich przypadkach użyć przedziału czasu, jak pokazano poniżej.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Określa przedział czasu zbierania przykładowych danych. Opcjonalnie określa również procent próbek, które muszą być dostępne w żądanym przedziale czasu.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)`zwróci 20 próbek, jeśli wszystkie próbki z ostatnich 10 minut są obecne w historii CPUPercent. Jeśli jednak ostatnia minuta historii nie była dostępna, tylko 18 próbek zostanie zwróconych. W takim przypadku:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)`nie powiedzie się, ponieważ tylko 90 procent próbek są dostępne.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)`się udało.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Określa przedział czasu zbierania danych, zarówno z czasem rozpoczęcia, jak i czasem zakończenia.<br/><br/>Jak wspomniano powyżej, występuje opóźnienie między pobraniem próbki a tym, kiedy jest dostępna dla formuły. Należy wziąć pod uwagę `GetSample` to opóźnienie podczas korzystania z metody. Zobacz `GetSamplePercent` poniżej. |
| GetSamplePeriod() |Zwraca okres próbek pobranych w historycznym zestawie danych próbki. |
| Liczba() |Zwraca całkowitą liczbę próbek w historii metryk. |
| HistoriaPowierzchnia() |Zwraca sygnaturę czasową najstarszego dostępnego przykładu danych dla metryki. |
| GetSamplePercent() |Zwraca procent próbek, które są dostępne dla danego przedziału czasu. Przykład:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Ponieważ `GetSample` metoda kończy się niepowodzeniem, jeśli procent `samplePercent` zwracanych próbek `GetSamplePercent` jest mniejsza niż określona, można użyć metody, aby najpierw sprawdzić. Następnie można wykonać alternatywną akcję, jeśli istnieje niewystarczające próbki, bez zatrzymywania automatycznej oceny skalowania. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Próbki, procent próbki i *Metoda GetSample()*
Podstawową operacją formuły skalowania automatycznego jest uzyskanie danych metryki zadań i zasobów, a następnie dostosowanie rozmiaru puli na podstawie tych danych. W związku z tym ważne jest, aby mieć jasne zrozumienie, jak formuły skalowania automatycznego współdziałają z danymi metryk (przykłady).

**Próbki**

Usługa Batch okresowo pobiera próbki metryk zadań i zasobów i udostępnia je formułom skalowania automatycznego. Te przykłady są rejestrowane co 30 sekund przez usługę Batch. Jednak zazwyczaj występuje opóźnienie między tym, kiedy te próbki zostały zarejestrowane, a kiedy są dostępne (i mogą być odczytywane przez) formuły skalowania automatycznego. Ponadto ze względu na różne czynniki, takie jak problemy z siecią lub inną infrastrukturą, próbki mogą nie być rejestrowane dla określonego interwału.

**Procent próbki**

Po `samplePercent` przekazaniu `GetSample()` do metody `GetSamplePercent()` lub wywoływana jest metoda, _procent_ odwołuje się do porównania całkowitej możliwej liczby próbek, które są rejestrowane przez usługę Batch, a liczbą próbek dostępnych dla formuły skalowania automatycznego.

Spójrzmy na 10-minutowy timespan jako przykład. Ponieważ próbki są rejestrowane co 30 sekund w ciągu 10 minut, maksymalna całkowita liczba próbek, które są rejestrowane przez partię będzie 20 próbek (2 na minutę). Jednak ze względu na nieodłączne opóźnienie mechanizmu raportowania i innych problemów na platformie Azure może istnieć tylko 15 przykładów, które są dostępne dla formuły skalowania automatycznego do odczytu. Na przykład w tym 10-minutowym okresie tylko 75% całkowitej liczby zarejestrowanych próbek może być dostępnych dla formuły.

**GetSample() i zakresy próbek**

Formuły skalowania automatycznego będą rosnąć i zmniejszać &mdash; pule, dodając węzły lub usuwając węzły. Ponieważ węzły kosztują pieniądze, należy upewnić się, że formuły używają inteligentnej metody analizy opartej na wystarczających danych. Dlatego zaleca się użycie analizy typu trendów w formułach. Ten typ powiększa i zmniejsza pul pul na podstawie zakresu pobranych próbek.

Aby to zrobić, należy użyć `GetSample(interval look-back start, interval look-back end)` do zwrócenia wektora próbek:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Gdy powyższy wiersz jest oceniany przez batch, zwraca zakres próbek jako wektor wartości. Przykład:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Po zebraniu wektora próbek można następnie użyć funkcji `min()`takich `max()`jak `avg()` , i uzyskać znaczące wartości z zebranego zakresu.

Aby uzyskać dodatkowe zabezpieczenia, można wymusić niepowodzenie oceny formuły, jeśli dla określonego okresu dostępna jest mniejsza niż określona wartość procentowa próbki. Wymuszenie niepowodzenia oceny formuły powoduje polecenie batch zaprzestania dalszej oceny formuły, jeśli określony procent próbek nie jest dostępny. W takim przypadku nie jest wprowadzana żadna zmiana rozmiaru puli. Aby określić wymagany procent próbek, aby ocena powiodła się, `GetSample()`należy określić ją jako trzeci parametr do . W tym miejscu określono wymóg 75 procent próbek:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Ponieważ może wystąpić opóźnienie w dostępności próbki, ważne jest, aby zawsze określić zakres czasu z czasem rozpoczęcia look-back, który jest starszy niż jedna minuta. Rozmnażowanie próbek za pośrednictwem systemu zajmuje około jednej minuty, `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` więc próbki z zakresu mogą być niedostępne. Ponownie można użyć parametru `GetSample()` procentowego, aby wymusić określone wymaganie procentowe próbki.

> [!IMPORTANT]
> **Zdecydowanie zalecamy** **unikanie polegania `GetSample(1)` *tylko* na formułach skalowania automatycznego.** To dlatego, `GetSample(1)` że zasadniczo mówi do usługi Batch, "Daj mi ostatnią próbkę masz, bez względu na to, jak dawno temu go pobrać." Ponieważ jest to tylko pojedyncza próbka i może to być starsza próbka, może nie być reprezentatywna dla większego obrazu ostatniego stanu zadania lub zasobu. Jeśli używasz `GetSample(1)`, upewnij się, że jest to część większej instrukcji, a nie jedyny punkt danych, na których opiera się formuła.
>
>

## <a name="metrics"></a>Metryki

Podczas definiowania formuły można używać zarówno metryk zasobów, jak i zadań. Można dostosować liczbę docelową dedykowanych węzłów w puli na podstawie danych metryk, które można uzyskać i ocenić. Zobacz [zmienne](#variables) sekcji powyżej, aby uzyskać więcej informacji na temat każdej metryki.

<table>
  <tr>
    <th>Metryka</th>
    <th>Opis</th>
  </tr>
  <tr>
    <td><b>Zasobów</b></td>
    <td><p>Metryki zasobów są oparte na procesorze, przepustowości, użyciu pamięci węzłów obliczeniowych i liczbie węzłów.</p>
        <p> Te zmienne zdefiniowane przez usługę są przydatne do wprowadzania korekt na podstawie liczby węzłów:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Te zmienne zdefiniowane przez usługę są przydatne do wprowadzania korekt na podstawie użycia zasobów węzła:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Zadanie</b></td>
    <td><p>Metryki zadań są oparte na stanie zadań, takich jak Aktywne, Oczekujące i Zakończone. Następujące zmienne zdefiniowane przez usługę są przydatne do wprowadzania korekt rozmiaru puli na podstawie metryk zadań:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Pisanie formuły skalowania automatycznego

Formuła skalowania automatycznego tworzy się, tworząc instrukcje, które używają powyższych składników, a następnie łączy te instrukcje w pełną formułę. W tej sekcji tworzymy przykładową formułę skalowania automatycznego, która może wykonywać niektóre rzeczywiste decyzje dotyczące skalowania.

Najpierw zdefiniujmy wymagania dla naszej nowej formuły skalowania automatycznego. Wzór powinien:

1. Zwiększ docelową liczbę dedykowanych węzłów obliczeniowych w puli, jeśli użycie procesora CPU jest wysokie.
1. Zmniejsz docelową liczbę dedykowanych węzłów obliczeniowych w puli, gdy użycie procesora CPU jest niskie.
1. Zawsze ograniczaj maksymalną liczbę dedykowanych węzłów do 400.
1. Zmniejszając liczbę węzłów, nie należy usuwać węzłów, które są uruchomione zadania; w razie potrzeby poczekaj, aż zadania zostaną zakończone, aby usunąć węzły.

Aby zwiększyć liczbę węzłów podczas wysokiego użycia procesora CPU, zdefiniuj instrukcję, która wypełnia zmienną zdefiniowaną przez użytkownika (`$totalDedicatedNodes`) wartością, która wynosi 110 procent bieżącej docelowej liczby dedykowanych węzłów, ale tylko wtedy, gdy minimalne średnie użycie procesora CPU w ciągu ostatnich 10 minut było powyżej 70 procent. W przeciwnym razie należy użyć wartości dla bieżącej liczby dedykowanych węzłów.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Aby *zmniejszyć* liczbę dedykowanych węzłów podczas niskiego użycia procesora `$totalDedicatedNodes` CPU, następna instrukcja w naszej formule ustawia tę samą zmienną do 90 procent bieżącej docelowej liczby dedykowanych węzłów, jeśli średnie użycie procesora CPU w ciągu ostatnich 60 minut wynosiło poniżej 20 procent. W przeciwnym razie należy `$totalDedicatedNodes` użyć bieżącej wartości, którą wypełniliśmy w powyższej instrukcji.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Teraz ogranicz docelową liczbę dedykowanych węzłów obliczeniowych do maksymalnie 400:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Oto pełna formuła:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Tworzenie puli z obsługą skalowania automatycznego za pomocą sdk wsadowych

Pool autoscaling can be configured using any of the [Batch SDKs](batch-apis-tools.md#azure-accounts-for-batch-development), the [Batch REST API](https://docs.microsoft.com/rest/api/batchservice/) [Batch PowerShell cmdlets](batch-powershell-cmdlets-get-started.md), and the [Batch CLI](batch-cli-get-started.md). W tej sekcji można zobaczyć przykłady dla .NET i Python.

### <a name="net"></a>.NET

Aby utworzyć pulę z włączoną skalowaniem automatycznym w programie .NET, wykonaj następujące kroki:

1. Utwórz pulę za pomocą [pliku BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
1. Ustaw właściwość [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) na `true`.
1. Ustaw [właściwość CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) za pomocą formuły skalowania automatycznego.
1. (Opcjonalnie) Ustaw właściwość [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (wartość domyślna to 15 minut).
1. Zaajmij pulę za pomocą [cloudpool.commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) lub [commitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

Poniższy fragment kodu tworzy pulę z włączoną skalą automatyczną w .NET. Formuła skalowania automatycznego puli ustawia docelową liczbę dedykowanych węzłów na 5 w poniedziałki i 1 co drugi dzień tygodnia. Automatyczny [interwał skalowania](#automatic-scaling-interval) jest ustawiony na 30 minut. W tym i innych fragmentów C# w `myBatchClient` tym artykule jest poprawnie zainicjowane wystąpienie [BatchClient][net_batchclient] klasy.

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));    
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Podczas tworzenia puli z włączoną skalą automatyczną nie należy określać parametru _targetDedicatedNodes_ ani parametru _targetLowPriorityNodes_ w wywołaniu **CreatePool**. Zamiast tego należy określić **Właściwości AutoScaleEnabled** i **AutoScaleFormula** w puli. Wartości dla tych właściwości określają docelowy numer każdego typu węzła. Ponadto, aby ręcznie zmienić rozmiar puli z włączoną skalą automatyczną (na przykład za pomocą [BatchClient.PoolOperations.ResizePoolAsync),][net_poolops_resizepoolasync]najpierw **wyłącz** automatyczne skalowanie w puli, a następnie zmienić jej rozmiar.
>
>

#### <a name="automatic-scaling-interval"></a>Automatyczny interwał skalowania

Domyślnie usługa Batch dostosowuje rozmiar puli zgodnie z formułą skalowania automatycznego co 15 minut. Ten interwał można konfigurować przy użyciu następujących właściwości puli:

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (partia .NET)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (INTERFEJS API REST)

Minimalny interwał wynosi pięć minut, a maksymalny 168 godzin. Jeśli określony jest interwał poza tym zakresem, usługa Batch zwraca błąd nieprawidłowego żądania (400).

> [!NOTE]
> Skalowanie automatyczne nie jest obecnie przeznaczone do reagowania na zmiany w mniej niż minutę, ale raczej jest przeznaczony do dostosowywania rozmiaru puli stopniowo podczas uruchamiania obciążenia.
>
>

### <a name="python"></a>Python

Podobnie można wykonać pulę z włączoną skalowaniem automatyczną za pomocą sdk języka Python przez:

1. Utwórz pulę i określ jej konfigurację.
1. Dodaj pulę do klienta usługi.
1. Włącz skalowanie automatyczne w puli za pomocą formuły, którą piszesz.

```python
# Create a pool; specify configuration
new_pool = batch.models.PoolAddParameter(
    id="autoscale-enabled-pool",
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
          publisher="Canonical",
          offer="UbuntuServer",
          sku="18.04-LTS",
          version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size="STANDARD_D1_v2",
    target_dedicated_nodes=0,
    target_low_priority_nodes=0
)
batch_service_client.pool.add(new_pool) # Add the pool to the service client

formula = """$curTime = time();
             $workHours = $curTime.hour >= 8 && $curTime.hour < 18; 
             $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5; 
             $isWorkingWeekdayHour = $workHours && $isWeekday; 
             $TargetDedicated = $isWorkingWeekdayHour ? 20:10;""";

# Enable autoscale; specify the formula
response = batch_service_client.pool.enable_auto_scale(pool_id, auto_scale_formula=formula,
                                            auto_scale_evaluation_interval=datetime.timedelta(minutes=10), 
                                            pool_enable_auto_scale_options=None, 
                                            custom_headers=None, raw=False)
```

> [!TIP]
> Więcej przykładów używania SDK języka Python można znaleźć w [repozytorium Szybki start języka Python batch w](https://github.com/Azure-Samples/batch-python-quickstart) usłudze GitHub.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Włączanie skalowania automatycznego w istniejącej puli

Każdy plik SDK partii umożliwia włączenie skalowania automatycznego. Przykład:

* [BatchClient.PoolOperations.EnableAutoScaleAsync][net_enableautoscaleasync] (partia .NET)
* [Włączanie automatycznego skalowania w puli][rest_enableautoscale] (INTERFEJS API REST)

Po włączeniu skalowania automatycznego w istniejącej puli należy pamiętać o następujących kwestiach:

* Jeśli skalowanie automatyczne jest obecnie wyłączone w puli podczas wystawiania żądania włączenia skalowania automatycznego, należy określić prawidłową formułę skalowania automatycznego podczas wystawiania żądania. Opcjonalnie można określić interwał oceny skalowania automatycznego. Jeśli interwał nie zostanie określony, używana jest wartość domyślna 15 minut.
* Jeśli skalowanie automatyczne jest obecnie włączone w puli, można określić formułę skalowania automatycznego, interwał oceny lub oba te elementy. Należy określić co najmniej jedną z tych właściwości.

  * Jeśli określisz nowy interwał oceny skalowania automatycznego, istniejący harmonogram oceny zostanie zatrzymany i zostanie uruchomiony nowy harmonogram. Czas rozpoczęcia nowego harmonogramu jest godziną, w której zostało wydane żądanie włączenia skalowania automatycznego.
  * Jeśli pominięto formułę skalowania automatycznego lub interwał oceny, usługa Batch nadal będzie używać bieżącej wartości tego ustawienia.

> [!NOTE]
> Jeśli określono wartości dla *obiektu docelowegoDedicatedNodes* lub *targetLowPriorityNodes* parametry **CreatePool** metody podczas tworzenia puli w .NET lub dla porównywalnych parametrów w innym języku, a następnie te wartości są ignorowane podczas automatycznego skalowania formuły jest oceniane.
>
>

Ten fragment kodu języka C# używa biblioteki [Batch .NET,][net_api] aby włączyć skalowanie automatyczne w istniejącej puli:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Aktualizowanie formuły skalowania automatycznego

Aby zaktualizować formułę w istniejącej puli z włączoną skalą automatyczną, należy wywołać operację, aby ponownie włączyć skalowanie automatyczne za pomocą nowej formuły. Na przykład jeśli skalowanie automatyczne `myexistingpool` jest już włączone podczas wykonywania następującego kodu .NET, `myNewFormula`jego formuła skalowania automatycznego jest zastępowana zawartością programu .

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Aktualizowanie interwału skalowania automatycznego

Aby zaktualizować interwał oceny skalowania automatycznego istniejącej puli z włączoną skalą automatyczną, należy wywołać operację, aby ponownie włączyć skalowanie automatyczne z nowym interwałem. Na przykład, aby ustawić interwał oceny skalowania automatycznego na 60 minut dla puli, która jest już włączona w skali automatycznej w .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Ocena formuły skalowania automatycznego

Formułę można ocenić przed zastosowaniem jej do puli. W ten sposób można przetestować formułę, aby zobaczyć, jak jej instrukcje oceniają przed umieszczeniem formuły w produkcji.

Aby ocenić formułę skalowania automatycznego, należy najpierw włączyć skalowanie automatyczne w puli z prawidłową formułą. Aby przetestować formułę w puli, która nie ma jeszcze włączonej `$TargetDedicatedNodes = 0` skalowania automatycznego, użyj formuły jednowierszowej przy pierwszym włączeniu skalowania automatycznego. Następnie użyj jednej z następujących opcji, aby ocenić formułę, którą chcesz przetestować:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) lub [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Te metody Batch .NET wymagają identyfikatora istniejącej puli i ciągu zawierającego formułę skalowania automatycznego do oceny.

* [Ocena formuły skalowania automatycznego](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    W tym żądaniu interfejsu API REST określ identyfikator puli w identyfikatorze URI i formułę skalowania automatycznego w elemencie *autoScaleFormula* treści żądania. Odpowiedź operacji zawiera wszelkie informacje o błędzie, które mogą być związane z formułą.

W tym urywek kodu [usługi Batch .NET][net_api] oceniamy formułę skalowania automatycznego. Jeśli pula nie ma włączone skalowanie automatyczne, możemy włączyć go najpierw.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = $CurrentDedicatedNodes;",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Pomyślna ocena formuły pokazanej w tym fragmentie kodu daje wyniki podobne do:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Uzyskaj informacje o przebiegach skalowania automatycznego

Aby upewnić się, że formuła działa zgodnie z oczekiwaniami, zaleca się okresowe sprawdzanie wyników przebiegów skalowania automatycznego, które usługa Batch wykonuje w puli. Aby to zrobić, pobierz (lub odśwież) odwołanie do puli i sprawdź właściwości jego ostatniego uruchomienia skalowania automatycznego.

W usłudze Batch .NET właściwość [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) ma kilka właściwości, które dostarczają informacji o najnowszym automatycznym przebiegu skalowania wykonywanego w puli:

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Wyniki](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

W interfejsie API REST [pobierz informacje o](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) żądaniu puli zwraca informacje o puli, która zawiera najnowsze informacje o uruchomieniu automatycznego skalowania we właściwości [autoScaleRun.](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool)

Poniższy fragment kodu języka C# używa biblioteki Batch .NET do drukowania informacji o ostatnim uruchomieniu skalowania automatycznego w puli _myPool:_

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Przykładowe dane wyjściowe poprzedniego fragmentu kodu:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Przykładowe formuły skalowania automatycznego

Przyjrzyjmy się kilku formułom, które pokazują różne sposoby dostosowywania ilości zasobów obliczeniowych w puli.

### <a name="example-1-time-based-adjustment"></a>Przykład 1: Korekta oparta na czasie

Załóżmy, że chcesz dostosować rozmiar puli na podstawie dnia tygodnia i porą dnia. W tym przykładzie pokazano, jak odpowiednio zwiększyć lub zmniejszyć liczbę węzłów w puli.

Formuła najpierw uzyskuje bieżący czas. Jeśli jest to dzień tygodnia (1-5) i w godzinach pracy (8 RANO do 6 PM), rozmiar puli docelowej jest ustawiona na 20 węzłów. W przeciwnym razie jest ustawiona na 10 węzłów.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```
`$curTime`można dostosować, aby odzwierciedlić lokalną strefę czasową, dodając `time()` do produktu `TimeZoneInterval_Hour` i przesunięcie UTC. Na przykład `$curTime = time() + (-6 * TimeInterval_Hour);` użyj dla mountain daylight time (MDT). Należy pamiętać, że przesunięcie musiałoby zostać dostosowane na początku i na końcu czasu letniego (jeśli dotyczy).

### <a name="example-2-task-based-adjustment"></a>Przykład 2: Dostosowanie oparte na zadaniach

W tym przykładzie rozmiar puli jest korygowany na podstawie liczby zadań w kolejce. Zarówno komentarze, jak i podziały wierszy są dopuszczalne w ciągach formuły.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $PendingTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$PendingTasks.GetSample(1)) : max( $PendingTasks.GetSample(1), avg($PendingTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - let running tasks finish before removing a node
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Przykład 3: Rozliczanie zadań równoległych

W tym przykładzie dostosowuje rozmiar puli na podstawie liczby zadań. Ta formuła uwzględnia również wartość [MaxTasksPerComputeNode,][net_maxtasks] która została ustawiona dla puli. Takie podejście jest przydatne w sytuacjach, gdy [równoległe wykonywanie zadań](batch-parallel-node-tasks.md) zostało włączone w puli.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Przykład 4: Ustawianie początkowego rozmiaru puli

W tym przykładzie pokazano fragment kodu języka C# z formułą skalowania automatycznego, która ustawia rozmiar puli na określoną liczbę węzłów dla początkowego okresu. Następnie dostosowuje rozmiar puli na podstawie liczby uruchomionych i aktywnych zadań po upływie początkowego okresu.

Wzór w poniższym urywek kodu:

* Ustawia początkowy rozmiar puli na cztery węzły.
* Nie dostosowuje rozmiaru puli w ciągu pierwszych 10 minut cyklu życia puli.
* Po 10 minutach uzyskuje maksymalną wartość liczby uruchomionych i aktywnych zadań w ciągu ostatnich 60 minut.
  * Jeśli obie wartości są 0 (co oznacza, że żadne zadania nie były uruchomione lub aktywne w ciągu ostatnich 60 minut), rozmiar puli jest ustawiony na 0.
  * Jeśli którakolwiek z wartości jest większa od zera, nie jest wprowadzana żadna zmiana.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Następne kroki

* [Maksymalizuj użycie zasobów obliczeniowych usługi Azure Batch za pomocą równoczesnych zadań węzłów](batch-parallel-node-tasks.md) zawiera szczegółowe informacje o tym, jak można wykonywać wiele zadań jednocześnie w węzłach obliczeniowych w puli. Oprócz skalowania automatycznego ta funkcja może pomóc w zmniejszeniu czasu trwania zadania dla niektórych obciążeń, oszczędzając pieniądze.
* Aby uzyskać inny booster wydajności, upewnij się, że aplikacja batch wysyła kwerendy usługi batch w najbardziej optymalny sposób. Zobacz [Kwerenda usługi Azure Batch skutecznie,](batch-efficient-list-queries.md) aby dowiedzieć się, jak ograniczyć ilość danych, które przecina przewody podczas kwerendy o stanie potencjalnie tysiące węzłów obliczeniowych lub zadań.

[net_api]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[net_batchclient]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient
[net_cloudpool_autoscaleformula]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula
[net_cloudpool_autoscaleevalinterval]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval
[net_enableautoscaleasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync
[net_maxtasks]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.maxtaskspercomputenode
[net_poolops_resizepoolasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync

[rest_api]: https://docs.microsoft.com/rest/api/batchservice/
[rest_autoscaleformula]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_autoscaleinterval]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_enableautoscale]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
