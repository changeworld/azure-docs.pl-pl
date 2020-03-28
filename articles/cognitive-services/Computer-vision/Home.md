---
title: Czym jest przetwarzanie obrazów? — Przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Usługa przetwarzania obrazów oferuje deweloperom dostęp do zaawansowanych algorytmów przetwarzania obrazów i zwracania informacji.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/27/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: af49e80766d7ac4bd2d9ed677a2571fb0a5a3189
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80131747"
---
# <a name="what-is-computer-vision"></a>Czym jest przetwarzanie obrazów?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Usługa przetwarzania obrazów przetwarzania w usłudze Azure zapewnia deweloperom dostęp do zaawansowanych algorytmów przetwarzanych obrazów i zwraca informacje, w zależności od interesujących Cię funkcji wizualnych. Na przykład wizja komputerowa może określić, czy obraz zawiera zawartość dla dorosłych, czy może znaleźć wszystkie ludzkie twarze na obrazie.

Funkcji Przetwarzania danych w aplikacji można używać za pośrednictwem natywnego sdk lub bezpośrednio przez wywołanie interfejsu API REST. Na tej stronie ogólnie omówiono, co można zrobić za pomocą przetwarzania obrazów.

## <a name="computer-vision-for-digital-asset-management"></a>Wizja komputerowa do zarządzania zasobami cyfrowymi

