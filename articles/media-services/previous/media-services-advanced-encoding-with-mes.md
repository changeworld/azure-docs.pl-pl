---
title: Kodowanie zaawansowane, dostosowując ustawienia wstępne usługi MES | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak przeprowadzić Zaawansowane kodowanie za pomocą usługi Media Encoder Standard zadań wstępne dostosowania.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: fadf1aa54f525fb3d4c414161583f8a89f2e4c05
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230257"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Kodowanie zaawansowane, dostosowując ustawienia wstępne usługi MES 

## <a name="overview"></a>Omówienie

W tym temacie przedstawiono sposób Dostosowywanie ustawień wstępnych usługi Media Encoder Standard. [Kodowania za pomocą usługi Media Encoder Standard za pomocą niestandardowych ustawień wstępnych](media-services-custom-mes-presets-with-dotnet.md) temacie pokazano, jak utworzyć zadanie kodowania i zadanie, które wykonuje to zadanie za pomocą platformy .NET. Po dostosowaniu ustawienie wstępne dostarczania niestandardowych ustawień wstępnych do kodowania zadania. 

Jeśli za pomocą wstępnie ustawionych XML, upewnij się zachować kolejność elementów, jak pokazano w poniższych przykładach XML (na przykład KeyFrameInterval powinien poprzedzać SceneChangeDetection).

