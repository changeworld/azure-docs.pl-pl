---
title: Wiele plików wejściowych i właściwości składnika za pomocą kodera Premium - Azure | Dokumentacja firmy Microsoft
description: W tym temacie wyjaśniono, jak używać setRuntimeProperties korzystanie z wielu plików wejściowych i przekazywanie danych niestandardowych do przepływu pracy Premium kodera multimediów procesor multimediów.
services: media-services
documentationcenter: ''
author: xpouyat
manager: cfowler
editor: ''
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: xpouyat;anilmur;juliako
ms.openlocfilehash: 66aec76e5af399e1909446b8ddf7a79aa1384d52
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788900"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Używanie wielu plików wejściowych i właściwości składnika za pomocą kodera — wersja Premium
## <a name="overview"></a>Przegląd
Istnieją scenariusze, w których może być konieczne dostosowanie właściwości składnika, określ zawartość XML listy klip lub wysłać wielu plików wejściowych podczas przesyłania zadania z **Media Encoder Premium w przepływie pracy** procesor multimediów. Przykłady to:

* Zastępując tekst na wideo i ustawianie wartości tekstowej (na przykład data bieżąca) w czasie wykonywania dla każdego wejściowego wideo.
* Dostosowywanie XML listy klip (Aby określić jedną lub kilka plików źródłowych z lub bez przycinania, itp.).
* Gdy wideo jest zaszyfrowana, zastępując obraz logo na wejściowego pliku wideo.
* Wiele kodowanie języka audio.

Aby umożliwić **Media Encoder Premium w przepływie pracy** wiedzieć, że niektóre właściwości w przepływie pracy jest zmieniany podczas tworzenia zadania lub wysyłania wielu plików wejściowych, należy użyć ciągu konfiguracji, który zawiera  **setRuntimeProperties** i/lub **transcodeSource**. W tym temacie opisano sposób korzystania z nich.

## <a name="configuration-string-syntax"></a>Składnia ciągu konfiguracji
Ciąg konfiguracji można ustawić w zadaniu kodowania używa dokument XML, który wygląda następująco:

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

Oto kod C#, który odczytuje plik XML konfiguracji z pliku, go zaktualizować z użyciem nazwy pliku prawo wideo i przekazuje je do zadań w ramach zadania:

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

## <a name="customizing-component-properties"></a>Dostosowywanie właściwości składnika
### <a name="property-with-a-simple-value"></a>Właściwość z wartością proste
W niektórych przypadkach warto dostosować właściwości składnika wraz z pliku przepływu pracy, który ma być wykonywane przez przepływ pracy Premium kodera multimediów.

Załóżmy, że przeznaczona tekst nakładki przepływu pracy na pliki wideo i tekst (na przykład data bieżąca) powinien można ustawić w czasie wykonywania. Można to zrobić, wysyłając tekst, który ma być ustawiona jako nową wartość właściwości text składnika nakładkę z zadania kodowania. Mechanizm ten umożliwia zmienić inne właściwości składnika w przepływie pracy (na przykład położenia lub kolor nakładki, szybkości transmisji bitów kodera AVC itp.).

**setRuntimeProperties** służy do zastępowania właściwość składniki przepływu pracy.

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

### <a name="property-with-an-xml-value"></a>Właściwość wartości XML
Aby ustawić właściwości, która oczekuje wartości XML, Hermetyzowanie przy użyciu `<![CDATA[ and ]]>`.

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
> Upewnij się, że nie można umieścić powrotu karetki tuż po `<![CDATA[`.

### <a name="propertypath-value"></a>wartości propertyPath
W poprzednich przykładach, została propertyPath "/ Media pliku danych wejściowych/filename" lub "/ inactiveTimeout" lub "clipListXml".
To jest ogólnie rzecz biorąc, nazwy składnika, a następnie nazwę właściwości. Ścieżka może mieć więcej lub mniej poziomy, tak samo, jak "/ primarySourceFile" (ponieważ jest właściwość w katalogu głównym przepływu pracy) lub "/ wideo przetwarzania/grafiki nakładki/nieprzezroczystość" (ponieważ nakładki znajduje się w grupie).    

