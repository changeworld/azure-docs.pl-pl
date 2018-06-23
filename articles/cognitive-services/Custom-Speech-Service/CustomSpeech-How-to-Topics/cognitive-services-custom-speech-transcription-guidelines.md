---
title: Wytyczne zapisu w niestandardowej usługi rozpoznawania mowy na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować dane do usługi mowy niestandardowe w usługach kognitywnych.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 2785a35ac7583ac3d9503cb721d10078d86aa365
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347172"
---
# <a name="transcription-guidelines"></a>Wskazówki dotyczące przekształcania
Aby zapewnić najlepsze wykorzystanie danych tekstu do dostosowania modelu akustycznego i język, należy przestrzegać następujących wytycznych przekształcania. Te wytyczne mają określonego języka.

## <a name="text-normalization"></a>Normalizacji tekstu

Optymalne stosowanych w dostosowania modelu akustycznego lub język dane tekstowe muszą być znormalizowane, co oznacza, że przekształceniu na standardowy, jednoznaczne czytelny przez system. W tej sekcji opisano normalizacji tekst wykonywane przez usługę mowy niestandardowych podczas importowania danych i normalizacji tekst, który użytkownik musi wykonać przed importu danych.

## <a name="inverse-text-normalization"></a>Tekst odwrotność funkcji normalizacji

Proces konwersji "nieprzetworzonej" niesformatowanego tekstu sformatowanego tekstu, tj. z wielkich liter i znaków interpunkcyjnych, jest nazywany tekst odwrotność funkcji normalizacji (ITN). ITN odbywa się na wyniki zwrócone przez interfejs API mowy usługi kognitywnych firmy Microsoft. Punkt końcowy niestandardowe wdrożenie za pomocą niestandardowej usługi rozpoznawania mowy używa tego samego ITN jako kognitywnych usług mowy interfejsu API usługi Microsoft. Jednak ta usługa nie obsługuje obecnie ITN niestandardowych, więc nowe warunki wynikające z modelu niestandardowych języka nie będą formatowane w wynikach rozpoznawania.

## <a name="transcription-guidelines-for-en-us"></a>Przekształcenia wytyczne dotyczące en US

Tekst dane przekazane do tej usługi powinna być zapisana w postaci zwykłego tekstu przy użyciu drukowalnych ASCII zestaw znaków. Każdy wiersz pliku powinna zawierać tekst dla tylko jednego utterance.

Należy koniecznie Unikaj używania znaków interpunkcyjnych Unicode. Przyczyną może być przypadkowo przygotowywania danych w programie przetwarzania program lub oskrobaniu danych ze stron sieci web. Zastąp te znaki odpowiednie zmiany ASCII. Na przykład:

| Unicode, aby uniknąć | Podstawienie ASCII |
|----- | ----- |
| "Hello world" (otwierające i zamykające cudzysłowów) | "Hello world" (podwójne cudzysłowy) |
| Foldery na dzień (prawy pojedynczy cudzysłów) | Foldery na dzień (apostrof) |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Tekst normalizacji wykonywane przez usługę mowy niestandardowe

Ta usługa będzie wykonywać następujące normalizacji tekst na danych zaimportowana jako zestaw danych języka lub transcriptions akustycznego zestawu danych. Obejmuje to

*   Wielkość liter na małe cały tekst
*   Usuwanie wszystkich znaków interpunkcyjnych z wyjątkiem apostrofów wewnętrznego programu word
*   Rozszerzanie liczb do formularza rozmowy, w tym dolarach

Oto kilka przykładów

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| Starbucks kawy | starbucks kawy |
| "Świętego krowy!" wspomniane Batman. | krowy Świętego powiedział batman |
| "Co?" sidekick wspomnianej Batman, działania okrężnego. | działanie okrężne sidekick jakie wspomnianej batman |
| Przejdź get - em! | em Przejdź get |
| Jestem double-jointed | i double używam połączona. |
| 104 głównej ulicy | jeden Niestety cztery główne ulica |
| Dostroić 102.7 | Dostosuj do jednego Niestety siedmiu punktu dwa |
| Pi jest około 3,14 | pi jest około trzech czterech jeden punkt |
| Kosztuje 3,14 $ | kosztuje trzech czternastu |

### <a name="text-normalization-required-by-users"></a>Tekst normalizacji wymaganych przez użytkowników

Aby zapewnić najlepsze wykorzystanie danych, następujące reguły normalizacji powinny być stosowane do danych przed jego zaimportowaniem.

*   Skróty powinna być zapisana w słowach, aby odzwierciedlić mówionej formy
*   Niestandardowe ciągi numeryczne powinna być zapisana słownie
*   Wyrazy zawierające znaki inne niż alfanumeryczne lub mieszane znaków alfanumerycznych powinien przetłumaczone, jak w przypadku
*   Typowe akronimów może pozostać jako pojedynczy element bez kropek lub spacji między literami, ale wszystkie inne akronimów powinna być zapisana w oddzielnych litery każdego literą rozdzielonych spacją

Oto kilka przykładów

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| 14 NE 3rd odzyskiwania po awarii. | czternastu northeast trzeci dysk |
| Dr Strangelove | Strangelove lekarza |
| Rentowność Kuba 007 | Kuba obligacji double Niestety siedem |
| KE$ ha | Kesha |
| Jak długo trwa 2 x 4 | Jak długo trwa dwóch przy czterech |
| Spotkanie przechodzi od 1 — 15: 00 | Spotkanie przechodzi od jednej do trzech pm |
| Moje typ krwi jest O + | Moje typ krwi jest dodatnią O |
| limitu górnego jest H20 | limitu górnego jest H 2 O |
| Odtwórz OU812 przez Van Halen | Odtwórz U O 2 1 8 przez Van Halen |

