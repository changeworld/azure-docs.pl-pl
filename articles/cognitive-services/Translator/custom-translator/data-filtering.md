---
title: Filtrowanie danych — tłumacz niestandardowy
titleSuffix: Azure Cognitive Services
description: Podczas przesyłania dokumentów, które mają być używane do szkolenia systemu niestandardowego, dokumenty przechodzą szereg kroków przetwarzania i filtrowania w celu przygotowania do szkolenia.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 1028443eaaf6c483cd7cd57289b0dcf2a9f11902
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595908"
---
# <a name="data-filtering"></a>Filtrowanie danych

Podczas przesyłania dokumentów, które mają być używane do szkolenia systemu niestandardowego, dokumenty przechodzą szereg kroków przetwarzania i filtrowania w celu przygotowania do szkolenia. Te kroki są wyjaśnione tutaj. Znajomość filtrowania może pomóc w zrozumieniu liczby zdań wyświetlanych w niestandardowym tłumaczu, a także kroki, które możesz wykonać samodzielnie, aby przygotować dokumenty do szkolenia z translatorem niestandardowym.

## <a name="sentence-alignment"></a>Wyrównanie zdań
Jeśli dokumentu nie ma formatu XLIFF, TMX lub ALIGN, tłumacz niestandardowy wyrównuje zdania źródłowych i docelowych dokumentów do siebie nawzajem, zdanie według zdania. Translator nie wykonuje wyrównania dokumentu — następuje nazewnictwo dokumentów w celu znalezienia pasującego dokumentu innego języka. W dokumencie translator niestandardowy próbuje znaleźć odpowiednie zdanie w innym języku. Używa znaczników dokumentu, takich jak osadzone znaczniki HTML, aby ułatwić wyrównanie.  

Jeśli widzisz dużą rozbieżność między liczbą zdań w źródłowych i docelowych dokumentach bocznych, dokument może nie być równoległy w pierwszej kolejności lub z innych powodów nie można wyrównać. Para dokumentów z dużą różnicą (>10%) zdania z każdej strony wymagają drugiego spojrzenia, aby upewnić się, że są one rzeczywiście równoległe. Translator niestandardowy wyświetla ostrzeżenie obok dokumentu, jeśli liczba zdań różni się podejrzanie.  


## <a name="deduplication"></a>Deduplikacja
Translator niestandardowy usuwa zdania, które są obecne w testach i dostrajania dokumentów z danych szkoleniowych.Usuwanie odbywa się dynamicznie wewnątrz przebiegu szkolenia, a nie w kroku przetwarzania danych. Translator niestandardowy zgłasza liczbę zdań w przeglądzie projektu przed takim usunięciem.  

## <a name="length-filter"></a>Długość filtra
* Usuń zdania z tylko jednym słowem po obu stronach.
* Usuń zdania z ponad 100 słowami po obu stronach.Chińskie, japońskie, koreańskie są zwolnione z podatku.
* Usuń zdania z mniej niż 3 znakami. Chińskie, japońskie, koreańskie są zwolnione z podatku.
* Usuń zdania z ponad 2000 znaków dla języka chińskiego, japońskiego, koreańskiego.
* Usuń zdania z mniej niż 1% znaków alfa.
* Usuń wpisy słownika zawierające więcej niż 50 słów.

## <a name="white-space"></a>Odstępu
* Zastąp dowolną sekwencję znaków odstępu, w tym tabulatory i sekwencje CR/LF pojedynczym znakiem spacji.
* Usuwanie miejsca początkowego lub końcowego w zdaniu

## <a name="sentence-end-punctuation"></a>Znaki interpunkcyjne końca zdania
Zastąp wiele znaków interpunkcyjnych końca zdania pojedynczym wystąpieniem.  

## <a name="japanese-character-normalization"></a>Normalizacja znaków japońskich
Konwertuj litery i cyfry o pełnej szerokości na znaki o połówkowej szerokości.

## <a name="unescaped-xml-tags"></a>Nieuprawniane znaczniki XML
Filtrowanie przekształca znaczniki bez zmiany w znaczniki wysuniętych:
* `&lt;`Staje się`&amp;lt;`
* `&gt;`Staje się`&amp;gt;`
* `&amp;`Staje się`&amp;amp;`

## <a name="invalid-characters"></a>Nieprawidłowe znaki
Translator niestandardowy usuwa zdania zawierające znak Unicode U+FFFD. Znak U + FFFD wskazuje konwersji kodowania nie powiodło się.

## <a name="next-steps"></a>Następne kroki

- [Trenuj model](how-to-train-model.md) w tłumaczu niestandardowym.
