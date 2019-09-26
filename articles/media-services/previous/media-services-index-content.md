---
title: Indeksowanie plików multimedialnych za pomocą Azure Media Indexer
description: Azure Media Indexer umożliwia przeszukiwanie zawartości plików multimedialnych oraz generowanie pełnotekstowego transkrypcji napisów i słów kluczowych. W tym temacie pokazano, jak używać Media Indexer.
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
ms.openlocfilehash: d1502b4e0e024a93db41a97589231eef1ed6696f
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310167"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Indeksowanie plików multimedialnych za pomocą Azure Media Indexer

> [!NOTE]
> Procesor multimediów [Azure Media Indexer](media-services-index-content.md) zostanie wycofany 1 października 2020. [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) zastępuje ten starszy procesor nośnika. Aby uzyskać więcej informacji, zobacz [Migrowanie z Azure Media Indexer i Azure Media Indexer 2 do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).

Azure Media Indexer umożliwia przeszukiwanie zawartości plików multimedialnych oraz generowanie pełnotekstowego transkrypcji napisów i słów kluczowych. Możesz przetwarzać jeden plik multimediów lub wiele plików multimediów w partii.  

Podczas indeksowania zawartości upewnij się, że używasz plików multimedialnych z jasną mową (bez muzyki w tle, szumu, efektów lub HISS mikrofonu). Oto kilka przykładów odpowiedniej zawartości: zarejestrowane spotkania, wykłady lub prezentacje. Następująca zawartość może nie być odpowiednia do indeksowania: filmy, programy telewizyjne, wszystko z mieszaniem dźwięku i dYwiękiem, źle zarejestrowano zawartość z hałasem w tle (HISS).

Zadanie indeksowania może generować następujące dane wyjściowe:

* Pliki napisów w następujących formatach: **Sami**, **ttml**i **WebVTT**.
  
    Pliki napisów kodowanych zawierają tag o nazwie rozpoznawalność, który ocenia zadanie indeksowania w zależności od tego, jak rozpoznawalna jest mowa w źródłowym wideo.  Można użyć wartości rozpoznawalność do wygenerowania plików wyjściowych na ekranie w celu zapewnienia użyteczności. Niska ocena oznacza niską wyniki indeksowania z powodu jakości audio.
* Plik słów kluczowych (XML).
* Plik BLOB indeksowania audio (AIB) do użycia z programem SQL Server.
  
    Aby uzyskać więcej informacji, zobacz [Używanie plików AIB z Azure Media Indexer i SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).

W tym artykule przedstawiono sposób tworzenia zadań indeksowania do **indeksowania elementu zawartości** i **indeksowania wielu plików**.

