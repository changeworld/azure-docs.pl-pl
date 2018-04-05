---
title: Automatyczne skalowanie obliczeniowe węzłów w puli partii zadań Azure | Dokumentacja firmy Microsoft
description: Włącz automatyczne skalowanie w puli chmury można dynamicznie zmieniać liczbę węzłów obliczeniowych w puli.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: multiple
ms.date: 06/20/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1114ea90ae6976a3bc3580ebae5fd853de0274a1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Utwórz formułę Skalowanie automatyczne skalowanie węzłów obliczeniowych w puli partii

Partia zadań Azure może automatycznie skalować puli na podstawie parametrów zdefiniowanych przez użytkownika. Automatyczne skalowanie partii dynamicznie dodaje węzły do puli jako wzrost wymagań zadań i usuwa jako zmniejszają węzły obliczeniowe. Automatycznie dostosowując liczba węzłów obliczeniowych używanych przez aplikację partii można zapisać zarówno czas i pieniądze. 

Możesz włączyć automatyczne skalowanie węzłów obliczeniowych puli można skojarzyć z nim *formułą automatycznego skalowania* zdefiniowana. Usługa partia zadań korzysta formułą automatycznego skalowania, aby określić liczbę węzłów obliczeniowych, które są wymagane do wykonania obciążenie. Obliczeniowe węzły mogą być dedykowanych węzłów lub [węzłów niskiego priorytetu](batch-low-pri-vms.md). Wsadowe odpowiada okresowo zbieranych danych metryki usługi. Przy użyciu tych danych metryki, partii dostosowuje liczbę węzłów obliczeniowych w puli na podstawie formuły i interwałem można konfigurować.

Można włączyć automatyczne skalowanie, gdy tworzona jest pula lub na istniejącej puli. Można również zmienić istniejącej formuły w puli, która jest skonfigurowana dla Skalowanie automatyczne. Partii umożliwia ocenę formułach przed przypisywanie ich do pul oraz do monitorowania stanu automatycznego skalowania działa.

W tym artykule omówiono różnymi jednostkami wchodzące w skład formułach skalowania automatycznego, w tym zmienne, operatory, operacje i funkcje. Omówiono sposób uzyskiwania różnych obliczeń zasób i zadanie metryk w partii. Te metryki umożliwia dostosowanie liczba węzłów z puli na podstawie stanu zasobu użycia i zadań. Następnie opisano sposobu konstruowania formuły i Włącz automatyczne skalowanie w puli przy użyciu REST partii i interfejsów API architektury .NET. Na koniec kończymy z kilku przykład formuły.

