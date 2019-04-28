---
title: Automatyczne skalowanie węzłów obliczeniowych w puli usługi Azure Batch | Dokumentacja firmy Microsoft
description: Włącz automatyczne skalowanie na pulę chmury w celu dynamicznego dostosowania liczby węzłów obliczeniowych w puli.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: multiple
ms.date: 06/20/2017
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fdc2cd8f2218d50aa49d6b4eab2800eb6c92d9c9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118115"
---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Utwórz formułę skalowania automatycznego skalowania węzłów obliczeniowych w puli usługi Batch

Usługa Azure Batch może automatycznie skalować pule na podstawie parametrów zdefiniowanych przez użytkownika. Automatyczne skalowanie usługi Batch są dodawane dynamicznie węzłów do puli jako zwiększenie zapotrzebowania zadań i usuwa węzły obliczeniowe, zgodnie z ich zmniejszyć. Automatycznie, dostosowując liczbę węzłów obliczeniowych, które korzystają z aplikacji usługi Batch można zapisać czas i pieniądze. 

Automatyczne skalowanie puli węzłów obliczeniowych można skojarzyć z nim można włączyć *formułę skalowania automatycznego* zdefiniowany. Usługa Batch używa formuły skalowania automatycznego, aby określić liczbę węzłów obliczeniowych, które są wymagane do wykonywania obciążenia. Obliczenia węzły mogą być węzły dedykowane lub [węzły o niskim priorytecie](batch-low-pri-vms.md). Batch odnosi się do danych metryk usługi, zbierane są okresowo. Przy użyciu tych danych metryk, Batch dostosowuje liczbę węzłów obliczeniowych w puli na podstawie formuły i w konfigurowalnym interwale.

Aby umożliwić automatyczne skalowanie, po utworzeniu puli lub w istniejącej puli. Można również zmienić istniejącej formuły w puli, który jest skonfigurowany na potrzeby skalowania automatycznego. Batch umożliwia ocenę formuły przed przypisaniem ich do pul oraz monitorować stan uruchomienia skalowania automatycznego.

W tym artykule omówiono różne jednostki, które tworzą formuły automatycznego skalowania, w tym zmienne, operatory, operacje i funkcje. Omówimy sposób uzyskiwania różne obliczeń zasób i zadanie metryki w ramach usługi Batch. Te metryki umożliwia dostosowanie liczby węzłów w puli na podstawie stanu zasobów użycia i zadania. Następnie opisano, jak utworzyć formułę i włączyć automatyczne skalowanie puli przy użyciu usługi Batch REST i interfejsów API platformy .NET. Na koniec mamy kończą kilka formuł w przykładzie.

