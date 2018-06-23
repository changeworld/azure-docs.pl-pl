Niektóre odpowiedzi Bing uwzględnić adresów URL do obrazów miniatur obsługiwane przez usługi Bing. Możesz zmieniać rozmiar i przycinanie obrazów miniatur. 

> [!NOTE]
> Sprawdź, czy rozmiar i przycinanie miniatury udostępniają scenariusza wyszukiwania oraz przestrzegania prawa innych firm, co jest wymagane przez użycie interfejsu API wyszukiwania usługi Bing i wyświetlić wymagania.


Aby zmienić rozmiar obrazu, obejmują Sz (szerokość) i (wysokość) parametry w adresie URL miniatury zapytania. Określ szerokość i wysokość w pikselach. Na przykład:  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
Jeśli zmiana rozmiaru obrazu, jego współczynnik proporcji jest obsługiwane. Aby zachować współczynnik proporcji, Wypełnienie białe może można dodać do obramowania obrazu. Na przykład jeśli zmieniasz obraz 480 x 359 do 200 x 200 bez przycinania, pełnej szerokości zawiera obraz, ale wysokość zawiera 25 pikseli biały dopełnienie u góry i u dołu obrazu. Taki sam będzie mieć wartość true, jeśli obraz został 359 x 480, z wyjątkiem po lewej stronie i prawe krawędzie zawiera białe dopełnienia. Jeśli Przytnij obraz, Wypełnienie białe nie została dodana.  

 
Na poniższej ilustracji przedstawiono oryginalnego rozmiaru obrazów miniatur (480 x 300).  
  
![Oryginalny obraz orientacji poziomej](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
Na poniższej ilustracji przedstawiono zmiany rozmiaru 200 x 200 obrazu. Współczynnik proporcji jest obsługiwana i obramowań górny i dolny są dopełniane przy białe (czarnym obramowaniem jest dołączony do wyświetlenia uzupełnienie).  
  
![Obraz po zmianie rozmiaru orientacji poziomej](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  



Jeśli określisz wymiarów, które są większe niż oryginalna szerokość i wysokość obrazu, obraz jest wypełniane biały na lewej i górnej krawędzi.  
  
Aby przyciąć obraz, obejmują c (przycięcia) parametr zapytania. Poniżej przedstawiono możliwe wartości, które można określić.  
  
- 4&mdash;niewidome współczynnik  
- 7&mdash;inteligentne współczynnik  
  
Jeśli żądanie jest wskaźnik inteligentny przycinanie (c = 7), obraz jest przycięty od środka obrazu obszaru zainteresowania na zewnątrz przy zachowaniu współczynnika proporcji obrazu. Obszaru zainteresowania jest obszar obrazu, który określa Bing zawiera większość elementów importu. Poniżej przedstawiono przykład region odsetek.  
  
![Region zainteresowań](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

Jeśli rozmiar obrazu i żądania wskaźnik inteligentny przycinanie obrazu zostanie zmniejszona żądanego rozmiaru przy zachowaniu współczynnika proporcji. Obraz jest następnie przycięty, na podstawie którego rozmiar jest zmieniany wymiarów. Na przykład jeśli po zmianie rozmiaru szerokość jest większa niż wysokość, obraz jest przycięty po lewej i prawej centrum obszaru zainteresowania. W przeciwnym razie obraz jest przycięty do góry i u dołu centrum obszaru zainteresowania.  
  
 
Poniżej przedstawiono zmniejszona do 200 x 200 przy użyciu wskaźnik inteligentny przycinanie obrazu.  
  
![Przycięty 200 x 200 obrazu orientacji poziomej](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
Poniżej przedstawiono zmniejszona do 200 x 100 przy użyciu wskaźnik inteligentny przycinanie obrazu.  
   
![Obraz pozioma przycięte do 200 x 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
Poniżej przedstawiono zmniejszona do 100 x 200 przy użyciu wskaźnik inteligentny przycinanie obrazu.  
  
![Przycięty 100 x 200 obrazu orientacji poziomej](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)



Jeśli Bing nie może określić region obrazu zainteresowań, Bing używa przycinanie stosunek ukryta.  
  
Jeśli żądanie jest ukryta stosunek przycinanie (c = 4), aby przyciąć obraz Bing stosowane są następujące reguły.  
  
- Jeśli (oryginalną szerokość obrazu / oryginalną wysokość obrazu) < (żądanie szerokość obrazu / zażądał wysokość obrazu), obraz jest mierzony z lewym górnym rogu przycięte u dołu.  
- Jeśli (oryginalny szerokość obrazu / oryginalną wysokość obrazu) > (żądanie szerokość obrazu / zażądał wysokość obrazu), obraz jest mierzony z Centrum i przycięte w lewo i prawo.  



Poniżej przedstawiono obrazu pionowy, który jest 225 x 300.  
  
![Oryginalny obraz słonecznika](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
Poniżej przedstawiono obrazu zmniejszona do 200 x 200 przy użyciu przycinanie stosunek ukryta. Obraz jest mierzony z lewym górnym rogu, co w dolnej części obrazu jest obcinane.  
  
![Obraz słonecznika przycięte 200 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
Poniżej przedstawiono obrazu zmniejszona do 200 x 100 przy użyciu przycinanie stosunek ukryta. Obraz jest mierzony z lewym górnym rogu, co w dolnej części obrazu jest obcinane.  
  
![Obraz słonecznika przycięte do 200 x 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
Poniżej przedstawiono obrazu zmniejszona do 100 x 200 przy użyciu przycinanie stosunek ukryta. Obraz jest mierzony z Centrum powodujące lewy i prawy części obrazu jest obcinane.  
  
![Obraz słonecznika przycięte 100 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)

