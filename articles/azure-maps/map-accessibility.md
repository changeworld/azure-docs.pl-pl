---
title: Tworzenie dostępnej aplikacji za pomocą usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak utworzyć dostępny aplikacji przy użyciu usługi Azure Maps
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 05/18/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: 537a8c80dc0d1fcb2f536d0e30200de19a2111a4
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867056"
---
# <a name="building-an-accessible-application"></a>Tworzenie dostępnej aplikacji

W tym artykule pokazano, jak utworzyć aplikację mapę, która może być używany przez czytnik zawartości ekranu.

## <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='500' scrolling='no' title='Wprowadź dostępnej aplikacji' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>wprowadzić dostępnej aplikacji</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Mapa jest wstępnie utworzone za pomocą niektóre funkcje ułatwień dostępu. Użytkownik może przejść na mapie za pomocą klawiatury, i jeśli czytnik zawartości ekranu jest uruchomiona, mapy powiadomi użytkownika o zmianach stanu. Na przykład użytkownik zostanie powiadomiony o szerokości nowej mapy, długości, powiększenia i lokalizacja, mapy jest panned lub element. Dodatkowe informacje, które jest umieszczone na mapie podstawowej powinny mieć odpowiedni informacji tekstowych dla użytkowników czytników zawartości ekranu. Za pomocą [okno podręczne](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) jest jednym ze sposobów osiągnięcia tego. W powyższym przykładzie wyszukiwania okna podręcznego z informacje tekstowe zostanie dodany do mapy dla każdego numeru pin, które jest umieszczone na mapie. Za pomocą [w podręcznym](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) dołączyć metoda umożliwia okno podręczne była widoczna czytnika ekranu bez wizualnie wyświetlania okno podręczne na mapie.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy okna podręcznego i jego metody używane w tym artykule:

* [Okno podręczne](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [Dołącz](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#attach)
    * [Usuń](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#remove)
    * [Otwórz](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [Zamknij](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

Zapoznaj się z naszym [kodu przykładowej strony](http://aka.ms/AzureMapsSamples) do scenariuszy mapowania.