Aby sprawdzić ścieżki i nazwy właściwości, użyj przycisku akcji, który jest od razu obok każdej właściwości. Można kliknąć ten przycisk, akcji i wybierz **Edytuj**. Spowoduje to wyświetlenie rzeczywistą nazwą właściwości oraz od razu nad nim przestrzeni nazw.

![Akcja i edytowanie](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Właściwość](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Wiele plików wejściowych
Każdego zadania, które można przesłać do **Media Encoder Premium w przepływie pracy** wymaga dwóch zasobów:

* Pierwsza z nich jest *zasobów przepływu pracy* zawierający plik przepływu pracy. Pliki przepływu pracy można zaprojektować przy użyciu [projektanta przepływów pracy](media-services-workflow-designer.md).
* Drugim jest *multimedialnej* zawierający pliki nośnika, który chcesz kodować.

Gdy wysyłasz wiele plików multimedialnych **Media Encoder Premium w przepływie pracy** kodera, obowiązują następujące ograniczenia:

* Wszystkie pliki multimedialne musi być w tej samej *multimedialnej*. Używanie wielu zasobów nośnika nie jest obsługiwane.
* Plik podstawowy musi być ustawiona w tej zawartości nośnika (najlepiej, jeśli jest to główny plik wideo, który musi przetworzyć koder).
* Należy przekazać dane konfiguracji, który zawiera **setRuntimeProperties** i/lub **transcodeSource** element do procesora.
  * **setRuntimeProperties** służy do zastępowania właściwość filename lub inna właściwość w części przepływu pracy.
  * **transcodeSource** służy do określania zawartości XML listy obiektów.

Połączenia w przepływie pracy:

* Możesz użyć jednego lub kilku składników danych wejściowych plików nośnika i planowane jest użycie **setRuntimeProperties** Aby określić nazwę pliku, następnie nie numer pin plik podstawowy składnik się z nimi łączyć. Upewnij się, że istnieje połączenie między obiektem plik podstawowy i Input(s) pliku nośnika.
* Jeśli wolisz korzystać XML listy obiektów i jeden składnik źródło nośnika, a następnie mogą jednocześnie połączyć ze sobą.

![Żadne połączenie z podstawowym pliku źródłowym wejście pliku multimediów](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Brak połączenia z podstawowego pliku do składników danych wejściowych pliku nośnika, jeśli używasz setRuntimeProperties można ustawić właściwość filename.*

![Połączenie z listy klip XML należy przyciąć listy źródłowej](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Można nawiązać XML listy klip źródło nośnika i używać transcodeSource.*

### <a name="clip-list-xml-customization"></a>Przytnij dostosowania XML listy
Można określić XML listy klip w przepływie pracy w czasie wykonywania za pomocą **transcodeSource** w konfiguracji ciągu XML. Wymaga to XML listy klip numeru pin do podłączenia do składnika źródło nośnika w przepływie pracy.

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

Jeśli chcesz określić /primarySourceFile do tej właściwości należy użyć nazwy plików wyjściowych za pomocą "Wyrażeń", a następnie zalecamy przekazanie XML listy klip jako właściwość *po* właściwości /primarySourceFile, aby uniknąć klip Lista zostać zastąpione przez ustawienie /primarySourceFile.

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

Z dodatkowych przycinanie dokładne ramki:

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

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Przykład 1: Nakładki obrazów na górze wideo

### <a name="presentation"></a>Prezentacja
Rozważmy przykład, w którym chcesz nałożyć obraz logo na wejściowego pliku wideo, gdy wideo jest zakodowany. W tym przykładzie wejściowy plik wideo ma nazwę "Microsoft_HoloLens_Possibilities_816p24.mp4" i logo o nazwie "logo.png". Należy wykonać następujące czynności:

* Utwórz zasób przepływu pracy z plikiem przepływu pracy (zobacz poniższy przykład).
* Utwórz zasób nośnika, który zawiera dwa pliki: MyInputVideo.mp4 jako plik podstawowy i MyLogo.png.
* Wysyłanie zadania do przepływu pracy Premium kodera multimediów procesor multimediów z powyższych zasobów wejściowych i określić następujący ciąg konfiguracji.

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

W powyższym przykładzie nazwa pliku wideo są wysyłane do składników danych wejściowych pliku nośnika, a właściwość primarySourceFile. Nazwa pliku logo są wysyłane do innego nośnika plik danych wejściowych jest podłączony do składnika graficzne nakładki.

> [!NOTE]
> Nazwa pliku wideo są wysyłane do właściwości primarySourceFile. Przyczyną tego jest tej właściwości należy użyć w przepływie pracy umożliwiające tworzenie za pomocą wyrażenia, na przykład nazwa pliku poprawne dane wyjściowe.

### <a name="step-by-step-workflow-creation"></a>Tworzenie krok przepływu pracy
Poniżej przedstawiono kroki, aby utworzyć przepływ pracy, który przyjmuje dwa pliki jako dane wejściowe: i obrazu wideo. Będzie ona nakładki obrazu na górze wideo.

Otwórz **projektanta przepływów pracy** i wybierz **pliku** > **nowy obszar roboczy** > **planu Transkodowanie**.

Nowy przepływ pracy zawiera trzy elementy:

* Podstawowym pliku źródłowym
* Obcina listę XML
* Zawartości pliku wyjściowej  

![Nowy przepływ pracy kodowania](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nowy przepływ pracy kodowania*

Aby zaakceptować pliku wejściowego nośnika, Rozpocznij od dodania składnika nośnika pliku wejściowego. Aby dodać składnik do przepływu pracy, poszukaj jej w polu wyszukiwania w repozytorium i przeciągnij żądaną pozycję okienku projektanta.

Następnie dodaj plik wideo do zastosowania w przypadku projektowania przepływu pracy. Aby to zrobić, kliknij w okienku tła w Projektancie przepływów pracy i poszukaj właściwości w okienku po prawej stronie właściwości w podstawowym pliku źródłowym. Kliknij ikonę folderu i wybierz odpowiedniego pliku wideo.

![Źródłowy plik podstawowy](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Źródłowy plik podstawowy*

Następnie określ plik wideo w składniku wejściowy plik nośnika.   

![Źródło danych wejściowych plików multimedialnych](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Źródło danych wejściowych plików multimedialnych*

Natychmiast po zakończeniu składnika danych wejściowych plików nośnika sprawdź plik i wypełnić jego końcówek wyjściowych, aby odzwierciedlić pliku, który go inspekcji.

Następnym krokiem jest można dodać "wideo danych typu Updater" Aby określić przestrzeń kolorów na Rec.709. Dodaj "Wideo Format konwertera" ustawioną na układ układ danych typu = planarnych można konfigurować. Spowoduje to przekonwertowanie strumienia wideo do formatu, który można podjąć jako źródło składnika nakładki.

![wideo Updater typu danych i Format konwertera](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*wideo Updater typu danych i Format konwertera*

![Typ układu = planarnych można konfigurować](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Typ układu jest konfigurowalne planarnych*

Następnie dodaj składnik nakładka wideo i połączyć (nieskompresowanych) wideo numeru pin (nieskompresowanych) wideo wejściowego pliku nośnika.

Dodaj inny nośnik plik danych wejściowych (można załadować pliku logo), kliknij ten składnik i zmień jego nazwę na "Logo dane wejściowe w pliku nośnika", a następnie wybierz obraz (plik PNG na przykład) właściwości pliku. Połączyć pin nieskompresowanych obrazu nieskompresowanych obrazu nakładki.

![Nakładki źródła pliku składnika i obrazów](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Nakładki źródła pliku składnika i obrazów*

Jeśli chcesz zmodyfikować pozycji logo na wideo (na przykład możesz chcieć umieść go na 10 procent wylogowuje na lewym górnym rogu wideo), usuń zaznaczenie pola wyboru "Ręcznego wprowadzania". Można to zrobić, ponieważ pozwala udostępnić plik logo do składnika nakładki używasz wejściowy plik nośnika.

![Pozycja nakładki](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Pozycja nakładki*

Do kodowania strumienia wideo H.264, należy dodać składniki kodera koder wideo AVC i AAC powierzchnię projektanta. Połącz numerów PIN.
Ustaw kodera AAC i wybierz Format Audio konwersji/ustawienie: 2.0 (L, R).

![Kodery audio i wideo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Kodery audio i wideo*

Teraz Dodaj **ISO Mpeg-4 multiplekser** i **dane wyjściowe pliku** składników i podłącz numery PIN, jak pokazano.

![MP4 multiplekser i danych wyjściowych w pliku](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplekser i danych wyjściowych w pliku*

Należy określić nazwę dla pliku wyjściowego. Kliknij przycisk **dane wyjściowe pliku** składnika i edycji wyrażenia dla pliku:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Nazwa pliku wyjściowego](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nazwa pliku wyjściowego*

Można uruchomić przepływu pracy lokalnie w celu sprawdzenia, czy działa prawidłowo.

Po jej zakończeniu, można go uruchomić w usłudze Azure Media Services.

Najpierw przygotować zasobów w usłudze Azure Media Services z dwoma plikami w nim: plik wideo i logo. Można to zrobić przy użyciu platformy .NET lub interfejsu API REST. Można to także zrobić przy użyciu portalu Azure lub [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Ten samouczek pokazuje, jak zarządzać zasobami za pomocą AMSE. Istnieją dwa sposoby dodawania plików do zawartości:

* Utwórz folder lokalny, skopiuj dwa pliki i przeciągnij i upuść folder **zasobów** kartę.
* Przekazywanie pliku wideo jako zasób, wyświetlane informacje o zasobie, przejdź do karty pliki i przekazać plik dodatkowy (logo).

> [!NOTE]
> Upewnij się ustawić plik podstawowy w zasobów (główny plik wideo).

![Pliki zasobów w AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Pliki zasobów w AMSE*

Wybierz element zawartości, a następnie wybierz kodować je za pomocą kodera Premium. Przekaż przepływu pracy i zaznacz go.

Kliknij przycisk, aby przekazać dane do procesora, a następnie dodaj następujący kod XML można ustawić właściwości środowisko uruchomieniowe:

![Koder Premium w AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Koder Premium w AMSE*

Następnie wklej poniższe dane XML. Należy określić nazwę pliku wideo wejście pliku multimediów i primarySourceFile. Określ nazwę pliku dla logo zbyt.

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

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*

Użycie zestawu .NET SDK do tworzenia i uruchamiania zadania, dane XML musi być przekazywane jako parametry konfiguracji.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

Po zakończeniu zadania plik MP4 elementu zawartości wyjściowej Wyświetla nakładki!

![Nakładka wideo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Nakładka wideo*

Możesz pobrać przykładowym przepływie pracy z [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Przykład 2: Wiele audio kodowanie

Przykładem wiele wersji językowych audio kodowania workfkow jest dostępna w [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Ten folder zawiera przykładowy przepływ pracy, który może służyć do kodowania pliku MXF do wielu zasobów plików MP4 z wielu ścieżek audio.

Ten przepływ pracy zakłada, że plik MXF zawiera jedną ścieżkę audio; dodatkowe ścieżki audio powinien zostać przekazany jako osobne pliki audio (WAV lub MP4...).

Do kodowania, wykonaj następujące czynności:

* Utwórz zasób usługi Media Services z pliku MXF i plików Audio (pliki audio 0-18).
* Upewnij się, że plik MXF jest ustawiony jako plik podstawowy.
* Utwórz zadania i zadania przy użyciu procesora koder Premium przepływu pracy. Użyj podane przepływu pracy (MultiMP4-1080p-19audio-v1.workflow).
* Przekazywanie danych setruntime.xml zadania (Jeśli używasz Azure Media Services Explorer, kliknij przycisk "przekazywania danych xml do przepływu pracy").
  * Zaktualizuj danych XML do określenia tagów prawidłowej nazwy i języków.
  * Przepływ pracy ma audio składniki o nazwie Audio 1-Audio 18.
  * RFC5646 jest obsługiwany przez język znaczników.

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

* Tych ścieżek należy wybieranych w programie Azure Media Player i zakodowanym elementem zawartości będzie zawierać ścieżek audio multi języka.

## <a name="see-also"></a>Zobacz także
* [Wprowadzenie do kodowania w Azure Media Services w wersji Premium](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Jak używać Premium kodowania w usłudze Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Kodowanie zawartości na żądanie za pomocą usługi Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Koderów-dekoderów i formaty Media Encoder Premium w przepływie pracy](media-services-premium-workflow-encoder-formats.md)
* [Przykładowe pliki przepływu pracy](https://github.com/Azure/azure-media-services-samples)
* [Narzędzia usługi Azure Media Services Explorer](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
