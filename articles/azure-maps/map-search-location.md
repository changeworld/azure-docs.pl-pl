---
title: Pokazywale wyszukiwania na mapie | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak wykonać żądanie wyszukiwania przy użyciu microsoft azure maps web SDK i wyświetlić wyniki na mapie.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e82a1daee381c7bad19c83fa735d0028bef2010e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371393"
---
# <a name="show-search-results-on-the-map"></a>Pokazywale wyszukiwania na mapie

Z tego artykułu dowiesz się, jak wyszukać interesującą lokalizację i wyświetlić wyniki wyszukiwania na mapie.

Istnieją dwa sposoby wyszukiwania lokalizacji zainteresowania. Jednym ze sposobów jest użycie modułu usługi do żądania wyszukiwania. Innym sposobem jest, aby żądanie wyszukiwania do [usługi Azure Maps fuzzy interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) za pośrednictwem interfejsu API [pobierania](https://fetch.spec.whatwg.org/). Poniżej omówiono oba sposoby.

## <a name="make-a-search-request-via-service-module"></a>Złożyć żądanie wyszukiwania za pomocą modułu usługi

<iframe height='500' scrolling='no' title='Pokaż wyniki wyszukiwania na mapie (moduł usługi)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Pokaż wyniki wyszukiwania na mapie (Moduł usługi)</a> przez Usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok konstruuje obiekt mapy i ustawia mechanizm uwierzytelniania, aby użyć tokenu dostępu. Możesz zobaczyć [tworzenie mapy](./map-create.md) dla instrukcji.

Drugi blok kodu tworzy `TokenCredential` do uwierzytelniania żądań HTTP do usługi Azure Maps z tokenem dostępu. Następnie przekazuje `TokenCredential` do `atlas.service.MapsURL.newPipeline()` i tworzy [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) wystąpienie. Reprezentuje `searchURL` adres URL do operacji usługi Azure Maps [Search.](https://docs.microsoft.com/rest/api/maps/search)

Trzeci blok kodu tworzy obiekt źródła danych przy użyciu [Klasy DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) i dodaj do niego wyniki wyszukiwania. [Warstwa symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstu lub ikon do renderowania danych opartych na punktach zawiniętych w [źródle danych](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symboli na mapie.  Następnie tworzona jest warstwa symboli. Źródło danych zostanie dodane do warstwy symboli, która jest następnie dodawana do mapy.

Czwarty blok kodu używa [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) metody w [module usługi](how-to-use-services-module.md). Umożliwia wykonywanie bezpłatnego wyszukiwania tekstu formularza za pośrednictwem [interfejsu API Get Search Fuzzy rest](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) w celu wyszukania punktu zainteresowania. Pobierz żądania do interfejsu API rozmyte wyszukiwanie może obsługiwać dowolną kombinację danych wejściowych rozmyte. Kolekcja funkcji GeoJSON z odpowiedzi jest następnie `geojson.getFeatures()` wyodrębniana przy użyciu metody i dodawana do źródła danych, co automatycznie powoduje renderowanie danych na mapie za pośrednictwem warstwy symboli.

Ostatni blok kodu dostosowuje granice kamery dla mapy za pomocą właściwości [Map setCamera.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)

Żądanie wyszukiwania, źródło danych, warstwa symboli i granice kamery znajdują się wewnątrz [detektora zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) mapy. Chcemy mieć pewność, że wyniki są wyświetlane po pełnym załadowaniu mapy.


## <a name="make-a-search-request-via-fetch-api"></a>Złożyć żądanie wyszukiwania za pośrednictwem interfejsu API pobierania

<iframe height='500' scrolling='no' title='Pokazywale wyszukiwania na mapie' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro Pokaż wyniki wyszukiwania na<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>mapie</a> przez Usługi Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Ustawia mechanizm uwierzytelniania, aby użyć tokenu dostępu. Możesz zobaczyć [tworzenie mapy](./map-create.md) dla instrukcji.

Drugi blok kodu tworzy adres URL, aby wykonać żądanie wyszukiwania. Tworzy również dwie tablice do przechowywania granic i pinów dla wyników wyszukiwania.

Trzeci blok kodu używa [interfejsu API pobierania](https://fetch.spec.whatwg.org/). [Interfejs API pobierania](https://fetch.spec.whatwg.org/) służy do żądania do usługi Azure Maps [fuzzy wyszukiwania interfejsu API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) do wyszukiwania w punktach szczególnych. Interfejs API wyszukiwania rozmytego może obsługiwać dowolną kombinację rozmytych danych wejściowych. Następnie obsługuje i analizuje odpowiedzi wyszukiwania i dodaje piny wynik do searchPins tablicy.

Czwarty blok kodu tworzy obiekt źródła danych przy użyciu [Klasy DataSource.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) W kodzie dodajemy wyniki wyszukiwania do obiektu źródłowego. [Warstwa symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstu lub ikon do renderowania danych opartych na punktach zawiniętych w [źródle danych](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symboli na mapie. Następnie tworzona jest warstwa symboli. Źródło danych zostanie dodane do warstwy symboli, która jest następnie dodawana do mapy.

Ostatni blok kodu tworzy [Obiekt BoundingBox.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) Wykorzystuje tablicę wyników, a następnie dostosowuje granice kamery dla mapy za pomocą [mapy setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Następnie renderuje piny wynik.

Żądanie wyszukiwania, źródło danych, warstwa symboli i granice kamery są ustawiane w [detektorze zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) mapy, aby upewnić się, że wyniki są wyświetlane po pełnym załadowaniu mapy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Najważniejsze wskazówki dotyczące korzystania z usługi wyszukiwania](how-to-use-best-practices-for-search.md)

Dowiedz się więcej o **rozmytym wyszukiwaniu:**

> [!div class="nextstepaction"]
> [Interfejs API wyszukiwania rozmytego usługi Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Mapę](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby uzyskać pełne przykłady kodu:

> [!div class="nextstepaction"]
> [Uzyskiwanie informacji na podstawie współrzędnych](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Wyświetlanie instrukcji dotyczących trasy z punktu A do punktu B](map-route.md)
