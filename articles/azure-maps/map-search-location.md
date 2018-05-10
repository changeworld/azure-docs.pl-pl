---
title: Pokaż wyniki wyszukiwania przy użyciu map Azure | Dokumentacja firmy Microsoft
description: Jak wykonać żądania wyszukiwania przy użyciu map Azure, a następnie Wyświetl wyniki na mapie Javascrip
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: f66b1f93d7bc4c2e7c511c10d7091760e8f6d023
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="show-search-results-on-the-map"></a>Pokaż wyniki wyszukiwania na mapie

W tym artykule przedstawiono sposób tworzenia żądania wyszukiwania i wyświetlić wyniki wyszukiwania na mapie. 

## <a name="understand-the-code"></a>Kodu

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
