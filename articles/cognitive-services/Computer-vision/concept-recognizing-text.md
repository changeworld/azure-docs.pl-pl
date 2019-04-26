---
title: Rozpoznawanie drukowane odręcznego tekstu przetwarzania obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z rozpoznawanie tekstu drukowanego i pisma odręcznego na obrazach za pomocą interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f7fd13b0b6df0b07543216e3c612520e528c1176
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368025"
---
# <a name="recognize-printed-and-handwritten-text"></a>Rozpoznawanie tekstu drukowanego i pisma odręcznego

Przetwarzania obrazów udostępnia szereg usług, które wykrywanie i wyodrębnianie tekstu drukowanego na klawiaturze lub odręcznie, który pojawia się w obrazach. Jest to przydatne w różnych scenariuszy, takich jak notatek, dokumentacji medycznej, zabezpieczeń i bankowością. Następujące trzy sekcje szczegółów trzech różnych rozpoznawanie tekstu interfejsów API, każdy zoptymalizowane pod kątem różnych przypadków użycia.

## <a name="read-api"></a>Odczyt interfejsu API

Interfejs API odczytu wykrywa zawartość tekstową na obrazie za pomocą naszych najnowszych modeli rozpoznawania i konwertuje zidentyfikowany tekst do strumienia znaków czytelnych. Jest zoptymalizowany dla obrazów zawierające dużo tekstu (takich jak dokumenty, które zostały przeskanowane cyfrowo) i obrazów przy użyciu wiele szumu visual. Asynchronicznie wykonuje ponieważ większe dokumenty może potrwać kilka minut, aby zwrócić wynik.

Operacja odczytu zachowuje oryginalne grupowanie wiersza rozpoznane słowa w danych wyjściowych. Każdy wiersz zawiera współrzędne pola ograniczenia, a każdy wyraz w wierszu ma również swój własny współrzędnych. Jeśli wyraz został rozpoznany bez obaw niski, te informacje zamieszczono również. Zobacz [dokumentację referencyjną interfejsu API odczytu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) Aby dowiedzieć się więcej.

> [!NOTE]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej i jest dostępna tylko dla tekstu w języku angielskim.

### <a name="image-requirements"></a>Wymagania obrazu

Interfejs API odczytu współpracuje z obrazów, które spełniają następujące wymagania:

- Obraz, który przedstawia w formacie JPEG, PNG, BMP, plików PDF lub TIFF.
- Wymiary obrazu musi mieć od 50 x 50 i 4200 x 4200 pikseli. Strony PDF muszą być 17 x 17 cali lub mniejsze.
- Rozmiar pliku obrazu musi być mniejsza niż 20 megabajtów (MB).

### <a name="limitations"></a>Ograniczenia

Jeśli używasz subskrypcji warstwy bezpłatna API odczytu będzie przetwarzać tylko pierwszych dwóch stron dokumentu PDF lub TIFF. Z subskrypcją płatną przetworzy maksymalnie 200 stron. Należy również zauważyć, że interfejs API wykryje maksymalnie 300 wierszy na stronie.

## <a name="ocr-optical-character-recognition-api"></a>Optyczne rozpoznawanie znaków (OCR) interfejsu API

Interfejs API optyczne rozpoznawanie znaków (OCR) przetwarzania obrazów jest podobny do interfejsu API odczytu, ale jest wykonywana synchronicznie i nie jest zoptymalizowany pod kątem dużych dokumentów. Używa starszej model uznawania osiągnięć, ale działa z innych języków; zobacz [języki](language-support.md#text-recognition) pełną listę obsługiwanych języków.

Jeśli to konieczne, optyczne rozpoznawanie znaków poprawia obrót rozpoznany, zwracając obrotowych przesunięcie w stopniach osi obraz w poziomie. Optyczne rozpoznawanie znaków także współrzędne ramki wystąpień poszczególnych wyrazów, jak pokazano na poniższej ilustracji.

![Obraz jest obracana jego tekstu jest odczytywać i umieszczony](./Images/vision-overview-ocr.png)

Zobacz [dokumenty referencyjne optyczne rozpoznawanie znaków](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) Aby dowiedzieć się więcej.

### <a name="image-requirements"></a>Wymagania obrazu

Interfejs API Rozpoznawania działa na obrazach, które spełniają następujące wymagania:

* Obraz, który przedstawia w formacie JPEG, PNG, GIF lub BMP.
* Rozmiar obrazu wejściowego musi składać się 50 x 50 i 4200 x 4200 pikseli.
* Tekst w obrazie można obracać o dowolnym wielu 90 stopni oraz małych kąt maksymalnie 40 stopni.

### <a name="limitations"></a>Ograniczenia

Na fotografii, gdzie jest tekst liczby wyników fałszywie dodatnich mogą pochodzić z częściowo rozpoznane słowa. W niektórych fotografii, szczególnie zdjęcia bez dowolny tekst dokładności mogą się różnić w zależności od typu obraz.

## <a name="recognize-text-api"></a>Rozpoznaje interfejs API tłumaczenia tekstu

> [!NOTE]
> Interfejs API tekstu rozpoznaje zostaje zastąpiona API odczytu. Interfejs API odczytu ma podobne funkcje i jest aktualizowana w celu obsługi plików PDF, TIFF i pliki stronicowania wielu.

Interfejs API rozpoznaje tekstu jest podobny do optyczne rozpoznawanie znaków, ale działa w sposób asynchroniczny oraz korzysta z modeli zaktualizowaną rozpoznawania. Zobacz [dokumentacja interfejsu API tłumaczenia tekstu rozpoznaje](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) Aby dowiedzieć się więcej.

### <a name="image-requirements"></a>Wymagania obrazu

Działa rozpoznawanie interfejsu API tłumaczenia tekstu przy użyciu obrazów, które spełniają następujące wymagania:

- Obraz, który przedstawia w formacie JPEG, PNG lub BMP.
- Wymiary obrazu musi mieć od 50 x 50 i 4200 x 4200 pikseli.
- Rozmiar pliku obrazu musi być mniejszy niż 4 megabajty (MB).

## <a name="improve-results"></a>Ulepsz wyniki

Dokładność operacje rozpoznawanie tekstu, zależy od jakości obrazów. Następujące czynniki mogą spowodować niedokładne odczytu:

* Rozmyte obrazy.
* Tekst odręczny lub napisany kursywą.
* Artystyczne style czcionki.
* Mały rozmiar tekstu.
* Złożone tła, cienie, oślepiające światło nad tekstem lub zniekształcenie perspektywy.
* Zbyt duży lub brak wielkie litery na początku słów.
* Indeks dolny, indeks górny lub tekst przekreślony.

## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z [wyodrębnianie tekstu drukowanego (OCR)](./quickstarts/csharp-print-text.md) Szybki Start, aby zaimplementować rozpoznawanie tekstu w prostej C# aplikacji.