> [!IMPORTANT]
> Podczas tworzenia konta usługi Batch można określić [konfiguracji konta](batch-api-basics.md#account), która określa, czy pule są przydzielane w subskrypcji usługi Batch (ustawienie domyślne) lub w ramach subskrypcji użytkownika. Jeśli utworzono konto usługi Batch przy użyciu domyślnej konfiguracji usługi Batch, Twoje konto jest ograniczona do maksymalnej liczby rdzeni, które mogą służyć do przetwarzania. Usługa Batch umożliwia skalowanie węzłów obliczeniowych, tylko do tego limit liczby rdzeni. Z tego powodu docelowa liczba węzłów obliczeniowych, określony przez formułę skalowania automatycznego nie może skontaktować się z usługi Batch. Zobacz [przydziały i limity dla usługi Azure Batch](batch-quota-limit.md) informacji na temat przeglądania i zwiększyć limity przydziału konta.
>
>Jeśli Twoje konto zostało utworzone przy użyciu konfiguracji subskrypcji użytkownika, Twoje konto udziałów w limit przydziału rdzeni subskrypcji. Aby uzyskać więcej informacji, zobacz sekcję [Virtual Machines limits (Limity maszyn wirtualnych)](../azure-subscription-service-limits.md#virtual-machines-limits) w artykule [Azure subscription and service limits, quotas, and constraints (Ograniczenia, przydziały i limity usług i subskrypcji platformy Azure)](../azure-subscription-service-limits.md).
>
>

## <a name="automatic-scaling-formulas"></a>Formuły skalowania automatycznego
Formułę skalowania automatycznego jest wartość ciągu, który zdefiniujesz zawierający jedną lub więcej instrukcji. Formułę skalowania automatycznego jest przypisana do puli [autoScaleFormula] [ rest_autoscaleformula] — element (interfejs REST usługi Batch) lub [CloudPool.AutoScaleFormula] [ net_cloudpool_autoscaleformula] Właściwość (platforma .NET usługi Batch). Usługa Batch używa formuły do określenia docelowa liczba węzłów obliczeniowych w puli dla kolejnego interwału przetwarzania. Parametry formuły nie może być dłuższa niż 8 KB, może zawierać maksymalnie 100 instrukcji, które są oddzielone średnikami i może zawierać podziałów wierszy i komentarze.

Można potraktować formuły skalowania automatycznego jako automatycznego skalowania usługi Batch "język". Instrukcje formuły są bezpłatne sformułowany wyrażeń, obejmujących zmienne zdefiniowane przez usługę (zmienne zdefiniowane przez usługę Batch) i zmienne zdefiniowane przez użytkownika (zmiennych zdefiniowanych przez użytkownika). Ich wykonywanie różnych operacji na te wartości przy użyciu wbudowanych typów, operatorów i funkcji. Na przykład instrukcja może mieć następującą formę:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formuły zwykle zawierają wiele instrukcji, które wykonują operacje na wartościach, które są uzyskiwane w poprzednich instrukcji. Na przykład, najpierw możemy uzyskać wartość `variable1`, należy przekazać do funkcji, aby wypełnić `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Dołącz te instrukcje formułę skalowania automatycznego na docelowa liczba węzłów obliczeniowych. Węzły dedykowane, jak i węzły o niskim priorytecie mają swoje własne ustawienia docelowej, dzięki czemu można zdefiniować docelowy dla każdego typu węzła. Formuła automatycznego skalowania może zawierać wartość docelową na węzły dedykowane i/lub wartość docelowa dla węzłów o niskim priorytecie.

Docelowa liczba węzłów może być większa w dolnym lub taka sama jak bieżąca liczba węzłów w puli tego typu. Batch ocenia formułę automatycznego skalowania puli w określonych odstępach czasu (zobacz [automatycznego skalowania interwałów](#automatic-scaling-interval)). Batch dostosowuje docelowa liczba każdy typ węzła w puli aby liczba określająca formułę skalowania automatycznego w czasie oceny.

### <a name="sample-autoscale-formula"></a>Przykładowe formułę skalowania automatycznego

Oto przykład formułę skalowania automatycznego, którą można dostosować do pracy w przypadku większości scenariuszy. Zmienne `startingNumberOfVMs` i `maxNumberofVMs` w przykładzie formuła mogą być dostosowane do potrzeb. Ta formuła jest skalowana węzły dedykowane, ale można modyfikować, aby zastosować do skalowania węzłów o niskim priorytecie także. 

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

Za pomocą tej formuły automatycznego skalowania puli został początkowo utworzony za pomocą pojedynczej maszyny Wirtualnej. `$PendingTasks` Metryki definiuje liczbę zadań, które są uruchomione lub umieszczone w kolejce. Formuła wyszukuje średnią liczbę zadań oczekujących w ostatnich 180 sekund i zestawy `$TargetDedicatedNodes` zmiennej odpowiednio. Formuła zapewnia, że docelowa liczba węzłów dedykowanych nigdy nie przekracza 25 maszyn wirtualnych. Ponieważ nowe zadania są przesyłane automatycznie zwiększa rozmiar puli. Jako zadania maszyny wirtualne stają się wolne pojedynczo, a następnie formułę skalowania automatycznego zmniejszania puli.

## <a name="variables"></a>Zmienne
Możesz używać ich obu **zdefiniowane przez usługę** i **zdefiniowanych przez użytkownika** zmiennych w formułach skalowania automatycznego. Wbudowane zmienne zdefiniowane przez usługę do usługi Batch. Niektóre zmienne zdefiniowane przez usługę są odczytu i zapisu, a niektóre są przeznaczone tylko do odczytu. Zmienne zdefiniowane przez użytkownika są zmiennych zdefiniowanych przez użytkownika. W formule przykładzie pokazano w poprzedniej sekcji `$TargetDedicatedNodes` i `$PendingTasks` zmiennych zdefiniowanych przez usługę. Zmienne `startingNumberOfVMs` i `maxNumberofVMs` zmiennych zdefiniowanych przez użytkownika.

> [!NOTE]
> Zmienne zdefiniowane przez usługę zawsze są poprzedzone znakiem dolara ($). W przypadku zmiennych zdefiniowanych przez użytkownika znak dolara jest opcjonalne.
>
>

W poniższej tabeli przedstawiono zmienne odczytu / zapisu i tylko do odczytu, które są zdefiniowane przez usługę Batch.

Może pobierać i ustawiać wartości tych zmiennych zdefiniowanych przez usługę, aby zarządzać liczbą węzłów obliczeniowych w puli:

| Zmienne zdefiniowane przez usługę odczytu / zapisu | Opis |
| --- | --- |
| $TargetDedicatedNodes |Docelowa Liczba dedykowanych węzłami obliczeniowymi w celu puli. Liczba dedykowanych węzłów jest określony jako obiektu docelowego, ponieważ pula nie zawsze osiągnąć żądaną liczbę węzłów. Na przykład jeśli docelowa liczba węzłów dedykowanych jest modyfikowany przez ocenę skalowania automatycznego, zanim puli osiągnęła docelowej początkowej, następnie puli może nie korzystał z obiektu docelowego. <br /><br /> Puli na koncie przy użyciu konfiguracji usługi Batch nie może osiągnąć jego element docelowy, jeśli element docelowy przekracza przydział węzła lub core konta usługi Batch. Puli na koncie przy użyciu konfiguracji subskrypcji użytkownika nie może osiągnąć jego element docelowy, jeśli element docelowy przekracza przydział rdzeni udostępniony dla subskrypcji.|
| $TargetLowPriorityNodes |Docelowa liczba o niskim priorytecie węzłami obliczeniowymi w celu puli. Liczba węzłów o niskim priorytecie jest określony jako obiektu docelowego, ponieważ pula nie zawsze osiągnąć żądaną liczbę węzłów. Na przykład jeśli docelowa liczba węzłów o niskim priorytecie jest modyfikowany przez ocenę skalowania automatycznego, zanim puli osiągnęła docelowej początkowej, następnie puli może nie osiągnąć element docelowy. Pulę również może nie osiągnąć jego element docelowy, jeśli element docelowy przekracza przydział węzła lub core konta usługi Batch. <br /><br /> Aby uzyskać więcej informacji na temat węzły obliczeniowe o niskim priorytecie, zobacz [maszyny wirtualne o niskim priorytecie za pomocą usługi Batch (wersja zapoznawcza)](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Akcja, która występuje, gdy węzły obliczeniowe są usuwane z puli. Możliwe wartości:<ul><li>**ponownego dodania do kolejki**— natychmiast kończy zadania i umieszcza je ponownie w kolejce zadania, dzięki czemu są one ponownie.<li>**Zakończenie**— natychmiast kończy zadania i usuwa je z kolejki zadań.<li>**taskcompletion**— oczekuje aktualnie uruchomione zadania, aby zakończyć, a następnie usunięcie węzła z puli.<li>**retaineddata**— oczekiwania dla wszystkich danych lokalnej zachowana zadania w węźle być oczyszczone przed usunięciem węzła z puli.</ul> |

Można pobrać wartości tych zmiennych zdefiniowanych przez usługę, aby wprowadzać zmiany, które są oparte na metryki z usługi Batch:

| Zmienne tylko do odczytu zdefiniowane przez usługę | Opis |
| --- | --- |
| $CPUPercent |Średni procent użycia procesora CPU. |
| $WallClockSeconds |Liczbę wykorzystane w ciągu kilku sekund. |
| $MemoryBytes |Średnia liczba megabajtów używane. |
| $DiskBytes |Średnia liczba gigabajtów używana na dyskach lokalnych. |
| $DiskReadBytes |Liczba odczytanych bajtów. |
| $DiskWriteBytes |Liczba zapisanych bajtów. |
| $DiskReadOps |Liczba operacji odczytu dysku. |
| $DiskWriteOps |Liczba operacji dysku zapisu. |
| $NetworkInBytes |Liczba bajtów przychodzących. |
| $NetworkOutBytes |Liczba bajtów ruchu wychodzącego. |
| $SampleNodeCount |Liczba węzłów obliczeniowych. |
| $ActiveTasks |Liczba zadań, które są gotowe do wykonania, ale jeszcze nie są wykonywane. Liczba $ActiveTasks obejmuje wszystkie zadania, które znajdują się w stanie aktywnym i którego zależności zostały spełnione. Wszystkie zadania, które znajdują się w stanie aktywnym, ale których zależności nie zostały spełnione są wykluczane z liczby $ActiveTasks.|
| $RunningTasks |Liczba zadań w stanie uruchomienia. |
| $PendingTasks |Suma $ActiveTasks i $RunningTasks. |
| $SucceededTasks |Liczba zadań, które zakończyło się pomyślnie. |
| $FailedTasks |Liczba zadań, które nie powiodło się. |
| $CurrentDedicatedNodes |Bieżąca liczba dedykowanych węzłów obliczeniowych. |
| $CurrentLowPriorityNodes |Bieżąca liczba o niskim priorytecie węzły obliczeniowe, w tym wszystkie węzły, które zostały opóźnieniem. |
| $PreemptedNodeCount | Liczba węzłów w puli, które są w stanie wyparte. |

> [!TIP]
> Zmienne tylko do odczytu, zdefiniowane przez usługę, które są wyświetlane w powyższej tabeli są *obiektów* dostarczające różnych metod dostępu do danych skojarzonych z każdym. Aby uzyskać więcej informacji, zobacz [uzyskać przykładowe dane](#getsampledata) w dalszej części tego artykułu.
>
>

## <a name="types"></a>Typy
Te typy są obsługiwane w formule:

* double
* doubleVec
* doubleVecList
* string
* Sygnatura czasowa — sygnatura czasowa jest złożone struktury, która zawiera następujące elementy:

  * rocznie
  * miesiąc (1-12)
  * dzień (1-31)
  * dzień tygodnia (w formacie numer; na przykład 1 dla kalendarza poniedziałek)
  * Godzina (w formacie liczby 24-godzinny; na przykład 13 oznacza 13: 00)
  * minuty (00-59)
  * sekundy (00-59)
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
Te operacje są dozwolone w typach, które są wymienione w poprzedniej sekcji.

| Operacja | Operatory obsługiwane | Typ wyniku |
| --- | --- | --- |
| podwójne *operator* double |+, -, *, / |double |
| podwójne *operator* timeinterval |* |timeinterval |
| doubleVec *operator* double |+, -, *, / |doubleVec |
| doubleVec *operator* doubleVec |+, -, *, / |doubleVec |
| TimeInterval *operator* double |*, / |timeinterval |
| TimeInterval *operator* timeinterval |+, - |timeinterval |
| TimeInterval *operator* znacznik czasu: |+ |timestamp |
| Sygnatura czasowa *operator* timeinterval |+ |timestamp |
| Sygnatura czasowa *operator* znacznik czasu: |- |timeinterval |
| *operator*double |-, ! |double |
| *operator*timeinterval |- |timeinterval |
| podwójne *operator* double |<, <=, ==, >=, >, != |double |
| ciąg *operator* ciągu |<, <=, ==, >=, >, != |double |
| Sygnatura czasowa *operator* znacznik czasu: |<, <=, ==, >=, >, != |double |
| TimeInterval *operator* timeinterval |<, <=, ==, >=, >, != |double |
| podwójne *operator* double |&&, &#124;&#124; |double |

Podczas testowania wartość o podwójnej precyzji z operator trójargumentowy (`double ? statement1 : statement2`), wartość różną od zera jest **true**, i zero jest **false**.

## <a name="functions"></a>Funkcje
Te wstępnie zdefiniowane **funkcje** są dostępne do użycia podczas definiowania formułę skalowania automatycznego.

| Funkcja | Zwracany typ | Opis |
| --- | --- | --- |
| AVG(doubleVecList) |double |Zwraca wartość średnią dla wszystkich wartości w doubleVecList. |
| Len(doubleVecList) |double |Zwraca długość wektora, który jest utworzony na podstawie doubleVecList. |
| LG(Double) |double |Zwraca dziennik podstawie 2 wartość podwójnej precyzji. |
| LG(doubleVecList) |doubleVec |Zwraca dotyczącą składnika dziennika na podstawie 2 doubleVecList. Vec(double) muszą być jawnie przekazywane dla parametru. W przeciwnym razie przyjmowana jest wersja double lg(double). |
| ln(Double) |double |Zwraca wartość logarytmu naturalnego wartość podwójnej precyzji. |
| ln(doubleVecList) |doubleVec |Zwraca dotyczącą składnika dziennika na podstawie 2 doubleVecList. Vec(double) muszą być jawnie przekazywane dla parametru. W przeciwnym razie przyjmowana jest wersja double lg(double). |
| log(Double) |double |Zwraca dziennik podstawa 10 wartość podwójnej precyzji. |
| log(doubleVecList) |doubleVec |Zwraca wartość dotyczącą składnika dziennika doubleVecList o podstawie 10. Vec(double) muszą być jawnie przekazywane pojedynczego parametru double. W przeciwnym razie przyjmowana jest wersja double log(double). |
| MAX(doubleVecList) |double |Zwraca maksymalną wartość doubleVecList. |
| min(doubleVecList) |double |Zwraca minimalną wartość w doubleVecList. |
| norm(doubleVecList) |double |Zwraca dwa normy wektor, który jest tworzony na podstawie doubleVecList. |
| percentyl (v doubleVec double p) |double |Zwraca element percentyl wektora v. |
| rand() |double |Zwraca losową wartość zakresu od 0,0 do 1,0. |
| Range(doubleVecList) |double |Zwraca różnicę w zakresie między minimalną i maksymalną wartości doubleVecList. |
| STD(doubleVecList) |double |Zwraca odchylenie standardowe przykładowych wartości w doubleVecList. |
| stop() | |Zatrzymuje obliczenia wyrażenia skalowania automatycznego. |
| sum(doubleVecList) |double |Zwraca sumę wszystkich składników doubleVecList. |
| czas (ciąg daty/godziny = "") |timestamp |Zwraca sygnaturę czasową od bieżącego czasu, jeśli nie przekazano żadnych parametrów, sygnaturę czasową ciągu daty i godziny, jeśli został przekazany. Formatów obsługiwanych daty/godziny są W3C DTF i RFC 1123. |
| Val (v doubleVec podwójny i:) |double |Zwraca wartość elementu, który znajduje się w lokalizacji i w wektorze v początkowy indeks o wartości zero. |

Niektóre funkcje, które są opisane w poprzedniej tabeli można zaakceptować listy jako argument. Rozdzielana przecinkami lista to dowolna kombinacja *double* i *doubleVec*. Na przykład:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

*DoubleVecList* wartość jest konwertowana na wartość typu single *doubleVec* przed oceną. Na przykład jeśli `v = [1,2,3]`, następnie wywoływania `avg(v)` jest równoważne z wywoływaniem `avg(1,2,3)`. Wywoływanie `avg(v, 7)` jest równoważne z wywoływaniem `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Uzyskaj przykładowych danych
Formuły automatycznego skalowania działają na dane metryk (przykłady), które są dostarczane przez usługę Batch. Formuła powiększa się lub zmniejsza rozmiar puli na podstawie wartości, które uzyskuje od usługi. Zmienne zdefiniowane przez usługę, które zostały opisane wcześniej są obiekty, które udostępniają różne metody dostępu do danych, który jest skojarzony z tym obiektem. Na przykład poniższe wyrażenie pokazuje żądanie pobrania w ciągu ostatnich pięciu minut użycia procesora CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Metoda | Opis |
| --- | --- |
| GetSample() |`GetSample()` Metoda zwraca wektor próbek danych.<br/><br/>Przykład jest 30 sekund danych metryk. Innymi słowy przykłady są uzyskiwane co 30 sekund. Ale wymienionych poniżej jest opóźnienie między po zebraniu próbkę, a kiedy jest ona dostępna w formule. W efekcie nie wszystkie przykłady w danym czasie może być dostępnych do oceny przez formułę.<ul><li>`doubleVec GetSample(double count)`<br/>Określa liczbę próbek do uzyskiwania z najbardziej aktualne przykłady, które zostały zebrane.<br/><br/>`GetSample(1)` Zwraca ostatni przykładom. Dla metryk, takich jak `$CPUPercent`, jednak ta powinna nie można użyć, ponieważ nie jest możliwe dowiedzieć się *podczas* próbki zostały zebrane. Może być ostatnie lub ze względu na problemy z systemem, może być dużo starsza. Zaleca się, w takich przypadkach można użyć przedział czasu, jak pokazano poniżej.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Określa przedział czasu zbierania danych przykładowych. Opcjonalnie określa również wartość procentowa próbek, które muszą być dostępne w żądanej horyzoncie czasowym.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` zwróci 20 próbek, gdy wszystkie przykłady dla ostatnich 10 minut znajdują się w historii CPUPercent. Jeśli w ciągu ostatniej minuty Historia nie jest dostępna, jednak tylko 18 przykłady będzie zwracany. W takim przypadku:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` może zakończyć się niepowodzeniem, ponieważ nie są dostępne tylko przez 90 procent próbki.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` powiedzie się.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Określa przedział czasu zbierania danych z godziny rozpoczęcia i godzinę zakończenia.<br/><br/>Jak wspomniano powyżej, brak opóźnienia między po próbki są zbierane i kiedy jest ona dostępna w formule. Należy rozważyć tego opóźnienia, korzystając z `GetSample` metody. Zobacz `GetSamplePercent` poniżej. |
| GetSamplePeriod() |Zwraca okres przykładów, które zostały wykonane w zestawie danych przykładowych historycznych. |
| Count() |Zwraca łączna liczba próbek w historii metryki. |
| HistoryBeginTime() |Zwraca sygnaturę czasową najstarsze próbki dostępnych danych dla metryki. |
| GetSamplePercent() |Zwraca wartość procentowa próbek, które są dostępne dla danego interwału. Na przykład:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Ponieważ `GetSample` metoda kończy się niepowodzeniem, jeśli wartość procentowa próbek zwracana jest mniejsza niż `samplePercent` określony, możesz użyć `GetSamplePercent` metody do sprawdzenia. Następnie alternatywne działanie, które można wykonać w przypadku niewystarczających przykłady, bez zatrzymaniu obliczania automatycznego skalowania. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Przykłady, procent próbki i *GetSample()* — metoda
Podstawowej funkcjonalności formułę skalowania automatycznego jest do uzyskania danych metryki zadań i zasobów, a następnie dopasuj rozmiar puli w oparciu o dane. W efekcie jest ważne, aby informacje o interakcjach formuły skalowania automatycznego z danymi metryk (przykłady).

**Przykłady**

Usługa Batch okresowo pobieranie próbek metryk zadań i zasobów i udostępnia je do formuły automatycznego skalowania. Te przykłady są rejestrowane co 30 sekund, przez usługę Batch. Istnieje jednak zwykle opóźnienia między kiedy te przykłady zostały zapisane, oraz gdy są udostępniane (i może zostać odczytany przez) formuły automatycznego skalowania. Ponadto ze względu na różnych czynników, takich jak sieci lub inne problemy z infrastrukturą, przykłady mogą nie zostać zarejestrowane dla określonego interwału.

**Procent próbki**

Podczas `samplePercent` jest przekazywany do `GetSample()` metody lub `GetSamplePercent()` metoda jest wywoływana, _procent_ dotyczy porównanie łączna liczba próbek, które są rejestrowane przez usługę Batch i liczba przykłady, które są dostępne do formuły automatycznego skalowania.

Na przykład Przyjrzyjmy timespan 10 minut. Ponieważ przykłady są rejestrowane co 30 sekund w ciągu 10 minut przedziału czasu, maksymalna łączna liczba próbek, które są rejestrowane przy użyciu usługi Batch będzie 20 próbek (2 na minutę). Jednak ze względu na opóźnienie nieprzerwaną pracę, mechanizm raportowania i inne problemy w systemie Azure, może być tylko 15 przykładów, które są dostępne dla Twojej formułę skalowania automatycznego do odczytu. Tak na przykład, w tym okresie 10-minutowe tylko 75% łączna liczba próbek rejestrowane mogą być dostępne do formuły.

**Przykład i GetSample() zakresów**

Formuły skalowania automatycznego mają być w stałym wzbogacaniu i zmniejszania puli &mdash; Dodawanie węzłów i usuwanie węzłów. Ponieważ węzły są kosztu pieniądze, chcesz mieć pewność, że formuł stosowanie inteligentne metody analizy, które są oparte na danych wystarczających. Dlatego zaleca się używać analizy trendu typu w formułach. Ten typ zwiększania lub zmniejszania puli na podstawie zakresu zebranych próbek.

Aby to zrobić, należy użyć `GetSample(interval look-back start, interval look-back end)` do zwrócenia wektor próbek:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Gdy powyższy wiersz jest obliczane przy użyciu usługi Batch, zwraca szereg przykładów jako wektor wartości. Na przykład:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Zostały zebrane w wektorze przykłady można następnie użyć funkcji, takich jak `min()`, `max()`, i `avg()` wyprowadzenia istotne wartości z zakresu zebrane.

Dla dodatkowego bezpieczeństwa możesz wymusić formuły, aby zakończyć się niepowodzeniem, jeśli jest mniejsza niż określony procent próbki dostępne dla określonego okresu. Po wymuszeniu formuły nie powiedzie się poinstruowanie partii zaprzestania dalszej oceny formuły, jeśli określona wartość procentowa próbek nie jest dostępna. W tym przypadku są wprowadzane żadne zmiany, aby rozmiar puli. Aby określić wymagane wartość procentowa próbek w wersji ewaluacyjnej została wykonana pomyślnie, należy określić go jako trzeci parametr `GetSample()`. W tym miejscu określono wymaganie 75 procent próbek:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Przykładowe dostępności może być opóźnienie, należy zawsze określić zakres czasu z godziną rozpoczęcia ponownie spojrzenie, która jest starsza niż minutę. To trwa około minuty dla przykładów Propagacja za pośrednictwem systemu, więc przykłady w zakresie `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` mogą nie być dostępne. Ponownie, można użyć parametru procent `GetSample()` do wymuszenia wymagań procent określonej próbki.

> [!IMPORTANT]
> Firma Microsoft **zdecydowanie zaleca się** , **uniknąć jednostki uzależnionej *tylko* na `GetSample(1)` w formułach skalowania automatycznego**. Jest to spowodowane `GetSample(1)` zasadniczo jest wyświetlany komunikat do usługi Batch "Zapewniają mi ostatniego przykładowy, możesz mieć, niezależnie od tego, jak dawno temu pobrano go." Ponieważ jest tylko jednego przykładu i może być starsze próbki, może nie być przedstawiciel większy obraz ostatnie zadanie lub stan zasobów. Jeśli używasz `GetSample(1)`, upewnij się, że jest on częścią większej instrukcji i nie zależy od swojej formule punktu tylko dane.
>
>

## <a name="metrics"></a>Metryki
Możesz użyć metryki zasobu i zadania podczas definiowania formuły. Docelowa Liczba dedykowanych węzłów w puli na podstawie danych metryk, Uzyskaj i oceny można dostosować. Zobacz [zmienne](#variables) Powyższa sekcja więcej informacji na temat wszystkie metryki.

<table>
  <tr>
    <th>Metryka</th>
    <th>Opis</th>
  </tr>
  <tr>
    <td><b>Zasób</b></td>
    <td><p>Metryki zasobów są oparte na procesor CPU, przepustowości, użycia pamięci przez węzły obliczeniowe i liczbę węzłów.</p>
        <p> Te zmienne zdefiniowane przez usługę są przydatne w przypadku wprowadzania dostosowań na podstawie liczby węzłów:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$preemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Te zmienne zdefiniowane przez usługę są przydatne w przypadku wprowadzania dostosowań na podstawie użycia zasobów węzła:</p>
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
    <td><b>Zadanie podrzędne</b></td>
    <td><p>Metryki zadania są na podstawie stanu zadań, takich jak aktywny, oczekujący i zakończone. Następujące zmienne zdefiniowane przez usługę są przydatne, aby wprowadzić zmiany rozmiaru puli na podstawie metryk zadań:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Zapis formułę skalowania automatycznego
Możesz utworzyć formułę skalowania automatycznego przez tworzenie instrukcji używających powyżej składników, a następnie łączenie tych instrukcji do całej formuły. W tej sekcji utworzymy przykład formułę skalowania automatycznego, które może wykonywać niektóre decyzje skalowania rzeczywistych.

Najpierw zdefiniujmy wymagań dotyczących naszych nową formułę skalowania automatycznego. Formuła powinna:

1. Zwiększ docelowa Liczba dedykowanych węzłów obliczeniowych w puli, jeśli użycie procesora CPU jest wysokie.
2. Zmniejsz docelowa Liczba dedykowanych węzłów obliczeniowych w puli, gdy użycie Procesora jest niskie.
3. Zawsze należy ograniczyć maksymalną liczbę dedykowanych węzłów do 400.

Aby zwiększyć liczbę węzłów podczas wysokie użycie procesora CPU, zdefiniuj instrukcji, która wypełnia zmienną zdefiniowanych przez użytkownika (`$totalDedicatedNodes`) z wartością, która jest 110 procent bieżącego docelowa liczba węzłów dedykowanych, ale tylko wtedy, gdy minimalna średniego użycia procesora CPU podczas ostatnich 10 minut był przekracza 70%. W przeciwnym razie użyj wartości bieżącej liczby dedykowanych węzłów.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Do *zmniejszyć* Liczba dedykowanych węzłów podczas niskie użycie procesora CPU, następnej instrukcji w naszej formule ustawia takie same `$totalDedicatedNodes` zmiennej do 90 procent firm z bieżącego docelowa liczba węzłów dedykowanych, jeśli średnie użycie procesora CPU w przeszłości 60 minuty w obszarze był 20 procent. W przeciwnym razie użyj bieżącą wartość `$totalDedicatedNodes` , wypełniona w powyższych instrukcji.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Teraz Ogranicz docelowa Liczba dedykowanych węzłów obliczeniowych do maksymalnie 400:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Oto Kończenie formuły:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-net"></a>Tworzenie puli włączone automatyczne skalowanie przy użyciu platformy .NET

Aby utworzyć pulę przy użyciu skalowania automatycznego jest włączona na platformie .NET, wykonaj następujące kroki:

1. Tworzenie puli za pomocą [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
2. Ustaw [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) właściwość `true`.
3. Ustaw [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) właściwości za pomocą formuły skalowania automatycznego.
4. (Opcjonalnie) Ustaw [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) właściwość (wartość domyślna to 15 minut).
5. Zatwierdź puli z [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) lub [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

Poniższy fragment kodu tworzy pulę włączona funkcja automatycznego skalowania na platformie .NET. Formułę skalowania automatycznego w puli, ustawia docelowa liczba węzły dedykowane do 5 w poniedziałek i 1 dla każdego dnia, tygodnia. [Interwał automatycznego skalowania](#automatic-scaling-interval) jest ustawiona na 30 minut. W tym i innych C# fragmenty kodu w tym artykule `myBatchClient` jest prawidłowo zainicjowany wystąpieniem [BatchClient] [ net_batchclient] klasy.

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
> Podczas tworzenia puli z włączoną funkcją automatycznego skalowania nie określaj _targetDedicatedNodes_ parametru lub _targetLowPriorityNodes_ parametr w wywołaniu **CreatePool** . Zamiast tego należy określić **AutoScaleEnabled** i **AutoScaleFormula** właściwości puli. Wartości tych właściwości określenia docelowej liczby każdy typ węzła. Również, aby ręcznie włączyć Skalowanie automatyczne zmienianie rozmiaru puli (na przykład za pomocą [BatchClient.PoolOperations.ResizePoolAsync][net_poolops_resizepoolasync]), jest to pierwszy **wyłączyć** automatyczne skalowanie w puli, a następnie zmień jego rozmiar.
>
>

Oprócz platforma .NET usługi Batch, możesz użyć dowolnej z innych [zestawów SDK usługi Batch](batch-apis-tools.md#azure-accounts-for-batch-development), [interfejs REST usługi Batch](https://docs.microsoft.com/rest/api/batchservice/), [poleceń cmdlet programu PowerShell usługi Batch](batch-powershell-cmdlets-get-started.md)i [interfejsu wiersza polecenia usługi Batch](batch-cli-get-started.md) do Konfigurowanie skalowania automatycznego.


### <a name="automatic-scaling-interval"></a>Interwał automatycznego skalowania
Domyślnie usługa Batch dopasowuje rozmiar puli zgodnie z jego formułę skalowania automatycznego co 15 minut. Ten interwał jest konfigurowane za pomocą następujących właściwości puli:

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval] [ rest_autoscaleinterval] (interfejs API REST)

Minimalny interwał wynosi 5 minut, a wartość maksymalna to 168 godzin. Jeśli określono interwału, poza tym zakresem, usługa Batch zwraca wartość błąd, który nieprawidłowe żądanie (400).

> [!NOTE]
> Skalowanie automatyczne aktualnie nie jest przeznaczona do reagowania na zmiany w mniej niż minutę, ale raczej jest przeznaczony do dopasowania rozmiaru puli stopniowo podczas uruchamiania obciążenia.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Włącz Skalowanie automatyczne w istniejącej puli

Każdy zestaw SDK usługi Batch zapewnia sposób włączania skalowania automatycznego. Na przykład:

* [BatchClient.PoolOperations.EnableAutoScaleAsync][net_enableautoscaleasync] (Batch .NET)
* [Włącz automatyczne skalowanie puli] [ rest_enableautoscale] (interfejs API REST)

Po włączeniu automatycznego skalowania w istniejącej puli należy przestrzegać następujące kwestie:

* Jeśli automatyczne skalowanie jest obecnie wyłączona w puli w przypadku wysyłania żądania, aby umożliwić skalowanie automatyczne, należy określić formułę skalowania automatycznego prawidłowe w przypadku wysyłania żądania. Opcjonalnie można określić interwał obliczania automatycznego skalowania. Jeśli nie określisz interwał, jest używana wartość domyślna wynosząca 15 minut.
* Automatyczne skalowanie jest obecnie włączona w puli, można określić formułę skalowania automatycznego i/lub interwał oceny. Należy określić co najmniej jedną z tych właściwości.

  * Jeśli określisz nowy interwał obliczania automatycznego skalowania, istniejący harmonogram oceny jest zatrzymana, a nowy harmonogram jest uruchamiany. Czas rozpoczęcia nowego harmonogramu jest czas, w którym został wystawiony żądanie, aby włączyć Skalowanie automatyczne.
  * Jeżeli pominięto formułę skalowania automatycznego lub interwał oceny, usługa partia zadań w dalszym ciągu używać bieżącej wartości tego ustawienia.

> [!NOTE]
> Jeśli podano wartości dla *targetDedicatedNodes* lub *targetLowPriorityNodes* parametry **CreatePool** metody podczas tworzenia puli na platformie .NET lub dla podczas automatycznego skalowania formuła jest oceniana, porównywalnych parametrów w innym języku, a następnie te wartości są ignorowane.
>
>

Ten fragment kodu języka C# używa [platformy .NET usługi Batch] [ net_api] biblioteki, aby umożliwić skalowanie automatyczne w istniejącej puli:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Aktualizacja formułę skalowania automatycznego

Aby zaktualizować formuły w istniejącej puli z włączoną funkcją automatycznego skalowania, wywołaj operację, aby umożliwić skalowanie automatyczne ponownie, podając nową formułę. Na przykład, jeśli automatyczne skalowanie jest już włączone dla `myexistingpool` po wykonaniu poniższego kodu .NET jego formułę skalowania automatycznego jest zastępowany zawartość `myNewFormula`.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Interwał automatycznego skalowania aktualizacji

Aby zaktualizować interwał obliczania automatycznego skalowania w istniejącej puli, z obsługą skalowania automatycznego, wywołaj operację, aby umożliwić skalowanie automatyczne ponownie, podając nowy interwał. Na przykład, aby ustawić interwał obliczania automatycznego skalowania do 60 minut dla puli, który jest już włączona funkcja automatycznego skalowania na platformie .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Oceń formułę skalowania automatycznego

Możesz ocenić formuły, zanim zostaną one zastosowane do puli. W ten sposób można przetestować formułę, aby zobaczyć, jak ocenić jej instrukcji przed umieszczeniem na formułę w środowisku produkcyjnym.

Aby ocenić formułę skalowania automatycznego, należy włączyć Skalowanie automatyczne w puli z prawidłową formułę. Aby przetestować formuły w puli, która nie ma jeszcze z włączonym skalowaniem automatycznym, użyj formuły jednowierszowych `$TargetDedicatedNodes = 0` po pierwszym włączeniu funkcji skalowania automatycznego. Następnie użyj jednego z następujących do oceny formułę, którą chcesz przetestować:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) or [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Te metody .NET usługi Batch wymagają identyfikator istniejącej puli i ciąg zawierający formułę skalowania automatycznego do oceny.

* [Oceń formułę skalowania automatycznego](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    W tym żądania interfejsu API REST, określ identyfikator puli w identyfikatorze URI i formułę skalowania automatycznego w *autoScaleFormula* elementu treści żądania. Odpowiedź operacji zawiera informacje o błędzie, który może być związany z formuły.

W tym [platformy .NET usługi Batch] [ net_api] fragment kodu Oceniamy formułę skalowania automatycznego. Jeśli pula nie ma włączone Skalowanie automatyczne, możemy ją najpierw włączyć.

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

Pomyślne oceny formuły pokazano w poniższym przykładzie generuje wyniki podobne do:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Uzyskać informacje na temat skalowania automatycznego uruchomienia

Aby upewnić się, że Twoja formuła działa zgodnie z oczekiwaniami, firma Microsoft zaleca, okresowo sprawdzać wyniki uruchomienia skalowania automatycznego, wykonywanych przez usługi Batch w puli. Tak, Pobierz (lub Odśwież) odwołanie do puli i zbadać właściwości jego ostatniej skalowania automatycznego uruchamiania.

Na platformie .NET usługi Batch [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) właściwość ma kilka właściwości, które dostarczają informacji na temat najnowszej automatyczne skalowanie uruchomienia wykonywane w puli:

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

W interfejsie API REST [uzyskać informacje na temat puli](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) żądania zwraca informacje o puli, która zawiera najnowszy automatyczne skalowanie uruchamianie informacji w [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) właściwości.

Poniższy fragment kodu języka C# korzysta z biblioteki .NET usługi Batch do drukowania informacji o ostatnim Skalowanie automatyczne uruchamianie w puli _myPool_:

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

## <a name="example-autoscale-formulas"></a>Przykład skalowania automatycznego formuły
Spójrzmy na kilka formuł, które pokazują różne sposoby, aby dostosować ilość zasobów obliczeniowych w puli.

### <a name="example-1-time-based-adjustment"></a>Przykład 1: Dopasowania na podstawie czasu
Załóżmy, że chcesz dopasować rozmiar puli na podstawie dnia tygodnia i pora dnia. W tym przykładzie pokazano, jak można zwiększyć lub zmniejszyć liczbę węzłów w puli, odpowiednio.

Formuła najpierw uzyskuje bieżącą godzinę. Jeśli jest dniem powszednim (1-5) i w godzinach pracy (8: 00 do 18: 00), rozmiar puli docelowej jest równa z 20 węzłów. W przeciwnym razie ustawiana jest na 10 węzłów.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Przykład 2: Dostosowanie opartego na zadaniach
W tym przykładzie rozmiar puli jest dostosowywany na podstawie liczby zadań w kolejce. Podziały wierszy i komentarze są dopuszczalne w ciągach formuły.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Przykład 3: Dla zadań równoległych
W tym przykładzie dopasowuje rozmiar puli na podstawie liczby zadań. Ta formuła również uwzględnia [MaxTasksPerComputeNode] [ net_maxtasks] wartość, która została ustawiona dla puli. Takie podejście jest przydatne w sytuacjach, w którym [równoległe wykonywanie zadań](batch-parallel-node-tasks.md) została włączona w puli.

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

### <a name="example-4-setting-an-initial-pool-size"></a>Przykład 4: Ustawianie rozmiaru puli początkowej
W tym przykładzie przedstawiono fragment kodu języka C# za pomocą formuły skalowania automatycznego, która ustawia rozmiar puli do określonej liczby węzłów dla początkowego okresu. Następnie dostosowuje jego rozmiaru puli na podstawie liczby uruchomiona i aktywne zadania po upływie okresu czasu początkowej.

Formuła w poniższym fragmencie kodu:

* Ustawia rozmiar puli początkowej czterech węzłów.
* Nie może dopasować rozmiar puli w ciągu 10 minut cyklu życia tej puli.
* Po upływie 10 minut, uzyskuje wartość maksymalna liczba zadań uruchomionych i aktywne w ciągu ostatnich 60 minut.
  * Jeśli obie wartości są równe 0 (co oznacza, że zadania nie zostały uruchomione lub aktywne w ciągu ostatnich 60 minut), rozmiar puli jest równa 0.
  * Jeśli każda wartość jest większa niż zero, nie zostanie zmienione.

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

## <a name="next-steps"></a>Kolejne kroki
* [Zmaksymalizuj użycie zasobów obliczeniowych usługi Azure Batch przy użyciu równoczesne zadania węzła](batch-parallel-node-tasks.md) zawiera szczegóły dotyczące sposobu wykonania wielu zadań jednocześnie w węzłach obliczeniowych w puli. Oprócz skalowania automatycznego ta funkcja może pomóc zmniejszyć czas trwania zadania dla niektórych obciążeń zaoszczędzić pieniądze.
* Inną wydajność zestawu wspomagającego upewnij się, że aplikacji usługi Batch korzysta z usługi Batch w sposób najbardziej optymalną. Zobacz [zapytań do usługi Azure Batch wydajnie](batch-efficient-list-queries.md) dowiesz się, jak ograniczyć ilość danych w sieci po wykonaniu kwerendy stanu tysięcy węzłów obliczeniowych lub zadania.

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
