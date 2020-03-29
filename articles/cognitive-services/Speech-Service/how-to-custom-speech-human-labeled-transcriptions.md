---
title: Wytyczne dotyczące transkrypcji oznaczone etykietą ludzką — usługa mowy
titleSuffix: Azure Cognitive Services
description: Aby poprawić dokładność rozpoznawania mowy, na przykład gdy wyrazy są usuwane lub niepoprawnie podstawione, można użyć transkrypcji oznaczonych przez człowieka wraz z danymi audio. Transkrypcje oznaczone przez człowieka są słownymi, dosłownymi transkrypcjami pliku audio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 81b4ffc8f77673e52bb78f891e3de618b67e0d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806066"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Jak tworzyć transkrypcje oznaczone przez człowieka

Jeśli chcesz poprawić dokładność rozpoznawania, zwłaszcza problemy, które są spowodowane usunięciem lub nieprawidłowym zastąpieniem słów, warto użyć transkrypcji oznaczonych przez człowieka wraz z danymi audio. Co to są transkrypcje oznaczone przez człowieka? To proste, są to słowa po słowach, dosłowne transkrypcje pliku audio.

Duża próbka danych transkrypcji jest wymagana w celu poprawy rozpoznawania, sugerujemy dostarczenie od 10 do 1000 godzin danych transkrypcyjnych. Na tej stronie przejrzymy wytyczne, które pomogą Ci tworzyć wysokiej jakości transkrypcje. Ten przewodnik jest podzielony według ustawień regionalnych, z sekcjami dla języka angielskiego, chińskiego mandaryńskiego i niemieckiego.

## <a name="us-english-en-us"></a>Angielski amerykański (en-US)

Transkrypcje oznaczone przez człowieka dla angielskiego dźwięku muszą być dostarczane jako zwykły tekst, tylko przy użyciu znaków ASCII. Unikaj używania znaków interpunkcyjnych Latin-1 lub Unicode. Znaki te są często nieumyślnie dodawane podczas kopiowania tekstu z aplikacji edytora tekstu lub skrobania danych ze stron internetowych. Jeśli te znaki są obecne, upewnij się, aby zaktualizować je z odpowiednim substytucji ASCII.

Oto kilka przykładów:

| Znaki, których należy unikać | Podstawienie | Uwagi |
| ------------------- | ------------ | ----- |
| "Witaj świecie" | "Witaj świecie" | Znaki otwarcia i zamknięcia zostały zastąpione odpowiednimi znakami ASCII. |
| Dzień Jana | Dzień Jana | Apostrof został zastąpiony odpowiednim znakiem ASCII. |
| to było dobre, nie, to było wspaniałe! | To było dobre - nie, to było świetne! | Kreska em została zastąpiona dwoma myślnikami. |

### <a name="text-normalization-for-us-english"></a>Normalizacja tekstu w języku angielskim usa

Normalizacja tekstu to przekształcanie słów w spójny format używany podczas szkolenia modelu. Niektóre reguły normalizacji są stosowane do tekstu automatycznie, jednak zalecamy stosowanie tych wskazówek podczas przygotowywania danych transkrypcji oznaczonych przez człowieka:

- Wypisuj skróty w słowach.
- Zapisz niestandardowe ciągi liczbowe w słowach (takich jak warunki księgowania).
- Znaki niealfabetyczne lub mieszane znaki alfanumeryczne powinny być transkrybowane jako wymawiane.
- Skróty wymawiane jako słowa nie powinny być edytowane (takie jak "radar", "laser", "RAM" lub "NATO").
- Wypisuj skróty, które są wymawiane jako oddzielne litery z każdą literą oddzieloną spacją.

Oto kilka przykładów normalizacji, które należy wykonać na transkrypcji:

