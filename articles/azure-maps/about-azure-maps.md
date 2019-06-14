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
ms.openlocfilehash: 8092cd169f93a6815e52517d805941ac7ddcbbc0
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66807516"
---
# <a name="what-is-azure-maps"></a>Co to jest usługa Azure Maps?

Azure Maps to zbiór usług geoprzestrzennych opartych na najświeższych dostępnych danych mapowych, zapewniający dokładny kontekst geograficzny do użycia w aplikacjach internetowych i mobilnych. Usługi Azure Maps składa się z interfejsów API REST w celu renderowania **mapy** w wielu stylów i obrazów satelitarnych **wyszukiwanie** adresy, miejsca i punktów orientacyjnych na świecie; **Routing** point-to-point, multipoint, multipoint optymalizacji, isochrone, pojazdów użytkowych, ruchu wpływ i macierz routing; wyświetlanie przepływu ruchu wiodące w branży i zdarzeń; **Mobilności** usługi żądania przesyłania publicznych, udostępnianie roweru, scooter udziału i samochodu udostępnianie informacji planowanie tras, wykorzystując alternatywne metody transportu i danych w czasie rzeczywistym; ustanowienie lokalizacji użytkownika przy użyciu **Geolokalizacja**; oraz konwertowanie lokalizacji **stref czasowych**, jak również pobieranie czasu, w lokalizacji. Dodatkowo usługi Azure Maps udostępnia usługi dla **Geofencing**, mapy **danych** magazyn — hostingu informacji o lokalizacji na platformie Azure; i **przestrzenne operacje** podanie lokalizacji analizy za pomocą analizy danych geoprzestrzennych. Usługi Azure Maps są dostępne bezpośrednio jako interfejsy API REST lub przy użyciu jednego z naszych niezawodnych zestawów **Web SDK** lub **Android SDK**. Te narzędzia umożliwiają deweloperom szybkie tworzenie i skalowanie rozwiązań integrujących informacje o lokalizacji z rozwiązaniami platformy Azure z poziomu chmury platformy Azure. Załóż bezpłatne [konto usługi Azure Maps](https://azure.microsoft.com/services/azure-maps/) już dzisiaj i rozpocznij tworzenie!

W poniższym filmie wideo wyjaśniono dokładnie działanie usługi Azure Maps:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Kontrolki mapy

### <a name="web-sdk"></a>Zestaw SDK sieci Web

Zestaw SDK sieci Web mapy platformy Azure umożliwia dostosowanie interaktywne mapy za pomocą własnych zawartości i obrazów do wyświetlania w sieci web lub aplikacji dla urządzeń przenośnych. Kontrolka korzysta z technologii WebGL, co umożliwia renderowanie dużych zestawów danych z wysoką wydajnością. Programowanie przy użyciu zestawu SDK przy użyciu języka JavaScript lub TypeScript.

![Zestaw SDK sieci Web usługi Azure Maps](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

Zestaw Azure SDK dla systemu Android mapy umożliwia tworzenie zaawansowanych mapowania mobilnych aplikacji. 

![Zestaw SDK systemu Android z usługi Azure Maps](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Usługi Azure Maps

Usługi Azure Maps składa się z następujących usług dziewięć zapewniające kontekstu geograficznego aplikacjom platformy Azure.

### <a name="data-service"></a>Usługa danych

Dane są imperatywne dla map, a w przybliżeniu danych klienta do usługi Azure Maps usługi spowoduje zmniejszenie opóźnień, zwiększającym produktywność i tworzenie nowych, zaawansowanych scenariuszy na światło się w swoich aplikacjach. Usługa danych umożliwia przekazywanie i do przechowywania danych geoprzestrzennych z przestrzennego operacji lub skład obrazu do zmniejszenia opóźnień, zwiększyć produktywność oraz obsługę nowych scenariuszy w aplikacjach. Aby uzyskać szczegółowe informacje w tej usłudze, odwiedź stronę [interfejsów API usługi danych](https://docs.microsoft.com/rest/api/maps/data) strony.

### <a name="mobility-service"></a>Usługa mobilności

Usługi mobilności mapy platformy Azure zapewnić analizy w czasie rzeczywistym lokalizacji w pobliżu usług przesyłania publicznego, w tym zatrzymuje, informacje dotyczące tras i szacunki czas podróży. Usługa umożliwia wyszukiwanie określonych typów obiektów, takich jak publiczny przesyłania zatrzymuje udostępnionego rowery/skutery/samochodów w całym danej lokalizacji, zwraca zbiór obiektu przesyłania przy użyciu szczegółów obiektu. Usługa umożliwia deweloperom także szczegóły wiersza przesyłania żądania obejmujące podstawowych informacji i dodatkowe informacje, takie jak Geometria linii, lista zostanie ono zatrzymane, zaplanowane i Przyjazdy przesyłania w czasie rzeczywistym i alerty usług. Użytkownicy mogą również żądać liczbę rowerów udostępnionego dostępne są pozostawiane w najbliższej dokowania, żądając dokowania informacji z stacje. Usługa mobilności ma również możliwość wyszukiwania pojazdów udziału dostępne samochód, zwraca szczegóły, takie jak przyszłych dostępności i bieżący poziom paliwa.
Usługa mobilności Maps umożliwia planowania podróży w czasie rzeczywistym, zwracając najlepsze opcje możliwe trasy i zapewnienie różnych trybach podróży, w tym przesyłania ruchomym jazdę i publicznych dostępne w ramach Miasto (Miasto). Ponadto deweloperzy mogą przesyłania trasy podróży szczegóły żądania z dodatkowymi informacjami takich geometrii trasy i szczegółowe harmonogramy trasy podróży.

Aby dowiedzieć się więcej o usłudze oraz informacje o różnych funkcjach, zobacz nasze [dokumentacji interfejsu API](https://docs.microsoft.com/rest/api/maps/mobility)

### <a name="render-service"></a>Render Service

Usługa Render Service umożliwia deweloperom tworzenie aplikacji internetowych i mobilnych związanych z mapowaniem. Usługa używa rastrowych obrazów graficznych o wysokiej jakości — dostępnych z 19 poziomami powiększenia — lub w pełni konfigurowalnych obrazów map w formacie wektorowym.

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

Usługa Render Service oferuje teraz interfejsy API w wersji zapoznawczej umożliwiające deweloperom pracę z obrazami satelitarnymi. Aby uzyskać więcej informacji, przeczytaj artykuł [Azure Maps Render APIs (Interfejsy API usługi Render w usłudze Azure Maps)](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Route Service

Usługa Route Service zawiera niezawodne obliczenia geometryczne rzeczywistej infrastruktury oraz tras dla wielu środków transportu. Usługa pozwala deweloperom obliczać trasy dla wielu sposobów przemieszczania się, takich jak samochód osobowy, ciężarówka, rower czy wędrówka. Usługa może również brać pod uwagę dane wejściowe, takie jak warunki na drogach, ograniczenia co do masy pojazdu lub transport materiałów niebezpiecznych.

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

Usługa Route Service oferuje teraz zaawansowane funkcje w wersji zapoznawczej, takie jak przetwarzanie wsadowe wielu żądań tras, macierzy czasu podróży i odległości między zestawem punktów początkowych i końcowych, a także odnajdowanie tras lub odległości, które można pokonać w określonym czasie lub z określoną ilością paliwa. Aby uzyskać szczegółowe informacje dotyczące możliwości wyznaczania trasy, przeczytaj [Azure Maps Route APIs (Interfejsy API usługi Route w usłudze Azure Maps)](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Search Service

Usługa Search Service umożliwia deweloperom wyszukiwanie adresów, miejsc, firm według nazwy lub kategorii, a także innych informacji geograficznych. Usługa Search Service umożliwia też [odwracanie adresów geokodowania](https://en.wikipedia.org/wiki/Reverse_geocoding) i przechodzenie przez ulice na podstawie długości i szerokości geograficznej.

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

Usługa Search Service umożliwia również dostęp do zaawansowanych funkcji, takich jak wyszukiwanie wzdłuż trasy, wyszukiwanie w ramach większego obszaru, grupowanie żądań wyszukiwania oraz wyszukiwanie większego obszaru zamiast konkretnego punktu. Interfejsy API do wyszukiwania grupowego i wyszukiwania obszaru są obecnie dostępne w wersji zapoznawczej. Aby uzyskać szczegółowe informacje na temat możliwości wyszukiwania, przeczytaj artykuł [Azure Maps Search APIs (Interfejsy API usługi Search w usłudze Azure Maps)](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations"></a>Operacje przestrzenne

Operacji na platformie Azure Maps przestrzenne spowoduje zająć informacji o lokalizacji i Analizuj je na bieżąco, aby ułatwić poinformowanie naszych klientów trwającą zdarzeń w czasie i spację, umożliwiając niemal w czasie rzeczywistym analiz i modelowania predykcyjnego zdarzeń. Usługa umożliwia klientom usługi Azure Maps natywnie w celu zwiększenia ich analizy lokalizacji za pomocą biblioteki typowych obliczeń matematycznych dane geograficzne, w tym usług, takich jak najbliższego punktu, najkrótszej odległości i bufory. Aby dowiedzieć się więcej o usłudze oraz informacje o różnych funkcjach, zobacz nasze [dokumentacji interfejsu API](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Time Zone Service

Usługa Time Zone Service umożliwia wysyłanie zapytań o bieżące, historyczne i przyszłe informacje o strefie czasowej przy użyciu par szerokość-długość geograficzna lub identyfikatora [IANA ID](https://www.iana.org/). Usługa Time Zone ponadto umożliwia konwertowanie identyfikatorów strefy czasowej systemu Microsoft Windows na strefy czasowe IANA, pobieranie przesunięcia strefy czasowej względem czasu UTC oraz pobieranie bieżącego czasu w odpowiedniej strefie czasowej. Typowa odpowiedź JSON na zapytanie wysyłane do usługi Time Zone Service wygląda jak w następującym przykładzie:

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

Aby uzyskać szczegółowe informacje na temat tej usługi, odwiedź stronę [Azure Maps Timezone APIs (Interfejsy API usługi Timezone w usłudze Azure Maps)](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Traffic Service

Usługa Traffic Service to zbiór usług internetowych umożliwiających deweloperom tworzenie aplikacji internetowych i mobilnych wymagających informacji o ruchu. Usługa udostępnia dwa typy danych:

* Traffic Flow — zarejestrowane prędkości i czasy podróży w czasie rzeczywistym dla wszystkich kluczowych dróg w sieci.
* Zdarzenia ruchu - aktualny widok o drogach i zdarzenia dotyczące sieci dróg.

![Ruch w usłudze Azure Maps](media/about-azure-maps/Introduction_Traffic.png)

Odwiedź stronę [Azure Maps Traffic APIs (Interfejsy API usługi Traffic w usłudze Azure Maps)](https://docs.microsoft.com/rest/api/maps/traffic), aby uzyskać więcej informacji.

### <a name="ip-to-location"></a>IP to Location

Usługa IP to Location umożliwia wyświetlenie podglądu dwuliterowego kodu kraju pobranego dla danego adresu IP. Ta usługa może ułatwić Ci dopasowanie i ulepszenie środowiska użytkownika przez dostarczanie dostosowanej zawartości w aplikacji na podstawie lokalizacji geograficznej.

Aby uzyskać informacje dotyczące interfejsów API REST dla usługi IP to Location, odwiedź stronę [interfejsów API geolokalizacji usługi Azure Maps](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Model programowania

Usługę Azure Maps zaprojektowano pod kątem mobilności i obsługi aplikacji na wielu platformach. Korzysta ona z modelu programowania niezależnego od języka i obsługuje format danych wyjściowych JSON za pośrednictwem interfejsów [API REST](https://docs.microsoft.com/rest/api/maps/).

Ponadto usługa Azure Maps oferuje wygodną [kontrolkę mapy JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) z prostym modelem programowania, która umożliwia szybkie i łatwe tworzenie aplikacji internetowych i mobilnych.

## <a name="usage"></a>Sposób użycia

Aby uzyskać dostęp do usługi Azure Maps, wystarczy przejść do witryny [Azure Portal](https://portal.azure.com) i utworzyć konto usługi Azure Maps.

Usługa Azure Maps korzysta ze schematu uwierzytelniania opartego na kluczach. Konto będzie zawierać dwa wstępnie wygenerowane klucze do użycia. Rozpocznij integrowanie tych możliwości obsługi lokalizacji w Twojej aplikacji przez użycie dowolnego z kluczy w żądaniu wysłanym do usługi Azure Maps.

## <a name="supported-regions"></a>Obsługiwane regiony

Interfejs API usługi Azure Maps jest obecnie dostępna we wszystkich krajach/regionach z wyjątkiem następujących regionów:

* Argentyna
* Chiny
* Indie
* Maroko
* Pakistan
* Korea Południowa

Upewnij się, że lokalizacja bieżącego adresu IP nie znajduje się w jeden z nieobsługiwanych krajów/regionów powyżej.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat nowych funkcji usługi Azure Maps, zobacz:

> [!div class="nextstepaction"]
> [Route Matrix, Isochrones, IP lookup, and more (Macierze tras, izochrony, wyszukiwanie adresów IP i nie tylko)](https://azure.microsoft.com/blog/route-matrix-isochrones-ip-lookup-and-more-added-to-azure-maps/)

Wypróbowanie przykładowej aplikacji przedstawiającej usługi Azure Maps:

> [!div class="nextstepaction"]
> [Szybki start: Tworzenie aplikacji sieci web](quick-demo-map-app.md)
