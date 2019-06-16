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
ms.openlocfilehash: 84c5dbcf5073ba8c0ae662af019cde590a9adf10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64686643"
---
# <a name="web-sdk-supported-browsers"></a>Przeglądarki obsługiwane przez zestaw Web SDK

Zestaw SDK usługi Azure Maps w sieci Web udostępnia funkcję pomocnika o nazwie [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Ta funkcja wykrywa, czy przeglądarki sieci web ma minimalny zestaw funkcji WebGL potrzebnych do ładowania i renderowanie formantu mapy. Poniżej przedstawiono przykładowy sposób korzystania z funkcji:

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Pulpitu

Zestaw SDK usługi Azure Maps w sieci Web obsługuje następujące przeglądarki pulpitu:

- Microsoft Edge (wersja bieżącym i poprzednim)
- Google Chrome (w bieżących i wcześniejszych wersji)
- Mozilla Firefox (bieżących i wcześniejszych wersji)
- Apple Safari (Mac OS X) (w bieżących i wcześniejszych wersji)

Zobacz też [docelowe starszej wersji przeglądarki](#Target-Legacy-Browsers) w dalszej części tego artykułu.

## <a name="mobile"></a>Urządzenia przenośne

Zestaw SDK usługi Azure Maps w sieci Web obsługuje następujące przeglądarki dla urządzeń przenośnych:

- Android
  - Bieżąca wersja programu Chrome na system Android 6.0 lub nowszy
  - Dla programu Chrome WebView w systemie Android 6.0 lub nowszym
- iOS
  - Safari Mobile dla bieżących i wcześniejszych wersji głównej systemu IOS
  - UIWebView i WKWebView na bieżącym i poprzednich wersji głównej systemu IOS
  - Bieżącej wersji przeglądarki Chrome dla systemu iOS

> [!TIP]
> Jeśli osadzasz mapę w aplikacji mobilnej za pomocą kontrolki WebView, warto użyć [pakietu npm zestawu Azure SDK sieci Web mapy](https://www.npmjs.com/package/azure-maps-control) zamiast odwoływać się do wersji zestawu SDK, który jest hostowany w usłudze Azure Content Delivery Sieć. Takie podejście skraca czas ładowania, ponieważ zestaw SDK jest już znajdować się na urządzeniu użytkownika, a nie muszą zostać pobrane w czasie wykonywania.

## <a name="nodejs"></a>Node.js

W przypadku następujących modułów zestawu SDK sieci Web są również obsługiwane w środowisku Node.js:

- Moduł usług ([dokumentacji](how-to-use-services-module.md) | [moduł npm](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Starsze przeglądarki

Możesz chcieć starszych przeglądarkach, które nie obsługują WebGL lub który ograniczona tylko pomoc techniczną dla docelowych. W takich przypadkach zalecane jest użycie usługi Azure Maps wraz z kontrolki mapy typu open source, takich jak [ulotka](https://leafletjs.com/). Oto przykład:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Usługi Azure Maps + ulotka" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióra <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>usługi Azure Maps + ulotka</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat zestawu SDK usługi sieci Web Azure Maps:

> [!div class="nextstepaction"]
> [Kontrolka mapy](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Moduł usług](how-to-use-services-module.md)
