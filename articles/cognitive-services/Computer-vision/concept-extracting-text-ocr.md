---
title: Możliwości wyodrębniania tekstu za pomocą oprogramowania OCR - przetwarzania obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z możliwości wyodrębniania tekstu za pomocą optycznego rozpoznawania znaków (OCR) przy użyciu interfejsu API przetwarzania obrazów.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 4cbc8ebcdd19aa65a37ffe0f82019a7681f1c6c5
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434878"
---
# <a name="extracting-text-with-ocr"></a>Wyodrębnianie tekstu za pomocą technologii optycznego rozpoznawania znaków

Technologia optycznego rozpoznawania znaków (OCR) w wizualizacji komputerowej wykrywa zawartość tekstową na obrazie i wyodrębnia zidentyfikowany tekst do strumienia znaków czytelnych. Wynik służy do wyszukiwania i wiele innych celów, takich jak dokumentacji medycznej, zabezpieczeniami i bankowością. Funkcja automatycznie wykrywa język. Optyczne rozpoznawanie znaków oszczędza czas i zapewnia wygodę dla użytkowników, umożliwiając im wykonywanie zdjęć tekstu zamiast przepisywania tekstu.

Optyczne rozpoznawanie znaków obsługuje 25 językach. Te języki są: arabski, chiński uproszczony, chiński tradycyjny, czeski, duński, holenderski, angielski, fiński, francuski, niemiecki, grecki, węgierski, włoski, japoński, koreański, norweski, Polski, portugalski, rumuński, rosyjski, serbski (cyrylica i łaciński) Słowacki, hiszpański, szwedzki i turecki.

Jeśli to konieczne, optyczne rozpoznawanie znaków poprawia obrót rozpoznany w stopniach wokół osi obraz w poziomie. Optyczne rozpoznawanie znaków zawiera współrzędne ramki wystąpień poszczególnych wyrazów, jak pokazano na poniższej ilustracji.

![Optyczne rozpoznawanie znaków — omówienie](./Images/vision-overview-ocr.png)

## <a name="ocr-requirements"></a>Wymagania dotyczące optyczne rozpoznawanie znaków

Przetwarzanie obrazów można wyodrębnić tekst za pomocą optycznego rozpoznawania znaków z obrazów, które spełniają następujące wymagania:

* Obraz musi być w formacie JPEG, PNG, GIF lub BMP
* Rozmiar obrazu wejściowego musi należeć do zakresu od 50 x 50 i 4200 x 4200 pikseli
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
