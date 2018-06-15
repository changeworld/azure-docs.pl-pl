---
title: Wstawianie reklam po stronie klienta | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób wstawiania reklam po stronie klienta.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
ms.openlocfilehash: 57cb06fcee029f72dd1cf81ef086985fdda3f45f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788970"
---
# <a name="inserting-ads-on-the-client-side"></a>Wstawianie reklam po stronie klienta
Ten artykuł zawiera informacje na temat sposobu Wstaw różnych typów reklam po stronie klienta.

Informacje na temat zamkniętego podpisów i ad obsługi w wideo transmisji strumieniowej na żywo, zobacz [obsługiwane kodowane i standardy wstawiania Ad](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Azure Media Player nie obsługuje obecnie reklam.
> 
> 

## <a id="insert_ads_into_media"></a>Wstawianie reklam do multimediów
Usługa Azure Media Services obsługuje wstawiania reklam za pośrednictwem platformy Windows Media: struktur odtwarzaczy. Struktur odtwarzaczy z obsługą ad są dostępne dla urządzeń z systemem Windows 8, Silverlight, Windows Phone 8 i iOS. Każdy framework player zawiera przykładowy kod, który pokazuje, jak wdrożyć aplikacja odtwarzacza. Istnieją trzy różne rodzaje reklamy, które można wstawić do nośnika: listy.

* **Liniowy** — pełna reklam ramki Wstrzymaj głównego wideo.
* **Rożne** — nakładki reklamy, które są wyświetlane podczas odtwarzania wideo głównego, zwykle logo lub inne statyczny obraz umieszczony odtwarzacza.
* **Pomocnik** — reklam wyświetlanych poza odtwarzacza.

Usługa AD można umieścić w dowolnym momencie wideo głównych osi czasu. Odtwarzacz musi sprawdzić, kiedy odtwarzać ad i które reklam, aby odtworzyć. Jest to realizowane przy użyciu zestawu standardowych plików opartych na języku XML: wideo Ad Service szablonu (VAST), cyfrowy wideo wielu Ad listy odtwarzania (VMAP) nośnika abstrakcyjny sekwencjonowania szablonu (MASZTÓW) i cyfrowy wideo Player Ad interfejsu definicji (VPAID). Pliki PRZEWAŻAJĄCA określają, jakie reklam, aby wyświetlić. Pliki VMAP Określ, kiedy do odtwarzania różnych reklam i zawierać PRZEWAŻAJĄCA XML. Pliki MASZTÓW są reklam sekwencji, które również mogą zawierać PRZEWAŻAJĄCA XML w inny sposób. Pliki VPAID zdefiniuj interfejs między odtwarzacza wideo i ad lub serwera usługi ad.

Każdy framework player zmieniło i wszystkie będą opisane w artykule własny. W tym artykule opisano podstawowe mechanizmy, które służy do wstawiania reklam. Aplikacji odtwarzacza wideo żądania reklam z serwera ad. Serwer ad może odpowiadać na kilka sposobów:

* Zwraca PRZEWAŻAJĄCA plik
* Zwraca plik VMAP (z osadzonych VAST)
* Zwraca plik MASZTÓW (z osadzonych VAST)
* Zwraca PRZEWAŻAJĄCA plik z VPAID reklam

### <a name="using-a-video-ad-service-template-vast-file"></a>Przy użyciu pliku szablonu (VAST) usługi Ad wideo
PRZEWAŻAJĄCA pliku Określa, jakie ad lub reklam, aby wyświetlić. Następujący kod XML jest przykładowy plik PRZEWAŻAJĄCA liniowej AD:

```xml
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
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
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
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

Liniowy ad jest opisane przez <**liniowy**> elementu. Określa czas trwania ad, śledzenia zdarzeń, kliknij go, kliknij przycisk śledzenia i liczbę **MediaFile** elementów. Zdarzenia śledzenia są określone w <**TrackingEvents**> element i umożliwić serwera ad do śledzenia różnych zdarzeń występujących podczas wyświetlania ad. W takim przypadku początek środkowego zakończeniu i rozwiń listę zdarzeń są śledzone. Zdarzenia uruchamiania występuje, gdy usługi ad jest wyświetlany. Zdarzenie środkowego występuje, gdy co najmniej się, że wyświetlił 50% ad osi czasu. Zdarzenie ukończenia występuje, gdy usługi ad zostało uruchomione na końcu. Rozwiń zdarzenie występuje, gdy użytkownik rozwija odtwarzacza wideo do pełnego ekranu. Clickthroughs są określane za pomocą <**przeglądowego**> w elemencie <**VideoClicks**> element i określa identyfikator URI zasobu do wyświetlenia, gdy użytkownik kliknie ad. ClickTracking jest określona w <**ClickTracking**> elementu również w <**VideoClicks**> element i umożliwia określenie zasobu śledzenia odtwarzacza na żądanie, gdy użytkownik kliknie ad . <**MediaFile**> elementy Określ informacje dotyczące określonego kodowania AD. Gdy istnieje więcej niż jeden <**MediaFile**> elementu odtwarzacza wideo można wybrać optymalne kodowanie dla platformy. 

Liniowy reklam mogą być wyświetlane w określonej kolejności. W tym celu należy dodać dodatkowe <Ad> elementy VAST pliku i określić kolejność przy użyciu atrybutu sekwencji. Poniższy przykład przedstawia to:

```xml
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResouce]]></Impression>
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
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResouce]]></Impression>
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

