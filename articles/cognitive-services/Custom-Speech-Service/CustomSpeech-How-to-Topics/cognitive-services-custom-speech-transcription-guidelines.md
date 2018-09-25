---
title: Tekst wskazówki w usłudze Custom Speech Service na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować dane usługę Custom Speech Service w usługach Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 2772665107f94a273731d2147154e546225b22dd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999736"
---
# <a name="transcription-guidelines"></a>Wytyczne dotyczące transkrypcji
W celu zapewnienia najlepszego wykorzystania danych tekstowych do dostosowania modelu akustyczne i językowe, należy przestrzegać następujących wytycznych transkrypcji. Te wytyczne są określonego języka.

## <a name="text-normalization"></a>Normalizacja tekstu

Dla optymalnego wykorzystania w dostosowaniu modelu akustycznego lub język danych tekstowych muszą być znormalizowane, co oznacza, że przekształcane na standardowy, jednoznaczną czytelny przez system. W tej sekcji opisano normalizacji tekstu, wykonywane przez Custom Speech Service, gdy dane są importowane i normalizacji tekst, który użytkownik musi wykonać przed importu danych.

## <a name="inverse-text-normalization"></a>Normalizacja odwrotność tekstu

Proces konwersji "pierwotne" niesformatowanego tekstu sformatowanego tekstu, tj. z wielkich liter i znaków interpunkcyjnych, jest wywoływana tekst odwrotny normalizacji (podczas). Podczas odbywa się na wyniki zwrócone przez Microsoft Cognitive Services interfejs API mowy. Niestandardowy punkt końcowy wdrażane za pomocą usługa Custom Speech Service używa tego samego podczas jako Microsoft Cognitive Services interfejs API mowy. Jednak ta usługa nie obsługuje obecnie niestandardowych podczas tak nowe warunki wynikające z modelu języka niestandardowego nie zostanie sformatowana w wyniki rozpoznawania.

## <a name="transcription-guidelines-for-en-us"></a>Tekst wskazówki dotyczące en US

Dane tekstowe przekazany do tej usługi, zapisywane są w postaci zwykłego tekstu przy użyciu drukowalnych ASCII zestaw znaków. Każdy wiersz w pliku może zawierać tekst dla tylko jednego wypowiedź.

Należy unikać użycia znaków Unicode, znaki interpunkcyjne. Przyczyną może być przypadkowo przygotowywania danych w programie przetwarzania program lub oskrobaniu danych ze stron sieci web. Zastąp te znaki odpowiednie podstawienia ASCII. Na przykład:

| Unicode, aby uniknąć | Podstawianie ASCII |
|----- | ----- |
| "Hello world" (Otwórz i Zamknij cudzysłów) | "Hello world" (podwójne cudzysłowy) |
| John's dnia (prawy pojedynczy cudzysłów) | John's dnia (apostrof) |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Tekst normalizacji wykonywane przez Custom Speech Service

Ta usługa będzie wykonywać następujące normalizacji tekstu danych importowany jako zestaw danych języka lub transkrypcje akustyczny zestawu danych. Obejmuje to

*   Wielkości liter na małe cały tekst
*   Usunięcie wszystkich znaków interpunkcyjnych z wyjątkiem apostrofy wewnętrznego programu word
*   Rozszerzanie liczb do mówionej formy, w tym dolarach

Poniżej przedstawiono kilka przykładów

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| Kawa kawę | Kawa kawę |
| "Joseph krowy!" wspomniane Batman. | krowy Joseph powiedział batman |
| "Jak?" wspomniane Batman sidekick, działanie okrężne. | jakie wspomniane batman sidekick okrężnego |
| Przejdź pobieranie - em! | Wybierz pozycję Pobierz em |
| Jestem double-jointed | i jestem double połączona |
| 104 głównej ulicy | co o czterech głównych ulicy |
| Dostosowywanie do 102.7 | Dostosowywanie do jednego o dwóch punktu 7 |
| Pi jest około 3,14 | pi jest około trzy punktu 1 4 |
| To kosztuje $3,14 | to kosztuje trzech czternastu |

### <a name="text-normalization-required-by-users"></a>Tekst normalizacji wymaganych przez użytkowników

W celu zapewnienia najlepszego wykorzystania danych, stosuje się następujące reguły normalizacji danych przed zaimportowaniem go.

*   Skróty powinny zapisywane w słowach, aby odzwierciedlić mówionej formy
*   Niestandardowe ciągi numeryczne powinny zapisywane w słowach
*   Wyrazy z znaki spoza alfabetu lub mieszane znaki alfanumeryczne powinny transkrybowanego, jak wymawiane
*   Typowe akronimów może pozostać jako pojedynczy element bez kropek lub spacji między literami, ale wszystkie inne akronimów powinien być zapisywany w oddzielnych litery z każdej litery oddzielone spacją

Poniżej przedstawiono kilka przykładów

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| 14 NE 3 odzyskiwania po awarii. | czternastu dysku trzeci północno-wschodnie |
| Dr Strangelove | Strangelove lekarzem |
| James Bond 007 | James obligacji double o 7 |
| KE$ zaświadczanie o kondycji | Kesha |
| Jak długo trwa 2 x 4 | Jak długo trwa dwa, cztery |
| Spotkania przechodzi z 1-15: 00 | Spotkania przechodzi od jednej do trzech pm |
| Mój typ krwi jest O + | Mój typ krwi jest dodatnia O |
| to H20 | to O 2 godz. |
| Odtwórz OU812 przez Van Halen | Odtwórz U O 2 1 8 przez Van Halen |

