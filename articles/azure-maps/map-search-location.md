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
ms.openlocfilehash: cf27864d691fe2fe13c9483348fb2abed121874d
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713507"
---
# <a name="show-search-results-on-the-map"></a>Pokaż wyniki wyszukiwania na mapie

W tym artykule przedstawiono sposób wyszukiwania lokalizacji zainteresowania i wyświetlić wyniki wyszukiwania na mapie.

Istnieją dwa sposoby, aby wyszukać lokalizację zainteresowania. Jednym ze sposobów jest użycie modułu usługi do żądania wyszukiwania. Druga metoda obejmuje utworzenie żądania wyszukiwania za pomocą [XMLHttpRequest](https://xhr.spec.whatwg.org/) do [interfejs API usługi Azure Maps rozmyte wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). Obie metody zostały podane poniżej.

## <a name="make-a-search-request-via-service-module"></a>Wyślij żądanie wyszukiwania za pomocą modułu usług

<iframe height='500' scrolling='no' title='Pokaż wyniki wyszukiwania na mapie (moduł usługi)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>wyniki wyszukiwania na mapie (moduł usługi)</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu tworzy obiekt mapy i inicjuje usługi klienta. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Drugi blok kodu używa [interfejsu API wyszukiwania rozmytego usługi Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) wyszukiwanie punktu orientacyjnego. Wyszukiwanie rozmyte interfejsu API może obsługiwać dowolną kombinację rozmyte danych wejściowych. Odpowiedź z usługi wyszukiwania rozmytego, następnie jest analizowany w formacie GeoJSON, używając [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest#geojsonsearchresponse) metody. Numery PIN zostaną następnie dodane do mapy, aby pokazać punktów orientacyjnych na mapie.

Ostatni blok kodu dostosowuje granice aparatu mapy za pomocą mapy [setCameraBounds](/javascript/api/azure-maps-control/atlas.map#setcamerabounds-cameraboundsoptions-) właściwości.

## <a name="make-a-search-request-via-xmlhttprequest"></a>Wyślij żądanie wyszukiwania za pomocą XMLHttpRequest

<iframe height='500' scrolling='no' title='Pokaż wyniki wyszukiwania na mapie' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Pokaż wyniki na mapie</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Drugi blok kodu dodaje warstwy wyniki wyszukiwania na mapie. Warstwa wyniki wyszukiwania będą wyświetlane wyniki wyszukiwania, ponieważ Przypina na mapie. Numery PIN są dodawane przy użyciu [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

Trzeci bloku kodu wysyła [XMLHttpRequest](https://xhr.spec.whatwg.org/) do [interfejs API usługi Azure Maps rozmyte wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) wyszukiwanie punktu orientacyjnego. Wyszukiwanie rozmyte interfejsu API może obsługiwać dowolną kombinację rozmyte danych wejściowych.

Ostatni blok kodu analizowania odpowiedzi i dostosowuje dostosowuje granice aparatu mapy za pomocą mapy [setCameraBounds](/javascript/api/azure-maps-control/atlas.map#setcamerabounds-cameraboundsoptions-) do renderowania numerów PIN wynik.

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
