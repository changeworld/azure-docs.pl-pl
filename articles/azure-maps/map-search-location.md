---
title: Wyświetlanie wyników wyszukiwania za pomocą usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak wykonać żądanie wyszukiwania za pomocą usługi Azure Maps, a następnie wyświetlić wyniki na mapie kodu Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: be01c9d96386804b8bc074d81041104cbf592df6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60768924"
---
# <a name="show-search-results-on-the-map"></a>Pokaż wyniki wyszukiwania na mapie

W tym artykule przedstawiono sposób wyszukiwania lokalizacji zainteresowania i wyświetlić wyniki wyszukiwania na mapie.

Istnieją dwa sposoby, aby wyszukać lokalizację zainteresowania. Jednym ze sposobów jest użycie modułu usługi do żądania wyszukiwania. Druga metoda obejmuje utworzenie żądania wyszukiwania do [interfejs API usługi Azure Maps rozmyte wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) za pośrednictwem [Fetch API](https://fetch.spec.whatwg.org/). Obie metody zostały podane poniżej.

## <a name="make-a-search-request-via-service-module"></a>Wyślij żądanie wyszukiwania za pomocą modułu usług

<iframe height='500' scrolling='no' title='Pokaż wyniki wyszukiwania na mapie (moduł usługi)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>wyniki wyszukiwania na mapie (moduł usługi)</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy i ustawia mechanizm uwierzytelniania, korzystanie z klucza subskrypcji. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Tworzy drugi blok kodu `SubscriptionKeyCredentialPolicy` do uwierzytelniania żądań HTTP do usługi Azure Maps za pomocą klucza subskrypcji. A następnie `atlas.service.MapsURL.newPipeline()` przyjmuje `SubscriptionKeyCredential` zasad i tworzy [potoku](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) wystąpienia. `searchURL` Reprezentuje adres URL do usługi Azure Maps [wyszukiwania](https://docs.microsoft.com/rest/api/maps/search) operacji.

Trzeci bloku kodu tworzy źródła danych obiektu przy użyciu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klasy i dodać wyniki wyszukiwania do niego. A [warstwy symbol](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstu lub ikony do renderowania oparta na punkcie danych, w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symbole na mapie.  Warstwa symbol zostanie utworzony i źródło danych jest dodawane do warstwy "symbol", która następnie zostanie dodany do mapy.

Czwarty blok kodu używa [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) method in Class metoda [moduł usługi](https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js). Umożliwia wykonywanie dowolnej postaci wyszukiwanie na podstawie tekstu przy użyciu [interfejsu API rest Pobierz wyszukiwania rozmytego](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) wyszukiwanie punktu orientacyjnego. Pobierz interfejs API wyszukiwania rozmytego może obsługiwać dowolną kombinację rozmyte danych wejściowych. Kolekcja funkcji GeoJSON z odpowiedzi jest wyodrębniany przy użyciu `geojson.getFeatures()` — metoda i dodać do źródła danych, co automatycznie powoduje danych są renderowane na mapie za pomocą warstwy symboli.

Ostatni blok kodu dostosowuje granice aparatu mapy za pomocą mapy [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) właściwości.

Żądania wyszukiwania, źródła danych i warstwy symboli i granice aparat są tworzone i gotowe przez zestaw w mapie [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) aby upewnić się, że wyniki są wyświetlane po mapy ładuje pełni.


## <a name="make-a-search-request-via-fetch-api"></a>Wyślij żądanie wyszukiwania za pomocą pobrania interfejsu API

<iframe height='500' scrolling='no' title='Pokaż wyniki wyszukiwania na mapie' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Pokaż wyniki na mapie</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy i ustawia mechanizm uwierzytelniania, korzystanie z klucza subskrypcji. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Drugi blok kodu tworzy adres URL, aby zgłosić wniosek o wyszukiwania. Tworzy również dwie tablice do przechowywania granice i kody PIN dla wyników wyszukiwania.

Trzeci blok kodu używa [Fetch API](https://fetch.spec.whatwg.org/) Aby zgłosić wniosek o [interfejs API usługi Azure Maps rozmyte wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) aby szukać punktów orientacyjnych. Interfejs API wyszukiwania rozmytego może obsługiwać dowolną kombinację rozmyte danych wejściowych. Następnie obsługuje analizuje odpowiedzi wyszukiwania i dodaje numerów PIN wynik do tablicy searchPins.

Czwarty bloku kodu tworzy źródła danych obiektu przy użyciu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klasy i dodać wyniki wyszukiwania do niego. A [warstwy symbol](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstu lub ikony do renderowania oparta na punkcie danych, w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symbole na mapie. Warstwa symbol zostanie utworzony i źródło danych jest dodawane do warstwy "symbol", która następnie zostanie dodany do mapy.

Tworzy ostatniego bloku kodu [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) obiektu przy użyciu tablicy wyników i ustala granice aparatu mapy za pomocą mapy [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Następnie renderowaniu numerów PIN wynik.

Żądania wyszukiwania, źródła danych i warstwy symboli i granice aparat są ustawiane w mapie [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) aby upewnić się, że wyniki są wyświetlane po mapy ładuje pełni.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o **Wyszukiwanie rozmyte**:

> [!div class="nextstepaction"]
> [Wyszukiwanie rozmyte interfejsu API usługi Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby uzyskać przykłady pełnego kodu:

> [!div class="nextstepaction"]
> [Uzyskiwanie informacji na podstawie współrzędnych](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Wyświetlanie instrukcji dotyczących trasy od A do B](./map-route.md)
