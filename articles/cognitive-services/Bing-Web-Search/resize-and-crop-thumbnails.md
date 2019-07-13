---
title: Zmień rozmiar i przyciąć obraz miniatury - API wyszukiwania w Internecie Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zmienić rozmiar i przycinany, miniatury, dostarczone przez interfejsy API wyszukiwania Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 6a5b2dada254a0bfc7fa60172f56221ba67ad279
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868033"
---
# <a name="resize-and-crop-thumbnail-images"></a>Zmienianie rozmiaru i przycinanie obrazów miniatur

Niektóre odpowiedzi z interfejsów API wyszukiwania Bing uwzględniają adresy URL miniatur przez Bing, którego rozmiar można zmieniać i przycinanie i może zawierać parametrów zapytań. Przykład:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Jeśli możesz wyświetlić podzbiór tych miniatury, podaj opcję, aby wyświetlić pozostałe obrazy.

> [!NOTE]
> Pamiętaj, że scenariusz wyszukiwania, który uwzględnia prawa innych firm, zgodnie z wymaganiami interfejsu API wyszukiwania Bing zapewni przycinanie i zmienianie rozmiaru obrazów miniatur [użycia i wyświetlają wymagania dotyczące](use-display-requirements.md).

## <a name="resize-a-thumbnail"></a>Zmiana rozmiaru miniatur 

Aby zmienić rozmiar miniatury, Bing zaleca się określić tylko jeden `w` (szerokość) lub `h` (wysokość) parametry zapytania w adresie URL miniatury. Określanie tylko wysokość lub szerokość umożliwia Bing zachować proporcje oryginalnego obrazu. Określ szerokość i wysokość w pikselach. 

Na przykład, jeśli jest miniatura oryginalnego 480 x 620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

I chcesz zmniejszyć jego rozmiar, ustaw `w` parametru na nową wartość (na przykład `336`) i Usuń `h` parametru:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Jeśli określisz tylko wysokość i szerokość miniatury oryginalny współczynnik proporcji obrazu zostanie zachowana. Jeśli jest określenie obydwu parametrów i współczynnik proporcji nie jest obsługiwane, Bing doda dopełnienie białe obramowanie obrazu.

Na przykład jeżeli zmienisz rozmiar obrazu 480 x 359 do 200 x 200 bez przycinania, całą szerokość będzie zawierać obraz, ale wysokość będzie zawierać 25 pikseli biały dopełnienie u góry i u dołu obrazu. Jeśli obraz był 359 x 480, obramowania lewy i prawy zawierałoby dopełnienie białe. Jeśli przycinasz obraz dopełnienie biały nie został dodany.  

Na poniższej ilustracji przedstawiono oryginalnego rozmiaru obraz miniatury (480 x 300).  
  
![Oryginalny obraz pozioma](./media/resize-crop/bing-resize-crop-landscape.png)  
  
Na poniższej ilustracji przedstawiono obrazu, rozmiar 200 x 200. Obsługiwany współczynnik proporcji, obramowania górny i dolny są dopełniane przy użyciu biały (czarne obramowanie, w tym miejscu jest dołączony do wyświetlenia uzupełnienie).  
  
