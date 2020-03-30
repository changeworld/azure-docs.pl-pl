---
title: Wiele plików wejściowych i właściwości składników z koderem Premium — Azure | Dokumenty firmy Microsoft
description: W tym temacie wyjaśniono, jak używać właściwości setRuntimeProperties do używania wielu plików wejściowych i przekazywania danych niestandardowych do procesora nośników Media Encoder Premium Workflow.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: xpouyat
ms.reviewer: anilmur;juliako
ms.openlocfilehash: 27bdf82d4515678e28eadf07fe325860fe5df063
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251000"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Używanie wielu plików wejściowych i właściwości składników za pomocą kodera Premium
## <a name="overview"></a>Omówienie
Istnieją scenariusze, w których może być konieczne dostosowanie właściwości składnika, określenie zawartości XML listy klipów lub wysłanie wielu plików wejściowych podczas przesyłania zadania za pomocą procesora **nośników Media Encoder Premium Workflow.** Przykłady to:

* Nakładanie tekstu na wideo i ustawianie wartości tekstowej (na przykład bieżącej daty) w czasie wykonywania dla każdego wejściowego wideo.
* Dostosowywanie pliku XML listy klipów (w celu określenia jednego lub kilku plików źródłowych, z przycinaniem lub bez niego itp.).
* Nakładanie obrazu logo na wejściowy film wideo podczas kodowania wideo.
* Wiele kodowania języka audio.

Aby poinformować **przepływ pracy media encoder Premium,** że podczas tworzenia zadania lub wysyłania wielu plików wejściowych zmieniane są niektóre właściwości w przepływie pracy, należy użyć ciągu konfiguracji zawierającego **zestawRuntimeProperties** i/lub **transcodeSource**. W tym temacie wyjaśniono, jak z nich korzystać.

## <a name="configuration-string-syntax"></a>Składnia ciągu konfiguracji
Ciąg konfiguracji ustawiony w zadaniu kodowania używa dokumentu XML, który wygląda następująco:

```xml
<?xml version="1.0" encoding="utf-8"?>
<transcodeRequest>
  <transcodeSource>
  </transcodeSource>
  <setRuntimeProperties>
    <property propertyPath="Media File Input/filename" value="VideoFileName.mp4" />
  </setRuntimeProperties>
</transcodeRequest>
```

Poniżej znajduje się kod Języka C#, który odczytuje konfigurację XML z pliku, aktualizuj go za pomocą odpowiedniej nazwy pliku wideo i przekazuje go do zadania w zadaniu:

```csharp
string premiumConfiguration = ReadAllText(@"D:\home\site\wwwroot\Presets\SetRuntime.xml").Replace("VideoFileName", myVideoFileName);

// Declare a new job.
IJob job = _context.Jobs.Create("Premium Workflow encoding job");

// Get a media processor reference, and pass to it the name of the 
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

// Create a task with the encoding details, using a string preset.
ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                              processor,
                              premiumConfiguration,
                              TaskOptions.None);

// Specify the input assets
task.InputAssets.Add(workflow); // workflow asset
task.InputAssets.Add(video); // video asset with multiple files

// Add an output asset to contain the results of the job. 
// This output is specified as AssetCreationOptions.None, which 
// means the output asset is not encrypted. 
task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
```

## <a name="customizing-component-properties"></a>Dostosowywanie właściwości komponentu
### <a name="property-with-a-simple-value"></a>Właściwość o prostej wartości
W niektórych przypadkach warto dostosować właściwość składnika wraz z plikiem przepływu pracy, który ma być wykonywany przez przepływ pracy usługi Media Encoder Premium.

Załóżmy, że zaprojektowałeś przepływ pracy, który nakłada tekst na filmy, a tekst (na przykład bieżąca data) ma być ustawiony w czasie wykonywania. Można to zrobić, wysyłając tekst, który ma być ustawiony jako nowa wartość właściwości tekstowej składnika nakładki z zadania kodowania. Za pomocą tego mechanizmu można zmienić inne właściwości składnika w przepływie pracy (takie jak położenie lub kolor nakładki, szybkość transmisji bitów kodera AVC itp.).