Najnowsze aktualizacje Azure Media Indexer można znaleźć w sekcji [Media Services blogAs](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Korzystanie z plików konfiguracji i manifestów dla zadań indeksowania
Można określić więcej szczegółów dotyczących zadań indeksowania przy użyciu konfiguracji zadania. Można na przykład określić, które metadane mają być używane dla pliku multimedialnego. Te metadane są używane przez Aparat języka do rozwinięcia słownika i znacznie poprawiają dokładność rozpoznawania mowy.  Można również określić żądane pliki wyjściowe.

Można również przetwarzać wiele plików multimedialnych jednocześnie przy użyciu pliku manifestu.

Aby uzyskać więcej informacji, zobacz [Ustawienia wstępne zadania dla Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Indeksowanie elementu zawartości
Poniższa metoda przekazuje plik multimedialny jako element zawartości i tworzy zadanie indeksowania elementu zawartości.

Jeśli plik konfiguracji nie zostanie określony, plik multimedialny jest indeksowany ze wszystkimi ustawieniami domyślnymi.

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
### <a id="output_files"></a>Pliki wyjściowe
Domyślnie zadanie indeksowania generuje następujące pliki wyjściowe. Pliki są przechowywane w pierwszym elemencie zawartości wyjściowej.

Jeśli istnieje więcej niż jeden plik nośnika wejściowego, indeksator generuje plik manifestu dla danych wyjściowych zadania o nazwie "wynik zadania. txt". Dla każdego pliku nośnika wejściowego, wyniki AIB, SAMI, TTML, WebVTT i słowo kluczowe, są numerowane sekwencyjnie i nazwane przy użyciu "alias".

| Nazwa pliku | Opis |
| --- | --- |
| **InputFileName.aib** |Plik BLOB indeksowania audio. <br/><br/> Plik BLOB indeksowania audio (AIB) to plik binarny, który można wyszukać w programie Microsoft SQL Server przy użyciu wyszukiwania pełnotekstowego.  Plik AIB jest bardziej wydajny niż proste pliki Caption, ponieważ zawiera alternatywy dla każdego wyrazu, co pozwala na znacznie bogatsze środowisko wyszukiwania. <br/> <br/>Wymaga instalacji dodatku indeksatora SQL na maszynie z uruchomionym programem Microsoft SQL Server 2008 lub nowszym. Wyszukiwanie AIB przy użyciu wyszukiwania pełnotekstowego w programie Microsoft SQL Server zapewnia dokładniejsze wyniki wyszukiwania niż wyszukiwanie plików napisów wygenerowanych przez WAMI. Wynika to z faktu, że AIB zawiera alternatywy wyrazów, które wyglądają podobnie jak w przypadku plików napisów o największej pewności dla każdego segmentu dźwięku. Jeśli wyszukiwanie wyrazów mówionych jest największe, zaleca się użycie AIB w połączeniu z Microsoft SQL Server.<br/><br/> Aby pobrać dodatek, kliknij <a href="https://aka.ms/indexersql">Azure Media Indexer dodatek SQL</a>. <br/><br/>Istnieje również możliwość użycia innych aparatów wyszukiwania, takich jak Apache Lucene/Solr, do zwykłego indeksowania filmu wideo opartego na zamkniętym podpisie i plikach XML słów kluczowych, ale spowoduje to mniej dokładne wyniki wyszukiwania. |
| **InputFileName.smi**<br/>**InputFileName.ttml**<br/>**InputFileName.vtt** |Pliki napisów (DW) w formatach SAMI, TTML i WebVTT.<br/><br/>Mogą one służyć do udostępniania plików audio i wideo osobom niepełnosprawnym.<br/><br/>Pliki napisów kodowanych zawierają tag o nazwie <b>rozpoznawalny</b> , który ocenia zadanie indeksowania w zależności od tego, jak rozpoznawalna jest mowa w źródłowym wideo.  Można użyć wartości <b>rozpoznawalność</b> do wygenerowania plików wyjściowych na ekranie w celu zapewnienia użyteczności. Niska ocena oznacza niską wyniki indeksowania z powodu jakości audio. |
| **InputFileName.kw.xml<br/>InputFileName.info** |Pliki słów kluczowych i informacji. <br/><br/>Plik słów kluczowych to plik XML, który zawiera słowa kluczowe wyodrębnione z zawartości mowy i informacje o częstotliwości i przesunięciu. <br/><br/>Plik info to zwykły plik tekstowy, który zawiera szczegółowe informacje dotyczące każdego rozpoznanego terminu. Pierwszy wiersz jest specjalny i zawiera wynik rozpoznania. Każdy kolejny wiersz jest rozdzielaną tabulatorami listą następujących danych: czas rozpoczęcia, czas zakończenia, słowo/fraza, pewność. Czasy są wyrażone w sekundach, a poziom pewności jest podawany jako liczba z przedziału od 0-1. <br/><br/>Przykładowy wiersz: "1,20 1,45 Word 0,67" <br/><br/>Te pliki mogą być używane w wielu celach, na przykład w celu przeprowadzenia analizy mowy lub udostępnienia aparatów wyszukiwania, takich jak Bing, Google lub Microsoft SharePoint, aby pliki multimedialne mogły być bardziej wykrywalne lub nawet wykorzystane do dostarczenia bardziej przydatnych reklam. |
| **JobResult.txt** |Manifest wyjściowy, obecny tylko podczas indeksowania wielu plików, zawierający następujące informacje:<br/><br/><table border="1"><tr><th>Plik_wejściowy</th><th>Alias</th><th>MediaLength</th><th>Błąd</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c. mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Jeśli nie wszystkie pliki multimediów wejściowych są indeksowane pomyślnie, zadanie indeksowania kończy się niepowodzeniem z kodem błędu 4000. Aby uzyskać więcej informacji, zobacz [kody błędów](#error_codes).

## <a name="index-multiple-files"></a>Indeksowanie wielu plików
Poniższa metoda przekazuje wiele plików multimedialnych jako element zawartości i tworzy zadanie indeksowania wszystkich plików w partii.

Plik manifestu z rozszerzeniem ". lst" jest tworzony i przekazywany do elementu zawartości. Plik manifestu zawiera listę wszystkich plików zasobów. Aby uzyskać więcej informacji, zobacz [Ustawienia wstępne zadania dla Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

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

### <a name="partially-succeeded-job"></a>Zadanie częściowo zakończone powodzeniem
Jeśli nie wszystkie pliki multimediów wejściowych są indeksowane pomyślnie, zadanie indeksowania zakończy się niepowodzeniem z kodem błędu 4000. Aby uzyskać więcej informacji, zobacz [kody błędów](#error_codes).

Generowane są te same dane wyjściowe (zgodnie z zakończonymi pomyślnie zadaniami). Można odwołać się do pliku manifestu wyjściowego, aby dowiedzieć się, które pliki wejściowe nie powiodły się, zgodnie z wartościami kolumny błędu. W przypadku plików wejściowych, które nie powiodły się, powstałe pliki AIB, SAMI, TTML, WebVTT i Keywords nie będą generowane.

### <a id="preset"></a>Ustawienia wstępne zadania dla Azure Media Indexer
Przetwarzanie z Azure Media Indexer może być dostosowane przez udostępnienie opcjonalnego ustawienia wstępnego zadania obok zadania.  Poniżej opisano Format tego pliku XML konfiguracji.

| Name | Wymagane | Opis |
| --- | --- | --- |
| **klawiatur** |false |Pliki zasobów, które mają być indeksowane.</p><p>Azure Media Indexer obsługuje następujące formaty plików multimedialnych: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Można określić nazwy plików w atrybucie **nazwy** lub **listy** elementu wejściowego (jak pokazano poniżej) . Jeśli nie określisz pliku zasobów do indeksowania, plik podstawowy jest wybierany. Jeśli nie ustawiono podstawowego pliku zasobów, pierwszy plik w wejściowym elemencie zawartości jest indeksowany.</p><p>Aby jawnie określić nazwę pliku zasobu, wykonaj następujące czynności:<br/>`<input name="TestFile.wmv">`<br/><br/>Można również indeksować wiele plików zasobów jednocześnie (do 10 plików). W tym celu:<br/><br/><ol class="ordered"><li><p>Utwórz plik tekstowy (plik manifestu) i nadaj mu rozszerzenie. lst. </p></li><li><p>Dodaj listę wszystkich nazw plików zasobów w danych wejściowych do tego pliku manifestu. </p></li><li><p>Dodaj (Przekaż) plik manifestu do elementu zawartości.  </p></li><li><p>Określ nazwę pliku manifestu w atrybucie listy danych wejściowych.<br/>`<input list="input.lst">`</li></ol><br/><br/>Uwaga: Jeśli dodasz więcej niż 10 plików do pliku manifestu, zadanie indeksowania zakończy się niepowodzeniem z kodem błędu 2006. |
| **metadata** |false |Metadane dla określonych plików zasobów używanych na potrzeby adaptacji słownika.  Przydatne do przygotowania indeksatora do rozpoznawania niestandardowych słów słownika, takich jak poprawne rzeczowniki.<br/>`<metadata key="..." value="..."/>` <br/><br/>Można podać **wartości** wstępnie zdefiniowanych **kluczy**. Obecnie obsługiwane są następujące klucze:<br/><br/>"title" i "Description" — służy do adaptacji słownictwa w celu dostosowania modelu języka dla zadania i poprawiania dokładności rozpoznawania mowy.  Wartości odnoszą się do wyszukiwania w Internecie, aby znaleźć kontekstowe dokumenty tekstowe, przy użyciu zawartości, aby rozszerzyć wewnętrzny słownik na czas trwania zadania indeksowania.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **oferowanych** <br/><br/> Dodano w wersji 1,2. Obecnie jedyną obsługiwaną funkcją jest rozpoznawanie mowy ("ASR"). |false |Funkcja rozpoznawania mowy ma następujące klucze ustawień:<table><tr><th><p>Klucz</p></th>        <th><p>Opis</p></th><th><p>Przykładowa wartość</p></th></tr><tr><td><p>Język</p></td><td><p>Język naturalny, który ma zostać rozpoznany w pliku multimedialnym.</p></td><td><p>Angielski, hiszpański</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>rozdzielana średnikami lista formatów żądanych napisów wyjściowych (jeśli istnieją)</p></td><td><p>ttml;sami;webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Flaga logiczna określająca, czy plik AIB jest wymagany (do użycia z SQL Server i IFilter klienta indeksatora).  Aby uzyskać więcej informacji, zobacz <a href="https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">Używanie plików AIB z Azure Media Indexer i SQL Server</a>.</p></td><td><p>Oznacza False</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Flaga logiczna określająca, czy plik XML jest wymagany.</p></td><td><p>Oznacza False. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Flaga logiczna określająca, czy wymuszać pełne podpisy (niezależnie od poziomu ufności).  </p><p>Wartość domyślna to false. w takim przypadku wyrazy i frazy, które mają mniej niż 50% poziomu ufności, są pomijane na podstawie końcowych etykiet wyjściowych i zastępowane przez wielokropek ("...").  Elipsy są przydatne do kontroli jakości napisów i inspekcji.</p></td><td><p>Oznacza False. </p></td></tr></table> |

### <a id="error_codes"></a>Kody błędów
W przypadku błędu, Azure Media Indexer powinien zgłosić jeden z następujących kodów błędów:

| Kod | Name | Możliwe przyczyny |
| --- | --- | --- |
| 2000 |Nieprawidłowa konfiguracja |Nieprawidłowa konfiguracja |
| 2001 |Nieprawidłowe zasoby wejściowe |Brak zawartości wejściowej lub pustego elementu zawartości. |
| 2002 |Nieprawidłowy manifest |Manifest jest pusty lub manifest zawiera nieprawidłowe elementy. |
| 2003 |Nie można pobrać pliku multimedialnego |Nieprawidłowy adres URL w pliku manifestu. |
| 2004 |Nieobsługiwany protokół |Protokół URL protokołu nie jest obsługiwany. |
| 2005 |Nieobsługiwany typ pliku |Typ pliku nośnika danych wejściowych nie jest obsługiwany. |
| 2006 |Zbyt wiele plików wejściowych |Manifest wejściowy zawiera więcej niż 10 plików. |
| 3000 |Dekodowanie pliku nośnika nie powiodło się |Nieobsługiwany koder-dekoder multimediów <br/>lub<br/> Uszkodzony plik multimedialny <br/>lub<br/> Brak strumienia audio w nośniku wejściowym. |
| 4000 |Indeksowanie wsadowe częściowo powiodło się |Nie można indeksować niektórych plików multimediów wejściowych. Aby uzyskać więcej informacji, zobacz <a href="#output_files">pliki wyjściowe</a>. |
| inne |Błędy wewnętrzne |Skontaktuj się z zespołem pomocy technicznej. [https://doi.org/10.13012/J8PN93H8](indexer@microsoft.com) |

## <a id="supported_languages"></a>Obsługiwane języki
Obecnie obsługiwane są języki w języku angielskim i hiszpańskim. Aby uzyskać więcej informacji, zobacz [wpis w blogu dotyczący wersji v 1.2](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Linki pokrewne
[Omówienie Azure Media Services Analytics](media-services-analytics-overview.md)

[Korzystanie z plików AIB z Azure Media Indexer i SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Indeksowanie plików multimedialnych z Azure Media Indexer 2 wersja zapoznawcza](media-services-process-content-with-indexer2.md)