## <a name="transcription-guidelines-for-zh-cn"></a>Przekształcenia wytyczne dotyczące zh-CN

Tekst dane przekazane do niestandardowej usługi rozpoznawania mowy należy używać **kodowania UTF-8 (z uwzględnieniem BOM)**. Każdy wiersz pliku powinna zawierać tekst dla tylko jednego utterance.

Należy koniecznie Unikaj używania znaków interpunkcyjnych połówkowej szerokości. Przyczyną może być przypadkowo przygotowywania danych w programie przetwarzania program lub oskrobaniu danych ze stron sieci web. Zastąp te znaki odpowiednie zmiany pełnej szerokości. Na przykład:

| Unicode, aby uniknąć | Podstawienie ASCII |
|----- | ----- |
| "你好" (otwierających i zamykających cudzysłowów) | "你好" (podwójny cudzysłów) |
| 需要什么帮助? (znak zapytania) | 需要什么帮助? |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Tekst normalizacji wykonywane przez usługę mowy niestandardowe

Ta usługa mowy wykona następujące normalizacji tekst na danych zaimportowana jako zestaw danych języka lub transcriptions akustycznego zestawu danych. Obejmuje to

*   Usuwanie wszystkie znaki interpunkcyjne
*   Rozszerzanie liczb do formularza rozmowy
*   Przekonwertować znaków o pełnej szerokości połówkowej szerokości litery.
*   Wszystkie wielkości liter na wielką angielski słowa

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

Aby zapewnić najlepsze wykorzystanie danych, stosuje się następujące reguły normalizacji danych _wcześniejsze_ do importowania.

*   Skróty powinna być zapisana w słowach, aby odzwierciedlić mówionej formy
*   Ta usługa nie obejmuje wszystkich ilości liczbowych. Jest bardziej niezawodny zapisać ciągów liczbowych w postaci rozmowy

Oto kilka przykładów

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="transcription-guidelines-for-de-de"></a>Przekształcenia wytyczne dotyczące de-DE

Należy używać tylko dane tekstowe przekazane do niestandardowej usługi rozpoznawania mowy **kodowania UTF-8 (z uwzględnieniem BOM)**. Każdy wiersz pliku powinna zawierać tekst dla tylko jednego utterance.

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Tekst normalizacji wykonywane przez usługę mowy niestandardowe

Ta usługa będzie wykonywać następujące normalizacji tekst na danych zaimportowana jako zestaw danych języka lub transcriptions akustycznego zestawu danych. Obejmuje to

*   Wielkość liter na małe cały tekst
*   Usuwanie wszystkie znaki interpunkcyjne, w tym języku angielskim lub niemieckim cudzysłowy ("test", "test", "test" lub "test" są ok)
*   Odrzuć wszystkie wiersze zawierające wszelkie znaki specjalne w tym: ^ l są ¤ ą ¦ § © ¬­ ª® ° granicach ² m x ÿ Ø¬¬
*   Rozszerzanie liczb do formularza word, w tym dolara ($) lub EUR
*   Możemy zaakceptować tylko umlaut dla, o u; inne zostaną zastąpione przez "th" lub odrzucone

Oto kilka przykładów

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| Pierścień Frankfurter | pierścień Frankfurter |
| "Hallo, Mama!" struktury sagt Tochter. | hallo mama sagt struktury tochter |
| ¡Eine Frage! | eine frage |
| szwajcarski WIR, haben | haben szwajcarski WIR |
| Macht DAS $10 | DAS macht zehn kwoty |


### <a name="text-normalization-required-by-users"></a>Tekst normalizacji wymaganych przez użytkowników

Aby zapewnić najlepsze wykorzystanie danych, następujące reguły normalizacji powinny być stosowane do danych przed jego zaimportowaniem.

*   Powinien być punkt dziesiętny, a nie. np. 2,3% i nie % 2.3
*   Separator godziny między godzinach i minutach powinna być: oraz nie., np., 12:00 Uhr
*   Skróty, takie jak "urzędu certyfikacji.", "bzw." nie zostaną zastąpione. Firma Microsoft zaleca użyj pełnej formy, aby uzyskać poprawny wymowy.
*   Pięć głównych operatorów matematycznych są usuwane: +, -, \*, /.
 Firma Microsoft zaleca się ich literału formie plus minus nieprawidłowo, geteilt je zastąpić.
*   Samo dotyczy komparatorów (<>, =) — gleich, kleiner als, grösser als
*   Użyj ułamek, takie jak 3/4 w postaci słowa "drei viertel" zamiast ¾
*   Zastąp € symbol formularza word "EUR"


Oto kilka przykładów

| Oryginalny tekst | Po normalizacji użytkownika | Po normalizacji systemu
|--------  | ----- | -------- |
| ES ist 12.23Uhr | ES ist 12:23Uhr | ES ist zwölf uhr drei i zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 i 3 — 4 | 2 i 3 minus 4 | zwei plus drei minus vier|
| Macht DAS € 12 | Macht DAS EUR 12 | DAS macht zwölf EUR |



## <a name="next-steps"></a>Kolejne kroki
* [Jak używać niestandardowych endpoint mowy na tekst](cognitive-services-custom-speech-create-endpoint.md)
* Zwiększyć dokładność z Twojej [niestandardowych modelu akustycznego](cognitive-services-custom-speech-create-acoustic-model.md)
* Zwiększyć dokładność z [model języka niestandardowych](cognitive-services-custom-speech-create-language-model.md)
