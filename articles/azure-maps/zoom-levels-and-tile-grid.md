---
title: Poziomy powiększenia i Siatka sąsiadująca w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o poziomy powiększenia i Siatka sąsiadująca w usługi Azure Maps
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: e7dcdb960fbd9196aca8b667269a4c6e5a1fb8f9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59261273"
---
# <a name="zoom-levels-and-tile-grid"></a>Poziomy powiększenia i siatka kafelków
Usługi Azure Maps Użyj rzutowania kulistego Mercator współrzędnych (EPSG: 3857).

Świat jest podzielony na kafelków w kształcie kwadratu. Usługi Azure maps zapewnia rastrowych i wektorowych kafelków 23 poziomami powiększenia, numerowane od 0 do 22. Na poziom powiększenia 0 cały świat pasuje do pojedynczego kafelka:

![Kafelek Światowej](./media/zoom-levels-and-tile-grid/world0.png)

Poziom powiększenia 1 używa cztery Kafelki do renderowania na świecie: 2 x 2 kwadrat

![World kafelka u góry z lewej](media/zoom-levels-and-tile-grid/world1a.png)     ![World kafelka u góry z prawej](media/zoom-levels-and-tile-grid/world1c.png) 

![World kafelka na dole z lewej](media/zoom-levels-and-tile-grid/world1b.png)     ![World kafelka na dole z prawej](media/zoom-levels-and-tile-grid/world1d.png) 

Każdy poziom powiększenia dodatkowe cztery bez reszty Kafelki poprzedniemu, Tworzenie siatki 2<sup>powiększenia</sup> x 2<sup>powiększenia</sup>. Poziom powiększenia 22 jest siatka 2<sup>22</sup> x 2<sup>22</sup>, lub kafelków 4,194,304 x 4,194,304 (17,592,186,044,416 Kafelki w sumie).

Usługi Azure Maps mapie kontrolki sieci web i Android, obsługują poziomy 25 powiększenia poziomy przybliżenia, numerowane od 0 do 24. Mimo, że dane będą dostępne tylko w kiedy Kafelki są dostępne na poziomach powiększenia.

Poniższa tabela zawiera pełną listę wartości dla poziomu powiększenia:

|Poziom powiększenia|Liczniki na piksel|Liczniki/kafelka po stronie|
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
|18|Update 0.6|152.8|
|19|0.3|76.4|
|20|0.15|38.2|
|21|0.075|19.1|
|22|0.0375|9.55|
|23|0.01875|4.775|
|24|0.009375|2.3875|

Kafelki są nazywane współrzędnymi powiększenia poziom x i y odpowiadający kafelka pozycji na siatce tego poziomu powiększenia.

Podczas ustalania, które poziom powiększenia do użycia, pamiętaj, że każda lokalizacja jest w stanie stały jej Kafelek. Oznacza to, że liczba kafelków potrzebne do wyświetlenia danego expanse terytorium jest zależne od określonych umieszczania powiększenia siatki na świecie. Na przykład jeśli istnieją dwa punkty 900 liczniki od siebie, jego *może* przyjmuje tylko trzy kafelków do wyświetlenia trasy między nimi na poziom powiększenia 17. Jednak jeśli western punkt znajduje się po prawej stronie jej Kafelek i Wschodniej punkt po lewej stronie jego kafelka, może upłynąć cztery Kafelki:

![Skala pokaz powiększenia](media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

Po określeniu poziom powiększenia x i y można obliczyć wartości. Jest w lewym górnym rogu kafelka w każdej siatce powiększenia x = 0, y = 0. Kafelek prawego dolnego rogu zostało wykonane w x = 2<sup>zoom -1</sup>, y = 2<sup>1 powiększenie</sup>.

Oto siatkę powiększenia poziom powiększenia 1:

![Powiększenie siatkę poziom powiększenia 1](media/zoom-levels-and-tile-grid/api_x_y.png)
