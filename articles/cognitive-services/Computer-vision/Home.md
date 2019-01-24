---
title: Co to jest interfejs API przetwarzania obrazów? — Przetwarzanie obrazów
titlesuffix: Azure Cognitive Services
description: Usługa przetwarzania obrazów oferuje deweloperom dostęp do zaawansowanych algorytmów przetwarzania obrazów i zwracania informacji.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: overview
ms.date: 08/22/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: cd4eca669936a206241069f1f8a8d9440f45be33
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229782"
---
# <a name="what-is-computer-vision"></a>Czym jest przetwarzanie obrazów?

Oparta na chmurze usługa przetwarzania obrazów oferuje deweloperom dostęp do zaawansowanych algorytmów przetwarzania obrazów i zwracania informacji. Przetwarzanie obrazów współpracuje z popularnymi formatami obrazów, takimi jak JPEG i PNG. W celu analizy obrazu można przekazać obraz lub określić jego adres URL. Algorytmy przetwarzania obrazów pozwalają analizować zawartość obrazu na różne sposoby, w zależności od interesujących Cię funkcji wizualnych. Na przykład usługa przetwarzania obrazów może stwierdzić, czy na obrazie znajduje się zawartość przeznaczona dla dorosłych, lub znaleźć wszystkie twarze na obrazie.

Wywołując usługę za pomocą naszych [bibliotek klienckich](quickstarts-sdk/csharp-analyze-sdk.md) lub wywołując [interfejs API REST](vision-api-how-to-topics/howtocallvisionapi.md) bezpośrednio, można używać przetwarzania obrazów w aplikacji, aby wykonywać następujące czynności:

- [Analizowanie obrazów w celu uzyskania szczegółowych informacji](#analyzing-images-for-insight)
- [Wyodrębnianie tekstu z obrazów](#extracting-text-from-images)
- [Moderowanie zawartości na obrazach](#moderating-content-in-images)

## <a name="analyzing-images-for-insight"></a>Analizowanie obrazów w celu uzyskania szczegółowych informacji

Za pomocą usługi przetwarzania obrazów można analizować obrazy, aby wykrywać funkcje wizualne i właściwości obrazów oraz dostarczać szczegółowe informacje na ich temat. Można zarówno przekazać zawartość obrazu w celu analizowania obrazów lokalnych, jak również określić adres URL obrazu, aby analizować obrazy zdalne.

Podczas analizowania obrazu usługa przetwarzania obrazów umożliwia wykonywanie następujących akcji:

| Akcja | Opis |
| ------ | ----------- |
|**[Tagowanie funkcji wizualnych](concept-tagging-images.md)**|Identyfikowanie i tagowanie elementów wizualnych na obrazie w oparciu o zestaw tysięcy rozpoznawalnych obiektów, istot żywych, scenerii i akcji. Gdy tagi są niejednoznaczne lub wykraczają poza popularną wiedzę, odpowiedź interfejsu API zawiera „wskazówki”, aby wyjaśnić ich znaczenie w kontekście znanego otoczenia. Tagowanie nie jest ograniczone do głównego tematu, na przykład do osoby na pierwszym planie, ale uwzględnia także otoczenie (wewnątrz lub na zewnątrz), meble, narzędzia, rośliny, zwierzęta, akcesoria, gadżety itd.|
|**[Wykrywanie obiektów](concept-object-detection.md)**| Wykrywanie obiektów jest podobne do tagowania, ale interfejs API zwraca współrzędne pola ograniczenia dla każdego zastosowanego tagu. Na przykład jeśli na obrazie znajduje się pies, kot i osoba, operacja wykrywania utworzy listę tych obiektów wraz z ich współrzędnymi na obrazie. Ta funkcja umożliwia przetwarzanie dalszych relacji między obiektami na obrazie. Ponadto w odpowiednich przypadkach informuje, że obraz zawiera wiele wystąpień tego samego tagu.|
|**[Kategoryzowanie obrazu](concept-categorizing-images.md)**|Identyfikowanie i kategoryzowanie całego obrazu za pomocą [taksonomii kategorii](Category-Taxonomy.md) z użyciem dziedzicznych hierarchii obiektów nadrzędnych i podrzędnych. Kategorii można używać oddzielnie lub z naszymi nowymi modelami tagowania.<br/>Obecnie jedynym obsługiwanym językiem tagowania i kategoryzowania obrazów jest angielski.|
|**[Opisywanie obrazu](concept-describing-images.md)**|Generowanie opisu całego obrazu w języku zrozumiałym dla człowieka przy użyciu pełnych zdań. Algorytmy przetwarzania obrazów generują różne opisy na podstawie obiektów zidentyfikowanych na obrazie. Poszczególne opisy są oceniane, po czym generowany jest współczynnik ufności. Następnie zwracana jest lista uporządkowana od najwyższego do najniższego współczynnika ufności.|
|**[Wykrywanie twarzy](concept-detecting-faces.md)** |Wykrywanie twarzy na obrazie i dostarczanie informacji o każdej wykrytej twarzy. Usługa przetwarzania obrazów zwraca współrzędne, prostokąt, płeć i wiek osoby dla każdej wykrytej twarzy.<br/>Przetwarzanie obrazów zawiera podzestaw funkcji, które można znaleźć w usłudze [rozpoznawania twarzy](/azure/cognitive-services/face/), a usługa rozpoznawania twarzy umożliwia bardziej szczegółową analizę, np. identyfikację twarzy i wykrywanie póz.|
|**[Wykrywanie typów obrazu](concept-detecting-image-types.md)**|Wykrywanie właściwości obrazu, na przykład czy obraz jest rysunkiem lub prawdopodobieństwa, że obraz jest obiektem clipart.|
|**[Wykrywanie zawartości specyficznej dla domeny](concept-detecting-domain-content.md)**|Wykrywanie i identyfikowanie zawartości obrazu specyficznej dla domeny, takiej jak osobistości i charakterystyczne elementy krajobrazu, przy użyciu modeli domeny. Na przykład jeśli obraz zawiera osoby, usługa przetwarzania obrazów, używając modelu domeny dla osobistości uwzględnionych w usłudze, może stwierdzić, czy osoby wykryte na obrazie pasują do znanych osobistości.|
|**[Wykrywanie schematu kolorów](concept-detecting-color-schemes.md)**|Analizowanie użycia kolorów na obrazie. Przetwarzanie obrazów może określić, czy obraz jest czarno-biały, czy kolorowy, a w przypadku obrazów kolorowych zidentyfikować kolory dominujące i kolory akcentu.|
|**[Generowanie miniatur](concept-generating-thumbnails.md)**|Analizowanie zawartości obrazu w celu wygenerowania dla niego odpowiedniej miniatury. Usługa przetwarzania obrazów najpierw generuje wysokiej jakości miniaturę, po czym analizuje obiekty na obrazie, aby określić *obszar zainteresowania*. Następnie przetwarzanie obrazów przycina obraz zgodnie z wymaganiami obszaru zainteresowania. W zależności od potrzeb wygenerowana miniatura może mieć współczynnik proporcji inny od współczynnika proporcji oryginalnego obrazu.|
|**[Określanie obszaru zainteresowania](concept-generating-thumbnails.md#area-of-interest)**|Przeanalizuj zawartość obrazu w celu zwrócenia współrzędnych *obszaru zainteresowania*. Jest to ta sama funkcja, która służy do generowania miniatur, ale zamiast przycinania obrazu przetwarzanie obrazów zwraca współrzędne pola ograniczenia regionu, dlatego aplikacja wywołująca może modyfikować oryginalny obraz zgodnie z potrzebami.|

## <a name="extracting-text-from-images"></a>Wyodrębnianie tekstu z obrazów

Usługa przetwarzania obrazów umożliwia [wyodrębnianie tekstu za pomocą optycznego rozpoznawania znaków](concept-extracting-text-ocr.md) z obrazu do strumienia znaków czytelnych dla komputera. Jeśli to konieczne, funkcja optycznego rozpoznawania znaków poprawiania kąt obrotu (w stopniach) rozpoznanego tekstu wokół poziomej osi obrazu i podaje współrzędne ramki każdego wyrazu. Funkcja optycznego rozpoznawania znaków obsługuje 25 języków i automatycznie wykrywa język wyodrębnionego tekstu.

Można również [rozpoznawać tekst drukowany i pismo odręczne](concept-recognizing-text.md) na obrazie. Przetwarzanie obrazów potrafi wykrywać i wyodrębniać zarówno tekst drukowany, jak i pismo odręczne z obrazów różnych obiektów na różnych powierzchniach i tłach, takich jak rachunki, plakaty, wizytówki, listy i tablice. Obecnie rozpoznawanie tekstu drukowanego i odręcznego jest w wersji zapoznawczej i jedynym obsługiwanym językiem jest angielski.  

## <a name="moderating-content-in-images"></a>Moderowanie zawartości na obrazach

Przy użyciu usługi przetwarzania obrazów można [wykrywać zawartość przeznaczoną dla dorosłych](concept-detecting-adult-content.md) na obrazie, oceniać prawdopodobieństwo, że obraz zawiera treści dla dorosłych, a także generować współczynnik ufności. Filtr umożliwiający wykrywanie zawartości dla dorosłych można ustawiać na ruchomej skali, aby dostosować go do swoich preferencji.

## <a name="using-containers"></a>Korzystanie z kontenerów

Po zainstalowaniu standardowego kontenera platformy Docker blisko danych można lokalnie rozpoznawać tekst drukowany i pismo odręczne, korzystając z [kontenerów przetwarzania obrazów](computer-vision-how-to-install-containers.md).

## <a name="image-requirements"></a>Wymagania obrazu

Przetwarzanie obrazów umożliwia analizowanie obrazów, które spełniają następujące wymagania:

- Obraz musi być w formacie JPEG, PNG, GIF lub BMP
- Rozmiar pliku obrazu musi być mniejszy niż 4 megabajty (MB)
- Wymiary obrazu muszą być większe niż 50x50 pikseli  
  W przypadku funkcji optycznego rozpoznawania znaków wymiary obrazu muszą wynosić od 50x50 pikseli do 4200x4200 pikseli

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi przetwarzania obrazów powinni znać zasady firmy Microsoft dotyczące danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z usługą przetwarzania obrazów, korzystając z jednego z naszych przewodników:

- [Analizowanie obrazu](quickstarts-sdk/csharp-analyze-sdk.md)
- [Wyodrębnianie tekstu odręcznego](quickstarts-sdk/csharp-hand-text-sdk.md)
- [Generowanie miniatur](quickstarts-sdk/csharp-thumb-sdk.md)
