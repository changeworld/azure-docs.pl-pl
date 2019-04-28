---
title: Indeksowaniu plików multimedialnych za pomocą usługi Azure Media Indexer
description: Usługa Azure Media Indexer umożliwia Dodaj zawartości multimediów jako możliwej do multimedialnej możliwość wyszukiwania oraz generowanie pełnotekstowej transkrypcji na podpisy kodowane oraz słowa kluczowe. W tym temacie pokazano, jak używać Media Indexer.
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
ms.date: 03/18/2019
ms.author: adsolank;juliako;johndeu
ms.openlocfilehash: a11ae0414d6737f1588515ec19524bcf499f0c74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61215809"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Indeksowaniu plików multimedialnych za pomocą usługi Azure Media Indexer
Usługa Azure Media Indexer umożliwia Dodaj zawartości multimediów jako możliwej do multimedialnej możliwość wyszukiwania oraz generowanie pełnotekstowej transkrypcji na podpisy kodowane oraz słowa kluczowe. Możesz przetwarzać jeden plik multimediów lub wiele plików multimediów w partii.  

> [!IMPORTANT]
> Podczas indeksowania zawartości, upewnij się, że wykorzystywaniu plików multimedialnych, które mają wyraźne mowy (bez muzyki w tle, hałasu, efekty i szumów mikrofon). Niektóre przykłady odpowiedniej zawartości to: rejestrowane spotkań, kursach i prezentacji. Następująca zawartość może nie być odpowiednie dla indeksowania: filmy, programy telewizyjne, wszystko za pomocą mieszanego audio i efekty dźwiękowe źle nagranej zawartości przy użyciu hałas w tle (szumów).
> 
> 

Zadania można generować następujące dane wyjściowe:

* Plików z napisami w następujących formatach: **LAPOŃSKI**, **TTML**, i **WebVTT**.
  
    Plików z napisami include — tag o nazwie Recognizability, których wyniki zadania na podstawie informacji o jak rozpoznać mowy w źródłowy plik wideo.  Wartość Recognizability służy do plików wyjściowych ekranu dla użyteczności. Niskim wynikiem będzie oznaczać niską indeksowania wyniki ze względu na jakość dźwięku.
* Plik słów kluczowych (XML).
* Dźwięk indeksowanie obiektów blob, plik (AIB) do użytku z programem SQL server.
  
    Aby uzyskać więcej informacji, zobacz [przy użyciu plików AIB, za pomocą usługi Azure Media Indexer i programu SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).

W tym artykule przedstawiono sposób tworzenia zadania indeksowania do **indeksu zasobów** i **indeksu w wielu plikach**.

