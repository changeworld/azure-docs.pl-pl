---
title: Poziom powiększenia i kafelka siatki w społeczności Maps Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o poziomach powiększenia i kafelka siatki w społeczności Maps Azure
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: 330333569f094fe3cec7f73ee3b20107ec70f5b5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="zoom-levels-and-tile-grid"></a>Poziomy powiększenia i siatka kafelków
Azure mapy Użyj projekcji kulistego Mercator współrzędnych (EPSG: 3857).

Świecie jest podzielona na kafelkach kwadratowy. Renderowania (rastrowe) zawiera 19 poziomy powiększenia ponumerowane od 0 do 18. Renderowania (wektor) zawiera 21 poziomy powiększenia ponumerowane od 0 do 20. Na poziomie powiększenia 0 cały świecie mieści się na jednej kafelków:

![Kafelek świata](./media/zoom-levels-and-tile-grid/world0.png)

Poziom powiększenia 1 używa cztery Kafelki do renderowania na świecie: 2 x 2 kwadratowe

![Lewy górny Kafelek świata](./media/zoom-levels-and-tile-grid/world1a.png)     ![World kafelka w górę na prawo](./media/zoom-levels-and-tile-grid/world1c.png) 

![Lewy dolny kafelka świata](./media/zoom-levels-and-tile-grid/world1b.png)     ![World kafelka u dołu po prawej](./media/zoom-levels-and-tile-grid/world1d.png) 


Każdy poziom powiększenia kolejnych quad bez reszty Kafelki poprzedniego tworzenia siatki 2<sup>powiększenie</sup> x 2<sup>powiększenie</sup>. Poziom powiększenia 20 jest siatka 2<sup>20</sup> x 2<sup>20</sup>, lub 1 048 576 x 1 048 576 Kafelki (109,951,162,778 Kafelki w całości).

Poniższa tabela zawiera pełną listę wartości poziom powiększenia:

|poziom powiększenia|liczniki na piksel|liczniki/kafelka po stronie|
|--- |--- |--- |
|0|156543|40075008|
|1|78271.5|20037504|
|2|39135.8|10018764.8|
|3|19567.9|5009382.4|
|4|9783.9|2504678.4|
|5|4892|1252352|
|6|2446|626176|
|7|1223|313088|
|8|611.5|156544|
|9|305.7|78259.2|
|10|152.9|39142.4|
|11|76.4|19558.4|
|12|38.2|9779.2|
|13|19.1|4889.6|
|14|9.6|2457.6|
|15|4.8|1228.8|
|16|2.4|614.4|
|17|1.2|307.2|
|18|0,6|152.8|
|19|0,3|76.4|
|20|0,15|38.2|

Kafelki są nazywane powiększenia poziomu i x i y współrzędne odpowiadający pozycji kafelka na siatce tego poziomu powiększenia.

Podczas określania poziom powiększenia do użycia, należy pamiętać, że każda lokalizacja jest w stanie stałym na jego kafelka. Oznacza to, że liczba Kafelki niezbędne do wyświetlania danego expanse terytorium ma zależne od określonych umieszczania powiększenia siatki na świecie. Na przykład, jeśli występują dwa punkty 900 liczników od siebie, jego *może* przyjmuje tylko trzy Kafelki, aby wyświetlić trasy między nimi na poziomie powiększenia 17. Jednak jeśli western punkt znajduje się po prawej stronie jego kafelków i Wschodniej punktu z lewej strony jej kafelka, może upłynąć cztery Kafelki:

![Wersja demonstracyjna Skala powiększenia](./media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

Po określeniu powiększenia x i y można obliczyć wartości. Górny Kafelek po lewej stronie każdego siatki powiększenia jest x = 0, y = 0; Kafelek prawym dolnym rogu jest x = 2<sup>powiększenie -1</sup>, y = 2<sup>1 powiększenia</sup>.

Oto siatki powiększenia poziomu powiększenia 1:

![Powiększenie siatki na poziomie powiększenia 1](./media/zoom-levels-and-tile-grid/api_x_y.png)
