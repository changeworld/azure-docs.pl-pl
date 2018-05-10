---
title: Kodowanie zaawansowane dostosowując ustawienia rynkowej | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak przeprowadzić Zaawansowane kodowanie dostosowując Media Encoder Standard ustawienia zadania.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: juliako
ms.openlocfilehash: 9480e6f3f651611e5281968d6d1651bd39dda44f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Kodowanie zaawansowane dostosowując rynkowej ustawienia 

## <a name="overview"></a>Przegląd

W tym temacie pokazano, jak dostosować ustawienia standardu Media Encoder Standard. [Kodowanie przy użyciu predefiniowanych Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md) temacie przedstawia sposób użycia platformy .NET do tworzenia kodowania zadań i zadań, która wykonuje to zadanie. Po dostosowaniu predefiniowanego Podaj predefiniowanych do kodowania zadań. 

>[!NOTE]
>Jeśli za pomocą wstępnie ustawionych XML, upewnij się zachować kolejność elementów, jak pokazano poniżej pokazano XML (na przykład KeyFrameInterval należy poprzedzać SceneChangeDetection).
>

W tym temacie przedstawiono w predefiniowanych, które wykonywać następujące zadania kodowania.

## <a name="support-for-relative-sizes"></a>Obsługa względnych rozmiarów

Podczas generowania miniatur, nie należy zawsze podać dane wyjściowe szerokość i wysokość w pikselach. Możesz określić je w procentach, w zakresie [1%,..., 100%].

### <a name="json-preset"></a>Ustawienie wstępne JSON
    "Width": "100%",
    "Height": "100%"

### <a name="xml-preset"></a>Ustawienie wstępne XML
    <Width>100%</Width>
    <Height>100%</Height>

## <a id="thumbnails"></a>Generowanie miniatur

W tej sekcji pokazano, jak dostosować ustawienie wstępne, które generuje miniatur. Ustawienie określonych poniżej zawiera informacje dotyczące sposobu kodowania z pliku, a także informacje potrzebne do generowania miniatur. Może mieć jedną z ustawienia rynkowej udokumentowane [to](media-services-mes-presets-overview.md) sekcji, a następnie dodaj kod, który generuje miniatur.  

> [!NOTE]
> **SceneChangeDetection** ustawienie w następujących ustawień można ustawić tylko na true, jeśli są kodowanie do pojedynczej szybkości transmisji bitów wideo. Jeśli są kodowanie wideo o różnych szybkościach transmisji bitów i zestawu **SceneChangeDetection** na wartość true, koder zwraca błąd.  
>
>

Aby uzyskać informacje o schemacie, zobacz [to](media-services-mes-schema.md) tematu.

