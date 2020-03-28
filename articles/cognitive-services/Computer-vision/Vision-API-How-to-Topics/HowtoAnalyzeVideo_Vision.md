---
title: Analizowanie filmów w czasie zbliżonym do rzeczywistego - Computer Vision
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzać analizę w czasie zbliżonym do rzeczywistego klatek pobranych ze strumienia wideo na żywo przy użyciu interfejsu API przetwarzania obrazów.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 18b158b7a4881619b93ab404de67f7bb25f92b6a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166830"
---
# <a name="analyze-videos-in-near-real-time"></a>Analizowanie filmów w czasie zbliżonym do rzeczywistego

W tym artykule pokazano, jak wykonać analizę w czasie zbliżonym do rzeczywistego w klatkach, które są pobierane ze strumienia wideo na żywo przy użyciu interfejsu API przetwarzania obrazu. Podstawowymi elementami takiej analizy są:

- Pobieranie klatek ze źródła wideo.
- Wybór ramek do analizy.
- Przesyłanie tych ramek do interfejsu API.
- Korzystanie z każdego wyniku analizy, który jest zwracany z wywołania interfejsu API.

Przykłady w tym artykule są napisane w języku C#. Aby uzyskać dostęp do kodu, przejdź do przykładowej strony [analizy ramki wideo](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) w usłudze GitHub.

## <a name="approaches-to-running-near-real-time-analysis"></a>Podejścia do analizy w czasie zbliżonym do rzeczywistego

Można rozwiązać problem uruchamiania w czasie zbliżonym do analizy w czasie rzeczywistym na strumienie wideo przy użyciu różnych metod. W tym artykule przedstawiono trzy z nich, w rosnącym poziomie wyrafinowania.

### <a name="design-an-infinite-loop"></a>Projektowanie nieskończonej pętli

Najprostszym projektem analizy w czasie zbliżonym do rzeczywistego jest nieskończona pętla. W każdej iteracji tej pętli, pobrać ramkę, analizować go, a następnie zużywają wynik:

```csharp
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

Gdyby analiza składała się z lekkiego algorytmu po stronie klienta, takie podejście byłoby odpowiednie. Jednak gdy analiza występuje w chmurze, wynikowe opóźnienie oznacza, że wywołanie interfejsu API może potrwać kilka sekund. W tym czasie nie rejestrujesz obrazów, a twój wątek zasadniczo nic nie robi. Maksymalna liczba klatek na sekundę jest ograniczona przez opóźnienie wywołań interfejsu API.

### <a name="allow-the-api-calls-to-run-in-parallel"></a>Zezwalaj na równoległe uruchamianie wywołań interfejsu API

Mimo że prosta, pętla jednowątkowa ma sens dla lekkiego algorytmu po stronie klienta, nie pasuje dobrze do opóźnienia wywołania interfejsu API w chmurze. Rozwiązaniem tego problemu jest umożliwienie długotrwałego wywołania interfejsu API do uruchamiania równolegle z frame-grabbing. W języku C#, można to zrobić przy użyciu równoległości opartej na zadaniach. Na przykład można uruchomić następujący kod:

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

Dzięki takiemu podejściu można uruchomić każdą analizę w osobnym zadaniu. Zadanie można uruchomić w tle podczas dalszego chwytania nowych ramek. Podejście pozwala uniknąć blokowania wątku głównego, jak czekać na wywołanie interfejsu API do zwrócenia. Podejście to może jednak stwarzać pewne wady:
* To kosztuje niektóre z gwarancji, że prosta wersja pod warunkiem. Oznacza to, że wiele wywołań interfejsu API może wystąpić równolegle, a wyniki mogą zostać zwrócone w niewłaściwej kolejności. 
* Może to również spowodować, że wiele wątków, aby wprowadzić ConsumeResult() funkcja jednocześnie, co może być niebezpieczne, jeśli funkcja nie jest bezpieczne dla wątków. 
* Na koniec ten prosty kod nie śledzi zadań, które są tworzone, więc wyjątki po cichu znikają. W związku z tym należy dodać wątek "konsumenta", który śledzi zadania analizy, podnosi wyjątki, zabija długotrwałe zadania i zapewnia, że wyniki są używane w odpowiedniej kolejności, po jednym na raz.

### <a name="design-a-producer-consumer-system"></a>Projektowanie systemu producent-konsument

Dla ostatecznego podejścia, projektowanie systemu "producent-konsument", można zbudować wątek producenta, który wygląda podobnie do wcześniej wspomniano nieskończonej pętli. Jednak zamiast zużywać wyniki analizy, gdy tylko są one dostępne, producent po prostu umieszcza zadania w kolejce, aby śledzić je.

```csharp
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

Można również utworzyć wątek konsumenta, który pobiera zadania z kolejki, czeka na ich zakończenie i wyświetla wynik lub zgłasza wyjątek, który został zgłoszony. Korzystając z kolejki, można zagwarantować, że wyniki zostaną zużyte po jednym na raz, w odpowiedniej kolejności, bez ograniczania maksymalnej liczby klatek na sekundę systemu.

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Wait until the task is completed.
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

## <a name="implement-the-solution"></a>Wdrożenie rozwiązania

### <a name="get-started-quickly"></a>Szybkie rozpoczęcie pracy

