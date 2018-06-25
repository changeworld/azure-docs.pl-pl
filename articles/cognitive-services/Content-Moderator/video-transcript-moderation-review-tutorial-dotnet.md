---
title: Azure moderatora zawartości — umiarkowane wideo i zapisy rozmów w .NET | Dokumentacja firmy Microsoft
description: Jak używać moderatora zawartości na średni wideo i zapisy rozmów w .NET.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/27/2018
ms.author: sajagtap
ms.openlocfilehash: a084b50e44fe26ba2547d0f7b7ed184fb71b190c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "35347633"
---
# <a name="video-and-transcript-moderation-tutorial"></a>Samouczek łagodzenia wideo i zapis

Moderatora zawartości wideo interfejsów API umożliwiają średnie wideo i utworzyć przeglądami wideo w narzędziu człowieka przeglądu. 

Szczegółowy samouczek ułatwia zrozumienie sposobu tworzenia kompletnego rozwiązania łagodzenia wideo i wykaz łagodzenia wspierana maszyny i tworzenie przeglądu ludzkich w pętli.

Pobierz [aplikacji konsolowej C#](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) w tym samouczku. Aplikacja konsoli używa zestawu SDK i powiązanych pakietów umożliwia wykonywanie następujących zadań:

- Kompresuj wejściowego wideo dla szybsze przetwarzanie
- Średnie wideo, aby pobrać zrzuty i ramki z informacjami
- Sygnatury czasowe ramki umożliwia tworzenie miniatur (obrazy)
- Przedstawia sygnatury czasowe i miniaturę, aby utworzyć przeglądami wideo
- Konwertuj wideo mowy na tekst (zapis) przy użyciu interfejsu API indeksatora nośnika
- Średnie zapis w usłudze łagodzenia tekstu
- Dodaj moderowane zapis do przeglądu wideo

## <a name="sample-program-outputs"></a>Przykładowe dane wyjściowe z programu

Przed przejściem dalej, Oto przykładowe dane wyjściowe Oto programu:

- [Dane wyjściowe konsoli](#program-output)
- [Przejrzyj wideo](#video-review-default-view)
- [Wyświetl wykaz](#video-review-transcript-view)

## <a name="prerequisites"></a>Wymagania wstępne

1. Zarejestrować się w celu [narzędzie przeglądu zawartości moderatora](https://contentmoderator.cognitive.microsoft.com/) witryny sieci web i [tworzyć niestandardowe tagi](Review-Tool-User-Guide/tags.md) przypisującej aplikacji konsolowej C# z kodem. Następujący ekran pokazuje znaczniki niestandardowe.

  ![Znaczniki niestandardowe łagodzenia wideo](images/video-tutorial-custom-tags.png)

1. Aby uruchomić aplikację przykładową, potrzebujesz konta platformy Azure i konta usługi Azure Media Services. Ponadto należy dostęp do zawartości moderatora prywatnej wersji zapoznawczej. Na koniec należy poświadczenia uwierzytelniania usługi Azure Active Directory. Szczegółowe informacje dotyczące uzyskiwania tych informacji, zobacz [szybkiego startu wideo łagodzenia API](video-moderation-api.md).

1. Przeprowadź edycję pliku `App.config` i Dodaj nazwę dzierżawcy usługi Active Directory, punktami końcowymi usług i kluczy subskrypcji wskazywanym przez `#####`. Należy podać następujące informacje:

|Klucz|Opis|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Punkt końcowy dla interfejsu API Azure Media Services (AMS)|
|`ClientSecret`|Klucz subskrypcji dla usługi Azure Media Services|
|`ClientId`|Identyfikator klienta dla usługi Azure Media Services|
|`AzureAdTenantName`|Nazwa Active Directory dzierżawy reprezentujący organizacji|
|`ContentModeratorReviewApiSubscriptionKey`|Klucz subskrypcji dla zawartości moderatora Przejrzyj interfejsu API|
|`ContentModeratorApiEndpoint`|Punkt końcowy dla interfejsu API moderatora zawartości|
|`ContentModeratorTeamId`|Identyfikator zespołu moderatora zawartości|

## <a name="getting-started"></a>Wprowadzenie

Klasa `Program` w `Program.cs` jest główny punkt wejścia do aplikacji łagodzenia wideo.

### <a name="methods-of-class-program"></a>Metody klasy programu

|Metoda|Opis|
|-|-|
|`Main`|Analizuje wiersza polecenia, zbiera dane wejściowe użytkownika, a następnie uruchamia przetwarzanie.|
|`ProcessVideo`|Kompresuje, przekazuje łagodzi warunki i tworzy przeglądami wideo.|
|`CreateVideoStreamingRequest`|Tworzy strumień do przekazania wideo|
|`GetUserInputs`|Zbiera dane wejściowe użytkownika; używany, gdy nie są obecne żadne opcje wiersza polecenia|
|`Initialize`|Inicjuje obiektów niezbędnych do procesu łagodzenia|

### <a name="the-main-method"></a>Metody Main

`Main()` jest której rozpoczyna się wykonanie, więc jest to miejsce zacząć od zrozumienia procesu łagodzenia wideo.

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

- Ścieżka do katalogu zawierającego pliki wideo MPEG-4 poddane łagodzenia. Wszystkie `*.mp4` łagodzenia składa się pliki w tym katalogu i jego podkatalogach.
- Opcjonalnie wartość logiczna (PRAWDA/FAŁSZ) flagę wskazującą, czy powinny być generowane tekst zapisy na potrzeby moderowania audio.

Jeśli podano żadnych argumentów wiersza polecenia `Main()` wywołania `GetUserInputs()`. Ta metoda monitu wprowadź ścieżkę do jednego pliku wideo i określ, czy ma być generowany wykaz tekstu.

> [!NOTE]
> Korzysta z aplikacji konsoli [interfejsu API usługi Azure Media indeksatora](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) do generowania zapisy z przekazanego wideo ścieżki audio. Wyniki są podane w formacie WebVTT. Aby uzyskać więcej informacji dotyczących tego formatu, zobacz [Format ścieżek tekstu wideo w sieci Web](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

### <a name="initialize-and-processvideo-methods"></a>Metody inicjowania i ProcessVideo

Niezależnie od tego, czy program Opcje pochodzi z wiersza polecenia lub danych wejściowych użytkownika interaktywnego `Main()` następnego wywołania `Initialize()` można utworzyć następujące wystąpienia:

|Klasa|Opis|
|-|-|
|`AMSComponent`|Kompresuje pliki wideo przed przesłaniem ich do łagodzenia.|
|`AMSconfigurations`|Interfejs do danych konfiguracji aplikacji, w `App.config`.|
|`VideoModerator`| Przekazywanie, kodowania, szyfrowania i łagodzenia przy użyciu zestawu SDK usług AMS|
|`VideoReviewApi`|Zarządza przeglądami wideo w usłudze moderatora zawartości|

Te klasy (aside z `AMSConfigurations`, która jest prosta) są opisane bardziej szczegółowo w kolejnych sekcjach tego samouczka.

Na koniec pliki wideo są przetworzonych pojedynczo, wywołując `ProcessVideo()` dla każdego.

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


`ProcessVideo()` Metody jest bardzo prosta. Wykonuje następujące operacje w kolejności:

- Kompresuje wideo
- Przekazuje wideo do zasobu usługi Azure Media Services
- Tworzy zadanie usługi AMS na średni wideo
- Tworzy w moderatora zawartości wideo przeglądu

Poniższe sekcje należy rozważyć bardziej szczegółowo niektóre poszczególnych procesów wywołana przez `ProcessVideo()`. 

## <a name="compressing-the-video"></a>Kompresja wideo

Aby zminimalizować ruch sieciowy, aplikacja konwertuje pliki wideo H.264 (AVC MPEG-4) format i może je obsłużyć maksymalną szerokość 640 pikseli. Koder-dekoder H.264 jest zalecane z powodu jego wysokiej wydajności (szybkość kompresji). Kompresja jest wykonywane przy użyciu wolnych `ffmpeg` narzędzie wiersza polecenia, który jest dostępny w `Lib` folderu rozwiązania Visual Studio. Pliki wejściowe może być dowolnym formacie obsługiwane przez `ffmpeg`, włącznie z koderów-dekoderów i najczęściej używane formatów plików wideo.

> [!NOTE]
> Podczas uruchamiania programu, używając opcji wiersza polecenia, należy określić katalog zawierający pliki wideo do przesłania do łagodzenia. Wszystkie pliki w tym katalogu o `.mp4` są przetwarzane rozszerzenie nazwy pliku. Aby przetwarzać inne rozszerzenia nazw plików, należy zaktualizować `Main()` metoda `Program.cs` uwzględnienie żądanego rozszerzenia.

Kod, który kompresuje pojedynczy plik wideo jest `AmsComponent` klasy w `AMSComponent.cs`. Metoda odpowiedzialnych za tę funkcję jest `CompressVideo()`, pokazano poniżej.

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

- Sprawdza, upewnij się, że konfiguracja w `App.config` zawiera wszystkich niezbędnych danych
- Sprawdza, upewnij się, że `ffmpeg` binarny jest obecny
- Tworzy nazwę pliku wyjściowego, dodając `_c.mp4` do podstawowej nazwy pliku (takie jak `Example.mp4`  ->  `E>xample_c.mp4`)
- Tworzy ciąg wiersza polecenia, aby dokonać konwersji
- Uruchamia `ffmpeg` przetworzyć przy użyciu wiersza polecenia
- Czeka na przetworzenie wideo

> [!NOTE]
> Jeśli znasz filmy wideo są już kompresowane przy użyciu H.264 i mieć odpowiednie wymiary, można ponownie napisać `CompressVideo()` pomijania kompresji.

Metoda zwraca nazwę pliku wyjściowego skompresowanego pliku.

## <a name="uploading-and-moderating-the-video"></a>Przekazywanie i Moderowanie wideo

Wideo muszą być przechowywane w usłudze Azure Media Services przed mogą być przetwarzane przez usługę łagodzenia zawartości. `Program` Klasy w `Program.cs` ma metodę krótki `CreateVideoStreamingRequest()` która zwraca obiekt reprezentujący żądanie przesyłania strumieniowego, służącego do przesyłania wideo.

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

Powstałe w ten sposób `UploadVideoStreamRequest` obiektu jest zdefiniowana w `UploadVideoStreamRequest.cs` (i jego element nadrzędny, `UploadVideoRequest`w `UploadVideoRequest.cs`). Te klasy nie są wyświetlane tutaj; ich krótki i służą tylko do przechowywania skompresowanych danych wideo i informacje o nim. Inna klasa tylko dane `UploadAssetResult` (`UploadAssetResult.cs`) służy do przechowywania wyników procesu przekazywania. Teraz istnieje możliwość zrozumieć te wiersze `ProcessVideo()`:

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

Te wiersze wykonywać następujące zadania:

- Utwórz `UploadVideoStreamRequest` przekazać skompresowane wideo
- Ustaw żądania `GenerateVTT` Flaga, jeśli użytkownik zażąda wykaz tekstu
- Wywołania `CreateAzureMediaServicesJobToModerateVideo()` przekazywanie i odbieranie wynik

## <a name="deep-dive-into-video-moderation"></a>Nowości w łagodzenia wideo

Metoda `CreateAzureMediaServicesJobToModerateVideo()` w `VideoModerator.cs`, który zawiera większość kodu, który współdziała z usługi Azure Media Services. Kod źródłowy metody jest wyświetlany w następujących wyodrębniania.

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

- Tworzy zadanie AMS przetwarzania do wykonania
- Dodaje zadania kodowania plików wideo, Moderowanie go i generowania wykaz tekstu
- Przesyła zadanie przekazywania plików i początku przetwarzania
- Pobiera wyniki łagodzenia, zapis tekst (jeśli jest to wymagane) oraz inne informacje

## <a name="sample-video-moderation-response"></a>Przykładowa odpowiedź łagodzenia wideo

Wynik zadania łagodzenia wideo (zobacz [szybkiego startu wideo łagodzenia](video-moderation-api.md) to struktura danych JSON zawierający wyniki łagodzenia. Te wyniki zawierają podział fragmenty (zrzuty) w ramach wideo każdego zawierający zdarzenia (klipy) za pomocą klatek kluczowych, które zostały oznaczone do przeglądu. Każdy klatki są oceniane przez prawdopodobieństwo, że zawiera ona zawartość dla dorosłych lub luksusowych. W poniższym przykładzie przedstawiono odpowiedź w formacie JSON:

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

Przekształcenia dźwięku z wideo jest również tworzone podczas `GenerateVTT` flaga jest ustawiona.

> [!NOTE]
> Korzysta z aplikacji konsoli [interfejsu API usługi Azure Media indeksatora](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) do generowania zapisy z przekazanego wideo ścieżki audio. Wyniki są podane w formacie WebVTT. Aby uzyskać więcej informacji dotyczących tego formatu, zobacz [Format ścieżek tekstu wideo w sieci Web](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).


## <a name="creating-the-human-in-the-loop-review"></a>Tworzenie przeglądu ludzkich w pętli

Proces łagodzenia zwraca listę klatek kluczowych z wideo, wraz z jego ścieżki audio zapisu. Następnym krokiem jest utworzyć przeglądu w narzędziu przeglądu moderatora zawartości dla człowieka moderatorów. Po powrocie do `ProcessVideo()` metody w `Program.cs`, zobacz wywołanie `CreateVideoReviewInContentModerator()` metody. Ta metoda jest w `videoReviewApi` klasy, która znajduje się w `VideoReviewAPI.cs`i jest wyświetlany w tym miejscu.

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

`CreateVideoReviewInContentModerator()` wywołuje kilka innych metod do wykonywania następujących zadań:

> [!NOTE]
> Korzysta z aplikacji konsoli [FFmpeg](https://ffmpeg.org/) biblioteki generowanie miniatur. Te miniatury (obrazy) odpowiadają ramki sygnatury czasowe w [wyjście wideo łagodzenia](#sample-video-moderation-response).

|Zadanie|Metody|Plik|
|-|-|-|
|Wyodrębnij klucz ramki z wideo i tworzy obrazy miniatur z nich|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Skanowanie wykaz tekstu, jeśli jest dostępne zlokalizować dla dorosłych lub luksusowych audio|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Przygotowanie i przesyła żądanie przeglądu wideo człowieka kontroli|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

## <a name="video-review-default-view"></a>Widok domyślny wideo przeglądu

Następujący ekran pokazuje wyniki z poprzednich kroków.

![Widok domyślny wideo przeglądu](images/video-tutorial-default-view.PNG)

## <a name="transcript-generation"></a>Generowanie zapis

Do tej pory kod przedstawiony w tym samouczku ma fokus w zawartości wizualnej. Przejrzyj zawartość mowy jest oddzielny i opcjonalne proces używający, jak wspomniano, zapisu generowane na podstawie nagrania audio. Nadszedł czas teraz, aby przyjrzeć jak zapisy tekst są tworzone i używane w procesie przeglądu. Zadanie Generowanie wykaz mieści się [Azure Media indeksatora](https://docs.microsoft.com/azure/media-services/media-services-index-content) usługi.

Aplikacja wykonuje następujące zadania:

|Zadanie|Metody|Plik|
|-|-|-|
|Ustalić, czy ma być generowany zapisy tekstu|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Jeśli tak, Prześlij zadanie przekształcenia łagodzenia w ramach|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Pobierz lokalną kopię zapisu|`GenerateTranscript()`|`VideoModerator.cs`|
|Flaga ramki wideo, które zawierają niewłaściwe audio|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Dodaj wyniki do przeglądu|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Konfiguracja zadania

Wróćmy prawo do przesyłania zadania przekształcania. `CreateAzureMediaServicesJobToModerateVideo()` wywołania (opisanych) `ConfigureTranscriptTask()`.

    private void ConfigureTranscriptTask(IJob job)
    {
        string mediaProcessorName = _amsConfigurations.MediaIndexer2MediaProcessor;
        IMediaProcessor processor = _mediaContext.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

        string configuration = File.ReadAllText(_amsConfigurations.MediaIndexerConfigurationJson);
        ITask task = job.Tasks.AddNew("AudioIndexing Task", processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("AudioIndexing Output Asset", AssetCreationOptions.None);
    }

Konfiguracja zadania wykaz jest do odczytu z pliku `MediaIndexerConfig.json` w rozwiązaniu `Lib` folderu. AMS zasoby są tworzone dla pliku konfiguracji i dane wyjściowe proces przekształcania. Uruchomienie zadania AMS, to zadanie tworzy wykaz tekst z pliku wideo, audio ścieżki.

> [!NOTE]
> Przykładowa aplikacja rozpoznaje tylko mowy w US English.

### <a name="transcript-generation"></a>Generowanie zapis

Wykaz jest publikowany jako zasób AMS. Aby przeprowadzić skanowanie wykaz niepożądanych zawartości, aplikacji pobiera zasobu z usługi Azure Media Services. `CreateAzureMediaServicesJobToModerateVideo()` wywołania `GenerateTranscript()`, są wyświetlane tutaj, aby pobrać plik.

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

Po konfiguracji niezbędne AMS rzeczywiste pobierania jest wykonywane przez wywołanie metody `DownloadAssetToLocal()`, ogólny funkcję, która kopiuje zasób AMS do pliku lokalnego.

## <a name="transcript-moderation"></a>Wykaz łagodzenia

Z wykaz pod ręką jest skanowany i używane w przeglądzie. Tworzenie przeglądu jest kontrolą `CreateVideoReviewInContentModerator()`, że wywołania `GenerateTextScreenProfanity()` celu zadania. Z kolei wywołuje tę metodę `TextScreen()`, który zawiera większość funkcjonalności. 

`TextScreen()` wykonuje następujące zadania:

- Parse wykaz czas tamps i etykiety
- Przedstawia podpisów dla łagodzenia tekstu
- Flaga ramek, które mogą mieć zawartości niepożądanych mowy

Przeanalizujmy każdej te zadania bardziej szczegółowo:

### <a name="initialize-the-code"></a>Inicjowanie kodu

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
    

### <a name="parse-the-transcript-for-captions"></a>Przeanalizować wykaz podpisów

Następnie należy przeanalizować wykaz sformatowany VTT podpisów i sygnatury czasowe. Narzędzie przeglądu wyświetla te podpisy na karcie Zapis na ekranie wideo przeglądu. Sygnatury czasowe są używane do synchronizacji podpisy z odpowiedniego klatek.

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

### <a name="moderate-captions-with-the-text-moderation-service"></a>Podpisy średni z usługą łagodzenia

Następnie możemy skanowania przeanalizowany podpisy tekstem zawartości moderatora interfejsu API.

> [!NOTE]
> Klucz usługi moderatora zawartości ma żądań na drugi limit szybkości (RPS). Jeśli przekroczysz limit zestawu SDK zgłasza wyjątek z kodem błędu 429. 
>
> Klucz warstwa bezpłatna ma limit szybkości jeden RPS.

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

### <a name="breaking-down-the-text-moderation-step"></a>Niszczy krok łagodzenia tekstu

`TextScreen()` jest metodą istotne, więc warto rozbicie go.

1. Po pierwsze metoda odczytuje plik wykaz wiersz po wierszu. Ignoruje puste wiersze i wiersze zawierające `NOTE` z wynikiem zaufania. Wyodrębnia on sygnatury czasowe i elementy tekstu z *podpowiedzi* w pliku. Wskazówki reprezentuje tekst ze ścieżki audio i zawiera godziny rozpoczęcia i zakończenia. Wskazówki rozpoczyna się od wiersza sygnatury czasu z ciągiem `-->`. Następuje jeden lub więcej wierszy tekstu.

1. Wystąpienia `CaptionScreentextResult` (zdefiniowany w `TranscriptProfanity.cs`) są używane do przechowywania informacji przeanalizować z każdego wskaźnika.  Po wykryciu nowego wiersza sygnatury czasu lub zostanie osiągnięta maksymalna długość tekstu 1024 znaków, nowy `CaptionScreentextResult` jest dodawany do `csrList`. 

1. Metoda przesyła dalej każdy wskaźnik do interfejsu API łagodzenia tekstu. Wywołuje zarówno `ContentModeratorClient.TextModeration.DetectLanguageAsync()` i `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, które są zdefiniowane w `Microsoft.Azure.CognitiveServices.ContentModerator` zestawu. Aby uniknąć są ograniczone szybkość, metoda wstrzymuje sekundy przed przesłaniem każdego wskaźnika.

1. Po otrzymaniu wyników z usługi łagodzenia tekstu, metoda analizuje następnie je, aby zobaczyć, czy spełniają progi zaufania. Te wartości są ustalane w `App.config` jako `OffensiveTextThreshold`, `RacyTextThreshold`, i `AdultTextThreshold`. Na koniec postanowienia niepożądanych sami są także przechowywane. Wszystkie ramki w okresie sygnalizacji są oznaczane jako zawierające obraźliwe, luksusowych i/lub tekst dla dorosłych.

1. `TextScreen()` Zwraca `TranscriptScreenTextResult` wystąpienia, które zawiera tekst wynik łagodzenia wideo jako całość. Ten obiekt zawiera flagi i wyników dla różnych typów niepożądanej zawartości, oraz listę wszystkich niepożądanych terminów. Element wywołujący `CreateVideoReviewInContentModerator()`, wywołania `UploadScreenTextResult()` Aby dołączyć te informacje do przeglądu, tak aby jest dostępny dla osób dokonujących przeglądu człowieka.
 
## <a name="video-review-transcript-view"></a>Wyświetl wykaz wideo przeglądu

Następujący ekran pokazuje wynik wykaz generowania i łagodzenia kroków.

![Wyświetl wykaz łagodzenia wideo](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Dane wyjściowe programu

Następujące dane wyjściowe wiersza polecenia programu zawiera różne zadania, ponieważ są one wykonywane. Ponadto wynik łagodzenia (w formacie JSON) i wykaz mowy są dostępne w tym samym katalogu co oryginalnych plików wideo.

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

[Pobierz rozwiązania Visual Studio](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) przykładowe pliki i wymaganych bibliotek na potrzeby tego samouczka i rozpocząć pracę na integracją.
