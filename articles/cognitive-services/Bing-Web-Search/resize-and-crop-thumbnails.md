---
title: Ponowne rozmiary i przycinanie miniatur obrazu — interfejs API wyszukiwania w sieci Bing
titleSuffix: Azure Cognitive Services
description: Niektóre odpowiedzi z interfejsów API wyszukiwania Bing obejmują adresy URL do obrazów miniatur obsługiwanych przez bing, które można zmienić i przyciąć, i mogą zawierać parametry zapytania.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 630b86f55a537d109c851cb585cfccc34d229f83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110641"
---
# <a name="resize-and-crop-thumbnail-images"></a>Ponowne rozmiary i przycinanie obrazów miniatur

Niektóre odpowiedzi z interfejsów API wyszukiwania Bing obejmują adresy URL do obrazów miniatur obsługiwanych przez bing, które można zmienić i przyciąć, i mogą zawierać parametry zapytania. Przykład:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Jeśli wyświetlasz podzbiór tych miniatur, podaj opcję wyświetlania pozostałych obrazów.

> [!NOTE]
> Upewnij się, że przycinanie i zmiana rozmiaru obrazów miniatur zapewni scenariusz wyszukiwania, który respektuje prawa innych firm, zgodnie z [wymaganiami api wyszukiwania](use-display-requirements.md)Bing i wyświetlania .

## <a name="resize-a-thumbnail"></a>Ponowne rozmiary miniatury 

Aby zmienić rozmiar miniatury, bing zaleca, `w` aby określić `h` tylko jeden (szerokość) lub (wysokość) parametry zapytania w adresie URL miniatury. Określenie tylko wysokości lub szerokości umożliwia bing zachować oryginalny aspekt obrazu. Określ szerokość i wysokość w pikselach. 

Na przykład, jeśli oryginalna miniatura ma rozmiar 480x620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

I chcesz zmniejszyć jego rozmiar, `w` ustawić parametr na nową `336`wartość (na przykład ) i usunąć `h` parametr:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Jeśli określisz tylko wysokość lub szerokość miniatury, oryginalny współczynnik proporcji obrazu zostanie zachowany. Jeśli określisz oba parametry, a współczynnik proporcji nie zostanie zachowany, bing doda białe dopełnienie do obramowania obrazu.

Na przykład jeśli zmienisz rozmiar obrazu 480x359 na 200x200 bez przycinania, pełna szerokość będzie zawierać obraz, ale wysokość będzie zawierać 25 pikseli białego dopełnienia w górnej i dolnej części obrazu. Jeśli obraz był 359x480, lewa i prawa obramowania będzie zawierać białe dopełnienie. Jeśli przytniesz obraz, biała dopełnienie nie zostanie dodana.  

Na poniższej ilustracji przedstawiono oryginalny rozmiar obrazu miniatury (480x300).  
  
![Oryginalny obraz krajobrazowy](./media/resize-crop/bing-resize-crop-landscape.png)  
  
Na poniższej ilustracji przedstawiono obraz o przesań do 200x200. Współczynnik proporcji jest zachowywany, a górne i dolne obramowania są wyściełane białymi (czarne obramowanie w tym miejscu jest włączone, aby wyświetlić dopełnienie).  
  
