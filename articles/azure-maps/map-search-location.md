---
title: Pokaż wyniki wyszukiwania za pomocą Azure Maps | Microsoft Docs
description: Jak wykonać żądanie wyszukiwania przy użyciu Azure Maps następnie wyświetlić wyniki w Azure Maps Web SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7c4c78e1d21754d42391a3762e9f7ed199a7376b
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68975968"
---
# <a name="show-search-results-on-the-map"></a>Pokaż wyniki wyszukiwania na mapie

W tym artykule pokazano, jak wyszukiwać interesujące lokalizacje i wyświetlać wyniki wyszukiwania na mapie.

Istnieją dwa sposoby wyszukiwania interesujących lokalizacji. Jednym ze sposobów jest użycie modułu usługi do żądania wyszukiwania. Druga metoda polega na tym, że żądanie wyszukiwania [Azure Maps interfejs API wyszukiwania rozmytego](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) za pomocą [interfejsu API pobierania](https://fetch.spec.whatwg.org/). Obie metody omówiono poniżej.

## <a name="make-a-search-request-via-service-module"></a>Utwórz żądanie wyszukiwania za pomocą modułu usługi

<iframe height='500' scrolling='no' title='Pokaż wyniki wyszukiwania na mapie (moduł usługi)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Pokaż wyniki wyszukiwania na mapie (module usługi)</a> przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w witrynie <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu konstruuje obiekt mapy i ustawia mechanizm uwierzytelniania do korzystania z tokenu dostępu. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

Drugi blok kodu tworzy `TokenCredential` do uwierzytelniania żądań HTTP do Azure Maps z tokenem dostępu. Następnie przekazuje `TokenCredential` do `atlas.service.MapsURL.newPipeline()` i tworzy wystąpienie potoku [](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . Reprezentuje adres URL służący do Azure Maps operacji [wyszukiwania.](https://docs.microsoft.com/rest/api/maps/search) `searchURL`

Trzeci blok kodu tworzy obiekt źródła danych przy użyciu klasy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) i dodaje do niego wyniki wyszukiwania. [Warstwa symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstu lub ikon do renderowania danych opartych na punkcie w postaci symboli [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapie.  Następnie zostanie utworzona warstwa symboli i źródło danych zostanie dodane do warstwy symboli, która jest następnie dodawana do mapy.

Czwarty blok kodu używa metody [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) w [module usługi](how-to-use-services-module.md). Umożliwia ona wykonywanie bezpłatnych wyszukiwań tekstowych za pośrednictwem [interfejsu API REST usługi Get Search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) w celu wyszukiwania punktów zainteresowania. Pobieranie rozmytego interfejsu API wyszukiwania może obsłużyć dowolną kombinację danych rozmytych. Kolekcje funkcji GEOJSON z odpowiedzi są następnie wyodrębniane przy użyciu `geojson.getFeatures()` metody i dodawane do źródła danych, co powoduje automatyczne wyniki renderowania danych na mapie za pośrednictwem warstwy symboli.

Ostatni blok kodu dostosowuje granice aparatu dla mapy za pomocą właściwości [Setcamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) mapy.

Żądanie wyszukiwania, źródło danych i warstwa symboli oraz granice aparatu są tworzone i ustawiane w ramach [odbiornika zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) gotowych mapy, aby upewnić się, że wyniki są wyświetlane po całkowitym załadowaniu mapy.


## <a name="make-a-search-request-via-fetch-api"></a>Utwórz żądanie wyszukiwania za pomocą interfejsu API pobierania

<iframe height='500' scrolling='no' title='Pokaż wyniki wyszukiwania na mapie' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Pokaż wyniki wyszukiwania na mapie</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu konstruuje obiekt mapy i ustawia mechanizm uwierzytelniania do korzystania z tokenu dostępu. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

Drugi blok kodu tworzy adres URL, na który zostanie wysłane żądanie wyszukiwania. Tworzy także dwie tablice do przechowywania granic i kodów PIN dla wyników wyszukiwania.

Trzeci blok kodu używa [interfejsu API pobierania](https://fetch.spec.whatwg.org/) , aby utworzyć żądanie [Azure Maps interfejsu API wyszukiwania rozmytego](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) w celu wyszukania interesujących punktów. Interfejs API wyszukiwania rozmytego może obsługiwać dowolną kombinację danych wejściowych rozmytych. Następnie obsługuje i analizuje odpowiedź wyszukiwania i dodaje numery PIN wyników do tablicy searchPins.

Czwarty blok kodu tworzy obiekt źródła danych przy użyciu klasy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) i dodaje do niego wyniki wyszukiwania. [Warstwa symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstu lub ikon do renderowania danych opartych na punkcie w postaci symboli [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapie. Następnie zostanie utworzona warstwa symboli i źródło danych zostanie dodane do warstwy symboli, która jest następnie dodawana do mapy.

Ostatni blok kodu tworzy obiekt [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) przy użyciu tablicy wyników, a następnie dostosowuje granice aparatu dla mapy za pomocą setcamera mapy. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) Następnie renderuje to numery PIN wyników.

Żądanie wyszukiwania, źródło danych i warstwa symboli oraz granice aparatu są ustawiane w obrębie [odbiornika zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) mapy, aby upewnić się, że wyniki są wyświetlane po całkowitym załadowaniu mapy.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat **wyszukiwania rozmytego**:

> [!div class="nextstepaction"]
> [Azure Maps interfejsu API wyszukiwania rozmytego](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Zmapować](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby zapoznać się z pełnymi przykładami kodu:

> [!div class="nextstepaction"]
> [Uzyskiwanie informacji z współrzędnych](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Pokaż kierunki od A do B](./map-route.md)
