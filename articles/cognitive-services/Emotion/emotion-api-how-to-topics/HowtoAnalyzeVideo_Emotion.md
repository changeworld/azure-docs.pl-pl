---
title: W czasie rzeczywistym analizy wideo przy użyciu interfejsu API rozpoznawania emocji — warstwa | Dokumentacja firmy Microsoft
description: Analiza niemal czasie rzeczywistym na ramki z strumienia wideo na żywo za pomocą interfejsu API rozpoznawania emocji — warstwa w usługach kognitywnych.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/25/2017
ms.author: anroth
ms.openlocfilehash: 3a809e729e3b697b92d9fc59351a200748bcb884
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347585"
---
# <a name="how-to-analyze-videos-in-real-time"></a>Jak analizować wideo w czasie rzeczywistym

> [!IMPORTANT]
> Interfejs API podglądu kończy się 30 października 2017 r. Testowanie nowego [wideo indeksatora interfejsu API w wersji zapoznawczej](https://azure.microsoft.com/services/cognitive-services/video-indexer/) można łatwo wyodrębnić szczegółowych informacji z wideo i celu ułatwienia pracy funkcję odnajdowania zawartości, takich jak wyniki wyszukiwania został określony poprzez wykrycie słowa rozmowy, kroje znaków i emocji. [Dowiedz się więcej](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

W tym przewodniku przedstawiono sposób przeprowadzania analizy niemal czasie rzeczywistym dla ramki z strumienia wideo na żywo. Podstawowe składniki w takich systemów to:
- Możliwość nabycia ramki ze źródła wideo
- Wybierz, które ramki do analizy
- Przesyłania tych ramek do interfejsu API
- Korzystać z każdego wyników analizy, która jest zwracana z wywołania interfejsu API

Te przykłady są napisane w języku C# i kod można znaleźć w witrynie GitHub tutaj: [ https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis ](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>Podejście
Istnieje wiele sposobów, aby rozwiązać problem uruchamiania analizy niemal czasie rzeczywistym na strumienie wideo. Firma Microsoft będzie uruchomić zwijania trzech metod w coraz większe poziomy wiedzy.

### <a name="a-simple-approach"></a>To podejście proste
Najprostszym projekt dla systemu analizy niemal czasie rzeczywistym jest nieskończoną pętlę, gdzie w każdej iteracji możemy przechwycić ramki, analizować i korzystać z wynikiem:
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
Jeśli nasze analizy obejmował lekkie algorytmu po stronie klienta, to podejście będzie odpowiedni. Jednak po naszej analiza jest przeprowadzana w chmurze, opóźnienia związanego oznacza, że wywołanie interfejsu API może zająć kilka sekund, w tym czasie nie możemy przechwytywania obrazów i naszych wątku zasadniczo robi nic. Nasze maksymalną szybkość klatek jest ograniczona przez opóźnienie wywołań interfejsu API.

### <a name="parallelizing-api-calls"></a>Parallelizing wywołań interfejsu API
Gdy proste pętli jednowątkowe sens dla lekkich algorytmu po stronie klienta, nie mieści się prawidłowo w przypadku opóźnienia związane z wywołań interfejsu API chmury. Rozwiązanie tego problemu jest umożliwienie długotrwałe wywołań interfejsu API do wykonania równolegle z Przechwytywanie ramki. W języku C# firma Microsoft można to osiągnąć za pomocą opartego na zadaniach równoległości, na przykład:
```CSharp
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
Spowoduje to uruchomienie analizy każdego w osobnym zadaniem, które można uruchomić w tle, gdy w dalszym ciągu Przechwytywanie nowej ramki. Dzięki temu można uniknąć blokuje wątku głównego podczas oczekiwania na wywołania interfejsu API wrócić, jednak firma Microsoft utracić niektóre gwarancji, że proste wersja podana — wiele wywołań interfejsu API mogą wystąpić równolegle, a wyniki może pobrać zwrócony w niewłaściwej kolejności. Może to spowodować wiele wątków jednocześnie wprowadzić funkcja ConsumeResult(), które może być niebezpieczne, jeśli funkcja nie jest bezpieczne wątkowo. Na koniec tego prostego kodu nie zachować informacje o zadań, które są tworzone, aby dyskretnie zniknie wyjątków. W związku z tym ostatnim składnika firmie Microsoft w celu dodania jest wątku "konsumentów", który będzie śledzić zadania analizy, zgłaszaj wyjątków kill długotrwałych zadań i upewnij się, że wyniki uzyskać używane we właściwej kolejności, pojedynczo.

### <a name="a-producer-consumer-design"></a>Projekt producent — konsument
W naszym systemie końcowe "producent — konsument" mamy producent wątku, który wygląda bardzo podobnie do naszej poprzedniej nieskończoną pętlę. Jednak zamiast używania wyniki analizy, jak są one dostępne, producenta po prostu umieszcza zadania do kolejki, aby śledzić je.
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
Mamy także wątku konsumenta, zużywający zadań poza kolejki oczekiwanie na zakończenie, oraz wyświetlanie wynik lub wywoływanie wyjątek, który został zgłoszony. Przy użyciu kolejki, możemy zagwarantować, wyniki uzyskać wykorzystanych pojedynczo, we właściwej kolejności, nie ograniczając maksymalną szybkość klatek systemu.
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
Na uruchomienie aplikacji zapasową tak szybko jak to możliwe, wdrożonych systemu opisane powyżej, pomyślny przebieg się wystarczająco elastyczny, aby zaimplementować wiele scenariuszy, będąc w użyciu. Aby uzyskać dostęp do kodu, przejdź do [ https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis ](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

Biblioteka zawiera klasy FrameGrabber, która implementuje systemu producent — konsument opisanych wyżej przetworzyć klatek wideo z kamery internetowej. Użytkownik może określić dokładną formę wywołania interfejsu API i klasy używa zdarzeń, aby wiedzieć, gdy nowej ramki są uzyskiwane lub jest dostępny nowy wynik analizy kodu wywołującego.

Aby zilustrować niektóre możliwości, istnieją dwie przykładowe aplikacje, które korzysta z biblioteki. Pierwsza to aplikacji konsoli simple i uproszczonej wersji to jest przedstawiony poniżej. Grabs ramek z kamery internetowej domyślne i przekazuje je do powierzchni interfejsu API wykrywania twarzy na obrazie.
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
Druga Przykładowa aplikacja jest bardziej interesujące i umożliwia określenie, które interfejs API do wywoływania w ramkach wideo. Na stronie po lewej stronie aplikacji pokazuje podgląd wideo na żywo, po prawej stronie zawiera najnowsze wyniku interfejsu API umieszczenia na odpowiednich ramki.

W większości trybów będzie widoczny opóźnienia między wideo na żywo po lewej stronie, a wizualizowanego analizy po prawej stronie. Opóźnienie to czas potrzebny do wywoływania interfejsu API. Wyjątkiem jest w trybie "EmotionsWithClientFaceDetect", który wykonuje wykrywania twarzy na obrazie lokalnie na komputerze klienckim przy użyciu OpenCV, przed przesłaniem żadnych obrazów do usług kognitywnych. Dzięki temu firma Microsoft wizualizacji wykryto kroju natychmiast, a następnie zaktualizuj emocji później po wywołaniu interfejsu API zwraca. Oznacza to możliwość podejście "hybrydowe", gdzie proste przetwarza można wykonać na kliencie, a następnie kognitywnych interfejsów API usługi można rozszerzyć to bardziej zaawansowane analizy, gdy jest to konieczne.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrowanie baza kodu
Aby zacząć korzystać z tego przykładu, wykonaj następujące kroki:

1. Pobieranie kluczy interfejsu API dla API przetwarzania obrazów z [subskrypcje](https://azure.microsoft.com/try/cognitive-services/). Analiza ramek wideo są stosowane interfejsy API:
    - [Interfejs API przetwarzania obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Emocji interfejsu API](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [Interfejs API rozpoznawania twarzy](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Klonowanie [Cognitive — przykłady-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) repozytorium GitHub

3. Otwórz w programie Visual Studio 2015 próbki, tworzenie i uruchamianie przykładowych aplikacji:
    - Dla BasicConsoleSample, klucz interfejsu API krój jest ustalony bezpośrednio w [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Dla LiveCameraSample należy wprowadzić klucze do panelu Ustawienia aplikacji. Zostaną one utrwalone między sesjami jako dane użytkownika.
        

Jeśli wszystko jest gotowe do integracji, **po prostu odwołuje się do biblioteki VideoFrameAnalyzer z własnych projektów.** 



## <a name="developer-code-of-conduct"></a>Kodeks postępowania dewelopera
Zgodnie ze wszystkimi usługami kognitywnych deweloperom tworzenie z naszych interfejsów API i przykłady są wymagane do wykonania "[Developer Kodeks postępowania dla usług Microsoft Cognitive](https://azure.microsoft.com/support/legal/developer-code-of-conduct/)." 


Obraz, głosu, wideo lub tekst zrozumienie możliwości VideoFrameAnalyzer używa kognitywnych usług firmy Microsoft. Microsoft otrzyma obrazy, audio, wideo i innych danych, możesz przekazać (za pomocą tej aplikacji) i może używać ich do celów poprawy jakości obsługi. Firma Microsoft poprosić o pomoc w ochrony osoby, dla którego dane aplikacji wysyła do usługi kognitywnych firmy Microsoft. 


## <a name="summary"></a>Podsumowanie
W tym przewodniku przedstawiono sposób uruchamiania analizy niemal czasie rzeczywistym na żywo strumieni wideo przy użyciu krój, wizji komputera i interfejsy API rozpoznawania emocji — warstwa i wykorzystania naszym przykładowym kodzie aby rozpocząć pracę.  Można rozpocząć tworzenie aplikacji z wolnego klucze interfejsu API w [stronę tworzenia konta usługi kognitywnych](https://azure.microsoft.com/try/cognitive-services/). 

Aby zapewnić opinii i sugestii w [repozytorium GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/), lub więcej szerokie opinii interfejsu API na naszych [witryny UserVoice](https://cognitive.uservoice.com/).

