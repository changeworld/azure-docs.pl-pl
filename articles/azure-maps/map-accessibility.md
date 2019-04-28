---
title: Tworzenie dostępnej aplikacji za pomocą usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak utworzyć dostępny aplikacji przy użyciu usługi Azure Maps
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 09/17/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: ef948b4dca3d3800a81ac52f3a73beee2558d21c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769706"
---
# <a name="building-an-accessible-application"></a>Tworzenie dostępnej aplikacji

W tym artykule pokazano, jak utworzyć aplikację mapę, która może być używany przez czytnik zawartości ekranu.

## <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='500' scrolling='no' title='Wprowadź dostępnej aplikacji' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>wprowadzić dostępnej aplikacji</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Mapa jest dostarczany ze wstępnie utworzonych za pomocą funkcji ułatwień dostępu. Użytkownicy mogą przejść mapy za pomocą klawiatury. Jeśli czytnik zawartości ekranu jest uruchomiona, mapy, powiadomi użytkownika o zmianach stanu.
Na przykład użytkownicy są powiadamiani o zmiany mapy, gdy mapy jest panned lub powiększone. Dodatkowe informacje, które jest umieszczone na mapie podstawowej powinny mieć odpowiedni informacji tekstowych dla użytkowników czytników zawartości ekranu.

Za pomocą [okno podręczne](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) jest jednym ze sposobów osiągnięcia tego. W powyższym przykładzie wyszukiwania okna podręcznego z informacje tekstowe zostanie dodany do mapy dla każdego numeru pin, które jest umieszczone na mapie. Za pomocą [w podręcznym](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) [dołączyć](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach) metoda umożliwia okno podręczne była widoczna czytnika ekranu bez wizualnie wyświetlania okno podręczne na mapie.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy okna podręcznego i jego metody używane w tym artykule:

> [!div class="nextstepaction"]
> [Okno podręczne](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Zapoznaj się z więcej przykładów kodu:

> [!div class="nextstepaction"]
> [Strona przykładowy kod](https://aka.ms/AzureMapsSamples)