Aby uzyskać najnowsze aktualizacje usługi Azure Media Indexer, zobacz [blogi dotyczące usługi Media Services](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Za pomocą plików konfiguracji i manifestu dla zadania indeksowania
Więcej szczegółów można określić dla indeksowania zadań podrzędnych przy użyciu konfiguracji zadania. Na przykład można określić które metadanych do użycia dla pliku multimedialnego. Te metadane używane przez aparat języka, aby rozwinąć jego słownictwa i znacznie zwiększa dokładność rozpoznawania mowy.  Możesz się również określić pliki żądaną produktu wyjściowego.

Wiele plików multimediów może także przetwarzać tylko raz, korzystając z pliku manifestu.

Aby uzyskać więcej informacji, zobacz [zadanie ustawienie wstępne dla usługi Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Indeks elementu zawartości
Następującą metodę przekazuje plik multimedialny jako zasobu i tworzy zadanie do indeksowania elementu zawartości.

Jeśli plik konfiguracji nie zostanie określony, plik multimedialny jest indeksowana przy użyciu wszystkich ustawień domyślnych.

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
Domyślnie zadania generuje następujące pliki wyjściowe. Pliki są przechowywane w pierwszym elementu zawartości wyjściowej.

W przypadku więcej niż jeden plik wejściowy media indeksator generuje plik manifestu dla danych wyjściowych zadania, o nazwie "JobResult.txt". Dla każdego wejściowy plik multimedialny, wynikowy AIB, SAMI, TTML, WebVTT i pliki — słowo kluczowe są sekwencyjnie ponumerowane i o nazwie "aliasu."

| Nazwa pliku | Opis |
| --- | --- |
| **InputFileName.aib** |Plik blob indeksowanie audio. <br/><br/> Plik dźwiękowy indeksowanie obiektów Blob (AIB) jest plikiem binarnym, które mogą być wyszukiwane w programie Microsoft SQL server przy użyciu wyszukiwania pełnotekstowego.  Plik AIB jest bardziej wydajne niż pliki proste podpisu, ponieważ zawiera alternatywy dla każdego wyrazu, co znacznie bardziej zaawansowane środowisko wyszukiwania. <br/> <br/>Wymaga instalacji dodatku SQL indeksatora na komputerze uruchomionych usług Microsoft SQL server 2008 lub nowszym. Wyszukiwanie AIB przy użyciu programu Microsoft SQL wyszukiwanie pełnotekstowe server zapewnia bardziej precyzyjne wyniki wyszukiwania niż wyszukiwanie plików z napisami generowane przez WAMI. Jest to spowodowane AIB zawiera słowa zastępcze, które brzmią podobnie, plików z napisami zawierają słowo najwyższy zaufania dla każdego segmentu audio. Jeśli wyszukiwanie wypowiadanych słów jest upmost znaczenie, zaleca się używać AIB w połączeniu z programem Microsoft SQL Server.<br/><br/> Aby pobrać dodatek, kliknij przycisk <a href="https://aka.ms/indexersql">Azure Media Indexer SQL dodatek</a>. <br/><br/>Jest również możliwe korzystanie z innych aparatów wyszukiwania, takie jak Apache Lucene/Solr po prostu indeksować wideo na podstawie napisów i pliki XML — słowo kluczowe, ale spowoduje to mniej dokładne wyniki wyszukiwania. |
| **InputFileName.smi**<br/>**InputFileName.ttml**<br/>**InputFileName.vtt** |Zamknięte pliki transkrypcji (DW) w formacie SAMI TTML i WebVTT.<br/><br/>Mogą one używane, aby udostępnić pliki audio i wideo dla osób z wadami słuchu niepełnosprawności.<br/><br/>Zamkniętych plikach podpis zawierać tag o nazwie <b>Recognizability</b> który ocenia indeksowania zadania, w oparciu o sposób rozpoznawalnych mowy w źródłowy plik wideo.  Można użyć wartości <b>Recognizability</b> do plików wyjściowych ekranu dla użyteczności. Niskim wynikiem będzie oznaczać niską indeksowania wyniki ze względu na jakość dźwięku. |
| **InputFileName.kw.xml<br/>InputFileName.info** |Pliki — słowo kluczowe i informacji. <br/><br/>Plik — słowo kluczowe jest plik XML, który zawiera słowa kluczowe wyodrębnione z zawartości mówionej o częstotliwości i przesunięciu informacji. <br/><br/>Informacje o pliku jest plik zwykły tekst, który zawiera szczegółowe informacje o każdym okresie został rozpoznany. Pierwszy wiersz jest szczególna i zawiera wynik Recognizability. Każdy kolejny wiersz jest listę rozdzielanych tabulatorami następujące dane: start czasu, czas zakończenia, słowo lub dana fraza, zaufanie. Czas, w którym znajdują się w ciągu kilku sekund i zaufania jest podawana jako liczbę z zakresu od 0-1. <br/><br/>Przykład wiersz: "wysokości 1.20 1,45 word 0,67" <br/><br/>Te pliki można używane dla liczby celów, takich jak do przeprowadzenia analizy mowy, lub udostępniane dla aparatów wyszukiwania, takie jak Bing, Google lub Microsoft SharePoint, aby pliki multimedialne mogą szybciej odnajdywać, lub nawet używane do dostarczania istotniejsze reklam. |
| **JobResult.txt** |Manifeście danych wyjściowych tylko wtedy, gdy indeksowanie wielu plików, zawierający następujące informacje:<br/><br/><table border="1"><tr><th>Wejściowy</th><th>Alias</th><th>MediaLength</th><th>Błąd</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Jeśli nie wszystkie multimedialnych plików wejściowych są indeksowane pomyślnie, indeksowania zadanie zakończy się niepowodzeniem z kodem błędu 4000. Aby uzyskać więcej informacji, zobacz [kody błędów](#error_codes).

## <a name="index-multiple-files"></a>Indeks wielu plików
Następującą metodę przesyła wiele plików multimediów jako zasobu i tworzy zadanie, aby indeksować te pliki w zadaniu wsadowym.

Plik manifestu z rozszerzeniem ".lst" jest utworzony i przekazywania do niego. Plik manifestu zawiera listę wszystkich plików zasobów. Aby uzyskać więcej informacji, zobacz [zadanie ustawienie wstępne dla usługi Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

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

### <a name="partially-succeeded-job"></a>Częściowo udanej zadania
Jeśli nie wszystkie multimedialnych plików wejściowych są indeksowane pomyślnie, indeksowania zadanie zakończy się niepowodzeniem z kodem błędu 4000. Aby uzyskać więcej informacji, zobacz [kody błędów](#error_codes).

Tych samych danych wyjściowych (jako zadania zakończyło się powodzeniem) są generowane. Mogą odwoływać się do pliku manifestu danych wyjściowych, aby dowiedzieć się, które pliki wejściowe są nie kończy się zgodnie z wartości w kolumnach błędu. Dla plików wejściowych, które nie powiodło się, wynikowy AIB, SAMI, TTML, WebVTT i słowo kluczowe pliki nie będą generowane.

### <a id="preset"></a> Ustawienie zadań wstępne dla usługi Azure Media Indexer
Przetwarzanie z usługi Azure Media Indexer można dostosować, podając ustawienie wstępne zadania wraz z zadaniem opcjonalnym.  Poniżej opisano, w formacie xml tej konfiguracji.

| Name (Nazwa) | Wymagane | Opis |
| --- | --- | --- |
| **Dane wejściowe** |false |Pliki zasobów, które mają być indeksowane.</p><p>Usługa Azure Media Indexer obsługuje następujące formaty plików multimediów: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Można określić nazwę pliku (s) w **nazwa** lub **listy** atrybutu **wejściowych** — element (jak pokazano poniżej). Jeśli nie określisz plik zasobu, który indeks, jest pobierany plik podstawowy. Jeśli nie podstawowego pliku zasobu jest ustawiona, pierwszego pliku wejściowego elementu jest indeksowana.</p><p>Aby jawnie określić nazwę pliku zasobów, należy wykonać:<br/>`<input name="TestFile.wmv">`<br/><br/>Można również indeks wiele zasobów pliki jednocześnie (maksymalnie 10). W tym celu:<br/><br/><ol class="ordered"><li><p>Utwórz plik tekstowy (plik manifestu) i nadaj mu rozszerzenie .lst. </p></li><li><p>Dodaj listę wszystkich nazw plików zasobów w danych wejściowych elementów zawartości do tego pliku manifestu. </p></li><li><p>Dodaj plik (przekazywanie) manifestu do zasobu.  </p></li><li><p>Określ nazwę pliku manifestu w atrybucie listy danych wejściowych.<br/>`<input list="input.lst">`</li></ol><br/><br/>Uwaga: Jeśli dodasz więcej niż 10 plików do pliku manifestu indeksowania zadanie zakończy się niepowodzeniem z kodem błędu 2006. |
| **metadata** |false |Metadane dla plików określonym zasobie używane na potrzeby dostosowania słownictwa.  Warto przygotować indeksator do rozpoznawania niestandardowych słownictwa słów, takich jak nazwy własne.<br/>`<metadata key="..." value="..."/>` <br/><br/>Możesz podać **wartości** dla wstępnie zdefiniowane **klucze**. Obecnie obsługiwane są następujące klucze:<br/><br/>"title" i "description" - używane na potrzeby dostosowania słownictwa celu dostosowanie język modelu dla zadania i zwiększenia dokładności rozpoznawania mowy.  Wartości zapełnić internetowych wyszukiwania można znaleźć dokumentów kontekstowe odpowiedni tekst, za pomocą zawartość, rozszerzyć słownik wewnętrzny dla czasu trwania zadania indeksowania.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **Funkcje** <br/><br/> Dodane w wersji 1.2. Obecnie jedyną obsługiwaną funkcją jest rozpoznawanie mowy ("ASR"). |false |Funkcja rozpoznawania mowy ma następujące klucze ustawień:<table><tr><th><p>Klucz</p></th>        <th><p>Opis</p></th><th><p>Przykładowa wartość</p></th></tr><tr><td><p>Język</p></td><td><p>Język naturalny rozpoznawany w pliku multimedialnego.</p></td><td><p>Angielski, hiszpański</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>rozdzieloną średnikami listę formatów podpisu żądaną produktu wyjściowego (jeśli istnieje)</p></td><td><p>ttml;sami;webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Flaga wartości logicznej, określając, czy plik AIB jest wymagana (do użytku z programem SQL Server i klienta IFilter indeksatora).  Aby uzyskać więcej informacji, zobacz <a href="https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">przy użyciu plików AIB, za pomocą usługi Azure Media Indexer i programu SQL Server</a>.</p></td><td><p>True; False</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Flaga wartości logicznej określenie, czy plik XML — słowo kluczowe jest wymagana.</p></td><td><p>True; False. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Flaga wartości logicznej, określając, czy chce wymusić pełne podpisy (niezależnie od tego, poziom ufności).  </p><p>Domyślna to false, w którym to przypadku słów i fraz, które mają mniej niż 50% ufności są pomijane w danych wyjściowych podpisem ostatecznego i zastąpione przez wielokropek ("...").  Wielokropek są przydatne do kontroli jakości podpisu i inspekcji.</p></td><td><p>True; False. </p></td></tr></table> |

### <a id="error_codes"></a>Kody błędów
W przypadku błąd powinien wysyłać raporty usługi Azure Media Indexer ponownie następujące kody błędów:

| Kod | Name (Nazwa) | Możliwe przyczyny |
| --- | --- | --- |
| 2000 |Nieprawidłowa konfiguracja |Nieprawidłowa konfiguracja |
| 2001 |Nieprawidłowe zasoby danych wejściowych |Brak danych wejściowych zasobów lub zasobu puste. |
| 2002 |Nieprawidłowy manifest |Manifest jest pusta lub manifest zawiera nieprawidłowe elementy. |
| 2003 |Nie można pobrać plik multimedialny |Nieprawidłowy adres URL w pliku manifestu. |
| 2004 |Nieobsługiwany protokół |Protokół Adres URL multimediów nie jest obsługiwana. |
| 2005 |Nieobsługiwany typ pliku |Typ pliku wejściowego nośnika nie jest obsługiwany. |
| 2006 |Zbyt wiele plików wejściowych |W manifeście danych wejściowych jest większa niż 10 plików. |
| 3000 |Nie można zdekodować pliku multimedialnego |Nieobsługiwana kodera <br/>lub<br/> Uszkodzony plik <br/>lub<br/> Nie strumienia audio w wejściowych nośnika. |
| 4000 |Indeksowanie Batch częściowo powiodło się. |Niektóre z plików wejściowych multimediów są nie mają być indeksowane. Aby uzyskać więcej informacji, zobacz <a href="#output_files">pliki wyjściowe</a>. |
| inne |Błędy wewnętrzne |Skontaktuj się z zespołem pomocy technicznej. indexer@microsoft.com |

## <a id="supported_languages"></a>Obsługiwane języki
Obecnie są obsługiwane w językach angielskim i hiszpańskim. Aby uzyskać więcej informacji, zobacz [wpis w blogu wersji 1.2](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Powiązane linki
[Przegląd Analityki usługi Azure Media Services](media-services-analytics-overview.md)

[Przy użyciu plików AIB za pomocą usługi Azure Media Indexer i programu SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Indeksowanie plików multimedialnych za pomocą usługi Azure Media Indexer 2 w wersji zapoznawczej](media-services-process-content-with-indexer2.md)