Rożne reklam są określone w <Creative> również element. W poniższym przykładzie przedstawiono <Creative> element, który opisuje rożne ad.

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

<**NonLinearAds**> element może zawierać co najmniej jeden <**NonLinear**> elementy, które opisują rożne ad. <**NonLinear**> element określa zasobu dla rożne ad. Zasób może być <**StaticResouce**>, <**IFrameResource**>, lub <**HTMLResouce**>. <**StaticResource**> opisuje zasób w innym języku niż HTML i definiuje atrybut creativeType, który określa sposób wyświetlania zasobu:

Obraz/gif, image/jpeg, obrazu/png — zasobu jest wyświetlana w formacie HTML <**img**> tagu.

Application/x-javascript — zasobu jest wyświetlana w formacie HTML <**skryptu**> tagu.

Application/x-shockwave-flash — zasobu jest wyświetlany w Flash player.

**IFrameResource** opisuje zasobu HTML, który może być wyświetlany w elementu IFrame. **HTMLResource** opisuje fragment kodu HTML, który można wstawiać do strony sieci web. **TrackingEvents** Określ śledzenia zdarzeń i identyfikator URI żądania po wystąpieniu zdarzenia. W tym przykładzie zdarzenia acceptInvitation i Zwiń są śledzone. Aby uzyskać więcej informacji na temat **NonLinearAds** elementu i jego elementów podrzędnych, zobacz IAB.NET/VAST. Należy pamiętać, że **TrackingEvents** element znajduje się w **NonLinearAds** elementu zamiast **NonLinear** elementu.

