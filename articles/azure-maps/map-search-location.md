---
title: Wyświetlanie wyników wyszukiwania za pomocą usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak wykonać żądanie wyszukiwania za pomocą usługi Azure Maps, a następnie wyświetlić wyniki na mapie kodu Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 08/26/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 31ff57f8a933ac17c6dfaa1a1fb0cf2bab0b6557
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43345275"
---
# <a name="show-search-results-on-the-map"></a>Pokaż wyniki wyszukiwania na mapie

W tym artykule przedstawiono sposób wyszukiwania lokalizacji zainteresowania i wyświetlić wyniki wyszukiwania na mapie. 

## <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='500' scrolling='no' title='Pokaż wyniki wyszukiwania na mapie (moduł usługi)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>wyniki wyszukiwania na mapie (moduł usługi)</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy i tworzy wystąpienie usługi klienta. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Drugi blok kodu używa Wyszukiwanie rozmyte [interfejsu API wyszukiwania rozmytego usługi Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) wyszukiwanie punktu orientacyjnego. Wyszukiwanie rozmyte interfejsu API może obsługiwać dowolną kombinację rozmyte danych wejściowych. Odpowiedź z usługi wyszukiwania rozmytego jest następnie przeanalizowany w formacie GeoJSON i numerów PIN są dodawane do mapę, aby pokazać punktów orientacyjnych na mapie. 

Ostatni blok kodu dodaje granice aparatu mapy za pomocą mapy [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) właściwości.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule: 

* [Wyszukiwanie rozmyte interfejsu API usługi Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły: 
* [Uzyskiwanie informacji na podstawie współrzędnych](./map-get-information-from-coordinate.md)
* [Wyświetlanie instrukcji dotyczących trasy od A do B](./map-route.md)
