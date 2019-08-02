---
title: Tworzenie dostępnej aplikacji z Azure Maps | Microsoft Docs
description: Jak skompilować dostępną aplikację przy użyciu Azure Maps
services: azure-maps
author: chgennar
ms.author: chgennar
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
manager: timlt
ms.openlocfilehash: 8865027c895be09150797608e43184f1fdefb267
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638793"
---
# <a name="building-an-accessible-application"></a>Tworzenie dostępnej aplikacji

W tym artykule przedstawiono sposób tworzenia aplikacji mapy, która może być używana przez czytnik ekranu.

## <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='500' scrolling='no' title='Utwórz dostępną aplikację' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro, aby <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>udostępnić aplikację</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mapa jest przedbudowana z funkcjami ułatwień dostępu. Użytkownicy mogą nawigować po mapie przy użyciu klawiatury. Jeśli czytnik ekranu jest uruchomiony, mapa będzie powiadamiać użytkownika o zmianach stanu.
Na przykład użytkownicy są powiadamiani o zmianach mapy, gdy mapa jest przesuwana lub powiększa. Wszelkie dodatkowe informacje umieszczane na mapie podstawowej powinny mieć odpowiednie informacje tekstowe dla użytkowników czytnika ekranu.

Korzystanie z [menu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) podręcznego jest jednym ze sposobów osiągnięcia tego celu. W powyższym przykładzie wyszukiwania do mapy jest dodawane okno podręczne z informacjami o tekstach, które są umieszczane na mapie. Użycie metody [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) [Attach](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach) dołączania umożliwia wyświetlenie okna podręcznego przez czytnik ekranu bez wizualnego wyświetlania okna podręcznego na mapie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasie popup i jej metodach używanych w tym artykule:

> [!div class="nextstepaction"]
> [Elementy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Zapoznaj się z dodatkowymi przykładami kodu:

> [!div class="nextstepaction"]
> [Przykładowa strona kodowa](https://aka.ms/AzureMapsSamples)
