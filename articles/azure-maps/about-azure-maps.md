---
title: Przegląd | Mapy platformy Microsoft Azure
description: Dowiedz się więcej o usługach i możliwościach w mapach platformy Microsoft Azure i o tym, jak z nich korzystać w aplikacjach.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: fdac12350db785e6194cd3d057f4d2adfefa5969
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80335730"
---
# <a name="what-is-azure-maps"></a>Co to jest usługa Azure Maps?

Usługa Azure Maps to zbiór usług geoprzestrzennych, które używają świeżych danych mapowania w celu zapewnienia kontekstu geograficznego do aplikacji sieci Web i aplikacji mobilnych. Usługa Azure Maps zapewnia:

* INTERFEJSY API REST do renderowania map w wielu stylach i na zdjęciach satelitarnych.''
* Wyszukiwanie usług w celu lokalizowania adresów, miejsc i interesujących miejsc na całym świecie.
* Różne opcje routingu; takie jak point-to-point, multipoint, multipoint optimization, izochrone, pojazdy użytkowe, ruch pod wpływem i routing matrycy.
* Widok przepływu ruchu i widok zdarzeń dla aplikacji, które wymagają informacji o ruchu drogowym.
* Usługa mobilności w celu żądania logistyki transportu publicznego, planowania tras w czasie rzeczywistym i żądania informacji o alternatywnych środkach transportu.
* Strefy czasowe i usługi geolokalizacji oraz konwertowanie lokalizacji na strefy czasowe.
* Usługa geofencingu i mapowanie magazynu danych z informacjami o lokalizacji hostowanymi na platformie Azure. 
* Analiza lokalizacji dzięki analizie geoprzestrzennej. 

Ponadto usługi Usługi Azure Maps są dostępne za pośrednictwem zestawu SDK sieci Web lub sdk systemu Android. Te narzędzia pomagają deweloperom szybko opracowywać i skalować rozwiązania, które integrują informacje o lokalizacji z rozwiązaniami platformy Azure. 

