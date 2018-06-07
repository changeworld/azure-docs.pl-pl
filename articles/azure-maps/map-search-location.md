---
title: Pokaż wyniki wyszukiwania przy użyciu map Azure | Dokumentacja firmy Microsoft
description: Jak wykonać żądania wyszukiwania przy użyciu map Azure, a następnie Wyświetl wyniki na mapie Javascrip
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b77737b16b23ed00c8f12f84e6a8558a665a7d15
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599885"
---
# <a name="show-search-results-on-the-map"></a>Pokaż wyniki wyszukiwania na mapie

W tym artykule przedstawiono sposób tworzenia żądania wyszukiwania i wyświetlić wyniki wyszukiwania na mapie. 

## <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='500' scrolling='no' title='Pokaż wyniki wyszukiwania na mapie' src='//codepen.io/azuremaps/embed/KQbaeM/?height=519&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Pokaż wyniki na mapie</a> mapach Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Widać [Tworzenie mapy](./map-create.md) instrukcje.

W drugim bloku kodu tworzy i dodaje warstwy wyszukiwania PIN na mapie. Widać [dodać numer pin na mapie](./map-add-pin.md) instrukcje.

Trzeci bloku kodu wysyła [XMLHttpRequest](https://xhr.spec.whatwg.org/) do [interfejsu API wyszukiwania rozmytego Azure map](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Ostatni blok kodu analizuje przychodzące odpowiedzi. Dla pomyślnej odpowiedzi zbiera informacje współrzędne geograficzne dla każdej lokalizacji zwracane. Dodaje wszystkie punkty lokalizacji do mapy jako kodów PIN, a dopasowuje granice mapy do renderowania numerów PIN.


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule: 

* [Azure mapuje Wyszukiwanie rozmyte interfejsu API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
    
Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły: 
* [Uzyskaj informacje z współrzędnych](./map-get-information-from-coordinate.md)
* [Pokaż instrukcjami z zakresu od A do B](./map-route.md)
