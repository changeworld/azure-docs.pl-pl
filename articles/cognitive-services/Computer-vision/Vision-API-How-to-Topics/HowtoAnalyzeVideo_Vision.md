---
title: 'Przykład: analiza wideo w czasie rzeczywistym — przetwarzanie obrazów'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak można wykonywać analizę niemalże w czasie rzeczywistym na ramkach pobieranych ze strumienia wideo na żywo za pomocą interfejsu API przetwarzania obrazów.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 25aed0f042050ebadbc6054fcbf0c68dbf782e5e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859073"
---
# <a name="how-to-analyze-videos-in-real-time"></a>Jak analizować filmy wideo w czasie rzeczywistym

W przewodniku pokazano, jak można prowadzić analizę niemalże w czasie rzeczywistym na ramkach pobieranych ze strumienia wideo na żywo. Podstawowe składniki takiego systemu to:

- Uzyskanie ramek ze źródła wideo
- Wybór ramek do analizy
- Przesłanie wybranych ramek do interfejsu API
- Wykorzystanie wyniku każdej analizy, który jest zwracany z wywołania interfejsu API

Te przykłady są napisane w języku C#, a ich kod można znaleźć w witrynie GitHub w tym miejscu: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>Podejście

Istnieje wiele sposobów podejścia do kwestii uruchamiania analizy niemalże w czasie rzeczywistym dotyczącej strumieniowych transmisji wideo. Zaczniemy od zarysowania trzech z nich, poczynając od najmniej skomplikowanego.

### <a name="a-simple-approach"></a>Podejście proste

Najprostszy projekt systemu analizy niemalże w czasie rzeczywistym to nieskończona pętla, w ramach której w każdej iteracji pobieramy ramkę, analizujemy ją, a następnie wykorzystujemy wynik:

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

Jeśli nasza analiza opiera się na prostym algorytmie po stronie klienta, to podejście może być odpowiednie. Jednak w przypadku, gdy analiza odbywa się w chmurze, związane z tym opóźnienie oznacza, że wywołanie interfejsu API może potrwać kilka sekund, w czasie których nie przechwytujemy obrazów i nasz wątek w zasadzie nic nie robi. Maksymalna szybkość ramek w naszym przypadku jest ograniczona przez opóźnienia wywołań interfejsu API.

### <a name="parallelizing-api-calls"></a>Równoległe wywołania interfejsu API

Podczas gdy prosta jednowątkowa pętla ma sens dla uproszczonego algorytmu po stronie klienta, nie jest ona odpowiednia, gdy występuje opóźnienie związane z wywołaniami interfejsu API w chmurze. Rozwiązanie tego problemu polega na dopuszczeniu długo działających wywołań interfejsu API równolegle z przechwytywaniem ramki. W języku C# możemy uzyskać ten efekt przy użyciu równoległości opartej na zadaniach, na przykład:

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

To podejście zakłada uruchomienie każdej analizy w osobnym zadaniu, które można uruchomić w tle, podczas gdy my będziemy nadal przechwytywać ramki. Pozwala ono uniknąć blokowania głównego wątku podczas oczekiwania na zakończenie wywołania interfejsu API. Jednocześnie jednak tracimy pewne gwarantowane cechy zapewniane przez prostą wersję — wiele wywołań interfejsu API może wystąpić równolegle, ale ich wyniki mogą być zwracane w nieprawidłowej kolejności. Takie podejście może także spowodować równoczesne wprowadzenie wielu wątków do funkcji ConsumeResult(), co może okazać się niebezpieczne, jeśli funkcja nie jest wątkowo bezpieczna. Wreszcie ten prosty kod nie śledzi tworzonych zadań, dlatego wyjątki nie będą zauważane. W związku z tym końcowym składnikiem, jaki musimy dodać, jest wątek „konsumenta”, który będzie śledzić zadania analizy, zgłaszać wyjątki, zabijać długo trwające zadania i gwarantować, że wyniki będą używane pojedynczo w odpowiedniej kolejności.

### <a name="a-producer-consumer-design"></a>Wzorzec producent — konsument

W naszym docelowym systemie „producent — konsument” mamy wątek producenta, który wygląda podobnie do naszej poprzedniej nieskończonej pętli. Jednak zamiast natychmiastowego wykorzystywania wyników analizy zaraz po ich udostępnieniu producent po prostu umieszcza zadania w kolejce, co pozwala na ich śledzenie.

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

Mamy również wątek konsumenta, który pobiera zadania z kolejki, czeka na ich ukończenie, po czym wyświetla wynik lub zgłasza wyjątek. Dzięki zastosowaniu kolejki możemy mieć pewność, że wyniki są wykorzystywane pojedynczo, we właściwej kolejności i bez ograniczenia maksymalnej szybkości ramek systemu.

