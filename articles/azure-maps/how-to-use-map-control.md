---
title: Jak używać kontrolki mapy platformy Azure Maps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać biblioteki Javascript po stronie klienta usługi Azure Maps — formant mapa.
author: dsk-2015
ms.author: dkshir
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 850f9b28c112c11fd98a8abc81a1811cd26d81cc
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166036"
---
# <a name="use-the-azure-maps-map-control"></a>Korzystanie z kontrolki mapy usługi Azure Maps

Biblioteka języka Javascript po stronie klienta — formant mapa programu umożliwia renderowanie mapy i funkcji usługi Azure Maps osadzone w sieci web lub aplikacji mobilnej.

## <a name="create-a-new-map-in-a-web-page"></a>Tworzenie nowej mapy na stronie sieci web

Mapę można osadzić na stronie sieci web przy użyciu biblioteki Javascript po stronie klienta formantu mapy.

1. Utwórz nowy plik i nadaj mu nazwę **MapSearch.html**.

2. Dodaj usługi Azure Maps arkusza stylów i skrypt źródło odwołania do `<head>` element pliku:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
    ```

3. Aby renderować nowej mapy w przeglądarce, należy dodać **#map** odwoływać w `<style>` elementu:

    ```html
    <style>
        #map {
            width: 100%;
            height: 100%;
        }
    </style>
    ```

4. Aby zainicjować formantu mapy, zdefiniować nową sekcję w treści html i utworzyć skrypt. Użyj własnego klucza konta usługi Azure Maps w skrypcie. Jeśli musisz utworzyć konto usługi lub znaleźć Twojego klucza, zobacz [jak zarządzać swoim kontem usługi Azure Maps i klucze](how-to-manage-account-keys.md). **Funkcji setLanguage** metody określa język, który ma być używany dla etykiety mapy i kontrolek. Aby uzyskać więcej informacji na temat obsługiwanych języków, zobacz [obsługiwane języki](https://docs.microsoft.com/azure/azure-maps/supported-languages).

    ```html
    <div id="map">
        <script>
            atlas.setSubscriptionKey("<_your account key_>");
            atlas.setLanguage("en");
            var map = new atlas.Map("map", {
                center: [-122.33263,47.59093],
                zoom: 12
            });
        </script>
    </div>
    ```

5. Otwórz plik w przeglądarce sieci web i wyświetlić mapę renderowany.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak utworzyć mapę przy użyciu pełny przykład:

> [!div class="nextstepaction"]
> [Tworzenie mapy](map-create.md)

Dowiedz się, jak do nadawania stylu mapy:

> [!div class="nextstepaction"]
> [Wybierz style mapy](choose-map-style.md)
