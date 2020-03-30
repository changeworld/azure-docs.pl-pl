---
title: Wykonywanie zaawansowanego kodowania przez dostosowywanie ustawień predefiniowanych MES | Dokumenty firmy Microsoft
description: W tym temacie pokazano, jak wykonać zaawansowane kodowanie, dostosowując ustawienia predefiniowane standardowych zadań programu Media Encoder.
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
ms.openlocfilehash: 5f7611fd9df207df51fa0e51218d8a234583b1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529787"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Wykonywanie zaawansowanego kodowania przez dostosowywanie ustawień predefiniowanych mes 

## <a name="overview"></a>Omówienie

W tym temacie pokazano, jak dostosować ustawienia predefiniowane programu Media Encoder Standard. W temacie [Kodowanie za pomocą standardu kodera multimediów przy użyciu niestandardowych ustawień predefiniowanych](media-services-custom-mes-presets-with-dotnet.md) przedstawiono sposób tworzenia zadania kodowania za pomocą programu .NET i zadania wykonującego to zadanie. Po dostosowaniu ustawień predefiniowanych należy podać niestandardowe ustawienia predefiniowane do zadania kodowania. 

Jeśli używasz ustawienia predefiniowane XML, upewnij się, aby zachować kolejność elementów, jak pokazano w przykładzie XML poniżej (na przykład KeyFrameInterval powinien poprzedzać SceneChangeDetection).

