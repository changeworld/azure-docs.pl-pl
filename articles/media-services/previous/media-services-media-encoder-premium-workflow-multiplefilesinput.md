---
title: Wiele plików wejściowych i właściwości składnika z użyciem kodera w warstwie Premium — Azure | Dokumentacja firmy Microsoft
description: W tym temacie wyjaśniono, jak za pomocą setRuntimeProperties korzystanie z wielu plików wejściowych i przekazać niestandardowe dane do procesora media Media Encoder Premium Workflow.
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
ms.author: xpouyat;anilmur;juliako
ms.openlocfilehash: 608ca4bc3b58dd3c718d6239f90260154d2f6c3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465537"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Używanie wielu plików wejściowych i właściwości składnika z użyciem kodera w warstwie Premium
## <a name="overview"></a>Omówienie
Istnieją scenariusze, w których użytkownik może być konieczne dostosowanie właściwości składnika Określ klipu kod XML listy zawartości lub wysłać wiele plików wejściowych, gdy prześlesz zadanie z **Media Encoder Premium Workflow** procesor multimediów. Przykłady to:

* Nałożenie tekstu, wideo i ustawianie wartości tekstowej (na przykład bieżąca data) w czasie wykonywania dla każdego wejściowego pliku wideo.
* Dostosowywanie XML listy klipu (Aby określić jeden lub kilka plików źródłowych, z lub bez przycinania, itp.).
* Nałożenie obraz logo na wejściowego pliku wideo, gdy wideo jest kodowany.
* Wiele kodowanie językowych.

Aby umożliwić **Media Encoder Premium Workflow** wiedzieć, że zmieniasz niektórych właściwości w przepływie pracy, podczas tworzenia zadania, lub wysłać wiele plików wejściowych, należy użyć ciągu konfiguracji, który zawiera  **setRuntimeProperties** i/lub **transcodeSource**. W tym temacie wyjaśniono, jak z nich korzystać.

## <a name="configuration-string-syntax"></a>Składnia ciągu konfiguracji
Ciąg konfiguracji, aby ustawić zadania kodowania używa dokumentu XML, który wygląda w następujący sposób:

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

Poniżej przedstawiono C# kod, który odczytuje plik XML konfiguracji z pliku, zaktualizuj go przy użyciu pliku wideo bezpośrednio i przekazuje je do zadań w ramach zadania:

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
### <a name="property-with-a-simple-value"></a>Właściwość o wartości prostego
W niektórych przypadkach warto dostosować właściwości składnika, wraz z plik przepływu pracy, który ma być wykonywane przez przepływ pracy usługi Media Encoder w warstwie Premium.

Załóżmy, że został zaprojektowany przepływu pracy ten tekst nakładki na filmów wideo i tekstu (na przykład bieżąca data) powinien być ustawiona w czasie wykonywania. Można to zrobić, wysyłając tekst, który można ustawić jako nową wartość dla właściwości tekstu składnika nakładkę z zadania kodowania. Ten mechanizm umożliwia zmieniać jej inne właściwości składnika w przepływie pracy (na przykład położenie i kolor nakładki, szybkości transmisji bitów kodera AVC itp.).

**setRuntimeProperties** służy do zastępowania właściwości składniki przepływu pracy.

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
Aby ustawić właściwości, która oczekuje wartości XML, hermetyzacji przy użyciu `<![CDATA[ and ]]>`.

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
> Upewnij się, że nie umieścić karetki w zwracanym tuż za `<![CDATA[`.

### <a name="propertypath-value"></a>wartości propertyPath
W poprzednich przykładach był propertyPath "/ plików multimedialnych danych wejściowych/filename" lub "/ inactiveTimeout" lub "clipListXml".
Jest to ogólnie rzecz biorąc, nazwa składnika, a następnie nazwę właściwości. Ścieżka może mieć więcej lub mniej poziomy, takie jak "/ primarySourceFile" (ponieważ właściwość jest głównym przepływu pracy) lub "/ Video przetwarzania/grafiki nakładki/nieprzezroczystość" (ponieważ nakładki znajduje się w grupie).    

Aby sprawdzić ścieżki i nazwy właściwości, użyj przycisku akcji, który jest od razu, obok każdej właściwości. Można kliknąć ten przycisk akcji i wybrać **Edytuj**. Spowoduje to wyświetlenie rzeczywista nazwa właściwości, a bezpośrednio nad nim przestrzeni nazw.