> [!NOTE] 
> Wiele zaawansowanych usług Media Services v2 funkcji Media Encoder Standard nie są obecnie dostępne w wersji 3. Aby uzyskać więcej informacji, zobacz [funkcji luki](https://docs.microsoft.com/azure/media-services/latest/migrate-from-v2-to-v3#feature-gaps-with-respect-to-v2-apis).

## <a name="support-for-relative-sizes"></a>Obsługa względne rozmiary

Podczas generowania miniatur, nie musisz zawsze określić dane wyjściowe szerokość i wysokość w pikselach. Możesz określić je w procentach, w zakresie [1%,..., 100%].

### <a name="json-preset"></a>Ustawienie wstępne JSON
    "Width": "100%",
    "Height": "100%"

### <a name="xml-preset"></a>Ustawienie wstępne XML
    <Width>100%</Width>
    <Height>100%</Height>

## <a id="thumbnails"></a>Generowanie miniatur

W tej sekcji pokazano, jak dostosować ustawienie wstępne, które generuje obrazy miniatur. Ustawienie wstępne zdefiniowane poniżej zawiera informacje na temat sposobu kodowania z pliku, a także informacje potrzebne do generowania miniatur. Można wykonać dowolne ustawienia wstępne usługi MES udokumentowane [to](media-services-mes-presets-overview.md) sekcji i Dodaj kod, który generuje obrazy miniatur.  

> [!NOTE]
> **SceneChangeDetection** ustawienie w następujące ustawienie wstępne można ustawić tylko wartość true, jeśli są kodowanie pojedyncza szybkość transmisji bitów wideo. Jeśli kodujesz film wideo o różnych szybkościach transmisji bitów i ustaw **SceneChangeDetection** do ma wartość true, kodera zwraca błąd.  
>
>

Aby uzyskać informacje o schemacie, zobacz [to](media-services-mes-schema.md) tematu.

Upewnij się zapoznać się z [zagadnienia](#considerations) sekcji.

### <a id="json"></a>Ustawienie wstępne JSON
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
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": 640,
              "Height": 360,
            }
          ],
          "Start": "00:00:01",
          "Step": "00:00:10",
          "Range": "00:00:58",
          "Type": "PngImage"
        },
        {
          "BmpLayers": [
            {
              "Type": "BmpLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "10%",
          "Step": "10%",
          "Range": "90%",
          "Type": "BmpImage"
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
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "BmpFormat"
          }
        },
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### <a id="xml"></a>Ustawienie wstępne XML
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
              <Width>640</Width>
              <Height>360</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
        <BmpImage Start="10%" Step="10%" Range="90%">
          <BmpLayers>
            <BmpLayer>
              <Width>640</Width>
              <Height>360</Height>
            </BmpLayer>
          </BmpLayers>
        </BmpImage>
        <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
          <PngLayers>
            <PngLayer>
              <Width>640</Width>
              <Height>360</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <BmpFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

### <a name="considerations"></a>Zagadnienia do rozważenia

Mają zastosowanie następujące kwestie:

* Użycie jawnego sygnatury czasowe początku/krok/zakresu przyjęto założenie, że źródło danych wejściowych jest co najmniej 1 minutę.
* Jpg/Png/BmpImage elementy mają Start kroku i zakresu z atrybutami ciągu — mogą być interpretowane jako:

  * Numer klatki, jeśli są one nieujemnymi liczbami całkowitymi, na przykład "Start": "120",
  * Względem czasu trwania źródła, jeśli wyrażonej w postaci nieujemnej, na przykład "Start": "15%", OR
  * Sygnatura czasowa, jeśli wyrażonej w postaci: mm: ss... Format, na przykład "Start": "00:01:00"

    Możesz mieszać i dopasowywać notacji, jak należy.

    Ponadto Start obsługuje makro specjalne: {najlepsze}, który próbuje określić pierwszej ramki "interesujący" uwagi zawartości: (Krok i zakres są ignorowane, gdy rozpoczęcia jest ustawiona na {najlepiej})
  * Domyślne: Start: {najlepsze}
* Format danych wyjściowych musi zostać podane jawnie dla każdego format obrazu: Jpg/Png/BmpFormat. Jeśli jest obecny, MES dopasowuje JpgVideo do JpgFormat i tak dalej. OutputFormat wprowadza nowe makro określonych koder-dekoder obrazu: {Index}, który musi być obecne (jeden raz i tylko jeden raz) dla formatów danych wyjściowych obrazu.

## <a id="trim_video"></a>Przycinanie wideo (wycinka)
Ta sekcja zawiera informacje o modyfikowanie ustawień wstępnych kodera obcina lub trim wejściowy plik wideo, których dane wejściowe są tak zwane mezzanine pliku lub plików na żądanie. Koder można również przycinanie lub trim środka trwałego, co jest przechwytywane lub archiwizacji ze strumienia na żywo — szczegóły dla tego są dostępne w [ten blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Aby trim filmów wideo, należy wykonać dowolne ustawienia wstępne usługi MES udokumentowane [to](media-services-mes-presets-overview.md) sekcji i modyfikować **źródeł** — element (jak pokazano poniżej). Wartość StartTime musi być zgodna bezwzględne sygnatury czasowe wejściowego filmu wideo. Na przykład, jeśli pierwszej ramki wejściowy plik wideo ma sygnaturę czasową 12:00:10.000, następnie wartość StartTime powinna być co najmniej 12:00:10.000 lub nowszym. W poniższym przykładzie przyjęto założenie, że wejściowy plik wideo ma sygnaturę czasową począwszy od zera. **Źródła** powinny być umieszczone na początku ustawienia wstępnego.

### <a id="json"></a>Ustawienie wstępne JSON
    {
      "Version": 1.0,
      "Sources": [
        {
          "StartTime": "00:00:04",
          "Duration": "00:00:16"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1500,
              "MaxBitrate": 1500,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1000,
              "MaxBitrate": 1000,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 650,
              "MaxBitrate": 650,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 400,
              "MaxBitrate": 400,
              "BufferWindow": "00:00:05",
              "Width": 320,
              "Height": 180,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### <a name="xml-preset"></a>Ustawienie wstępne XML
Aby trim filmów wideo, należy wykonać dowolne ustawienia wstępne usługi MES udokumentowane [tutaj](media-services-mes-presets-overview.md) i modyfikować **źródeł** — element (jak pokazano poniżej).

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source StartTime="PT4S" Duration="PT14S"/>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>3400</Bitrate>
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
              <MaxBitrate>3400</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>2250</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>2250</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1500</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1500</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1000</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1000</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>650</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>650</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>400</Bitrate>
              <Width>320</Width>
              <Height>180</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>400</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

## <a id="overlay"></a>Utwórz nakładki

Media Encoder Standard umożliwia nakładki obrazu do istniejącego wideo. Obecnie obsługiwane są następujące formaty: png, jpg, gif oraz bmp. Ustawienie wstępne zdefiniowane poniżej jest podstawowy przykład nakładka wideo.

Oprócz definiowania istniejących plików, masz również umożliwić usługi Media Services wiedzieć, plik, który w elemencie zawartości jest styl obrazowy nakładki i plik, który jest źródłem wideo na który chcesz nakładki na obrazie. Plik wideo ma być **głównej** pliku.

Jeśli używasz platformy .NET, Dodaj następujące dwie funkcje na przykład .NET, zdefiniowane w [to](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) tematu. **UploadMediaFilesFromFolder** funkcja przekazuje pliki z folderu (na przykład BigBuckBunny.mp4 i Image001.png) i ustawia plik mp4 do podstawowego pliku w elemencie zawartości. **EncodeWithOverlay** funkcja używa niestandardowego pliku wstępnie zdefiniowane, która została przekazana do niej (na przykład, ustawienie wstępne, które następuje) do utworzenia zadania kodowania.


    static public IAsset UploadMediaFilesFromFolder(string folderPath)
    {
        IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);
    
        foreach (var af in asset.AssetFiles)
        {
            // The following code assumes 
            // you have an input folder with one MP4 and one overlay image file.
            if (af.Name.Contains(".mp4"))
                af.IsPrimary = true;
            else
                af.IsPrimary = false;
    
            af.Update();
        }
    
        return asset;
    }

    static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
    {
        // Declare a new job.
        IJob job = _context.Jobs.Create("Media Encoder Standard Job");
        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(customPresetFileName);

        // Create a task
        ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input assets to be encoded.
        // This asset contains a source file and an overlay file.
        task.InputAssets.Add(assetSource);

        // Add an output asset to contain the results of the job. 
        task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
        job.Submit();
        job.GetExecutionProgressTask(CancellationToken.None).Wait();

        return job.OutputMediaAssets[0];
    }


> [!NOTE]
> Bieżące ograniczenia:
>
> Nakładka krycie nie jest obsługiwana.
>
> Źródłowy plik wideo i nakładki plik obrazu muszą znajdować się w tej samej zawartości, a plik wideo musi być ustawiona jako plik podstawowy, w tym zasobie.
>
>

### <a name="json-preset"></a>Ustawienie wstępne JSON
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "VideoOverlay": {
              "Position": {
                "X": 100,
                "Y": 100,
                "Width": 100,
                "Height": 50
              },
              "AudioGainLevel": 0.0,
              "MediaParams": [
                {
                  "OverlayLoopCount": 1
                },
                {
                  "IsOverlay": true,
                  "OverlayLoopCount": 1
                }
              ],
              "Source": "Image001.png",
              "Clip": {
                "Duration": "00:00:05"
              },
              "FadeInDuration": {
                "Duration": "00:00:01"
              },
              "FadeOutDuration": {
                "StartTime": "00:00:03",
                "Duration": "00:00:04"
              }
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1045,
              "MaxBitrate": 1045,
              "BufferWindow": "00:00:05",
              "ReferenceFrames": 3,
              "EntropyMode": "Cavlc",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Type": "CopyAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}{Extension}",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### <a name="xml-preset"></a>Ustawienie wstępne XML
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source>
          <Streams />
          <Filters>
            <VideoOverlay>
              <Source>Image001.png</Source>
              <Clip Duration="PT5S" />
              <FadeInDuration Duration="PT1S" />
              <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
              <Position X="100" Y="100" Width="100" Height="50" />
              <Opacity>0</Opacity>
              <AudioGainLevel>0</AudioGainLevel>
              <MediaParams>
                <MediaParam>
                  <IsOverlay>false</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                </MediaParam>
              </MediaParams>
            </VideoOverlay>
          </Filters>
          <Pad>true</Pad>
        </Source>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>1045</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>0</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cavlc</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1045</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <CopyAudio />
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}{Extension}">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>


## <a id="silent_audio"></a>Wstawić dyskretnej ścieżki audio, jeśli dane wejściowe zawierają brak audio
Domyślnie Jeśli wyślesz dane wejściowe do kodera, który zawiera tylko, audio i wideo nie, następnie elementu zawartości wyjściowej zawiera pliki, które zawierają dane tylko wideo. Niektóre odtwarzacze nie można obsłużyć takich strumieni danych wyjściowych. To ustawienie umożliwia wymuszenie kodera można dodać dyskretnej ścieżki audio w danych wyjściowych w tym scenariuszu.

Aby wymusić kodera, aby utworzyć element zawartości zawierający dyskretnej ścieżki audio, gdy dane wejściowe zawierają bez dźwięku, określ wartość "InsertSilenceIfNoAudio".

Można wykonać dowolne ustawienia wstępne usługi MES udokumentowane w artykule [to](media-services-mes-presets-overview.md) sekcji, a następnie wprowadzić następujące zmiany:

### <a name="json-preset"></a>Ustawienie wstępne JSON
    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

### <a name="xml-preset"></a>Ustawienie wstępne XML
    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

## <a id="deinterlacing"></a>Wyłącz automatyczne usuwanie przeplotu.
Klienci nie muszą nic robić, jeśli ich zawartość przeplotu, aby się automatycznie, usuń zaznaczenie pola z przeplotem. Gdy przeplotu cofnąć automatyczne jest włączone (ustawienie domyślne) system MES wykonuje automatyczne wykrywanie ramki z przeplotem i tylko cofnąć interlaces ramek oznaczone jako z przeplotem.

Można wyłączyć przeplotu cofnąć automatycznie. Ta opcja nie jest zalecane.

### <a name="json-preset"></a>Ustawienie wstępne JSON
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

### <a name="xml-preset"></a>Ustawienie wstępne XML
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


## <a id="audio_only"></a>Ustawienia wstępne tylko dane audio
W tej sekcji przedstawiono dwa ustawienia tylko dane audio wstępne usługi MES: AAC Audio i AAC dobrą jakość dźwięku.

### <a name="aac-audio"></a>AAC Audio
    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### <a name="aac-good-quality-audio"></a>Dźwięk dobrej jakości adaptacyjnych kontrolek aplikacji
    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a id="concatenate"></a>Łączenie dwóch lub więcej plików wideo

W poniższym przykładzie pokazano, jak można wygenerować ustawienie wstępne do łączenia dwóch lub więcej plików wideo. Najbardziej typowym scenariuszem jest, gdy chcesz dodać nagłówek lub przyczepy do głównego pliku wideo. Przeznaczeniem jest, gdy pliki wideo, edytowany razem mają właściwości (rozdzielczości wideo szybkość klatek, liczba ścieżki audio, itp.). Należy zadbać nie można mieszać filmów wideo, różne szybkości odtwarzania lub inny numer ścieżki audio.

>[!NOTE]
>Bieżący projekt funkcja łączenia oczekuje, że wejściowy klipów wideo są spójne pod względem rozdzielczości, szybkości klatek itp. 

### <a name="requirements-and-considerations"></a>Wymagania i uwagi

* Filmy wideo w danych wejściowych powinien mieć tylko jedną ścieżkę audio.
* Wprowadź filmów wideo, powinny mieć ten sam szybkość klatek.
* Musisz przekazać wideo do oddzielnych zasobów i ustawić filmy wideo jako plik podstawowy w poszczególnych zasobów.
* Musisz wiedzieć, czas trwania filmów wideo.
* Poniższe przykłady wstępnie zdefiniowane przyjęto założenie, że wszystkich wejściowych plików wideo rozpocząć z sygnaturą czasową o wartości zero. Należy zmodyfikować wartości StartTime, jeśli wideo ma różne timestamp początkowy, tak jak zwykle w przypadku z archiwami na żywo.
* Ustawienie wstępne JSON sprawia, że jawne odwołania do wartości AssetID zasoby danych wejściowych.
* W przykładowym kodzie założono, że ustawienie wstępne JSON został zapisany do pliku lokalnego, takie jak "C:\supportFiles\preset.json". Przyjęto również założenie, że dwa zasoby zostały utworzone przez przekazanie dwa pliki wideo oraz poznać wynikowe wartości AssetID.
* Fragment kodu i JSON ustawienie wstępne przedstawiono przykład łączenia dwóch plików wideo. Można go rozszerzyć do więcej niż dwóch filmów wideo według:

  1. Zadanie wywołania. InputAssets.Add() wielokrotnie, aby dodać więcej filmów wideo, w kolejności.
  2. Dzięki czemu odpowiadające edytuje do elementu "Źródła" w formacie JSON, dodając więcej wpisów w tej samej kolejności.

### <a name="net-code"></a>Kod platformy .NET

    IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
    IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();

    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
    // Get a media processor reference, and pass to it the name of the
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");

    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);

    // Specify the input videos to be concatenated (in order).
    task.InputAssets.Add(asset1);
    task.InputAssets.Add(asset2);
    // Add an output asset to contain the results of the job.
    // This output is specified as AssetCreationOptions.None, which
    // means the output asset is not encrypted.
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);

    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

### <a name="json-preset"></a>Ustawienie wstępne JSON

Zaktualizuj niestandardowe ustawienie wstępne o identyfikatorach zasobów, które chcesz połączyć, a także z segmentem odpowiednim czasem dla każdego pliku wideo.

    {
      "Version": 1.0,
      "Sources": [
        {
          "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
          "StartTime": "00:00:01",
          "Duration": "00:00:15"
        },
        {
          "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
          "StartTime": "00:00:02",
          "Duration": "00:00:05"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [
            {
              "Level": "auto",
              "Bitrate": 1800,
              "MaxBitrate": 1800,
              "BufferWindow": "00:00:05",
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
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
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a id="crop"></a>Przycinanie wideo za pomocą usługi Media Encoder Standard
Zobacz [Przycinanie wideo za pomocą usługi Media Encoder Standard](media-services-crop-video.md) tematu.

## <a id="no_video"></a>Wstawianie ścieżki wideo, gdy dane wejściowe nie ma karty wideo

Domyślnie w przypadku wysłania danych wejściowych do kodera, który zawiera tylko dźwięk i żadnego klipu wideo, następnie elementu zawartości wyjściowej zawiera pliki, które zawierają dane tylko audio. Niektóre odtwarzaczy, w tym przez Azure Media Player (zobacz [to](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)) nie może mieć możliwość obsługi takich strumieni. To ustawienie umożliwia wymuszenie kodera dodać monochromatycznych Śledź wideo do danych wyjściowych w tym scenariuszu.

> [!NOTE]
> Wymuszanie kodera, aby wstawić dane wyjściowe śledzenia wideo zwiększa rozmiar danych wyjściowych elementu zawartości, a tym samym koszt jest naliczany dla zadania kodowania. Należy przeprowadzić testy, aby sprawdzić, czy to zwiększenie wynikowy ma jedynie wpływ skromną na Twoich miesięcznych opłat.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Wstawianie zawartości wideo na tylko najniższej szybkości transmisji bitów

Załóżmy, że są przy użyciu wielu kodowania szybkości transmisji bitów ustawień, takich jak ["H264 szybkość transmisji bitów h264 720p"](media-services-mes-preset-h264-multiple-bitrate-720p.md) do zakodowania całego wejściowych katalogu do przesyłania strumieniowego, zawierającą różnych plików wideo i audio — pliki tylko do. W tym scenariuszu po danych wejściowych nie ma karty wideo, można wymusić kodera Wstawianie monochromatycznych Ścieżka wideo na tylko najniższej szybkości transmisji bitów, w przeciwieństwie Wstawianie zawartości wideo na szybkość transmisji bitów co dane wyjściowe. Aby to osiągnąć, należy użyć **InsertBlackIfNoVideoBottomLayerOnly** flagi.

Można wykonać dowolne ustawienia wstępne usługi MES udokumentowane w artykule [to](media-services-mes-presets-overview.md) sekcji, a następnie wprowadzić następujące zmiany:

#### <a name="json-preset"></a>Ustawienie wstępne JSON
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>Ustawienie wstępne XML

Gdy za pomocą języka XML, należy użyć warunku = "InsertBlackIfNoVideoBottomLayerOnly" jako atrybut do **H264Video** elementu i stan = "InsertSilenceIfNoAudio" jako atrybut do **AACAudio**.

```
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideoBottomLayerOnly">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .
```

### <a name="inserting-video-at-all-output-bitrates"></a>Wstawianie zawartości wideo na wszystkich danych wyjściowych szybkości transmisji
Załóżmy, że są przy użyciu wielu kodowania szybkości transmisji bitów ustawień, takich jak ["H264 szybkość transmisji bitów h264 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) do zakodowania całego wejściowych katalogu do przesyłania strumieniowego, zawierającą różnych plików wideo i audio — pliki tylko do. W tym scenariuszu w przypadku danych wejściowych nie ma karty wideo, można wymusić kodera do wstawienia monochromatycznych Ścieżka wideo na wszystkich szybkości transmisji danych wyjściowych. Gwarantuje to, że dane wyjściowe są wszystkie jednorodnego względem liczby ścieżki audio i wideo ścieżki. Aby to osiągnąć, należy określić flagę "InsertBlackIfNoVideo".

Można wykonać dowolne ustawienia wstępne usługi MES udokumentowane w artykule [to](media-services-mes-presets-overview.md) sekcji, a następnie wprowadzić następujące zmiany:

#### <a name="json-preset"></a>Ustawienie wstępne JSON
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>Ustawienie wstępne XML

Gdy za pomocą języka XML, należy użyć warunku = "InsertBlackIfNoVideo" jako atrybut do **H264Video** elementu i stan = "InsertSilenceIfNoAudio" jako atrybut do **AACAudio**.

```
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideo">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .  
```

## <a id="rotate_video"></a>Obróć wideo
[Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) obsługuje obrotu kątami 0/90/180/270. Domyślne zachowanie to "Auto", gdzie próbuje wykryć metadanych obrotu w przychodzących plików wideo, a także kompensuje ona. Obejmują następujące elementy **źródeł** elementu wstępne zdefiniowane w [to](media-services-mes-presets-overview.md) sekcji:

### <a name="json-preset"></a>Ustawienie wstępne JSON
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...
### <a name="xml-preset"></a>Ustawienie wstępne XML
    <Sources>
           <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

Zobacz też [to](media-services-mes-schema.md#PreserveResolutionAfterRotation) tematu więcej informacji na temat kodera interpretowanie ustawienia szerokość i wysokość w ustawienie wstępne, po wyzwoleniu wynagrodzenie obrotu.

Wartość "0" służy do wskazywania kodera ignorowanie metadanych obrotu, jeśli jest dostępna w wejściowego pliku wideo.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Omówienie kodowania usługi Media Services](media-services-encode-asset.md)