| Tekst oryginalny               | Tekst po normalizacji              |
| --------------------------- | ------------------------------------- |
| Dr Bruce Banner            | Doktor Bruce Banner                   |
| James Bond, 007             | James Bond, podwójne oh siedem           |
| Ke$ha                       | Kesha                                 |
| Jak długo jest 2x4         | Jak długo jest dwa przez cztery           |
| Spotkanie trwa od 13:00 do 15:00 | Spotkanie trwa od jednego do trzech |
| Moja grupa krwi to O+         | Moja grupa krwi jest O dodatnia           |
| Woda jest H20                | Woda jest H 2 O                        |
| Zagraj oU812 przez Van Halen     | Zagraj o U 8 1 2 przez Van Halen           |
| UTF-8 with BOM              | U T F 8 z BOM                      |

Następujące reguły normalizacji są automatycznie stosowane do transkrypcji:

- Użyj małych liter.
- Usuń wszystkie znaki interpunkcyjne z wyjątkiem apostrofów w wyrazach.
- Rozwiń liczby na słowa/formularz mówiony, na przykład kwoty w dolarach.

Oto kilka przykładów normalizacji wykonywanej automatycznie na transkrypcji:

| Tekst oryginalny                          | Tekst po normalizacji          |
| -------------------------------------- | --------------------------------- |
| "Święta krowa!" powiedział Batman.               | święta krowa powiedział batman              |
| "Co?" powiedział pomocnik Batmana, Robin. | co powiedział pomocnik Batmana robin |
| Idź dostać -em!                            | go dostać em                         |
| Jestem podwójnie połączony                     | Jestem podwójnie połączony                |
| Ulica Wiązu 104                         | jeden oh cztery ulica Wiązu            |
| Dostrajanie do 102,7                          | dostroić do jednego oh dwa punkty siedem    |
| Pi to około 3,14                       | pi to około trzech punktów jeden czwarty  |
| Kosztuje \$3,14                        | kosztuje trzy czternaście           |

## <a name="mandarin-chinese-zh-cn"></a>Chiński mandaryński (zh-CN)

Transkrypcje oznaczone przez człowieka dla chińskiego dźwięku mandaryńskiego muszą być zakodowane w trybie UTF-8 znacznikiem kolejności bajtów. Należy unikać używania znaków interpunkcyjnych o połówkowej szerokości. Znaki te mogą być przypadkowo dołączone podczas przygotowywania danych w programie do przetwarzania tekstu lub zeskrobać dane ze stron internetowych. Jeśli te znaki są obecne, upewnij się, aby zaktualizować je z odpowiednim podstawienia pełnej szerokości.

Oto kilka przykładów:

| Znaki, których należy unikać | Podstawienie   | Uwagi |
| ------------------- | -------------- | ----- |
| "Japonki" | "Japonki" | Znaki otwarcia i zamknięcia zostały zastąpione odpowiednimi znakami. |
| 中田田田田田? | 中田田田田田?| Znak zapytania został zastąpiony odpowiednim charakterem. |

### <a name="text-normalization-for-mandarin-chinese"></a>Normalizacja tekstu dla języka mandaryńskiego w języku chińskim

Normalizacja tekstu to przekształcanie słów w spójny format używany podczas szkolenia modelu. Niektóre reguły normalizacji są stosowane do tekstu automatycznie, jednak zalecamy stosowanie tych wskazówek podczas przygotowywania danych transkrypcji oznaczonych przez człowieka:

- Wypisuj skróty w słowach.
- Wypij ciągi liczbowe w formie mówionej.

Oto kilka przykładów normalizacji, które należy wykonać na transkrypcji:

| Tekst oryginalny | Tekst po normalizacji |
| ------------- | ------------------------ |
| 21 | 中田田田田田 |
| 3 504 | 中 田田 |

Następujące reguły normalizacji są automatycznie stosowane do transkrypcji:

- Usuń wszystkie znaki interpunkcyjne
- Rozwijanie liczb do formularza mówionego
- Konwertowanie liter o pełnej szerokości na litery o połówkowej szerokości
- Używanie wielkich liter dla wszystkich angielskich wyrazów

