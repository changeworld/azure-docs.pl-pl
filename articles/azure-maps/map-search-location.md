---
title: Wyświetlanie wyników wyszukiwania za pomocą usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak wykonać żądanie wyszukiwania za pomocą usługi Azure Maps, a następnie wyświetlić wyniki na mapie kodu Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0cc0ec79cf23d3bba01845ed64493df010c1ca66
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161700"
---
# <a name="show-search-results-on-the-map"></a>Pokaż wyniki wyszukiwania na mapie

W tym artykule przedstawiono sposób wyszukiwania lokalizacji zainteresowania i wyświetlić wyniki wyszukiwania na mapie. 

Istnieją dwa sposoby, aby wyszukiwać lokalizacja zainteresowania jeden ze sposobów jest przy użyciu modułu usługi się żądanie wyszukiwania, a druga, wprowadzając żądania wyszukiwania za pomocą [XMLHttpRequest](https://xhr.spec.whatwg.org/) do [Azure Maps wyszukiwanieminterfejsuAPI](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). Omówimy zarówno poniżej.

## <a name="use-service-module-to-make-a-search-request"></a>Moduł usługi użyj żądania wyszukiwania

### <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='500' scrolling='no' title='Pokaż wyniki wyszukiwania na mapie (moduł usługi)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>wyniki wyszukiwania na mapie (moduł usługi)</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy i tworzy wystąpienie usługi klienta. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Drugi blok kodu używa Wyszukiwanie rozmyte [interfejsu API wyszukiwania rozmytego usługi Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) wyszukiwanie punktu orientacyjnego. Wyszukiwanie rozmyte interfejsu API może obsługiwać dowolną kombinację rozmyte danych wejściowych. Odpowiedź z usługi wyszukiwania rozmytego, następnie jest analizowany w formacie GeoJSON, używając [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest#geojsonsearchresponse) metody. Numery PIN zostaną następnie dodane do mapy, aby pokazać punktów orientacyjnych na mapie.

Ostatni blok kodu dostosowuje granice aparatu mapy za pomocą mapy [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) właściwości.


## <a name="search-by-xmlhttprequest"></a>Wyszukaj według XMLHttpRequest

### <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='500' scrolling='no' title='Pokaż wyniki wyszukiwania na mapie' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Pokaż wyniki na mapie</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Drugi blok kodu dodaje warstwy wyniki wyszukiwania na mapie. Warstwa wyniki wyszukiwania będą wyświetlane wyniki wyszukiwania, ponieważ Przypina na mapie.

Trzeci bloku kodu wysyła [XMLHttpRequest](https://xhr.spec.whatwg.org/) do [interfejs API usługi Azure Maps rozmyte wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) wyszukiwanie punktu orientacyjnego. Wyszukiwanie rozmyte interfejsu API może obsługiwać dowolną kombinację rozmyte danych wejściowych.

Ostatni blok kodu analizowania odpowiedzi i dostosowuje dostosowuje granice aparatu mapy za pomocą mapy [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) do renderowania numerów PIN wynik.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule: 

* [Wyszukiwanie rozmyte interfejsu API usługi Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły: 
* [Uzyskiwanie informacji na podstawie współrzędnych](./map-get-information-from-coordinate.md)
* [Wyświetlanie instrukcji dotyczących trasy od A do B](./map-route.md)