## <a name="transcription-guidelines-for-zh-cn"></a>Tekst wskazówki dotyczące zh-CN

Należy użyć danych tekstowych przekazany do Custom Speech Service **kodowania UTF-8 (z uwzględnieniem BOM)**. Każdy wiersz w pliku może zawierać tekst dla tylko jednego wypowiedź.

Należy unikać użycia znaków interpunkcyjnych połowę szerokości. Przyczyną może być przypadkowo przygotowywania danych w programie przetwarzania program lub oskrobaniu danych ze stron sieci web. Zastąp te znaki odpowiednie podstawienia pełnej szerokości. Na przykład:

| Unicode, aby uniknąć | Podstawianie ASCII |
|----- | ----- |
| "你好" (otwierających i zamykających podwójne cudzysłowy) | "你好" (cudzysłów) |
| 需要什么帮助? (znak zapytania) | 需要什么帮助? |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Tekst normalizacji wykonywane przez Custom Speech Service

Ta usługa mowy wykona następujące normalizacji tekst na dane zaimportowane jako zestaw danych języka lub transkrypcje akustyczny zestawu danych. Obejmuje to

*   Usuwanie wszystkie znaki interpunkcyjne
*   Rozszerzanie liczb do mówionej formy
*   Konwertuj znaków o pełnej szerokości na połowę szerokości litery.
*   Wielkości liter górnego wszystkie wyrazy w języku angielskim

Oto kilka przykładów:

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| 3,1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

### <a name="text-normalization-required-by-users"></a>Tekst normalizacji wymaganych przez użytkowników

W celu zapewnienia najlepszego wykorzystania danych, stosuje się następujące reguły normalizacji danych _wcześniejsze_ do importowania.

*   Skróty powinny zapisywane w słowach, aby odzwierciedlić mówionej formy
*   Ta usługa nie obejmuje wszystkich ilości liczbowych. Jest bardziej niezawodna zapisać ciągów liczbowych w mówionej formy

Poniżej przedstawiono kilka przykładów

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="transcription-guidelines-for-de-de"></a>Tekst wskazówki dotyczące de-DE.

Dane przekazywane do Custom Speech Service należy używać tylko **kodowania UTF-8 (z uwzględnieniem BOM)**. Każdy wiersz w pliku może zawierać tekst dla tylko jednego wypowiedź.

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Tekst normalizacji wykonywane przez Custom Speech Service

Ta usługa będzie wykonywać następujące normalizacji tekstu danych importowany jako zestaw danych języka lub transkrypcje akustyczny zestawu danych. Obejmuje to

*   Wielkości liter na małe cały tekst
*   Usunięcie wszystkich znaków interpunkcyjnych, w tym języku angielskim lub niemieckim cudzysłowy ("test", "test", "test" lub "test" jest ok)
*   Odrzuć wszystkie wiersze zawierające żadnych znaków specjalnych, w tym: ^ są l ¤ §... ą © ¬­ ª® ° granicach ² m x ÿ Ø¬¬
*   Rozszerzanie liczb do formularza programu word, w tym Dolar lub euro kwot
*   Możemy zaakceptować tylko umlaut dla, o u; inne zostaną zastąpione przez "th" lub odrzucone

Poniżej przedstawiono kilka przykładów

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| Pierścień Frankfurter | pierścień Frankfurter |
| "Hallo, Mama!" struktury sagt Tochter. | hallo mama sagt struktury tochter |
| ¡Eine Frage! | eine frage |
| szwajcarski WIR, haben | haben szwajcarski WIR |
| Macht DAS w wysokości 10 USD | Dolar zehn macht DAS |


### <a name="text-normalization-required-by-users"></a>Tekst normalizacji wymaganych przez użytkowników

W celu zapewnienia najlepszego wykorzystania danych, stosuje się następujące reguły normalizacji danych przed zaimportowaniem go.

*   Powinien być punktu dziesiętnego, a nie. np. 2,3% i nie % 2.3
*   Separator godziny między godzinach i minutach powinny być: i not., np. 12:00 Uhr
*   Skróty, takie jak "urzędu certyfikacji.", "bzw." nie zostaną zastąpione. Zaleca się użyj pełnej formy, aby mogła mieć poprawny Wymowa.
*   Pięć głównych operatorów matematycznych zostaną usunięte: +, -, \*, /.
 Zaleca się zastąpić je według ich literału formularza, plusa/minusa, nieprawidłowo, geteilt.
*   Dotyczy komparatorów, (<>, =) — gleich, kleiner als, grösser als
*   Użyj ułamek, takie jak 3/4 w postaci słowo "drei viertel" zamiast ¾
*   Zamień symbol — możliwość ciągłego dostosowywania formularza słowo "Euro"


Poniżej przedstawiono kilka przykładów

| Oryginalny tekst | Po jego normalizacji | Po normalizacji systemu
|--------  | ----- | -------- |
| ES ISTA 12.23Uhr | 12:23Uhr ISTA ES | ES ISTA zwölf uhr drei i zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 + 3 – 4 | 2 i 3 minus 4 | zwei oraz drei minus vier|
| DAS macht 12€ | DAS macht euro 12 | euro zwölf macht DAS |



## <a name="next-steps"></a>Kolejne kroki
* [Jak używać niestandardowego punktu końcowego mowy na tekst](cognitive-services-custom-speech-create-endpoint.md)
* Zwiększ dokładność za pomocą usługi [niestandardowy model akustyczny](cognitive-services-custom-speech-create-acoustic-model.md)
* Zwiększ dokładność z [model języka niestandardowego](cognitive-services-custom-speech-create-language-model.md)