Pomocnik reklam są zdefiniowane w <CompanionAds> elementu. <CompanionAds> Element może zawierać jeden lub więcej <Companion> elementów. Każdy <Companion> elementu opisuje ad pomocnika i może zawierać <StaticResource>, <IFrameResource>, lub <HTMLResource> określono w taki sam sposób jak rożne ad. PRZEWAŻAJĄCA plik może zawierać wiele pomocnika reklam i aplikacja odtwarzacza można wybrać najodpowiedniejszy ad do wyświetlenia. Aby uzyskać więcej informacji o VAST, zobacz [3.0 PRZEWAŻAJĄCA](http://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Przy użyciu wielu pliku listy odtwarzania (VMAP) Ad cyfrowy wideo
Pliku VMAP umożliwia określenie, kiedy występują podziały ad, jak długo trwa każdego podziału ile reklam mogą być wyświetlane w ramach podziału i jakie typy reklam mogą być wyświetlane podczas podziału. Poniższy przykładowy plik VMAP, który definiuje podział pojedynczego ad:

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[http://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scaleable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
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

Rozpoczyna się od pliku VMAP <VMAP> element, który zawiera co najmniej jeden <AdBreak> elementy Definiowanie podziału ad. Każdy podziału ad Określa typ podziału, identyfikator podziału i przesunięcie czasu. Atrybut breakType Określa typ ad, która może być odtwarzany podczas przerwy: liniowych, rożne, lub wyświetlić. Wyświetlenie mapy reklam PRZEWAŻAJĄCA pomocnika reklam. Można określić więcej niż jeden typ ad w postaci listy rozdzielanej przecinkami (bez spacji). BreakID jest opcjonalny identyfikator usługi ad. TimeOffset Określa, kiedy powinny być wyświetlane ad. Można ją określić w jednym z następujących sposobów:

1. Godzina w formacie hh: mm: lub GG:mm:ss.mmm .mmm w przypadku milisekund. Wartość tego atrybutu określa czas od początku wideo osi czasu na początku podziału ad.
2. Wartość procentowa — w formacie n %, gdzie n to odsetek wideo osi czasu do odtwarzania przed odtwarzanie ad
3. Rozpoczęcie i zakończenie — Określa, że usługi ad powinna być wyświetlana przed lub po wideo został wyświetlony
4. Umieść — określa kolejność podziały ad, gdy czas przerwy ad jest nieznany, takie jak transmisja strumieniowa na żywo. Kolejność każdego podziału ad jest określona w formacie #n, gdzie n to liczba całkowita mniejsza od 1. 1 oznacza, że powinien zostać odtworzone ad przy okazji pierwszego, 2 oznacza ad powinna zostać odtworzone przy okazji drugi i tak dalej.

W ramach <AdBreak> elementu, może istnieć jedna <**AdSource**> elementu. <**AdSource**> element zawiera następujące atrybuty:

1. Identyfikator — Określa identyfikator źródła usługi ad
2. allowMultipleAds — wartość logiczna określająca, czy można wyświetlić wiele reklam podczas podziału ad
3. followRedirects — opcjonalna wartość logiczna określająca, czy należy przestrzegać odtwarzacza wideo przekierowuje w odpowiedzi usługi ad

<**AdSource**> element udostępnia odtwarzacz odpowiedzi ad wbudowanego lub odwołanie do odpowiedzi ad. Nazwa może zawierać jedną z następujących elementów:

* <VASTAdData> Wskazuje, że odpowiedź PRZEWAŻAJĄCA ad jest osadzony w pliku VMAP
* <AdTagURI> Identyfikator URI, który odwołuje się do odpowiedzi ad z innego systemu
* <CustomAdData> -dowolny ciąg, który reprezentuje-PRZEWAŻAJĄCA odpowiedzi

W tym przykładzie odpowiedzi w wierszu ad zostanie określony z <VASTAdData> element, który zawiera odpowiedzi PRZEWAŻAJĄCA ad. Aby uzyskać więcej informacji na temat innych elementów zobacz [VMAP](http://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

<**AdBreak**> element może również zawierać jedną <**TrackingEvents**> elementu. <**TrackingEvents**> element służy do śledzenia początek lub koniec podziału ad lub czy wystąpił błąd podczas podziału ad. <**TrackingEvents**> elementu zawiera jeden lub więcej <**śledzenia**> elementów, z których każdy określa zdarzenia śledzenia i śledzenia identyfikatora URI. Zdarzenia śledzenia możliwe są:

1. breakStart — śledzi początku podziału ad
2. breakEnd — sprawdzenie jego ukończenia podziału ad
3. Błąd — śledzi wystąpił błąd, który wystąpił podczas podziału ad

W poniższym przykładzie przedstawiono plik VMAP określający zdarzenia śledzenia

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

Aby uzyskać więcej informacji na temat <**TrackingEvents**> elementu i jego elementów podrzędnych, zobacz http://iab.org/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Przy użyciu abstrakcyjny nośnika, sekwencjonowania pliku szablonu (MASZTÓW)
Plik MASZTÓW umożliwia określenie wyzwalaczy, które określają, kiedy jest wyświetlany przy użyciu usług ad. Oto przykładowy plik MASZTÓW, który zawiera wyzwalacze ad zbiorczego sprzed, ad zbiorczego pośredniej i ad po zbiorczego.

```xml
    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
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


Rozpoczyna się od pliku MASZTÓW **MASZTÓW** element, który zawiera jeden **wyzwalaczy** elementu. <triggers> Elementu zawiera jeden lub więcej **wyzwalacza** elementów, które określają, kiedy można odtwarzać przy użyciu usług ad. 

**Wyzwalacza** element zawiera **startConditions** element Określ rozpoczęcia ad do odtwarzania. **StartConditions** elementu zawiera jeden lub więcej <condition> elementów. Podczas każdego <condition> zwraca wartość true, wyzwalacz zainicjowaniu lub odwołane, w zależności od czy <condition> jest zawarty w **startConditions** lub **endConditions** — element odpowiednio. Gdy wiele <condition> elementy znajdują się, są traktowane jako niejawne OR, warunki obliczane do wartości true spowoduje, że trigger, aby zainicjować. <condition> elementy mogą być zagnieżdżone. Gdy podrzędny <condition> elementy są zdefiniowane, są traktowane jak i niejawne, wszystkie warunki musi zwrócić wartość true dla wyzwalacza do zainicjowania. <condition> Element zawiera następujące atrybuty, które definiują warunek: 

1. **Typ** — Określa typ warunku, zdarzenia lub właściwości
2. **Nazwa** — nazwa właściwości lub zdarzeń, które mają być użyte podczas obliczania
3. **wartość** — wartość, która będzie porównywany właściwości
4. **operator** — operacji do użycia podczas obliczania: EQ (równości), NEQ (różne), GTR (większe), GEQ (większe lub równe), LT (poniżej), LEQ (mniejsze niż lub równe), MOD (modulo)

**endConditions** również zawierać <condition> elementów. Jeśli wynikiem warunku jest PRAWDA wyzwalacza zostanie zresetowany. <trigger> Zawiera również element <sources> element, który zawiera co najmniej jeden <source> elementów. <source> Elementy Definiowanie identyfikator URI odpowiedzi usługi ad i typ odpowiedzi ad. W tym przykładzie identyfikatora URI znajduje się na OGROMNĄ odpowiedzi. 

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

### <a name="using-video-player-ad-interface-definition-vpaid"></a>Przy użyciu odtwarzacza wideo-Ad definicji interfejsu (VPAID)
VPAID to interfejs API umożliwiający jednostki ad pliku wykonywalnego do komunikowania się z odtwarzacza wideo. Dzięki temu ad wysokiej interaktywnego środowiska. Użytkownik może interakcyjnie ad i ad może odpowiadać na akcje wykonywane przez Podgląd. Na przykład ad mogą być wyświetlane przyciski umożliwiające użytkownika wyświetlić więcej informacji lub dłużej wersji ad. Odtwarzacza wideo musi obsługiwać interfejs API VPAID i ad pliku wykonywalnego musi implementować interfejs API. Gdy odtwarzacza zażąda się, że usługi ad z serwera ad serwera mogą odpowiadać za pomocą PRZEWAŻAJĄCA odpowiedzi, który zawiera VPAID ad.

Wykonywalny ad jest tworzony w kodu, który musi zostać wykonana w środowisko uruchomieniowe, takie jak Adobe Flash™ lub JavaScript, które mogą być wykonywane w przeglądarce sieci web. Po powrocie z serwera ad PRZEWAŻAJĄCA odpowiedzi zawierające VPAID ad wartość apiFramework atrybutu w <MediaFile> element musi być "VPAID". Ten atrybut określa, że zawarte ad jest VPAID ad pliku wykonywalnego. Atrybut type musi mieć ustawioną typ MIME pliku wykonywalnego, takie jak "application/x-shockwave-flash" lub "application/x-javascript". Poniższy fragment kodu przedstawia XML <MediaFile> element na podstawie PRZEWAŻAJĄCA odpowiedzi zawierające VPAID ad pliku wykonywalnego. 

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

Można zainicjować pliku wykonywalnego ad przy użyciu <AdParameters> w elemencie <Linear> lub <NonLinear> elementów w PRZEWAŻAJĄCA odpowiedzi. Aby uzyskać więcej informacji na temat <AdParameters> elementu, zobacz [3.0 PRZEWAŻAJĄCA](http://www.iab.net/media/file/VASTv3.0.pdf). Aby uzyskać więcej informacji na temat interfejsu API VPAID, zobacz [VPAID 2.0](http://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Wdrażanie systemu Windows lub Windows Phone 8 Player z obsługą usługi Ad
Platforma Microsoft Media: Framework Player dla systemu Windows 8 i Windows Phone 8 zawiera kolekcję przykładowej aplikacji, które opisano sposób wdrożenia aplikacji odtwarzacza wideo, za pomocą środowiska. Możesz pobrać Player Framework i przykłady z [Framework Player dla systemu Windows 8 i Windows Phone 8](https://playerframework.codeplex.com).

Po otwarciu rozwiązania Microsoft.PlayerFramework.Xaml.Samples pojawi się liczba folderów w ramach projektu. Folder reklamy zawiera przykładowy kod dotyczą tworzenia odtwarzacza wideo z obsługą usługi ad. Wewnątrz reklamy folder jest XAML/cs pliki, które przedstawiają sposób wstawiania reklam w inny sposób. Poniższa lista zawiera opis każdego:

* AdPodPage.xaml przedstawia sposób wyświetlania pod ad.
* AdSchedulingPage.xaml pokazano, jak zaplanować reklam.
* FreeWheelPage.xaml przedstawia sposób użycia wtyczki FreeWheel można zaplanować reklam.
* MastPage.xaml pokazano, jak zaplanować reklam z plikiem MASZTÓW.
* ProgrammaticAdPage.xaml pokazano, jak programowo zaplanować reklam do klipu wideo.
* ScheduleClipPage.xaml pokazano, jak zaplanować ad bez PRZEWAŻAJĄCA pliku.
* VastLinearCompanionPage.xaml pokazuje sposób wstawiania w układzie liniowych i ad pomocnika.
* VastNonLinearPage.xaml pokazuje, jak można wstawić ad liniowej.
* VmapPage.xaml pokazano, jak określić reklam z plikiem VMAP.

Każda z tych próbek używa klasy MediaPlayer zdefiniowane przez platformę player. Większość przykładów za pomocą wtyczki obsługę różnych formatach odpowiedzi ad. Przykładowe ProgrammaticAdPage programowo współdziała z wystąpienia elementu MediaPlayer.

### <a name="adpodpage-sample"></a>Przykładowe AdPodPage
W przykładzie użyto AdSchedulerPlugin, aby zdefiniować, kiedy należy wyświetlić przy użyciu usług ad. W tym przykładzie anonsu pośredniej zbiorczego jest zaplanowane do odtwarzania po 5 sekund. Pod ad (grupa reklam, aby wyświetlić w kolejności) jest określona w pliku PRZEWAŻAJĄCA zwrócony z serwera usługi ad. Identyfikator URI do OGROMNYCH plików jest określona w <RemoteAdSource> elementu.

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

Aby uzyskać więcej informacji o AdSchedulerPlugin, zobacz [reklamy w ramach odtwarzacza w systemie Windows 8 i Windows Phone 8](http://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
W tym przykładzie użyto także AdSchedulerPlugin. Planowana trzy reklam, ad wstępne zbiorczego ad zbiorczego pośredniej i ad po zbiorczego. Identyfikator URI do VAST dla każdej usługi ad jest określona w <RemoteAdSource> elementu.

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
W przykładzie użyto FreeWheelPlugin, która określa atrybut źródłowy, który określa identyfikator URI, który wskazuje plik SmartXML, który określa ad zawartości, a także informacje o planowaniu ad.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage
W przykładzie użyto MastSchedulerPlugin, która pozwala na użycie pliku MASZTÓW. Atrybut źródłowy określa lokalizację pliku MASZTÓW.
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

Plik ProgrammaticAdPage.xaml.cs tworzy AdHandlerPlugin dodaje TimelineMarker, aby określić ad powinna być wyświetlana, a następnie dodanie obsługi dla zdarzenia MarkerReached ładuje RemoteAdSource określenie identyfikatora URI do OGROMNYCH plików, a następnie odgrywa ad.

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
W przykładzie użyto AdSchedulerPlugin można zaplanować ad pośredniej zbiorczego, określając plik wmv, który zawiera usługi ad.

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
W tym przykładzie przedstawiono metodę zastosowania AdSchedulerPlugin można zaplanować pośredniej zbiorczego liniowej usługi ad z ad pomocnika. <RemoteAdSource> Element określa lokalizację pliku PRZEWAŻAJĄCA.

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
W przykładzie użyto AdSchedulerPlugin można zaplanować liniowej i inne usługi ad. Lokalizacja pliku PRZEWAŻAJĄCA zostanie określony z <RemoteAdSource> elementu.

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
W przykładzie użyto VmapSchedulerPlugin można zaplanować przy użyciu pliku VMAP reklam. Identyfikator URI do pliku VMAP jest określony w atrybucie źródła <VmapSchedulerPlugin> elementu.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Implementacja systemu iOS odtwarzacza wideo z obsługą usługi Ad
Platforma Microsoft Media: Framework odtwarzacza dla systemu iOS zawiera kolekcję przykładowej aplikacji, które opisano sposób wdrożenia aplikacji odtwarzacza wideo, za pomocą środowiska. Możesz pobrać Player Framework i przykłady z [Azure Media Player Framework](https://github.com/Azure/azure-media-player-framework). Strona github zawiera łącze do stron typu Wiki, zawierający dodatkowe informacje w ramach odtwarzacza i wprowadzenie do przykładu player: [Azure Media Player Wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Planowanie reklam z VMAP
Poniższy przykład przedstawia sposób tworzenia harmonogramu przy użyciu pliku VMAP reklam.

```csharp
    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
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
Poniższy przykład pokazuje, jak można zaplanować późne wiązanie PRZEWAŻAJĄCA ad.


```csharp
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
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

   Poniższy przykład pokazuje, jak można zaplanować wczesne ad PRZEWAŻAJĄCA powiązania.

```csharp
    //Example:4 Schedule an early binding VAST ad
    //Download the VAST file
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]])
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

Poniższy przykład przedstawia sposób wstawiania ad za pomocą nierównej Wytnij edycji (rz)

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

Poniższy przykład przedstawia sposób tworzenia harmonogramu pod ad.

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

Poniższy przykład pokazuje, jak można zaplanować-trwałe ad pośredniej zbiorczego. Ad trwałe tylko zostanie odtworzony po niezależnie od wszelkich znalezienia wykonuje przeglądarki.

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

Poniższy przykład pokazuje, jak można zaplanować jego umocowania ad pośredniej zbiorczego. Trwałe ad jest wyświetlany za każdym razem, osiągnięty określonego punktu w wideo na osi czasu.

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

Poniższy przykład pokazuje, jak można zaplanować ad końcu pliku.

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

Poniższy przykład pokazuje, jak można zaplanować ad wstępne zbiorczego.

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

Poniższy przykład pokazuje, jak można zaplanować ad nakładki pośredniej zbiorczego.

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

## <a name="see-also"></a>Zobacz też
[Opracowywanie aplikacji odtwarzacza wideo](media-services-develop-video-players.md)