Usługa Computer Vision może zasilać wiele scenariuszy zarządzania zasobami cyfrowymi (DAM). DAM to proces biznesowy polegający na organizowaniu, przechowywaniu i pobieraniu zasobów multimedialnych oraz zarządzaniu prawami i uprawnieniami cyfrowymi. Na przykład firma może chcieć grupować i identyfikować obrazy na podstawie widocznych logo, twarzy, obiektów, kolorów itd. Możesz też automatycznie generować [podpisy obrazów](./Tutorials/storage-lab-tutorial.md) i dołączać słowa kluczowe, aby można je było przeszukiwać. Aby uzyskać wszechstronne rozwiązanie DAM przy użyciu usług Cognitive Services, usługi Azure Cognitive Search i inteligentnego raportowania, zobacz [Przewodnik akceleratora rozwiązań wyszukiwania wiedzy](https://github.com/Azure-Samples/azure-search-knowledge-mining) w usłudze GitHub. Aby uzyskać inne przykłady DAM, zobacz repozytorium [szablonów rozwiązań przetwarzania obrazów.](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates)

## <a name="analyze-images-for-insight"></a>Analizowanie obrazów w celu uzyskania szczegółowych informacji

Obrazy można analizować, aby wykrywać cechy wizualne i właściwości obrazów oraz dostarczać szczegółowe informacje na ich temat. Wszystkie funkcje w poniższej tabeli są udostępniane przez interfejs API [analizowania obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).

| Akcja | Opis |
| ------ | ----------- |
|**[Tagowanie funkcji wizualnych](concept-tagging-images.md)**|Identyfikowanie i tagowanie elementów wizualnych na obrazie w oparciu o zestaw tysięcy rozpoznawalnych obiektów, istot żywych, scenerii i akcji. Gdy tagi są niejednoznaczne lub nie powszechnie znane, odpowiedź interfejsu API zawiera wskazówki, aby wyjaśnić kontekst tagu. Tagowanie nie jest ograniczone do głównego tematu, na przykład do osoby na pierwszym planie, ale uwzględnia także otoczenie (wewnątrz lub na zewnątrz), meble, narzędzia, rośliny, zwierzęta, akcesoria, gadżety itd.|
|**[Wykrywanie obiektów](concept-object-detection.md)**| Wykrywanie obiektów jest podobne do tagowania, ale interfejs API zwraca współrzędne pola ograniczenia dla każdego zastosowanego tagu. Na przykład jeśli na obrazie znajduje się pies, kot i osoba, operacja wykrywania utworzy listę tych obiektów wraz z ich współrzędnymi na obrazie. Ta funkcja umożliwia przetwarzanie dalszych relacji między obiektami na obrazie. Ponadto w odpowiednich przypadkach informuje, że obraz zawiera wiele wystąpień tego samego tagu.|
|**[Wykrywanie marek](concept-brand-detection.md)**|Identyfikuj marki handlowe na obrazach lub filmach na podstawie bazy danych z tysiącami znaków logo z całego świata. Za pomocą tej funkcji można na przykład ustalać, które marki są najpopularniejsze w mediach społecznościowych lub najpowszechniej promowane za pomocą lokowania produktów w mediach.|
|**[Kategoryzowanie obrazu](concept-categorizing-images.md)**|Identyfikowanie i kategoryzowanie całego obrazu za pomocą [taksonomii kategorii](Category-Taxonomy.md) z użyciem dziedzicznych hierarchii obiektów nadrzędnych i podrzędnych. Kategorii można używać oddzielnie lub z naszymi nowymi modelami tagowania.<br/>Obecnie jedynym obsługiwanym językiem tagowania i kategoryzowania obrazów jest angielski.|
|**[Opisywanie obrazu](concept-describing-images.md)**|Generowanie opisu całego obrazu w języku zrozumiałym dla człowieka przy użyciu pełnych zdań. Algorytmy przetwarzania obrazów generują różne opisy na podstawie obiektów zidentyfikowanych na obrazie. Poszczególne opisy są oceniane, po czym generowany jest współczynnik ufności. Następnie zwracana jest lista uporządkowana od najwyższego do najniższego współczynnika ufności.|
|**[Wykrywanie twarzy](concept-detecting-faces.md)** |Wykrywanie twarzy na obrazie i dostarczanie informacji o każdej wykrytej twarzy. Usługa przetwarzania obrazów zwraca współrzędne, prostokąt, płeć i wiek osoby dla każdej wykrytej twarzy.<br/>Funkcja Przetwarzania Obrazów komputerowych zapewnia podzbiór funkcji usługi [Twarz.](/azure/cognitive-services/face/) Za pomocą usługi Twarz można korzystać w celu bardziej szczegółowej analizy, takiej jak identyfikacja twarzy i wykrywanie położenia.|
|**[Wykrywanie typów obrazu](concept-detecting-image-types.md)**|Wykrywanie właściwości obrazu, na przykład czy obraz jest rysunkiem lub prawdopodobieństwa, że obraz jest obiektem clipart.|
|**[Wykrywanie zawartości specyficznej dla domeny](concept-detecting-domain-content.md)**|Wykrywanie i identyfikowanie zawartości obrazu specyficznej dla domeny, takiej jak osobistości i charakterystyczne elementy krajobrazu, przy użyciu modeli domeny. Jeśli na przykład obraz zawiera osoby, funkcja Wizja komputerowa może użyć modelu domeny dla gwiazd, aby ustalić, czy osoby wykryte na obrazie są znanymi gwiazdami.|
|**[Wykrywanie schematu kolorów](concept-detecting-color-schemes.md)**|Analizowanie użycia kolorów na obrazie. Przetwarzanie obrazów może określić, czy obraz jest czarno-biały, czy kolorowy, a w przypadku obrazów kolorowych zidentyfikować kolory dominujące i kolory akcentu.|
|**[Generowanie miniatur](concept-generating-thumbnails.md)**|Analizowanie zawartości obrazu w celu wygenerowania dla niego odpowiedniej miniatury. Usługa przetwarzania obrazów najpierw generuje wysokiej jakości miniaturę, po czym analizuje obiekty na obrazie, aby określić *obszar zainteresowania*. Następnie przetwarzanie obrazów przycina obraz zgodnie z wymaganiami obszaru zainteresowania. W zależności od potrzeb wygenerowana miniatura może mieć współczynnik proporcji inny od współczynnika proporcji oryginalnego obrazu.|
|**[Określanie obszaru zainteresowania](concept-generating-thumbnails.md#area-of-interest)**|Przeanalizuj zawartość obrazu w celu zwrócenia współrzędnych *obszaru zainteresowania*. Zamiast przycinać obraz i generować miniaturę, funkcja Wizja komputerowa zwraca współrzędne obwiedni regionu, dzięki czemu aplikacja wywołująca może zmodyfikować oryginalny obraz zgodnie z potrzebami.|

## <a name="extract-text-from-images"></a>Wyodrębnianie tekstu z obrazów

Za pomocą interfejsu COMPUTER Vision [Read](concept-recognizing-text.md#read-api) API można wyodrębnić drukowany i odręczny tekst z obrazów do strumienia znaków czytelnego maszynowo. Interfejs API odczytu korzysta z naszych najnowszych modeli i współpracuje z tekstem na różnych powierzchniach i tłach, takich jak paragony, plakaty, wizytówki, listy i tablice. Obecnie język angielski i hiszpański są jedynymi obsługiwanymi językami.

Można również użyć interfejsu API [optycznego rozpoznawania znaków (OCR)](concept-recognizing-text.md#ocr-optical-character-recognition-api) do wyodrębniania drukowanego tekstu w kilku językach. Jeśli to konieczne, funkcja optycznego rozpoznawania znaków poprawia kąt obrotu i podaje współrzędne ramki każdego wyrazu. Funkcja optycznego rozpoznawania znaków obsługuje 25 języków i automatycznie wykrywa język rozpoznanego tekstu.

## <a name="moderate-content-in-images"></a>Moderowanie zawartości na obrazach

Za pomocą funkcji Wizja komputerowa można [wykrywać zawartość dla dorosłych](concept-detecting-adult-content.md) w obrazie i zwracać wyniki zaufania dla różnych klasyfikacji. Próg oznaczania zawartości można ustawić na skali przesuwnej, aby dostosować ją do preferencji.

## <a name="use-containers"></a>Korzystanie z kontenerów

[Kontenery przetwarzania obrazów za pomocą funkcji przetwarzania obrazów można](computer-vision-how-to-install-containers.md) rozpoznawać tekst drukowany i odręczny lokalnie, instalując standardowy kontener platformy Docker bliżej danych.

## <a name="image-requirements"></a>Wymagania obrazu

Przetwarzanie obrazów umożliwia analizowanie obrazów, które spełniają następujące wymagania:

- Obraz musi być w formacie JPEG, PNG, GIF lub BMP
- Rozmiar pliku obrazu musi być mniejszy niż 4 megabajty (MB)
- Wymiary obrazu muszą być większe niż 50x50 pikseli
  - W przypadku interfejsu API odczytu wymiary obrazu muszą wynosić od 50 x 50 do 10000 x 10000 pikseli.

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi przetwarzania obrazów powinni znać zasady firmy Microsoft dotyczące danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z przetwarzaniem obrazów, korzystając z przewodnika Szybki start:

- [Szybki start: biblioteka klienta programu .NET z wizją komputera](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
- [Szybki start: biblioteka klienta Computer Vision Python](./quickstarts-sdk/client-library.md?pivots=programming-language-python)
- [Szybki start: biblioteka klienta Java usługi Computer Vision](./quickstarts-sdk/client-library.md?pivots=programming-language-java)
