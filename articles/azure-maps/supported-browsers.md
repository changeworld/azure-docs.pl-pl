---
title: Przeglądarki obsługiwane przez web SDK | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się o obsługiwanych przeglądarkach dla microsoft azure maps web SDK i jak sprawdzić, czy przeglądarka jest obsługiwana przeglądarka.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: e81b15b974469d319384a67b08512130b7876a30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988791"
---
# <a name="web-sdk-supported-browsers"></a>Przeglądarki obsługiwane przez zestaw Web SDK

Zestaw Azure Maps Web SDK udostępnia funkcję pomocniczą o nazwie [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Ta funkcja wykrywa, czy przeglądarka internetowa ma minimalny zestaw funkcji WebGL wymaganych do obsługi ładowania i renderowania formantu mapy. Oto przykład korzystania z tej funkcji:

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Aplikacje klasyczne

Zestaw Azure Maps Web SDK obsługuje następujące przeglądarki na komputerach:

- Microsoft Edge (bieżąca i poprzednia wersja)
- Google Chrome (aktualna i poprzednia wersja)
- Mozilla Firefox (aktualna i poprzednia wersja)
- Apple Safari (Mac OS X) (wersja bieżąca i poprzednia)

Zobacz też [Docelowe starsze przeglądarki](#Target-Legacy-Browsers) w dalszej części tego artykułu.

## <a name="mobile"></a>Urządzenia przenośne

Zestaw Azure Maps Web SDK obsługuje następujące przeglądarki mobilne:

- Android
  - Aktualna wersja Chrome na Androida 6.0 lub nowszym
  - Chrome WebView na Androida 6.0 lub nowszym
- iOS
  - Mobile Safari w bieżącej i poprzedniej głównej wersji systemu iOS
  - UIWebView i WKWebView w bieżącej i poprzedniej głównej wersji systemu iOS
  - Bieżąca wersja Chrome dla systemu iOS

> [!TIP]
> Jeśli osadzasz mapę wewnątrz aplikacji mobilnej przy użyciu formantu WebView, możesz użyć [pakietu npm zestawu Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) zamiast odwoływać się do wersji zestawu SDK hostowanego w usłudze Azure Content Delivery Network. Takie podejście skraca czas ładowania, ponieważ zestawu SDK jest już na urządzeniu użytkownika i nie trzeba pobierać w czasie wykonywania.

## <a name="nodejs"></a>Node.js

Następujące moduły SDK sieci Web są również obsługiwane w pliku Node.js:

- Moduł usług[documentation](how-to-use-services-module.md) | [(moduł dokumentacji npm)](https://www.npmjs.com/package/azure-maps-rest)

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>Kierowanie starszych przeglądarek

Możesz chcieć kierować reklamy na starsze przeglądarki, które nie obsługują webgl lub które mają tylko ograniczoną obsługę. W takich przypadkach zaleca się korzystanie z usług Usługi Azure Maps wraz z formantem mapy typu open source, takim jak [ulotka.](https://leafletjs.com/) Oto przykład:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapy Azure + ulotka" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pen <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + ulotka</a> przez Usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o sdk azure maps web:

> [!div class="nextstepaction"]
> [Kontrolka mapy](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Moduł usług](how-to-use-services-module.md)
