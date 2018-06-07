---
title: Tworzenia aplikacji dostępny przy użyciu map Azure | Dokumentacja firmy Microsoft
description: Sposób tworzenia aplikacji dostępny przy użyciu mapy usługi Azure
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
ms.openlocfilehash: 2523287669628b8c58028cae9887743c20b6bc5e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655086"
---
# <a name="building-an-accessible-application"></a>Kompilowanie aplikacji niedostępny

W tym artykule przedstawiono sposób tworzenia aplikacji mapy, który może służyć do odczytywania zawartości ekranu.

## <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='500' scrolling='no' title='Aplikacja dostępna' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>dostępny aplikacja</a> mapach Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mapa jest wbudowane z niektóre funkcje ułatwień dostępu. Mapy, przy użyciu klawiatury może przejść użytkownik, a jeśli czytnik ekranu jest uruchomiona, mapy powiadomi użytkownika o zmianach stanu. Na przykład użytkownik zostanie powiadomiony o szerokości nowej mapy, długości, powiększenia i lokalizację, gdy panned lub powiększenie mapy. Wszelkie dodatkowe informacje, które znajduje się na mapie podstawowej powinien mieć odpowiednich informacji tekstowych dla użytkowników czytników ekranu. Przy użyciu [podręcznego](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) jest jednym ze sposobów osiągnięcia tego. W powyższym przykładzie wyszukiwania okna podręcznego o informacji tekstowych jest dodawany do mapy dla każdego numeru pin, który znajduje się na mapie. Przy użyciu [w podręcznym](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) attach — metoda umożliwia menu podręczne, aby była widoczna czytników ekranu bez wizualnie wyświetlania menu podręcznego na mapie.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o klasie podręcznego i jego metody używane w tym artykule:

* [Menu podręczne](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [Dołącz](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#attach)
    * [Usuń](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#remove)
    * [Otwórz](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [Zamknij](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

Zapoznaj się z naszym [kod przykładową stronę](http://aka.ms/AzureMapsSamples) dla scenariuszy mapowania.
