---
title: Wstawianie reklam po stronie klienta | Dokumenty firmy Microsoft
description: Z tego artykułu dowiesz się, jak wstawić reklamy do multimediów po stronie klienta.
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
ms.openlocfilehash: 274ee09ae98dd229b255e58261f462e322be9f89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565744"
---
# <a name="inserting-ads-on-the-client-side"></a>Wstawianie reklam po stronie klienta
Ten artykuł zawiera informacje dotyczące wstawiania różnych typów reklam po stronie klienta.

Aby uzyskać informacje na temat podpisów kodowanych i obsługi reklam w transmisjach wideo na żywo, zobacz [Obsługiwane napisy kodowane i standardy wstawiania reklam](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Usługa Azure Media Player obecnie nie obsługuje reklam.
> 
> 

## <a name="inserting-ads-into-your-media"></a><a id="insert_ads_into_media"></a>Wstawianie reklam do multimediów
Usługa Azure Media Services zapewnia obsługę wstawiania reklam za pośrednictwem platformy Windows Media Platform: Player Frameworks. Struktury odtwarzacza z obsługą reklam są dostępne dla urządzeń z systemem Windows 8, Silverlight, Windows Phone 8 i iOS. Każda struktura odtwarzacza zawiera przykładowy kod, który pokazuje, jak zaimplementować aplikację odtwarzacza. Istnieją trzy różne rodzaje reklam, które można wstawić do listy multimediów.There are three different kinds of ads you can insert into your media:list.

* **Liniowe** – reklamy pełnoklatkowe, które wstrzymują główny film.
* **Nieliniowe** – nakładki reklamy, które są wyświetlane jako główny film jest odtwarzany, zwykle logo lub inny statyczny obraz umieszczony w odtwarzaczu.
* **Towarzysz** – reklamy wyświetlane poza odtwarzaczem.

Reklamy można umieszczać w dowolnym momencie w linii czasowej głównego filmu. Musisz poinformować gracza, kiedy ma odtwarzać reklamę i jakie reklamy mają być odtwarzane. Odbywa się to przy użyciu zestawu standardowych plików opartych na języku XML: Video Ad Service Template (VAST), Digital Video Multiple Ad Playlist (VMAP), Media Abstract Sequencing Template (MAST) i Digital Video Player Ad Interface Definition (VPAID). Pliki VAST określają, jakie reklamy mają być wyświetlane. Pliki VMAP określają, kiedy mają być odtwarzane różne reklamy i zawierają VAST XML. Pliki MAST to kolejny sposób sekwencjonowania reklam, które również mogą zawierać VAST XML. Pliki VPAID definiują interfejs między odtwarzaczem wideo a serwerem reklam lub reklam.

Każda struktura gracza działa inaczej i każdy z nich zostanie omówiony w swoim artykule. W tym artykule opisano podstawowe mechanizmy wstawiania reklam. Aplikacje odtwarzacza wideo żądają reklam z serwera reklam. Serwer reklam może reagować na wiele sposobów:

* Zwracanie pliku VAST
* Zwraca plik VMAP (z osadzoną vast)
* Zwraca plik MAST (z osadzoną vast)
* Zwraca plik VAST z reklamami VPAID

### <a name="using-a-video-ad-service-template-vast-file"></a>Korzystanie z pliku szablonu usługi reklam wideo (VAST)
Plik VAST określa, jaką reklamę lub reklamy mają być wyświetlane. Następujący kod XML jest przykładem pliku VAST dla reklamy liniowej:

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

Reklama liniowa jest opisana przez <element> **liniowy.** Określa czas trwania reklamy, śledzenie zdarzeń, klikanie, śledzenie kliknięć i szereg elementów **MediaFile.** Zdarzenia śledzenia są określone w <**TrackingEvents**> element i umożliwiają serwerowi reklam śledzenie różnych zdarzeń, które występują podczas wyświetlania reklamy. W takim przypadku zdarzenia start, midpoint, complete i expand są śledzone. Zdarzenie początkowe występuje, gdy reklama jest wyświetlana. Zdarzenie punktu środkowego występuje, gdy co najmniej 50% osi czasu reklamy zostało wyświetlone. Pełne zdarzenie ma miejsce, gdy reklama jest prowadzona do końca. Zdarzenie Rozwiń występuje, gdy użytkownik rozszerza odtwarzacz wideo na pełny ekran. Kliknięcia są określane za pomocą <**ClickThrough**> element w <**VideoClicks**> element i określa identyfikator URI do zasobu do wyświetlenia, gdy użytkownik kliknie reklamę. ClickTracking jest określony w <**ClickTracking**> element, również w <**VideoClicks**> element i określa zasobów śledzenia dla odtwarzacza, aby zażądać, gdy użytkownik kliknie reklamę. Elementy <**MediaFile**> określają informacje o określonym kodowaniu reklamy. Gdy istnieje więcej niż jeden <**mediafile**> elementem, odtwarzacz wideo można wybrać najlepsze kodowanie dla platformy.

Reklamy liniowe mogą być wyświetlane w określonej kolejności. Aby to zrobić, `<Ad>` dodaj dodatkowe elementy do pliku VAST i określ kolejność przy użyciu atrybutu sekwencji. Zostało to przedstawione w poniższym przykładzie:

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

Reklamy nieliniowe `<Creative>` są również określone w elemencie. W poniższym `<Creative>` przykładzie przedstawiono element opisujący reklamę nieliniową.

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

<**NonLinearAds**> element może zawierać jeden lub więcej <**elementów> nieliniowych,** z których każdy może opisywać reklamę nieliniową. Element <**> nieliniowej** określa zasób reklamy nieliniowej. Zasób może być <**> StaticResource,** <> **IFrameResource** lub> **<HTMLResource.** \<**StaticResource**> opisuje zasób nie-HTML i definiuje atrybut creativeType, który określa sposób wyświetlania zasobu:

Image/gif, image/jpeg, image/png – zasób jest wyświetlany w tagu HTML <**img**>.

Application/x-javascript – zasób jest wyświetlany w **skrypcie** <HTML> tagu.

Aplikacja/x-shockwave-flash – zasób jest wyświetlany w odtwarzaczu Flash.

**IFrameResource** opisuje zasób HTML, który może być wyświetlany w ramce IFrame. **HTMLResource** opisuje fragment kodu HTML, który można wstawić do strony sieci web. **TrackingEvents** określić zdarzenia śledzenia i identyfikator URI do żądania, gdy wystąpi zdarzenie. W tym przykładzie acceptInvitation i zwiń zdarzenia są śledzone. Aby uzyskać więcej informacji na **nonlinearAds** elementu i jego elementów podrzędnych, zobacz IAB.NET/VAST. Należy zauważyć, że **TrackingEvents** element znajduje się w **NonLinearAds** elementu, a nie **nonlinear** elementu.

Reklamy towarzyszące `<CompanionAds>` są definiowane w elemencie. Element `<CompanionAds>` może zawierać jeden `<Companion>` lub więcej elementów. Każdy `<Companion>` element opisuje reklamę `<StaticResource>`towarzyszącą `<IFrameResource>`i `<HTMLResource>` może zawierać , lub które są określone w taki sam sposób jak w reklamie nieliniowej. Plik VAST może zawierać wiele reklam towarzyszących, a aplikacja odtwarzacza może wybrać najbardziej odpowiednią reklamę do wyświetlenia. Aby uzyskać więcej informacji na temat vast, zobacz [VAST 3.0](https://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Korzystanie z pliku digital video multiple ad playlist (VMAP)
Plik VMAP pozwala określić, kiedy występują przerwy reklamowe, jak długo jest każda przerwa, ile reklam można wyświetlać w przerwie i jakie typy reklam mogą być wyświetlane podczas przerwy. W przykładowym pliku VMAP, który definiuje pojedynczy podział reklamy:

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

Plik VMAP rozpoczyna `<VMAP>` się od elementu, `<AdBreak>` który zawiera jeden lub więcej elementów, z których każdy definiuje przerwę reklamy. Każdy podział reklamy określa typ przerwy, identyfikator przerwania i przesunięcie czasu. Atrybut breakType określa typ reklamy, którą można odtwarzać podczas przerwy: liniową, nieliniową lub wyświetlaną. Reklamy displayowe są mapowane na reklamy towarzyszące VAST. Na liście oddzielonych przecinkami (bez spacji) można określić więcej niż jeden typ reklamy. Identyfikator breakID jest opcjonalnym identyfikatorem reklamy. TimeOffset określa, kiedy reklama ma być wyświetlana. Można go określić w jeden z następujących sposobów:

1. Czas – w formacie hh:mm:ss lub hh:mm:ss.mmm, w którym .mmm jest milisekund. Wartość tego atrybutu określa czas od początku osi czasu filmu do początku przerwy reklamy.
2. Procent – w formacie n%, w którym n jest procentem osi czasu filmu do odtwoczyniania przed odtwociem reklamy
3. Start/End – określa, że reklama powinna być wyświetlana przed wyświetleniem filmu lub po nim
4. Pozycja – określa kolejność przerw reklamowych, gdy czas przerw reklamowych jest nieznany, na przykład w transmisji na żywo. Kolejność każdego podziału reklamy jest określona w formacie #n, gdzie n jest całkowitej liczby 1 lub większej. 1 oznacza, że reklama powinna być odtwarzana przy pierwszej okazji, 2 oznacza, że reklama powinna być odtwarzana przy drugiej okazji i tak dalej.

W `<AdBreak>` obrębie elementu może istnieć jeden <**adsource**> element. <> **adsource** element zawiera następujące atrybuty:

1. Id – określa identyfikator źródła reklamy
2. allowMultipleAds – wartość logiczna określająca, czy podczas przerwy reklamowej można wyświetlać wiele reklam
3. followRedirects – opcjonalna wartość logiczna określająca, czy odtwarzacz wideo powinien honorować przekierowania w odpowiedzi na reklamę

Element> **<AdSource** zapewnia graczowi wbudowaną odpowiedź reklamową lub odniesienie do odpowiedzi na reklamę. Może zawierać jeden z następujących elementów:

* `<VASTAdData>`wskazuje, że odpowiedź na reklamę VAST jest osadzona w pliku VMAP
* `<AdTagURI>`identyfikator URI, który odwołuje się do odpowiedzi na reklamę z innego systemu
* `<CustomAdData>`-dowolny ciąg reprezentujący odpowiedź nienadyskową

W tym przykładzie odpowiedź na reklamę `<VASTAdData>` w wierszu jest określona z elementem zawierającym odpowiedź na reklamę VAST. Aby uzyskać więcej informacji na temat innych elementów, zobacz [VMAP](https://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

<**AdBreak**> element może również zawierać jeden <**TrackingEvents**> element. Element> **śledzenia** <umożliwia śledzenie rozpoczęcia lub zakończenia przerwy reklamowej lub tego, czy wystąpił błąd podczas przerwy reklamy. <**TrackingEvents**> element zawiera jeden lub więcej <**śledzenia**> elementów, z których każdy określa zdarzenie śledzenia i śledzenia identyfikatora URI. Możliwe zdarzenia śledzenia to:

1. breakStart – śledzi początek przerwy w reklamie
2. breakEnd – śledzenie zakończenia przerwy reklamowej
3. błąd – śledzi błąd, który wystąpił podczas przerwy reklamowej

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

Aby uzyskać więcej informacji na temat <**TrackingEvents**> element i jego dzieci, zobaczhttp://iab.net/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Korzystanie z pliku szablonu sekwencjonowania abstrakcji nośnika (MAST)
Plik MAST umożliwia określenie wyzwalaczy definiujących wyświetlanie reklamy. Poniżej przedstawiono przykładowy plik MAST zawierający wyzwalacze reklamy przed rolką, reklamy w trakcie rolki i reklamy porolce.

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


Plik MAST rozpoczyna się od elementu **MAST,** który zawiera jeden element **wyzwalający.** Element `<triggers>` zawiera jeden lub więcej elementów **wyzwalających,** które określają, kiedy reklama powinna być odtwarzana.

Element **wyzwalacza** zawiera element **startConditions,** który określa, kiedy reklama powinna rozpocząć odtwarzanie. Element **startConditions** zawiera jeden `<condition>` lub więcej elementów. Gdy `<condition>` każdy ocenia true wyzwalacz jest inicjowany `<condition>` lub odwołany w zależności od tego, czy jest zawarty w **startConditions** lub **endConditions** element odpowiednio. Gdy `<condition>` istnieje wiele elementów, są one traktowane jako niejawne LUB, każdy warunek oceny true spowoduje wyzwalacz do zainicjowania. `<condition>`elementy mogą być zagnieżdżone. Gdy `<condition>` elementy podrzędne są wstępnie ustawione, są one traktowane jako niejawne i wszystkie warunki muszą ocenić true dla wyzwalacza do zainicjowania. Element `<condition>` zawiera następujące atrybuty definiujące warunek:

1. **typ** – określa typ warunku, zdarzenia lub właściwości
2. **nazwa** – nazwa nieruchomości lub zdarzenia, które mają być użyte podczas oceny
3. **wartość** – wartość, z którą nieruchomość zostanie obliczona
4. **operator** – operacja używana podczas oceny: EQ (równe), NEQ (nie równe), GTR (większa), GEQ (większa lub równa), LT (Mniej niż), LEQ (mniej lub równe), MOD (modulo)

**endConditions** zawierają `<condition>` również elementy. Gdy warunek ma wartość true wyzwalacz jest resetowany. Element `<trigger>` zawiera również `<sources>` element, który `<source>` zawiera jeden lub więcej elementów. Elementy `<source>` definiują identyfikator URI do odpowiedzi na reklamę i typ odpowiedzi na reklamę. W tym przykładzie identyfikator URI jest podany do odpowiedzi VAST.

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

### <a name="using-video-player-ad-interface-definition-vpaid"></a>Korzystanie z definicji interfejsu odtwarzacza wideo i reklamy (VPAID)
VPAID to interfejs API umożliwiający wykonywalne jednostki reklamowe komunikowanie się z odtwarzaczem wideo. Pozwala to na wysoce interaktywne wrażenia z reklam. Użytkownik może wchodzić w interakcje z reklamą, a reklama może reagować na działania podejmowane przez widza. Na przykład reklama może wyświetlać przyciski, które umożliwiają użytkownikowi wyświetlanie większej ilości informacji lub dłuższej wersji reklamy. Odtwarzacz wideo musi obsługiwać interfejs API VPAID, a reklama wykonywalna musi implementować interfejs API. Gdy gracz zażąda reklamy z serwera reklam, serwer może odpowiedzieć odpowiedzią VAST zawierającą reklamę VPAID.

Reklama wykonywalna jest tworzona w kodzie, który musi być wykonywany w środowisku wykonawczym, takim jak Adobe Flash™ lub JavaScript, który może być wykonywany w przeglądarce internetowej. Gdy serwer reklam zwraca odpowiedź VAST zawierającą reklamę VPAID, wartość atrybutu `<MediaFile>` apiFramework w elemencie musi być "VPAID". Ten atrybut określa, że zawarta reklama jest reklamą wykonywalną VPAID. Atrybut typu musi być ustawiony na typ MIME pliku wykonywalnego, taki jak "application/x-shockwave-flash" lub "application/x-javascript". Poniższy fragment kodu XML `<MediaFile>` pokazuje element z odpowiedzi VAST zawierającej reklamę wykonywalną VPAID.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type="application/x-shockwaveflash"
                  width="640" height="480" apiFramework="VPAID">
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

Reklamę wykonywalną `<AdParameters>` można zainicjować przy użyciu elementu w `<Linear>` lub `<NonLinear>` elementów w odpowiedzi VAST. Aby uzyskać więcej `<AdParameters>` informacji na temat elementu, zobacz [VAST 3.0](https://www.iab.net/media/file/VASTv3.0.pdf). Aby uzyskać więcej informacji na temat interfejsu API VPAID, zobacz [VPAID 2.0](https://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementowanie odtwarzacza z systemem Windows lub Windows Phone 8 z obsługą reklam
Microsoft Media Platform: Player Framework dla systemów Windows 8 i Windows Phone 8 zawiera kolekcję przykładowych aplikacji, które pokazują, jak zaimplementować aplikację odtwarzacza wideo przy użyciu struktury. Program Player Framework i przykłady można pobrać z [programu Player Framework dla systemów Windows 8 i Windows Phone 8](https://playerframework.codeplex.com).

Po otwarciu rozwiązania Microsoft.PlayerFramework.Xaml.Samples zostanie wyświetlonej wiele folderów w ramach projektu. Folder Reklama zawiera przykładowy kod odpowiedni do tworzenia odtwarzacza wideo z obsługą reklam. Wewnątrz folderu Reklama znajduje się wiele plików XAML/cs, z których każdy pokazuje, jak wstawić reklamy w inny sposób. Na poniższej liście opisano każdą z nich:

* AdPodPage.xaml Pokazuje, jak wyświetlić kapsułę reklamy.
* Strona AdScheduling.xaml Pokazuje, jak planować reklamy.
* FreeWheelPage.xaml Pokazuje, jak korzystać z wtyczki FreeWheel zaplanować reklamy.
* MastPage.xaml Pokazuje, jak zaplanować reklamy z plikiem MAST.
* ProgrammaticAdPage.xaml Pokazuje, jak programowo zaplanować reklamy w filmie.
* ScheduleClipPage.xaml Pokazuje, jak zaplanować reklamę bez pliku VAST.
* VastLinearCompanionPage.xaml Pokazuje, jak wstawić reklamę liniową i towarzyszącą.
* VastNonLinearPage.xaml Pokazuje, jak wstawić reklamę nieliniową.
* VmapPage.xaml Pokazuje, jak określić reklamy za pomocą pliku VMAP.

Każda z tych próbek używa MediaPlayer klasy zdefiniowane przez ramy odtwarzacza. Większość przykładów używa wtyczek, które dodają obsługę różnych formatów odpowiedzi reklamy. ProgrammaticAdPage próbki programowo współdziała z MediaPlayer wystąpienia.

### <a name="adpodpage-sample"></a>Przykład AdPodPage
W tym przykładzie użyto adschedulerPlugin do określenia, kiedy ma być wyświetlana reklama. W tym przykładzie reklama w trakcie akcji ma zostać odtworzona po pięciu sekundach. Zasobnik reklamy (grupa reklam do wyświetlenia w kolejności) jest określony w pliku VAST zwróconym z serwera reklam. Identyfikator URI do pliku VAST `<RemoteAdSource>` jest określony w elemencie.

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

Aby uzyskać więcej informacji na temat AdSchedulerPlugin, zobacz [Reklama w programie Player Framework w systemach Windows 8 i Windows Phone 8](https://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>Strona z rozszyciem AdScheduling
W tym przykładzie użyto również adschedulerPlugin. Planuje trzy reklamy, reklamę przed rolką, reklamę w trakcie rolki i reklamę porolce. Identyfikator URI do vast dla każdej `<RemoteAdSource>` reklamy jest określony w elemencie.

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

### <a name="freewheelpage"></a>FreeWheelPage (Strona freewheel)
W tym przykładzie użyto FreeWheelPlugin, który określa source atrybut, który określa identyfikator URI, który wskazuje plik SmartXML, który określa zawartość reklamy, a także informacje o harmonogramie reklam.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>Strona masztowa
W tym przykładzie użyto mastschedulerPlugin, który umożliwia użycie pliku MAST. Atrybut Source określa lokalizację pliku MAST.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Ten przykład programowo współdziała z MediaPlayer. Plik ProgrammaticAdPage.xaml zawiera wystąpienia programu MediaPlayer:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

ProgrammaticAdPage.xaml.cs plik tworzy AdHandlerPlugin, dodaje TimelineMarker, aby określić, kiedy reklama powinna być wyświetlana, a następnie dodaje program obsługi dla MarkerReached zdarzenie, które ładuje RemoteAdSource określając identyfikator URI do pliku VAST, a następnie odtwarza reklamę.

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

### <a name="scheduleclippage"></a>Strona Harmonogramu
W tym przykładzie użyto adschedulerPlugin do zaplanowania reklamy w trakcie rolki, określając plik wmv zawierający reklamę.

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

### <a name="vastlinearcompanionpage"></a>VastLinearCompanionPage (Strona vastlinearcompanion)
W tym przykładzie pokazano, jak za pomocą AdSchedulerPlugin zaplanować reklamę liniową w połowie rolki z reklamą towarzyszącą. Element `<RemoteAdSource>` określa lokalizację pliku VAST.

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

### <a name="vastlinearnonlinearpage"></a>VastLinear NieliniowyPage
W tym przykładzie użyto AdSchedulerPlugin do zaplanowania reklamy liniowej i nieliniowej. Lokalizacja pliku VAST jest `<RemoteAdSource>` określona za pomocą elementu.

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

### <a name="vmappage"></a>Strona VMAP
W tym przykładzie użyto programu VmapSchedulerPlugin do planowania reklam przy użyciu pliku VMAP. Identyfikator URI do pliku VMAP jest określony w `<VmapSchedulerPlugin>` atrybucie Source elementu.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Wdrażanie odtwarzacza wideo na iOS z obsługą reklam
Microsoft Media Platform: Player Framework dla systemu iOS zawiera kolekcję przykładowych aplikacji, które pokazują, jak zaimplementować aplikację odtwarzacza wideo przy użyciu struktury. Można pobrać platformę programu Player i przykłady z [programu Azure Media Player Framework](https://github.com/Azure/azure-media-player-framework). Strona GitHub zawiera łącze do wiki, które zawiera dodatkowe informacje na temat struktury odtwarzacza i wprowadzenie do przykładu gracza: [Azure Media Player Wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Planowanie reklam za pomocą vmapa
W poniższym przykładzie pokazano, jak zaplanować reklamy przy użyciu pliku VMAP.

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
W poniższym przykładzie pokazano, jak zaplanować późne powiązanie reklamy VAST.


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

   W poniższym przykładzie pokazano, jak zaplanować wczesną reklamę VAST.

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

W poniższym przykładzie pokazano, jak wstawić reklamę przy użyciu edycji z grubsza (RCE)

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

W poniższym przykładzie pokazano, jak zaplanować kapsułę reklamy.

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

W poniższym przykładzie pokazano, jak zaplanować niekleiącą reklamę w trakcie rolki. Reklama nieklejąca jest odtwarzana tylko raz, niezależnie od tego, czy widz będzie się starał.

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

W poniższym przykładzie pokazano, jak zaplanować przyklejoną reklamę w trakcie rolki. Reklama przyklejona jest wyświetlana za każdym razem, gdy zostanie osiągnięty określony punkt na osi czasu filmu.

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

W poniższym przykładzie pokazano, jak zaplanować reklamę porolce.

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

W poniższym przykładzie pokazano, jak zaplanować reklamę przed rolką.

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

W poniższym przykładzie pokazano, jak zaplanować reklamę nakładki w trakcie rzutu.

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

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
