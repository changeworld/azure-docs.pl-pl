---
title: 'Przykład: analiza wideo w czasie rzeczywistym — interfejs API rozpoznawania emocji'
titlesuffix: Azure Cognitive Services
description: Interfejs API rozpoznawania emocji umożliwia przeprowadzanie analizy klatek pobieranych ze strumienia wideo na żywo w czasie zbliżonym do rzeczywistego.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: sample
ms.date: 01/25/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: df955a23393c82565e8f31e59e148798a0f89bbf
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48236484"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>Przykład: jak analizować filmy wideo w czasie rzeczywistym

> [!IMPORTANT]
> Interfejs API rozpoznawania emocji zostanie wycofany w dniu 15 lutego 2019 r. Rozpoznawanie emocji jest teraz ogólnie dostępne jako część [interfejsu API rozpoznawania twarzy](https://docs.microsoft.com/azure/cognitive-services/face/).

W tym przewodniku pokazano, jak w czasie zbliżonym do rzeczywistego przeprowadzać analizę klatek pobieranych ze strumienia wideo na żywo. Podstawowe składniki takiego systemu obejmują:
- Pobieranie klatek ze źródła wideo
- Wybieranie klatek do analizy
- Przesyłanie tych klatek do interfejsu API
- Korzystanie z poszczególnych wyników analizy zwracanych przez wywołania interfejsu API

Te przykłady są napisane w języku C#, a ich kod można znaleźć w witrynie GitHub w tym miejscu: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>Podejście
Istnieje wiele sposobów rozwiązania problemu związanego z uruchamianiem analizy strumieni wideo, która działa w czasie zbliżonym do rzeczywistego. Na początku przedstawimy trzy metody o coraz większym stopniu skomplikowania.

### <a name="a-simple-approach"></a>Prosta metoda
Najprostszy projekt systemu analizy działającej w czasie zbliżonym do rzeczywistego polega na użyciu nieskończonej pętli. W każdej iteracji pobieramy klatkę, analizujemy ją, a następnie wykorzystujemy wynik:
```CSharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```
Jeśli analiza jest oparta na prostym algorytmie po stronie klienta, taka metoda jest odpowiednia. Jednak gdy analiza odbywa się w chmurze, występuje opóźnienie, co sprawia, że wywołanie interfejsu API może trwać kilka sekund. W tym czasie nie przechwytujemy obrazów, a wątek w zasadzie nic nie robi. Maksymalna liczba klatek na sekundę jest ograniczona przez opóźnienia wywołań interfejsu API.

### <a name="parallelizing-api-calls"></a>Równoległe wywołania interfejsu API
Prosta, jednowątkowa pętla sprawdza się w przypadku uproszczonego algorytmu po stronie klienta, ale nie jest odpowiednia, gdy występuje opóźnienie związane z wywołaniami interfejsu API w chmurze. Rozwiązanie tego problemu polega na równoległym przechwytywaniu klatek i wykonywaniu długotrwałych wywołań interfejsu API. W języku C# możemy uzyskać ten efekt przy użyciu równoległości opartej na zadaniach, na przykład:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () =>
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

Ten kod umożliwia uruchamianie poszczególnych analiz w oddzielnych zadaniach, które działają w tle, gdy są przechwytywane klatki. Pozwala to uniknąć blokowania głównego wątku podczas oczekiwania na zwrócenie wywołania interfejsu API. Tracimy jednak część funkcjonalności zapewnianej przez prostą wersję — równolegle może wystąpić wiele wywołań interfejsu API, ale ich wyniki mogą zostać zwrócone w nieprawidłowej kolejności. Może to także spowodować równoczesne wprowadzenie wielu wątków do funkcji ConsumeResult(), co może okazać się ryzykowne, jeśli funkcja ta nie jest bezpieczna wątkowo. Oprócz tego ten prosty kod nie śledzi tworzonych zadań, co uniemożliwia obsługę wyjątków, które będą znikać w trybie cichym. W związku z tym na końcu musimy dodać wątek „konsumenta”, który pozwala śledzić zadania analizy, zgłaszać wyjątki i przerywać długotrwałe zadania, a także gwarantuje, że wyniki będą używane pojedynczo w odpowiedniej kolejności.

### <a name="a-producer-consumer-design"></a>Architektura producent-konsument
W końcowym systemie „producent-konsument” mamy wątek producenta, który jest bardzo podobny do poprzedniej nieskończonej pętli. Jednak producent nie korzysta z wyników analizy od razu po ich udostępnieniu, tylko po prostu umieszcza zadania w kolejce, co umożliwia ich śledzenie.
```CSharp
// Queue that will contain the API call tasks.
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();
     
// Producer thread.
while (true)
{
    // Grab a frame.
    Frame f = GrabFrame();
 
    // Decide whether to analyze the frame.
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread.
        var analysisTask = Task.Run(async () =>
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }

        // Push the task onto the queue.
        taskQueue.Add(analysisTask);
    }
}
```
Mamy również wątek konsumenta, który pobiera zadania z kolejki, czeka na ich ukończenie, po czym wyświetla wynik lub zgłasza wyjątek. Zastosowanie kolejki gwarantuje, że wyniki są używane pojedynczo i we właściwej kolejności, a dostępna w systemie maksymalna liczba klatek na sekundę nie jest ograniczana.
```CSharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed.
    var output = await analysisTask;
     
    // Consume the exception or result.
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implementing-the-solution"></a>Implementowanie rozwiązania
### <a name="getting-started"></a>Wprowadzenie
Aby jak najszybciej rozpocząć korzystanie z aplikacji, zaimplementowaliśmy opisany system w sposób, który zapewnia łatwość użytkowania oraz elastyczność wystarczającą do wdrożenia wielu scenariuszy. Aby uzyskać dostęp do kodu, otwórz stronę [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

Biblioteka ta zawiera klasę FrameGrabber, która implementuje omówiony system producent-konsument umożliwiający przetwarzanie klatek wideo z kamery internetowej. Użytkownik może określić dokładną formę wywołania interfejsu API. Za pomocą zdarzeń klasa przekazuje do kodu wywołującego informację o pozyskaniu nowej klatki lub dostępności nowego wyniku analizy.

Dwie przykładowe aplikacje korzystające z tej biblioteki ilustrują niektóre z tych możliwości. Pierwsza z nich to prosta aplikacja konsolowa, a jej uproszczona wersja jest przedstawiona poniżej. Pobiera ona klatki z domyślnej kamery internetowej i przekazuje je do interfejsu API rozpoznawania twarzy w celu wykrycia twarzy.
```CSharp
using System;
using VideoFrameAnalyzer;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;
     
namespace VideoFrameConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create grabber, with analysis type Face[].
            FrameGrabber<Face[]> grabber = new FrameGrabber<Face[]>();

            // Create Face API Client. Insert your Face API key here.
            FaceServiceClient faceClient = new FaceServiceClient("<subscription key>");

            // Set up our Face API call.
            grabber.AnalysisFunction = async frame => return await faceClient.DetectAsync(frame.Image.ToMemoryStream(".jpg"));

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.Analysis != null)
                    Console.WriteLine("New result received for frame acquired at {0}. {1} faces detected", e.Frame.Metadata.Timestamp, e.Analysis.Length);
            };

            // Tell grabber to call the Face API every 3 seconds.
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for keypress to stop
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```
Druga przykładowa aplikacja jest nieco bardziej interesująca. Umożliwia ona wybór interfejsu API, który ma zostać wywołany do przetwarzania klatek wideo. W lewej części okna aplikacji jest wyświetlany podgląd wideo na żywo, a w prawej części — najnowszy wynik analizy interfejsu API nałożony na odpowiednią klatkę.

W większości trybów będzie występować widoczne opóźnienie między obrazem wideo na żywo po lewej stronie a wizualizowaną analizą po prawej stronie. To opóźnienie jest związane z czasem wymaganym do wykonania wywołania interfejsu API. Wyjątkiem jest tryb „EmotionsWithClientFaceDetect”, który korzysta z biblioteki OpenCV do przeprowadzenia operacji wykrywania twarzy na lokalnym komputerze klienckim przed przesłaniem jakichkolwiek obrazów do usług Cognitive Services. Dzięki temu można natychmiast zwizualizować wykrytą twarz, a później, po zakończeniu wywołania interfejsu API, zaktualizować emocje. Takie podejście ma charakter „hybrydowy” — niektóre proste operacje przetwarzania można wykonywać na komputerze klienckim, a w razie potrzeby można użyć interfejsów API usług Cognitive Services w celu przeprowadzenia bardziej zaawansowanej analizy.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrowanie z bazą kodu
Aby zacząć pracę z tym przykładem, wykonaj następujące kroki:

1. Ze strony [Subskrypcje](https://azure.microsoft.com/try/cognitive-services/) pobierz klucze interfejsu API przetwarzania obrazów. Interfejsy API umożliwiające analizę klatek wideo obejmują:
    - [Interfejs API przetwarzania obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Interfejs API rozpoznawania emocji](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [Interfejs API rozpoznawania twarzy](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Sklonuj repozytorium GitHub [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

3. Otwórz przykład w programie Visual Studio 2015, a następnie skompiluj go i uruchom przykładowe aplikacje:
    - W przypadku aplikacji BasicConsoleSample klucz interfejsu API rozpoznawania twarzy jest zapisany bezpośrednio w kodzie w pliku [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - W przypadku aplikacji LiveCameraSample klucze należy wprowadzić w aplikacji w okienku Ustawienia. Zostaną one utrwalone między sesjami jako dane użytkownika.


Gdy wszystko będzie gotowe do integracji, **we własnych projektach wystarczy po prostu odwołać się do biblioteki VideoFrameAnalyzer**.



## <a name="developer-code-of-conduct"></a>Kodeks postępowania dewelopera
Podobnie jak w przypadku innych usług Cognitive Services, deweloperzy korzystający z naszych interfejsów API i przykładów są zobowiązani do postępowania zgodnie z dokumentem „[Developer Code of Conduct for Azure Cognitive Services](https://azure.microsoft.com/support/legal/developer-code-of-conduct/)” („Kodeks postępowania dla deweloperów usług Azure Cognitive Services”).


Funkcje biblioteki VideoFrameAnalyzer umożliwiające interpretację obrazu, głosu, wideo lub tekstu korzystają z usług Azure Cognitive Services. Do firmy Microsoft są wysyłane obrazy, pliki audio i wideo oraz inne dane przekazywane za pośrednictwem tej aplikacji. Dane te mogą zostać użyte w celu udoskonalania usług. Prosimy o pomoc w ochronie prywatności osób, których dane są wysyłane do usług Azure Cognitive Services przy użyciu aplikacji.


## <a name="summary"></a>Podsumowanie
W tym przewodniku wyjaśniono, jak przeprowadzać analizę strumieni wideo na żywo w czasie zbliżonym do rzeczywistego przy użyciu interfejsów API rozpoznawania twarzy, przetwarzania obrazów i rozpoznawania emocji oraz pokazano, jak rozpocząć pracę, korzystając z naszego przykładowego kodu.  Możesz rozpocząć pracę nad aplikacją przy użyciu bezpłatnych kluczy interfejsu API na [stronie tworzenia konta usług Microsoft Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

Zachęcamy do przekazywania opinii i sugestii w [repozytorium GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) lub — w przypadku obszerniejszych informacji zwrotnych dotyczących interfejsów API — w naszej [witrynie UserVoice](https://cognitive.uservoice.com/).