Upewnij się przejrzeć [zagadnienia](#considerations) sekcji.

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
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
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

Następujące kwestie:

* Użycie jawnego sygnatury czasowe początku/krok/zakresu zakłada, że źródło danych wejściowych jest co najmniej 1 minutę.
* Elementy BmpImage-jpg/Png mają Start, krok i należeć do zakresu atrybutów ciąg — mogą być interpretowane jako:

  * Numer ramki, jeśli są one — nieujemne liczby całkowite, na przykład "Start": "120"
  * Względem źródła czas trwania, jeśli wyrażone jako sufiks %, na przykład "Start": "15%", lub
  * Sygnatura czasowa Jeśli wyrażonej w postaci hh: mm:... Format, na przykład "Start": "00: 01:00"

    Można mieszać i dopasowywać notacji jako użytkownik należy.

    Ponadto Start obsługuje makra specjalnego: {najlepszych}, który próbuje określić pierwszej ramki "interesujące" notatki zawartości: (krok i zakres są ignorowane, gdy Start ma ustawioną wartość {najlepiej})
  * Wartości domyślne: Start: {najlepsze}
* Format danych wyjściowych muszą zostać jawnie dostarczone dla każdego formatu obrazu: BmpFormat-Jpg/Png. Jeśli jest obecny, rynkowej odpowiada JpgVideo do JpgFormat i tak dalej. OutputFormat wprowadza nowe określone makro koder-dekoder obrazów: {indeks}, która musi być zawierają (jeden raz i tylko jeden raz) formatów wyjściowych obrazu.

## <a id="trim_video"></a>Przycinanie wideo (wycinka)
Ta sekcja zawiera informacje o modyfikując ustawienia kodera celu trim wejściowy plik wideo, gdy dane wejściowe są tak zwane mezzanine pliku lub plików na żądanie. Koder może również służyć do obcina lub trim zasób, przechwycone lub zarchiwizowane strumienia na żywo — szczegóły dla tego są dostępne w [ten blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Aby przyciąć filmy wideo, może mieć jedną z ustawienia rynkowej udokumentowane [to](media-services-mes-presets-overview.md) sekcji i zmodyfikuj **źródeł** elementu (jak pokazano poniżej). Wartość StartTime musi być zgodna bezwzględną znacznikami czasu wejściowy plik wideo. Na przykład, jeśli pierwszej ramki wejściowy plik wideo ma sygnaturę 12:00:10.000, następnie wartość StartTime powinna być co najmniej 12:00:10.000 lub nowszej. W poniższym przykładzie przyjęto założenie, że wejściowy plik wideo ma sygnaturę czasową początkowego o wartości zero. **Źródeł** należy umieścić na początku ustawienie.

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
Aby przyciąć filmy wideo, może mieć jedną z ustawienia rynkowej udokumentowane [tutaj](media-services-mes-presets-overview.md) i zmodyfikuj **źródeł** elementu (jak pokazano poniżej).

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
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

Media Encoder Standard umożliwia nakładki obrazu na istniejące wideo. Obecnie obsługiwane są następujące formaty: png, jpg, gif, bmp, a. Ustawienie określonych poniżej jest podstawowy przykład nakładka wideo.

Oprócz Definiowanie istniejących plików, masz również pozwala ustalić, który plik w elemencie zawartości jest obraz nakładki i plik, który jest źródłem wideo na którym chcesz nałożyć obrazu usługi Media Services. Plik musi być **głównej** pliku.

Jeśli używasz platformy .NET, Dodaj następujące dwie funkcje przykład .NET zdefiniowane w [to](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) tematu. **UploadMediaFilesFromFolder** funkcji przekazywania plików z folderu (na przykład BigBuckBunny.mp4 i Image001.png) i ustawia plik mp4 jest podstawowym plikiem w elemencie zawartości. **EncodeWithOverlay** funkcja używa niestandardowego pliku predefiniowanych, który został przekazany do niego (na przykład ustawienie poniżej) można utworzyć zadania kodowania.


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
> Ustawienie nieprzezroczystość nakładki nie jest obsługiwane.
>
> Źródłowy plik wideo i nakładki plik obrazu muszą być w tej samej zawartości, a plik wideo musi być ustawiona jako plik podstawowy w tym zasobów.
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
                  "OverlayLoopCount": 1,
                  "InputLoop": true
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
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
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
                  <InputLoop>false</InputLoop>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>true</InputLoop>
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


## <a id="silent_audio"></a>Wstawić dyskretnej ścieżki audio, gdy dane wejściowe nie zawierają żadnych audio
Domyślnie w przypadku wysłania danych wejściowych do kodera, który zawiera tylko wideo i audio nie następnie elementu zawartości wyjściowej zawiera pliki, które zawierają dane tylko wideo. Niektóre odtwarzacze nie można obsłużyć tych strumieni wyjściowych. To ustawienie umożliwia wymuszenie kodera, aby dodać dyskretnej ścieżki audio danych wyjściowych w tym scenariuszu.

Aby wymusić kodera, aby utworzyć element zawartości zawierający dyskretnej ścieżki audio, gdy dane wejściowe nie zawierają żadnych audio, określ wartość "InsertSilenceIfNoAudio".

Może mieć jedną z ustawienia rynkowej udokumentowane w [to](media-services-mes-presets-overview.md) sekcji, a następnie wprowadź następujące zmiany:

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

## <a id="deinterlacing"></a>Wyłączanie automatycznego usuwania przeplotu.
Klienci nie trzeba wykonywać żadnych czynności, jeśli ich zawartość przeplotu się automatycznie do naprzemiennych. Gdy przeplotu cofnąć automatycznie jest włączone (ustawienie domyślne) żądania uznania nie automatyczne wykrywanie naprzemiennych ramek i tylko cofnąć interlaces oznaczony jako naprzemiennych ramki.

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


## <a id="audio_only"></a>Ustawienia tylko do audio
W tej sekcji przedstawiono dwie predefiniowane rynkowej tylko dźwięk: AAC Audio i AAC dobrej jakości Audio.

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

### <a name="aac-good-quality-audio"></a>AAC dobrej jakości Audio
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

Poniższy przykład przedstawia, jak można wygenerować predefiniowanego do łączenia dwóch lub więcej plików wideo. Najbardziej typowym scenariuszem jest, gdy chcesz dodać nagłówka i centralną do głównego pliku wideo. Zamierzonym użyciem jest, gdy właściwości (rozdzielczości szybkość klatek, liczba ścieżki audio, itp.) udziału plików wideo edytowany razem. Należy zadbać nie można mieszać wideo stawek innej ramki lub z różną liczbą ścieżki audio.

>[!NOTE]
>Bieżący projekt funkcji łączenia oczekuje, że wejściowy klipy wideo są spójne pod względem rozdzielczości, szybkość klatek itp. 

### <a name="requirements-and-considerations"></a>Wymagania i uwagi

* Wejściowy wideo powinien mieć tylko jedną ścieżkę audio.
* Wideo wejściowe powinny mieć tego samego szybkość klatek.
* Należy przekazać pliki wideo do oddzielnych zasobów i ustawić wideo jako plik podstawowy w poszczególnych zasobów.
* Musisz wiedzieć, czas trwania plików wideo.
* Poniższe przykłady predefiniowanych przyjęto założenie, że wejściowy wideo rozpocząć z sygnaturą czasową o wartości zero. Należy zmodyfikować wartości StartTime, jeśli pliki wideo ma inną sygnaturą czasową początkowego, tak jak zwykle w przypadku z archiwami na żywo.
* Ustawienie JSON sprawia, że jawnego odwołania do wartości AssetID zasobów wejściowych.
* W przykładowym kodzie przyjęto założenie, że ustawienie JSON został zapisany do pliku lokalnego, takie jak "C:\supportFiles\preset.json". Zakłada się również, czy dwa zasoby zostały utworzone przez dwa pliki wideo i że znasz wynikowe wartości AssetID.
* Fragment kodu i JSON ustawienie wstępne przedstawiono przykład łączenia dwóch plików wideo. Można go rozszerzyć do więcej niż dwóch wideo przez:

  1. Zadanie telefonicznej. InputAssets.Add() wielokrotnie, aby dodać więcej filmów w kolejności.
  2. Tworzenie odpowiadającego edytuje do elementu "Sources" w formacie JSON, dodając więcej wpisów w tej samej kolejności.

### <a name="net-code"></a>Kodu platformy .NET

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

Zaktualizuj niestandardowe ustawienia wstępnego z identyfikatorami zasobów, które chcesz łączyć i z segmentem odpowiedni czas dla każdego wideo.

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

## <a id="crop"></a>Przytnij wideo przy użyciu Media Encoder Standard
Zobacz [Przycinanie wideo z Media Encoder Standard](media-services-crop-video.md) tematu.

## <a id="no_video"></a>Wstaw Ścieżka wideo, gdy dane wejściowe nie ma karty wideo

Domyślnie w przypadku wysłania danych wejściowych do kodera, który zawiera tylko audio i wideo nie następnie elementu zawartości wyjściowej zawiera pliki, które zawierają dane tylko audio. Niektóre odtwarzacze, łącznie z usługi Azure Media Player (zobacz [to](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)) nie może mieć możliwość obsługi tych strumieni. To ustawienie umożliwia wymuszenie kodera, aby dodać ścieżkę wideo monochromatyczny danych wyjściowych w tym scenariuszu.

> [!NOTE]
> Wymuszanie kodera, aby wstawić dane wyjściowe śledzenia wideo zwiększa rozmiar danych wyjściowych zasobów, a tym samym poniesione koszty zadania kodowania. Należy uruchomić testy, aby sprawdzić, czy to zwiększenie wynikowe jedynie niewielkie wpływ na Twoje opłaty miesięczne.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Wstawianie wideo na wyłącznie najniższa szybkość transmisji bitów

Załóżmy, że są przy użyciu wielu kodowania szybkości transmisji bitów ustawień, takich jak ["H264 szybkość transmisji bitów 720p"](media-services-mes-preset-h264-multiple-bitrate-720p.md) do zakodowania całego wejściowych katalogu do przesyłania strumieniowego, zawierający zarówno pliki wideo i pliki audio. W tym scenariuszu gdy dane wejściowe nie ma karty wideo, możesz wymusić kodera, aby wstawić monochromatyczny Ścieżka wideo na właśnie najniższa szybkość transmisji bitów, zamiast Wstawianie wideo w każdym szybkości transmisji bitów danych wyjściowych. Aby to osiągnąć, należy użyć **InsertBlackIfNoVideoBottomLayerOnly** flagi.

Może mieć jedną z ustawienia rynkowej udokumentowane w [to](media-services-mes-presets-overview.md) sekcji, a następnie wprowadź następujące zmiany:

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

Za pomocą XML, używaj warunku = "InsertBlackIfNoVideoBottomLayerOnly" jako atrybutu do **H264Video** elementu i warunku = "InsertSilenceIfNoAudio" jako atrybutu do **AACAudio**.

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

### <a name="inserting-video-at-all-output-bitrates"></a>Wstawianie wideo na wszystkich danych wyjściowych szybkości transmisji bitów
Załóżmy, że są przy użyciu wielu kodowania szybkości transmisji bitów ustawień, takich jak ["H264 szybkość transmisji bitów 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) do zakodowania całego wejściowych katalogu do przesyłania strumieniowego, zawierający zarówno pliki wideo i pliki audio. W tym scenariuszu gdy dane wejściowe nie ma karty wideo, możesz wymusić kodera, aby wstawić monochromatyczny Ścieżka wideo na wszystkich szybkości transmisji bitów danych wyjściowych. Gwarantuje to, że dane wyjściowe są wszystkie jednorodnego względem liczba ścieżek wideo i audio ścieżki. Aby to osiągnąć, należy określić flagi "InsertBlackIfNoVideo".

Może mieć jedną z ustawienia rynkowej udokumentowane w [to](media-services-mes-presets-overview.md) sekcji, a następnie wprowadź następujące zmiany:

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

Za pomocą XML, używaj warunku = "InsertBlackIfNoVideo" jako atrybutu do **H264Video** elementu i warunku = "InsertSilenceIfNoAudio" jako atrybutu do **AACAudio**.

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
[Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) obsługuje obrotu kątami 0/90/180/270. Domyślne zachowanie to "Auto", gdy próbuje wykryć metadanych obrotu przychodzące plik wideo, a także kompensuje go. Obejmują **źródeł** elementu do jednego z ustawień określonych w [to](media-services-mes-presets-overview.md) sekcji:

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

Zobacz też [to](media-services-mes-schema.md#PreserveResolutionAfterRotation) tematu, aby uzyskać więcej informacji na koder interpretowanie ustawienia szerokość i wysokość w ustawienie wstępne, po wyzwoleniu kompensacji obrotu.

Wartość "0" służy do wskazania koder zignorowanie obrotu metadane, jeśli jest obecny w danych wejściowych wideo.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Usługi multimediów kodowania — omówienie](media-services-encode-asset.md)
