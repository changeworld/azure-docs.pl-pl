---
title: Kwerendy wydajności po stronie serwera
description: Jak wykonywać kwerendy wydajności po stronie serwera za pośrednictwem wywołań interfejsu API
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 9a28dee2d1e6d1355b729a56e8eeb8447e4ed8c8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682028"
---
# <a name="server-side-performance-queries"></a>Kwerendy wydajności po stronie serwera

Dobra wydajność renderowania na serwerze ma kluczowe znaczenie dla stabilnej liczby klatek na sekundę i dobrego doświadczenia użytkownika. Ważne jest, aby uważnie monitorować charakterystykę wydajności serwera i optymalizować w razie potrzeby. Dane dotyczące wydajności można wyszukiwać za pomocą dedykowanych funkcji interfejsu API.

Najbardziej wpływowe dla wydajności renderowania są dane wejściowe modelu. Można dostosować dane wejściowe zgodnie z opisem w [konfigurowanie konwersji modelu](../../how-tos/conversion/configure-model-conversion.md).

Wydajność aplikacji po stronie klienta może być wąskie gardło, zbyt. Aby uzyskać dogłębną analizę wydajności po stronie klienta, zaleca się [śledzenie wydajności.](../../how-tos/performance-tracing.md)

## <a name="clientserver-timeline"></a>Oś czasu klienta/serwera

Przed przejściem do szczegółów dotyczących różnych wartości opóźnienia, warto przyjrzeć się punkty synchronizacji między klientem i serwerem na osi czasu:

![Oś czasu potoku](./media/server-client-timeline.png)

Na ilustracji pokazano, jak:

* *a Oszacowanie Pose* jest uruchamiane przez klienta przy stałej szybkości klatek 60-Hz (co 16,6 ms)
* serwer uruchamia renderowanie na podstawie
* serwer odsyła zakodowany obraz wideo
* klient dekoduje obraz, wykonuje niektóre prace procesora i GPU na nim, a następnie wyświetla obraz

## <a name="frame-statistics-queries"></a>Kwerendy statystyk ramek

Statystyki ramek zawierają pewne informacje wysokiego poziomu dla ostatniej klatki, takie jak opóźnienie. Dane podane w `FrameStatistics` strukturze są mierzone po stronie klienta, więc interfejs API jest wywołaniem synchronickim:

````c#
void QueryFrameData(AzureSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
````

Pobrany `FrameStatistics` obiekt zawiera następujące elementy członkowskie:

| Członek | Wyjaśnienie |
|:-|:-|
| opóźnieniePoseToReceive | Opóźnienie z szacowania ułożenia kamery na urządzeniu klienckim, dopóki ramka serwera dla tej pozy jest w pełni dostępna dla aplikacji klienckiej. Ta wartość obejmuje w obie strony sieci, czas renderowania serwera, dekodowanie wideo i kompensację jittera. Patrz **interwał 1 na powyższej ilustracji.**|
| opóźnienieReceiveToPresent | Opóźnienie od dostępności odebranej ramki zdalnej, dopóki aplikacja kliencka wywołuje PresentFrame na procesorze CPU. |
| utajeniepresenttowyświetlać  | Opóźnienie prezentacji ramki na procesorze do momentu zaświecenia się wyświetlania. Ta wartość obejmuje czas procesora GPU klienta, buforowanie klatek wykonywane przez system operacyjny, ponowne wykonanie sprzętu i czas skanowania wyświetlacza zależny od urządzenia. Patrz **interwał 2 na powyższej ilustracji.**|
| czasSinceLastPresent | Czas między kolejnymi wywołaniami presentframe na procesorze CPU. Wartości większe niż czas wyświetlania (na przykład 16,6 ms na urządzeniu klienckim 60-Hz) wskazują na problemy spowodowane tym, że aplikacja kliencka nie zakończyła obciążenia procesora CPU w czasie. Patrz **interwał 3 na powyższej ilustracji.**|
| videoFramesReceived | Liczba ramek odebranych z serwera w ostatniej sekundzie. |
| videoFrameReusedCount | Liczba odebranych klatek w ostatniej sekundzie, które były używane na urządzeniu więcej niż raz. Wartości niezerowe wskazują, że ramki musiały być ponownie użyczone i ponownieprojected albo z powodu jitter sieci lub nadmierny czas renderowania serwera. |
| videoFramesSukapped | Liczba odebranych klatek w ostatniej sekundzie, które zostały zdekodowane, ale nie są wyświetlane na wyświetlaczu, ponieważ nadeszła nowsza ramka. Wartości niezerowe wskazują, że jittering sieci spowodowane wiele ramek, które mają być opóźnione, a następnie dotrzeć na urządzeniu klienckim razem w serii. |
| videoFramesZdeksycarded | Bardzo podobny do **videoFramesSkipped**, ale powodem odrzucenia jest to, że ramka weszła tak późno, że nie może być nawet skorelowane z każdym oczekującym stanowią już. Jeśli tak się stanie, istnieje poważne spory sieci.|
| videoFrameMinDelta | Minimalny czas między dwoma kolejnymi klatkami przychodzącymi w ciągu ostatniej sekundy. Wraz z videoFrameMaxDelta, zakres ten daje wskazanie trema spowodowane przez sieć lub kodek wideo. |
| videoFrameMaxDelta | Maksymalny czas między dwoma kolejnymi klatkami przychodzącymi w ciągu ostatniej sekundy. Wraz z videoFrameMinDelta, zakres ten daje wskazanie trema spowodowane przez sieć lub kodek wideo. |

Suma wszystkich wartości opóźnienia jest zazwyczaj znacznie większa niż czas dostępnej klatki przy 60 Hz. Jest to OK, ponieważ wiele klatek jest w locie równolegle, a nowe żądania klatek są uruchamiane z żądaną szybkością klatek, jak pokazano na ilustracji. Jednak jeśli opóźnienie staje się zbyt duże, wpływa na jakość [ponownegoprojection późnego etapu](../../overview/features/late-stage-reprojection.md)i może zagrozić ogólne doświadczenie.

`videoFramesReceived``videoFrameReusedCount`, i `videoFramesDiscarded` może służyć do pomiaru wydajności sieci i serwera. Jeśli `videoFramesReceived` jest `videoFrameReusedCount` niski i jest wysoki, może to wskazywać na przeciążenie sieci lub niską wydajność serwera. Wysoka `videoFramesDiscarded` wartość wskazuje również przeciążenie sieci.

Wreszcie`timeSinceLastPresent`, `videoFrameMinDelta`i `videoFrameMaxDelta` dać wyobrażenie o wariancji przychodzących klatek wideo i lokalnych obecnych połączeń. Wysoka wariancja oznacza niestabilną liczbę klatek na sekundę.

Żadna z powyższych wartości nie wskazuje wyraźnego opóźnienia sieci (czerwone strzałki na ilustracji), ponieważ dokładny czas, przez jaki `latencyPoseToReceive`serwer jest zajęty renderowaniem, musi zostać odjąć od wartości roundtrip . Część po stronie serwera ogólnego opóźnienia jest informacja, która jest niedostępna dla klienta. Jednak następny akapit wyjaśnia, jak ta wartość jest przybliżona `networkLatency` za pośrednictwem dodatkowych danych wejściowych z serwera i udostępniane za pośrednictwem wartości.

## <a name="performance-assessment-queries"></a>Kwerendy dotyczące oceny wydajności

*Zapytania dotyczące oceny wydajności* zawierają bardziej szczegółowe informacje na temat obciążenia procesora CPU i procesora GPU na serwerze. Ponieważ dane są wymagane z serwera, wykonywanie zapytań migawki wydajności następuje zwykle wzorzec asynchronii:

``` cs
PerformanceAssessmentAsync _assessmentQuery = null;

void QueryPerformanceAssessment(AzureSession session)
{
    _assessmentQuery = session.Actions.QueryServerPerformanceAssessmentAsync();
    _assessmentQuery.Completed += (PerformanceAssessmentAsync res) =>
    {
        // do something with the result:
        PerformanceAssessment result = res.Result;
        // ...

        _assessmentQuery = null;
    };
}
```

W przeciwieństwie `FrameStatistics` do `PerformanceAssessment` obiektu, obiekt zawiera informacje po stronie serwera:

| Członek | Wyjaśnienie |
|:-|:-|
| timeCPU | Średni czas procesora serwera na klatkę w milisekundach |
| timeGPU | Średni czas gpu serwera na klatkę w milisekundach |
| procesor wykorzystania | Całkowite wykorzystanie procesora CPU serwera w procentach |
| wykorzystanieGPU | Całkowite wykorzystanie gpu serwera w procentach |
| memoryCPU | Całkowite wykorzystanie pamięci głównej serwera w procentach |
| memoryGPU | Całkowite wykorzystanie dedykowanej pamięci wideo w procentach procesora graficznego serwera |
| siećLatywa | Przybliżone średnie opóźnienie sieci w obie strony w milisekundach. Na powyższej ilustracji odpowiada to sumie czerwonych strzałek. Wartość jest obliczany przez odjęcie rzeczywistego `latencyPoseToReceive` czasu `FrameStatistics`renderowania serwera od wartości . Chociaż to przybliżenie nie jest dokładne, daje pewne wskazanie opóźnienia sieci, izolowane od wartości opóźnienia obliczonych na kliencie. |
| wielokątyRendered | Liczba trójkątów renderowanych w jednej ramce. Liczba ta obejmuje również trójkąty, które są wybite później podczas renderowania. Oznacza to, że liczba ta nie różni się znacznie w różnych pozycjach kamery, ale wydajność może się drastycznie różnić, w zależności od wskaźnika uboju trójkąta.|

Aby pomóc ci ocenić wartości, każda porcja ma klasyfikację jakości, **taką**jak Great , **Good**, **Mediocre**lub **Bad.**
Ta metryka oceny zapewnia przybliżone wskazanie kondycji serwera, ale nie powinna być postrzegana jako bezwzględna. Załóżmy na przykład, że widzisz "przeciętny" wynik dla czasu GPU. Jest uważany za przeciętny, ponieważ zbliża się do limitu dla ogólnego budżetu ramowego. W twoim przypadku jednak może to być dobra wartość, ponieważ renderujesz złożony model.

## <a name="statistics-debug-output"></a>Dane wyjściowe debugowania statystyk

Klasa `ARRServiceStats` zawija się wokół statystyk ramki i zapytań oceny wydajności i zapewnia wygodne funkcje do zwracania statystyk jako zagregowane wartości lub jako wstępnie utworzony ciąg. Poniższy kod jest najprostszym sposobem, aby wyświetlić statystyki po stronie serwera w aplikacji klienckiej.

``` cs
ARRServiceStats _stats = null;

void OnConnect()
{
    _stats = new ARRServiceStats();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

Powyższy kod wypełnia etykietę tekstową następującym tekstem:

![ArrServiceStats wyjście ciągu](./media/arr-service-stats.png)

Interfejs `GetStatsString` API formatuje ciąg wszystkich wartości, ale każda pojedyncza wartość może `ARRServiceStats` być również wyszukiwana programowo z wystąpienia.

Istnieją również warianty elementów członkowskich, które agregują wartości w czasie. Zobacz elementy członkowskie `*Avg`z `*Max`przyrostkiem , lub `*Total`. Element `FramesUsedForAverage` członkowski wskazuje, ile ramek zostało użytych do tej agregacji.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie śladów wydajności](../../how-tos/performance-tracing.md)
* [Konfigurowanie konwersji modelu](../../how-tos/conversion/configure-model-conversion.md)
