---
title: Co to jest interfejs API przetwarzania obrazów? — Przetwarzanie obrazów
titlesuffix: Azure Cognitive Services
description: Usługa przetwarzania obrazów oferuje deweloperom dostęp do zaawansowanych algorytmów przetwarzania obrazów i zwracania informacji.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/04/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 87c5d24f0e9077acf66bacd97097cfc32bd49ead
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60901106"
---
# <a name="what-is-computer-vision"></a>Czym jest przetwarzanie obrazów?

Usługa przetwarzania obrazów platformy Azure oferuje deweloperom dostęp do zaawansowanych algorytmów przetwarzania obrazów i informacji zwrotnych. W celu analizy obrazu można przekazać obraz lub określić jego adres URL. Algorytmy przetwarzania obrazów pozwalają analizować zawartość na różne sposoby, w zależności od interesujących Cię funkcji wizualnych. Na przykład usługa przetwarzania obrazów może stwierdzić, czy na obrazie znajduje się zawartość przeznaczona dla dorosłych, lub znaleźć wszystkie twarze na obrazie.

Przetwarzania obrazów w aplikacji można używać, korzystając z natywnego zestawu SDK lub wywołując interfejs API REST bezpośrednio. Na tej stronie ogólnie omówiono, co można zrobić za pomocą przetwarzania obrazów.

## <a name="analyze-images-for-insight"></a>Analizowanie obrazów w celu uzyskania szczegółowych informacji

Obrazy można analizować, aby wykrywać cechy wizualne i właściwości obrazów oraz dostarczać szczegółowe informacje na ich temat. Wszystkie funkcje w poniższej tabeli są udostępniane przez interfejs API [analizowania obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).

