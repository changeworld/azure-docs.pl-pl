---
title: Jak używać formantu mapy mapy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać biblioteki Javascript po stronie klienta formantu mapy mapy Azure.
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 4ba4b9a9f2357d283ddc03a4723cb08b48d40a9b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599215"
---
# <a name="how-to-use-the-azure-maps-map-control"></a>Jak używać formantu mapy mapy Azure
Biblioteka języka Javascript po stronie klienta formantu mapy umożliwia renderowanie map i osadzone mapuje Azure funkcji do sieci web lub aplikacji mobilnej. 

## <a name="create-a-new-map-in-a-web-page"></a>Tworzenie nowej mapy na stronie sieci web

Na stronie sieci web można osadzić mapy, przy użyciu biblioteki Javascript po stronie klienta formantu mapy.

1. Utwórz nowy plik i nadaj mu nazwę MapSearch.html.

2. Dodać mapy Azure arkusza stylów i skrypt źródła odwołań do `<head>` elementu pliku:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
    ```
    
3. Aby renderować nowej mapy w przeglądarce, Dodaj **#map** odwołania w `<style>` elementu.

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. Aby można było zainicjować formantu mapy, należy zdefiniować nową sekcję w treści html i utworzyć skrypt. Użyj własnego klucza konta Azure mapy w skrypcie. Jeśli musisz utworzyć konto lub znaleźć Twojego klucza, zobacz [jak zarządzać Twoje konto Azure map i kluczy](how-to-manage-account-keys.md)

    ```html
    <div id="map">
        <script>
            var MapsAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": MapsAccountKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. Otwórz plik w przeglądarce sieci web i wyświetlić renderowanych mapy.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule pokazano, jak utworzyć mapę podstawowe kluczem mapy Azure. Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły: 

* [Tworzenie mapy](map-create.md)
* [Dodaj kod pin](map-add-pin.md)