---
title: Funkcje stylu mapy | Mapy Microsoft Azure
description: W tym artykule opisano funkcje związane z stylem dostępne w Microsoft Azure Maps Web SDK.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: cabf39f017afe440c883a63db57643c5c5367128
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189739"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Wybierz styl mapy w Azure Maps

Wiele [obsługiwanych stylów mapy w Azure Maps](./supported-map-styles.md) są dostępne w zestawie SDK sieci Web. W tym artykule przedstawiono sposób korzystania z funkcji związanych z stylem. Zapoznaj się z ustawieniem stylu podczas ładowania mapy i Dowiedz się, jak ustawić nowy styl mapy za pomocą kontrolki selektor stylów.

## <a name="set-style-on-map-load"></a>Ustawianie stylu podczas ładowania mapy

W poniższym kodzie opcja `style` mapy jest ustawiona na `grayscale_dark` przy inicjacji.

<br/>

<iframe height='500' scrolling='no' title='Ustawianie stylu podczas ładowania mapy' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Ustawianie stylu ładowania mapy</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="update-the-style"></a>Aktualizowanie stylu

W poniższym kodzie po załadowaniu wystąpienia mapy styl mapy jest aktualizowany z `road`, aby `satellite` przy użyciu funkcji [SetStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) .

<br/>

<iframe height='500' scrolling='no' title='Aktualizowanie stylu' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Aktualizowanie stylu</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker"></a>Dodaj selektor stylów

Poniższy kod dodaje [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) do mapy, dzięki czemu użytkownik może łatwo przełączać się między różnymi stylami mapy. Przełączenie stylu mapy za pomocą kontrolki styl mapy w prawym górnym rogu.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie selektora stylów' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro, <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>dodając selektor stylów</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Domyślnie w przypadku korzystania z warstwy cenowej S0 Azure Maps w kontrolce wybór stylu są wyświetlane wszystkie dostępne style. Jeśli chcesz zmniejszyć liczbę stylów na tej liście, Przekaż tablicę stylów, które mają być wyświetlane na liście w `mapStyle` opcję selektora stylów. Jeśli używasz systemu S1 i chcesz wyświetlić wszystkie dostępne style, ustaw opcję `mapStyles` selektora stylów, aby `"all"`.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Zmapować](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Dodawanie formantów do map:

> [!div class="nextstepaction"]
> [Dodaj kontrolki mapy](map-add-controls.md)

> [!div class="nextstepaction"]
> [Dodaj numer PIN](map-add-pin.md)