![Obraz poziomy o przesiedlił się](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Jeśli określisz wymiary, które są większe niż oryginalna szerokość i wysokość obrazu, Bing doda białe dopełnienie po lewej i górnej obramowania.  

## <a name="request-different-thumbnail-sizes"></a>Żądanie różnych rozmiarów miniatur

Aby zażądać innego rozmiaru obrazu miniatury, usuń wszystkie parametry `id` zapytania `pid` z adresu URL miniatury, z wyjątkiem i parametrów. Następnie dodaj parametr `&w` zapytania (szerokość) lub `&h` (wysokość) z żądanym rozmiarem obrazu w pikselach, ale nie obydwoma. Bing zachowa oryginalny współczynnik proporcji obrazu. 

Aby zwiększyć szerokość obrazu określonego przez powyższy adres URL do 165 pikseli, należy użyć następującego adresu URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Jeśli zażądasz obrazu, który jest większy niż oryginalny rozmiar obrazu, Bing dodaje białe dopełnienie wokół obrazu w razie potrzeby. Jeśli na przykład oryginalny rozmiar obrazu to 474x316 `&w` i ustawiono na 500, bing zwróci obraz o wymiarach 500x333. Ten obraz będzie miał 8,5 pikseli białego dopełnienia wzdłuż górnej i dolnej krawędzi i 13 pikseli dopełnienia na lewej i prawej krawędzi.

Aby zapobiec Bing dodawanie biały dopełnienie, jeśli żądany rozmiar jest większy `&p` niż oryginalny rozmiar obrazu, należy ustawić parametr kwerendy na 0. Na przykład jeśli uwzględnisz `&p=0` parametr w powyższym adresie URL, Bing zwróci obraz 474x316 zamiast obrazu 500x333:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Jeśli określisz `&w` `&h` zarówno i parametry kwerendy, Bing zachowa proporcje obrazu i dodaje biały dopełnienie w razie potrzeby. Jeśli na przykład oryginalny rozmiar obrazu to 474x316 i ustawiono parametry szerokości i wysokości na`&w=200&h=200`200x200 ( Bing zwraca obraz zawierający 33 piksele białej dopełnienia u góry i u dołu. Jeśli uwzględnisz `&p` parametr kwerendy, Bing zwraca obraz 200x134.

## <a name="crop-a-thumbnail"></a>Przycinanie miniatury 

Aby przyciąć obraz, `c` należy dołączyć parametr kwerendy (przytnij). Można użyć następujących wartości:
  
- `4`&mdash; Stosunek blindów  
- `7`&mdash; Inteligentny stosunek  

### <a name="smart-ratio-cropping"></a>Inteligentne przycinanie proporcji

Jeśli zażądasz przycinania współczynnika `c` inteligentnego (przez ustawienie parametru `7`na ), Bing przyciąże obraz od środka jego regionu zainteresowania na zewnątrz, przy zachowaniu proporcji obrazu. Region zainteresowania jest obszar obrazu, który określa Bing zawiera najwięcej części importu. Poniżej przedstawiono przykład region zainteresowania.  
  
![Region zainteresowania](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Jeśli zmienić rozmiar obrazu i zażądać inteligentnego przycinania współczynnika, Bing zmniejsza obraz do żądanego rozmiaru przy zachowaniu współczynnika proporcji. Bing następnie przycina obraz na podstawie wymiarów o rozmiarze. Na przykład jeśli szerokość o zmniejszeniu jest mniejsza lub równa wysokości, Bing przyciągnie obraz po lewej i prawej stronie środka regionu zainteresowania. W przeciwnym razie Bing przyciąże go do góry i na dole środka regionu zainteresowania.  
  
 
Poniżej przedstawiono obraz zredukowany do 200x200 przy użyciu przycinania smart ratio. Ponieważ bing mierzy obraz z lewego górnego rogu, dolna część obrazu jest przycięta. 
  
![Obraz krajobrazu przycięty do 200x200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
Poniżej przedstawiono obraz zredukowany do 200x100 przy użyciu przycinania smart ratio. Ponieważ bing mierzy obraz z lewego górnego rogu, dolna część obrazu jest przycięta. 
   
![Obraz krajobrazu przycięty do 200x100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
Poniżej przedstawiono obraz zredukowany do 100x200 przy użyciu przycinania smart ratio. Ponieważ bing mierzy obraz od środka, lewa i prawa część obrazu jest przycięta.
  
![Obraz poziomy przycięty do 100x200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Jeśli usługa Bing nie może określić regionu zainteresowania obrazu, usługa użyje przycinania blind ratio.  

### <a name="blind-ratio-cropping"></a>Przycinanie blind ratio

Jeśli zażądasz przycinania blind `c` ratio `4`(przez ustawienie parametru na), Bing używa następujących reguł do przycinania obrazu.  
  
- Jeśli `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`obraz jest mierzony od lewego górnego rogu i przycięty u dołu.  
- Jeśli `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`obraz jest mierzony od środka i przycięty do lewej i prawej strony.  

Poniżej przedstawiono obraz portretowy o sytórze 225x300.  
  
![Oryginalny obraz słonecznika](./media/resize-crop/bing-resize-crop-sunflower.png)
  
Poniżej przedstawiono obraz zredukowany do 200x200 przy użyciu przycinania blind ratio. Obraz jest mierzony od lewego górnego rogu, co powoduje przycięcie dolnej części obrazu.  
  
![Obraz słonecznika przycięty do 200x200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
Poniżej przedstawiono obraz zredukowany do 200x100 przy użyciu przycinania blind ratio. Obraz jest mierzony od lewego górnego rogu, co powoduje przycięcie dolnej części obrazu.  
  
![Obraz słonecznika przycięty do 200x100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
Poniżej przedstawiono obraz zredukowany do 100x200 przy użyciu przycinania blind ratio. Obraz jest mierzony od środka, co powoduje przycinanie lewej i prawej części obrazu.  
  
![Obraz słonecznika przycięty do 100x200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Następne kroki

* [Co to są interfejsy API wyszukiwania Bing?](bing-api-comparison.md)
* [Wymagania dotyczące użycia i wyświetlania interfejsu API wyszukiwania Bing](use-display-requirements.md)
