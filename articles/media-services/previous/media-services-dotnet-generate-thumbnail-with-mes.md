---
title: Generowanie miniatur przy użyciu usługi Media Encoder Standard za pomocą platformy .NET
description: W tym temacie pokazano, jak używać platformy .NET do kodowania zasobu i generowania miniatur jednocześnie przy użyciu standardu media encoder.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61244233"
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>Generowanie miniatur przy użyciu usługi Media Encoder Standard za pomocą platformy .NET 

Za pomocą programu Media Encoder Standard można wygenerować jedną lub więcej miniatur z wejściowego wideo w formatach plików obrazów [JPEG,](https://en.wikipedia.org/wiki/JPEG) [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics)lub [BMP.](https://en.wikipedia.org/wiki/BMP_file_format) Można przesyłać zadania, które generują tylko obrazy, lub można łączyć generowanie miniatur z kodowaniem. Ten artykuł zawiera kilka przykładowych ustawień miniatur XML i JSON dla takich scenariuszy. Na końcu artykułu znajduje się [przykładowy kod,](#code_sample) który pokazuje, jak używać mediastor .NET SDK do wykonania zadania kodowania.

Aby uzyskać więcej informacji na temat elementów, które są używane w przykładowych ustawień predefiniowanych, należy przejrzeć [schemat Standardowy koderów multimediów](media-services-mes-schema.md).

Zapoznaj się z sekcją [Zagadnienia.](media-services-dotnet-generate-thumbnail-with-mes.md#considerations)
    
## <a name="example-of-a-single-png-file-preset"></a>Przykład predefiniowanego ustawienia "pojedynczego pliku PNG"

Następujące ustawienia predefiniowane JSON i XML mogą być używane do tworzenia pojedynczego wyjściowego pliku PNG z pierwszych kilku sekund wejściowego wideo, w którym koder podejmuje próbę znalezienia "interesującej" klatki. Należy zauważyć, że wymiary obrazu wyjściowego zostały ustawione na 100%, co oznacza, że odpowiadają one wymiarom wejściowego wideo. Należy również zauważyć, jak ustawienie "Format" w "Wyjścia" jest wymagane, aby dopasować użycie "PngLayers" w sekcji "Kodeki". 

### <a name="json-preset"></a>Ustawienia wstępne JSON

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
    
### <a name="xml-preset"></a>Ustawienia predefiniowane XML

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

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Przykład predefiniowanych ustawień "seria obrazów JPEG"

Następujące ustawienia predefiniowane JSON i XML mogą być używane do tworzenia zestawu 10 obrazów przy znacznikach czasu 5%, 15%, ..., 95% wejściowej osi czasu, gdzie rozmiar obrazu jest określony jako jedna czwarta wejściowego wideo.

### <a name="json-preset"></a>Ustawienia wstępne JSON

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

### <a name="xml-preset"></a>Ustawienia predefiniowane XML
    
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

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Przykład predefiniowanego ustawienia "jeden obraz przy określonym sygnaturze czasowej"

Następujące ustawienia predefiniowane JSON i XML mogą być używane do tworzenia pojedynczego obrazu JPEG na 30-sekundowym znaku wejściowego wideo. To ustawienie wstępne oczekuje, że wejściowy film wideo będzie trwać dłużej niż 30 sekund (w przeciwnym razie zadanie zakończy się niepowodzeniem).

### <a name="json-preset"></a>Ustawienia wstępne JSON

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

### <a name="xml-preset"></a>Ustawienia predefiniowane XML
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

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Przykład predefiniowanych ustawień "miniatur w różnych rozdzielczościach"

Następujące ustawienie predefiniowane może służyć do generowania miniatur w różnych rozdzielczościach w jednym zadaniu. W przykładzie w pozycjach 5%, 15%, ..., 95% osi czasu wejściowego koder generuje dwa obrazy – jeden na 100% wejściowej rozdzielczości wideo, a drugi na 50%.

Zanotuj użycie makra {Resolution} w polu Nazwa_pliku; oznacza, że koder użyje szerokości i wysokości określonej w sekcji Kodowanie predefiniowanych ustawień podczas generowania nazwy pliku obrazów wyjściowych. Pomaga to również łatwo rozróżniać różne obrazy

### <a name="json-preset"></a>Ustawienia wstępne JSON

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

### <a name="xml-preset"></a>Ustawienia predefiniowane XML
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

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Przykład generowania miniatury podczas kodowania

Podczas gdy wszystkie powyższe przykłady omówiły sposób przesyłania zadania kodowania, które tylko tworzy obrazy, można również połączyć kodowanie wideo/ audio z generowaniem miniatur. Następujące ustawienia predefiniowane JSON i XML mówią **medicoder Standard,** aby wygenerował miniaturę podczas kodowania.

### <a name="json-preset"></a><a id="json"></a>Ustawienia wstępne JSON
Aby uzyskać informacje na temat schematu, zobacz [ten](https://msdn.microsoft.com/library/mt269962.aspx) artykuł.

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

### <a name="xml-preset"></a><a id="xml"></a>Ustawienia predefiniowane XML
Aby uzyskać informacje na temat schematu, zobacz [ten](https://msdn.microsoft.com/library/mt269962.aspx) artykuł.

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

## <a name="encode-video-and-generate-thumbnail-with-net"></a><a id="code_sample"></a>Kodowanie wideo i generowanie miniatury za pomocą platformy .NET

Poniższy przykład kodu używa media services .NET SDK do wykonywania następujących zadań:

* Utwórz zadanie kodowania.
* Uzyskaj odwołanie do kodera Media Encoder Standard.
* Załaduj wstępnie ustawiony kod [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) lub [JSON,](media-services-dotnet-generate-thumbnail-with-mes.md#json) który zawiera ustawienia predefiniowane kodowania, a także informacje potrzebne do generowania miniatur. Można zapisać ten [kod XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) lub [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) w pliku i użyć następującego kodu, aby załadować plik.
  
        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
* Dodaj do zadania pojedyncze zadanie kodowania. 
* Określ zasób wejściowy, który ma być zakodowany.
* Utwórz zasób wyjściowy zawierający zakodowany zasób.
* Dodaj program obsługi zdarzeń, aby sprawdzić postęp zadania.
* Przesyłanie zadania.

Zobacz [programowanie usługi Media Services z .NET](media-services-dotnet-how-to-use.md) artykuł, aby uzyskać wskazówki dotyczące konfigurowania środowiska deweloperskiego.

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
Obowiązują następujące zastrzeżenia:

* Użycie jawnych sygnatur czasowych dla start/step/range zakłada, że źródło wejściowe ma co najmniej 1 minutę.
* Elementy Jpg/Png/BmpImage mają atrybuty ciągu Start, Step i Range — można je interpretować w następujący sposób:
  
  * Numer ramki, jeśli są to nieujemne liczby całkowite, na przykład "Start": "120",
  * W stosunku do czasu trwania źródła, jeśli jest wyrażony jako %-sufiks, na przykład "Start": "15%", OR
  * Sygnatura czasowa wyrażona jako HH:MM:SS... Formacie. Na przykład "Start" : "00:01:00"
    
    Możesz mieszać i dopasowywki do notacji, jak chcesz.
    
    Ponadto start obsługuje również specjalne makro:{Best}, które próbuje określić pierwszą "interesującą" ramkę zawartości UWAGA: (Krok i zakres są ignorowane, gdy start jest ustawiony na {Najlepszy})
  * Ustawienia domyślne: Start:{Best}
* Format wyjściowy musi być jawnie podany dla każdego formatu obrazu: Jpg/Png/BmpFormat. Gdy jest obecny, MES pasuje do JpgVideo do JpgFormat i tak dalej. OutputFormat wprowadza nowe makro specyficzne dla kodeka obrazu: {Index}, które musi być obecne (raz i tylko raz) dla formatów wyjściowych obrazu.

## <a name="next-steps"></a>Następne kroki

Można sprawdzić [postęp zadania,](media-services-check-job-progress.md) gdy zadanie kodowania jest w toku.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Omówienie kodowania usług multimedialnych](media-services-encode-asset.md)

