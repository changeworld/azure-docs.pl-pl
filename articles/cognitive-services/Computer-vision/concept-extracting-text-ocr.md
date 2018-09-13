---
title: Możliwości wyodrębniania tekstu za pomocą oprogramowania OCR
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Pojęcia związane z możliwości wyodrębniania tekstu za pomocą funkcji optycznego rozpoznawania znaków przy użyciu przetwarzania obrazów w usługach Azure Cognitive Services.
services: cognitive-services
author: deken
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 07efe49c53b13094f3f67db3b3becb57a7c473d8
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44725545"
---
# <a name="extracting-text-with-ocr"></a>Możliwości wyodrębniania tekstu za pomocą oprogramowania OCR

Technologia optycznego rozpoznawania znaków (OCR) w wizualizacji komputerowej wykrywa zawartość tekstową na obrazie i wyodrębnia zidentyfikowany tekst do strumienia znaków czytelnych. Wynik służy do wyszukiwania i wiele innych celów, takich jak dokumentacji medycznej, zabezpieczeniami i bankowością. Funkcja automatycznie wykrywa język. Optyczne rozpoznawanie znaków oszczędza czas i zapewnia wygodę dla użytkowników, umożliwiając im wykonywanie zdjęć tekstu zamiast przepisywania tekstu.

Optyczne rozpoznawanie znaków obsługuje 25 językach. Te języki są: arabski, chiński uproszczony, chiński tradycyjny, czeski, duński, holenderski, angielski, fiński, francuski, niemiecki, grecki, węgierski, włoski, japoński, koreański, norweski, Polski, portugalski, rumuński, rosyjski, serbski (cyrylica i łaciński) Słowacki, hiszpański, szwedzki i turecki.

Jeśli to konieczne, optyczne rozpoznawanie znaków poprawia obrót rozpoznany w stopniach wokół osi obraz w poziomie. Optyczne rozpoznawanie znaków zawiera współrzędne ramki wystąpień poszczególnych wyrazów, jak pokazano na poniższej ilustracji.

![Optyczne rozpoznawanie znaków — omówienie](./Images/vision-overview-ocr.png)

## <a name="ocr-requirements"></a>Wymagania dotyczące optyczne rozpoznawanie znaków

Przetwarzanie obrazów można wyodrębnić tekst za pomocą optycznego rozpoznawania znaków z obrazów, które spełniają następujące wymagania:

* Obraz musi być w formacie JPEG, PNG, GIF lub BMP
* Rozmiar obrazu wejściowego musi należeć do zakresu od 40 x 40 i 3200 x 3200 pikseli
* Obraz nie może być większy niż 10 megapikseli

Obrazu wejściowego można obracać o dowolnym wielu 90 stopni oraz małych kąt maksymalnie 40 stopni.

## <a name="improving-ocr-accuracy"></a>Poprawianie dokładności optyczne rozpoznawanie znaków

Dokładność rozpoznawanie tekstu, zależy od jakości obrazu. Odczyt nieprawidłowych może być spowodowane w następujących sytuacjach:

* Obrazy rozmyte.
* Tekstu odręcznego lub kursywnych.
* Style czcionki sztuki.
* Rozmiar małego tekstu.
* Złożone tła, cieni lub odbić za pośrednictwem zakłócenia tekstu lub perspektywę.
* Zbyt duży lub brak wielkie litery na początku słowa
* Indeks dolny, indeks górny lub przekreślony tekst.

### <a name="ocr-limitations"></a>Ograniczenia optyczne rozpoznawanie znaków

Na fotografii, gdzie jest tekst liczby wyników fałszywie dodatnich mogą pochodzić z częściowo rozpoznane słowa. W niektórych fotografii, szczególnie zdjęcia bez dowolny tekst dokładności mogą się różnić w znacznie w zależności od typu obraz.

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [rozpoznawanie tekstu drukowanego i pisma odręcznego](concept-recognizing-text.md).