---
title: Indeksowanie plików multimediów za pomocą programu Azure Media Indexer
description: Usługa Azure Media Indexer umożliwia wyszukiwanie zawartości plików multimedialnych i generowanie transkrypcji pełnotekstowej dla podpisów kodowych i słów kluczowych. W tym temacie pokazano, jak używać indeksatora multimediów.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/22/2019
ms.author: juliako
ms.reviewer: johndeu
ms.openlocfilehash: 7ccc2d5956b44a8cd85f19e0905539c32f58bc5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164002"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Indeksowanie plików multimediów za pomocą programu Azure Media Indexer

> [!NOTE]
> Procesor multimediów **indeksu mediów azure** zostanie wycofany. Aby uzyskać daty wycofania, zobacz ten temat [starszych składników.](legacy-components.md) [Indeksator wideo usługi Azure Media Services](https://docs.microsoft.com/azure/media-services/video-indexer/) zastępuje ten starszy procesor multimediów. Aby uzyskać więcej informacji, zobacz [Migrowanie z indeksatora multimediów azure i indeksatora multimediów azure 2 do indeksatora wideo usługi Azure Media Services.](migrate-indexer-v1-v2.md)

Usługa Azure Media Indexer umożliwia wyszukiwanie zawartości plików multimedialnych i generowanie transkrypcji pełnotekstowej dla podpisów kodowych i słów kluczowych. W partii można przetworzyć jeden plik multimedialny lub wiele plików multimedialnych.  

Podczas indeksowania zawartości należy używać plików multimedialnych, które mają wyraźną mowę (bez muzyki w tle, szumów, efektów lub syc mikrofonu). Oto kilka przykładów odpowiedniej zawartości: nagrane spotkania, wykłady lub prezentacje. Następująca zawartość może nie być odpowiednia do indeksowania: filmy, programy telewizyjne, wszystko z mieszanym dźwiękiem i efektami dźwiękowymi, źle nagrana zawartość z szumem tła (syk).

Zadanie indeksowania może generować następujące dane wyjściowe:

* Pliki podpisów kodowane w następujących formatach: **TTML**i **WebVTT**.
  
    Pliki z podpisami kodowymi zawierają znacznik o nazwie Rozpoznawalność, który ocenia zadanie indeksowania na podstawie rozpoznawalności mowy w źródłowym filmie wideo.  Można użyć wartości rozpoznawalność do ekranu plików wyjściowych dla użyteczności. Niski wynik oznaczałby słabe wyniki indeksowania ze względu na jakość dźwięku.
* Plik słów kluczowych (XML).

W tym artykule pokazano, jak utworzyć zadania indeksowania do **indeksowania zasobu** i **indeksowania wielu plików**.

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Używanie plików konfiguracyjnych i manifestów do indeksowania zadań
Można określić więcej szczegółów dla zadań indeksowania przy użyciu konfiguracji zadania. Na przykład można określić, które metadane mają być używane dla pliku multimedialnego. Te metadane są używane przez aparat języka, aby rozwinąć jego słownictwa i znacznie poprawia dokładność rozpoznawania mowy.  Można również określić żądane pliki wyjściowe.

Można również przetwarzać wiele plików multimedialnych jednocześnie za pomocą pliku manifestu.

Aby uzyskać więcej informacji, zobacz [Predefiniowane ustawienia zadań dla indeksatora multimediów platformy Azure](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Indeksować środek trwały
Poniższa metoda przekazuje plik multimedialny jako zasób i tworzy zadanie indeksowania zasobu.

Jeśli nie określono pliku konfiguracyjnego, plik multimedialny jest indeksowany ze wszystkimi ustawieniami domyślnymi.

```csharp
    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }  
```

<!-- __ -->
### <a name="output-files"></a><a id="output_files"></a>Pliki wyjściowe
Domyślnie zadanie indeksowania generuje następujące pliki wyjściowe. Pliki są przechowywane w pierwszym zasób wyjściowy.

Gdy istnieje więcej niż jeden plik nośnika wejściowego, indeksator generuje plik manifestu dla wyjść zadania o nazwie "JobResult.txt". Dla każdego pliku nośnika wejściowego wynikowe pliki TTML, WebVTT i słowa kluczowe są kolejno numerowane i nazwane przy użyciu "Alias".

| Nazwa pliku | Opis |
| --- | --- |
| **InputFileName.ttml**<br/>**InputFileName.vtt** |Pliki podpisów kodowych (CC) w formatach TTML i WebVTT.<br/><br/>Mogą być używane do udostępnienia plików audio i wideo osobom z niepełnosprawnością słuchu.<br/><br/>Pliki podpisów kodowane zawierają znacznik o nazwie <b>Rozpoznawalność,</b> który ocenia zadanie indeksowania na podstawie rozpoznawalności mowy w źródłowym filmie wideo.  Można użyć wartości <b>rozpoznawalność</b> do ekranu plików wyjściowych dla użyteczności. Niski wynik oznaczałby słabe wyniki indeksowania ze względu na jakość dźwięku. |
| **InputFileName.kw.xml<br/>InputFileName.info** |Pliki słów kluczowych i informacji. <br/><br/>Plik słowa kluczowego to plik XML zawierający słowa kluczowe wyodrębnione z zawartości mowy, z informacjami o częstotliwości i przesunięciu. <br/><br/>Plik informacji jest plikiem w postaci zwykłego tekstu, który zawiera szczegółowe informacje o każdym rozpoznanym terminie. Pierwsza linia jest szczególna i zawiera wynik rozpoznawalności. Każdy kolejny wiersz jest oddzieloną kartą listą następujących danych: czas rozpoczęcia, czas zakończenia, słowo/fraza, pewność siebie. Czasy podane są w sekundach, a pewność siebie jest podana jako liczba od 0-1. <br/><br/>Przykładowa linia: "1,20 1,45 słowa 0,67" <br/><br/>Pliki te mogą być używane do wielu celów, takich jak, do przeprowadzania analizy mowy lub narażone na wyszukiwarki, takie jak Bing, Google lub Microsoft SharePoint, aby pliki multimedialne były bardziej wykrywalne, a nawet używane do wyświetlania trafniejszych reklam. |
| **JobResult.txt** |Manifest wyjściowy, obecny tylko podczas indeksowania wielu plików, zawierający następujące informacje:<br/><br/><table border="1"><tr><th>Plik wejściowy</th><th>Alias</th><th>MediaLength (właso)</th><th>Błąd</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Jeśli nie wszystkie wejściowe pliki multimedialne są indeksowane pomyślnie, zadanie indeksowania kończy się niepowodzeniem z kodem błędu 4000. Aby uzyskać więcej informacji, zobacz [Kody błędów](#error_codes).

## <a name="index-multiple-files"></a>Indeksowanie wielu plików
Poniższa metoda przekazuje wiele plików multimedialnych jako zasób i tworzy zadanie indeksowania wszystkich tych plików w partii.

Tworzony jest plik manifestu z rozszerzeniem ".lst" i przesyłany do zasobu. Plik manifestu zawiera listę wszystkich plików zasobów. Aby uzyskać więcej informacji, zobacz [Predefiniowane ustawienia zadań dla indeksatora multimediów platformy Azure](https://msdn.microsoft.com/library/dn783454.aspx).

```csharp
    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }
```

### <a name="partially-succeeded-job"></a>Zadanie częściowo powiodło się
Jeśli nie wszystkie wejściowe pliki multimedialne są indeksowane pomyślnie, zadanie indeksowania zakończy się niepowodzeniem z kodem błędu 4000. Aby uzyskać więcej informacji, zobacz [Kody błędów](#error_codes).

Generowane są te same dane wyjściowe (jako zadania, które powiodły się). Można odwołać się do pliku manifestu wyjściowego, aby dowiedzieć się, które pliki wejściowe nie powiodły się, zgodnie z wartościami kolumny Błąd. W przypadku plików wejściowych, które nie powiodły się, wynikowe pliki TTML, WebVTT i słowa kluczoweego NIE zostaną wygenerowane.

### <a name="task-preset-for-azure-media-indexer"></a><a id="preset"></a>Predefiniowane ustawienia zadań dla indeksatora multimediów platformy Azure
Przetwarzanie z usługi Azure Media Indexer można dostosować, zapewniając opcjonalne zadanie ustawione wraz z zadaniem.  Poniżej opisano format tej konfiguracji xml.

| Nazwa | Wymagane | Opis |
| --- | --- | --- |
| **Wejście** |false |Pliki zasobów, które chcesz indeksować.</p><p>Usługa Azure Media Indexer obsługuje następujące formaty plików multimedialnych: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Nazwę pliku można określić w **atrybucie nazwa** lub **lista** elementu **wejściowego** (jak pokazano poniżej). Jeśli nie określisz, który plik zasobu ma być indeksowane, plik podstawowy jest pobierany. Jeśli nie ustawiono żadnego pliku zasobu podstawowego, pierwszy plik w zasobie wejściowym jest indeksowany.</p><p>Aby jawnie określić nazwę pliku zasobu, wykonaj następujące polecenie:<br/>`<input name="TestFile.wmv">`<br/><br/>Można również indeksować wiele plików zasobów jednocześnie (maksymalnie 10 plików). W tym celu:<br/><br/><ol class="ordered"><li><p>Utwórz plik tekstowy (plik manifestu) i nadaj mu rozszerzenie lst. </p></li><li><p>Dodaj listę wszystkich nazw plików zasobów w zasobie wejściowym do tego pliku manifestu. </p></li><li><p>Dodaj (przekaż) plik manifestu do zasobu.  </p></li><li><p>Określ nazwę pliku manifestu w atrybucie listy danych wejściowych.<br/>`<input list="input.lst">`</li></ol><br/><br/>Uwaga: Jeśli dodasz więcej niż 10 plików do pliku manifestu, zadanie indeksowania zakończy się niepowodzeniem z kodem błędu 2006. |
| **Metadanych** |false |Metadane dla określonych plików zasobów używanych do adaptacji słownictwa.  Przydatne do przygotowania indeksatora do rozpoznawania niestandardowych słów słownictwa, takich jak rzeczowniki.<br/>`<metadata key="..." value="..."/>` <br/><br/>Można podać **wartości** wstępnie zdefiniowanych **kluczy**. Obecnie obsługiwane są następujące klucze:<br/><br/>"title" i "description" - używane do adaptacji słownictwa, aby dostosować model języka do swojej pracy i poprawić dokładność rozpoznawania mowy.  Wartości seed Wyszukiwania internetowe, aby znaleźć kontekstowo istotne dokumenty tekstowe, przy użyciu zawartości, aby rozszerzyć słownika wewnętrznego na czas trwania zadania indeksowania.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **Funkcje** <br/><br/> Dodano w wersji 1.2. Obecnie jedyną obsługiwaną funkcją jest rozpoznawanie mowy ("ASR"). |false |Funkcja rozpoznawania mowy ma następujące klawisze ustawień:<table><tr><th><p>Klucz</p></th>        <th><p>Opis</p></th><th><p>Przykładowa wartość</p></th></tr><tr><td><p>Język</p></td><td><p>Język naturalny, który ma być rozpoznawany w pliku multimedialnym.</p></td><td><p>Angielski, hiszpański</p></td></tr><tr><td><p>PodpisFormaty</p></td><td><p>oddzielona średnikami lista żądanych formatów podpisów wyjściowych (jeśli istnieje)</p></td><td><p>ttml;webvtt</p></td></tr><tr><td><p></p></td><td><p> </p></td><td><p>To prawda; False</p></td></tr><tr><td><p>Generowanie słów</p></td><td><p>Flaga logiczna określająca, czy plik XML słowa kluczowego jest wymagany.</p></td><td><p>To prawda; False. </p></td></tr><tr><td><p>SiłaFullCaption</p></td><td><p>Flaga logiczna określająca, czy mają być wymuszane pełne podpisy (niezależnie od poziomu ufności).  </p><p>Wartość domyślna jest false, w którym to przypadku wyrazy i frazy, które mają mniej niż 50% poziomu ufności są pomijane w końcowych wyjściach podpisu i zastępowane elipsami ("...").  Elipsy są przydatne do kontroli jakości podpisów i inspekcji.</p></td><td><p>To prawda; False. </p></td></tr></table> |

### <a name="error-codes"></a><a id="error_codes"></a>Kody błędów
W przypadku błędu indeksator multimediów platformy Azure powinien zgłosić jeden z następujących kodów błędów:

| Code | Nazwa | Możliwe przyczyny |
| --- | --- | --- |
| 2000 |Nieprawidłowa konfiguracja |Nieprawidłowa konfiguracja |
| 2001 |Nieprawidłowe zasoby wejściowe |Brak zasobów wejściowych lub pustego zasobu. |
| 2002 |Nieprawidłowy manifest |Manifest jest pusty lub manifest zawiera nieprawidłowe elementy. |
| 2003 |Nie można pobrać pliku multimedialnego |Nieprawidłowy adres URL w pliku manifestu. |
| 2004 |Nieobsługiwał protokołu |Protokół adresu URL nośnika nie jest obsługiwany. |
| 2005 |Nieobsługiwany typ pliku |Typ pliku nośnika wejściowego nie jest obsługiwany. |
| 2006 |Zbyt wiele plików wejściowych |Istnieje więcej niż 10 plików w manifeście wejściowym. |
| 3000 |Nie można zdekodować pliku multimedialnego |Nieobsługiwał kodek nośników <br/>lub<br/> Uszkodzony plik multimedialny <br/>lub<br/> Brak strumienia audio na nośnikach wejściowych. |
| 4000 |Indeksowanie wsadowe częściowo zakończyło się pomyślnie |Niektóre wejściowe pliki multimedialne nie mogą być indeksowane. Aby uzyskać więcej informacji, zobacz <a href="#output_files">Pliki wyjściowe</a>. |
| inne |Błędy wewnętrzne |Skontaktuj się z zespołem pomocy technicznej. indexer@microsoft.com |

## <a name="supported-languages"></a><a id="supported_languages"></a>Obsługiwane języki
Obecnie obsługiwane są języki angielski i hiszpański.  

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Powiązane linki
[Omówienie usługi Azure Media Services Analytics](media-services-analytics-overview.md)

[Indeksowanie plików multimedialnych za pomocą programu Azure Media Indexer 2 Preview](media-services-process-content-with-indexer2.md)

