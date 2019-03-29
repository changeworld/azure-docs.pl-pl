---
title: Zestaw SDK sieci Web obsługiwane przeglądarki — usługi Azure Maps | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o obsługiwanych przeglądarkach zestawu SDK sieci Web usługi Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: bc876fbf0eb15f887d57d4ddcca2301ef7233afa
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577343"
---
# <a name="web-sdk-supported-browsers"></a>Przeglądarki obsługiwane przez zestaw SDK sieci Web

Zestaw SDK usługi Azure Maps w sieci Web udostępnia funkcję Pomocnika [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-) do wykrywania, czy przeglądarki sieci web ma minimalne wymagane funkcje WebGL obsługuje ładowanie i renderowanie formantu mapy. 

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    //Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Klasyczna

Zestaw SDK usługi Azure Maps w sieci Web obsługuje następujące przeglądarki pulpitu.

- Bieżących i wcześniejszych wersji programu Microsoft Edge 
- Bieżącym i poprzednich wersji przeglądarki Chrome 
- Bieżącym i poprzednich wersji przeglądarki Firefox niższej 
- Bieżącym i poprzednich wersji przeglądarki Safari (Mac OS X) 

Zobacz też [docelowe starszej wersji przeglądarki](#Target-Legacy-Browsers).

## <a name="mobile"></a>Komórkowy

Zestaw SDK usługi Azure Maps w sieci Web obsługuje następujące przeglądarki dla urządzeń przenośnych.

-  Android
    * Bieżąca wersja programu Chrome na system Android 6.0 i nowsze
    * Dla programu Chrome WebView na system Android 6.0 i nowsze
- iOS
    * Safari Mobile dla bieżących i wcześniejszych wersji głównej systemu IOS
    * UIWebView i WKWebView na bieżącym i poprzednich wersji głównej systemu IOS
    * Bieżącej wersji przeglądarki Chrome dla systemu iOS

> [!TIP]
> W przypadku osadzania map w aplikacji mobilnej, za pomocą kontrolki WebView mogą wolisz używać [pakietu npm zestawu Azure SDK sieci Web mapy](https://www.npmjs.com/package/azure-maps-control) zamiast odwoływać się do usługi CDN hostowanych wersję zestawu SDK. Zmniejszy to czas ładowania zestawu SDK będą znajdować się już na urządzeniu użytkownika i nie muszą zostać pobrane w czasie wykonywania.

## <a name="nodejs"></a>Node.js

W przypadku następujących modułów zestawu SDK sieci Web są również obsługiwane w środowisku Node.js:

- Moduł usług ([dokumentacji](how-to-use-services-module.md) | [moduł npm](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Starsze przeglądarki

Jeśli potrzebujesz pod kątem starszych przeglądarkach, które mogą nie obsługiwać lub mają ograniczoną obsługę WebGL, zaleca się używać usługi Azure Maps w połączeniu z kontrolki mapy typu open source, takich jak [ulotka](https://leafletjs.com/). 


<iframe height="500" style="width: 100%;" scrolling="no" title="Usługi Azure Maps + ulotka" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióra <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>usługi Azure Maps + ulotka</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat zestawu SDK usługi sieci Web Azure Maps.

> [!div class="nextstepaction"]
> [Kontrolka mapy](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Moduł usług](how-to-use-services-module.md)
