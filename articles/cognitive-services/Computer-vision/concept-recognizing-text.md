---
title: Drukowane, odręczne rozpoznawanie tekstu - Wizja komputerowa
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z rozpoznawaniem drukowanego i odręcznego tekstu w obrazach za pomocą interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a4c90ed12c8023e0b9ebc509b20d8d9224b49f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220161"
---
# <a name="recognize-printed-and-handwritten-text"></a>Rozpoznawanie tekstu drukowanego i pisma odręcznego

Usługa Computer Vision udostępnia szereg usług, które wykrywają i wyodrębniają drukowany lub odręczny tekst wyświetlany na obrazach. Jest to przydatne w różnych scenariuszach, takich jak notatek, dokumentacji medycznej, zabezpieczeń i bankowości. W poniższych trzech sekcjach szczegółowo trzy różne interfejsy API rozpoznawania tekstu, każdy zoptymalizowany pod kątem różnych przypadków użycia.

## <a name="read-api"></a>Odczyt interfejsu API

Interfejs API odczytu wykrywa zawartość tekstową obrazu przy użyciu naszych najnowszych modeli rozpoznawania i konwertuje zidentyfikowany tekst na strumień znaków do odczytu maszynowego. Jest zoptymalizowany pod kątem obrazów o dużej ilości tekstu (takich jak dokumenty, które zostały zeskanowane cyfrowo) i obrazów z dużą ilością szumów wizualnych. Określi, który model rozpoznawania ma być używany dla każdego wiersza tekstu, obsługując obrazy zarówno drukowanym, jak i odręcznym tekstem. Interfejs API odczytu wykonuje asynchronicznie, ponieważ większe dokumenty może potrwać kilka minut, aby zwrócić wynik.

Operacja Odczyt zachowuje oryginalne grupowanie wierszy rozpoznanych wyrazów w swoich danych wyjściowych. Każda linia ma współrzędne obwiedni, a każde słowo w wierszu ma również własne współrzędne. Jeśli słowo zostało rozpoznane z niskim zaufaniem, informacje te są również przekazywane. Aby dowiedzieć się więcej, zobacz [dokumenty referencyjne odczytu interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) i dokumenty [referencyjne podglądu interfejsu API odczytu.](https://go.microsoft.com/fwlink/?linkid=2118322)

> [!NOTE]
> Ta funkcja jest dostępna tylko w języku angielskim i hiszpańskim (wersja zapoznawcza).

### <a name="image-requirements"></a>Wymagania obrazu

Interfejs API odczytu działa z obrazami spełniaymi następujące wymagania:

- Obraz musi być przedstawiony w formacie JPEG, PNG, BMP, PDF lub TIFF.
- Wymiary obrazu muszą wynosić od 50 x 50 do 10000 x 10000 pikseli. Strony PDF muszą mieć rozmiar 17 x 17 cali lub mniej.
- Rozmiar pliku obrazu musi być mniejszy niż 20 megabajtów (MB).

### <a name="limitations"></a>Ograniczenia

Jeśli korzystasz z subskrypcji bezpłatnej warstwy, interfejs API odczytu będzie przetwarzać tylko dwie pierwsze strony dokumentu PDF lub TIFF. Dzięki płatnej subskrypcji przetworzy do 200 stron. Należy również zauważyć, że interfejs API wykryje maksymalnie 300 wierszy na stronie.

## <a name="ocr-optical-character-recognition-api"></a>INTERFEJS API OCR (optyczne rozpoznawanie znaków)

Interfejs API optycznego rozpoznawania znaków (OCR) programu Computer Vision jest podobny do interfejsu API odczytu, ale jest wykonywany synchronicznie i nie jest zoptymalizowany pod kątem dużych dokumentów. Używa wcześniejszego modelu rozpoznawania, ale działa z większą liczniejszymi językami; Zobacz [Obsługa języka](language-support.md#text-recognition) dla pełnej listy obsługiwanych języków.

W razie potrzeby funkcja OCR koryguje obrót rozpoznanego tekstu, zwracając przesunięcie obrotowe w stopniach względem poziomej osi obrazu. OCR udostępnia również współrzędne ramek każdego wyrazu, jak pokazano na poniższej ilustracji.

![Obrócony obraz i jego tekst jest odczytywany i wytyczony](./Images/vision-overview-ocr.png)

Więcej informacji można znaleźć w [dokumentów referencyjnych OCR.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)

### <a name="image-requirements"></a>Wymagania obrazu

Interfejs API OCR działa na obrazach spełnia ych następujące wymagania:

* Obraz musi być przedstawiony w formacie JPEG, PNG, GIF lub BMP.
* Rozmiar obrazu wejściowego musi wynosić od 50 x 50 do 4200 x 4200 pikseli.
* Tekst na obrazie można obracać dowolną wielokrotnością 90 stopni plus mały kąt do 40 stopni.

### <a name="limitations"></a>Ograniczenia

Na zdjęciach, na których tekst jest dominujący, fałszywe alarmy mogą pochodzić z częściowo rozpoznanych słów. Na niektórych zdjęciach, zwłaszcza zdjęciach bez tekstu, precyzja może się różnić w zależności od typu obrazu.

## <a name="recognize-text-api"></a>Rozpoznawanie interfejsu API tekstu

> [!NOTE]
> Interfejs API rozpoznawania tekstu jest przestarzały na rzecz interfejsu API odczytu. Interfejs API odczytu ma podobne możliwości i jest aktualizowany do obsługi plików PDF, TIFF i wielostronicowych.

Interfejs API rozpoznawania tekstu jest podobny do rozpoznawania środowiska, ale wykonuje asynchronicznie i używa zaktualizowanych modeli rozpoznawania. Aby dowiedzieć się więcej, zobacz [dokumenty odwołania do interfejsu API rozpoznawania tekstu.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)

### <a name="image-requirements"></a>Wymagania obrazu

Interfejs API rozpoznawania tekstu działa z obrazami spełniaymi następujące wymagania:

- Obraz musi być przedstawiony w formacie JPEG, PNG lub BMP.
- Wymiary obrazu muszą wynosić od 50 x 50 do 4200 x 4200 pikseli.
- Rozmiar pliku obrazu musi być mniejszy niż 4 megabajty (MB).

## <a name="limitations"></a>Ograniczenia

Dokładność operacji rozpoznawania tekstu zależy od jakości obrazów. Następujące czynniki mogą powodować niedokładny odczyt:

* Rozmyte obrazy.
* Tekst odręczny lub napisany kursywą.
* Artystyczne style czcionki.
* Mały rozmiar tekstu.
* Złożone tła, cienie, oślepiające światło nad tekstem lub zniekształcenie perspektywy.
* Przewymiarowane lub brakujące wielkie litery na początku słów.
* Indeks dolny, indeks górny lub tekst przekreślony.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie [z wyodrębniaj tekst (Przeczytaj)](./QuickStarts/CSharp-hand-text.md) Szybki start, aby zaimplementować rozpoznawanie tekstu w prostej aplikacji Języka C#.