```csharp
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

Aby jak najszybciej uzyskać gotową działającą aplikację, zaimplementowano opisany powyżej system w sposób wystarczająco elastyczny, aby implementował wiele scenariuszy, pozostając jednocześnie łatwy w użyciu. Aby uzyskać dostęp do kodu, przejdź do strony [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

Biblioteka zawiera klasę FrameGrabber, która implementuje omówiony powyżej system producent — konsument, pozwalający na przetwarzanie ramek wideo z kamery internetowej. Użytkownik może określić dokładną formę wywołania interfejsu API, a klasa korzysta ze zdarzeń, aby przekazać do wywołującego kodu informację o tym, kiedy nowa ramka została pozyskana lub dostępny jest nowy wynik analizy.

Dwie przykładowe aplikacje korzystające z tej biblioteki ilustrują niektóre z tych możliwości. Pierwsza z nich to prosta aplikacja konsolowa, której uproszczoną wersję przedstawiono poniżej. Pobiera ona ramki z domyślnej kamery internetowej i przekazuje je do interfejsu API rozpoznawania twarzy w celu wykrycia twarzy.

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

        private static void Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face API Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face API.
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

            // Tell grabber when to call API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

Druga przykładowa aplikacja jest trochę bardziej interesująca i umożliwia wybranie, który interfejs API może przyjmować ramki wideo. Po lewej stronie aplikacja przedstawia podgląd wideo na żywo, a po prawej stronie pokazuje najnowszy wynik interfejsu API nałożony na odpowiadającą mu ramkę.

W większości trybów będzie występować widoczne opóźnienie między obrazem wideo na żywo po lewej stronie a wizualizowaną analizą po prawej stronie. To opóźnienie wynika z czasu potrzebnego na realizację wywołania interfejsu API. Wyjątkiem od tej reguły jest tryb „EmotionsWithClientFaceDetect” (wykrywanie emocji na twarzy po stronie klienta), który przed przesłaniem jakichkolwiek obrazów do usług Cognitive Services przeprowadza lokalny proces wykrywania twarzy na komputerze klienckim za pomocą biblioteki OpenCV. W ten sposób natychmiast dokonujemy wizualizacji wykrytej twarzy, a następnie aktualizujemy emocje po zakończeniu wywołania interfejsu API. Ten przykład demonstruje podejście „hybrydowe”, w którym pewna prosta część przetwarzania może być wykonywana na komputerze klienckim, po czym, jeśli jest to konieczne, za pomocą interfejsów API usług Cognitive Services można rozszerzyć jej wyniki, przeprowadzając bardziej zaawansowaną analizę.

![Zrzut ekranu aplikacji LiveCameraSample przedstawiający obrazu z wyświetlanymi tagami](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrowanie z bazą kodu

Aby zacząć pracę z tym przykładem, wykonaj następujące kroki:

1. Pobierz z [Subskrypcji](https://azure.microsoft.com/try/cognitive-services/) klucze interfejsu API dla interfejsów API wizualizacji. Odpowiednimi interfejsami API do analizy ramek wideo są:
    - [Interfejs API przetwarzania obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Interfejs API rozpoznawania twarzy](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Sklonuj repozytorium GitHub [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

3. Otwórz przykład w programie Visual Studio 2015 lub nowszym, Kompiluj i uruchamiaj przykładowe aplikacje:
    - W przypadku aplikacji BasicConsoleSample klucz interfejsu API rozpoznawania twarzy jest zapisany bezpośrednio w kodzie w pliku [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - W przypadku aplikacji LiveCameraSample klucze należy wprowadzić w okienku Ustawienia aplikacji. Zostaną one utrwalone pomiędzy sesjami jako dane użytkownika.

Gdy wszystko będzie gotowe do integracji, wystarczy **po prostu odwoływać się do biblioteki VideoFrameAnalyzer z własnych projektów.**

Funkcje biblioteki VideoFrameAnalyzer umożliwiające interpretację obrazu, głosu, wideo lub tekstu korzystają z usług Azure Cognitive Services. Do firmy Microsoft są wysyłane obrazy, pliki audio i wideo oraz inne dane przekazywane za pośrednictwem tej aplikacji. Dane te mogą zostać użyte w celu udoskonalania usług. Prosimy o pomoc w ochronie prywatności osób, których dane są wysyłane do usług Azure Cognitive Services przy użyciu aplikacji.

## <a name="summary"></a>Podsumowanie

W tym przewodniku wyjaśniono, jak uruchamiać analizę niemal w czasie rzeczywistym strumieni wideo na żywo przy użyciu interfejsów API rozpoznawania i przetwarzanie obrazów oraz jak można użyć naszego przykładowego kodu, aby rozpocząć pracę. Możesz rozpocząć pracę nad aplikacją przy użyciu bezpłatnych kluczy interfejsu API na [stronie tworzenia konta usług Microsoft Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

Zachęcamy do przekazywania opinii i sugestii w [repozytorium GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) lub — w przypadku obszerniejszych informacji zwrotnych dotyczących interfejsów API — w naszej [witrynie UserVoice](https://cognitive.uservoice.com/).

