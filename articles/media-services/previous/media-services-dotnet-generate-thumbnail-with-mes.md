---
title: Generowanie miniatur przy użyciu usługi Media Encoder Standard za pomocą platformy .NET
description: W tym temacie pokazano, jak kodowanie elementu zawartości oraz generowanie miniatur, w tym samym czasie za pomocą usługi Media Encoder Standard za pomocą platformy .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6bc29c098bcf7ef1d1a2e2532a00c95f0ec7e927
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61244233"
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>Generowanie miniatur przy użyciu usługi Media Encoder Standard za pomocą platformy .NET 

Umożliwia Media Encoder Standard generowanie miniatur co najmniej jeden z wejściowego pliku wideo w [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics), lub [BMP](https://en.wikipedia.org/wiki/BMP_file_format) formatów plików obrazów. Możesz przesłać zadania, które tworzą tylko obrazy lub można połączyć generowanie miniatur przy użyciu kodowania. Ten artykuł zawiera kilka przykładowych XML i JSON miniatury wstępne w przypadku takich scenariuszy. Na końcu tego artykułu jest [przykładowego kodu](#code_sample) pokazujący, jak używać zestawu SDK .NET usługi Media Services do wykonywania tego zadania kodowania.

Aby uzyskać szczegółowe informacje na temat elementów, które są używane w przykładowych ustawieniach wstępnych, należy zapoznać się z [schemat usługi Media Encoder Standard](media-services-mes-schema.md).

Upewnij się zapoznać się z [zagadnienia](media-services-dotnet-generate-thumbnail-with-mes.md#considerations) sekcji.
    
## <a name="example-of-a-single-png-file-preset"></a>Przykładowe ustawienia wstępnego "pojedynczy plik PNG"

Następujące ustawienie wstępne formatami JSON i XML można wygenerować plik PNG pojedynczego wyjścia z pierwszym kilka sekund, wideo, danych wejściowych gdzie kodera zapewniają optymalny próba wyszukiwanie "interesujący" ramki. Należy pamiętać, że wymiary obrazu dane wyjściowe zostały ustawione do 100%, co oznacza, że odpowiadają one wymiary wejściowego filmu wideo. Należy zauważyć, jak ustawienie "Format" "Wyjściowe" jest konieczne dopasowanie użycie "PngLayers" w sekcji "Kodery-dekodery". 

### <a name="json-preset"></a>Ustawienie wstępne JSON

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "PngImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        }
      ]
    }
```
    
### <a name="xml-preset"></a>Ustawienie wstępne XML

```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <PngImage Start="{Best}">
          <PngLayers>
            <PngLayer>
              <Width>100%</Width>
              <Height>100%</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Przykładowe ustawienia wstępnego "series obrazy JPEG"

Następujące ustawienie wstępne formatami JSON i XML może służyć do tworzenia zestaw 10 zdjęć na sygnatury czasowe 5% 15%,..., 95% danych wejściowych osi czasu, gdy rozmiar obrazu jest określony jako jeden kwartału z wejściowego pliku wideo.

### <a name="json-preset"></a>Ustawienie wstępne JSON

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>Ustawienie wstępne XML
    
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="5%" Step="10%" Range="96%">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Przykładowe ustawienia wstępnego "jeden obraz o określonej sygnatury czasowej"

Następujące ustawienie wstępne formatami JSON i XML może służyć do wyprodukowania jednego obrazu JPEG pozycji 30 sekund z wejściowego pliku wideo. To ustawienie wstępne oczekuje, że wejściowy plik wideo na więcej niż 30 sekund czasu trwania (inne zadanie nie powiedzie się).

### <a name="json-preset"></a>Ustawienie wstępne JSON

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "00:00:30",
          "Step": "1",
          "Range": "1",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>Ustawienie wstępne XML
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="00:00:30" Step="00:00:01" Range="00:00:01">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Przykładowe ustawienia wstępnego "miniatury w różnych rozdzielczościach"

Następujące ustawienie wstępne może służyć do generowania miniatur w różnych rozdzielczościach w jedno zadanie. W tym przykładzie w pozycji % 5 15%,..., 95 procent wejściowych osi czasu, kodera generuje dwa obrazy — jedną w 100% wejściowych rozdzielczości wideo, a druga w wysokości 50%.

Zwróć uwagę na użycie makr {rozpoznawania} w nazwie pliku; oznacza to, do kodera, szerokość i wysokość, który określono w sekcji kodowanie ustawienie wstępne podczas generowania nazwy plików obrazów w danych wyjściowych. Pomaga to również określić łatwe rozróżnienie między różnymi obrazami

