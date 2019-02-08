---
title: Możliwości wyodrębniania tekstu za pomocą oprogramowania OCR - przetwarzania obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z możliwości wyodrębniania tekstu za pomocą optycznego rozpoznawania znaków (OCR) przy użyciu interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 0f43724218994818908e87834ed1b70f4bca330b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873806"
---
# <a name="extracting-text-with-optical-character-recognition"></a>Trwa wyodrębnianie tekstu za pomocą funkcji optycznego rozpoznawania znaków

Technologia optycznego rozpoznawania znaków (OCR) w wizualizacji komputerowej wykrywa zawartość tekstową na obrazie i wyodrębnia zidentyfikowany tekst do strumienia znaków czytelnych. Wynik służy do wyszukiwania oraz wielu innych celów, ma np. zastosowanie w przypadku dokumentacji medycznej, branży ochrony i bankowości. Umożliwia automatyczne wykrywanie języka. Optyczne rozpoznawanie znaków oszczędza czas i jest wygodne dla użytkowników, którzy mogą wykonywać zdjęcia tekstu zamiast go przepisywać.

OCR obsługuje 25 języków. Te języki to: arabski, chiński uproszczony, chiński tradycyjny, czeski, duński, holenderski, angielski, fiński, francuski, niemiecki, grecki, węgierski, włoski, japoński, koreański, norweski, polski, portugalski, rumuński, rosyjski, serbski (cyrylica i alfabet łaciński), słowacki, hiszpański, szwedzki i turecki.

Jeśli to konieczne, OCR poprawia kąt obrotu (w stopniach) rozpoznanego tekstu wokół poziomej osi obrazu. Optyczne rozpoznawanie znaków zawiera współrzędne ramki wystąpień poszczególnych wyrazów, jak pokazano na poniższej ilustracji.

![Diagram przedstawiający za obrazu oraz jego tekstu, odczytu, a umieszczony](./Images/vision-overview-ocr.png)

## <a name="ocr-requirements"></a>Wymagania dotyczące optyczne rozpoznawanie znaków

Przetwarzanie obrazów można wyodrębnić tekst za pomocą optycznego rozpoznawania znaków z obrazów, które spełniają następujące wymagania:

* Obraz musi być w formacie JPEG, PNG, GIF lub BMP
* Rozmiar obrazu wejściowego musi należeć do zakresu od 50 x 50 i 4200 x 4200 pikseli


Obrazu wejściowego można obracać o dowolnym wielu 90 stopni oraz małych kąt maksymalnie 40 stopni.

## <a name="improving-ocr-accuracy"></a>Poprawianie dokładności optyczne rozpoznawanie znaków

Dokładność rozpoznawania tekstu zależy od jakości obrazu. Przyczyny nieprawidłowego odczytu mogą być następujące:

* Rozmyte obrazy.
* Tekst odręczny lub napisany kursywą.
* Artystyczne style czcionki.
* Mały rozmiar tekstu.
* Złożone tła, cienie, oślepiające światło nad tekstem lub zniekształcenie perspektywy.
* Zbyt duże wielkie litery na początku wyrazów lub ich brak.
* Indeks dolny, indeks górny lub tekst przekreślony.

### <a name="ocr-limitations"></a>Ograniczenia optyczne rozpoznawanie znaków

Na fotografii, gdzie jest tekst liczby wyników fałszywie dodatnich mogą pochodzić z częściowo rozpoznane słowa. W niektórych fotografii, szczególnie zdjęcia bez dowolny tekst dokładności mogą się różnić w znacznie w zależności od typu obraz.

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [rozpoznawanie tekstu drukowanego i pisma odręcznego](concept-recognizing-text.md).