Możesz zarejestrować bezpłatne [konto usługi Azure Maps](https://azure.microsoft.com/services/azure-maps/) i rozpocząć tworzenie.

W poniższym filmie wideo wyjaśniono dokładnie działanie usługi Azure Maps:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Kontrolki mapy

### <a name="web-sdk"></a>Zestaw SDK sieci Web

Zestaw Azure Maps Web SDK umożliwia dostosowywanie interaktywnych map za pomocą własnej zawartości i obrazów. Możesz użyć tej interaktywnej mapy zarówno dla aplikacji internetowych, jak i mobilnych. Formant mapy korzysta z webgl, dzięki czemu można renderować duże zestawy danych o wysokiej wydajności. Programowanie za pomocą narzędzia SDK przy użyciu języka JavaScript lub TypeScript.

![Przykładowa mapa zmiany populacji](media/about-azure-maps/intro_web_map_control.png)

### <a name="android-sdk"></a>Android SDK

Użyj narzędzia Azure Maps Android SDK do tworzenia aplikacji mapowania mobilnego. 

![Mapowanie przykładów na urządzeniu przenośnym](media/about-azure-maps/android_sdk.png)

## <a name="services-in-azure-maps"></a>Usługi Azure Maps

Usługa Azure Maps składa się z następujących dziewięciu usług, które mogą zapewnić kontekst geograficzny aplikacji platformy Azure.

### <a name="data-service"></a>Usługa Data Service

Dane są niezbędne dla map. Usługa danych umożliwia przekazywanie i przechowywanie danych geoprzestrzennych do użytku z operacjami przestrzennymi lub kompozycją obrazu.  Przybliżenie danych klientów do usługi Azure Maps zmniejszy opóźnienia, zwiększy produktywność i stworzy nowe scenariusze w aplikacjach. Aby uzyskać szczegółowe informacje na temat tej usługi, zobacz [dokumentację interfejsu API usługi danych](https://docs.microsoft.com/rest/api/maps/data).

### <a name="mobility-service"></a>Usługa mobilności

Usługa Azure Maps Mobility umożliwia planowanie podróży w czasie rzeczywistym. Zwraca najlepsze możliwe opcje trasy i zapewnia różne rodzaje trybów podróży. W przypadku obszarów metra (miasta) środki te mogą obejmować spacery, jazdę na rowerze i transport publiczny. Możesz poprosić o trasę tranzytową, geometrię linii, listy przystanków, zaplanowane przyloty, przyjazdy w czasie rzeczywistym i alerty serwisowe.

Usługa umożliwia również wyszukiwanie określonych typów obiektów wokół lokalizacji. Użytkownicy mogą wyszukiwać udostępnione rowery, skutery lub samochody w pobliżu. Użytkownicy mogą poprosić o liczbę dostępnych rowerów w najbliższej stacji dokującej i wyszukać dostępne pojazdy do udostępniania przejazdów. Użytkownicy mogą również znaleźć szczegółowe informacje, takie jak przyszła dostępność pojazdów i aktualny poziom paliwa.

Aby dowiedzieć się więcej o usłudze, zobacz [dokumentację interfejsu API mobilności.](https://docs.microsoft.com/rest/api/maps/mobility)

### <a name="render-service"></a>Render Service

Usługa Renderowania pomaga deweloperom tworzyć aplikacje internetowe i mobilne z funkcjami mapowania. Usługa używa rastrowych obrazów graficznych o wysokiej jakości — dostępnych z 19 poziomami powiększenia — lub w pełni konfigurowalnych obrazów map w formacie wektorowym.

![Przykład mapy z usługi Renderowanie](media/about-azure-maps/intro_map.png)

Usługa Render Service oferuje teraz interfejsy API w wersji zapoznawczej umożliwiające deweloperom pracę z obrazami satelitarnymi. Aby uzyskać więcej informacji, przeczytaj [dokumentację interfejsu API renderowania](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Route Service

Usługa Route Service zawiera niezawodne obliczenia geometryczne rzeczywistej infrastruktury oraz tras dla wielu środków transportu. Usługa umożliwia deweloperom obliczanie kierunków w wielu trybach podróży, takich jak samochód, ciężarówka, rower lub spacer. Usługa uwzględnia również dane wejściowe, takie jak warunki drogowe, ograniczenia wagowe lub transport materiałów niebezpiecznych.

![Przykład mapy z usługi Trasa](media/about-azure-maps/intro_route.png)

Usługa Trasa oferuje podgląd zaawansowanych funkcji, takich jak: 

* Przetwarzanie wsadowe wielu żądań marszruty.
* Matryce czasu podróży i odległości między zestawem pochodzenia i miejsc docelowych.
* Znajdowanie tras lub odległości, które użytkownicy mogą podróżować na podstawie wymagań dotyczących czasu lub paliwa. 

Aby uzyskać szczegółowe informacje na temat możliwości routingu, przeczytaj [dokumentację interfejsu API trasy](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Search Service

Usługa wyszukiwania ułatwia deweloperom wyszukiwanie adresów, miejsc, wizyt firm według nazwy lub kategorii oraz innych informacji geograficznych. Usługa wyszukiwania może odwracać adresy [geokodowania](https://en.wikipedia.org/wiki/Reverse_geocoding) i przecinać ulice na podstawie szerokości i długości geograficznej.

![Przykład wyszukiwania na mapie](media/about-azure-maps/intro_search.png)

Usługa Wyszukiwania oferuje również zaawansowane funkcje, takie jak:

* Szukaj wzdłuż trasy.
* Szukaj w szerszym obszarze.
* Wy partii grupy żądań wyszukiwania.
* Wyszukaj większy obszar zamiast punktu lokalizacji. 

Interfejsy API do wyszukiwania grupowego i wyszukiwania obszaru są obecnie dostępne w wersji zapoznawczej. Aby uzyskać więcej informacji na temat możliwości wyszukiwania, przeczytaj [dokumentację interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations-service"></a>Usługa operacji przestrzennych

Usługa Operacje przestrzenne usługi Azure Maps pobiera informacje o lokalizacji. Szybko analizuje informacje o lokalizacji, aby pomóc klientom informować klientów o bieżących wydarzeniach, które mają miejsce w czasie i przestrzeni. Umożliwia niemal analizę w czasie rzeczywistym i modelowanie predykcyjne zdarzeń. 

Usługa umożliwia klientom zwiększenie ich lokalizacji analizy z biblioteki wspólnych geoprzestrzennych obliczeń matematycznych. Typowe obliczenia obejmują najbliższy punkt, dużą odległość okręgu i bufory. Aby dowiedzieć się więcej o usłudze i różnych funkcjach, przeczytaj [dokumentację interfejsu API operacji przestrzennych](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Time Zone Service

Usługa Strefa czasowa umożliwia wykonywanie zapytań o bieżące, historyczne i przyszłe informacje o strefie czasowej. Można użyć par szerokości i długości geograficznej lub identyfikatora [IANA](https://www.iana.org/). Usługa Strefa czasowa umożliwia również:

* Konwertowanie identyfikatorów stref czasowych systemu Microsoft Windows na strefy czasowe IANA.
* Pobieranie przesunięcia strefy czasowej do czasu UTC.
* Uzyskiwanie bieżącego czasu w wybranej strefie czasowej. 

Typowa odpowiedź JSON dla kwerendy do usługi strefa czasowa wygląda następująco:

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

Aby uzyskać szczegółowe informacje na temat tej usługi, przeczytaj [dokumentację interfejsu API strefy czasowej](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Traffic Service

Usługa Ruch to pakiet usług sieci web, których deweloperzy mogą używać w aplikacjach sieci Web lub aplikacjach mobilnych, które wymagają informacji o ruchu drogowym. Usługa udostępnia dwa typy danych:

* Przepływ ruchu: obserwowane w czasie rzeczywistym prędkości i czasy podróży dla wszystkich kluczowych dróg w sieci.
* Zdarzenia drogowe: aktualny widok korków i zdarzeń w sieci drogowej.

![Przykład mapy z informacjami o ruchu drogowym](media/about-azure-maps/intro_traffic.png)

Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API ruchu](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location-service"></a>Usługa ip do lokalizacji

Użyj usługi IP do lokalizacji, aby wyświetlić podgląd pobranego dwuliterowego kodu kraju dla adresu IP. Ta usługa może pomóc zwiększyć komfort użytkownika, udostępniając niestandardową zawartość aplikacji na podstawie lokalizacji geograficznej.

Aby uzyskać więcej informacji na temat usługi IP do lokalizacji w interfejsie API REST, przeczytaj [dokumentację interfejsu API geolokalizacji usługi Azure Maps.](https://docs.microsoft.com/rest/api/maps/geolocation)

## <a name="programming-model"></a>Model programowania

Usługa Azure Maps została stworzona z myślą o mobilności i może pomóc w opracowywaniu aplikacji między platformami. Używa modelu programowania, który jest niezależny od języka i obsługuje dane wyjściowe JSON za pośrednictwem [interfejsów API REST](https://docs.microsoft.com/rest/api/maps/).

Ponadto usługa Azure Maps oferuje wygodną [kontrolę mapy JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) z prostym modelem programowania. Program jest szybki i łatwy zarówno w aplikacjach internetowych, jak i mobilnych.

## <a name="usage"></a>Sposób użycia

Uzyskiwanie dostępu do usług usługi Azure Maps to kwestia przechodzenia do [witryny Azure portal](https://portal.azure.com) i tworzenia konta usługi Azure Maps.

Usługa Azure Maps korzysta ze schematu uwierzytelniania opartego na kluczach. Twoje konto jest wyposażone w dwa klucze już wygenerowane dla Ciebie, użyj albo kluczy. Rozpocznij integrację tych funkcji lokalizacji w aplikacji i wykonuj żądania do usług Usługi Azure Maps.

Uwaga — usługa Azure Maps udostępnia klientowi zapytania adresowe/lokalizacyjne ("Zapytania") z usługą TomTom innej firmy w celu tworzenia funkcji mapowania. Zapytania nie są połączone z żadnym klientem ani użytkownikiem końcowym po udostępnieniu tomTom i nie mogą być używane do identyfikowania osób. Firma Microsoft jest obecnie w trakcie dodawania urządzenia TomTom do listy podwykonawców usług online. Należy pamiętać, że mobilność i usługi pogodowe, które obejmują integrację z Moovit i AccuWeather są obecnie w [PODGLĄDZIE.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="supported-regions"></a>Obsługiwane regiony

Interfejsy API usługi Azure Maps są obecnie dostępne we wszystkich krajach i regionach z wyjątkiem następujących:

* Chiny
* Korea Południowa

Sprawdź, czy lokalizacja bieżącego adresu IP znajduje się w obsługiwanym kraju.

## <a name="next-steps"></a>Następne kroki

Wypróbuj przykładową aplikację prezentują usługę Azure Maps:

> [!div class="nextstepaction"]
> [Szybki start: tworzenie aplikacji sieci Web](quick-demo-map-app.md)

Bądź na bieżąco z usługami Azure Maps: 

> [!div class="nextstepaction"]
> [Blog usługi Azure Maps](https://azure.microsoft.com/blog/topics/azure-maps/)
