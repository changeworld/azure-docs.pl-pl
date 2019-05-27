---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: f96c3a693ce8fc099374c998b35ce2fa90f4bb3f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124716"
---
Odpowiedzi na niektóre Bing obejmują adresy URL do obrazów miniatury obsługiwane przez usługę Bing. Możesz zmienić rozmiar i przycinanie obrazów miniatur. 

> [!NOTE]
> Upewnij się, rozmiar i przycinanie miniatury udostępniają scenariusz wyszukiwania oraz przestrzeganie praw innych firm, zgodnie z potrzebami przy użyciu interfejsu API wyszukiwania Bing i wyświetlają wymagania dotyczące.


Aby zmienić rozmiar obrazu, należy dołączyć kwerend Sz (szerokość) parametr h (wysokość) zapytania lub oba parametry w adresie URL miniatury. Określ szerokość i wysokość w pikselach. Na przykład:  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
Jeśli określisz tylko szerokość lub parametr zapytania wysokość Bing obsługuje współczynnik proporcji obrazu. Jeśli określisz wysokość i szerokość i nie zachować oryginalny współczynnik proporcji obrazu, Bing dodaje dopełnienie białe obramowanie obrazu. Na przykład jeżeli zmienisz rozmiar obrazu 480 x 359 do 200 x 200 bez przycinania, całą szerokość zawiera obraz, ale wysokość zawiera 25 pikseli biały dopełnienie u góry i u dołu obrazu. Taki sam będzie wartość true, jeśli obraz został 359 x 480, z wyjątkiem po lewej stronie i prawe krawędzie będzie zawierać białego dopełnienia. Jeśli przycinasz obraz dopełnienie biały nie został dodany.  

 
Na poniższej ilustracji przedstawiono oryginalnego rozmiaru obraz miniatury (480 x 300).  
  
![Oryginalny obraz pozioma](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
Na poniższej ilustracji przedstawiono obrazu, rozmiar 200 x 200. Obsługiwany współczynnik proporcji, obramowania górny i dolny są dopełniane przy użyciu biały (czarne obramowanie jest dołączony do wyświetlenia uzupełnienie).  
  
![Obraz o zmienionym rozmiarze pozioma](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  



Jeśli określisz wymiarów, które są większe niż oryginalna szerokość i wysokość obrazu, obraz, który jest dopełniana biały na lewej i górnej krawędzi.  
  
Aby przyciąć obraz, należy dołączyć c (przycinanie) parametr zapytania. Poniżej przedstawiono możliwe wartości, które można określić.  
  
- 4&mdash;daltonistom współczynnik  
- 7&mdash;inteligentny wskaźnik  
  
Jeśli żądanie jest współczynnik inteligentne przycinanie (c = 7), obraz zostanie przycięty z Centrum obrazu regionu zainteresowania na zewnątrz przy zachowaniu współczynnika proporcji obrazu. Obszar jest obszar obrazu, określająca Bing zawiera większość części importu. Na poniższym obrazie przedstawiono przykład regionu zainteresowania.  
  
![Regionu zainteresowania](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

Jeśli rozmiar obrazu i żądania, współczynnik inteligentne przycinanie obrazu zmniejszono żądanego rozmiaru przy zachowaniu współczynnika proporcji. Obraz, który następnie jest przycięty, w oparciu o zmienionym rozmiarze wymiarów. Na przykład jeśli szerokość o zmienionym rozmiarze jest mniejsza lub równa wysokość, obraz zostanie przycięty po lewej i prawej środka regionu zainteresowania. W przeciwnym razie, obraz zostanie przycięty do górnej i dolnej części środek regionu zainteresowania.  
  
 
Na poniższym obrazie przedstawiono obraz skrócony do 200 x 200 za pomocą przycięcie inteligentny wskaźnik.  
  
![Obraz pozioma przycięte do 200 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
Na poniższym obrazie przedstawiono obraz skrócony do 200 x 100, za pomocą przycięcie inteligentny wskaźnik.  
   
![Obraz pozioma przycięte do 200 x 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
Na poniższym obrazie przedstawiono obraz ograniczona do 100 x 200 za pomocą przycięcie inteligentny wskaźnik.  
  
![Obraz pozioma przycięte do 100 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)



Jeśli Bing nie może określić region obrazu interesujące, usługa Bing używa stosunku niewidomych przycinania.  
  
Jeśli żądanie jest przycinanie stosunku niewidomych (c = 4), usługa Bing używa następujących reguł, aby przyciąć obraz.  
  
- Jeśli (oryginalna szerokość obrazu / oryginalna wysokość obrazu) < (żądana szerokość obrazu / żądana wysokość obrazu), obraz, który jest mierzony w lewym górnym rogu przycięte u dołu.  
- Jeśli (oryginalna szerokość obrazu / oryginalna wysokość obrazu) > (żądana szerokość obrazu / żądana wysokość obrazu), mierzone w Centrum i przycięte do lewej i prawej stronie obrazu.  



Na poniższym obrazie przedstawiono obrazu pionowa, który jest 225 x 300.  
  
![Oryginalny obraz słonecznika](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
Na poniższym obrazie przedstawiono obraz skrócony do 200 x 200 za pomocą przycięcie stosunku niewidomych. Obraz jest mierzony w lewym górnym rogu, co w dolnej części obrazu jest obcinane.  
  
![Obraz słonecznika przycięte do 200 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
Na poniższym obrazie przedstawiono obraz skrócony do 200 x 100, za pomocą przycięcie stosunku niewidomych. Obraz jest mierzony w lewym górnym rogu, co w dolnej części obrazu jest obcinane.  
  
![Obraz słonecznika przycięte do 200 x 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
Na poniższym obrazie przedstawiono obraz ograniczona do 100 x 200 za pomocą przycięcie stosunku niewidomych. Obraz jest mierzony z Centrum skutkuje części lewego i prawego obrazu jest obcinane.  
  
![Obraz słonecznika przycięte do 100 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)

