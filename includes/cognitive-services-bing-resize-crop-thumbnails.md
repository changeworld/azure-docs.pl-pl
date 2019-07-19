---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: f96c3a693ce8fc099374c998b35ce2fa90f4bb3f
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868977"
---
Niektóre odpowiedzi Bing zawierają adresy URL obrazów miniatur obsługiwanych przez usługę Bing. Można zmienić rozmiar i przyciąć obrazy miniatur. 

> [!NOTE]
> Upewnij się, że rozmiar i przycinanie miniatury zawierają scenariusz wyszukiwania i Uwzględnij prawa stron trzecich zgodnie z wymaganiami Wyszukiwanie Bing interfejsu API i wyświetlaj wymagania.


Aby zmienić rozmiar obrazu, w adresie URL miniatury należy dodać parametr zapytania w (Width), h (Height) lub oba te elementy. Określ szerokość i wysokość w pikselach. Na przykład:  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
Jeśli określisz tylko szerokość lub tylko parametr zapytania Height, Bing zachowa współczynnik proporcji obrazu. Jeśli określisz szerokość i wysokość i nie zachowasz oryginalnego współczynnika proporcji obrazu, Bing doda białe uzupełnienie obramowania obrazu. Na przykład jeśli zmienisz rozmiar obrazu 480x359 na 200x200 bez przycinania, Pełna szerokość zawiera obraz, ale wysokość zawiera 25 pikseli białego wypełnienia u góry i u dołu obrazu. Ta sama wartość powinna być równa true, jeśli obraz został 359x480, z wyjątkiem lewego i prawego obramowania będzie zawierać białe uzupełnienie. W przypadku przycinania obrazu białe uzupełnienie nie zostanie dodane.  

 
Na poniższej ilustracji przedstawiono oryginalny rozmiar obrazu miniatury (480x300).  
  
![Oryginalny obraz krajobrazu](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
Na poniższej ilustracji przedstawiono rozmiar obrazu zmieniony na 200x200. Współczynnik proporcji jest obsługiwany, a obramowanie górne i dolne jest dopełniane bielą (czarny obramowanie jest dołączane, aby pokazać uzupełnienie).  
  
![Obraz o zmienionym rozmiarze](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  



Jeśli określisz wymiary, które są większe niż oryginalna szerokość i wysokość obrazu, obraz zostanie uzupełniony bielą od lewej i górnego obramowania.  
  
Aby przyciąć obraz, należy uwzględnić parametr zapytania c (przycinanie). Poniżej przedstawiono możliwe wartości, które można określić.  
  
- 4&mdash;wskaźnik niewidomy  
- 7&mdash;inteligentny współczynnik  
  
Jeśli zażądasz przycinania inteligentnego współczynnika (c = 7), obraz zostanie przycięty z środkowej części regionu obrazu na zewnątrz przy zachowaniu współczynnika proporcji obrazu. Regionem zainteresowania jest obszar obrazu, który jest określany przez usługę Bing, zawierający większość części importu. Poniżej przedstawiono przykładowy region zainteresowania.  
  
![Region zainteresowania](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

W przypadku zmiany rozmiaru obrazu i zażądania przycinania inteligentnego wskaźnika obraz jest zmniejszany do żądanego rozmiaru przy zachowaniu współczynnika proporcji. Obraz zostanie następnie przycięty na podstawie wymiarów o zmienionym rozmiarze. Na przykład jeśli szerokość o zmienionym rozmiarze jest mniejsza lub równa wysokości, obraz zostanie przycięty do lewej i prawej strony środka zainteresowania. W przeciwnym razie obraz zostanie przycięty do góry i dołu środka obszaru zainteresowania.  
  
 
Poniżej przedstawiono obraz zredukowany do 200x200 przy użyciu inteligentnego przycinania proporcji.  
  
![Obraz poziomy przycięty do 200x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
Poniżej przedstawiono obraz zredukowany do 200x100 przy użyciu inteligentnego przycinania proporcji.  
   
![Obraz poziomy przycięty do 200x100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
Poniżej przedstawiono obraz zredukowany do 100x200 przy użyciu inteligentnego przycinania proporcji.  
  
![Obraz poziomy przycięty do 100x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)



Jeśli Bing nie można określić regionu, w którym znajduje się obraz, Bing używa przycinania ślepej proporcji.  
  
Jeśli zażądasz przycinania wskaźnika (c = 4), Bing Przycinanie obrazu przy użyciu poniższych reguł.  
  
- Jeśli (oryginalna Szerokość obrazu/oryginalna wysokość obrazu) < (żądana Szerokość obrazu/żądana wysokość obrazu), obraz jest mierzony od lewego górnego rogu i przycięty u dołu.  
- Jeśli (oryginalna Szerokość obrazu/oryginalna wysokość obrazu) > (żądana Szerokość obrazu/żądana wysokość obrazu), obraz jest mierzony od środka i przycięty do lewej i prawej.  



Poniżej przedstawiono obraz w pionie, który jest 225x300.  
  
![Oryginalny obraz słonecznika](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
Poniżej przedstawiono obraz zredukowany do 200x200 przy użyciu przycinania niewidomych wskaźników. Obraz jest mierzony od lewego górnego rogu, w dolnej części obrazu, który zostanie przycięty.  
  
![Obraz słonecznika przycięty do 200x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
Poniżej przedstawiono obraz zredukowany do 200x100 przy użyciu przycinania niewidomych wskaźników. Obraz jest mierzony od lewego górnego rogu, w dolnej części obrazu, który zostanie przycięty.  
  
![Obraz słonecznika przycięty do 200x100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
Poniżej przedstawiono obraz zredukowany do 100x200 przy użyciu przycinania niewidomych wskaźników. Obraz jest mierzony od środka, co powoduje wycinanie lewej i prawej części obrazu.  
  
![Obraz słonecznika przycięty do 100x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)

