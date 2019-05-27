---
title: Wstawianie reklam po stronie klienta | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób Wstawianie reklam po stronie klienta.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 49c836f5e9189104ba77e8f3d865f4db199c4060
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002971"
---
# <a name="inserting-ads-on-the-client-side"></a>Wstawianie reklam po stronie klienta
Ten artykuł zawiera informacje na temat sposobu Wstaw różnego rodzaju reklam po stronie klienta.

Informacje na temat zamknięte podpisy i ad obsługi w transmisji strumieniowej na żywo w filmach wideo, zobacz [Ad wstawiania standardów i obsługiwane kodowane](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Usługa Azure Media Player nie obsługuje obecnie reklam.
> 
> 

## <a id="insert_ads_into_media"></a>Wstawianie reklam do multimediów
Usługa Azure Media Services zapewnia obsługę wstawiania reklam za pośrednictwem platformy Media Windows: Architektury odtwarzaczy. Architektury odtwarzaczy z obsługą usługi ad są dostępne dla urządzeń z systemem Windows 8, Silverlight, Windows Phone 8 i iOS. Każdy struktury odtwarzacza zawiera przykładowy kod, który pokazuje, jak zaimplementować aplikacja odtwarzacza. Istnieją trzy różne rodzaje reklam, które można wstawić do nośnika: listy.

* **Liniowy** — pełna reklamy ramki, które wstrzymanie odtwarzania filmu głównego.
* **Nieliniowych** — reklamy nakładki, które są wyświetlane jako odtwarzania wideo głównym, zwykle logo lub inne obraz statyczny umieszczony w odtwarzaczu.
* **Pomocnik** — reklam, które są wyświetlane poza odtwarzacza.

Usługa AD można umieścić w dowolnym momencie w głównym wideo osi czasu. Musisz poinformować odtwarzacza, kiedy należy odtworzyć ad i reklam, które do odtwarzania. Odbywa się przy użyciu zestawu standardowych plików oparty na formacie XML: Szablon (VAST), Digital Video usługi Ad wideo, wiele Ad odtwarzania (VMAP), Media abstrakcji sekwencjonowania szablonu (MASZTÓW), a definicja interfejsu Ad cyfrowy odtwarzacz wideo (VPAID). OGROMNA pliki określają, jakie reklam, aby wyświetlić. Pliki VMAP Określ, kiedy do odtwarzania różnych reklam i zawierać OGROMNA kod XML. Pliki MASZTÓW są innym sposobem reklamy sekwencji, które również mogą zawierać duże XML. Pliki VPAID definiują interfejs między odtwarzacza wideo i usługi ad lub serwera usługi ad.

Każdy struktury odtwarzacza zmieniło i każdy zostały omówione w artykule swój własny. W tym artykule opisano podstawowe mechanizmów, które służy do wstawiania reklam. Aplikacji odtwarzacza wideo żądać reklam z serwera usługi ad. Serwer usługi ad może odpowiadać na różne sposoby:

* Zwróć OGROMNYCH plików
* Zwrócić VMAP plik (przy użyciu osadzonych VAST)
* Zwrócić MASZTÓW plik (przy użyciu osadzonych VAST)
* Zwrócić OGROMNA plik za pomocą VPAID reklam

### <a name="using-a-video-ad-service-template-vast-file"></a>Użycie pliku szablonów (VAST) usługi Ad wideo
OGROMNA plik Określa, jakie usługi ad lub AD, aby wyświetlić. Następujący kod XML znajduje się przykład OGROMNEJ pliku liniowej ad:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
```

Liniowy ad jest opisana przez <**liniowej**> element. Określa czas trwania ad, śledzenia zdarzeń, kliknij za pośrednictwem śledzenie kliknięć i liczbę **MediaFile** elementów. Zdarzenia śledzenia są określone w <**TrackingEvents**> element i Zezwól serwerowi usługi ad do śledzenia różnych zdarzeń, które występują podczas wyświetlania ad. W takim przypadku rozpoczęcia punktu środkowego zakończone i rozwiń listę zdarzeń są śledzone. Zdarzenie rozpoczęcia występuje, gdy jest wyświetlana ad. Zdarzenie punktu środkowego występuje, gdy co najmniej wyświetlił 50% ad osi czasu. Zdarzenie ukończenia występuje, gdy ad zostało uruchomione na końcu. Zdarzenie rozwinięcia występuje, gdy użytkownik rozwija odtwarzacza wideo do pełnego ekranu. W witrynie są określane za pomocą <**przeglądowe**> elemencie <**VideoClicks**> elementu i określa identyfikator URI do zasobu do wyświetlenia, gdy użytkownik kliknie ad. ClickTracking została określona w <**ClickTracking**> elementu również w ramach <**VideoClicks**> elementu i Określa zasób śledzenia dla gracza, aby zażądać, gdy użytkownik kliknie ad . <**MediaFile**> elementy Określ informacje dotyczące określonego kodowania usługi ad. Gdy istnieje więcej niż jeden <**MediaFile**> elementu odtwarzacza wideo można wybrać optymalne kodowanie dla platformy.

Liniowy reklam, mogą być wyświetlane w określonej kolejności. Aby to zrobić, Dodaj dodatkowe `<Ad>` elementów do VAST pliku i określić kolejność przy użyciu atrybutu sekwencji. Ilustruje to poniższy przykład:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
```

Reklamy nieliniowych są określone w `<Creative>` również element. W poniższym przykładzie przedstawiono `<Creative>` element, który opisuje nieliniowych ad.

```xml
    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>
```

<**NonLinearAds**> element może zawierać jeden lub więcej <**NonLinear**> elementy, z których każdy może opisywać nieliniowych ad. <**NonLinear**> element Określa zasób dla nieliniowych ad. Zasób może być <**staticresource —**>, <**IFrameResource**>, lub <**HTMLResource**>. \<**Staticresource —**> w tym artykule opisano zasobów innych niż HTML i definiuje atrybut creativeType, który określa sposób wyświetlania zasobu:

Image/gif, image/jpeg, image/png — zasób jest wyświetlana w formacie HTML <**img**> tag.

Application/x-javascript — zasób jest wyświetlana w formacie HTML <**skryptu**> tag.

Application/x-shockwave-flash — zasób jest wyświetlany w odtwarzaczu Flash.

**IFrameResource** opisuje zasobu HTML, która może być wyświetlana w ramce IFrame. **HTMLResource** opisuje fragment kodu HTML, które mogą być wstawiane do strony sieci web. **TrackingEvents** Określ zdarzenia śledzenia i identyfikatora URI żądania po wystąpieniu zdarzenia. W tym przykładzie acceptInvitation i Zwiń zdarzeń są śledzone. Aby uzyskać więcej informacji na temat **NonLinearAds** elementu i jego elementy podrzędne, zobacz IAB.NET/VAST. Należy pamiętać, że **TrackingEvents** element znajduje się w obrębie **NonLinearAds** elementu zamiast **NonLinear** elementu.

Pomocnik reklam są zdefiniowane w ramach `<CompanionAds>` elementu. `<CompanionAds>` Element może zawierać jeden lub więcej `<Companion>` elementów. Każdy `<Companion>` element w tym artykule opisano ad pomocnika i może zawierać `<StaticResource>`, `<IFrameResource>`, lub `<HTMLResource>` określone w taki sam sposób jak nieliniowych ad. OGROMNA plik może zawierać wiele reklam pomocnika, a aplikacja odtwarzacza można wybrać najbardziej odpowiedni ad, aby wyświetlić. Aby uzyskać więcej informacji na temat VAST zobacz [OGROMNA 3.0](https://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>#Using Digital Video wielu Ad listy odtwarzania (VMAP)
Plik VMAP można określić po wystąpieniu przerwy ad, jak długo trwa każdego podziału, jak wiele reklam, może być wyświetlana w podziału i jakie typy AD może być wyświetlany podczas podziału. Następujące opcje w VMAP przykładowy plik, który definiuje podziału pojedynczej usługi ad:

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[https://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scalable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

Plik VMAP zaczyna się od `<VMAP>` element, który zawiera co najmniej jeden `<AdBreak>` elementów, definiowanie przerwanie usługi ad. Każdy podziału ad Określa typ podziału, identyfikator przerwania i przesunięcie czasu. Atrybut breakType Określa typ ad, która może być odtwarzany podczas przerwy: liniowych, nieliniowych, lub wyświetlić. Wyświetl reklamy, mapy do OGROMNYCH pomocnika reklam. Można określić więcej niż jeden typ usługi ad w listę rozdzielanych przecinkami (bez spacji). BreakID jest opcjonalny identyfikator ad. TimeOffset Określa, kiedy ad powinna być wyświetlana. Można wybrać jeden z następujących sposobów:

1. Godzina w formacie: mm: ss lub GG:mm:ss.mmm, w którym .mmm jest milisekund. Wartość tego atrybutu określa czas od początku osi czasu w wideo na początku tego podziału usługi ad.
2. Procent — format n %, gdzie n to wartość procentowa wideo osi czasu do odtwarzania przed odtwarzanie ad
3. Rozpoczęcia/zakończenia — określa, że usługi ad powinna być wyświetlana przed lub po film wideo został wyświetlony
4. Umieść — określa kolejność podziały ad, gdy czas przerwy ad jest nieznany, takiego jak transmisja strumieniowa na żywo. Kolejność każdego podziału ad jest określona w formacie #n, gdzie n to liczba całkowita 1 lub większą. 1 oznacza ad powinna być odtwarzane przy okazji pierwszego 2 oznacza ad powinna być odtworzona w drugiej szansy sprzedaży i tak dalej.

W ramach `<AdBreak>` elementu, może istnieć tylko jeden <**AdSource**> element. <**AdSource**> element zawiera następujące atrybuty:

1. Identyfikator — Określa identyfikator źródła usługi ad
2. allowMultipleAds — wartość logiczna określająca, czy wiele reklam, mogą być wyświetlane podczas przerwy ad
3. followRedirects — opcjonalna wartość logiczna określająca, jeśli odtwarzacza wideo należy przestrzegać przekierowuje w odpowiedzi usługi ad

<**AdSource**> element udostępnia odtwarzacz odpowiedź ad wbudowanych lub odwołanie do odpowiedzi usługi ad. Nazwa może zawierać jedną z następujących elementów:

* `<VASTAdData>` Wskazuje, że odpowiedź OGROMNA ad jest osadzony w pliku VMAP
* `<AdTagURI>` Identyfikator URI, który odwołuje się do ad odpowiedzi z innego systemu
* `<CustomAdData>` -dowolny ciąg, który reprezentuje odpowiedź — duże

W tym przykładzie odpowiedź ad w tekście jest określony za pomocą `<VASTAdData>` element, który zawiera odpowiedź OGROMNA ad. Aby uzyskać więcej informacji na temat innych elementów zobacz [VMAP](https://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

<**AdBreak**> element może także zawierać jeden <**TrackingEvents**> element. <**TrackingEvents**> element służy do śledzenia początku lub końcu parametru break ad lub tego, czy wystąpił błąd podczas podziału usługi ad. <**TrackingEvents**> element zawiera co najmniej jeden <**śledzenia**> elementy, z których każdy określa zdarzenie śledzenia i śledzenie identyfikatora URI. Zdarzenia śledzenia możliwe są:

1. breakStart — śledzi początku podziału ad
2. breakEnd — śledzenia wykonania podziału ad
3. Błąd — śledzi błędu, który wystąpił podczas przerwy ad

W poniższym przykładzie pokazano plik VMAP, który określa zdarzenia śledzenia

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

Aby uzyskać więcej informacji na temat <**TrackingEvents**> element i jego elementy podrzędne, zobacz http://iab.net/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Za pomocą abstrakcyjny Media, sekwencjonowanie plik szablonu (MASZTÓW)
Plik MASZTÓW umożliwia określenie wyzwalacze, które określają, kiedy jest wyświetlana przy użyciu usług ad. Oto przykładowy plik MASZTÓW, który zawiera Wyzwalacze dla usługi pre roll ad, ad środku procesu i ad po wdrożenie.

```xml
    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>
```


Plik MASZTÓW zaczyna się od **MASZTÓW** element, który zawiera jeden **wyzwalaczy** elementu. `<triggers>` Elementu zawiera jeden lub więcej **wyzwalacza** elementy, które określają, kiedy powinna być odtworzona usługi ad.

**Wyzwalacza** element zawiera **startConditions** element określające rozpoczęcia reklamy do odtwarzania. **StartConditions** elementu zawiera jeden lub więcej `<condition>` elementów. Podczas każdego `<condition>` jest spełniony, wyzwalacz jest inicjowana lub odwoływane, w zależności od czy `<condition>` znajduje się w obrębie **startConditions** lub **endConditions** — element odpowiednio. Gdy wiele `<condition>` znajdują się elementy, będą one traktowane jako niejawny lub dowolny warunek oceny na wartość true powoduje, że wyzwalacz inicjujący. `<condition>` elementy mogą być zagnieżdżone. Gdy podrzędny `<condition>` elementy są ustawione wstępnie tak, będą one traktowane jako i niejawne, wszystkie warunki musi zwrócić wartość true dla wyzwalacza do inicjowania. `<condition>` Element zawiera następujące atrybuty, które określają warunek:

1. **Typ** — Określa typ zdarzenia, właściwość lub warunku
2. **Nazwa** — nazwa właściwości lub zdarzenia, które ma być używany podczas oceny
3. **wartość** — wartość, która będzie porównywany właściwość
4. **operator** — operacja ma być używany podczas oceny: EQ (równe), NEQ (nie równa się), GTR (większe), GEQ (większe lub równe), LT (mniejsze niż), LEQ (mniejsze niż lub równe), dzielenie MODULO (modulo)

**endConditions** również zawierać `<condition>` elementów. Jeśli wynikiem warunku jest wartość true, wyzwalacz jest resetowany. `<trigger>` Zawiera również element `<sources>` element, który zawiera co najmniej jeden `<source>` elementów. `<source>` Elementy Definiowanie identyfikator URI odpowiedzi usługi ad i typ odpowiedzi usługi ad. W tym przykładzie identyfikator URI znajduje się do OGROMNYCH odpowiedzi.

```xml
    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
```

### <a name="using-video-player-ad-interface-definition-vpaid"></a>Za pomocą odtwarzacza wideo — Ad definicji interfejsu (VPAID)
VPAID to interfejs API, umożliwiających jednostki ad pliku wykonywalnego do komunikowania się z odtwarzacza wideo. Dzięki temu ad wysoce interaktywnych środowisk. Użytkownik może korzystać z usług ad i ad mogą odpowiadać na akcje wykonywane przez przeglądarkę. Na przykład usługi ad mogą być wyświetlane przyciski, dzięki czemu można wyświetlić więcej informacji lub dłuższej wersji z usług ad. Odtwarzacz wideo musi obsługiwać interfejs API VPAID i pliku wykonywalnego usługi ad musi implementować interfejs API. Gdy gracz żądania usługi ad z serwera ad serwer może odpowiadać z OBSZERNYM odpowiedź, która zawiera VPAID ad.

Wykonywalny ad jest tworzony w kodzie, który musi zostać wykonana w środowisku uruchomieniowym, takie jak Adobe Flash™ lub JavaScript, która może być wykonywana w przeglądarce sieci web. Po powrocie z serwera ad OGROMNA odpowiedź zawierającą VPAID ad wartość apiFramework atrybutu w `<MediaFile>` element musi być "VPAID". Ten atrybut definiuje zawarte ad ad do pliku wykonywalnego VPAID. Atrybut typu musi być równa typ MIME pliku wykonywalnego, takie jak "application/x-shockwave-flash" lub "application/x-javascript". Poniższy fragment kodu przedstawia XML `<MediaFile>` elementu z OBSZERNYM odpowiedź zawierającą ad do pliku wykonywalnego VPAID.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

Wykonywalny ad mogą być inicjowane przy użyciu `<AdParameters>` elemencie `<Linear>` lub `<NonLinear>` elementów w OBSZERNYM odpowiedzi. Aby uzyskać więcej informacji na temat `<AdParameters>` elementu, zobacz [OGROMNA 3.0](https://www.iab.net/media/file/VASTv3.0.pdf). Aby uzyskać więcej informacji na temat interfejsu API VPAID zobacz [VPAID 2.0](https://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementowanie Windows lub Windows Phone 8 Player z obsługą usługi Ad
Microsoft Media Platform: Player Framework dla systemu Windows 8 i Windows Phone 8 zawiera kolekcję przykładowych aplikacji, które pokazują, jak wdrażanie przy użyciu platformy aplikacji odtwarzacza wideo. Możesz pobrać Player Framework i przykładów z poziomu [Player Framework dla systemu Windows 8 i Windows Phone 8](https://playerframework.codeplex.com).

Po otwarciu rozwiązania Microsoft.PlayerFramework.Xaml.Samples, zobaczysz liczbę folderów w ramach projektu. Folder reklamy zawiera przykładowy kod dotyczą tworzenia odtwarzacza wideo z obsługą usługi ad. Wewnątrz reklamy folder jest XAML/cs pliki, które pokazują, jak wstawianie reklam w inny sposób. Poniższa lista zawiera opis:

* AdPodPage.xaml pokazuje sposób wyświetlania zasobnika usługi ad.
* AdSchedulingPage.xaml pokazuje, jak zaplanować reklam.
* FreeWheelPage.xaml pokazuje, jak za pomocą wtyczki FreeWheel zaplanować reklam.
* MastPage.xaml pokazuje, jak zaplanować reklam z plikiem MASZTÓW.
* ProgrammaticAdPage.xaml pokazuje, jak programowo zaplanować reklam w wideo.
* ScheduleClipPage.xaml pokazuje sposób tworzenia harmonogramu bez OGROMNYCH plików przy użyciu usług ad.
* VastLinearCompanionPage.xaml przedstawiono sposób wstawiania liniowych i towarzyszące ad.
* VastNonLinearPage.xaml przedstawia sposób wstawiania nieliniowych ad.
* VmapPage.xaml pokazuje sposób określania reklam z plikiem VMAP.

Każda próbka używa MediaPlayer — klasa zdefiniowana przez strukturę odtwarzacza. Większość przykładów za pomocą wtyczki, który dodano obsługę różnych formatach odpowiedzi usługi ad. Przykładowe ProgrammaticAdPage programowo współdziała z wystąpienia elementu MediaPlayer.

### <a name="adpodpage-sample"></a>Przykładowe AdPodPage
W tym przykładzie używa AdSchedulerPlugin w celu zdefiniowania, kiedy będą wyświetlane przy użyciu usług ad. W tym przykładzie anonsu środku wdrożenie jest zaplanowane do odtwarzania po 5 sekundach. Zasobnik ad (grupa reklam, aby wyświetlić w kolejności) jest określone w pliku OGROMNA zwrócony z serwera usługi ad. Identyfikator URI do OGROMNYCH plików jest określony w `<RemoteAdSource>` elementu.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">

        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>

                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>

                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>
```

Aby uzyskać więcej informacji na temat AdSchedulerPlugin zobacz [reklamowych w ramach odtwarzacza systemu Windows 8 i Windows Phone 8](https://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
W tym przykładzie używa również AdSchedulerPlugin. Planuje ona trzy reklam, ad wstępne wdrożenie, ad środku procesu i ad po wdrożenie. Identyfikator URI do VAST w przypadku każdej reklamy została określona w `<RemoteAdSource>` elementu.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>

                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="freewheelpage"></a>FreeWheelPage
W tym przykładzie użyto FreeWheelPlugin, który określa atrybut źródłowy, który określa identyfikator URI, który wskazuje plik SmartXML, który określa ad zawartości, a także informacje dotyczące planowania usługi ad.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage
W tym przykładzie użyto MastSchedulerPlugin, która pozwala na używanie plików MASZTÓW. Atrybut źródłowy określa lokalizację pliku MASZTÓW.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
W tym przykładzie programowo współdziała z MediaPlayer. Plik ProgrammaticAdPage.xaml tworzy MediaPlayer:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

Plik ProgrammaticAdPage.xaml.cs tworzy AdHandlerPlugin dodaje TimelineMarker, aby określić przy użyciu usług ad powinien zostać wyświetlony, a następnie dodaje program obsługi zdarzeń MarkerReached, ładuje RemoteAdSource, określając identyfikatora URI do OGROMNYCH plików, a następnie odgrywa ad.

```csharp
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;

            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }

            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }
```

### <a name="scheduleclippage"></a>ScheduleClipPage
Ta próbka używa AdSchedulerPlugin można zaplanować ad środku procesu, określając plik wmv, który zawiera ad.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearcompanionpage"></a>VastLinearCompanionPage
Ten przykład ilustruje sposób użycia AdSchedulerPlugin można zaplanować środku procesu liniowego usługi ad z usługą ad pomocnika. `<RemoteAdSource>` Element określa lokalizację pliku OGROMNYCH.

```xml
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage
Ta próbka używa AdSchedulerPlugin można zaplanować liniowych i nieliniowych ad. Lokalizacja pliku OGROMNA jest określony za pomocą `<RemoteAdSource>` elementu.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vmappage"></a>VMAPPage
Ta próbka używa VmapSchedulerPlugin można zaplanować przy użyciu pliku VMAP reklam. Identyfikator URI pliku VMAP jest określony w atrybut źródłowy `<VmapSchedulerPlugin>` elementu.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Implementowanie odtwarzacza wideo z obsługą usługi Ad dla systemu iOS
Microsoft Media Platform: Struktury odtwarzacza dla systemu iOS zawiera kolekcję przykładowych aplikacji, które pokazują, jak wdrażanie przy użyciu platformy aplikacji odtwarzacza wideo. Możesz pobrać Player Framework i przykładów z poziomu [usługi Azure Media Player Framework](https://github.com/Azure/azure-media-player-framework). Na stronie usługi GitHub ma link do witryny typu Wiki, która zawiera dodatkowe informacje na temat struktury odtwarzacza i wprowadzenie do przykładu player: [Witryny typu Wiki usługi Azure Media Player](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Planowanie reklam z VMAP
Poniższy przykład pokazuje sposób tworzenia harmonogramu przy użyciu pliku VMAP reklam.

```csharp
    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }
            }
```

### <a name="scheduling-ads-with-vast"></a>Planowanie reklam z VAST
Poniższy przykład pokazuje sposób tworzenia harmonogramu późne powiązania OGROMNA ad.


```csharp
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

   Poniższy przykład pokazuje sposób tworzenia harmonogramu wczesne ad OGROMNA powiązania.

```csharp
    //Example:4 Schedule an early binding VAST ad
    //Download the VAST file
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]])
    {
        [self logFrameworkError];
    }
    else
    {
        adLinearTime.startTime = 7;
        adLinearTime.duration = 0;

        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }
```

Poniższy przykład przedstawia sposób wstawiania ad przy użyciu nierównej Wytnij edycji (Ródłowy)

```csharp
    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }
```

Poniższy przykład pokazuje, jak można zaplanować zasobnika usługi ad.

```csharp
    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;

    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Poniższy przykład pokazuje, jak można zaplanować-umocowany ad roll pośredniej. Ad umocowany tylko zostanie odtworzony po niezależnie od wszelkich znalezienia wykonuje przeglądarka.

```csharp
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Poniższy przykład pokazuje, jak można zaplanować umocowany ad roll pośredniej. Umocowany ad jest wyświetlany za każdym razem, osiągnięty określonego punktu na osi czasu w wideo.

```csharp
    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Poniższy przykład pokazuje sposób tworzenia harmonogramu ad po wdrożenie.

```csharp
    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Poniższy przykład pokazuje sposób tworzenia harmonogramu ad wstępne wdrożenie.

```csharp
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Poniższy przykład pokazuje sposób tworzenia harmonogramu ad środku roll nakładki.

```csharp
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przesyłanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Opracowywanie aplikacji odtwarzacza wideo](media-services-develop-video-players.md)

