---
title: Indeksowanie plików multimedialnych na pliki z indeksatora multimediów Azure
description: Indeksator usługi Azure Media umożliwia upewnij przeszukiwanie zawartości plików multimedialnych oraz do generowania zapisu pełnotekstowego dla zamkniętego podpisów i słów kluczowych. W tym temacie pokazano, jak używać nośnika indeksatora.
services: media-services
documentationcenter: ''
author: Asolanki
manager: cfowler
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/20/2017
ms.author: adsolank;juliako;johndeu
ms.openlocfilehash: 320d8cfa21c36e0f0d751d29d461a0023ab563e7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Indeksowanie plików multimedialnych na pliki z indeksatora multimediów Azure
Indeksator usługi Azure Media umożliwia upewnij przeszukiwanie zawartości plików multimedialnych oraz do generowania zapisu pełnotekstowego dla zamkniętego podpisów i słów kluczowych. Możesz przetwarzać jeden plik multimediów lub wiele plików multimediów w partii.  

> [!IMPORTANT]
> Podczas indeksowania zawartości, upewnij się użyć plików multimedialnych, które mają wyraźne mowy (bez muzyką w tle, szumu, efekty lub szumów mikrofonu). Oto kilka przykładów odpowiedniej zawartości: rejestrowane spotkań, wykładów lub prezentacji. Następująca zawartość może nie nadawać się do indeksowania: filmy, programy telewizyjne, wszystko z mieszanym audio i efekty, nieprawidłowo zarejestrowana zawartości za pomocą szumu tła (szumów).
> 
> 

Zadania indeksowania można wygenerować następujących danych wyjściowych:

* Zamknięte podpis pliki w następujących formatach: **SAMI**, **TTML**, i **WebVTT**.
  
    Pliki napisów obejmują tag o nazwie Recognizability, których wyniki zadania indeksowania zależnie od sposobu rozpoznawalnych ma mowy w źródła wideo.  Wartość Recognizability służy do plików wyjściowych ekranu dla użyteczność. Niski wynik będzie oznaczać niską indeksowania wyników z powodu jakość dźwięku.
* Plik ze słowami kluczowymi (XML).
* Audio indeksowania plików obiektów blob (AIB) do użytku z programem SQL server.
  
    Aby uzyskać więcej informacji, zobacz [przy użyciu plików AIB z indeksatora multimediów Azure i SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).

W tym artykule przedstawiono sposób tworzenia zadania indeksowania **indeksu zasobów** i **indeksu wiele plików**.