![Akcja/Edytuj](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Właściwość](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Wiele plików wejściowych
Każde zadanie, która zostanie przesłana do **Media Encoder Premium Workflow** wymaga dwóch zasobów:

* Pierwsza z nich jest *zasobów przepływu pracy* zawierający plik przepływu pracy. Można zaprojektować pliki przepływu pracy przy użyciu [projektanta przepływów pracy](media-services-workflow-designer.md).
* Drugim jest *elementu zawartości multimediów* zawierający pliki nośników, który chcesz zakodować.

Gdy wysyłasz wiele plików multimedialnych do **Media Encoder Premium Workflow** obowiązują następujące ograniczenia encoder:

* Wszystkie pliki multimedialne musi być w tej samej *elementu zawartości multimediów*. Używanie wielu zasobów multimedialnych nie jest obsługiwane.
* Plik podstawowy musi być ustawiona w tym elementu zawartości multimediów (najlepiej, jeśli jest to główny plik wideo, który koder zostanie poproszony o przetwarzania).
* Należy przekazać dane konfiguracji, który zawiera **setRuntimeProperties** i/lub **transcodeSource** element do procesora.
  * **setRuntimeProperties** służy do zastępowania właściwość filename lub innego składniki przepływu pracy.
  * **transcodeSource** służy do określania zawartości XML listy klipu.

Połączenia w przepływie pracy:

* Możesz użyć jednego lub kilku składników nośników pliku wejściowego i planowane jest użycie **setRuntimeProperties** Aby określić nazwę pliku, następnie nie można nawiązać pin składnika plik podstawowy je. Upewnij się, że istnieje połączenie między obiektem plik podstawowy i Input(s) plik nośnika.
* Jeśli wolisz używać klipu listy XML i jeden składnik źródło nośnika, możesz podłączyć zarówno ze sobą.

![Brak połączenia z podstawowego pliku źródłowego nośnika pliku wejściowego](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Brak połączenia z podstawowego pliku do składników nośników pliku wejściowego, jeśli używasz setRuntimeProperties można ustawić właściwości nazwy pliku.*

![Połączenie z listy klipu XML, kiedy należy przyciąć źródło listy](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Można nawiązać XML listy klipu źródło nośnika i używać transcodeSource.*

### <a name="clip-list-xml-customization"></a>Przytnij dostosowania XML listy
Należy określić kod XML listy klipu w przepływie pracy w czasie wykonywania za pomocą **transcodeSource** w konfiguracji ciągu XML. Wymaga to numer pin klipu listy XML do podłączenia do składnika źródło nośnika w przepływie pracy.

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

Jeśli chcesz określić /primarySourceFile, aby używać tej właściwości na nazwy plików wyjściowych przy użyciu "Wyrażenia", a następnie zaleca się przekazanie XML listy klipu jako właściwość *po* właściwość /primarySourceFile pozwala uniknąć klipu Lista zostać zastąpione przez ustawienie /primarySourceFile.

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

Za pomocą dodatkowych przycinania dokładne ramki:

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

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Przykład 1: Nakładki obrazów na podstawie wideo

### <a name="presentation"></a>Prezentacja
Należy wziąć pod uwagę przykładowi, w którym chcesz nakładki obrazu logo na wejściowego pliku wideo, gdy wideo jest kodowany. W tym przykładzie wejściowy plik wideo ma nazwę "Microsoft_HoloLens_Possibilities_816p24.mp4" i logo nosi nazwę "logo.png". Należy wykonać następujące czynności:

* Utwórz zasób przepływu pracy z plikiem przepływu pracy (zobacz poniższy przykład).
* Tworzenie elementu zawartości multimediów, który zawiera dwa pliki: MyInputVideo.mp4 jako plik podstawowy i MyLogo.png.
* Wysyłanie zadania do Media Encoder Premium Workflow procesor multimediów za pomocą powyższych zasobów danych wejściowych i określ następujący ciąg konfiguracji.

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

W powyższym przykładzie nazwa pliku wideo są wysyłane do składnika Media pliku wejściowego i właściwość primarySourceFile. Nazwa pliku logo są wysyłane do innego nośnika pliku wejściowego, podłączonego do składnika grafiki nakładki.

> [!NOTE]
> Nazwa pliku wideo jest wysyłany do właściwości primarySourceFile. Przyczyną jest tej właściwości należy użyć przepływu pracy do tworzenia poprawną nazwę pliku wyjściowego przy użyciu wyrażeń, na przykład.

### <a name="step-by-step-workflow-creation"></a>Instrukcje krok po kroku przepływ został utworzony
Poniżej przedstawiono kroki, aby utworzyć przepływ pracy, który przyjmuje dwa pliki jako dane wejściowe: wideo i obraz. Będzie ona nakładki obrazu na podstawie wideo.

Otwórz **projektanta przepływów pracy** i wybierz **pliku** > **nowy obszar roboczy** > **planu Transkodowanie**.

Nowy przepływ pracy zawiera trzy elementy:

* Podstawowym pliku źródłowym
* Obcina listę XML
* Plik/elementu zawartości wyjściowej  

![Nowy przepływ pracy kodowania](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nowy przepływ pracy kodowania*

Aby zaakceptować pliku wejściowego nośnika, Rozpocznij od dodania składnika Media pliku wejściowego. Aby dodać składnik do przepływu pracy, poszukaj go w polu wyszukiwania w repozytorium i przeciągnij żądnego wpisu na okienka projektanta.

Następnie dodaj plik wideo ma być używany do projektowania przepływu pracy. Aby to zrobić, kliknij przycisk okienka projektanta przepływów pracy w tle i poszukaj właściwości pliku podstawowego źródła, w okienku po prawej stronie właściwości. Kliknij ikonę folderu, a następnie wybierz odpowiedni plik wideo.

![Źródłowy plik podstawowy](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Źródłowy plik podstawowy*

Następnie określ plik wideo w składniku Media pliku wejściowego.   

![Źródło danych wejściowych plików multimedialnych](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Źródło danych wejściowych plików multimedialnych*

Zaraz po zakończeniu tej operacji składnika Media pliku wejściowego Sprawdź plik i wypełnianie jej PinY wyjściowe, aby odzwierciedlić pliku który go inspekcji.

Następnym krokiem jest dodać "wideo danych typu Updater" Aby określić przestrzeń kolorów na Rec.709. Dodaj "Wideo w formacie konwertera" który jest ustawiony na układ układ danych typu = planarnych można konfigurować. Spowoduje to przekonwertowanie strumienia wideo do formatu, który mogą być podejmowane jako źródło składnika nakładki.

![Konwerter formatu i Updater typu danych wideo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Konwerter formatu i Updater typu danych wideo*

![Typ układu = konfigurowalne planarnych](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Typ układu jest konfigurowalne planarnych*

Następnie dodaj składnik wideo nakładki i połączyć (bez kompresji) wideo numeru pin (bez kompresji) wideo z pliku wejściowego nośnika.

Dodawanie innego nośnika pliku wejściowego (na przykład można załadować pliku logo), kliknij ten składnik, zmień jego nazwę na "Logo dane wejściowe w pliku nośnika" i wybierz pozycję obrazu (.png plik na przykład) we właściwości. Numer pin bez kompresji obrazu należy nawiązać numer pin bez kompresji obrazu nakładki.

![Źródło pliku składnika i obraz nakładki](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Źródło pliku składnika i obraz nakładki*

Jeśli chcesz zmodyfikować położenie logo film wideo (na przykład warto umieść go na 10 procent rabatu w lewym górnym rogu wideo), usuń zaznaczenie pola wyboru "Ręcznie Input". Można to zrobić, ponieważ korzysta z pliku wejściowego Media pozwala udostępnić plik logo do składnika nakładki.

![Pozycja nakładki](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Pozycja nakładki*

Kodowanie strumienia wideo H.264, należy dodać składniki kodera AVC koder wideo i AAC do powierzchni projektanta. Połącz z numerów PIN.
Konfigurowanie kodera adaptacyjnych kontrolek aplikacji, a następnie wybierz ustawienie konwersji formatu Audio/wstępne: W WERSJI 2.0 (L, R).

![Kodery audio i wideo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Kodery audio i wideo*

Teraz Dodaj **ISO Mpeg-4 multiplekser** i **produkt wyjściowy pliku** składników i podłącz numery PIN, jak pokazano.

![MP4 multiplekser i dane wyjściowe pliku](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplekser i dane wyjściowe pliku*

Należy określić nazwę dla pliku wyjściowego. Kliknij przycisk **produkt wyjściowy pliku** składnika i edytowanie wyrażenia dla pliku:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Nazwa wyjściowego pliku](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nazwa wyjściowego pliku*

Można uruchomić przepływu pracy lokalnie, aby sprawdzić, czy działa poprawnie.

Po jej zakończeniu, możesz ją uruchomić w usłudze Azure Media Services.

Najpierw Przygotuj zasobu w usłudze Azure Media Services z dwoma plikami w niej: plik wideo i logo. Można to zrobić przy użyciu platformy .NET lub interfejsu API REST. Można to również zrobić przy użyciu witryny Azure portal lub [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

W tym samouczku dowiesz się, jak zarządzać zasobami za pomocą AMSE. Istnieją dwa sposoby dodawania plików do elementu zawartości:

* Utwórz folder lokalny, skopiuj dwa pliki i przeciągnij i upuść folder do **zasobów** kartę.
* Przekazywanie pliku wideo, jako element zawartości, wyświetlania informacji dotyczących zasobu, przejdź do karty pliki i przekazać dodatkowy plik (logo).

> [!NOTE]
> Upewnij się ustawić plik podstawowy w zasobie (główny plik wideo).

![Pliki elementów zawartości w AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Pliki elementów zawartości w AMSE*

Wybierz element zawartości, a następnie wybierz Zakoduj je z użyciem kodera w warstwie Premium. Przekaż przepływ pracy i wybierz ją.

Kliknij przycisk, aby przekazać dane do procesora, a następnie dodaj następujący kod XML, aby ustawić właściwości środowiska uruchomieniowego:

![Koder w warstwie Premium w AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Koder w warstwie Premium w AMSE*

Następnie wklej następujące dane XML. Należy określić nazwę pliku wideo dla nośnika pliku wejściowego i primarySourceFile. Określ nazwę pliku z logo zbyt.

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

Korzystając z zestawu .NET SDK do tworzenia i uruchamiania zadania, te dane XML musi być przekazywane jako parametry konfiguracji.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

Po ukończeniu zadania plik MP4 w zasobach wyjściowych Wyświetla nakładki!

![Nakładki na wideo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Nakładki na wideo*

Możesz pobrać przykładowy przepływ pracy z [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Przykład 2: Wiele audio kodowanie

Przykład wielu języków audio, przepływ pracy kodowania jest dostępna w [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Ten folder zawiera przykładowy przepływ pracy, który może służyć do kodowania pliku MXF do zasobu pliki multi MP4 z wieloma ścieżki audio.

Ten przepływ pracy zakłada, że w pliku MXF zawiera jedną ścieżkę audio; dodatkowe ścieżki audio mają być przekazywane jako oddzielne pliki audio (WAV lub MP4...).

Do kodowania, wykonaj następujące kroki:

* Utwórz zasób usługi Media Services za pomocą pliku MXF i pliki Audio (pliki audio 0-18).
* Upewnij się, że w pliku MXF jest ustawiony jako plik podstawowy.
* Utwórz zadanie i zadania przy użyciu procesora koder w warstwie Premium przepływu pracy. Korzystania z przepływu pracy dostarczane (MultiMP4-1080p-19audio-v1.workflow).
* Przekazywanie danych setruntime.xml zadania (Jeśli używasz usługi Azure Media Services Explorer, kliknij przycisk "Przekaż dane xml do przepływu pracy").
  * Zaktualizuj dane XML, aby określić znaczniki odpowiedniego pliku nazwy i języków.
  * Przepływ pracy zawiera składniki audio, o nazwie Audio 1-Audio 18.
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

* Zakodowanym elementem zawartości będzie zawierać ścieżki audio języka różnej i te ścieżki powinien być możliwy w usłudze Azure Media Player.

## <a name="see-also"></a>Zobacz także
* [Wprowadzenie do kodowania w usłudze Azure Media Services w warstwie Premium](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Jak używać kodowanie w warstwie Premium w usłudze Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Kodowanie zawartości na żądanie przy użyciu usługi Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Formaty i kodeki usługi Media Encoder Premium Workflow](media-services-premium-workflow-encoder-formats.md)
* [Przykładowe pliki przepływu pracy](https://github.com/Azure/azure-media-services-samples)
* [Narzędzie Azure Media Services Explorer](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
