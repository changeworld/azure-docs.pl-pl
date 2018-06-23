---
title: Komputer wizję interfejsu API kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
description: Użyj zaawansowane algorytmy w interfejsie API przetwarzania obrazów komputera, aby przetworzyć obrazach i zwracają informacje w kognitywnych usług firmy Microsoft.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/10/2017
ms.author: kefre
ms.openlocfilehash: 86e0441c600162e479c678d3cb1dbeaad423ddb5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349113"
---
# <a name="what-is-computer-vision-api-version-10"></a>Co to jest komputer wizji interfejsu API w wersji 1.0?

> [!IMPORTANT]
> Nowa wersja interfejsu API wizji komputer jest teraz dostępna, zobacz:
>- [Omówienie](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [Komputer wizji interfejsu API w wersji 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

Interfejsu API oparte na chmurze wizji komputera oferuje deweloperom dostęp do zaawansowanych algorytmów przetwarzania obrazów i zwracanie informacji. Przekazywanie obrazu lub określając adres URL obrazu, algorytmy wizji komputera Microsoft można analizować zawartości wizualnej na różne sposoby, na podstawie danych wejściowych i opcji użytkownika. Przy użyciu interfejsu API przetwarzania obrazów komputera użytkowników można analizować obrazów do:
* [Tag obrazów na podstawie zawartości.](#Tagging)
* [Klasyfikowanie obrazów.](#Categorizing)
* [Określ typ i jakość obrazów.](#Identifying)
* [Wykrywanie kroje człowieka i zwracać współrzędnych. ](#Faces)
* [Rozpoznaje zawartości specyficznego dla domeny.](#Domain-Specific)
* [Generowanie opisy zawartości.](#Descriptions)
* [OCR umożliwia zidentyfikowanie wydruku można odnaleźć w obrazach tekstu.](#OCR)
* [Rozpoznaje pisma ręcznego.](#RecognizeText)
* [Odróżnić schematy kolorów.](#Color)
* [Flaga zawartość dla dorosłych.](#Adult)
* [Przytnij zdjęć do użycia jako miniatury.](#Thumbnails)

## <a name="requirements"></a>Wymagania
* Obsługiwane metody wejściowe: binarne w formie application/octet strumienia lub adres URL obrazu Raw.
* Obsługiwane formaty obrazów: JPEG, PNG, GIF, BMP.
* Rozmiar pliku obrazu: mniej niż 4 MB.
* Wymiar obrazu: większa niż 50 x 50 pikseli.

## <a name="tagging-images"></a>Znakowanie obrazów
Interfejs API przetwarzania obrazów komputera zwraca tagi na podstawie więcej niż 2000 rozpoznawalnych obiektów, istot życia, dekoracje i akcji. Gdy tagi są niejednoznaczne lub nie wspólnej wiedzy, odpowiedzi interfejsu API zawiera wskazówki, wyjaśnienie tagu w kontekście ustawienie znane. Tagi nie są organizowane jako taksonomię i Brak hierarchii dziedziczenia istnieje. Kolekcja tagi zawartości stanowi podstawę dla obrazu "opis", wyświetlane jako człowieka język do odczytu w pełnych zdań. Należy pamiętać, że w tym momencie język angielski jest jedynym obsługiwanym językiem opis obrazu.

Po przekazywanie obrazu lub określając adres URL obrazu, API wizji komputer, algorytmów output tagi na podstawie obiektów, istot życia i działań określonych w obrazie. Znakowanie nie jest ograniczona do głównego podmiot, takie jak osoby na pierwszym planie nie zawiera też mebli (wewnątrz lub na zewnątrz), ustawienia, narzędzia, roślin, zwierząt, Akcesoria, gadżetów itp.

### <a name="example"></a>Przykład
![House_Yard](./Images/house_yard.jpg) '

```json
Returned Json
{
   'tags':[
      {
         "name":"grass",
         "confidence":0.999999761581421
      },
      {
         "name":"outdoor",
         "confidence":0.999970674514771
      },
      {
         "name":"sky",
         "confidence":999289751052856
      },
      {
         "name":"building",
         "confidence":0.996463239192963
      },
      {
         "name":"house",
         "confidence":0.992798030376434
      },
      {
         "name":"lawn",
         "confidence":0.822680294513702
      },
      {
         "name":"green",
         "confidence":0.641222536563873
      },
      {
         "name":"residential",
         "confidence":0.314032256603241
      },
   ],
}
```
## <a name="categorizing-images"></a>Obrazy kategoryzacji
Oprócz znakowanie i opisy komputera wizji API zwraca kategorie na podstawie taksonomii zdefiniowane w poprzednich wersjach. Te kategorie są zorganizowane jako taksonomii z hierarchiami dziedzicznym nadrzędny/podrzędny. Wszystkie kategorie są w języku angielskim. Mogą one używane autonomicznie lub z naszych nowe modele.

### <a name="the-86-category-concept"></a>Pojęcie 86 kategorii
Na podstawie listy 86 koncepcji pokazano na poniższym diagramie, visual funkcje dostępne w obrazie mogą zostać podzielone od szerokiej do określonych. Pełna taksonomii w formacie tekstowym, zobacz [taksonomii kategorii](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).

![Analizowanie kategorii](./Images/analyze_categories.jpg)

Image (Obraz)                                                  | Odpowiedź
------------------------------------------------------ | ----------------
![Kobieta dachu](./Images/woman_roof.jpg)                 | osoby
![Zdjęcie rodziny](./Images/family_photo.jpg)             | people_crowd
![Pies cute](./Images/cute_dog.jpg)                     | animal_dog
![Na zewnątrz górski](./Images/mountain_vista.jpg)       | outdoor_mountain
![Wizja analizowanie chleb żywności](./Images/bread.jpg)       | food_bread

## <a name="identifying-image-types"></a>Identyfikowanie typy obrazów
Istnieje kilka sposobów kategoryzację obrazów. Interfejs API przetwarzania obrazów komputera można ustawić Flaga wartości logicznej wskazująca, czy obraz jest białe czarny lub kolor. Można także ustawić flagę wskazującą, czy obraz jest rysowanie linii. Można również określić, czy obraz jest grafikę klip lub nie i wskazywać jego jakości jako takie w skali od 0 do 3.

### <a name="clip-art-type"></a>Typ pozyskiwać
Wykrywa, czy obraz jest grafikę przycinania.  

Wartość | Znaczenie
----- | --------------
0     | Non pozyskiwać
1     | niejednoznaczne
2     | Normalny pozyskiwać
3     | dobra pozyskiwać

Image (Obraz)|Odpowiedź
----|----
![Wizja analizowanie ser obiektów graficznych](./Images/cheese_clipart.jpg)|3 dobrej pozyskiwać
![Wizja analizowanie metr DOM](./Images/house_yard.jpg)|0 Non pozyskiwać

### <a name="line-drawing-type"></a>Typ rysunku
Wykrywa, czy obraz jest rysowanie linii.

Image (Obraz)|Odpowiedź
----|----
![Wizja analizowanie Lion rysowania](./Images/lion_drawing.jpg)|True
![Wizja analizowanie kwiat](./Images/flower.jpg)|False

### <a name="faces"></a>Twarze
Wykrywa człowieka kroje w obrębie obrazu i generuje współrzędne krój, prostokąt dla krój, płeć i wiek. Te funkcje visual są podzbiorem metadanych wygenerowany dla powierzchni. Dla szerszej metadanych wygenerowany dla powierzchni (twarzy identyfikacji, wykrywania ułożenia i inne) za pomocą interfejsu API twarzy na obrazie.  

Image (Obraz)|Odpowiedź
----|----
![Wizja analizowania powierzchni dachu kobieta](./Images/woman_roof_face.png) | [{"wiek": 23, "płci": "Żeńskiego", "faceRectangle": {"left": 1379 "top": 320, "szerokość": 310, "wysokość": 310}}]
![Wizja analizowania powierzchni rozszerzenia programu Microsoft Operations Manager](./Images/mom_daughter_face.png) | [{"wiek": 28 "płci": "Żeńskiego", "faceRectangle": {"left": 447 "top": 195, "szerokość": 162, "wysokość": 162}}, {"wiek": 10, "płci": "Męskiego", "faceRectangle": {"left": 355 "top": 87, "szerokość": 143, "wysokość": 143}}]
![Wizja analizowania powierzchni Phot rodziny](./Images/family_photo_face.png) | [{"wiek": 11, "płci": "Męskiego", "faceRectangle": {"left": 113 "top": 314, "szerokość": 222, "wysokość": 222}}, {"wiek": 11, "płci": "Żeńskiego", "faceRectangle": {"left": 1200 "top": 632, "szerokość": 215, "wysokość": 215}}, {"wiek": 41, "płci": "Męskiego", " faceRectangle": {"left": 514"top": 223,"szerokość": 205,"wysokość": 205}}, {"wiek": 37,"płci":"Żeńskiego","faceRectangle": {"left": 1008"top": 277,"szerokość": 201,"wysokość": 201}}]


## <a name="domain-specific-content"></a>Zawartość specyficznego dla domeny

Dodatkowo kategoryzacji do znakowania i najwyższego poziomu, interfejsu API wizji komputer obsługuje również informacje dotyczące specjalnych (lub specyficznego dla domeny). Informacje można zaimplementować jako metoda autonomiczna lub kategoryzacji wysokiego poziomu. Działa ona jako środek do uściślenia taksonomii kategorii 86 przez dodanie modeli specyficznego dla domeny.

Obecnie tylko specjalistyczne informacje obsługiwane są rozpoznawania renomy i dzielnicę rozpoznawania. Są one uściślenia specyficznego dla domeny dla osoby, osób grupy kategorii i punkty orientacyjne na całym świecie.

Istnieją dwie opcje przy użyciu modeli specyficznego dla domeny:

### <a name="option-one---scoped-analysis"></a>Opcja jednej - zakresie analizy
Przeanalizuj tylko wybrany model, przez wywołanie metody POST protokołu HTTP. Dla tej opcji Jeśli znasz model, który ma być używany, należy określić nazwę modelu i możesz pobrać wyłącznie informacje dotyczące tego modelu. Na przykład można tej opcji tylko Wyszukaj renomy rozpoznawania. Odpowiedź zawiera listę dopasowania znanych osób, wraz z ich wyniki ufności potencjał.

### <a name="option-two---enhanced-analysis"></a>Opcja 2 - rozszerzonej analizy
Przeanalizuj dodatkowych informacji związanych z programem do kategorii z taksonomii 86 kategorii. Ta opcja jest dostępna do użycia w aplikacjach, gdzie użytkownicy chcą uzyskać ogólny obraz analizy oprócz szczegółów z jednego lub więcej modeli specyficznego dla domeny. Gdy ta metoda jest wywoływana, najpierw jest wywoływana klasyfikatora taksonomii 86 kategorii. Czy zgodną jakiejkolwiek kategorii znane/pasujące modeli, jest zgodny z drugim przebiegu wywołań klasyfikatora. Na przykład jeśli "Szczegóły = all" lub "szczegóły" obejmują "znanych osób", metoda wywołuje klasyfikatora renomy po klasyfikatora 86 kategorii. Wynik zawiera tagi, począwszy od "people_".

## <a name="generating-descriptions"></a>Generowanie opisów 
Algorytmy komputera wizji API analizowanie zawartości obrazu. Analiza stanowi podstawę dla "opis" wyświetlany jako język zrozumiałą dla użytkownika w pełnych zdań. Opis zawiera podsumowanie, co znaleziono w obrazie. Algorytmy komputera wizji API generują opisy różnych oparte na obiektach określone w obrazie. Opisy są oceniane w każdej i generowane wynik zaufania. Lista jest następnie zwracany uporządkowanej z najwyższym wynik zaufania malejąco. Przykład robotów używaną do generowania podpisów obrazu tej technologii znajduje się [tutaj](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).  

### <a name="example-description-generation"></a>Przykład opisu generacji
![Poniż & budynków W](./Images/bw_buildings.jpg) '
```json
 Returned Json

'description':{
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a large city',
         'confidence':0.607638706850331
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a photo of a large city',
         'confidence':0.577256764264197
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a city',
         'confidence':0.538493271791207
      }
   ]   
   'description':[
      "tags":{
         "outdoor",
         "city",
         "building",
         "photo",
         "large",
      }
   ]
}
```

## <a name="perceiving-color-schemes"></a>Odczuwania schematy kolorów
Algorytm wizji komputera wyodrębnia kolory z obrazu. Kolory są analizowane w trzech różnych kontekstach: na pierwszym planie, w tle i w całości obrazu. Są one zgrupowane w kolory akcentu dominującą dwanaście 12. Kolorów te akcent są czarny, niebieski, brązowy szary, zielony, orange, różowy, purpurowy, red, jest, biały i żółty. W zależności od kolorów obrazu prostego czarne biały lub kolory akcentu może być zwrócona w kody szesnastkowe kolorów.

Image (Obraz)                                                       | Pierwszego planu |Tło| Kolory
----------------------------------------------------------- | --------- | ------- | ------
![Na zewnątrz górski](./Images/mountain_vista.jpg)            | Czarny     | Czarny   | Biały
![Wizja analizowanie kwiat](./Images/flower.jpg)               | Czarny     | Biały   | Białe, czarne, zielony
![Wizja analizowanie Train stacji](./Images/train_station.jpg) | Czarny     | Czarny   | Czarny

### <a name="accent-color"></a>Kolor akcentu
Kolor wyodrębniony z obrazu do reprezentowania najbardziej atrakcyjnych kolorów do użytkowników za pośrednictwem różnych dominującą kolorów i nasycenie.

Image (Obraz)                                                       | Odpowiedź
----------------------------------------------------------- | ----
![Na zewnątrz górski](./Images/mountain_vista.jpg)            | #BC6F0F
![Wizja analizowanie kwiat](./Images/flower.jpg)               | #CAA501
![Wizja analizowanie Train stacji](./Images/train_station.jpg) | #484B83


### <a name="black--white"></a>Czerni
Flaga wartości logicznej, która wskazuje, czy obraz jest czarny & białe lub nie.

Image (Obraz)                                                      | Odpowiedź
---------------------------------------------------------- | ----
![Wizja analizowanie kompilowanie](./Images/bw_buildings.jpg)      | True
![Wizja analizowanie metr DOM](./Images/house_yard.jpg)      | False

## <a name="flagging-adult-content"></a>Flaguje zawartość dla dorosłych
Z różnych kategorii visual to grupa dla dorosłych i luksusowych, które włącza wykrywanie materiałów dla dorosłych i ograniczają możliwość wyświetlania obrazów seksualnego zawartością. Filtr do wykrywania zawartości dla dorosłych i luksusowych można ustawić na ruchomej skali do uwzględnienia preferencji użytkownika.

## <a name="optical-character-recognition-ocr"></a>OCR (Rozpoznawania)
Technologia Rozpoznawania wykrywa zawartości tekstowej w obrazie i wyodrębnia tekst zidentyfikowanych w strumieniu czytelną znaków. Wynik służącego do wyszukiwania i wiele innych celów, takie jak rekordy medyczne, zabezpieczeń i banku. Funkcja automatycznie wykrywa język. Rozpoznawania zaoszczędzić czas i zapewnia wygodę użytkowników mogą robić zdjęcia tekst, a przetwarzanie tekstu.

Rozpoznawania obsługuje języki 25. Te języki są: arabski, chiński uproszczony, chiński tradycyjny, czeski, duński, holenderski, angielski, fiński, francuski, niemiecki, grecki, węgierski, włoski, japoński, koreański, norweski, Polski, portugalski, rumuński, rosyjski, serbski (łaciński i cyrylica), Słowacki, hiszpański, szwedzki i turecki.

W razie potrzeby Rozpoznawania poprawia obrót rozpoznany w stopniach wokół osi poziomej obrazu. Rozpoznawania zawiera współrzędne ramki każdego wyrazu, jak pokazano poniżej ilustracji.

![Przegląd Rozpoznawania](./Images/vision-overview-ocr.png) wymagania dotyczące Rozpoznawania:
- Rozmiar obrazu wejściowego musi należeć do zakresu od 40 x 40 i 3200 x 3200 pikseli.
- Obraz nie może być większa niż 10 milionów pikseli.

Obraz wprowadzania można obracać o wszelkich wielokrotnością 90 stopni oraz małych kąt z maksymalnie 40 stopni.

Dokładność rozpoznawania tekstu zależy od jakości obrazu. Niedokładne odczytu może być spowodowane w następujących sytuacjach:
- Obrazy rozmyty.
- Ręczne lub tworzone jako tekst.
- Style czcionki artystycznego.
- Rozmiar małego tekstu.
- Tło złożonych, cieni lub odbić za pośrednictwem zakłócenia tekst lub perspektywy.
- Zbyt duży lub brak wielkich liter na początku słowa
- Indeks dolny, indeks górny lub przekreślenia tekstu.

Ograniczenia: Na fotografii, gdzie jest tekst, fałszywych alarmów mogą pochodzić z częściowo rozpoznanym słów. Do niektórych fotografii, szczególnie zdjęć bez dowolny tekst dokładności można znacznie zależy od typu obrazu.

## <a name="recognize-handwritten-text"></a>Rozpoznawania pisma ręcznego tekstu
Ta technologia umożliwia wykrywanie i wyodrębniania pismo uwagi, liter, essays, tablic, formularzy itd. Działa ona na różnych powierzchniach i tłach, takich jak biały papier, żółte karteczki samoprzylepne i tablice.

Rozpoznawanie tekstu odręcznego oszczędza czas i pracę, a także może zwiększyć produktywność dzięki opcji przedstawiania tekstu w postaci obrazu zamiast jego transkrypcji. Powoduje możliwość digitize uwagi. Ta digitization umożliwia Implementowanie wyszukiwania szybkie i łatwe. Zmniejsza ona również nieład związany z dużą ilością papieru.

Wymagania wprowadzania:
- Obsługiwane formaty obrazów: BMP, JPEG i PNG.
- Rozmiar pliku obrazu musi być mniejszy niż 4 MB.
- Wymiary obrazu musi być co najmniej 40 x 40, co najwyżej 3200 x 3200.

Uwaga: ta technologia jest obecnie dostępna w wersji zapoznawczej i tylko w przypadku tekstu w języku angielskim.

## <a name="generating-thumbnails"></a>Trwa generowanie miniatur
Miniatury jest mała reprezentacja obrazu w pełnym rozmiarze. Zróżnicowane urządzeń, takich jak telefony, tablety i komputery utworzyć potrzebę miniatur rozmiary i układy środowisko (UX) inny użytkownik. Przy użyciu przycinanie inteligentne, ta funkcja API przetwarzania obrazów komputera pomaga w rozwiązaniu problemu.

Po przekazaniu obrazu, pobiera wygenerowany miniatur wysokiej jakości i algorytm API przetwarzania obrazów komputera analizuje obiektów w obrazie. Następnie Przycina obraz do wymagań wartość obszaru zainteresowania (ROI). Dane wyjściowe pobiera wyświetlane w ramach specjalnych, jak pokazano poniżej ilustracji. Wygenerowany miniatur można użyć dowolnego racji aspekt innego niż współczynnik proporcji oryginalnego obrazu w celu uwzględnienia potrzeb użytkownika.

Algorytm miniatur działa w następujący sposób:

1. Usuwa zbędne elementy z obrazu i rozpoznaje obiektu głównego "region odsetek" (ROI).
2. Przycina obraz na podstawie zidentyfikowanych regionu zainteresowań.
3. Zmienia współczynnik proporcji do rozmiaru miniatur docelowej.

![Miniatury](./Images/thumbnail-demo.png)
