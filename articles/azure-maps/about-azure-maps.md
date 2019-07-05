---
title: Omówienie usługi Azure Maps | Microsoft Docs
description: Wprowadzenie do usługi Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 02/04/2019
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 5f41263113568cf9f3771119135be8db37119181
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442940"
---
# <a name="what-is-azure-maps"></a>Co to jest usługa Azure Maps?

Usługi Azure Maps to zbiór usług geoprzestrzennych, korzystających z aktualnymi danymi w celu zapewnienia dokładnego kontekstu geograficznego aplikacjom internetowym i mobilnym. Usługa Azure Maps udostępnia:

* Interfejsy API REST dla map w wiele stylów i obrazów satelitarnych renderowania.
* Wyszukuje adresy, miejsca i punktów orientacyjnych na całym świecie.
* Routing typu punkt-punkt, multipoint, multipoint optymalizacji, isochrone, pojazdów użytkowych, ruchu wpływ i macierzy routing; Wyświetlanie przepływu ruchu i zdarzeń.
* Usługi mobilności dla żądania przesyłania publicznych i alternatywne rodzaje transportu (np. udostępnianie roweru scooter udziału i udziału samochód) i planowanie tras w czasie rzeczywistym. 
* Ustanowienie lokalizacji użytkownika przy użyciu geograficzną i konwertowanie lokalizacji do stref czasowych. 
* Usługi geofencing i Mapa magazynu danych przy użyciu informacji o lokalizacji hostowanych na platformie Azure. 
* Analizy lokalizacji za pomocą analizy danych geoprzestrzennych. 

Oprócz interfejsów API REST usługi Azure Maps są dostępne za pośrednictwem zestawu SDK sieci Web lub zestawu SDK systemu Android. Te narzędzia ułatwiają deweloperom szybko opracowywać i skalowania rozwiązań integrujących informacje o lokalizacji w rozwiązaniach platformy Azure. 

