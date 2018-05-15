---
title: Omówienie usługi Azure Maps | Microsoft Docs
description: Wprowadzenie do usługi Azure Maps
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: overview
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: e84580c4023be87ebfc1988c631af0b76e213987
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="an-introduction-to-azure-maps"></a>Wprowadzenie do usługi Azure Maps
Usługa Azure Maps to portfel usług geoprzestrzennych obejmujący interfejsy API usług dla map, wyszukiwania, wyznaczania tras, ruchu drogowego oraz stref czasowych. Portfel usług zgodnych z interfejsem Azure OneAPI umożliwia używanie znanych narzędzi do szybkiego tworzenia i skalowania rozwiązań, które będą integrować informacje o lokalizacji w rozwiązaniach platformy Azure. Usługa Azure Maps udostępnia deweloperom ze wszystkich branż zaawansowane funkcje geoprzestrzenne wraz z aktualnymi danymi map niezbędnymi do zapewniania kontekstu geograficznego aplikacjom internetowym i mobilnym. Usługa Azure Maps to zestaw interfejsów API REST wyposażony w internetową kontrolkę JavaScript. Dzięki niej programowanie jest łatwe i elastyczne, a rezultaty można przenosić na różne platformy. 

Następujące wideo stanowi wprowadzenie do usługi Azure Maps:

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Usługa Azure Maps składa się z pięciu głównych usług wspierających aplikacje platformy Azure wymagające kontekstu geograficznego. Każdą z tych usług szczegółowo omówiono.

Usługa **Render Service** została zaprojektowana dla deweloperów do tworzenia aplikacji internetowych i mobilnych związanych z mapowaniem. Usługa używa rastrowych obrazów graficznych o wysokiej jakości — dostępnych z 19 poziomami powiększenia — lub w pełni konfigurowalnych obrazów map w formacie wektorowym.

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

Usługa **Route Service** zawiera niezawodne obliczenia geometryczne rzeczywistej infrastruktury oraz trasy dla wielu środków transportu. Usługa pozwala deweloperom obliczać trasy dla wielu sposobów przemieszczania się, takich jak samochód osobowy, ciężarówka, rower czy wędrówka. Usługa może również brać pod uwagę dane wejściowe, takie jak warunki na drogach, ograniczenia co do masy pojazdu lub transport materiałów niebezpiecznych.

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

