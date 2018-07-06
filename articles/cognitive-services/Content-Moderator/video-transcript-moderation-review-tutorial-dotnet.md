---
title: Usługa Azure Content Moderator — umiarkowane filmów wideo i zapisy na platformie .NET | Dokumentacja firmy Microsoft
description: Jak używać usługi Content Moderator należy moderować materiały wideo i transkrypcje na platformie .NET.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/27/2018
ms.author: sajagtap
ms.openlocfilehash: 0f851c030a05880d79a998ed4b4a941082c057b9
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865475"
---
# <a name="video-and-transcript-moderation-tutorial"></a>Samouczek dotyczący Moderowanie filmów wideo i transkrypcji

Pakiet Content Moderator wideo interfejsy API umożliwiają Moderowanie filmów wideo i utworzyć przeglądy wideo w narzędziu do przeglądu przez ludzi. 

To szczegółowy samouczek ułatwia zrozumienie, jak tworzyć kompletne rozwiązanie Moderowanie wideo i transkrypcji wspomagane maszynowo Moderowanie i tworzenie przeglądu człowieka w pętli.

Pobierz [aplikacji konsolowej C#](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) na potrzeby tego samouczka. Aplikacja konsoli korzysta z zestawu SDK i powiązanych pakietów, do wykonywania następujących zadań:

- Kompresowanie danych wejściowych wideo, aby przyspieszyć przetwarzanie
- Moderowanie wideo, aby pobrać zrzuty i ramki za pomocą usługi insights
- Użyj sygnatury czasowe ramki, aby utworzyć miniatury (obrazy)
- Prześlij sygnatury czasowe i miniatury, aby utworzyć przeglądy wideo
- Konwertuj wideo mowy na tekst (zapis) przy użyciu interfejsu API indeksatora multimediów
- Moderowanie transkrypcji przy użyciu usługi Moderowanie tekstu
- Dodaj moderowanych transkrypcji do wideo przeglądu

## <a name="sample-program-outputs"></a>Przykładowe dane wyjściowe z programu

Przed przejściem dalej, Przyjrzyjmy się dane wyjściowe z przykładowej follwing od programu:

- [Dane wyjściowe konsoli](#program-output)
- [Przegląd wideo](#video-review-default-view)
- [Wyświetl transkrypcji](#video-review-transcript-view)

## <a name="prerequisites"></a>Wymagania wstępne

1. Należy zasubskrybować [narzędzie do przeglądu usługi Content Moderator](https://contentmoderator.cognitive.microsoft.com/) witryny sieci web i [tworzyć niestandardowe tagi](Review-Tool-User-Guide/tags.md) przypisującej aplikacji konsolowej C# z kodem. Na poniższym ekranie przedstawiono etykiety niestandardowe.

  ![Moderowanie filmów wideo znaczniki niestandardowe](images/video-tutorial-custom-tags.png)

1. Uruchamianie przykładowej aplikacji, potrzebne jest konto platformy Azure i konto usługi Azure Media Services. Ponadto musisz mieć dostęp do prywatnej wersji zapoznawczej usługi Content Moderator. Ponadto musisz mieć poświadczenia uwierzytelniania usługi Azure Active Directory. Aby uzyskać szczegółowe informacje dotyczące uzyskiwania tych informacji, zobacz [tego przewodnika Szybki Start interfejsu API moderowania wideo](video-moderation-api.md).

1. Edytuj plik `App.config` Dodaj nazwę dzierżawcy usługi Active Directory, punkty końcowe usługi i klucze subskrypcji wskazywanym przez `#####`. Potrzebne są następujące informacje:

|Klucz|Opis|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Punkt końcowy interfejsu API usługi Azure Media Services (AMS)|
|`ClientSecret`|Klucz subskrypcji dla usługi Azure Media Services|
|`ClientId`|Identyfikator klienta usługi Azure Media Services|
|`AzureAdTenantName`|Nazwa Active Directory dzierżawy reprezentujący Twojej organizacji|
|`ContentModeratorReviewApiSubscriptionKey`|Klucz subskrypcji dla usługi Content Moderator Przegląd interfejsu API|
|`ContentModeratorApiEndpoint`|Punkt końcowy dla interfejsu API usługi Content Moderator|
|`ContentModeratorTeamId`|Identyfikator zespołu pakietu Content moderator|

## <a name="getting-started"></a>Wprowadzenie

Klasa `Program` w `Program.cs` jest główny punkt wejścia do aplikacji Moderowanie filmów wideo.

### <a name="methods-of-class-program"></a>Metody klasy programu

|Metoda|Opis|
|-|-|
|`Main`|Analizuje wiersza polecenia, a następnie zbiera dane wejściowe użytkownika i uruchamia przetwarzanie.|
|`ProcessVideo`|Kompresuje, przekazuje, łagodzi warunki i tworzy przeglądy wideo.|
|`CreateVideoStreamingRequest`|Tworzy strumień przekazywania plików wideo|
|`GetUserInputs`|Zbiera dane wejściowe użytkownika używany, gdy nie są obecne żadne opcje wiersza polecenia|
|`Initialize`|Inicjuje obiektów niezbędnych do proces moderowania|

### <a name="the-main-method"></a>Metoda główna

`Main()` to, gdzie się rozpoczyna wykonywanie więc jest to miejsce, aby rozpocząć, opis procesu Moderowanie filmów wideo.

    static void Main(string[] args)
    {
        if (args.Length == 0)
        {
            string videoPath = string.Empty;
            GetUserInputs(out videoPath);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(Path.GetDirectoryName(videoPath), "log.txt");
            try
            {
                ProcessVideo(videoPath).Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
        else
        {
            DirectoryInfo directoryInfo = new DirectoryInfo(args[0]);
            if (args.Length == 2)
                bool.TryParse(args[1], out generateVtt);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(args[0], "log.txt");
            var files = directoryInfo.GetFiles("*.mp4", SearchOption.AllDirectories);
            foreach (var file in files)
            {
                try
                {
                    ProcessVideo(file.FullName).Wait();
                }
                catch (Exception ex)
                {
                    Console.WriteLine(ex.Message);
                }
            }
        }
    }

`Main()` obsługuje następujące argumenty wiersza polecenia:

- Ścieżka do katalogu zawierającego pliki wideo MPEG-4 do przesłania do moderacji. Wszystkie `*.mp4` pliki w tym katalogu i jego podkatalogach są przesyłane do moderacji.
- Opcjonalnie (PRAWDA/FAŁSZ) flagę logiczną wskazującą, czy tekst zapisy powinny być generowane na potrzeby moderowania audio.

Jeśli nie argumenty wiersza polecenia są obecne, `Main()` wywołania `GetUserInputs()`. Ta metoda monituje użytkownika, wprowadź ścieżkę do pojedynczego pliku wideo i określić, czy powinny być generowane transkrypcji tekstu.

> [!NOTE]
> Aplikacja konsoli używa [interfejsu API usługi Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) do generowania transkrypcji z przekazanego filmu ścieżkę audio. Wyniki są podane w formacie WebVTT. Aby uzyskać więcej informacji na temat tego formatu, zobacz [Format ścieżki tekstu wideo w sieci Web](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

### <a name="initialize-and-processvideo-methods"></a>Inicjowanie i ProcessVideo metody

Niezależnie od tego, czy opcje programu pochodzi z wiersza polecenia lub danych wejściowych użytkownika interakcyjnego `Main()` następnego wywołania `Initialize()` do tworzenia następujących przypadkach:

|Klasa|Opis|
|-|-|
|`AMSComponent`|Kompresuje pliki wideo przed przesłaniem ich do moderacji.|
|`AMSconfigurations`|Interfejs do danych konfiguracji aplikacji, w `App.config`.|
|`VideoModerator`| Przekazywanie, kodowanie, szyfrowania i Moderowanie przy użyciu zestawu SDK usługi AMS|
|`VideoReviewApi`|Zarządza przeglądy wideo w usłudze Content Moderator|

Te klasy (aside z `AMSConfigurations`, który jest prostym) zostały omówione bardziej szczegółowo w kolejnych częściach tego samouczka.

Na koniec, pliki wideo są przetworzonych pojedynczo, wywołując `ProcessVideo()` dla każdego.

    private static async Task ProcessVideo(string videoPath)
    {
        Stopwatch sw = new Stopwatch();
        sw.Start();
        Console.ForegroundColor = ConsoleColor.White;
        Console.WriteLine("\nVideo compression process started...");

        var compressedVideoPath = amsComponent.CompressVideo(videoPath);
        if (string.IsNullOrWhiteSpace(compressedVideoPath))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Compression failed.");
        }

        Console.WriteLine("\nVideo compression process completed...");

        UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
        UploadAssetResult uploadResult = new UploadAssetResult();

        if (generateVtt)
        {
            uploadResult.GenerateVTT = generateVtt;
        }
        Console.WriteLine("\nVideo moderation process started...");

        if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Review process failed.");
        }

        Console.WriteLine("\nVideo moderation process completed...");
        Console.WriteLine("\nVideo review process started...");
        string reviewId = await videoReviewApi.CreateVideoReviewInContentModerator(uploadResult);
        Console.WriteLine("\nVideo review successfully completed...");
        sw.Stop();
        Console.WriteLine("\nTotal Elapsed Time: {0}", sw.Elapsed);
        using (var stw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            stw.WriteLine("Video File Name: " + Path.GetFileName(videoPath));
            stw.WriteLine($"ReviewId: {reviewId}");
            stw.WriteLine("Total Elapsed Time: {0}", sw.Elapsed);
        }
    }


`ProcessVideo()` Metoda jest dość prosta. W takiej kolejności wykonuje następujące operacje:

- Kompresuje wideo
- Przekazywanie wideo do zasobu usługi Azure Media Services
- Tworzy zadanie usługi AMS do umiarkowanego wideo
- Tworzy wideo przeglądu usługi Content Moderator

Poniższe sekcje należy wziąć pod uwagę bardziej szczegółowo niektóre poszczególnych procesów wywoływane przez `ProcessVideo()`. 

## <a name="compressing-the-video"></a>Kompresowanie wideo

Aby zminimalizować ruch sieciowy, aplikacja konwertuje pliki wideo H.264 (AVC MPEG-4) format i można je skalować do maksymalną szerokość 640 pikseli. Koder-dekoder H.264 jest zalecane z powodu jej wysokiej wydajności (stawka kompresji). Kompresja jest wykonywane przy użyciu bezpłatnych `ffmpeg` narzędzie wiersza polecenia, które są objęte `Lib` folder rozwiązania Visual Studio. Pliki wejściowe mogą być dowolnym formacie obsługiwanym przez `ffmpeg`, włącznie z najczęściej używanych formatów plików wideo i kodery-dekodery.

> [!NOTE]
> Kiedy uruchamiasz program, używając opcji wiersza polecenia, możesz określić katalog zawierający pliki wideo do przesłania do moderacji. Wszystkie pliki w tym katalogu o `.mp4` są przetwarzane przez rozszerzenie nazwy pliku. Aby przetwarzać inne rozszerzenia nazwy pliku, należy zaktualizować `Main()` method in Class metoda `Program.cs` obejmujący żądaną rozszerzenia.

Kod, który kompresuje pojedynczego pliku wideo jest `AmsComponent` klasy w `AMSComponent.cs`. Metoda odpowiedzialny za ta funkcja jest `CompressVideo()`, pokazano poniżej.

    public string CompressVideo(string videoPath)
    {
        string ffmpegBlobUrl;
        if (!ValidatePreRequisites())
        {
            Console.WriteLine("Configurations check failed. Please cross check the configurations!");
            throw new Exception();
        }

        if (File.Exists(_configObj.FfmpegExecutablePath))
        {
            ffmpegBlobUrl = this._configObj.FfmpegExecutablePath;
        }
        else
        {
            Console.WriteLine("ffmpeg.exe is missing. Please check the Lib folder");
            throw new Exception();
        }

        string videoFilePathCom = videoPath.Split('.')[0] + "_c.mp4";
        ProcessStartInfo processStartInfo = new ProcessStartInfo();
        processStartInfo.WindowStyle = ProcessWindowStyle.Hidden;
        processStartInfo.FileName = ffmpegBlobUrl;
        processStartInfo.Arguments = "-i \"" + videoPath + "\" -vcodec libx264 -n -crf 32 -preset veryfast -vf scale=640:-1 -c:a aac -aq 1 -ac 2 -threads 0 \"" + videoFilePathCom + "\"";
        var process = Process.Start(processStartInfo);
        process.WaitForExit();
        process.Close();
        return videoFilePathCom;
    }

Kod wykonuje następujące czynności:

- Kontrole, aby upewnić się, że konfiguracja w `App.config` zawierająca wszystkie niezbędne dane
- Sprawdza, czy `ffmpeg` binarnego jest obecny
- Tworzy nazwę pliku wyjściowego, dodając `_c.mp4` do podstawowej nazwy pliku (takie jak `Example.mp4`  ->  `E>xample_c.mp4`)
- Tworzy ciąg wiersza polecenia w celu wykonania konwersji
- Uruchamia `ffmpeg` przetwarzać, przy użyciu wiersza polecenia
- Czeka na film wideo do przetworzenia

> [!NOTE]
> Jeśli znasz filmy wideo są już skompresowane przy użyciu H.264 i ma odpowiednie wymiarów, można napisać ponownie `CompressVideo()` do pominięcia kompresji.

Metoda zwraca nazwę pliku wyjściowego skompresowanego pliku.

## <a name="uploading-and-moderating-the-video"></a>Przekazywanie i Moderowanie wideo

Film wideo muszą być przechowywane w usłudze Azure Media Services, zanim mogą być przetwarzane przez usługę moderowanie zawartości. `Program` Klasy w `Program.cs` jest krótki metoda `CreateVideoStreamingRequest()` , zwraca obiekt reprezentujący żądania przesyłania strumieniowego, używany do przekazywania wideo.

    private static UploadVideoStreamRequest CreateVideoStreamingRequest(string compressedVideoFilePath)
    {
        return
            new UploadVideoStreamRequest
            {
                VideoStream = File.ReadAllBytes(compressedVideoFilePath),
                VideoName = Path.GetFileName(compressedVideoFilePath),
                EncodingRequest = new EncodingRequest()
                {
                    EncodingBitrate = AmsEncoding.AdaptiveStreaming
                },
                VideoFilePath = compressedVideoFilePath
            };
    }

Wartość wynikowa `UploadVideoStreamRequest` obiekt jest zdefiniowany w `UploadVideoStreamRequest.cs` (i jego obiektu nadrzędnego, `UploadVideoRequest`w `UploadVideoRequest.cs`). Te klasy nie są wyświetlane w tym miejscu; ich krótkie i służą tylko do przechowywania skompresowane dane wideo i informacje o nim. Innej klasy jako tylko do danych `UploadAssetResult` (`UploadAssetResult.cs`) jest używany do przechowywania wyników procesu przekazywania. Teraz istnieje możliwość zrozumieć te wiersze `ProcessVideo()`:

    UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
    UploadAssetResult uploadResult = new UploadAssetResult();

    if (generateVtt)
    {
        uploadResult.GenerateVTT = generateVtt;
    }
    Console.WriteLine("\nVideo moderation process started...");

    if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
    {
        Console.ForegroundColor = ConsoleColor.Red;
        Console.WriteLine("Video Review process failed.");
    }

Te wiersze należy wykonać następujące zadania:

- Utwórz `UploadVideoStreamRequest` do przekazania skompresowane wideo
- Ustaw żądania `GenerateVTT` flagę, jeśli użytkownik zażądał transkrypcji tekstu
- Wywołania `CreateAzureMediaServicesJobToModerateVideo()` wykonywania przekazywania i uzyskania wyniku

## <a name="deep-dive-into-video-moderation"></a>Szczegółowo omówi Moderowanie filmów wideo

Metoda `CreateAzureMediaServicesJobToModerateVideo()` znajduje się w `VideoModerator.cs`, w którym znajduje się duża część kodu, który korzysta z usługi Azure Media Services. Kod źródłowy metody jest wyświetlany w następujących wyodrębniania.

    public bool CreateAzureMediaServicesJobToModerateVideo(UploadVideoStreamRequest uploadVideoRequest, UploadAssetResult uploadResult)
    {
        asset = CreateAsset(uploadVideoRequest);
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        // Encoding the asset , Moderating the asset, Generating transcript in parallel
        IAsset encodedAsset = null;
        //Creates the job for the tasks.
        IJob job = this._mediaContext.Jobs.Create("AMS Review Job");

        //Adding encoding task to job.
        ConfigureEncodeAssetTask(uploadVideoRequest.EncodingRequest, job);

        ConfigureContentModerationTask(job);

        //adding transcript task to job.
        if (uploadResult.GenerateVTT)
        {
            ConfigureTranscriptTask(job);
        }

        Stopwatch timer = new Stopwatch();
        timer.Start();
        //submit and execute job.
        job.Submit();
        job.GetExecutionProgressTask(new CancellationTokenSource().Token).Wait();
        timer.Stop();
        using (var sw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            sw.WriteLine("AMS Job Elapsed Time: {0}", timer.Elapsed);
        }

        if (job.State == JobState.Error)
        {
            throw new Exception("Video moderation has failed due to AMS Job error.");
        }

        UploadAssetResult result = uploadResult;
        encodedAsset = job.OutputMediaAssets[0];
        result.ModeratedJson = GetCmDetail(job.OutputMediaAssets[1]);
        // Check for valid Moderated JSON
        var jsonModerateObject = JsonConvert.DeserializeObject<VideoModerationResult>(result.ModeratedJson);

        if (jsonModerateObject == null)
        {
            return false;
        }
        if (uploadResult.GenerateVTT)
        {
            GenerateTranscript(job.OutputMediaAssets.Last());
        }

        uploadResult.StreamingUrlDetails = PublishAsset(encodedAsset);
        string downloadUrl = GenerateDownloadUrl(asset, uploadVideoRequest.VideoName);
        uploadResult.StreamingUrlDetails.DownloadUri = downloadUrl;
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        uploadResult.VideoFilePath = uploadVideoRequest.VideoFilePath;
        return true;
    }

Ten kod wykonuje następujące zadania:

- Tworzy zadanie usługi AMS przetwarzania do wykonania
- Dodaje zadania kodowania pliku wideo, Moderowanie go i generowanie transkrypcji tekstu
- Przesyła zadanie przekazywania plików i rozpoczęcia przetwarzania
- Pobiera wyniki moderacji, transkrypcji tekstu (jeśli jest to wymagane) oraz inne informacje

## <a name="sample-video-moderation-response"></a>Przykładowa odpowiedź Moderowanie filmów wideo

Wynik zadania Moderowanie filmów wideo (zobacz [Moderowanie filmów wideo przewodnika Szybki Start](video-moderation-api.md) jest strukturą danych JSON, zawierającą wyniki moderacji. Te wyniki zawierają podział fragmentów (zrzuty) w ramach wideo każdego zdarzenia zawierający (klipy) z użyciem klatek kluczowych, które zostały oznaczone do przeglądu. Każdej ramki kluczowe są oceniane przez prawdopodobieństwo, że zawiera on zawartość dla dorosłych lub erotycznej. Poniższy przykład przedstawia odpowiedź w formacie JSON:

    {
        "version": 2,
        "timescale": 90000,
        "offset": 0,
        "framerate": 50,
        "width": 1280,
        "height": 720,
        "totalDuration": 18696321,
        "fragments": [
        {
            "start": 0,
            "duration": 18000
        },
        {
            "start": 18000,
            "duration": 3600,
            "interval": 3600,
            "events": [
            [
            {
                "reviewRecommended": false,
                "adultScore": 0.00001,
                "racyScore": 0.03077,
                "index": 5,
                "timestamp": 18000,
                "shotIndex": 0
            }
            ]
        ]
        },
        {
            "start": 18386372,
            "duration": 119149,
            "interval": 119149,
            "events": [
            [
            {
                "reviewRecommended": true,
                "adultScore": 0.00000,
                "racyScore": 0.91902,
                "index": 5085,
                "timestamp": 18386372,
                "shotIndex": 62
            }
        ]
        ]
        }
    ]
    }

Transkrypcja dźwięku z filmu wideo jest także generowany, gdy `GenerateVTT` flaga jest ustawiona.

> [!NOTE]
> Aplikacja konsoli używa [interfejsu API usługi Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) do generowania transkrypcji z przekazanego filmu ścieżkę audio. Wyniki są podane w formacie WebVTT. Aby uzyskać więcej informacji na temat tego formatu, zobacz [Format ścieżki tekstu wideo w sieci Web](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).


## <a name="creating-the-human-in-the-loop-review"></a>Tworzenie przeglądu człowieka w pętli

Proces moderowania zwraca listę kluczowych ramek z filmu wideo, wraz z transkrypcji jego ścieżki audio. Następnym krokiem jest o utworzenie recenzji w narzędzie do przeglądu usługi Content Moderator dla moderatorów ludzi. Po powrocie do `ProcessVideo()` method in Class metoda `Program.cs`, zobacz wywołanie `CreateVideoReviewInContentModerator()` metody. Ta metoda jest w `videoReviewApi` klasy, która znajduje się w `VideoReviewAPI.cs`i jest wyświetlany w tym miejscu.

    public async Task<string> CreateVideoReviewInContentModerator(UploadAssetResult uploadAssetResult)
    {
    
        string reviewId = string.Empty;
        List<ProcessedFrameDetails> frameEntityList = framegenerator.CreateVideoFrames(uploadAssetResult);
        string path = uploadAssetResult.GenerateVTT == true ? this._amsConfig.FfmpegFramesOutputPath + Path.GetFileNameWithoutExtension (uploadAssetResult.VideoName) + "_aud_SpReco.vtt" : "";
        TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult();
        
    if (File.Exists(path))
        {
            screenTextResult = await GenerateTextScreenProfanity(reviewId, path, frameEntityList);
            uploadAssetResult.Category1TextScore = screenTextResult.Category1Score;
            uploadAssetResult.Category2TextScore = screenTextResult.Category2Score;
            uploadAssetResult.Category3TextScore = screenTextResult.Category3Score;
            uploadAssetResult.Category1TextTag = screenTextResult.Category1Tag;
            uploadAssetResult.Category2TextTag = screenTextResult.Category2Tag;
            uploadAssetResult.Category3TextTag = screenTextResult.Category3Tag;
        }
        
        var reviewVideoRequestJson = CreateReviewRequestObject(uploadAssetResult, frameEntityList);
        if (string.IsNullOrWhiteSpace(reviewVideoRequestJson))
        {
            throw new Exception("Video review process failed in CreateVideoReviewInContentModerator");
        }
        
        reviewId = JsonConvert.DeserializeObject<List<string>>(ExecuteCreateReviewApi(reviewVideoRequestJson).Result).FirstOrDefault();
        frameEntityList = framegenerator.GenerateFrameImages(frameEntityList, uploadAssetResult, reviewId);
        await CreateAndPublishReviewInContentModerator(uploadAssetResult, frameEntityList, reviewId, path, screenTextResult);
        return reviewId;
    
    }

`CreateVideoReviewInContentModerator()` wywołuje kilka metod do wykonywania następujących zadań:

> [!NOTE]
> Aplikacja konsoli używa [FFmpeg](https://ffmpeg.org/) biblioteki do generowania miniatur. Te miniatury (obrazy) odpowiadają sygnatury czasowe ramki w [Moderowanie filmów wideo w danych wyjściowych](#sample-video-moderation-response).

|Zadanie|Metody|Plik|
|-|-|-|
|Wyodrębnij klucz ramek z filmu wideo i tworzy obrazy miniatur z nich|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Skanowanie transkrypcję tekstu, jeśli to możliwe zlokalizować treści dla dorosłych lub erotycznej audio|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Przygotuj i przesyła żądanie wideo przeglądu przez ludzi kontroli|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

## <a name="video-review-default-view"></a>Widok domyślny Przegląd wideo

Na poniższym ekranie przedstawiono wyniki z poprzednich kroków.

![Widok domyślny Przegląd wideo](images/video-tutorial-default-view.PNG)

## <a name="transcript-generation"></a>Generowanie transkrypcji

Do tej pory kod przedstawiony w tym samouczku skupiła się na zawartości wizualnej. Przegląd zawartości mowy jest oddzielne i opcjonalnie proces używający, jak wspomniano wcześniej, transkrypcji, wygenerowany na podstawie audio. Warto teraz Przyjrzyj się jak tekst zapisy są tworzone i używane w procesie przeglądu. Zadanie Generowanie transkrypcji znajduje się [usługi Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content) usługi.

Aplikacja wykonuje następujące zadania:

|Zadanie|Metody|Plik|
|-|-|-|
|Ustalić, czy mają być generowane transkrypcje tekstu|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Jeśli tak, Prześlij zadanie przekształcenia jako część moderowania|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Pobierz kopię lokalną transkrypcję|`GenerateTranscript()`|`VideoModerator.cs`|
|Flaga klatki filmu wideo, które zawierają niewłaściwe audio|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Dodaj wyniki do przeglądu|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Konfiguracja zadania

Teraz można przejść bezpośrednio do przesyłania zadania transkrypcji. `CreateAzureMediaServicesJobToModerateVideo()` wywołania (opisano wcześniej) `ConfigureTranscriptTask()`.

    private void ConfigureTranscriptTask(IJob job)
    {
        string mediaProcessorName = _amsConfigurations.MediaIndexer2MediaProcessor;
        IMediaProcessor processor = _mediaContext.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

        string configuration = File.ReadAllText(_amsConfigurations.MediaIndexerConfigurationJson);
        ITask task = job.Tasks.AddNew("AudioIndexing Task", processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("AudioIndexing Output Asset", AssetCreationOptions.None);
    }

Konfigurację zadania transkrypcji zostaną odczytane z pliku `MediaIndexerConfig.json` dla rozwiązania `Lib` folderu. Zasobami usługi AMS są tworzone dla pliku konfiguracji i dane wyjściowe przetwarzania transkrypcji. Po uruchomieniu zadania AMS to zadanie tworzy transkrypcji tekst na podstawie ścieżki audio pliku wideo.

> [!NOTE]
> Przykładowa aplikacja rozpoznaje tylko mowy w US English.

### <a name="transcript-generation"></a>Generowanie transkrypcji

Transkrypcja jest publikowany jako zasób usługi AMS. Aby przeprowadzić skanowanie transkrypcji dla niepożądanej zawartości, ta aplikacja pobiera element zawartości z usługi Azure Media Services. `CreateAzureMediaServicesJobToModerateVideo()` wywołania `GenerateTranscript()`, jak pokazano tutaj, aby pobrać plik.

    public bool GenerateTranscript(IAsset asset)
    {
        try
        {
            var outputFolder = this._amsConfigurations.FfmpegFramesOutputPath;
            IAsset outputAsset = asset;
            IAccessPolicy policy = null;
            ILocator locator = null;
            policy = _mediaContext.AccessPolicies.Create("My 30 days readonly policy", TimeSpan.FromDays(360), AccessPermissions.Read);
            locator = _mediaContext.Locators.CreateLocator(LocatorType.Sas, outputAsset, policy, DateTime.UtcNow.AddMinutes(-5));
            DownloadAssetToLocal(outputAsset, outputFolder);
            locator.Delete();
            return true;
        }
        catch
        {   //TODO:  Logging
            Console.WriteLine("Exception occured while generating index for video.");
            throw;
        }
    }

Po konfiguracji usługi AMS konieczne rzeczywiste pobierania odbywa się przez wywołanie metody `DownloadAssetToLocal()`, funkcja ogólna, która skopiuje zasób usługi AMS do pliku lokalnego.

## <a name="transcript-moderation"></a>Moderowanie transkrypcji

Za pomocą transkrypcję pod ręką jest skanowany i używane w przeglądzie. Tworzenie przeglądu jest kontrolą `CreateVideoReviewInContentModerator()`, które wywołuje `GenerateTextScreenProfanity()` do wykonywania pracy. Z kolei wywołuje tę metodę `TextScreen()`, który zawiera większość funkcjonalności. 

`TextScreen()` wykonuje następujące zadania:

- Analiza transkrypcji czas tamps i podpisy
- Prześlij każdy podpis Moderowanie tekstu
- Oflaguj wszystkie ramki, które może mieć zawartości niepożądanej mowy

Przeanalizujmy każdy tych zadań, które bardziej szczegółowo:

### <a name="initialize-the-code"></a>Kod inicjowania

Najpierw należy zainicjować wszystkie zmienne i kolekcje.

    private async Task<TranscriptScreenTextResult> TextScreen(string filepath, List<ProcessedFrameDetails> frameEntityList)
    {
        List<TranscriptProfanity> profanityList = new List<TranscriptProfanity>();
        string responseContent = string.Empty;
        HttpResponseMessage response;
        bool category1Tag = false;
        bool category2Tag = false;
        bool category3Tag = false;
        double category1Score = 0;
        double category2Score = 0;
        double category3Score = 0;
        List<string> vttLines = File.ReadAllLines(filepath).Where(line => !line.Contains("NOTE Confidence:") && line.Length > 0).ToList();
        StringBuilder sb = new StringBuilder();
        List<CaptionScreentextResult> csrList = new List<CaptionScreentextResult>();
        CaptionScreentextResult captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };

        // Code from the next sections in the tutorial
    

### <a name="parse-the-transcript-for-captions"></a>Analizowanie transkrypcji dla podpisów

Następnie należy przeanalizować sformatowane transkrypcji VTT na potrzeby napisów i sygnatury czasowe. Narzędzie do przeglądu wyświetla te podpisy na karcie transkrypcji na ekran Przegląd wideo. Sygnatury czasowe są używane do synchronizacji podpisów, przy użyciu odpowiedniego klatki wideo.

        // Code from the previous section(s) in the tutorial

        //
        // Parse the transcript
        //
        foreach (var line in vttLines.Skip(1))
        {
                if (line.Contains("-->"))
                {
                    if (sb.Length > 0)
                    {
                        captionScreentextResult.Captions.Add(sb.ToString());
                        sb.Clear();
                    }
                    if (captionScreentextResult.Captions.Count > 0)
                    {
                        csrList.Add(captionScreentextResult);
                        captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };
                    }
                    string[] times = line.Split(new string[] { "-->" }, StringSplitOptions.RemoveEmptyEntries);
                    string startTimeString = times[0].Trim();
                    string endTimeString = times[1].Trim();
                    int startTime = (int)TimeSpan.ParseExact(startTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    int endTime = (int)TimeSpan.ParseExact(endTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    captionScreentextResult.StartTime = startTime;
                    captionScreentextResult.EndTime = endTime;
                }
                else
                {
                    sb.Append(line);
                }
                if (sb.Length + line.Length > 1024)
                {
                    captionScreentextResult.Captions.Add(sb.ToString());
                    sb.Clear();
                }
            }
            if (sb.Length > 0)
            {
                captionScreentextResult.Captions.Add(sb.ToString());
            }
            if (captionScreentextResult.Captions.Count > 0)
            {
                csrList.Add(captionScreentextResult);
            }

            // Code from the following section in the quickstart

### <a name="moderate-captions-with-the-text-moderation-service"></a>Umiarkowany podpisów przy użyciu usługi Moderowanie tekstu

Następnie możemy skanowanie podpisy przeanalizowanego tekstu za pomocą pakietu Content Moderator interfejs API tłumaczenia tekstu.

> [!NOTE]
> Klucz usługi Content Moderator ma żądań na drugi limit szybkości (jednostek Uzależnionych). Jeśli przekroczysz limit, zestaw SDK zgłasza wyjątek z kodem błędu 429. 
>
> Klucz w warstwie bezpłatna obowiązuje limit szybkości jeden RPS.

    //
    // Moderate the captions or cues
    //
    int waitTime = 1000;
    foreach (var csr in csrList)
    {
                bool captionAdultTextTag = false;
                bool captionRacyTextTag = false;
                bool captionOffensiveTextTag = false;
                bool retry = true;

                foreach (var caption in csr.Captions)
                {
                    while (retry)
                    {
                        try
                        {
                            System.Threading.Thread.Sleep(waitTime);
                            var lang = await CMClient.TextModeration.DetectLanguageAsync("text/plain", caption);
                            var oRes = await CMClient.TextModeration.ScreenTextWithHttpMessagesAsync(lang.DetectedLanguageProperty, "text/plain", caption, null, null, null, true);
                            response = oRes.Response;
                            responseContent = await response.Content.ReadAsStringAsync();
                            retry = false;
                        }
                        catch (Exception e)
                        {
                            if (e.Message.Contains("429"))
                            {
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                                waitTime = (int)(waitTime * 1.5);
                                Console.WriteLine($"wait time: {waitTime}");
                            }
                            else
                            {
                                retry = false;
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                            }
                        }
                    }
                    var jsonTextScreen = JsonConvert.DeserializeObject<TextScreen>(responseContent);
                    if (jsonTextScreen != null)
                    {
                        TranscriptProfanity transcriptProfanity = new TranscriptProfanity();
                        transcriptProfanity.TimeStamp = "";
                        List<Terms> transcriptTerm = new List<Terms>();
                        if (jsonTextScreen.Terms != null)
                        {
                            foreach (var term in jsonTextScreen.Terms)
                            {
                                var profanityobject = new Terms
                                {
                                    Term = term.Term,
                                    Index = term.Index
                                };
                                transcriptTerm.Add(profanityobject);
                            }
                            transcriptProfanity.Terms = transcriptTerm;
                            profanityList.Add(transcriptProfanity);
                        }
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) captionAdultTextTag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) captionRacyTextTag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) captionOffensiveTextTag = true;
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) category1Tag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) category2Tag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) category3Tag = true;
                        category1Score = jsonTextScreen.Classification.Category1.Score > category1Score ? jsonTextScreen.Classification.Category1.Score : category1Score;
                        category2Score = jsonTextScreen.Classification.Category2.Score > category2Score ? jsonTextScreen.Classification.Category2.Score : category2Score;
                        category3Score = jsonTextScreen.Classification.Category3.Score > category3Score ? jsonTextScreen.Classification.Category3.Score : category3Score;
                    }
                    foreach (var frame in frameEntityList.Where(x => x.TimeStamp >= csr.StartTime && x.TimeStamp <= csr.EndTime))
                    {
                        frame.IsAdultTextContent = captionAdultTextTag;
                        frame.IsRacyTextContent = captionRacyTextTag;
                        frame.IsOffensiveTextContent = captionOffensiveTextTag;
                    }
                }
            }
            TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult()
            {
                TranscriptProfanity = profanityList,
                Category1Tag = category1Tag,
                Category2Tag = category2Tag,
                Category3Tag = category3Tag,
                Category1Score = category1Score,
                Category2Score = category2Score,
                Category3Score = category3Score
            };
            return screenTextResult;
    }

### <a name="breaking-down-the-text-moderation-step"></a>Potężne kroku Moderowanie tekstu

`TextScreen()` jest to metoda znacznej, więc rozbijemy to.

1. Po pierwsze metoda odczytuje plik transkrypcji wiersz po wierszu. Ignoruje puste wiersze i wiersze zawierające `NOTE` z oceną zaufania. Wyodrębnienie sygnatury czasowe i elementy tekstowe z *podpowiedzi* w pliku. Wskazówki reprezentuje tekst ze ścieżki audio i zawierają godziny rozpoczęcia i zakończenia. Wskazówki zaczyna się od wiersza sygnatury czasu parametrami `-->`. Następuje jeden lub więcej wierszy tekstu.

1. Wystąpienia elementu `CaptionScreentextResult` (zdefiniowane w `TranscriptProfanity.cs`) są używane do przechowywania informacji przeanalizować z każdego wskaźnika.  Po wykryciu nowego wiersza sygnatury czasu lub zostanie osiągnięta maksymalna długość tekstu 1024 znaków, nową `CaptionScreentextResult` jest dodawany do `csrList`. 

1. Metoda przesyła obok każdego wskaźnika do interfejsu API moderowania tekstu. Wywoływanych przez nią zarówno `ContentModeratorClient.TextModeration.DetectLanguageAsync()` i `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, które są określone w `Microsoft.Azure.CognitiveServices.ContentModerator` zestawu. Aby uniknąć współczynnik limited, metoda wstrzymuje się chwilę przed przesłaniem każdego wskaźnika.

1. Po otrzymaniu wyników z usługi Moderowanie tekstu, metoda analizuje następnie je, aby zobaczyć, czy spełniają one progi zaufania. Te wartości są określane w `App.config` jako `OffensiveTextThreshold`, `RacyTextThreshold`, i `AdultTextThreshold`. Na koniec Kodeksem warunki, samodzielnie są także przechowywane informacje. Wszystkie ramki w zakresie czasu sygnalizacji są oznaczane jako zawierające obraźliwe, erotycznej i/lub tekst treści dla dorosłych.

1. `TextScreen()` Zwraca `TranscriptScreenTextResult` wystąpienia, które zawiera wynik Moderowanie tekstu z filmu wideo, jako całości. Ten obiekt zawiera flagi i ocenia dla różnych typów niepożądanej zawartości wraz z listy wszystkich Kodeksem warunków. Obiekt wywołujący, `CreateVideoReviewInContentModerator()`, wywołania `UploadScreenTextResult()` można dołączyć te informacje do przeglądu, dzięki czemu jest ona dostępna dla recenzentów, ludzi.
 
## <a name="video-review-transcript-view"></a>Wyświetl transkrypcji wideo przeglądu

Na poniższym ekranie przedstawiono wynik transkrypcję generowania i Moderowanie kroków.

![Moderowanie filmów wideo transkrypcji widoku](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Dane wyjściowe programu

Następujące dane wyjściowe wiersza polecenia programu zawiera różne zadania, ponieważ są one wykonywane. Ponadto wynik moderation (w formacie JSON) i transkrypcja mowy, są dostępne w tym samym katalogu co oryginalne pliki wideo.

    Microsoft.ContentModerator.AMSComponentClient
    Enter the fully qualified local path for Uploading the video :
    "Your File Name.MP4"
    Generate Video Transcript? [y/n] : y
    
    Video compression process started...
    Video compression process completed...
    
    Video moderation process started...
    Video moderation process completed...
    
    Video review process started...
    Video Frames Creation inprogress...
    Frames(83) created successfully.
    Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
    Video review successfully completed...
    
    Total Elapsed Time: 00:05:56.8420355


## <a name="next-steps"></a>Kolejne kroki

[Pobierz rozwiązanie programu Visual Studio](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) przykładowe pliki i bibliotek wymaganych na potrzeby tego samouczka i Rozpocznij pracę nad integracją.