Najnowsze aktualizacje indeksatora multimediów Azure, zobacz [blogi Media Services](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Za pomocą plików konfiguracji i manifestu dla zadania indeksowania
Więcej szczegółów można określić dla zadań indeksowania przy użyciu konfiguracji zadania. Na przykład można określić, które metadanych do użycia dla pliku nośnika. Te metadane jest używana przez aparat języka aby rozwinąć jego słownictwa i znacznie zwiększa dokładność rozpoznawania mowy.  Jest również możliwość Określ pliki żądanego wyniku.

Wiele plików multimedialnych może także przetwarzać jednocześnie przy użyciu pliku manifestu.

Aby uzyskać więcej informacji, zobacz [dla usługi Azure Media indeksatora ustawień wstępnych zadań](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Indeksu zasobów
Następująca metoda przekazuje plik nośnika jako zasób i tworzy zadanie do indeksu zasobów.

Jeśli nie określono żadnych pliku konfiguracji, plik nośnika jest indeksowany z wszystkie ustawienia domyślne.

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
Domyślnie zadania indeksowania generuje następujące pliki wyjściowe. Pliki są przechowywane w pierwszym zawartości wyjściowej.

W przypadku więcej niż jeden plik wejściowy nośnika indeksatora generuje plik manifestu dla danych wyjściowych zadania, o nazwie "JobResult.txt". Dla każdego wejściowy plik multimedialny, wynikowy AIB, SAMI, TTML, WebVTT i pliki — słowo kluczowe są sekwencyjnie numerowane i o nazwie "aliasu."

| Nazwa pliku | Opis |
| --- | --- |
| **InputFileName.aib** |Plik obiektu blob indeksowania audio. <br/><br/> Plik dźwiękowy indeksowanie obiektów Blob (AIB) jest plikiem binarnym, które można przeszukiwać w Microsoft SQL server przy użyciu wyszukiwania pełnotekstowego.  Plik AIB jest bardziej wydajne niż pliki proste podpisu, ponieważ zawiera alternatywy dla każdego wyrazu, co pozwala znacznie więcej możliwości wyszukiwania. <br/> <br/>Wymaga instalacji dodatku indeksatora SQL na maszynie uruchomionych usług Microsoft SQL server 2008 lub nowszym. Wyszukiwanie AIB przy użyciu programu Microsoft SQL wyszukiwanie pełnotekstowe server zapewnia dokładniejsze wyniki wyszukiwania niż pliki napisów wygenerowane przez WAMI wyszukiwania. Jest to spowodowane AIB zawiera alternatywy słowa, które dźwiękowej podobne pliki napisów zawierają najwyższy word zaufania dla każdego segmentu dźwięku. Jeśli wyszukiwanie słów rozmowy jest upmost znaczenie, zaleca się używania AIB w połączeniu z programem Microsoft SQL Server.<br/><br/> Aby pobrać dodatek, kliknij przycisk <a href="http://aka.ms/indexersql">Azure Media indeksatora SQL dodatek</a>. <br/><br/>Istnieje również możliwość mogą korzystać z innych aparatów wyszukiwania, takich jak Apache Lucene/Solr po prostu indeksowania wideo na podstawie napisów i pliki XML — słowo kluczowe, ale spowoduje to mniej dokładne wyniki wyszukiwania. |
| **InputFileName.smi**<br/>**InputFileName.ttml**<br/>**InputFileName.vtt** |Podpis (DW) zamkniętych plikach w formatach SAMI TTML i WebVTT.<br/><br/>Ich można udostępnić osoby niepełnosprawne przesłuchanie plików audio i wideo.<br/><br/>Zamknięte pliki podpis zawierać tag o nazwie <b>Recognizability</b> który wyników zadania indeksowania, zależnie od sposobu rozpoznawalnych ma mowy w źródła wideo.  Można użyć wartości <b>Recognizability</b> do plików wyjściowych ekranu dla użyteczność. Niski wynik będzie oznaczać niską indeksowania wyników z powodu jakość dźwięku. |
| **InputFileName.kw.xml<br/>InputFileName.info** |Pliki — słowo kluczowe i informacje. <br/><br/>Plik — słowo kluczowe jest plik XML, który zawiera słowa kluczowe wyodrębnione z zawartości mowy, częstotliwości i przesunięcia informacje. <br/><br/>Plik informacji o to plik tekstowy, który zawiera szczegółowych informacji na temat każdego terminu rozpoznany. Pierwszy wiersz jest szczególna i zawiera wynik Recognizability. Każdej kolejny wiersz znajduje się lista tabulatorem następujące dane: start czasu, czas zakończenia, word/frazy, zaufania. Czasy są podane w sekundach i zaufania jest podawana jako liczbę z zakresu od 0-1. <br/><br/>Przykład wiersza: "word 1,20 wysokości 1,45 0,67" <br/><br/>Te pliki mogą używany dla wielu celów, takich jak do wykonywania analizy mowy lub ujawniony dla aparatów wyszukiwania, takie jak Bing, Google lub Microsoft SharePoint, aby pliki multimedialne mogą szybciej odnajdywać lub nawet używanych do ich dostarczania więcej odpowiednich reklam. |
| **JobResult.txt** |Dane wyjściowe manifestu, tylko wtedy, gdy indeksowanie wielu plików, zawierający następujące informacje:<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Błąd</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Jeśli nie wszystkie pliki multimedialne wejściowe są indeksowane pomyślnie, indeksowania zadanie zakończy się niepowodzeniem z kodem błędu 4000. Aby uzyskać więcej informacji, zobacz [kody błędów](#error_codes).

## <a name="index-multiple-files"></a>Indeks wielu plików
Następująca metoda przekazywania wielu plików multimedialnych jako zasobu i tworzy zadanie do indeksowania tych plików w partii.

Plik manifestu z rozszerzeniem ".lst" został utworzony i przekazywanie do niego. Plik manifestu zawiera listę wszystkich plików zasobów. Aby uzyskać więcej informacji, zobacz [dla usługi Azure Media indeksatora ustawień wstępnych zadań](https://msdn.microsoft.com/library/dn783454.aspx).

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
Jeśli nie wszystkie pliki multimedialne wejściowe są indeksowane pomyślnie, zadania indeksowania zakończy się niepowodzeniem z kodem błędu 4000. Aby uzyskać więcej informacji, zobacz [kody błędów](#error_codes).

Generowane są tego samego danych wyjściowych (jako zadania zakończyło się pomyślnie). Może się odwoływać do pliku manifestu danych wyjściowych, aby dowiedzieć się, które pliki wejściowe nie powiodło, zgodnie z wartości w kolumnie błędu. Dla plików wejściowych, które nie powiodło się, wynikowy AIB, SAMI, TTML, WebVTT i słowo kluczowe plików nie zostanie wygenerowany.

### <a id="preset"></a> Zadanie wstępne ustawienie indeksatora multimediów Azure
Przetwarzania przez indeksator multimediów Azure można dostosować, podając zadaniem opcjonalnym ustawienia wstępnego obok zadania.  Poniżej opisano format tego pliku konfiguracyjnego xml.

| Name (Nazwa) | Wymagane | Opis |
| --- | --- | --- |
| **Dane wejściowe** |false |Pliki zasobów, które mają być indeksowane.</p><p>Indeksator multimediów Azure obsługuje następujące formaty plików nośnika: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Należy określić nazwę pliku (s), w **nazwa** lub **listy** atrybutu **wejściowych** elementu (jak pokazano poniżej). Jeśli nie określisz, który plik zasobów do indeksu, jest pobierany plik podstawowy. Jeśli plik podstawowy zasobów nie jest ustawiona, pierwszy plik wejściowy zasobów jest indeksowany.</p><p>Aby jawnie określić nazwę pliku zasobów, należy wykonać:<br/>`<input name="TestFile.wmv">`<br/><br/>Można również indeksu wiele zasobów plików jednocześnie (maksymalnie 10). W tym celu:<br/><br/><ol class="ordered"><li><p>Utwórz plik tekstowy (plik manifestu) i nadaj mu rozszerzenie .lst. </p></li><li><p>Dodaj listę wszystkich nazw plików zasobów w zawartości wejściowej do tego pliku manifestu. </p></li><li><p>Dodaj plik thanifest (przekazywanie) do elementu zawartości.  </p></li><li><p>Określ nazwę pliku manifestu w atrybucie lista danych wejściowych.<br/>`<input list="input.lst">`</li></ol><br/><br/>Uwaga: Jeśli dodasz więcej niż 10 plików do pliku manifestu zadania indeksowania zakończy się niepowodzeniem z kodem błędu 2006 roku. |
| **metadata** |false |Metadane dla plików określonym zasobie używany do dostosowania słownika.  Warto przygotować indeksatora rozpoznawanie słownictwa niestandardowych słów, takich jak nazwy własne.<br/>`<metadata key="..." value="..."/>` <br/><br/>Możesz podać **wartości** dla wstępnie zdefiniowane **klucze**. Obecnie obsługiwane są następujące klucze:<br/><br/>"title" i "opis" - służącą do dostosowania słownictwa dostosować język modelu dla zadania i zwiększyć dokładność rozpoznawania mowy.  Wartości inicjatora przeszukiwania Internetu do wyszukiwania dokumentów kontekstowej tekst, aby rozszerzyć słownik wewnętrzny czas trwania zadania indeksowania przy użyciu zawartości.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **Funkcje** <br/><br/> Dodany w wersji 1.2. Obecnie jedyną obsługiwaną funkcją jest rozpoznawanie mowy ("ASR"). |false |Funkcja rozpoznawania mowy ma następujące klucze ustawień:<table><tr><th><p>Klucz</p></th>        <th><p>Opis</p></th><th><p>Przykładowa wartość</p></th></tr><tr><td><p>Język</p></td><td><p>Języka naturalnego zostać rozpoznany w pliku multimedialnego.</p></td><td><p>Angielski, hiszpański</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>Rozdzielana średnikami lista formatów podpisu żądanego wyniku (jeśli istnieje)</p></td><td><p>ttml;sami;webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Flaga wartości logicznej określenie, czy plik AIB jest wymagany (na potrzeby używania z programu SQL Server i klienta IFilter indeksatora).  Aby uzyskać więcej informacji, zobacz <a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">przy użyciu plików AIB z indeksatora multimediów Azure i SQL Server</a>.</p></td><td><p>Wartość PRAWDA; Wartość false</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Flaga wartości logicznej określenie, czy plik XML — słowo kluczowe jest wymagana.</p></td><td><p>Wartość PRAWDA; Wartość false. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Flaga wartości logicznej określenie, czy należy wymusić pełne podpisy (niezależnie od poziomu zaufania).  </p><p>Domyślna to false, w takim przypadku słów i wyrażeń, które mają mniej niż 50% ufności są wyszczególniane podpis ostateczne dane wyjściowe i zastępuje wielokropek ("...").  Wielokropek są przydatne do kontroli jakości podpisu i inspekcji.</p></td><td><p>Wartość PRAWDA; Wartość false. </p></td></tr></table> |

### <a id="error_codes"></a>Kody błędów
W przypadku błędu, zgłoś Azure Media indeksatora ponownie następujące kody błędów:

| Kod | Name (Nazwa) | Możliwe przyczyny |
| --- | --- | --- |
| 2000 |Nieprawidłowa konfiguracja |Nieprawidłowa konfiguracja |
| 2001 |Nieprawidłowy zasobów wejściowych |Brak zasobów wejściowych lub pusty zasobów. |
| 2002 |Nieprawidłowy manifest |Manifest jest pusta lub manifestu zawiera nieprawidłowe elementy. |
| 2003 |Nie można pobrać pliku multimedialnego |Nieprawidłowy adres URL w pliku manifestu. |
| 2004 |Nieobsługiwany protokół |Protokół adresu URL nośnika nie jest obsługiwany. |
| 2005 |Nieobsługiwany typ pliku |Typ pliku wejściowego nośnika nie jest obsługiwany. |
| 2006 |Zbyt wiele plików wejściowych |Istnieje więcej niż 10 plików w manifeście wejściowego. |
| 3000 |Nie można zdekodować plik nośnika |Koder-dekoder nieobsługiwany nośnik <br/>lub<br/> Uszkodzony plik <br/>lub<br/> Nie strumieniem audio na nośniku wejściowego. |
| 4000 |Indeksowanie partii częściowo powiodła się |Niektóre pliki multimedialne wejściowego nie powiodło się być indeksowany. Aby uzyskać więcej informacji, zobacz <a href="#output_files">pliki wyjściowe</a>. |
| inne |Błędy wewnętrzne |Skontaktuj się z zespołem pomocy technicznej. indexer@microsoft.com |

## <a id="supported_languages"></a>Obsługiwane języki
Obecnie są obsługiwane w językach angielskim i hiszpańskim. Aby uzyskać więcej informacji, zobacz [w wersji 1.2 blogu](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Powiązane linki
[Przegląd analiz usługi Azure Media Services](media-services-analytics-overview.md)

[Przy użyciu plików AIB z indeksatora multimediów Azure i SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Indeksowanie plików multimedialnych na pliki z podglądem indeksatora 2 multimediów Azure](media-services-process-content-with-indexer2.md)