Usługa **Search Service** została opracowana dla deweloperów do wyszukiwania adresów, miejsc i list biznesowych według nazwy lub kategorii oraz innych informacji geograficznych. Usługa Search Service może też [odwracać adresy geokodowania](https://en.wikipedia.org/wiki/Reverse_geocoding) i przechodzić przez ulice w oparciu o długość/szerokość geograficzną. 

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

Usługa **Time Zone Service** umożliwia wysyłanie zapytań o bieżące, historyczne i przyszłe informacje o strefie czasowej przy użyciu par szerokość-długość geograficzna lub identyfikatora [IANA ID](http://www.iana.org/). Usługa Time Zone ponadto umożliwia konwertowanie identyfikatorów strefy czasowej systemu Microsoft Windows na strefy czasowe IANA, pobieranie przesunięcia strefy czasowej względem czasu UTC oraz pobieranie bieżącego czasu w odpowiedniej strefie czasowej. Typowa odpowiedź JSON na zapytanie wysyłane do usługi Time Zone Service wygląda jak w następującym przykładzie:

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

Usługa **Traffic Service** to zbiór usług internetowych zaprojektowanych dla deweloperów do tworzenia aplikacji internetowych i mobilnych wymagających informacji o ruchu. Usługa udostępnia dwa typy danych:
* Traffic Flow — zarejestrowane prędkości i czasy podróży w czasie rzeczywistym dla wszystkich kluczowych dróg w sieci. 
* Traffic Incidents — dokładny wgląd w korki na drogach i zdarzenia w sieci dróg.

![Ruch w usłudze Azure Maps](media/about-azure-maps/Introduction_Traffic.png)

Usługa Azure Maps została opracowana z myślą o mobilności i może wspierać aplikacje wieloplatformowe, ponieważ model programowania jest niezależny i obsługuje dane wyjściowe JSON za pośrednictwem interfejsów API REST. Ponadto usługa Azure Maps oferuje wygodną kontrolkę mapy JavaScript z prostym modelem programowania, która umożliwia szybkie opracowywanie aplikacji internetowych i mobilnych. 

Usługa Azure Maps używa schematu uwierzytelniania opartego na kluczach, więc uzyskanie dostępu do usług wymaga jedynie przejścia do witryny [Azure Portal](http://portal.azure.com) i utworzenia konta usługi Azure Maps. Konto będzie zawierać dwa wstępnie wygenerowane klucze do użycia. Rozpocznij integrowanie tych możliwości opartych na lokalizacji bezpośrednio w aplikacjach przy użyciu dowolnego z kluczy w żądaniach wysyłanych do usługi Azure Maps.

## <a name="unsupported-regions"></a>Nieobsługiwane regiony
Interfejs API usługi Azure Maps jest obecnie niedostępny w niektórych krajach. Sprawdź swój bieżący adres IP i zweryfikuj, czy nie pochodzi on z jednego z nieobsługiwanych krajów wymienionych poniżej:

* Argentyna
* Chiny
* Indie
* Maroko
* Pakistan
* Korea Południowa

## <a name="relationship-with-bing-maps"></a>Relacja z usługą Mapy Bing
Mapy opisane w tym dokumencie różnią się od udostępnianych przez usługę Mapy Bing. Mimo że korzystają z wielu takich samych funkcji, te usługi są różne i nie są ze sobą związane. Ta usługa platformy Azure nie wpływa na ofertę produktu Mapy Bing ani związane z nim plany.

Celem firmy Microsoft jest zapewnienie wyboru dla społeczności deweloperów pod względem ofert usług lokalizacji. Poniższa tabela zawiera wytyczne dla deweloperów, którzy wybierają usługę do użycia: 

| Scenariusz | Użyj usługi Azure Maps, gdy… | Użyj usługi Mapy Bing, gdy… |
| ------------- | ------------- | ------------- |
| Środowisko deweloperskie | Tworzenie w innych usługach platformy Azure lub koordynacja z nimi | Używanie chmury innej firmy lub innego środowiska deweloperskiego |
| Etap programowania  | Ponieważ usługa Azure Maps znajduje się obecnie w publicznej wersji zapoznawczej, jest zoptymalizowana pod kątem testowania na wczesnym etapie i opracowywania weryfikacji koncepcji | Na potrzeby środowiska produkcyjnego wymagana jest umowa SLA klasy korporacyjnej |
| Opcje cennika | Wystarczające są wstępne opcje cenowe na potrzeby dewelopera | Wymagany jest dostosowany cennik klasy korporacyjnej |
| Środowisko przypadków użycia | Wymagane jest użycie w pojeździe | Użycie w pojeździe nie jest wymagane |
| Pokrycie geograficznie | Indie, Chiny, Japonia i Korea Południowa — niewymagane | Indie, Chiny, Japonia i Korea Południowa — wymagane |
| Zawartość map | Standardowe mapy powierzchni są wystarczające | Wymagane są obrazy satelitarne, z powietrza i ulic |
| Bazowe źródło map | Preferowane jest użycie danych mapowych programu TomTom | Preferowane jest użycie danych mapowych programu HERE |

Załóż[konto w usłudze Azure Maps jeszcze dziś](http://aka.ms/azurelbsportal).

## <a name="next-steps"></a>Następne kroki

Masz teraz ogólne pojęcie o usłudze Azure Maps. Następnym krokiem będzie wypróbowanie przykładowej aplikacji prezentującej działanie usługi.

> [!div class="nextstepaction"]
> [Uruchom demonstracyjną mapę z interaktywnym wyszukiwaniem](quick-demo-map-app.md)