| Akcja | Opis |
| ------ | ----------- |
|**[Tagowanie funkcji wizualnych](concept-tagging-images.md)**|Identyfikowanie i tagowanie elementów wizualnych na obrazie w oparciu o zestaw tysięcy rozpoznawalnych obiektów, istot żywych, scenerii i akcji. Gdy tagi są niejednoznaczne lub wykraczają poza popularną wiedzę, odpowiedź interfejsu API zawiera „wskazówki”, aby wyjaśnić ich znaczenie w kontekście znanego otoczenia. Tagowanie nie jest ograniczone do głównego tematu, na przykład do osoby na pierwszym planie, ale uwzględnia także otoczenie (wewnątrz lub na zewnątrz), meble, narzędzia, rośliny, zwierzęta, akcesoria, gadżety itd.|
|**[Wykrywanie obiektów](concept-object-detection.md)**| Wykrywanie obiektów jest podobne do tagowania, ale interfejs API zwraca współrzędne pola ograniczenia dla każdego zastosowanego tagu. Na przykład jeśli na obrazie znajduje się pies, kot i osoba, operacja wykrywania utworzy listę tych obiektów wraz z ich współrzędnymi na obrazie. Ta funkcja umożliwia przetwarzanie dalszych relacji między obiektami na obrazie. Ponadto w odpowiednich przypadkach informuje, że obraz zawiera wiele wystąpień tego samego tagu.|
|**[Wykrywanie marek](concept-brand-detection.md)**|Identyfikuj marki handlowe na obrazach lub filmach na podstawie bazy danych z tysiącami znaków logo z całego świata. Za pomocą tej funkcji można na przykład ustalać, które marki są najpopularniejsze w mediach społecznościowych lub najpowszechniej promowane za pomocą lokowania produktów w mediach.|
|**[Kategoryzowanie obrazu](concept-categorizing-images.md)**|Identyfikowanie i kategoryzowanie całego obrazu za pomocą [taksonomii kategorii](Category-Taxonomy.md) z użyciem dziedzicznych hierarchii obiektów nadrzędnych i podrzędnych. Kategorii można używać oddzielnie lub z naszymi nowymi modelami tagowania.<br/>Obecnie jedynym obsługiwanym językiem tagowania i kategoryzowania obrazów jest angielski.|
|**[Opisywanie obrazu](concept-describing-images.md)**|Generowanie opisu całego obrazu w języku zrozumiałym dla człowieka przy użyciu pełnych zdań. Algorytmy przetwarzania obrazów generują różne opisy na podstawie obiektów zidentyfikowanych na obrazie. Poszczególne opisy są oceniane, po czym generowany jest współczynnik ufności. Następnie zwracana jest lista uporządkowana od najwyższego do najniższego współczynnika ufności.|
|**[Wykrywanie twarzy](concept-detecting-faces.md)** |Wykrywanie twarzy na obrazie i dostarczanie informacji o każdej wykrytej twarzy. Usługa przetwarzania obrazów zwraca współrzędne, prostokąt, płeć i wiek osoby dla każdej wykrytej twarzy.<br/>Przetwarzanie obrazów zawiera podzestaw funkcji, które można znaleźć w usłudze [rozpoznawania twarzy](/azure/cognitive-services/face/), a usługa rozpoznawania twarzy umożliwia bardziej szczegółową analizę, np. identyfikację twarzy i wykrywanie póz.|
|**[Wykrywanie typów obrazu](concept-detecting-image-types.md)**|Wykrywanie właściwości obrazu, na przykład czy obraz jest rysunkiem lub prawdopodobieństwa, że obraz jest obiektem clipart.|
|**[Wykrywanie zawartości specyficznej dla domeny](concept-detecting-domain-content.md)**|Wykrywanie i identyfikowanie zawartości obrazu specyficznej dla domeny, takiej jak osobistości i charakterystyczne elementy krajobrazu, przy użyciu modeli domeny. Na przykład jeśli obraz zawiera osoby, usługa przetwarzania obrazów, używając modelu domeny dla osobistości uwzględnionych w usłudze, może stwierdzić, czy osoby wykryte na obrazie pasują do znanych osobistości.|
|**[Wykrywanie schematu kolorów](concept-detecting-color-schemes.md)**|Analizowanie użycia kolorów na obrazie. Przetwarzanie obrazów może określić, czy obraz jest czarno-biały, czy kolorowy, a w przypadku obrazów kolorowych zidentyfikować kolory dominujące i kolory akcentu.|
|**[Generowanie miniatur](concept-generating-thumbnails.md)**|Analizowanie zawartości obrazu w celu wygenerowania dla niego odpowiedniej miniatury. Usługa przetwarzania obrazów najpierw generuje wysokiej jakości miniaturę, po czym analizuje obiekty na obrazie, aby określić *obszar zainteresowania*. Następnie przetwarzanie obrazów przycina obraz zgodnie z wymaganiami obszaru zainteresowania. W zależności od potrzeb wygenerowana miniatura może mieć współczynnik proporcji inny od współczynnika proporcji oryginalnego obrazu.|
|**[Określanie obszaru zainteresowania](concept-generating-thumbnails.md#area-of-interest)**|Przeanalizuj zawartość obrazu w celu zwrócenia współrzędnych *obszaru zainteresowania*. Jest to ta sama funkcja, która służy do generowania miniatur, ale zamiast przycinania obrazu przetwarzanie obrazów zwraca współrzędne pola ograniczenia regionu, dlatego aplikacja wywołująca może modyfikować oryginalny obraz zgodnie z potrzebami.|

## <a name="extract-text-from-images"></a>Wyodrębnianie tekstu z obrazów

Przetwarzanie obrazów służą do wyodrębniania tekstu z obrazu do strumienia znaków do odczytu maszynowego przy użyciu [optyczne rozpoznawanie znaków (OCR)](concept-recognizing-text.md#ocr-optical-character-recognition-api). Jeśli to konieczne, funkcja optycznego rozpoznawania znaków poprawia kąt obrotu i podaje współrzędne ramki każdego wyrazu. Funkcja optycznego rozpoznawania znaków obsługuje 25 języków i automatycznie wykrywa język rozpoznanego tekstu.

Można również użyć [API odczytu](concept-recognizing-text.md#read-api) umożliwia wyodrębnianie tekstu odręcznego i drukować z obrazy i dokumenty zawierające dużo tekstu. Interfejs API odczytu używa modeli zaktualizowane i działa dla obiektów różne na różnych powierzchniach i tłach, takich jak przychody, plakaty, wizytówki, litery i tablic. Obecnie interfejs API odczytu jest w wersji zapoznawczej i jedynym obsługiwanym językiem jest angielski.

## <a name="moderate-content-in-images"></a>Moderowanie zawartości na obrazach

Przy użyciu usługi przetwarzania obrazów można [wykrywać zawartość przeznaczoną dla dorosłych](concept-detecting-adult-content.md) na obrazie i zwracać współczynnik ufności. Filtr umożliwiający wykrywanie zawartości dla dorosłych można ustawiać na ruchomej skali, aby dostosować go do swoich preferencji.

## <a name="use-containers"></a>Korzystanie z kontenerów

Po zainstalowaniu standardowego kontenera platformy Docker blisko danych można lokalnie rozpoznawać tekst drukowany i pismo odręczne, [korzystając z kontenerów przetwarzania obrazów](computer-vision-how-to-install-containers.md).

## <a name="image-requirements"></a>Wymagania obrazu

Przetwarzanie obrazów umożliwia analizowanie obrazów, które spełniają następujące wymagania:

- Obraz musi być w formacie JPEG, PNG, GIF lub BMP
- Rozmiar pliku obrazu musi być mniejszy niż 4 megabajty (MB)
- Wymiary obrazu muszą być większe niż 50x50 pikseli
  - W przypadku funkcji optycznego rozpoznawania znaków wymiary obrazu muszą wynosić od 50x50 pikseli do 4200x4200 pikseli

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi przetwarzania obrazów powinni znać zasady firmy Microsoft dotyczące danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Kolejne kroki

Rozpocznij pracę z przetwarzaniem obrazów, korzystając z przewodnika Szybki start:

- [Szybki start: Analizowanie obrazu](quickstarts-sdk/csharp-analyze-sdk.md)
- [Szybki start: Wyodrębnianie tekstu odręcznego](quickstarts-sdk/csharp-hand-text-sdk.md)
- [Szybki start: Generowanie miniatur](quickstarts-sdk/csharp-thumb-sdk.md)
