---
title: Jak używać kontrolki mapy platformy Azure Maps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać biblioteki Javascript po stronie klienta usługi Azure Maps — formant mapa.
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 5b8703c218790549a0cf5a319345132a0eca66ce
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44025193"
---
# <a name="how-to-use-the-azure-maps-map-control"></a>Jak używać kontrolki mapy platformy Azure Maps
Biblioteka języka Javascript po stronie klienta — formant mapa programu umożliwia renderowanie mapy i funkcji usługi Azure Maps osadzone w sieci web lub aplikacji mobilnej. 

## <a name="create-a-new-map-in-a-web-page"></a>Tworzenie nowej mapy na stronie sieci web

Mapę można osadzić na stronie sieci web przy użyciu biblioteki Javascript po stronie klienta formantu mapy.

1. Utwórz nowy plik i nadaj mu nazwę MapSearch.html.

2. Dodaj usługi Azure Maps arkusza stylów i skrypt źródło odwołania do `<head>` element pliku:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
    ```
    
3. Aby renderować nowej mapy w przeglądarce, Dodaj **#map** odwoływać w `<style>` elementu.

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. Aby zainicjować formantu mapy, należy zdefiniować nową sekcję w treści html i utworzyć skrypt. Użyj własnego klucza konta usługi Azure Maps w skrypcie. Jeśli musisz utworzyć konto usługi lub znaleźć Twojego klucza, zobacz [jak zarządzać swoim kontem usługi Azure Maps i klucze](how-to-manage-account-keys.md)

    ```html
    <div id="map">
        <script>
            var MapsAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": MapsAccountKey,
                center: [-122.33263,47.59093],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. Otwórz plik w przeglądarce sieci web i wyświetlić mapę renderowany.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule pokazano, jak utworzyć mapę podstawową za pomocą klucza usługi Azure Maps. Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły: 

* [Tworzenie mapy](map-create.md)
* [Wybierz style mapy](choose-map-style.md)
