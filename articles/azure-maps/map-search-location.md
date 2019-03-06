---
title: Wyświetlanie wyników wyszukiwania za pomocą usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak wykonać żądanie wyszukiwania za pomocą usługi Azure Maps, a następnie wyświetlić wyniki na mapie kodu Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0db64916ca84737a6a713c3d2544b2f48c3682c0
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2019
ms.locfileid: "57403095"
---
# <a name="show-search-results-on-the-map"></a>Pokaż wyniki wyszukiwania na mapie

W tym artykule przedstawiono sposób wyszukiwania lokalizacji zainteresowania i wyświetlić wyniki wyszukiwania na mapie.

Istnieją dwa sposoby, aby wyszukać lokalizację zainteresowania. Jednym ze sposobów jest użycie modułu usługi do żądania wyszukiwania. Druga metoda obejmuje utworzenie żądania wyszukiwania za pomocą [XMLHttpRequest](https://xhr.spec.whatwg.org/) do [interfejs API usługi Azure Maps rozmyte wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). Obie metody zostały podane poniżej.

## <a name="make-a-search-request-via-service-module"></a>Wyślij żądanie wyszukiwania za pomocą modułu usług

<iframe height='500' scrolling='no' title='Pokaż wyniki wyszukiwania na mapie (moduł usługi)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>wyniki wyszukiwania na mapie (moduł usługi)</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy i usługa klienta jest inicjowana. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Drugi blok kodu używa `getSearchFuzzy` method in Class metoda [moduł usługi](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1). Umożliwia wykonywanie dowolnej postaci wyszukiwanie na podstawie tekstu przy użyciu [interfejsu API rest Wyszukiwanie rozmyte](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) wyszukiwanie punktu orientacyjnego. Wyszukiwanie rozmyte interfejsu API może obsługiwać dowolną kombinację rozmyte danych wejściowych. Odpowiedź z usługi wyszukiwania rozmytego, następnie jest analizowany w formacie GeoJSON, używając `getGeoJsonSearchResponse` metody. 

Trzeci bloku kodu tworzy źródła danych obiektu przy użyciu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klasy i dodać wyniki wyszukiwania do niego. A [warstwy symbol](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstu lub ikony do renderowania oparta na punkcie danych, w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symbole na mapie.  Warstwa symbol zostanie utworzony i źródło danych jest dodawane do warstwy "symbol", która następnie zostanie dodany do mapy.

Ostatni blok kodu dostosowuje granice aparatu mapy za pomocą mapy [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) właściwości.

Żądania wyszukiwania, źródła danych i warstwy symboli i granice aparat są tworzone i ustawić w mapie [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) aby upewnić się, że wyniki są wyświetlane po mapy ładuje pełni.


## <a name="make-a-search-request-via-xmlhttprequest"></a>Wyślij żądanie wyszukiwania za pomocą XMLHttpRequest

<iframe height='500' scrolling='no' title='Pokaż wyniki wyszukiwania na mapie' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Pokaż wyniki na mapie</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Drugi blok kodu wysyła [XMLHttpRequest](https://xhr.spec.whatwg.org/) do [interfejs API usługi Azure Maps rozmyte wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) wyszukiwanie punktu orientacyjnego. Interfejs API wyszukiwania rozmytego może obsługiwać dowolną kombinację rozmyte danych wejściowych. 

Trzeci bloku kodu analizowania odpowiedzi wyszukiwania i zapisuje wyniki w tablicy, aby obliczyć granic. Zwraca wyniki wyszukiwania.

Czwarty bloku kodu tworzy źródła danych obiektu przy użyciu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klasy i dodać wyniki wyszukiwania do niego. A [warstwy symbol](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstu lub ikony do renderowania oparta na punkcie danych, w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symbole na mapie. Warstwa symbol zostanie utworzony i źródło danych jest dodawane do warstwy "symbol", która następnie zostanie dodany do mapy.

Tworzy ostatniego bloku kodu [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) obiektu przy użyciu tablicy wyników i ustala granice aparatu mapy za pomocą mapy [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Renderowania numerów PIN wynik.

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