### <a name="json-preset"></a>Ustawienie wstępne JSON

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        },
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "50%",
          "Height": "50%"
        }

          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
          "Format": {
        "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>Ustawienie wstępne XML
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
    <Encoding>
    <JpgImage Start="5%" Step="10%" Range="96%"><JpgImage Start="00:00:01" Step="00:00:15">
      <JpgLayers>
       <JpgLayer>
        <Width>100%</Width>
        <Height>100%</Height>
        <Quality>90</Quality>
       </JpgLayer>
       <JpgLayer>
        <Width>50%</Width>
        <Height>50%</Height>
        <Quality>90</Quality>
       </JpgLayer>
      </JpgLayers>
    </JpgImage>
    </Encoding>
    <Outputs>
      <Output FileName="{Basename}_{Resolution}_{Index}{Extension}">
        <JpgFormat/>
      </Output>
    </Outputs>
    </Preset>
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Przykład generowania miniatur podczas kodowania

Chociaż wszystkie powyższe przykłady zostały omówione jak przesyłasz zadanie kodowania, które generuje wyłącznie obrazy, można także połączyć audio/wideo, kodowania z generowanie miniatur. Poinformuj następujące ustawienie wstępne formatami JSON i XML **Media Encoder Standard** generować miniatury podczas kodowania.

### <a id="json"></a>Ustawienie wstępne JSON
Aby uzyskać informacje o schemacie, zobacz [to](https://msdn.microsoft.com/library/mt269962.aspx) artykułu.

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
    
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }
```

### <a id="xml"></a>Ustawienie wstępne XML
Aby uzyskać informacje o schemacie, zobacz [to](https://msdn.microsoft.com/library/mt269962.aspx) artykułu.

```csharp
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>100%</Width>
              <Height>100%/Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Resolution}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>   
```

## <a id="code_sample"></a>Kodowanie wideo i wygenerować miniatury przy użyciu platformy .NET

Poniższy przykład kodu używa Media Services .NET SDK do wykonywania następujących zadań:

* Utwórz zadania kodowania.
* Pobierz odwołanie do usługi Media Encoder Standard kodera.
* Załaduj ustawienia wstępnego [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) lub [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) zawierających kodowanie ustawienie wstępne, a także informacje potrzebne do generowania miniatur. Można go zapisać [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) lub [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) w pliku i użyj następujący kod do załadowania pliku.
  
        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
* Dodaj pojedynczego zadania kodowania do zadania. 
* Określ wejściowego elementu do zakodowania.
* Tworzenie zasobu danych wyjściowych, który zawiera zakodowanym elementem zawartości.
* Dodaj program obsługi zdarzeń, aby sprawdzić postęp zadania.
* Przesyłanie zadania.

Zobacz [projektowanie usługi Media Services przy użyciu platformy .NET](media-services-dotnet-how-to-use.md) artykuł, aby uzyskać wskazówki dotyczące sposobu konfigurowania środowiska deweloperskiego.

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace EncodeAndGenerateThumbnails
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
        ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
        ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
        Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the thumbnails.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Thumbnail Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("ThumbnailPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard Thumbnail task",
                    processor,
                    configuration,
                    TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                    AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:

                    // Cast sender as a job.
                    IJob job = (IJob)sender;

                    // Display or log error details as needed.
                    break;
                default:
                    break;
            }
        }

        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

## <a name="considerations"></a>Zagadnienia do rozważenia
Mają zastosowanie następujące kwestie:

* Użycie jawnego sygnatury czasowe początku/krok/zakresu przyjęto założenie, że źródło danych wejściowych jest co najmniej 1 minutę.
* Jpg/Png/BmpImage elementy mają Start kroku i zakresu z atrybutami ciągu — mogą być interpretowane jako:
  
  * Numer klatki, jeśli są one nieujemnymi liczbami całkowitymi, na przykład "Start": "120",
  * Względem czasu trwania źródła, jeśli wyrażonej w postaci nieujemnej, na przykład "Start": "15%", OR
  * Sygnatura czasowa, jeśli wyrażonej w postaci: mm: ss... Format. Na przykład "Start": "00:01:00"
    
    Możesz mieszać i dopasowywać notacji, jak należy.
    
    Ponadto Start obsługuje makro specjalne: {najlepsze}, który próbuje określić pierwszej ramki "interesujący" uwagi zawartości: (Krok i zakres są ignorowane, gdy rozpoczęcia jest ustawiona na {najlepiej})
  * Wartość domyślna: Start: {najlepsze}
* Format danych wyjściowych musi zostać podane jawnie dla każdego format obrazu: Jpg/Png/BmpFormat. Jeśli jest obecny, MES dopasowuje JpgVideo do JpgFormat i tak dalej. OutputFormat wprowadza nowe makro określonych koder-dekoder obrazu: {Index}, który musi być obecne (jeden raz i tylko jeden raz) dla formatów danych wyjściowych obrazu.

## <a name="next-steps"></a>Kolejne kroki

Możesz sprawdzić [postęp zadania](media-services-check-job-progress.md) podczas zadania kodowania jest w stanie oczekiwania.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Omówienie kodowania usługi Media Services](media-services-encode-asset.md)

