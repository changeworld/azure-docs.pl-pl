---
title: Interfejs API wizualizacji komputerowej dla usług Microsoft Cognitive Services | Dokumentacja firmy Microsoft
description: Użyj zaawansowanych algorytmów w interfejs API przetwarzania obrazów, ułatwiające przetwarzania obrazów i zwracania informacji w usługach Microsoft Cognitive Services.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/10/2017
ms.author: kefre
ms.openlocfilehash: 84d931ad79bf32b39a4d771f6afd1c9a05ad2395
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714823"
---
# <a name="what-is-computer-vision-api-version-10"></a>Co to jest komputer Vision API w wersji 1.0?

> [!IMPORTANT]
> Udostępniono nową wersję interfejsu API przetwarzania obrazów, zobacz:
>- [Omówienie](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [Wersja interfejsu API przetwarzania komputera w wersji 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

Oparty na chmurze interfejs API przetwarzania obrazów oferuje deweloperom dostęp do zaawansowanych algorytmów przetwarzania obrazów i zwracania informacji. Po przekazaniu obrazu lub podaniu jego adresu URL algorytmy funkcji przetwarzania obrazu firmy Microsoft mogą dokonać analizy zawartości wizualnej na różne sposoby w zależności od danych wejściowych i wybranych opcji użytkownika. Za pomocą interfejsu API przetwarzania obrazów użytkowników można analizować obrazów w celu:
* [Tagować obrazy na podstawie zawartości.](#Tagging)
* [Kategoryzowanie obrazów.](#Categorizing)
* [Określ typ i jakość obrazów.](#Identifying)
* [Wykrywaj twarze i zwraca ich współrzędnych. ](#Faces)
* [Rozpoznawać zawartość specyficznego dla domeny.](#Domain-Specific)
* [Generowanie opisy zawartości.](#Descriptions)
* [Optyczne rozpoznawanie znaków umożliwia zidentyfikowanie drukowanego tekstu w obrazach.](#OCR)
* [Rozpoznawanie tekstu odręcznego.](#RecognizeText)
* [Rozróżnia schematów kolorów.](#Color)
* [Flaga zawartości dla dorosłych.](#Adult)
* [Przycinanie zdjęcia do użycia jako miniatury.](#Thumbnails)

## <a name="requirements"></a>Wymagania
* Obsługiwane metody wejściowe: binarne w formie application/octet strumienia lub adres URL obrazu Raw.
* Obsługiwane formaty obrazów: JPEG, PNG, GIF, BMP.
* Rozmiar pliku obrazu: mniej niż 4 MB.
* Wymiar obrazu: większa niż 50 x 50 pikseli.

## <a name="tagging-images"></a>Tagowanie obrazów
Interfejs API przetwarzania obrazów zwraca tagi oparte na ponad 2000 rozpoznawalnych obiektów, istot żywych, scenerii i akcji. Gdy tagi są niejednoznaczne lub nie popularną wiedzę, odpowiedź interfejsu API zapewnia wskazówek, wyjaśnienie tagu w kontekście znanych ustawienie. Tagi nie są zorganizowane w postaci taksonomię i Brak hierarchii dziedziczenia istnieje. Kolekcja znaczników zawartości stanowi podstawę dla obrazu "opis" jest wyświetlany jako ludzi język do odczytu w postaci kompletnych zdań. Należy pamiętać, że w tym momencie język angielski jest jedynym obsługiwanym językiem opis obrazu.

Po przekazaniu obrazu lub określając adres URL obrazu, algorytmy interfejs API przetwarzania obrazów w danych wyjściowych tagi na podstawie obiektów, istot żywych i działań określonych na obrazie. Oznakowanie nie jest ograniczona do głównego tematów, takich jak osoby na pierwszym planie, ale obejmuje również ustawienie meble (wewnątrz lub na zewnątrz), narzędzia, zakładów, zwierząt, Akcesoria, gadżety itp.

### <a name="example"></a>Przykład
![House_Yard](./Images/house_yard.png) '

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
## <a name="categorizing-images"></a>Kategoryzowanie obrazów
Oprócz znakowanie i opisy interfejs API przetwarzania obrazów zwraca kategorie na podstawie taksonomii zdefiniowane w poprzednich wersjach. Te kategorie są uporządkowane jako taksonomii z dziedzicznym hierarchie nadrzędny/podrzędny. Wszystkie kategorie są w języku angielskim. Mogą one używane autonomicznie lub z naszych nowych modeli.

### <a name="the-86-category-concept"></a>Koncepcja 86 kategorii
Na podstawie listy koncepcji 86 pokazano na poniższym diagramie, visual funkcji, które znajdują się w obrazie, mogą zostać podzielone od szerokiej określone. Dla pełnej taksonomię w formacie tekstowym, zobacz [Taksonomia kategorii](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).

![Analizowanie kategorii](./Images/analyze_categories.png)

Image (Obraz)                                                  | Odpowiedź
------------------------------------------------------ | ----------------
![Kobieta dachu](./Images/woman_roof.png)                 | osoby
![Rodzina zdjęć](./Images/family_photo.png)             | people_crowd
![Urocze pies](./Images/cute_dog.png)                     | animal_dog
![Aktywność na świeżym powietrzu górski](./Images/mountain_vista.png)       | outdoor_mountain
![Wizja analizowanie chleb żywności](./Images/bread.png)       | food_bread

## <a name="identifying-image-types"></a>Identyfikowanie typów obrazów
Istnieje kilka sposobów kategoryzowanie obrazów. Interfejs API przetwarzania obrazów może ustawić flagę logiczną, aby wskazać, czy obraz jest czarno biały lub kolorowy. Można również ustawić flagę wskazującą, czy obraz jest obiektem. Można również określić, czy obraz jest clipart, czy nie, a następnie wskazują jego jakości w związku z tym w skali 0 – 3.

### <a name="clip-art-type"></a>Typ obiektu clipart
Wykrywa, czy obraz jest clipart.  

Wartość | Znaczenie
----- | --------------
0     | Inne niż clipart
1     | niejednoznaczne
2     | Normalny clipart
3     | dobre clipart

Image (Obraz)|Odpowiedź
----|----
![Wizja analizowanie ser clipart](./Images/cheese_clipart.png)|3-dobrej clipart
![Wizja analizowanie Yard DOM](./Images/house_yard.png)|0-Non clipart

### <a name="line-drawing-type"></a>Typ rysunku
Wykrywa, czy obraz jest obiektem.

Image (Obraz)|Odpowiedź
----|----
![Wizja analizowanie Lion rysowania](./Images/lion_drawing.png)|True
![Wizja analizowanie Kwiatek](./Images/flower.png)|False

### <a name="faces"></a>Twarze
Wykrywa twarze osób na zdjęciu i generuje współrzędne rozpoznawania twarzy, prostokąt twarzy, płeć i wieku. Te funkcje visual stanowią podzestaw metadanych wygenerowanych twarzy. Dla bardziej rozległe metadanych wygenerowany dla powierzchni (Identyfikacja twarzy, wykrywanie ułożenia i inne) należy użyć interfejsu API rozpoznawania twarzy.  

Image (Obraz)|Odpowiedź
----|----
![Wizja analizowanie kobieta dachu twarzy](./Images/woman_roof_face.png) | [{"wiek": 23, "płeć": "Żeńskiego", "faceRectangle": {"left": 1379 "Najważniejsze": 320, "szerokość": 310, "height": 310}}]
![Wizja analizowania powierzchni rozszerzenia programu Microsoft Operations Manager](./Images/mom_daughter_face.png) | [{"wiek": 28, "płeć": "Żeńskiego", "faceRectangle": {"left": 447 "Najważniejsze": 195, "szerokość": 162, "height": 162}}, {"wiek": 10, "płeć": "Mężczyzna", "faceRectangle": {"left": 355 "Najważniejsze": 87, "szerokość": 143, "height": 143}}]
![Wizja analizowanie rodziny Phot twarzy](./Images/family_photo_face.png) | [{"wiek": 11, "płeć": "Mężczyzna", "faceRectangle": {"left": 113 "Najważniejsze": 314, "szerokość": 222, "height": 222}}, {"wiek": 11, "płeć": "Żeńskiego", "faceRectangle": {"left": 1200 "Najważniejsze": 632, "szerokość": 215, "height": 215}}, {"wiek": 41, "płeć": "Mężczyzna", " faceRectangle": {"left": 514"Najważniejsze": 223,"szerokość": 205,"height": 205}}, {"wiek": 37,"płeć":"Żeńskiego","faceRectangle": {"left": 1008"Najważniejsze": 277,"szerokość": 201,"height": 201}}]


## <a name="domain-specific-content"></a>Zawartość specyficznego dla domeny

Ponadto do znakowania i najwyższego poziomu o kategoryzacji, interfejs API przetwarzania obrazów obsługuje również informacje specjalistyczne (lub specyficznego dla domeny). Informacje można zaimplementować jako metoda autonomiczna lub wysokiego poziomu o kategoryzacji. Działa ona jako sposób, aby dalej zawęzić Taksonomia kategorii 86 przez dodanie modeli specyficznych dla domeny.

Obecnie tylko informacje specjalistyczne, obsługiwane są rozpoznawanie osobistości i charakterystycznych elementów krajobrazu rozpoznawania. Są one uściślenia specyficznego dla domeny dla osoby, osób grupy kategorii i charakterystycznych elementów krajobrazu na świecie.

Istnieją dwie opcje używania modeli specyficznych dla domeny:

### <a name="option-one---scoped-analysis"></a>Opcja pierwsza - zakresami analizy
Analizuj tylko wybrany model, wywołując wywołanie metody POST protokołu HTTP. Dla tej opcji Jeśli znasz model, który chcesz użyć, określ nazwę modelu i możesz pobrać wyłącznie informacje istotne dla modelu. Na przykład można używasz tej opcji do rozpoznawania osobistości są wyszukiwane tylko. Odpowiedź zawiera listę potencjalnych dopasowania osobistości, wraz z ich oceny zaufania.

### <a name="option-two---enhanced-analysis"></a>Opcja druga — rozszerzone analizy
Analizuj, aby zapewnić dodatkowe szczegóły powiązane z kategorii Taksonomia kategorii 86. Ta opcja jest dostępna do użytku w aplikacjach, w którym użytkownicy chcą uzyskać ogólny obraz analizy oprócz szczegółów z jednego lub więcej modeli specyficznych dla domeny. Gdy ta metoda jest wywoływana, najpierw jest wywoływana klasyfikatora Taksonomia kategorii 86. Jeśli dowolnej kategorii zgodny z typem znane/dopasowania modeli, druga — dostęp próbny wywołania klasyfikatora postępuje zgodnie ze. Na przykład jeśli "Szczegóły = all" lub "szczegóły" dołączyć "osobistości", metoda wywołuje klasyfikatora osobistości po klasyfikatora 86 kategorii jest wywoływana. Wynik zawiera tagi rozpoczynające się od "people_".

## <a name="generating-descriptions"></a>Generowanie opisów 
Algorytmy API przetwarzania obrazów analizowanie zawartości obrazu. Ta analiza stanowi podstawę dla "opis" wyświetlana jako język zrozumiałą dla użytkownika w postaci kompletnych zdań. Opis podsumowuje, co znajduje się na obrazie. Algorytmy API przetwarzania obrazów Generowanie opisy różnych na podstawie obiektów zidentyfikowane na obrazie. Poszczególne opisy są oceniane, po czym generowany jest współczynnik ufności. Następnie zwracana jest lista uporządkowana od najwyższego do najniższego współczynnika ufności. Przykład bot, korzystającą z tej technologii w celu generowania podpisów obrazów można znaleźć [tutaj](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).  

### <a name="example-description-generation"></a>Przykładowy opis generacji
![Kompil & budynki W](./Images/bw_buildings.png) '
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

## <a name="perceiving-color-schemes"></a>Odczuwaniu schematy kolorów
Algorytm przetwarzania obrazów wyodrębnia kolory z obrazu. Kolory są analizowane w trzech różnych kontekstach: na pierwszym planie, w tle i w całości obrazu. Są one podzielone na 12 dominujących kolorów akcentu. Te kolory akcentu są czarne, niebieski, brown, szary, zielony, orange, różowy, fioletowy, red, zielonomodrym, biały i żółty. W zależności od kolory na obrazie proste czarno biały lub kolorów akcentu mogą być zwracane w szesnastkowe kody kolorów.

Image (Obraz)                                                       | Pierwszy plan |Tło| Kolory
----------------------------------------------------------- | --------- | ------- | ------
![Aktywność na świeżym powietrzu górski](./Images/mountain_vista.png)            | Czarny     | Czarny   | Biały
![Wizja analizowanie Kwiatek](./Images/flower.png)               | Czarny     | Biały   | Białe, Black, zielony
![Wizja analizowanie Train stacji](./Images/train_station.png) | Czarny     | Czarny   | Czarny

### <a name="accent-color"></a>Kolor akcentu
Wyodrębnione z obrazu, zaprojektowane do reprezentowania najbardziej atrakcyjnych koloru dla użytkowników za pośrednictwem różnych kolory dominujące i nasycenie koloru.

Image (Obraz)                                                       | Odpowiedź
----------------------------------------------------------- | ----
![Aktywność na świeżym powietrzu górski](./Images/mountain_vista.png)            | #BC6F0F
![Wizja analizowanie Kwiatek](./Images/flower.png)               | #CAA501
![Wizja analizowanie Train stacji](./Images/train_station.png) | #484B83


### <a name="black--white"></a>Czarno -biały
Flagę logiczną, wskazującą, czy obraz jest czarno- & biały lub nie.

Image (Obraz)                                                      | Odpowiedź
---------------------------------------------------------- | ----
![Wizja analizowanie kompilowanie](./Images/bw_buildings.png)      | True
![Wizja analizowanie Yard DOM](./Images/house_yard.png)      | False

## <a name="flagging-adult-content"></a>Flagowanie treści dla dorosłych
Z różnych kategorii visual jest grupy dla dorosłych, która włącza wykrywanie materiałów treści dla dorosłych oraz ogranicza wyświetlanie obrazów zawierającą seksualnym zawartość. Filtr dla dorosłych wykrywania zawartości można ustawić na ruchomej skali umożliwiających preferencji użytkownika.

## <a name="optical-character-recognition-ocr"></a>Optyczne rozpoznawanie znaków (OCR)
Technologia OCR wykrywa zawartość tekstową na obrazie i wyodrębnia zidentyfikowany tekst do strumienia znaków czytelnych. Wynik służy do wyszukiwania i wiele innych celów, takich jak dokumentacji medycznej, zabezpieczeniami i bankowością. Funkcja automatycznie wykrywa język. Optyczne rozpoznawanie znaków oszczędza czas i zapewnia wygodę dla użytkowników, umożliwiając im wykonywanie zdjęć tekstu zamiast przepisywania tekstu.

Optyczne rozpoznawanie znaków obsługuje 25 językach. Te języki są: arabski, chiński uproszczony, chiński tradycyjny, czeski, duński, holenderski, angielski, fiński, francuski, niemiecki, grecki, węgierski, włoski, japoński, koreański, norweski, Polski, portugalski, rumuński, rosyjski, serbski (cyrylica i łaciński) Słowacki, hiszpański, szwedzki i turecki.

Jeśli to konieczne, optyczne rozpoznawanie znaków poprawia obrót rozpoznany w stopniach wokół osi obraz w poziomie. Optyczne rozpoznawanie znaków zawiera współrzędne ramki wystąpień poszczególnych wyrazów, jak pokazano poniżej ilustracji.

![Omówienie optyczne rozpoznawanie znaków](./Images/vision-overview-ocr.png) wymagania dotyczące Rozpoznawania:
- Rozmiar obrazu wejściowego musi być między 40 x 40 i 3200 x 3200 pikseli.
- Obraz nie może być większy niż 10 megapikseli.

Obrazu wejściowego można obracać o dowolnym wielu 90 stopni oraz małych kąta z maksymalnie 40 stopni.

Dokładność rozpoznawanie tekstu, zależy od jakości obrazu. Odczyt nieprawidłowych może być spowodowane w następujących sytuacjach:
- Obrazy rozmyte.
- Tekstu odręcznego lub kursywnych.
- Style czcionki sztuki.
- Rozmiar małego tekstu.
- Złożone tła, cieni lub odbić za pośrednictwem zakłócenia tekstu lub perspektywę.
- Zbyt duży lub brak wielkie litery na początku słowa
- Indeks dolny, indeks górny lub przekreślony tekst.

Ograniczenia: Dla których tekst jest dominującym zdjęć, wyniki fałszywie dodatnie mogą pochodzić z częściowo rozpoznane słowa. W niektórych zdjęcia, szczególnie zdjęcia bez dowolny tekst dokładności mogą się różnić w znacznie w zależności od typu obraz.

## <a name="recognize-handwritten-text"></a>Rozpoznawanie tekstu odręcznego
Ta technologia umożliwia wykrywanie i wyodrębnianie tekstu odręcznego z notatek, litery, esejów, tablic, formularzy itd. Działa ona na różnych powierzchniach i tłach, takich jak biały papier, żółte karteczki samoprzylepne i tablice.

Rozpoznawanie tekstu odręcznego oszczędza czas i pracę, a także może zwiększyć produktywność dzięki opcji przedstawiania tekstu w postaci obrazu zamiast jego transkrypcji. Zapewnia on możliwość notatek. Ta digitization pozwala na implementowanie szybkiego i prostego wyszukiwania. Zmniejsza ona również nieład związany z dużą ilością papieru.

Wymagania dotyczące danych wejściowych:
- Obsługiwane formaty obrazów: JPEG, PNG i BMP.
- Rozmiar pliku obrazu musi być mniejszy niż 4 MB.
- Wymiary obrazu musi być co najmniej 40 x 40, maksymalnie 3200 x 3200.

Uwaga: ta technologia jest obecnie dostępna w wersji zapoznawczej i tylko w przypadku tekstu w języku angielskim.

## <a name="generating-thumbnails"></a>Generowanie miniatur
Miniatura jest reprezentację mały obraz w pełnym rozmiarze. Zależeć od urządzeń, takich jak telefony, tablety i komputery utworzyć na potrzeby innego użytkownika, układy środowiska i rozmiary miniatur. Za pomocą inteligentne przycinanie, ta funkcja interfejsu API Computer Vision pomaga w rozwiązaniu problemu.

Po przekazaniu obrazu, wysokiej jakości miniaturę pobiera wygenerowany i algorytm interfejsu API przetwarzania obrazów analizuje obiekty na obrazie. Następnie Przycina obraz zgodnie z wymaganiami "regionu zainteresowania" (ROI). Dane wyjściowe pobiera wyświetlane w ramach specjalne, jak pokazano poniżej ilustracji. Wygenerowane miniatury można użyć dowolnego racja aspekt, który różni się od proporcji niż oryginalny obraz spełniają wymagania użytkownika.

Algorytm miniatury działa w następujący sposób:

1. Usuwa zbędne elementy z obrazu i rozpoznaje głównym obiektem "regionu zainteresowania" (ROI).
2. Przycina obraz na podstawie zidentyfikowanych regionu zainteresowania.
3. Zmienia współczynnika proporcji do rozmiaru miniatur docelowego.

![Miniatury](./Images/thumbnail-demo.png)