> [!NOTE] 
> Wiele zaawansowanych funkcji usługi Media Services w wersji 2 wzoru Media Encoder Standard nie jest obecnie dostępnych w wersji 3. Aby uzyskać więcej informacji, zobacz [luki w operacjach](https://docs.microsoft.com/azure/media-services/latest/media-services-v2-vs-v3#feature-gaps-with-respect-to-v2-apis).

## <a name="support-for-relative-sizes"></a>Obsługa względnych rozmiarów

Podczas generowania miniatur nie trzeba zawsze określać szerokość i wysokość wydruku w pikselach. Można je określić w procentach, w zakresie [1%, ..., 100%].

### <a name="json-preset"></a>Ustawienia wstępne JSON
    "Width": "100%",
    "Height": "100%"

### <a name="xml-preset"></a>Ustawienia predefiniowane XML
    <Width>100%</Width>
    <Height>100%</Height>

## <a name="generate-thumbnails"></a><a id="thumbnails"></a>Generowanie miniatur

W tej sekcji pokazano, jak dostosować ustawienia wstępne, które generuje miniatury. Predefiniowane zdefiniowane poniżej zawiera informacje o tym, jak chcesz zakodować plik, a także informacje potrzebne do generowania miniatur. Możesz wziąć dowolne ustawienia predefiniowane MES udokumentowane [w tej](media-services-mes-presets-overview.md) sekcji i dodać kod, który generuje miniatury.  

> [!NOTE]
> Ustawienie **SceneChangeDetection** w następującym ustawieniu predefiniowane można ustawić na true tylko wtedy, gdy kodujesz do pojedynczego wideo o szybkości transmisji bitów. Jeśli kodujesz wideo o wielu szybkościach transmisji bitów i ustawiasz **SceneChangeDetection** na true, koder zwraca błąd.  
>
>

Aby uzyskać informacje na temat schematu, zobacz [ten](media-services-mes-schema.md) temat.

Zapoznaj się z sekcją [Zagadnienia.](#considerations)

### <a name="json-preset"></a><a id="json"></a>Ustawienia wstępne JSON
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


### <a name="xml-preset"></a><a id="xml"></a>Ustawienia predefiniowane XML
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

Obowiązują następujące zastrzeżenia:

* Użycie jawnych sygnatur czasowych dla start/step/range zakłada, że źródło wejściowe ma co najmniej 1 minutę.
* Elementy Jpg/Png/BmpImage mają atrybuty ciągu Start, Step i Range — można je interpretować w następujący sposób:

  * Numer ramki, jeśli są to nieujemne liczby całkowite, na przykład "Start": "120",
  * W stosunku do czasu trwania źródła, jeśli jest wyrażony jako %-sufiks, na przykład "Start": "15%", OR
  * Sygnatura czasowa wyrażona jako HH:MM:SS... na przykład "Start" : "00:01:00"

    Możesz mieszać i dopasowywki do notacji, jak chcesz.

    Ponadto start obsługuje również specjalne makro:{Best}, które próbuje określić pierwszą "interesującą" ramkę zawartości UWAGA: (Krok i zakres są ignorowane, gdy start jest ustawiony na {Najlepszy})
  * Ustawienia domyślne: Start:{Best}
* Format wyjściowy musi być jawnie podany dla każdego formatu obrazu: Jpg/Png/BmpFormat. Gdy jest obecny, MES pasuje do JpgVideo do JpgFormat i tak dalej. OutputFormat wprowadza nowe makro specyficzne dla kodeka obrazu: {Index}, które musi być obecne (raz i tylko raz) dla formatów wyjściowych obrazu.

## <a name="trim-a-video-clipping"></a><a id="trim_video"></a>Przycinanie wideo (przycinanie)
W tej sekcji o mówi się o modyfikowaniu ustawień precoder do klipu lub przycięcia wejściowego wideo, w którym dane wejściowe są tak zwanym plikiem antresoli lub plikiem na żądanie. Koder może być również używany do przycinania lub przycinania zasobu, który jest przechwytywany lub archiwizowany ze strumienia na żywo - szczegóły tego są dostępne w [tym blogu.](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)

Aby przyciąć filmy, możesz wziąć dowolne ustawienia MES udokumentowane [w tej](media-services-mes-presets-overview.md) sekcji i zmodyfikować element **Źródła** (jak pokazano poniżej). Wartość StartTime musi odpowiadać bezwzględnym sygnaturom czasowym wejściowego wideo. Na przykład jeśli pierwsza klatka wejściowego wideo ma sygnaturę czasową 12:00:10.000, następnie StartTime powinien być co najmniej 12:00:10.000 i większa. W poniższym przykładzie zakładamy, że wejściowy film wideo ma sygnaturę czasową początkową zero. **Źródła** powinny być umieszczone na początku predefiniowanych ustawień.

### <a name="json-preset"></a><a id="json"></a>Ustawienia wstępne JSON
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

### <a name="xml-preset"></a>Ustawienia predefiniowane XML
Aby przyciąć filmy, możesz wziąć dowolne z udokumentowanych [tutaj](media-services-mes-presets-overview.md) ustawień MES i zmodyfikować element **Źródła** (jak pokazano poniżej).

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

## <a name="create-an-overlay"></a><a id="overlay"></a>Tworzenie nakładki

Standard emkweracie multimediów umożliwia nałkienie obrazu na istniejący film. Obecnie obsługiwane są następujące formaty: png, jpg, gif i bmp. Predefiniowane ustawienie zdefiniowane poniżej jest podstawowym przykładem nakładki wideo.

Oprócz zdefiniowania wstępnie ustawionego pliku, należy również poinformować program Media Services, który plik w zasobie jest obrazem nakładki i który plik jest źródłowym plikiem wideo, na który chcesz nałożyć obraz. Plik wideo musi być plikiem **podstawowym.**

Jeśli używasz platformy .NET, dodaj następujące dwie funkcje do przykładu .NET zdefiniowanego w [tym](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) temacie. Funkcja **UploadMediaFilesFromFolder** przesyła pliki z folderu (na przykład BigBuckBunny.mp4 i Image001.png) i ustawia plik mp4 jako podstawowy plik w zasobie. **Funkcja EncodeWithOverlay** używa niestandardowego pliku predefiniowanego, który został do niego przekazany (na przykład preset, który następuje), aby utworzyć zadanie kodowania.


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
> Aktualne ograniczenia:
>
> Ustawienie krycia nakładki nie jest obsługiwane.
>
> Źródłowy plik wideo i plik obrazu nakładki muszą znajdować się w tym samym zasobie, a plik wideo musi być ustawiony jako plik podstawowy w tym zasobie.
>
>

### <a name="json-preset"></a>Ustawienia wstępne JSON
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


### <a name="xml-preset"></a>Ustawienia predefiniowane XML
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


## <a name="insert-a-silent-audio-track-when-input-has-no-audio"></a><a id="silent_audio"></a>Włóż cichą ścieżkę audio, gdy wejście nie ma dźwięku
Domyślnie jeśli wysyłasz dane wejściowe do kodera, który zawiera tylko wideo i nie audio, a następnie zasób wyjściowy zawiera pliki, które zawierają tylko dane wideo. Niektórzy gracze mogą nie być w stanie obsłużyć takich strumieni wyjściowych. To ustawienie służy do wymuszania kodera, aby dodać cichą ścieżkę audio do danych wyjściowych w tym scenariuszu.

Aby wymusić koder do wytworzenia zasobu, który zawiera cichą ścieżkę audio, gdy wejście nie ma dźwięku, należy określić wartość "InsertSilenceIfNoAudio".

Można wykonać dowolne ustawienia mes udokumentowane w [tej](media-services-mes-presets-overview.md) sekcji i dokonać następującej modyfikacji:

### <a name="json-preset"></a>Ustawienia wstępne JSON
    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

### <a name="xml-preset"></a>Ustawienia predefiniowane XML
    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

## <a name="disable-auto-de-interlacing"></a><a id="deinterlacing"></a>Wyłącz automatyczne usuwanie przeplotu
Klienci nie muszą nic robić, jeśli podoba im się zawartość przeplotu, aby automatycznie zdjęła z przeplotem. Gdy automatyczne usuwanie przeplotu jest włączone (domyślnie), mes automatycznie wykrywa klatki z przeplotem i usuwa tylko przeploty ramek oznaczonych jako przeplot.

Automatyczne usuwanie przeplotu można wyłączyć. Ta opcja nie jest zalecana.

### <a name="json-preset"></a>Ustawienia wstępne JSON
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

### <a name="xml-preset"></a>Ustawienia predefiniowane XML
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


## <a name="audio-only-presets"></a><a id="audio_only"></a>Predefiniowane ustawienia tylko audio
W tej sekcji przedstawiono dwa ustawienia mes tylko audio: AAC Audio i AAC Dobrej Jakości Audio.

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

### <a name="aac-good-quality-audio"></a>AAC Dobrej jakości dźwięku
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

## <a name="concatenate-two-or-more-video-files"></a><a id="concatenate"></a>Łączenie dwóch lub więcej plików wideo

Poniższy przykład ilustruje, jak można wygenerować predefiniowane do łączenia dwóch lub więcej plików wideo. Najczęstszym scenariuszem jest dodanie nagłówka lub zwiastuna do głównego filmu. Przeznaczenie jest wtedy, gdy edytowane pliki wideo są razem współużytkowane właściwości (rozdzielczość wideo, liczba klatek na sekundę, liczba ścieżek audio, itp.). Należy uważać, aby nie mieszać filmów z różnych klatek na sekundę lub z różną liczbą ścieżek audio.

>[!NOTE]
>Obecny projekt funkcji łączenia oczekuje, że wejściowe klipy wideo są spójne pod względem rozdzielczości, liczby klatek na sekundę itp. 

### <a name="requirements-and-considerations"></a>Wymagania i uwagi

* Filmy wejściowe powinny mieć tylko jedną ścieżkę audio.
* Filmy wejściowe powinny mieć taką samą liczbę klatek na sekundę.
* Musisz przesłać swoje filmy do oddzielnych zasobów i ustawić filmy jako plik podstawowy w każdym zasobie.
* Musisz znać czas trwania swoich filmów.
* Wstępnie ustawione przykłady poniżej zakładają, że wszystkie wejściowe filmy wideo zaczynają się od sygnatury czasowej zero. Należy zmodyfikować wartości StartTime, jeśli filmy mają inny sygnatura czasowa uruchamiania, jak to zwykle ma miejsce w przypadku archiwów na żywo.
* Predefiniowane ustawienie JSON zawiera jawne odwołania do wartości AssetID zasobów wejściowych.
* Przykładowy kod zakłada, że predefiniowane json zostało zapisane w pliku lokalnym, takim jak "C:\supportFiles\preset.json". Zakłada się również, że dwa zasoby zostały utworzone przez przesłanie dwóch plików wideo i że znasz wynikowe wartości AssetID.
* Fragment kodu i ustawienie predefiniowane JSON pokazuje przykład łączenia dwóch plików wideo. Możesz rozszerzyć go na więcej niż dwa filmy, aby:

  1. Zadanie wywoływania. InputAssets.Add() wielokrotnie, aby dodać więcej filmów w kolejności.
  2. Dokonywanie odpowiednich zmian do elementu "Źródła" w JSON, dodając więcej wpisów, w tej samej kolejności.

### <a name="net-code"></a>Kod .NET

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

### <a name="json-preset"></a>Ustawienia wstępne JSON

Zaktualizuj niestandardowe ustawienia wstępne za pomocą identyfikatorów zasobów, które chcesz łączyć, oraz z odpowiednim segmentem czasu dla każdego filmu.

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

## <a name="crop-videos-with-media-encoder-standard"></a><a id="crop"></a>Przycinanie wideo za pomocą usługi Media Encoder Standard
Zobacz temat [Klipy wideo z koderem multimedialnym standardem.](media-services-crop-video.md)

## <a name="insert-a-video-track-when-input-has-no-video"></a><a id="no_video"></a>Wstawianie ścieżki wideo, gdy wejście nie ma wideo

Domyślnie jeśli wysyłasz dane wejściowe do kodera, który zawiera tylko dźwięk i nie wideo, a następnie zasób wyjściowy zawiera pliki, które zawierają tylko dane audio. Niektórzy gracze, w tym Azure Media Player (zobacz [to)](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)może nie być w stanie obsłużyć takich strumieni. To ustawienie służy do wymuszania kodera, aby dodać monochromatyczny utwór wideo do danych wyjściowych w tym scenariuszu.

> [!NOTE]
> Wymuszanie kodera do wstawiania wyjściowej ścieżki wideo zwiększa rozmiar wyjściowego zasobu, a tym samym koszt poniesionych na zadanie kodowania. Należy przeprowadzić testy, aby sprawdzić, czy ten wynikowy wzrost ma tylko niewielki wpływ na miesięczne opłaty.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Wstawianie wideo przy najniższej szybkości transmisji bitów

Załóżmy, że używasz wielu ustawień kodowania szybkości transmisji bitów, takich jak ["H264 Multiple Bitrate 720p",](media-services-mes-preset-h264-multiple-bitrate-720p.md) aby zakodować cały katalog wejściowy do przesyłania strumieniowego, który zawiera kombinację plików wideo i plików tylko audio. W tym scenariuszu, gdy dane wejściowe nie ma wideo, można wymusić koder wstawić monochromatyczny ścieżkę wideo przy najniższej szybkości transmisji bitów, w przeciwieństwie do wstawiania wideo przy każdej szybkości transmisji bitów wyjściowej. Aby to osiągnąć, należy użyć **InsertBlackIfNoVideoBottomLayerOnly** flagi.

Można wykonać dowolne ustawienia mes udokumentowane w [tej](media-services-mes-presets-overview.md) sekcji i dokonać następującej modyfikacji:

#### <a name="json-preset"></a>Ustawienia wstępne JSON
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>Ustawienia predefiniowane XML

Korzystając z języka XML, należy użyć pliku Condition="InsertBlackIfNoVideoBottomLayerOnly" jako atrybutu elementu **H264Video** i Condition="InsertSilenceIfNoAudio" jako atrybutu **AACAudio**.

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

### <a name="inserting-video-at-all-output-bitrates"></a>Wstawianie wideo przy wszystkich szybkościach transmisji bitów wyjściowych
Załóżmy, że używasz wielu ustawień kodowania szybkości transmisji bitów, takich jak ["H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) do kodowania całego katalogu wejściowego do przesyłania strumieniowego, który zawiera kombinację plików wideo i plików tylko audio. W tym scenariuszu, gdy dane wejściowe nie ma wideo, można wymusić koder wstawić monochromatyczne ścieżki wideo na wszystkich szybkościach transmisji bitów wyjściowych. Dzięki temu zasoby wyjściowe są jednorodne pod względem liczby ścieżek wideo i ścieżek audio. Aby to osiągnąć, należy określić flagę "InsertBlackIfNoVideo".

Można wykonać dowolne ustawienia mes udokumentowane w [tej](media-services-mes-presets-overview.md) sekcji i dokonać następującej modyfikacji:

#### <a name="json-preset"></a>Ustawienia wstępne JSON
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>Ustawienia predefiniowane XML

Korzystając z języka XML, należy użyć pliku Condition="InsertBlackIfNoVideo" jako atrybutu elementu **H264Video** i condition="InsertSilenceIfNoAudio" jako atrybutu **AACAudio**.

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

## <a name="rotate-a-video"></a><a id="rotate_video"></a>Obracanie wideo
[Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) obsługuje obrót pod kątem 0/90/180/270. Domyślnym zachowaniem jest "Auto", gdzie próbuje wykryć metadane obrotu w przychodzącym pliku wideo i skompensować go. Dołącz następujący element **Źródła** do jednego z ustawień predefiniowanych zdefiniowanych w [tej](media-services-mes-presets-overview.md) sekcji:

### <a name="json-preset"></a>Ustawienia wstępne JSON
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
### <a name="xml-preset"></a>Ustawienia predefiniowane XML
    <Sources>
           <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

Zobacz także [ten](media-services-mes-schema.md#PreserveResolutionAfterRotation) temat, aby uzyskać więcej informacji na temat interpretacji ustawień Szerokość i Wysokość w ustawieniach predefiniowanych, gdy zostanie wyzwolona kompensacja obrotu.

Można użyć wartości "0", aby wskazać koderowi, aby zignorować metadane obrotu, jeśli są obecne, w wejściowym filmie wideo.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Omówienie kodowania usług multimedialnych](media-services-encode-asset.md)