> [!IMPORTANT]
> Podczas tworzenia konta usługi partia zadań można określić [konfiguracji konta](batch-api-basics.md#account), która określa, czy pule są przydzielane w ramach subskrypcji usługi partii (ustawienie domyślne) lub w ramach subskrypcji użytkownika. Jeśli utworzono konta partii zadań z domyślnej konfiguracji usługi partia zadań Twoje konto jest ograniczona do maksymalna liczba rdzeni, które mogą być używane do przetwarzania. Usługa partia zadań skalowanie węzłów obliczeniowych do tego limitu core. Z tego powodu usługa partia zadań nie może nawiązać połączenia docelowej liczba węzłów obliczeniowych określony formułą automatycznego skalowania. Zobacz [przydziały i limity dla usługi partia zadań Azure](batch-quota-limit.md) informacji na temat przeglądania i zwiększenie przydziały Twojego konta.
>
>Jeśli Twoje konto zostało utworzone przy użyciu konfiguracji subskrypcji użytkownika, Twoje konto udziałów w limit przydziału rdzeni dla subskrypcji. Aby uzyskać więcej informacji, zobacz sekcję [Virtual Machines limits (Limity maszyn wirtualnych)](../azure-subscription-service-limits.md#virtual-machines-limits) w artykule [Azure subscription and service limits, quotas, and constraints (Ograniczenia, przydziały i limity usług i subskrypcji platformy Azure)](../azure-subscription-service-limits.md).
>
>

## <a name="automatic-scaling-formulas"></a>Automatyczne skalowanie formuł
Formuła skalowania automatycznego jest wartość ciągu przez Ciebie, która zawiera jedną lub więcej instrukcji. Formuła skalowania automatycznego jest przypisany do puli [autoScaleFormula] [ rest_autoscaleformula] elementu (REST partii) lub [CloudPool.AutoScaleFormula] [ net_cloudpool_autoscaleformula] Właściwość (partiami platformy .NET). Usługa partia zadań używa formułę do określania docelowych liczba węzłów obliczeniowych w puli przy następnym interwale przetwarzania. Ciąg formuły nie może być dłuższa niż 8 KB, może zawierać maksymalnie 100 instrukcji, które są oddzielone średnikami i może zawierać podziałów wierszy i komentarze.

Można traktować automatycznego skalowania formuł jako skalowania automatycznego partii "język". Instrukcje formuły są wolne sformułowanych wyrażeń zawierających zarówno usługa zdefiniowana zmiennych (zmiennych zdefiniowanych przez usługi partia zadań), jak i zmienne zdefiniowane przez użytkownika (zmiennych zdefiniowanych przez użytkownika). Mogą oni wykonywać różne operacje tych wartości za pomocą wbudowanych typów, Operatorzy i funkcje. Na przykład instrukcja może mieć następującą formę:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formuły zwykle zawierają wiele instrukcji, które wykonują operacje na wartości, które zostały uzyskane w poprzednim instrukcje. Na przykład, najpierw możemy uzyskać wartości `variable1`, następnie przekazać do funkcji, aby wypełnić `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Dołącz te instrukcje formuły skalowania automatycznego na docelowym liczba węzłów obliczeniowych. Dedykowanych węzłów, jak i węzły niskiego priorytetu mają własnych ustawień obiektu docelowego, dzięki czemu można zdefiniować docelowy dla każdego typu węzła. Formuła automatycznego skalowania może zawierać wartość docelowa dla dedykowanych węzłów i/lub wartość docelowa dla węzłów o niskim priorytecie.

Docelowy liczba węzłów mogą być wyższe, małe lub taka sama jak bieżąca liczba węzłów tego typu w puli. Wsadowe ocenia formułą automatycznego skalowania puli w określonych odstępach czasu (zobacz [automatyczne skalowanie interwałów](#automatic-scaling-interval)). Partii dostosowuje liczbę docelowych każdy typ węzła w puli, aby liczba, która określa formułę automatycznego skalowania w chwili szacowania.

### <a name="sample-autoscale-formula"></a>Przykładowa formuła skalowania automatycznego

Oto przykład formuły skalowania automatycznego, którą można dostosować do pracy w przypadku większości scenariuszy. Zmienne `startingNumberOfVMs` i `maxNumberofVMs` w przykładzie formuły można dostosować do własnych potrzeb. W tej formule skaluje dedykowanych węzłów, ale można zmodyfikować, aby dotyczą również węzłów niskiego priorytetu skali. 

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

Tą formułą automatycznego skalowania puli został początkowo utworzony z jednej maszyny Wirtualnej. `$PendingTasks` Metryka definiuje liczbę zadań, które są uruchomione lub umieszczonych w kolejce. Formuła znajduje średnią liczbę zadań oczekujących w ostatnich 180 sekund i zestawy `$TargetDedicatedNodes` zmiennej odpowiednio. Formuła gwarantuje, że docelowy Liczba dedykowanych węzłów nigdy nie przekracza 25 maszyn wirtualnych. Nowe zadania zostały przesłane, automatycznie zwiększa rozmiar puli. Jako zakończenie zadania wolnego jeden po drugim stają się maszyn wirtualnych i formuła Skalowanie automatyczne zmniejszana puli.

## <a name="variables"></a>Zmienne
Można użyć zarówno **usługa zdefiniowana** i **zdefiniowane przez użytkownika** zmiennych w formułach skalowania automatycznego. Zmienne zdefiniowane przez usługi są wbudowane w celu usługi partia zadań. Niektóre zmienne zdefiniowane usługi są odczytu i zapisu, a niektóre są tylko do odczytu. Zmienne zdefiniowane przez użytkownika są zmiennych zdefiniowanych przez użytkownika. W formule przykładzie pokazano w poprzedniej sekcji `$TargetDedicatedNodes` i `$PendingTasks` są zmienne zdefiniowane przez usługę. Zmienne `startingNumberOfVMs` i `maxNumberofVMs` są zmienne zdefiniowane przez użytkownika.

> [!NOTE]
> Zmienne zdefiniowane przez usługi są zawsze poprzedzone znak dolara ($). W przypadku zmiennych zdefiniowanych przez użytkownika znak dolara jest opcjonalna.
>
>

W poniższej tabeli przedstawiono zmienne odczytu zapisu i tylko do odczytu, które są definiowane przez usługi partia zadań.

Możesz pobrać i ustawić wartości tych zmiennych zdefiniowanych przez usługę, aby zarządzać liczbą węzłów obliczeniowych w puli:

| Zmienne zdefiniowane przez usługę do odczytu / zapisu | Opis |
| --- | --- |
| $TargetDedicatedNodes |Docelowy Liczba dedykowanych obliczeniowe węzłów dla tej puli. Liczba dedykowanych węzłów jest określony jako element docelowy, ponieważ pula nie zawsze będzie odpowiednią liczbę węzłów. Na przykład docelowy Liczba dedykowanych węzłów jest modyfikowany przez ocenę automatycznego skalowania przed puli osiągnęła początkowej docelowej, następnie puli może nie osiągnąć obiektu docelowego. <br /><br /> Pulą konto utworzone z konfiguracją usługi partia zadań nie może osiągnąć elementem docelowym, jeśli element docelowy przekracza przydział węzła lub core konta wsadowego. Pulą konto utworzone przy użyciu konfiguracji subskrypcji użytkownika nie może osiągnąć elementem docelowym, jeśli element docelowy przekracza limit przydziału rdzeni udostępnionego dla subskrypcji.|
| $TargetLowPriorityNodes |Liczba docelowych niskiego priorytetu obliczeniowe węzłów dla tej puli. Liczba węzłów o niskim priorytecie jest określony jako element docelowy, ponieważ pula nie zawsze będzie odpowiednią liczbę węzłów. Na przykład docelowej liczby węzłów niskiego priorytetu jest modyfikowany przez ocenę skalowania automatycznego przed puli osiągnęła początkowej docelowej, następnie puli może nie osiągnąć cel. Pula również może nie będzie elementem docelowym, jeśli element docelowy przekracza przydział węzła lub core konta wsadowego. <br /><br /> Aby uzyskać więcej informacji na węzłach obliczeniowych niskiego priorytetu, zobacz [niskiego priorytetu maszyn wirtualnych za pomocą partii (wersja zapoznawcza)](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Akcja, która występuje, gdy węzły obliczeniowe są usuwane z puli. Możliwe wartości:<ul><li>**requeue**--natychmiast kończy zadań i umieszczane w kolejce zadań, dzięki czemu są one zmienił.<li>**przerwanie**— natychmiast kończy zadań i usuwa je z kolejki zadań.<li>**taskcompletion**--oczekuje aktualnie uruchomione zadania, aby zakończyć, a następnie usuwa węzeł z puli.<li>**retaineddata**--czeka na wszystkich danych lokalnych zachowane zadań na węzeł, który ma zostać wyczyszczone przed usunięciem węzeł z puli.</ul> |

Można pobrać wartości tych zmiennych zdefiniowanych przez usługę wprowadzanie korekt, oparte na metryki z usługi partia zadań:

| Zmienne tylko do odczytu usługa zdefiniowana | Opis |
| --- | --- |
| $CPUPercent |Średni procent użycia procesora CPU. |
| $WallClockSeconds |Liczba zużywane sekund. |
| $MemoryBytes |Średnia liczba megabajtów używane. |
| $DiskBytes |Średnia liczba gigabajtów używane na lokalnych dyskach. |
| $DiskReadBytes |Liczba bajtów odczytanych. |
| $DiskWriteBytes |Liczba zapisanych bajtów. |
| $DiskReadOps |Liczba operacji odczytu dysku. |
| $DiskWriteOps |Liczba operacji dysku zapisu. |
| $NetworkInBytes |Liczba bajtów przychodzących. |
| $NetworkOutBytes |Liczba bajtów wychodzących. |
| $SampleNodeCount |Liczba węzłów obliczeniowych. |
| $ActiveTasks |Liczba zadań, które są gotowe do wykonania, ale nie są jeszcze wykonywany. Licznik $ActiveTasks obejmuje wszystkie zadania, które znajdują się w stanie aktywnym i którego zależności zostały spełnione. Wszystkie zadania, które są w stanie aktywnym, ale których zależności nie zostały spełnione są wykluczone z liczby $ActiveTasks.|
| $RunningTasks |Liczba zadań w stanie uruchomienia. |
| $PendingTasks |Suma $ActiveTasks i $RunningTasks. |
| $SucceededTasks |Liczba zadań, które zakończyło się pomyślnie. |
| $FailedTasks |Liczba zadań, których nie powiodła się. |
| $CurrentDedicatedNodes |Bieżąca liczba dedykowanych węzły obliczeniowe. |
| $CurrentLowPriorityNodes |Bieżąca liczba niskiego priorytetu obliczeniowe węzły, w tym wszystkie węzły, które zostały opóźnieniem. |
| $PreemptedNodeCount | Liczba węzłów w puli, które są w stanie opóźnieniem. |

> [!TIP]
> Zmienne tylko do odczytu, usługa zdefiniowana, które są wyświetlane w poprzedniej tabeli są *obiektów* zawierających różne metody dostępu do danych skojarzonych z każdym. Aby uzyskać więcej informacji, zobacz [uzyskać przykładowe dane](#getsampledata) dalszej części tego artykułu.
>
>

## <a name="types"></a>Typy
Te typy są obsługiwane w formule:

* O podwójnej precyzji
* doubleVec
* doubleVecList
* ciąg
* Sygnatura czasowa — sygnatura czasowa jest złożone struktura, która zawiera następujące składniki:

  * rok
  * miesiąc (1-12)
  * dzień (1-31)
  * dzień tygodnia (w formacie numeru; na przykład 1, od poniedziałku)
  * Godzina (w formacie liczby 24-godzinnym; na przykład 13 oznacza 13: 00)
  * minut (00 do 59)
  * drugie (00 do 59)
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
| Podwójna *operator* podwójne |+, -, *, / |O podwójnej precyzji |
| Podwójna *operator* parametru timeinterval |* |timeinterval |
| doubleVec *operator* podwójne |+, -, *, / |doubleVec |
| doubleVec *operator* doubleVec |+, -, *, / |doubleVec |
| parametru TimeInterval *operator* podwójne |*, / |timeinterval |
| parametru TimeInterval *operator* parametru timeinterval |+, - |timeinterval |
| parametru TimeInterval *operator* sygnatury czasowej |+ |sygnatura czasowa |
| Sygnatura czasowa *operator* parametru timeinterval |+ |sygnatura czasowa |
| Sygnatura czasowa *operator* sygnatury czasowej |- |timeinterval |
| *operator*podwójne |-, ! |O podwójnej precyzji |
| *operator*timeinterval |- |timeinterval |
| Podwójna *operator* podwójne |<, <=, ==, >=, >, != |O podwójnej precyzji |
| ciąg *operator* ciągu |<, <=, ==, >=, >, != |O podwójnej precyzji |
| Sygnatura czasowa *operator* sygnatury czasowej |<, <=, ==, >=, >, != |O podwójnej precyzji |
| parametru TimeInterval *operator* parametru timeinterval |<, <=, ==, >=, >, != |O podwójnej precyzji |
| Podwójna *operator* podwójne |&&, &#124;&#124; |O podwójnej precyzji |

Podczas testowania wartość o podwójnej precyzji z trójargumentowy (`double ? statement1 : statement2`), jest różna od zera jest **true**, i zero jest **false**.

## <a name="functions"></a>Funkcje
Te wstępnie zdefiniowane **funkcje** są dostępne do użycia podczas definiowania automatycznego skalowania formuły.

| Funkcja | Zwracany typ | Opis |
| --- | --- | --- |
| AVG(doubleVecList) |O podwójnej precyzji |Zwraca średnią wartość dla wszystkich wartości w doubleVecList. |
| Len(doubleVecList) |O podwójnej precyzji |Zwraca długość wektora, która jest tworzona na podstawie doubleVecList. |
| LG(Double) |O podwójnej precyzji |Zwraca dziennik podstawowej 2 podwójnego. |
| lg(doubleVecList) |doubleVec |Zwraca podstawie 2 doubleVecList component-wise dziennika. Vec(double) muszą być jawnie przekazywane w parametrze. W przeciwnym razie przyjęto wersji lg(double) dwa razy. |
| ln(Double) |O podwójnej precyzji |Zwraca logarytm naturalny podwójnego. |
| ln(doubleVecList) |doubleVec |Zwraca podstawie 2 doubleVecList component-wise dziennika. Vec(double) muszą być jawnie przekazywane w parametrze. W przeciwnym razie przyjęto wersji lg(double) dwa razy. |
| log(Double) |O podwójnej precyzji |Zwraca dziennik podwójnego o podstawie 10. |
| log(doubleVecList) |doubleVec |Zwraca dziennik component-wise doubleVecList o podstawie 10. Vec(double) muszą być jawnie przekazywane dla jednego parametru dwa razy. W przeciwnym razie przyjęto wersji log(double) dwa razy. |
| max(doubleVecList) |O podwójnej precyzji |Zwraca maksymalną wartość doubleVecList. |
| min(doubleVecList) |O podwójnej precyzji |Zwraca minimalną wartość doubleVecList. |
| norm(doubleVecList) |O podwójnej precyzji |Zwraca dwa normy wektora, która jest tworzona na podstawie doubleVecList. |
| percentyl (v doubleVec podwójne p) |O podwójnej precyzji |Zwraca element percentyl wektora v. |
| rand() |O podwójnej precyzji |Zwraca wartość losowe od 0,0 do 1,0. |
| range(doubleVecList) |O podwójnej precyzji |Zwraca różnicę między minimalną i maksymalną wartości doubleVecList. |
| STD(doubleVecList) |O podwójnej precyzji |Zwraca odchylenie standardowe przykładowe wartości doubleVecList. |
| stop() | |Zatrzymuje obliczania wyrażenia Skalowanie automatyczne. |
| sum(doubleVecList) |O podwójnej precyzji |Zwraca sumę wszystkich składników doubleVecList. |
| czas (string, dateTime = "") |sygnatura czasowa |Zwraca sygnaturę czasową bieżącego czasu, jeśli nie przekazano żadnych parametrów lub sygnatura czasowa ciągu daty i godziny, jeśli został przekazany. Formaty obsługiwanych dateTime są W3C DTF i RFC 1123. |
| Val (v doubleVec i dwa razy) |O podwójnej precyzji |Zwraca wartość elementu, który znajduje się w lokalizacji i w przypadku wektora v z indeksem początkowej zero. |

Niektóre funkcje, które zostały opisane w poprzedniej tabeli można zaakceptować listy jako argument. Rozdzielana przecinkami lista jest dowolną kombinację *podwójne* i *doubleVec*. Na przykład:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

*DoubleVecList* wartość jest konwertowana na jeden *doubleVec* przed oceny. Na przykład jeśli `v = [1,2,3]`, wywołując `avg(v)` jest odpowiednikiem wywołania `avg(1,2,3)`. Wywoływanie `avg(v, 7)` jest odpowiednikiem wywołania `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Uzyskaj przykładowe dane
Funkcja automatycznego skalowania formuły działają na metryki danych (przykłady) dostarczonego przez usługi partia zadań. Formuła powiększa się lub zmniejsza rozmiar puli na podstawie wartości uzyskiwanych z usługi. Definicja usługi zmiennych, które zostały opisane wcześniej są obiekty, które zapewniają różne metody dostępu do danych, który jest skojarzony z tym obiektem. Na przykład poniższe wyrażenie zawiera żądanie get ostatnich pięciu minut użycia procesora CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Metoda | Opis |
| --- | --- |
| GetSample() |`GetSample()` Metoda zwraca wektor próbek danych.<br/><br/>Przykładowe wynosi 30 sekund warto danych metryki. Innymi słowy przykłady są uzyskiwane co 30 sekund. Ale przypadków wymienionych poniżej, opóźnienie między po próbki są zbierane i gdy jest ona dostępna do formuły. Tak nie wszystkie przykłady w danym czasie może być dostępny do szacowania przez formułę.<ul><li>`doubleVec GetSample(double count)`<br/>Określa liczbę próbek można uzyskać z najnowsze przykłady, które zostały zebrane.<br/><br/>`GetSample(1)` Zwraca ostatniej próbki dostępne. Dla metryk, takich jak `$CPUPercent`, jednak to nie stosuje się, ponieważ nie jest możliwe, należy wiedzieć *podczas* próbki został zebrany. Może być ostatnie lub z powodu problemów dotyczących systemu, może być znacznie starsza. Najlepiej w takich przypadkach można użyć przedział czasu, jak pokazano poniżej.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Określa przedział czasu zbierania przykładowych danych. Opcjonalnie określa również wartość procentowa próbek, które muszą być dostępne w ramach czasowych żądanej.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` zwróci 20 próbek, gdy wszystkie próbki dla ostatnich 10 minut znajdują się w historii CPUPercent. Jeśli w ciągu ostatniej minuty Historia nie jest dostępna, jednak tylko 18 przykłady będzie zwracany. W takim przypadku:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` nie powiedzie się, ponieważ nie są dostępne tylko 90 procent próbek.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` powiedzie się.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Określa przedział czasu zbierania danych z godziny rozpoczęcia i godzina zakończenia.<br/><br/>Jak wspomniano powyżej, istnieje opóźnienie między po próbki są zbierane i gdy jest ona dostępna do formuły. Należy rozważyć to opóźnienie, korzystając z `GetSample` metody. Zobacz `GetSamplePercent` poniżej. |
| GetSamplePeriod() |Zwraca okres próbek, które zostały wykonane w zestawie danych historycznych próbki. |
| Count() |Zwraca łączna liczba próbek w historii metryki. |
| HistoryBeginTime() |Zwraca sygnaturę czasową najstarsze próbki dostępnych danych dla metryki. |
| GetSamplePercent() |Zwraca wartość procentowa próbek, które są dostępne dla danego interwału. Na przykład:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Ponieważ `GetSample` metody zakończy się niepowodzeniem, jeśli wartość procentowa próbek zwracany jest mniejsza niż `samplePercent` określony, można użyć `GetSamplePercent` metodę, aby sprawdzić w pierwszej kolejności. Następnie można wykonać akcję alternatywną, jeśli brakuje przykłady są obecne, bez zatrzymania automatycznego skalowania oceny. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Przykłady, wartość procentowa próbki i *GetSample()* — metoda
Zapewnienia podstawowej funkcjonalności formułę skalowania automatycznego jest uzyskania danych metryki zadań i zasobów, a następnie Dostosuj rozmiar puli na podstawie tych danych. Jest tak, należy dysponować przejrzysty sposób skalowania automatycznego formuły interakcji z metryki danych (przykłady).

**Przykłady**

Usługa partia zadań okresowo przyjmuje przykłady zadań i zasobu metryki i udostępnia formułach skalowania automatycznego. Te przykłady są rejestrowane co 30 sekund, przez usługi partia zadań. Istnieje jednak zwykle opóźnienia między kiedy te przykłady zostały zarejestrowane oraz w przypadku ich stają się dostępne do (i może zostać odczytany przez) formułach skalowania automatycznego. Ponadto ze względu na różne czynniki, takie jak sieci lub inne problemy z infrastrukturą, próbki mogą nie zostać zarejestrowane dla określonego interwału.

**Przykładowe wartości procentowej**

Gdy `samplePercent` jest przekazywana do `GetSample()` — metoda lub `GetSamplePercent()` metoda jest wywoływana, _procent_ odwołuje się do porównania całkowita liczba próbek, które są rejestrowane przez usługę partii i liczba przykłady, które są dostępne z formułą automatycznego skalowania.

Na przykład Przyjrzyjmy timespan 10 minut. Ponieważ próbki są rejestrowane co 30 sekund w timespan 10 minut, maksymalna liczba próbek, które są rejestrowane przez partię będzie 20 próbek (2 na minutę). Jednak z powodu opóźnienia związanego z używaniem mechanizmu raportowania i inne problemy w systemie Azure, może być tylko 15 przykłady, które są dostępne dla formuły skalowania automatycznego do odczytu. Tak na przykład, w tym okresie 10-minutowych tylko 75% łączna liczba próbek, rejestrowane mogą być dostępne do formuły.

**Zakresy GetSample() i próbki**

Formułach skalowania automatycznego mają być powiększania i zmniejszanie z pul &mdash; Dodawanie węzłów i usuwanie węzłów. Ponieważ węzłów koszt pieniądze, chcesz upewnij się, że formułach Użyj inteligentnego metody analizy, który jest oparty na wystarczającą ilość danych. Dlatego zaleca się używać analizy typu analizy trendów w formułach. Ten typ rozwoju i zmniejsza z puli na podstawie zakresu zbieranych próbek.

Aby to zrobić, użyj `GetSample(interval look-back start, interval look-back end)` do zwrócenia wektor próbek:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Jeśli wiersz powyżej jest oceniane w partii, zwraca zakres próbek jako wektor wartości. Na przykład:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Po zostały zebrane wektor próbek, można następnie użyć funkcji, takich jak `min()`, `max()`, i `avg()` do uzyskania istotnych wartości z zakresu zebrane.

Aby dodatkowo zwiększyć bezpieczeństwo możesz wymusić formuły, aby zakończyć się niepowodzeniem, jeśli jest mniejsza niż określony procent próbki dostępnych w określonym czasie. Po wymuszeniu formuły niepowodzenie, poinstruuj się partii przestaną dalsze oceny formuły, jeśli określona wartość procentowa próbek nie jest dostępna. W tym przypadku są wprowadzane żadne zmiany, aby rozmiar puli. Aby określić wymagana wartość procentowa próbek w wersji ewaluacyjnej powiodło się, należy określić go jako trzeci parametr `GetSample()`. W tym miejscu określono wymaganie 75 procent próbek:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Ponieważ mogą występować opóźnienia w dostępności próbki, należy zawsze określić zakres czasu, czas rozpoczęcia wygląd Wstecz, która jest starsza niż minuta. Trwa około minuty dla przykładów propagację za pośrednictwem systemu, więc przykłady w zakresie `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` mogą nie być dostępne. Ponownie, można użyć parametru procent `GetSample()` wymusić wymaganie procent określonej próbki.

> [!IMPORTANT]
> Firma Microsoft **zdecydowanie zaleca się** że **uniknąć jednostki uzależnionej *tylko* na `GetSample(1)` w formułach skalowania automatycznego**. Jest to spowodowane `GetSample(1)` zasadniczo mówi, aby usługa partia zadań "Mnie ostatniej próbki, należy podać ma, niezależnie od tego, jak dawno temu pobraniu." Ponieważ jest tylko jeden przykład i mogą być starsze próbki, nie może być przedstawiciel większy obraz ostatnie zadanie lub stan zasobów. Jeśli używasz `GetSample(1)`, upewnij się, że jest część instrukcję większy i nie zależy od formuły punktu danych tylko.
>
>

## <a name="metrics"></a>Metryki
Podczas definiowania formuły, można użyć zarówno zasobów, jak i zadanie metryki. Dostosuj jest docelowy Liczba dedykowanych węzłów w puli na podstawie danych metryki uzyskać i oceny. Zobacz [zmienne](#variables) sekcji powyżej, aby uzyskać więcej informacji na wszystkie metryki.

<table>
  <tr>
    <th>Metryka</th>
    <th>Opis</th>
  </tr>
  <tr>
    <td><b>Zasób</b></td>
    <td><p>Metryki zasobów są oparte na procesor CPU, przepustowości, użycie pamięci przez węzły obliczeniowe i liczby węzłów.</p>
        <p> Te zmienne zdefiniowane usługi są przydatne do korekt na podstawie liczby węzłów:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$preemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Te zmienne zdefiniowane usługi są przydatne do korekt na podstawie użycia zasobów węzła:</p>
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
    <td><p>Metryki zadań opartych na stan zadania, takie jak aktywny, oczekujące i zakończone. Następujące zmienne zdefiniowane przez usługi są przydatne, aby wprowadzić zmiany rozmiaru puli, w oparciu metryki zadań:</p>
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
Tworzenie formułą automatycznego skalowania przez tworzenie instrukcji, które używają powyżej składników następnie łączenie tych instrukcji do ukończenia formuły. W tej sekcji utworzymy skalowania automatycznego przykład formuły, które można wykonywać pewne decyzje skalowania rzeczywistych.

Najpierw zdefiniujmy wymagania dotyczące naszej nowej formuły skalowania automatycznego. Formuła następujące czynności:

1. Jeśli użycie procesora CPU jest wysokie, należy zwiększyć liczbę docelowych węzłów obliczeniowych dedykowanych w puli.
2. Zmniejszyć liczbę docelowych węzłów obliczeniowych dedykowanych w puli, gdy wykorzystanie Procesora jest niskie.
3. Zawsze należy ograniczyć maksymalna liczba dedykowanych węzłów do 400.

Aby zwiększyć liczbę węzłów podczas wysokiego użycia procesora CPU, zdefiniuj instrukcji, która wypełnia zmiennej zdefiniowanej przez użytkownika (`$totalDedicatedNodes`) z wartością, która jest 110 procent bieżący numer docelowej dedykowanych węzłów, ale tylko wtedy, gdy minimalna średniego użycia procesora CPU podczas ostatnich 10 minut była powyżej 70 procent. W przeciwnym razie użyj wartości dla bieżąca liczba dedykowanych węzłów.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Do *zmniejszyć* Liczba dedykowanych węzłów podczas niski użycia procesora CPU, następnej instrukcji w naszym formuły zestawów takie same `$totalDedicatedNodes` zmienną do 90 procent bieżącego docelowa Liczba dedykowanych węzłów, jeśli średniego użycia procesora CPU w ciągu ostatnich 60 minut w obszarze został 20 procent. W przeciwnym razie użyj bieżącą wartość `$totalDedicatedNodes` który mamy wypełniony w powyższych instrukcji.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Teraz można ograniczyć liczbę docelowych węzłów obliczeniowych dedykowane do maksymalnie 400:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Oto całej formuły:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-net"></a>Utwórz pulę włączona funkcja automatycznego skalowania z platformą .NET

Aby utworzyć pulę z Skalowanie automatyczne włączona w programie .NET, wykonaj następujące kroki:

1. Utwórz pulę z [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
2. Ustaw [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) właściwości `true`.
3. Ustaw [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) właściwości z formułą automatycznego skalowania.
4. (Opcjonalnie) Ustaw [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) właściwość (wartość domyślna to 15 minut).
5. Zatwierdź puli z [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) lub [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

Poniższy fragment kodu tworzy włączona funkcja automatycznego skalowania puli .NET. Formuła automatycznego skalowania puli ustawia docelowy Liczba dedykowanych węzłów do 5 w poniedziałek i 1 dla każdego dnia, tygodnia. [Interwał automatycznego skalowania](#automatic-scaling-interval) jest ustawiona na 30 minut. W tym i innych C# fragmenty kodu w tym artykule `myBatchClient` jest prawidłowo zainicjowany wystąpieniem [BatchClient] [ net_batchclient] klasy.

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "small", // single-core, 1.75 GB memory, 225 GB disk
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));    
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Podczas tworzenia puli włączone skalowania automatycznego nie należy określać _targetDedicatedComputeNodes_ parametru lub _targetLowPriorityComputeNodes_ parametr w wywołaniu **CreatePool** . Zamiast tego określ **AutoScaleEnabled** i **AutoScaleFormula** właściwości w puli. Wartości tych właściwości zliczane docelowym każdego typu węzła. Ponadto do ręcznie zmiany rozmiaru włączona funkcja automatycznego skalowania puli (na przykład z [BatchClient.PoolOperations.ResizePoolAsync][net_poolops_resizepoolasync]), pierwszy **wyłączyć** automatyczne skalowanie na w puli, a następnie zmienić jego rozmiar.
>
>

Oprócz partiami platformy .NET, można użyć dowolnego z innych [wsadowego zestawów SDK](batch-apis-tools.md#azure-accounts-for-batch-development), [REST partii](https://docs.microsoft.com/rest/api/batchservice/), [poleceń cmdlet programu PowerShell partii](batch-powershell-cmdlets-get-started.md)i [CLI partii](batch-cli-get-started.md) do Konfigurowanie skalowania automatycznego.


### <a name="automatic-scaling-interval"></a>Interwał automatycznego skalowania
Domyślnie usługa partia zadań dopasowuje rozmiar puli zgodnie z jego formułą automatycznego skalowania co 15 minut. Interwał ten jest można skonfigurować za pomocą następujących właściwości puli:

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval] [ rest_autoscaleinterval] (interfejsu API REST)

Minimalny interwał to pięć minut, a maksymalna to 168 godzin. Jeśli określono interwał poza tym zakresem, usługa partia zadań zwrócą błąd nieprawidłowe żądanie (400).

> [!NOTE]
> Skalowanie automatyczne aktualnie nie jest przeznaczona do reagowania na zmiany w mniej niż minutę, ale raczej jest przeznaczona do dopasowania rozmiaru puli stopniowo w momencie uruchamiania obciążeń.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Włącz funkcję skalowania automatycznego na istniejącej puli

Zestaw SDK każdej partii udostępnia sposób, aby umożliwić skalowanie automatyczne. Na przykład:

* [BatchClient.PoolOperations.EnableAutoScaleAsync][net_enableautoscaleasync] (Batch .NET)
* [Włącz automatyczne skalowanie w puli] [ rest_enableautoscale] (interfejsu API REST)

Po włączeniu Skalowanie automatyczne w istniejącej puli pamiętać następujące kwestie:

* Jeśli automatyczne skalowanie jest obecnie wyłączona w puli w przypadku wysyłania żądania, aby umożliwić skalowanie automatyczne, należy określić formułę prawidłowy automatycznego skalowania w przypadku wysyłania żądania. Opcjonalnie można określić interwał oceny skalowania automatycznego. Jeśli nie określisz interwał, jest używana wartość domyślna wynosząca 15 minut.
* Jeśli skalowania automatycznego jest aktualnie włączone w puli, można określić formułą automatycznego skalowania i interwał oceny. Należy określić co najmniej jeden z tych właściwości.

  * Jeśli określisz nowy interwał oceny skalowania automatycznego istniejącego harmonogramu szacowania został zatrzymany, a nowy harmonogram jest uruchomiona. Czas rozpoczęcia nowy harmonogram jest czas, w którym został wystawiony żądania, aby umożliwić skalowanie automatyczne.
  * W przypadku pominięcia formułą automatycznego skalowania lub interwał oceny, usługa partia zadań będzie później nadal używać bieżącą wartość tego ustawienia.

> [!NOTE]
> Jeśli określono wartości dla *targetDedicatedComputeNodes* lub *targetLowPriorityComputeNodes* parametry **CreatePool** metody podczas tworzenia puli w .NET, lub porównywalny parametrów w innym języku, następnie wartości te są ignorowane podczas szacowania automatycznego skalowania formuły.
>
>

Następujący fragment kodu C# używa [partiami platformy .NET] [ net_api] biblioteki, aby umożliwić skalowanie automatyczne w istniejącej puli:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Aktualizacja formułą automatycznego skalowania

Aby zaktualizować formułę w istniejącej puli włączona funkcja automatycznego skalowania, wywołaj operację, aby umożliwić skalowanie automatyczne ponownie, używając nowej formuły. Na przykład, jeśli już włączone Skalowanie automatyczne `myexistingpool` podczas wykonywania następującego kodu platformy .NET, jego formuły skalowania automatycznego jest zastępowany zawartość `myNewFormula`.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Interwał automatycznego skalowania aktualizacji

Aby zaktualizować interwał oceny skalowania automatycznego istniejącej puli włączona funkcja automatycznego skalowania, wywołaj operację, aby umożliwić skalowanie automatyczne ponownie, używając nowego interwału. Na przykład, aby ustawić interwał oceny skalowania automatycznego do 60 minut dla puli, która jest już włączona funkcja automatycznego skalowania w .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Ocena formułą automatycznego skalowania

Formuły można obliczyć zanim zostaną one zastosowane do puli. W ten sposób można przetestować formuły, aby zobaczyć, jak jego instrukcje oceny jego przydatności przed umieszczeniem formuły w środowisku produkcyjnym.

Aby ocenić formułą automatycznego skalowania, należy najpierw włączyć Skalowanie automatyczne w puli z prawidłową formułę. Aby przetestować formuły w puli, która nie ma jeszcze włączone Skalowanie automatyczne, należy użyć jednego wiersza formuły `$TargetDedicatedNodes = 0` po pierwszym włączeniu Skalowanie automatyczne. Następnie służą do oceny formułę, którą chcesz przetestować jedną z następujących czynności:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) lub [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Te metody partiami platformy .NET wymagają identyfikator określający istniejącą pulę, a ciąg zawierający formułą automatycznego skalowania do oceny.

* [Szacowanie formuły skalowania automatycznego](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    W tym żądania interfejsu API REST, określ identyfikator puli w identyfikatorze URI i formułą automatycznego skalowania w *autoScaleFormula* elementu treści żądania. Odpowiedź operacji zawiera informacje o błędzie, który może być związana z formułą.

W tym [partiami platformy .NET] [ net_api] fragment kodu, możemy ocenić formułą automatycznego skalowania. Jeśli pula nie ma włączone Skalowanie automatyczne, możemy włączyć ją najpierw.

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
        autoscaleFormula: "$TargetDedicatedNodes = {pool.CurrentDedicatedNodes};",
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

Pomyślne obliczanie formuły pokazano w poniższym przykładzie tworzy wyniki podobne do:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Uzyskiwanie informacji o uruchomieniu automatycznego skalowania

Aby upewnić się, że formuła działa zgodnie z oczekiwaniami, firma Microsoft zaleca okresowo sprawdzać wyniki przebiegów Skalowanie automatyczne, wykonywanych w Twojej puli partii. Tak, Pobierz (lub Odśwież) odwołanie do puli i sprawdź, czy właściwości funkcja automatycznego skalowania jego ostatniego uruchomienia.

W partii .NET [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) właściwość ma kilka właściwości, które zawierają informacje o najnowszych automatyczne skalowanie Uruchom wykonywane w puli:

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

W interfejsie API REST [uzyskać informacje o puli](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) żądania zwraca informacje o puli, która zawiera najnowsze automatyczne skalowanie Uruchom informacji [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool#bk_autrun) właściwości.

Poniższy fragment kodu C# korzysta z biblioteki partiami platformy .NET do drukowania informacji o ostatnich Skalowanie automatyczne uruchamianie w puli _myPool_:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Przykładowe dane wyjściowe poprzedniego fragmentu:

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

## <a name="example-autoscale-formulas"></a>Przykład formuły skalowania automatycznego
Oto kilka formuły, które przedstawiono różne sposoby, aby dopasować ilość zasobów obliczeniowych w puli.

### <a name="example-1-time-based-adjustment"></a>Przykład 1: Dopasowania na podstawie czasu
Załóżmy, że chcesz dostosować rozmiar puli na podstawie dzień tygodnia i godzinę. W tym przykładzie przedstawiono sposób zwiększenia lub zmniejszenia liczby węzłów w puli odpowiednio.

Formuła najpierw pobiera bieżącą godzinę. Jeśli dzień (1-5), a w godzinach pracy (8 AM do 18: 00), rozmiar puli docelowej wynosi 20 węzłów. W przeciwnym razie wartość jest ustawiona na 10 węzłów.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Przykład 2: Dostosowywanie opartego na zadaniach
W tym przykładzie rozmiar puli jest uwzględniany w oparciu o liczbę zadań w kolejce. Zarówno podziały wierszy, jak i komentarze są dozwolone w ciągach formuły.

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

### <a name="example-3-accounting-for-parallel-tasks"></a>Przykład 3: Uwzględniająca zadań równoległych
W tym przykładzie można dostosować do rozmiaru puli na podstawie liczby zadań. W tej formule uwzględnia również [MaxTasksPerComputeNode] [ net_maxtasks] wartość, która została ustawiona dla tej puli. Takie rozwiązanie jest przydatne w sytuacjach, w którym [równoległe wykonywanie zadań](batch-parallel-node-tasks.md) został włączony na pulę.

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

### <a name="example-4-setting-an-initial-pool-size"></a>Przykład 4: Ustawienie rozmiaru puli początkowej
Ten przykład przedstawia fragment kodu C# formułą automatycznego skalowania, która ustawia rozmiar puli do określonej liczby węzłów w przedziale czasu początkowej. A następnie go dopasowuje rozmiar puli, w zależności od liczby uruchomiona i aktywne zadania po upływie okresu czasu początkowej.

Formuła w poniższy fragment kodu:

* Ustawia rozmiar puli początkowej czterech węzłów.
* W ciągu 10 minut życia puli nie Dostosuj rozmiar puli.
* Po 10 minutach uzyskuje wartość maksymalna liczba zadań uruchomionych i aktywne w ciągu ostatnich 60 minut.
  * Jeśli obie wartości są równe 0 (co oznacza, że żadne zadania zostały uruchomione lub aktywne w ciągu ostatnich 60 minut), rozmiar puli jest równa 0.
  * Jeśli każda wartość jest większa niż zero, są wprowadzane żadne zmiany.

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
* [Maksymalizowanie użycia zasobów obliczeniowych partii zadań Azure z węzła równoczesnych zadań](batch-parallel-node-tasks.md) zawiera szczegółowe informacje o sposobie można wykonywać wielu zadań jednocześnie w węzłach obliczeniowych w puli. Oprócz Skalowanie automatyczne ta funkcja może pomóc zmniejszyć czas trwania zadania dla niektórych obciążeń, co pozwala zaoszczędzić pieniądze.
* Dla innego wydajności zestawu wspomagającego upewnij się, że aplikacja partii zapytań usługa partia zadań w optymalny sposób. Zobacz [wydajnie zapytania usługi partia zadań Azure](batch-efficient-list-queries.md) Aby dowiedzieć się, jak ograniczyć ilość danych w sieci po wykonaniu kwerendy stanu tysięcy węzłów obliczeniowych lub zadania.

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