**setRuntimeProperties** służy do zastępowania właściwości w składnikach przepływu pracy.

Przykład:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
      <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
  </setRuntimeProperties>
</transcodeRequest>
```

### <a name="property-with-an-xml-value"></a>Właściwość z wartością XML
Aby ustawić właściwość, która oczekuje wartości XML, hermetyzuj za pomocą programu `<![CDATA[ and ]]>`.

Przykład:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

> [!NOTE]
> Upewnij się, że nie należy `<![CDATA[`umieszczać powrotu karetki tuż po .

### <a name="propertypath-value"></a>wartość propertyPath
W poprzednich przykładach propertyPath był "/Media File Input/filename" lub "/inactiveTimeout" lub "clipListXml".
Jest to, ogólnie rzecz biorąc, nazwa składnika, a następnie nazwa właściwości. Ścieżka może mieć więcej lub mniej poziomów, takich jak "/primarySourceFile" (ponieważ właściwość znajduje się w katalogu głównym przepływu pracy) lub "/Video Processing/Graphic Overlay/Opacity" (ponieważ nakładka znajduje się w grupie).    

Aby sprawdzić nazwę ścieżki i właściwości, użyj przycisku akcji, który znajduje się bezpośrednio obok każdej właściwości. Możesz kliknąć ten przycisk akcji i wybrać pozycję **Edytuj**. Spowoduje to wyświetlenie rzeczywistej nazwy właściwości i bezpośrednio nad nią obszaru nazw.

![Akcja/edycja](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Właściwość](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Wiele plików wejściowych
Każde zadanie przesłane do **przepływu pracy usługi Media Encoder Premium** wymaga dwóch zasobów:

* Pierwszym z nich jest *zasób przepływu pracy,* który zawiera plik przepływu pracy. Pliki przepływu pracy można projektować za pomocą [projektanta przepływu pracy](media-services-workflow-designer.md).
* Drugi to *zasób multimedialny* zawierający pliki multimedialne, które chcesz zakodować.

Podczas wysyłania wielu plików multimedialnych do kodera **przepływu pracy usługi Media Encoder Premium** obowiązują następujące ograniczenia:

* Wszystkie pliki multimedialne muszą znajdować się w tym samym *zasobie multimedialnym*. Korzystanie z wielu zasobów multimedialnych nie jest obsługiwane.
* W tym zasób multimedialny należy ustawić plik podstawowy (najlepiej jest to główny plik wideo, który koder jest proszony o przetworzenie).
* Konieczne jest przekazanie danych konfiguracyjnych, które zawierają **setRuntimeProperties** i/lub **transcodeSource** element do procesora.
  * **setRuntimeProperties** służy do zastępowania właściwości filename lub innej właściwości w składnikach przepływu pracy.
  * **transcodeSource** służy do określania zawartości XML listy klipów.

Połączenia w przepływie pracy:

* Jeśli używasz jednego lub kilku składników wprowadzania plików multimedialnych i planujesz użyć **setRuntimeProperties** do określenia nazwy pliku, nie należy łączyć z nimi numeru pin składnika pliku podstawowego. Upewnij się, że nie ma połączenia między obiektem pliku podstawowego a wejściami pliku multimedialnego.
* Jeśli wolisz używać pliku XML z listą klipów i jednego składnika Źródło multimediów, możesz połączyć oba elementy ze sobą.

![Brak połączenia z pliku źródłowego podstawowego z wejściem pliku multimedialnego](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Nie ma połączenia z pliku podstawowego ze składnikami wprowadzania plików multimedialnych, jeśli do ustawienia właściwości nazwy pliku używasz właściwości setRuntimeProperties.*

![Połączenie ze źródła XML listy klipów do listy klipów](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Można podłączyć kod XML listy klipów do źródła multimediów i użyć usługi transcodeSource.*

### <a name="clip-list-xml-customization"></a>Dostosowywanie XML listy klipów
Można określić kod XML listy klipów w przepływie pracy w czasie wykonywania przy użyciu **transcodeSource** w ciągu konfiguracji XML. Wymaga to połączenia kodu XML listy klipów ze składnikiem Źródło multimediów w przepływie pracy.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <transcodeSource>
      <clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
      </clipList>
    </transcodeSource>
    <setRuntimeProperties>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Jeśli chcesz określić /primarySourceFile użyć tej właściwości do nazwy plików wyjściowych przy użyciu "Wyrażenia", a następnie zaleca się przekazywanie Clip List XML jako właściwość *po* /primarySourceFile właściwości, aby uniknąć konieczności Clip List zostać zastąpione przez /primarySourceFile ustawienie.

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Z dodatkowym przycinaniem z dokładnością do ramki:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Przykład 1: Nałóż obraz na wierzch filmu

### <a name="presentation"></a>Prezentacja
Rozważmy przykład, w którym chcesz nałożyć obraz logo na wejściowy film wideo podczas kodowania wideo. W tym przykładzie wejściowy film o nazwie "Microsoft_HoloLens_Possibilities_816p24.mp4", a logo nosi nazwę "logo.png". Należy wykonać następujące kroki:

* Utwórz zasób przepływu pracy za pomocą pliku przepływu pracy (zobacz poniższy przykład).
* Utwórz zasób multimedialny, który zawiera dwa pliki: MyInputVideo.mp4 jako plik podstawowy i MyLogo.png.
* Wyślij zadanie do procesora nośników Media Encoder Premium Workflow z powyższymi zasobami wejściowymi i określ następujący ciąg konfiguracji.

Konfiguracja:

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

W powyższym przykładzie nazwa pliku wideo jest wysyłana do składnika Wprowadzanie pliku multimedialnego i właściwości primarySourceFile. Nazwa pliku logo jest wysyłana do innego wejścia pliku multimedialnego, który jest połączony ze składnikiem nakładki graficznej.

> [!NOTE]
> Nazwa pliku wideo jest wysyłana do właściwości primarySourceFile. Powodem tego jest użycie tej właściwości w przepływie pracy do tworzenia poprawnej nazwy pliku wyjściowego przy użyciu wyrażeń, na przykład.

### <a name="step-by-step-workflow-creation"></a>Tworzenie przepływu pracy krok po kroku
Poniżej przedstawiono kroki tworzenia przepływu pracy, który przyjmuje dwa pliki jako dane wejściowe: wideo i obraz. Nałoży obraz na górę filmu.

Otwórz **Projektanta przepływu pracy** i wybierz pozycję Plan**transkodowania****nowego obszaru roboczego** >  **.** > 

Nowy przepływ pracy zawiera trzy elementy:

* Podstawowy plik źródłowy
* Kod XML listy klipów
* Plik wyjściowy/zasób  

![Nowy przepływ pracy kodowania](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nowy przepływ pracy kodowania*

Aby zaakceptować wejściowy plik multimedialny, zacznij od dodania składnika Wprowadzanie pliku multimedialnego. Aby dodać składnik do przepływu pracy, poszukaj go w polu wyszukiwania Repozytorium i przeciągnij żądany wpis do okienka projektanta.

Następnie dodaj plik wideo, który ma być używany do projektowania przepływu pracy. Aby to zrobić, kliknij okienko tła w Projektancie przepływu pracy i poszukaj właściwości Podstawowy plik źródłowy w okienku właściwości po prawej stronie. Kliknij ikonę folderu i wybierz odpowiedni plik wideo.

![Podstawowe źródło pliku](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Podstawowe źródło pliku*

Następnie określ plik wideo w składniku Wprowadzanie pliku multimedialnego.   

![Źródło wprowadzania pliku multimedialnego](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Źródło wprowadzania pliku multimedialnego*

Jak to nastąpi, składnik Wprowadzanie pliku multimedialnego sprawdzi plik i wypełni jego piny wyjściowe, aby odzwierciedlić plik, który został sprawdzony.

Następnym krokiem jest dodanie "Video Data Type Updater", aby określić przestrzeń kolorów do Rec.709. Dodaj "Konwerter formatów wideo", który jest ustawiony na Układ danych / typ układu = Konfigurowalny planar. Spowoduje to konwersję strumienia wideo na format, który może być traktowany jako źródło składnika nakładki.

![Video Data Type Updater i Format Converter](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Konwerter typów danych wideo*

![Typ układu = Konfigurowalna planar](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Typ układu to Konfigurowalna planar*

Następnie dodaj komponent Nakładka wideo i podłącz (nieskompresowany) pin wideo do (nieskompresowanego) pinu wideo wejściowego pliku multimedialnego.

Dodaj kolejne wejście pliku multimedialnego (aby załadować plik logo), kliknij ten składnik i zmień jego nazwę na "Media File Input Logo" i wybierz obraz (na przykład plik png) we właściwości pliku. Podłącz sworzeń obrazu Uncompressed do nieskompresowanego sworznia obrazu nakładki.

![Nakładki składnik i źródło pliku obrazu](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Nakładki składnik i źródło pliku obrazu*

Jeśli chcesz zmodyfikować położenie logo na filmie (na przykład możesz ustawić je w 10 procentach od lewego górnego rogu filmu), wyczyść pole wyboru "Wejście ręczne". Można to zrobić, ponieważ używasz wprowadzania pliku multimedialnego, aby dostarczyć plik logo do składnika nakładki.

![Położenie nakładki](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Położenie nakładki*

Aby zakodować strumień wideo do formatu H.264, dodaj do powierzchni projektanta składniki kodera wideo AVC i kodera AAC. Podłącz szpilki.
Skonfiguruj koder AAC i wybierz opcję Konwersja/Ustawienie predefiniowane formatu audio: 2.0 (L, R).

![Kodery audio i wideo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Kodery audio i wideo*

Teraz dodaj komponenty **multipleksera ISO Mpeg-4** i **wyjścia plików** i podłącz piny, jak pokazano na rysunku.

![Multiplekser MP4 i wyjście plików](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*Multiplekser MP4 i wyjście plików*

Należy ustawić nazwę pliku wyjściowego. Kliknij składnik **Dane wyjściowe pliku** i edytuj wyrażenie dla pliku:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Nazwa wyjściowa pliku](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nazwa wyjściowa pliku*

Przepływ pracy można uruchomić lokalnie, aby sprawdzić, czy działa poprawnie.

Po zakończeniu można go uruchomić w usłudze Azure Media Services.

Najpierw przygotuj zasób w usłudze Azure Media Services z dwoma plikami: plikiem wideo i logo. Można to zrobić za pomocą interfejsu API .NET lub REST. Można to również zrobić przy użyciu witryny Azure portal lub [Usługi Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

W tym samouczku pokazano, jak zarządzać zasobami za pomocą usługi AMSE. Istnieją dwa sposoby dodawania plików do zasobu:

* Utwórz folder lokalny, skopiuj dwa w nim pliki oraz przeciągnij i upuść folder na kartę **Zasób.**
* Przekaż plik wideo jako zasób, wyświetl informacje o zasobie, przejdź do karty pliki i przekaż dodatkowy plik (logo).

> [!NOTE]
> Upewnij się, że w zasobie (głównym pliku wideo) ustawisz plik podstawowy.

![Pliki zasobów w amse](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Pliki zasobów w amse*

Wybierz zasób i wybierz kodowanie go koderem Premium. Przekaż przepływ pracy i wybierz go.

Kliknij przycisk, aby przekazać dane do procesora, a następnie dodaj następujący kod XML, aby ustawić właściwości środowiska wykonawczego:

![Koder Premium w AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Koder Premium w AMSE*

Następnie wklej następujące dane XML. Należy określić nazwę pliku wideo zarówno dla wejścia pliku multimedialnego, jak i pliku primarySourceFile. Określ również nazwę nazwy pliku dla logo.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

![setRuntimeWłaściwości](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeWłaściwości*

Jeśli używasz .NET SDK do tworzenia i uruchamiania zadania, te dane XML muszą być przekazywane jako ciąg konfiguracji.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

Po zakończeniu zadania plik MP4 w zasób wyjściowy wyświetla nakładkę!

![Nakładka na film](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Nakładka na film*

Przykładowy przepływ pracy można pobrać z [usługi GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Przykład 2 : Wielokrotne kodowanie języka audio

Przykład wielu przepływów pracy kodowania języka audio jest dostępny w [usłudze GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Ten folder zawiera przykładowy przepływ pracy, który może służyć do kodowania pliku MXF do zasobu wielu plików MP4 z wieloma ścieżkami audio.

Ten przepływ pracy zakłada, że plik MXF zawiera jedną ścieżkę audio; dodatkowe ścieżki audio powinny być przekazywane jako oddzielne pliki audio (WAV lub MP4...).

Aby zakodować, wykonaj następujące czynności:

* Utwórz zasób usługi Media Services za pomocą pliku MXF i plików Audio (od 0 do 18 plików audio).
* Upewnij się, że plik MXF jest ustawiony jako plik podstawowy.
* Utwórz zadanie i zadanie przy użyciu procesora Kodera przepływu pracy premium. Użyj dostarczonego przepływu pracy (Przepływ pracy MultiMP4-1080p-19audio-v1.work).
* Przekaż dane setruntime.xml do zadania (jeśli używasz Usługi Azure Media Services Explorer, użyj przycisku "przekaż dane xml do przepływu pracy").
  * Zaktualizuj dane XML, aby określić poprawne nazwy plików i tagi języków.
  * Przepływ pracy ma składniki audio o nazwie Audio 1 do Audio 18.
  * RFC5646 jest obsługiwany dla tagu języka.

```xml
<?xml version="1.0" encoding="utf-16"?>
<transcodeRequest>
  <setRuntimeProperties>
    <property propertyPath="Media File Input Video/filename" value="MainVideo.mxf" />
    <property propertyPath="Language/language_code" value="en" />
    <property propertyPath="/primarySourceFile" value="MainVideo.mxf" />
    <property propertyPath="Audio 1/Media File Input/filename" value="french-audio.wav" />
    <property propertyPath="Audio 1/Language/language_code" value="fr" />
    <property propertyPath="Audio 2/Media File Input/filename" value="german-audio.wav" />
    <property propertyPath="Audio 2/Language/language_code" value="de" />
    <property propertyPath="Audio 3/Media File Input/filename" value="japanese-audio.wav" />
    <property propertyPath="Audio 3/Language/language_code" value="ja" />
  </setRuntimeProperties>
</transcodeRequest>
```

* Zakodowany zasób będzie zawierał wiele ścieżek audio w wielu językach, a te ścieżki powinny być wybierane w usłudze Azure Media Player.

## <a name="see-also"></a>Zobacz też
* [Wprowadzenie do kodowania premium w usłudze Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Jak korzystać z kodowania premium w usłudze Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Kodowanie zawartości na żądanie za pomocą usługi Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Formaty i kodeki usługi Media Encoder Premium](media-services-premium-workflow-encoder-formats.md)
* [Przykładowe pliki przepływu pracy](https://github.com/Azure/azure-media-services-samples)
* [Narzędzie Azure Media Services Explorer](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