![Obraz o zmienionym rozmiarze pozioma](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Jeśli określisz wymiarów, które są większe niż szerokość i wysokość oryginalnego obrazu, Bing doda białe dopełnienie po lewej stronie i górnej krawędzi.  

## <a name="request-different-thumbnail-sizes"></a>Żądanie różne rozmiary miniatury

Aby poprosić o rozmiarze innego obraz miniatury, Usuń wszystkie parametry zapytania z adresu URL miniatury, z wyjątkiem `id` i `pid` parametrów. Następnie dodaj albo `&w` (szerokość) lub `&h` parametr zapytania (wysokość) o rozmiarze odpowiedni obraz w pikselach, ale nie oba. Bing zachowają oryginalny współczynnik proporcji obrazu. 

Aby zwiększyć szerokość obrazu określonego przez powyższy adres URL do 165 pikseli, należy użyć następującego adresu URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Jeśli żądania obrazu, który jest większy niż rozmiar oryginalnego obrazu Bing dodaje białe wypełnienia wokół obrazu zgodnie z potrzebami. Na przykład, jeśli oryginalny obraz, rozmiar to 474 x 316 i ustawisz `&w` na 500, Bing zwróci obraz 500 x 333. Ten obraz będzie mieć szerokość 8,5 piksele białe dopełnienie wzdłuż górnej i dolnej krawędzi i 13 piksele dopełnienie po lewej i prawej krawędzi.

Aby zapobiec sytuacji, w której Bing Dodawanie białego dopełnienie, jeśli żądany rozmiar jest większy niż rozmiar oryginalnego obrazu, należy ustawić `&p` zapytania parametr na wartość 0. Na przykład Jeśli dołączysz `&p=0` parametru w powyższy adres URL usługi Bing zwróci obrazem 474 x 316 zamiast obrazu 500 x 333:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Jeśli określisz zarówno `&w` i `&h` parametrów zapytania, Bing, zachowa współczynnik proporcji obrazu i dodaje dopełnienie białe, zgodnie z potrzebami. Na przykład jeśli oryginalny obraz, rozmiar to 474 x 316 i Ustaw szerokość i wysokość parametrów do 200 x 200 (`&w=200&h=200`), Wyszukiwarka Bing zwróci obrazu zawierającego 33 piksele białe dopełnienie u góry i u dołu. Jeśli dołączysz `&p` parametr zapytania Wyszukiwarka Bing zwraca 200 x 134 obrazu.

## <a name="crop-a-thumbnail"></a>Przytnij miniatury 

Aby przyciąć obraz, należy dołączyć `c` parametr zapytania (przycinanie). Można użyć następujących wartości:
  
- `4` &mdash; Stosunku niewidomych  
- `7` &mdash; Inteligentny wskaźnik  

### <a name="smart-ratio-cropping"></a>Inteligentne przycinanie współczynnik

Jeśli żądanie jest współczynnik inteligentne przycinanie (przez ustawienie `c` parametr `7`), Bing będzie przyciąć obraz ze środka w jego regionu zainteresowania na zewnątrz, przy zachowaniu współczynnika proporcji obrazu. Obszar jest obszar obrazu, określająca Bing zawiera większość części importu. Na poniższym obrazie przedstawiono przykład regionu zainteresowania.  
  
![Regionu zainteresowania](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Jeśli rozmiar obrazu i żądania, współczynnik inteligentne przycinanie Bing zmniejsza obraz do żądanego rozmiaru przy zachowaniu współczynnika proporcji. Bing następnie Przycina obraz, w oparciu o zmienionym rozmiarze wymiarów. Na przykład jeśli szerokość o zmienionym rozmiarze jest mniejsza lub równa wysokość, Bing przyciąć obraz w lewo i prawo do środka regionu zainteresowania. W przeciwnym razie Bing będzie zbioru go do górnej i dolnej części środek regionu zainteresowania.  
  
 
Na poniższym obrazie przedstawiono obraz skrócony do 200 x 200 za pomocą przycięcie inteligentny wskaźnik. Ponieważ Bing mierzy obrazu w lewym górnym rogu, w dolnej części obrazu jest obcinane. 
  
![Obraz pozioma przycięte do 200 x 200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
Na poniższym obrazie przedstawiono obraz skrócony do 200 x 100, za pomocą przycięcie inteligentny wskaźnik. Ponieważ Bing mierzy obrazu w lewym górnym rogu, w dolnej części obrazu jest obcinane. 
   
![Obraz pozioma przycięte do 200 x 100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
Na poniższym obrazie przedstawiono obraz ograniczona do 100 x 200 za pomocą przycięcie inteligentny wskaźnik. Ponieważ Bing mierzy obrazu z witryny Centrum, części lewego i prawego obrazu są obcinane.
  
![Obraz pozioma przycięte do 100 x 200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Jeśli Bing nie może określić region obrazu interesujące, będzie używane przez usługę, stosunku niewidomych przycinania.  

### <a name="blind-ratio-cropping"></a>Przycinanie stosunku niewidomych

Jeśli żądanie jest przycinanie stosunku niewidomych (przez ustawienie `c` parametr `4`), usługa Bing używa następujących reguł, aby przyciąć obraz.  
  
- Jeśli `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`, obraz jest mierzony od lewego górnego rogu oraz przycięte u dołu.  
- Jeśli `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`, mierzone w Centrum i przycięte do lewej i prawej stronie obrazu.  

Na poniższym obrazie przedstawiono obrazu pionowa, który jest 225 x 300.  
  
![Oryginalny obraz słonecznika](./media/resize-crop/bing-resize-crop-sunflower.png)
  
Na poniższym obrazie przedstawiono obraz skrócony do 200 x 200 za pomocą przycięcie stosunku niewidomych. Obraz jest mierzony od lewego górnego rogu, co w dolnej części obrazu jest obcinane.  
  
![Obraz słonecznika przycięte do 200 x 200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
Na poniższym obrazie przedstawiono obraz skrócony do 200 x 100, za pomocą przycięcie stosunku niewidomych. Obraz jest mierzony od lewego górnego rogu, co w dolnej części obrazu jest obcinane.  
  
![Obraz słonecznika przycięte do 200 x 100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
Na poniższym obrazie przedstawiono obraz ograniczona do 100 x 200 za pomocą przycięcie stosunku niewidomych. Obraz jest mierzony z Centrum skutkuje części lewego i prawego obrazu jest obcinane.  
  
![Obraz słonecznika przycięte do 100 x 200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Kolejne kroki

* [Co to są interfejsy API wyszukiwania Bing?](bing-api-comparison.md)
* [Użyj interfejsu API wyszukiwania Bing i wyświetlają wymagania dotyczące](use-display-requirements.md)