Aby ułatwić uruchomienie aplikacji tak szybko, jak to możliwe, zaimplementowaliśmy system opisany w poprzedniej sekcji. Ma być wystarczająco elastyczny, aby pomieścić wiele scenariuszy, a jednocześnie jest łatwy w użyciu. Aby uzyskać dostęp do kodu, przejdź do przykładowej strony [analizy ramki wideo](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) w usłudze GitHub.

Biblioteka zawiera `FrameGrabber` klasę, która implementuje wcześniej omówiony system producent-konsument do przetwarzania klatek wideo z kamery internetowej. Użytkownicy mogą określić dokładną formę wywołania interfejsu API, a klasa używa zdarzeń, aby poinformować kod wywołujący, gdy nowa ramka zostanie nabyta lub gdy dostępny jest nowy wynik analizy.

Aby zilustrować niektóre możliwości, udostępniliśmy dwie przykładowe aplikacje korzystające z biblioteki. 

Pierwsza przykładowa aplikacja to prosta aplikacja konsoli, która pobiera ramki z domyślnej kamery internetowej, a następnie przesyła je do usługi Twarz w celu wykrycia twarzy. Uproszczona wersja aplikacji jest powielana w następującym kodzie:

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
using VideoFrameAnalyzer;

namespace BasicConsoleSample
{
    internal class Program
    {
        const string ApiKey = "<your API key>";
        const string Endpoint = "https://<your API region>.api.cognitive.microsoft.com";

        private static async Task Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up a Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face service.
                return (await faceClient.Face.DetectWithStreamAsync(frame.Image.ToMemoryStream(".jpg"))).ToArray();
            };

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.TimedOut)
                    Console.WriteLine("API call timed out.");
                else if (e.Exception != null)
                    Console.WriteLine("API call threw an exception.");
                else
                    Console.WriteLine($"New result received for frame acquired at {e.Frame.Metadata.Timestamp}. {e.Analysis.Length} faces detected");
            };

            // Tell grabber when to call the API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            await grabber.StartProcessingCameraAsync();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            await grabber.StopProcessingAsync();
        }
    }
}
```

Druga przykładowa aplikacja jest nieco bardziej interesująca. To pozwala wybrać, który interfejs API do wywołania na klatkach wideo. Po lewej stronie aplikacja wyświetla podgląd wideo na żywo. Po prawej stronie nakłada najnowszy wynik interfejsu API na odpowiednią ramkę.

W większości trybów występuje widoczne opóźnienie między transmisją wideo na żywo po lewej stronie a zwizualizowaną analizą po prawej stronie. To opóźnienie jest czas potrzebny do wywołania interfejsu API. Wyjątek jest w trybie "EmotionsWithClientFaceDetect", który wykonuje wykrywanie twarzy lokalnie na komputerze klienckim przy użyciu OpenCV przed przesłaniem żadnych obrazów do usługi Azure Cognitive Services. 

Korzystając z tego podejścia, można natychmiast wizualizować wykrytą twarz. Następnie można zaktualizować emocje później, po wywołaniu interfejsu API zwraca. Pokazuje to możliwość podejścia "hybrydowego". Oznacza to, że niektóre proste przetwarzanie można wykonać na kliencie, a następnie interfejsy API usług Cognitive Services mogą służyć do rozszerzenia tego przetwarzania o bardziej zaawansowaną analizę, gdy jest to konieczne.

![Aplikacja LiveCameraSample wyświetlająca obraz z tagami](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>Integruj przykłady z bazą kodu

Aby rozpocząć pracę z tym przykładem, wykonaj następujące czynności:

1. Pobierz z [Subskrypcji](https://azure.microsoft.com/try/cognitive-services/) klucze interfejsu API dla interfejsów API wizualizacji. W przypadku analizy klatek wideo odpowiednie usługi to:
    - [Wizja komputerowa](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Rozpoznawanie twarzy](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Klonuj [repozytorium Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub.

3. Otwórz przykład w programie Visual Studio 2015 lub nowszym, a następnie skompiluj i uruchom przykładowe aplikacje:
    - W przypadku basicconsoleSample klawisz Twarzy jest zakodowany na miejscu bezpośrednio w [pliku BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - W przypadku aplikacji LiveCameraSample wprowadź klawisze w okienku **Ustawienia** aplikacji. Klucze są zachowywane w sesjach jako dane użytkownika.

Gdy będziesz gotowy do integracji przykładów, odwołaj się do biblioteki VideoFrameAnalyzer z własnych projektów.

Funkcje rozumienia obrazu, głosu, wideo i tekstu w usłudze VideoFrameAnalyzer korzystają z usług Azure Cognitive Services. Firma Microsoft odbiera obrazy, pliki audio, wideo i inne dane przesyłane przez ciebie (za pośrednictwem tej aplikacji) i może wykorzystywać je do celów ulepszania usług. Prosimy o pomoc w ochronie prywatności osób, których dane są wysyłane do usług Azure Cognitive Services przy użyciu aplikacji.

## <a name="summary"></a>Podsumowanie

W tym artykule dowiesz się, jak uruchamiać analizę w czasie zbliżonym do rzeczywistego na transmisjach wideo na żywo przy użyciu usług Face and Computer Vision. Dowiedziałeś się również, jak możesz użyć naszego przykładowego kodu, aby rozpocząć. Aby rozpocząć tworzenie aplikacji przy użyciu bezpłatnych kluczy interfejsu API, przejdź do [strony rejestracji usług Azure Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

Zapraszam do przekazywania opinii i sugestii w [repozytorium GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/). Aby przekazać szersze opinie api, przejdź do naszej [witryny UserVoice](https://cognitive.uservoice.com/).

