---
title: Trwa wyodrębnianie tekstu za pomocą optycznego rozpoznawania znaków (OCR) - przetwarzania obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z możliwości wyodrębniania tekstu za pomocą optycznego rozpoznawania znaków (OCR) przy użyciu interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: deb73eb9fdd6879a5fbe1fed820bf92b2d627b65
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310444"
---
# <a name="extract-text-with-optical-character-recognition"></a>Wyodrębnij tekst za pomocą funkcji optycznego rozpoznawania znaków

Przetwarzanie obrazów, funkcja optycznego rozpoznawania znaków (OCR) wykrywa zawartość tekstową na obrazie i konwertuje zidentyfikowany tekst do strumienia znaków czytelnych. Wynik może być użyty do wielu celów, takich jak wyszukiwanie, dokumentacji medycznej, zabezpieczeń i bankowością. 

Optyczne rozpoznawanie znaków obsługuje 25 językach: arabski, chiński uproszczony, chiński tradycyjny, czeski, duński, holenderski, angielski, fiński, francuski, niemiecki, grecki, węgierski, włoski, japoński, koreański, norweski, polski, portugalski, rumuński, rosyjski, serbski (cyrylica i alfabet łaciński), słowacki, hiszpański, szwedzki i turecki. Optyczne rozpoznawanie znaków automatycznie wykrywa język tekstu wykryte.

Jeśli to konieczne, optyczne rozpoznawanie znaków poprawia obrót rozpoznany, zwracając obrotowych przesunięcie w stopniach osi obraz w poziomie. Optyczne rozpoznawanie znaków są także współrzędne ramki wystąpień poszczególnych wyrazów, jak pokazano na poniższej ilustracji.

![Diagram przedstawiający za obrazu oraz jego tekstu, odczytu, a umieszczony](./Images/vision-overview-ocr.png)

## <a name="image-requirements"></a>Wymagania obrazu

Przetwarzanie obrazów można wyodrębnić tekst za pomocą optycznego rozpoznawania znaków z obrazów, które spełniają następujące wymagania:

* Obraz musi być w formacie JPEG, PNG, GIF lub BMP
* Rozmiar obrazu wejściowego musi należeć do zakresu od 50 x 50 i 4200 x 4200 pikseli
* Tekst w obrazie można obracać o dowolnym wielu 90 stopni oraz małych kąt maksymalnie 40 stopni.

## <a name="improving-ocr-accuracy"></a>Poprawianie dokładności optyczne rozpoznawanie znaków

Dokładność rozpoznawania tekstu zależy od jakości obrazu. Odczyt nieprawidłowych może być spowodowane przez następujące czynności:

* Rozmyte obrazy.
* Tekst odręczny lub napisany kursywą.
* Artystyczne style czcionki.
* Mały rozmiar tekstu.
* Złożone tła, cienie, oślepiające światło nad tekstem lub zniekształcenie perspektywy.
* Zbyt duże wielkie litery na początku wyrazów lub ich brak.
* Indeks dolny, indeks górny lub tekst przekreślony.

### <a name="ocr-limitations"></a>Ograniczenia optyczne rozpoznawanie znaków

W obrazach, których tekst jest dominującym wyników fałszywie dodatnich mogą pochodzić z częściowo rozpoznane słowa. W niektórych obrazów, szczególnie zdjęcia bez dowolny tekst dokładności mogą się różnić w znacznie w zależności od typu obraz.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [dokumentację referencyjną optyczne rozpoznawanie znaków](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) Aby dowiedzieć się więcej.