Oto kilka przykładów normalizacji wykonywanej automatycznie na transkrypcji:

| Tekst oryginalny | Tekst po normalizacji |
| ------------- | ------------------------ |
| 3.1415 | 19.00. |
| ¥ 3,5 | 19.00. |
| w f y z | W F Y Z |
| 1992 r. 8 8 | 19.00 |
| 中田田田田? | 中田 田田 |
| 5:00 | 中田 田田 |
| 21- | 中田 田田 |

## <a name="german-de-de-and-other-languages"></a>niemiecki (de-DE) i inne języki

Transkrypcje oznaczone przez człowieka dla niemieckiego dźwięku (i innych języków innych niż angielski lub mandaryński chiński) muszą być zakodowane w języku UTF-8 znacznikiem kolejności bajtów. Dla każdego pliku audio należy podać jeden zapis oznaczony etykietą ludzką.

### <a name="text-normalization-for-german"></a>Normalizacja tekstu dla języka niemieckiego

Normalizacja tekstu to przekształcanie słów w spójny format używany podczas szkolenia modelu. Niektóre reguły normalizacji są stosowane do tekstu automatycznie, jednak zalecamy stosowanie tych wskazówek podczas przygotowywania danych transkrypcji oznaczonych przez człowieka:

- Wpisz punkty dziesiętne jako "", a nie ".".
- Wpisz separatory czasu jako ":" a nie "." 12:00).
- Skróty takie jak "ca." nie są zastępowane. Zalecamy użycie pełnego formularza mówionego.
- Cztery główne operatory matematyczne (+, -, \*i /) są usuwane. Zalecamy zastąpienie ich pisemną formą: "plus", "minus", "mal" i "geteilt".
- Operatory porównania są usuwane (=, < i >). Zalecamy zastąpienie ich "gleich", "kleiner als" i "grösser als".
- Napisz ułamki, takie jak 3/4, w formie pisemnej (na przykład: "drei viertel" zamiast 3/4).
- Zastąp symbol "€" jego pisemną formą "Euro".

Oto kilka przykładów normalizacji, które należy wykonać na transkrypcji:

| Tekst oryginalny    | Tekst po normalizacji użytkownika | Tekst po normalizacji systemu       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr              | es ist zwölf uhr drei und zwanzig uhr |
| {12.45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3 - 4        | 2 plus 3 minus 4              | zwei plus drei minus vier             |

Następujące reguły normalizacji są automatycznie stosowane do transkrypcji:

- Użyj małych liter dla całego tekstu.
- Usuń wszystkie znaki interpunkcyjne, w tym różne rodzaje cudzysłowów ("test", "test", "test" i "test" są OK).
- Wyrzucić rzędy z dowolnymi znakami specjalnymi z tego zestawu: ¢ ¤ ¥ ¦ § © ¬ ® ° ± ² μ × ÿ Ø¬ ¬ ¬ .
- Rozwiń liczby do postaci mówionej, w tym kwot w dolarach lub euro.
- Zaakceptuj umlauts tylko dla a, o, i ty. Inne zostaną zastąpione przez "th" lub zostaną odrzucone.

Oto kilka przykładów normalizacji wykonywanej automatycznie na transkrypcji:

| Tekst oryginalny    | Tekst po normalizacji |
| ---------------- | ------------------------ |
| Pierścień Frankfurtera | pierścień frankfurter         |
| ¡Eine Frage!     | eine frage               |
| wir, haben       | wir haben                |

## <a name="next-steps"></a>Następne kroki

- [Przygotowanie i przetestowanie danych](how-to-custom-speech-test-data.md)
- [Sprawdź swoje dane](how-to-custom-speech-inspect-data.md)
- [Oceń swoje dane](how-to-custom-speech-evaluate-data.md)
- [Trenowanie modelu](how-to-custom-speech-train-model.md)
- [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)