Możesz zarejestrować się bezpłatne [konta usługi Azure Maps](https://azure.microsoft.com/services/azure-maps/) i zacznij programować.

W poniższym filmie wideo wyjaśniono dokładnie działanie usługi Azure Maps:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Kontrolki mapy

### <a name="web-sdk"></a>Zestaw SDK sieci Web

Zestaw SDK sieci Web mapy platformy Azure umożliwia dostosowanie interaktywne mapy za pomocą własnych zawartości i obrazów do wyświetlania w sieci web lub aplikacji dla urządzeń przenośnych. Ten formant korzysta z WebGL, dzięki czemu można renderować dużych zestawów danych o wysokiej wydajności. Programowanie przy użyciu zestawu SDK przy użyciu języka JavaScript lub TypeScript.

![Przykładowa mapa populacji zmiany](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

Użyj usługi Azure Maps SDK dla systemu Android do tworzenia aplikacji mobilnych mapowania. 

![Przykłady mapy na urządzeniu przenośnym](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Usługi Azure Maps

Usługi Azure Maps składa się z następujących usług dziewięć zapewniające kontekstu geograficznego aplikacjom platformy Azure.

### <a name="data-service"></a>Usługa danych

Dane są technologii dla społeczności maps. Usługa danych, przekazywanie i przechowywanie danych geoprzestrzennych do użytku z przestrzennego operacji lub skład obrazu.  Przybliżeniu danych klienta do usługi Azure Maps spowoduje zmniejszenie opóźnień, zwiększyć produktywność i tworzenie nowych scenariuszy w aplikacjach. Aby uzyskać szczegółowe informacje w tej usłudze, zobacz [dokumentacji interfejsu API usługi danych](https://docs.microsoft.com/rest/api/maps/data).

### <a name="mobility-service"></a>Usługa mobilności

Usługa Azure Maps mobilności umożliwia planowanie podróży w czasie rzeczywistym. Ono zwraca najlepsze opcje możliwe trasy i zapewnia różnych trybach podróży. Obszarów metro (Miasto) tych trybów mogą obejmować zalet jazdę i przesyłania publicznych. Deweloperzy mogą żądać przesyłania trasy podróży szczegóły, takie jak Geometria linii, listy zostanie ono zatrzymane, zaplanowane i przyjęcia w czasie rzeczywistym i alerty usług.

Usługa umożliwia również wyszukiwanie określonych typów obiektów, takich jak udostępnione rowerów, skutery lub samochodów wokół lokalizacji. Użytkownicy mogą zażądać liczbę rowerów udostępnionego dostępne są pozostawiane w najbliższej dokowania. Mogą wyszukiwania dostępny udział samochodu pojazdów i szczegółowe informacje, takie jak przyszłych dostępności i bieżący poziom we wprowadzaniu.

Aby dowiedzieć się więcej o usłudze, zobacz [dokumentacji interfejsu API mobilności](https://docs.microsoft.com/rest/api/maps/mobility).

### <a name="render-service"></a>Render Service

Usługa Render service ułatwia deweloperom tworzenie aplikacji internetowych i mobilnych związanych z mapowaniem. Usługa używa rastrowych obrazów graficznych o wysokiej jakości — dostępnych z 19 poziomami powiększenia — lub w pełni konfigurowalnych obrazów map w formacie wektorowym.

![Przykład mapy usługi renderowania](media/about-azure-maps/Introduction_Map.png)

Usługa Render Service oferuje teraz interfejsy API w wersji zapoznawczej umożliwiające deweloperom pracę z obrazami satelitarnymi. Aby uzyskać więcej informacji, przeczytaj [dokumentację API renderowania](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Route Service

Usługa Route Service zawiera niezawodne obliczenia geometryczne rzeczywistej infrastruktury oraz tras dla wielu środków transportu. Usługa pozwala deweloperom obliczać trasy dla wielu sposobów przemieszczania się, takich jak samochód osobowy, ciężarówka, rower czy wędrówka. Usługa może również brać pod uwagę dane wejściowe, takie jak warunki na drogach, ograniczenia co do masy pojazdu lub transport materiałów niebezpiecznych.

![Przykład mapy z usługi Route service](media/about-azure-maps/Introduction_Route.png)

Usługi Route service oferuje wersję zapoznawczą zaawansowane funkcje, takie jak: 

* Przetwarzanie wsadowe wielu żądań trasy.
* Macierze z podróży czasu i odległości między zestaw źródeł i miejsc docelowych.
* Znajdowanie tras lub odległości, które użytkownicy mogą być przesyłane na podstawie czasu lub paliwo wymagań. 

Aby uzyskać szczegółowe informacje dotyczące możliwości routingu, przeczytaj [dokumentacji interfejsu API usługi Route](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Search Service

Usługa wyszukiwania pomaga deweloperom wyszukiwanie adresów, miejsc, list biznesowych według nazwy lub kategorii i innych informacji geograficznych. Usługa Search service może [odwrotnego geocode](https://en.wikipedia.org/wiki/Reverse_geocoding) adresów i ulice w oparciu długości i szerokości geograficzne.

![Przykład wyszukiwania na mapie](media/about-azure-maps/Introduction_Search.png)

Usługa Search udostępnia również zaawansowane funkcje, takie jak:

* Wyszukaj wzdłuż trasy.
* Wyszukiwanie w obszarze szersze.
* Batch grupę realizowania żądań wyszukiwania.
* Wyszukaj większy obszar zamiast w punkcie lokalizacji. 

Interfejsy API do wyszukiwania grupowego i wyszukiwania obszaru są obecnie dostępne w wersji zapoznawczej. Aby uzyskać szczegółowe informacje na temat możliwości wyszukiwania, przeczytaj [dokumentacji interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations-service"></a>Usługa Operations przestrzenne

Usługa Azure Maps przestrzenne operacje przyjmuje informacji o lokalizacji i analizuje je na bieżąco, aby pomóc natychmiastowego informowania klientów trwającą zdarzeń w zakresie czasu i miejsca. Dzięki temu niemal w czasie rzeczywistym analiz i modelowania predykcyjnego zdarzeń. 

Usługa umożliwia klientom zwiększyć ich analizy lokalizacji za pomocą biblioteki typowych obliczeń matematycznych dane geograficzne, w tym najbliższego punktu, najkrótszej odległości i buforów. Aby dowiedzieć się więcej o usłudze oraz informacje o różnych funkcjach, przeczytaj [dokumentacji przestrzenne operacje interfejsu API](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Time Zone Service

Time Zone service umożliwia informacje o strefie czasowej bieżące, historyczne i przyszłe kwerendy za pomocą obu pary szerokości/długości geograficznej lub [IANA ID](https://www.iana.org/). Usługa Time Zone Ponadto umożliwia:

* Konwertowanie identyfikatorów strefy czasowej Microsoft Windows na strefy czasowe IANA.
* Pobieranie przesunięcia strefy czasowej na czas UTC.
* Pobieranie bieżącego czasu w strefie czasowej. 

Typowa odpowiedź JSON na zapytanie do Time Zone service wygląda jak w następującym przykładzie:

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

Aby uzyskać szczegółowe informacje w tej usłudze, przeczytaj [dokumentacji interfejsu API w strefie czasowej](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Traffic Service

Usługa Traffic service to zestaw usług sieci web, które deweloperzy mogą używać do tworzenia aplikacji internetowych i mobilnych wymagających informacji o ruchu. Usługa udostępnia dwa typy danych:

* Przepływ ruchu: W czasie rzeczywistym obserwować prędkości i czasy podróży dla wszystkich kluczowych dróg w sieci.
* Zdarzenia ruchu: Aktualny widok na drogach i zdarzenia dotyczące sieci dróg.

![Przykład mapy za pomocą informacji o ruchu](media/about-azure-maps/Introduction_Traffic.png)

Aby uzyskać więcej informacji, zobacz [dokumentacji interfejsu API ruchu](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location-service"></a>Adres IP, aby usługa lokalizacji

Użyj adresu IP do lokalizacji usługi, aby kod pobrany kraju dwuliterowych dla adresu IP w wersji zapoznawczej. Ta usługa może pomóc dostosować i poprawić funkcjonalność z perspektywy użytkownika, zapewniając niestandardowych aplikacji zawartości na podstawie lokalizacji geograficznej.

Aby uzyskać szczegółowe informacje na temat interfejsów API REST dla adresu IP do lokalizacji usługi, przeczytaj [dokumentacji interfejsu API usługi Azure Maps Geolokalizacja](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Model programowania

Usługi Azure Maps zaprojektowano pod kątem mobilności i mogą pomóc Ci tworzyć aplikacje dla wielu platform. Używa modelu programowania, który jest niezależny od języka i obsługuje dane wyjściowe JSON za pośrednictwem [interfejsów API REST](https://docs.microsoft.com/rest/api/maps/).

Ponadto usługa Azure Maps oferuje wygodną [kontrolkę mapy JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) z prostym modelem programowania, która umożliwia szybkie i łatwe tworzenie aplikacji internetowych i mobilnych.

## <a name="usage"></a>Sposób użycia

Uzyskiwanie dostępu do usług Azure Maps jest kwestią zamierza [witryny Azure portal](https://portal.azure.com) i utworzenie konta usługi Azure Maps.

Usługa Azure Maps korzysta ze schematu uwierzytelniania opartego na kluczach. Twoje konto ma dwa klucze, które już wygenerowany dla Ciebie. Rozpocznij integrowanie tych możliwości obsługi lokalizacji w Twojej aplikacji przez użycie dowolnego z kluczy w żądaniu wysłanym do usługi Azure Maps.

## <a name="supported-regions"></a>Obsługiwane regiony

Obecnie dostępne we wszystkich krajach/regionach z wyjątkiem tych są interfejsów API usługi Azure Maps:

* Argentyna
* Chiny
* Indie
* Maroko
* Pakistan
* Korea Południowa

Upewnij się, że lokalizacja bieżącego adresu IP nie znajduje się w jeden z nieobsługiwanych krajów/regionów.

## <a name="next-steps"></a>Kolejne kroki

Wypróbuj przykładową aplikację, w których usługi Azure Maps:

> [!div class="nextstepaction"]
> [Szybki start: Tworzenie aplikacji sieci web](quick-demo-map-app.md)

Bądź na bieżąco na usługi Azure Maps: 

> [!div class="nextstepaction"]
> [Blog usługi Azure Maps](https://azure.microsoft.com/blog/topics/azure-maps/)
