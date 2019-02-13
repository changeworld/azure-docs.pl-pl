---
title: Co to jest interfejs API przetwarzania obrazów?
titlesuffix: Azure Cognitive Services
description: Interfejs API przetwarzania obrazów oferuje deweloperom dostęp do zaawansowanych algorytmów przetwarzania obrazów i zwracania informacji.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 08/10/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 1ee28d04bfce00a6fe47d6914b75cfd7b18e634d
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816547"
---
# <a name="what-is-computer-vision-api-version-10"></a>Co to jest interfejs API przetwarzania obrazów w wersji 1.0?

> [!IMPORTANT]
> Obecnie dostępna jest nowa wersja interfejsu API przetwarzania obrazów, zobacz:
>- [Omówienie](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [Interfejs API przetwarzania obrazów w wersji 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

Oparty na chmurze interfejs API przetwarzania obrazów oferuje deweloperom dostęp do zaawansowanych algorytmów przetwarzania obrazów i zwracania informacji. Po przekazaniu obrazu lub podaniu jego adresu URL algorytmy funkcji przetwarzania obrazu firmy Microsoft mogą dokonać analizy zawartości wizualnej na różne sposoby w zależności od danych wejściowych i wybranych opcji użytkownika. Za pomocą interfejsu API przetwarzania obrazów użytkownicy mogą analizować obrazy w celu:
* Oznaczania obrazów w oparciu o zawartość.
* Kategoryzowania obrazów.
* Określania typu i jakości obrazów.
* [Wykrywania twarzy i zwracania ich współrzędnych.](#Faces)
* Rozpoznawania zawartości specyficznej dla domeny.
* Generowania opisów zawartości.
* Używania optycznego rozpoznawania znaków do identyfikacji tekstu drukowanego w obrazach.
* Rozpoznawania tekstu odręcznego.
* Rozróżniania schematów kolorów.
* Flagowania zawartości dla dorosłych.
* Przycinania zdjęć do użycia jako miniatury.

## <a name="requirements"></a>Wymagania
* Obsługiwane metody wprowadzania danych: dane binarne nieprzetworzonego obrazu w postaci pliku typu application/octet stream lub adres URL obrazu.
* Obsługiwane formaty obrazów: JPEG, PNG, GIF, BMP.
* Rozmiar pliku obrazu: mniej niż 4 MB.
* Wymiary obrazu: więcej niż 50 x 50 pikseli.

## <a name="tagging-images"></a>Oznaczanie obrazów
Interfejs API przetwarzania obrazów zwraca tagi na podstawie tysięcy rozpoznawalnych obiektów, istot żywych, scenerii i akcji. Gdy tagi są niejednoznaczne lub wykraczają poza popularną wiedzę, odpowiedź interfejsu API zawiera „wskazówki”, aby wyjaśnić ich znaczenie w kontekście znanego otoczenia. Tagi nie są zorganizowane w formie taksonomii i nie występują hierarchie dziedziczenia. Kolekcja tagów zawartości stanowi podstawę „opisu” obrazu wyświetlanego jako język zrozumiały dla użytkownika w formie pełnych zdań. Należy pamiętać, że obecnie jedynym obsługiwanym językiem opisu obrazu jest język angielski.

Po przekazaniu obrazu lub określeniu jego adresu URL algorytmy interfejsu API przetwarzania obrazów zwracają tagi w oparciu o obiekty, istoty żywe i działania zidentyfikowane na obrazie. Tagowanie nie jest ograniczone do głównego tematu, na przykład do osoby na pierwszym planie, ale uwzględnia także otoczenie (wewnątrz lub na zewnątrz), meble, narzędzia, rośliny, zwierzęta, akcesoria, gadżety itd.

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
Oprócz oznaczania i opisów interfejs API przetwarzania obrazów zwraca kategorie oparte na taksonomii zdefiniowane w poprzednich wersjach. Te kategorie są uporządkowane w formie taksonomii z użyciem dziedziczonych hierarchii obiektów nadrzędnych i podrzędnych. Wszystkie kategorie są w języku angielskim. Mogą być używane oddzielnie lub z naszymi nowymi modelami.

### <a name="the-86-category-concept"></a>Koncepcja 86 kategorii
Na podstawie listy 86 koncepcji widocznej na poniższym diagramie cechy wizualne obrazu można podzielić na szerokie lub konkretne. Aby uzyskać informacje dotyczące pełnej taksonomii w formacie tekstowym, zobacz temat [Taksonomia kategorii](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).

![Analizowanie kategorii](./Images/analyze_categories.png)

Image (Obraz)                                                  | Odpowiedź
------------------------------------------------------ | ----------------
![Kobieta na dachu](./Images/woman_roof.png)                 | osoby
![Zdjęcie rodzinne](./Images/family_photo.png)             | people_crowd
![Słodki piesek](./Images/cute_dog.png)                     | animal_dog
![Na zewnątrz w górach](./Images/mountain_vista.png)       | outdoor_mountain
![Analiza obrazu — żywność, chleb](./Images/bread.png)       | food_bread

## <a name="identifying-image-types"></a>Identyfikowanie typów obrazów
Istnieje wiele sposobów na kategoryzowanie obrazów. Interfejs API przetwarzania obrazów może ustawić flagę logiczną, aby wskazać, czy obraz jest czarno biały, czy kolorowy. Może również ustawić flagę wskazującą, czy obraz jest obiektem rysunkowym. Może ona również określić, czy obraz jest obiektem clipart oraz wskazać jego jakość w skali od 0 do 3.

### <a name="clip-art-type"></a>Typ obiektu clipart
Wykrywa, czy obraz jest obiektem clipart.  

Wartość | Znaczenie
----- | --------------
0     | Obiekt inny niż clipart
1     | Niejednoznaczny
2     | Normalny obiekt clipart
3     | Poprawny obiekt clipart

Image (Obraz)|Odpowiedź
----|----
![Analiza obrazu — obiekt clipart przedstawiający ser](./Images/cheese_clipart.png)|3 Poprawny obiekt clipart
![Analiza obrazu — podwórze](./Images/house_yard.png)|0 Obiekt inny niż clipart

### <a name="line-drawing-type"></a>Typ rysunku
Wykrywa, czy obraz jest rysunkiem.

Image (Obraz)|Odpowiedź
----|----
![Analiza obrazu — rysunek przedstawiający lwa](./Images/lion_drawing.png)|True
![Analiza obrazu — kwiat](./Images/flower.png)|False

### <a name="faces"></a>Twarze
Wykrywa twarze osób na zdjęciu i generuje współrzędne rozpoznawania twarzy, prostokąt twarzy, płeć i wiek. Te funkcje wizualne stanowią podzestaw metadanych wygenerowanych dla twarzy. W celu uzyskania większej liczby metadanych generowanych dla twarzy (w ramach identyfikacji twarzy, wykrywania postawy itd.) należy użyć interfejsu API rozpoznawania twarzy.  

Image (Obraz)|Odpowiedź
----|----
![Analiza obrazu — kobieta, dach, twarz](./Images/woman_roof_face.png) | [ { "age": 23, "gender": "Female", "faceRectangle": { "left": 1379, "top": 320, "width": 310, "height": 310 } } ]
![Analiza obrazu — matka, córka, twarz](./Images/mom_daughter_face.png) | [ { "age": 28, "gender": "Female", "faceRectangle": { "left": 447, "top": 195, "width": 162, "height": 162 } }, { "age": 10, "gender": "Female", "faceRectangle": { "left": 355, "top": 87, "width": 143, "height": 143 } } ]
![Analiza obrazu — zdjęcie rodzinne, twarz](./Images/family_photo_face.png) | [ { "age": 11, "gender": "Female", "faceRectangle": { "left": 113, "top": 314, "width": 222, "height": 222 } }, { "age": 11, "gender": "Female", "faceRectangle": { "left": 1200, "top": 632, "width": 215, "height": 215 } }, { "age": 41, "gender": "Female", "faceRectangle": { "left": 514, "top": 223, "width": 205, "height": 205 } }, { "age": 37, "gender": "Female", "faceRectangle": { "left": 1008, "top": 277, "width": 201, "height": 201 } } ]


## <a name="domain-specific-content"></a>Zawartość specyficzna dla domeny

Oprócz oznaczania i kategoryzacji najwyższego poziomu interfejs API przetwarzania obrazów obsługuje również informacje specjalistyczne (lub specyficzne dla domeny). Informacje specjalistyczne można wdrażać jako metodę autonomiczną lub na wysokim poziomie kategoryzacji. Funkcjonują one jako sposób dalszego uściślenia taksonomii obejmującej 86 kategorii przez dodanie modeli specyficznych dla domeny.

Obecnie jedynymi obsługiwanymi informacjami specjalistycznymi są informacje uzyskiwane na podstawie rozpoznawania twarzy i rozpoznawania elementów krajobrazu. Są to uściślenia specyficzne dla domeny dla kategorii osób i grup osób oraz elementów krajobrazu na całym świecie.

Istnieją dwie opcje używania modeli specyficznych dla domeny:

### <a name="option-one---scoped-analysis"></a>Opcja pierwsza — analiza z zakresami
Analizowany jest tylko wybrany model, za pomocą wywołania metody HTTP POST. W przypadku tej opcji, jeśli wiesz, którego modelu chcesz użyć, podajesz nazwę modelu i uzyskujesz informacje istotne tylko dla tego modelu. Możesz na przykład użyć tej opcji tylko do wyszukiwania rozpoznawania celebrytów. Odpowiedź zawiera listę potencjalnych pasujących celebrytów wraz ze współczynnikami ufności.

### <a name="option-two---enhanced-analysis"></a>Opcja druga — rozszerzona analiza
Analiza dostarczająca dodatkowe szczegóły związane z kategoriami z taksonomii zawierającej 86 kategorii. Ta opcja jest dostępna w aplikacjach, w których użytkownicy chcą uzyskać analizę ogólną obrazu oprócz szczegółów z co najmniej jednego modelu specyficznego dla domeny. Po wywołaniu tej metody klasyfikator taksonomii zawierający 86 kategorii zostanie wywołany jako pierwszy. Jeśli którakolwiek z kategorii jest zgodna ze znanym/pasującym modelem, następuje drugie przekazanie wywołań klasyfikatora. Jeśli na przykład parametr 'details=all' lub "details" zawiera wartość 'celebrities', metoda wywołuje klasyfikator celebrytów po wywołaniu klasyfikatora zawierającego 86 kategorii. Wynik zawiera tagi rozpoczynające się od 'people_'.

## <a name="generating-descriptions"></a>Generowanie opisów 
Algorytmy interfejsu API przetwarzania obrazów analizują zawartość obrazu. Ta analiza stanowi podstawę „opisu” wyświetlanego jako język zrozumiały dla użytkownika w formie pełnych zdań. Opis zawiera podsumowanie zawartości obrazu. Algorytmy interfejsu API przetwarzania obrazów generują różne opisy na podstawie obiektów zidentyfikowanych na obrazie. Poszczególne opisy są oceniane, po czym generowany jest współczynnik ufności. Następnie zwracana jest lista uporządkowana od najwyższego do najniższego współczynnika ufności. Przykład bota generującego podpisy obrazów przy użyciu tej technologii można znaleźć [tutaj](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).  

### <a name="example-description-generation"></a>Przykładowy opis generacji
![Budynki B&W](./Images/bw_buildings.png) '
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

## <a name="perceiving-color-schemes"></a>Postrzeganie schematów kolorów
Algorytm przetwarzania obrazów wyodrębnia z obrazu kolory. Kolory są analizowane w trzech różnych kontekstach: na pierwszym planie, w tle i w całości obrazu. Są pogrupowane w 12 dominujących kolorów wiodących. Kolory wiodące to: czarny, niebieski, brązowy, szary, zielony, pomarańczowy, różowy, fioletowy, czerwony, zielonomodry, biały i żółty. W zależności od kolorów na obrazie zwykły czarny lub biały bądź kolory wiodące mogą być zwracane w szesnastkowych kodach kolorów.

Image (Obraz)                                                       | Pierwszy plan |Tło| Kolory
----------------------------------------------------------- | --------- | ------- | ------
![Na zewnątrz w górach](./Images/mountain_vista.png)            | Czarny     | Czarny   | Biały
![Analiza obrazu — kwiat](./Images/flower.png)               | Czarny     | Biały   | Biały, czarny, zielony
![Analiza obrazu — stacja kolejowa](./Images/train_station.png) | Czarny     | Czarny   | Czarny

### <a name="accent-color"></a>Kolor wiodący
Kolor wyodrębniony z obrazu, który ma być dla użytkowników najlepiej widoczny, dzięki użyciu różnych kolorów dominujących oraz nasycenia.

Image (Obraz)                                                       | Odpowiedź
----------------------------------------------------------- | ----
![Na zewnątrz w górach](./Images/mountain_vista.png)            | #BC6F0F
![Analiza obrazu — kwiat](./Images/flower.png)               | #CAA501
![Analiza obrazu — stacja kolejowa](./Images/train_station.png) | #484B83


### <a name="black--white"></a>Czarno -biały
Flaga logiczna, która wskazuje, czy obraz jest czarno-biały czy nie.

Image (Obraz)                                                      | Odpowiedź
---------------------------------------------------------- | ----
![Analiza obrazu — budynek](./Images/bw_buildings.png)      | True
![Analiza obrazu — podwórze](./Images/house_yard.png)      | False

## <a name="flagging-adult-content"></a>Flagowanie zawartości dla dorosłych
Spośród różnych kategorii wizualnych możemy wyróżnić grupę przeznaczoną dla zawartości dla dorosłych i o charakterze erotycznym. Umożliwia ona wykrywanie materiałów dla dorosłych i ogranicza wyświetlanie obrazów zawierających zawartość o charakterze erotycznym. Filtr umożliwiający wykrywanie zawartości dla dorosłych można ustawiać na ruchomej skali, aby dostosować go do preferencji użytkownika.

## <a name="optical-character-recognition-ocr"></a>Optyczne rozpoznawanie znaków (OCR)
Technologia OCR wykrywa zawartość tekstową na obrazie i wyodrębnia zidentyfikowany tekst do strumienia znaków, które mogą być odczytane przez komputer. Wynik służy do wyszukiwania oraz wielu innych celów, ma np. zastosowanie w przypadku dokumentacji medycznej, branży ochrony i bankowości. Umożliwia automatyczne wykrywanie języka. Optyczne rozpoznawanie znaków oszczędza czas i jest wygodne dla użytkowników, którzy mogą wykonywać zdjęcia tekstu zamiast go przepisywać.

OCR obsługuje 25 języków. Te języki to: arabski, chiński uproszczony, chiński tradycyjny, czeski, duński, holenderski, angielski, fiński, francuski, niemiecki, grecki, węgierski, włoski, japoński, koreański, norweski, polski, portugalski, rumuński, rosyjski, serbski (cyrylica i alfabet łaciński), słowacki, hiszpański, szwedzki i turecki.

Jeśli to konieczne, OCR poprawia kąt obrotu (w stopniach) rozpoznanego tekstu wokół poziomej osi obrazu. OCR dostarcza współrzędne ramki poszczególnych wyrazów, jak przedstawiono na ilustracji poniżej.

![OCR —przegląd](./Images/vision-overview-ocr.png) Wymagania dotyczące OCR:
- Rozmiar obrazu wejściowego musi być z zakresu od 40 x 40 do 3200 x 3200 pikseli.
- Obraz nie może być większy niż 10 megapikseli.

Obraz wejściowy można obracać o dowolną wielokrotność 90 stopni oraz pod niewielkim kątem wynoszącym maksymalnie 40 stopni.

Dokładność rozpoznawania tekstu zależy od jakości obrazu. Przyczyny nieprawidłowego odczytu mogą być następujące:
- Rozmyte obrazy.
- Tekst odręczny lub napisany kursywą.
- Artystyczne style czcionki.
- Mały rozmiar tekstu.
- Złożone tła, cienie, oślepiające światło nad tekstem lub zniekształcenie perspektywy.
- Zbyt duże wielkie litery na początku wyrazów lub ich brak.
- Indeks dolny, indeks górny lub tekst przekreślony.

Ograniczenia: w przypadku zdjęć, na których dominuje tekst, wyniki fałszywie dodatnie mogą pochodzić z częściowo rozpoznanych wyrazów. W przypadku niektórych zdjęć, szczególnie zdjęć bez tekstu, dokładność może być bardzo różna w zależności od typu obrazu.

## <a name="recognize-handwritten-text"></a>Rozpoznawanie tekstu odręcznego
Ta technologia umożliwia wykrywanie i wyodrębnianie tekstu odręcznego z notatek, listów, esejów, tablic, formularzy itd. Działa ona na różnych powierzchniach i tłach, takich jak biały papier, żółte karteczki samoprzylepne i tablice.

Rozpoznawanie tekstu odręcznego oszczędza czas i pracę, a także może zwiększyć produktywność dzięki opcji przedstawiania tekstu w postaci obrazu zamiast jego transkrypcji. Umożliwia przekształcenie notatek odręcznych w cyfrowe. Taka cyfryzacja pozwala na wdrożenie szybkiego i łatwego wyszukiwania. Zmniejsza ona również nieład związany z dużą ilością papieru.

Wymagania dotyczące danych wejściowych:
- Obsługiwane formaty obrazów: JPEG, PNG i BMP.
- Rozmiar pliku obrazu musi być mniejszy niż 4 MB.
- Wymiary obrazu muszą wynosić co najmniej 40 x 40, maksymalnie 3200 x 3200.

Uwaga: ta technologia jest obecnie dostępna w wersji zapoznawczej i tylko dla tekstów w języku angielskim.

## <a name="generating-thumbnails"></a>Generowanie miniatur
Miniatura to niewielki obrazek reprezentujący duży obraz w pełnym wymiarze. Różne urządzenia, takie jak telefony, tablety i komputery PC, mają różne wymagania dotyczące środowiska użytkownika i rozmiarów miniatur. Interfejs API przetwarzania obrazów pomaga rozwiązać ten problem za pomocą funkcji inteligentnego przycinania.

Po przekazaniu obrazu zostaje wygenerowana wysokiej jakości miniatura, a algorytm interfejsu API przetwarzania obrazów analizuje obiekty na obrazie. Następnie przycina obraz zgodnie z wymaganiami „obszaru zainteresowania”. Dane wyjściowe są wyświetlane w ramach określonej struktury, jak pokazano na ilustracji poniżej. W zależności od potrzeb użytkownika wygenerowana miniatura może mieć współczynnik proporcji inny od współczynnika proporcji oryginalnego obrazu.

Algorytm miniatury działa w następujący sposób:

1. Usuwa zbędne elementy z obrazu i rozpoznaje obiekt główny, „obszar zainteresowania”.
2. Przycina obraz w oparciu o zidentyfikowany obszar zainteresowania.
3. Zmienia współczynnik proporcji i dopasowuje go do docelowego rozmiaru miniatur.

![Miniatury](./Images/thumbnail-demo.png)
