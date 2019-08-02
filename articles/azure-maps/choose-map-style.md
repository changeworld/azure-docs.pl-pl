---
title: Funkcje stylu mapy w Azure Maps | Microsoft Docs
description: Dowiedz się więcej na temat funkcji związanych z stylem Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 52936b14264bd4fe1846ae365e1de447d594b612
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639062"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Wybierz styl mapy w Azure Maps

Azure Maps ma cztery różne style map do wyboru. Aby uzyskać więcej informacji na temat stylów mapy, zobacz [obsługiwane style mapy w Azure Maps](./supported-map-styles.md). W tym artykule przedstawiono sposób użycia funkcji związanych z stylem do ustawiania stylu obciążenia mapy, ustawiania nowego stylu i używania formantu selektora stylów.

## <a name="set-style-on-map-load"></a>Ustawianie stylu podczas ładowania mapy

<iframe height='500' scrolling='no' title='Ustawianie stylu podczas ładowania mapy' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Ustawianie stylu ładowania mapy</a> według Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Blok kodu powyżej ustawia klucz subskrypcji i tworzy obiekt mapy, z stylem ustawionym na grayscale_dark. Aby uzyskać instrukcje dotyczące sposobu tworzenia mapy, zobacz temat [Tworzenie mapy](./map-create.md) .

## <a name="update-the-style"></a>Aktualizowanie stylu

<iframe height='500' scrolling='no' title='Aktualizowanie stylu' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Aktualizowanie stylu</a> według<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ten blok kodu ustawia klucz subskrypcji i tworzy obiekt mapy bez wstępnego ustawiania stylu. Aby uzyskać instrukcje dotyczące sposobu tworzenia mapy, zobacz temat [Tworzenie mapy](./map-create.md) .

Drugi blok kodu używa metody setStyle mapy [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) do ustawienia stylu mapy do satelity.

## <a name="add-the-style-picker"></a>Dodaj selektor stylów

<iframe height='500' scrolling='no' title='Dodawanie selektora stylów' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro, <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>dodając selektor stylów</a> według Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() w <a href='https://codepen.io'>CodePen</a>.
</iframe>

Pierwszy blok kodu w powyższym kodzie ustawia klucz subskrypcji i tworzy obiekt mapy, styl mapy jest wstępnie ustawiony na grayscale_dark. Aby uzyskać instrukcje dotyczące sposobu tworzenia mapy, zobacz temat [Tworzenie mapy](./map-create.md) .

Drugi blok kodu konstruuje selektor stylu przy użyciu konstruktora [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) Atlas.

Selektor stylu umożliwia wybór stylu dla mapy. Trzeci blok kodu dodaje selektor stylu do mapy za pomocą [formantów mapy. Dodaj](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metodę. Selektor stylu znajduje się w obrębie **odbiornika zdarzeń** mapy, aby upewnić się, że ładuje się po całkowitym załadowaniu mapy.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Zmapować](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Dodaj kontrolkę do swoich map:

> [!div class="nextstepaction"]
> [Dodaj kontrolki mapy](./map-add-controls.md)

Dodaj numer PIN mapy:

> [!div class="nextstepaction"]
> [Dodaj numer PIN](./map-add-pin.md)
