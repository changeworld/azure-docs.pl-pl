---
title: Przekształcenia wytyczne dotyczące szkolenie | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować tekstu, aby dostosować akustycznego i modele języka i czcionek głosu usługi mowy.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 93ab7c81a773f692b2b970bb1901d82b7aceb5a2
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "35356155"
---
# <a name="transcription-guidelines-for-using-speech-service"></a>Przekształcenia wytyczne dotyczące korzystania z usługi rozpoznawania mowy

Aby dostosować **mowy na tekst** lub **tekst na mowę**, musisz podać tekst wraz z mowy. Każdy wiersz tekstu odpowiada jednej utterance. Tekst jest nazywany *wykaz*, i należy ją utworzyć w określonym formacie.

Usługa rozpoznawania mowy wykonuje niektóre normalizations dla Ciebie w celu dostosowania tekstu. Inne zadania normalizacji należy wykonać przed przesłaniem do trenowania tekst. 

W tym artykule opisano oba rodzaje normalizations. Wytyczne się nieco różnić dla różnych języków.

## <a name="us-english-en-us"></a>Angielski (US) (en US)

Tekst dane przekazane do tej usługi, które mają być zapisywane w postaci zwykłego tekstu przy użyciu tylko ASCII zestaw znaków. Każdy wiersz pliku powinna zawierać tekst dla pojedynczego utterance.

Należy koniecznie należy unikać użycia rozszerzonej (Latin-1) i znaków interpunkcyjnych Unicode. Te znaki mogą być uwzględnione przypadkowo podczas przygotowywania danych w edytorze tekstów lub oskrobaniu danych ze stron sieci web. Zastąp te znaki odpowiednie zmiany ASCII. Na przykład:

| Znaki, aby uniknąć | Podstawienie |
|----- | ----- |
| "Hello world" (otwierające i zamykające cudzysłowów) | "Hello world" (podwójne cudzysłowy) |
| Foldery na dzień (prawy pojedynczy cudzysłów) | Foldery na dzień (apostrof) |
| było dobrej — nie było wspaniałe! (pauza) | dobrym — został nie, było bardzo! (Łączniki) |

### <a name="text-normalization-performed-by-the-service"></a>Tekst normalizacji wykonywane przez usługę

Usługa mowy wykonuje następujące normalizacji tekst na zapisy tekstu.

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

### <a name="text-normalization-you-must-perform"></a>Normalizacji tekst, który należy wykonać

Mają zastosowanie następujące normalizacji do Twojej zapisy tekstu.

*   Skróty powinna być zapisana w słowach, aby odzwierciedlić mówionej formy
*   Niestandardowe ciągi numeryczne (na przykład niektóre datę lub formularze księgowości) mają być zapisywane słownie
*   Wyrazy zawierające znaki inne niż alfanumeryczne lub mieszane znaków alfanumerycznych powinien przetłumaczone, jak w przypadku
*   Pozostaw skróty Wymowa jako słowa bez zmian. Na przykład radarowego, laserowa pamięci RAM, NATO i Mr.
*   Zapis skróty Wymowa jako osobne litery, liter rozdzielone spacjami. Na przykład IBM, Procesora, FBI, do ustalenia, NaN. 

Oto kilka przykładów:

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| 14 NE 3rd odzyskiwania po awarii. | czternastu northeast trzeci dysk |
| Dr Strangelove | Strangelove lekarza |
| Rentowność Kuba 007 | Niestety dwukrotnie rentowność Kuba siedem |
| KE$ ha | Kesha |
| Jak długo trwa 2 x 4 | Jak długo trwa dwóch przy czterech |
| Spotkanie przechodzi od 1 — 15: 00 | Spotkanie przechodzi od jednej do trzech pm |
| Moje typ krwi jest O + | Moje typ krwi jest dodatnią O |
| limitu górnego jest H20 | limitu górnego jest H 2 O |
| Odtwórz OU812 przez Van Halen | Odtwórz U O 2 1 8 przez Van Halen |
| UTF-8 z BOM | U T F 8 z BOM |

## <a name="chinese-zh-cn"></a>Chiński (zh-CN)

Tekst dane przekazane do niestandardowej usługi rozpoznawania mowy należy używać kodowania UTF-8 z znacznika kolejności bajtów. Każdy wiersz pliku powinna zawierać tekst dla pojedynczego utterance.

Należy koniecznie Unikaj używania znaków interpunkcyjnych połówkowej szerokości. Te znaki mogą być uwzględnione przypadkowo podczas przygotowywania danych w edytorze tekstów lub oskrobaniu danych ze stron sieci web. Zastąp je z odpowiednich podstawień pełnej szerokości. Na przykład:

| Znaki, aby uniknąć | Podstawienie |
|----- | ----- |
| "你好" (otwierających i zamykających cudzysłowów) | "你好" (podwójny cudzysłów) |
| 需要什么帮助? (znak zapytania) | 需要什么帮助? |

### <a name="text-normalization-performed-by-the-service"></a>Tekst normalizacji wykonywane przez usługę

Usługa mowy wykonuje następujące normalizacji tekst na zapisy tekstu.

*   Usuwanie wszystkie znaki interpunkcyjne
*   Rozszerzanie liczb do mówionej formy
*   Konwersja znaków o pełnej szerokości na litery połówkowej szerokości
*   Wszystkie wielkości liter na wielką angielski słowa

Oto kilka przykładów.

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| 3,1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

### <a name="text-normalization-you-must-perform"></a>Normalizacji tekst, który należy wykonać

Przed zaimportowaniem go stosować następujące normalizacji tekstu.

*   Skróty powinna być zapisana w słowach, aby odzwierciedlić mówionej formy
*   Ta usługa nie obejmuje wszystkich ilości liczbowych. Jest to bardziej niezawodny, aby zapisać ciągów liczbowych w mówionej formy.

Oto kilka przykładów.

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Inne języki

Dane przekazane do **mowy na tekst** usługa musi używać kodowania UTF-8 z znacznika kolejności bajtów. Każdy wiersz pliku powinna zawierać tekst dla pojedynczego utterance.

> [!NOTE]
> Poniższe przykłady użycia niemiecki. Jednak te wytyczne mają zastosowanie do wszystkich języków, które nie są angielskie US lub chiński.

### <a name="text-normalization-performed-by-the-service"></a>Tekst normalizacji wykonywane przez usługę

Usługa mowy wykonuje następujące normalizacji tekst na zapisy tekstu.

*   Wielkość liter na małe cały tekst
*   Usuwanie wszystkich znaków interpunkcyjnych, łącznie z różnych typów ofert ("test", "test", "test" lub "test" są ok)
*   Odrzuca wszystkie wiersze zawierające znaków specjalnych z zestawu ^ l są ¤ ą ¦ § © ¬­ ª® ° granicach ² m x ÿ Ø¬¬
*   Rozszerzanie liczb do formularza word, w tym dolara ($) lub EUR
*   Umlaut są akceptowane tylko w przypadku, o u; inne zostanie zastąpione przez "th" lub odrzucone

Oto kilka przykładów

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| Pierścień Frankfurter | pierścień Frankfurter |
| "Hallo, Mama!" struktury sagt Tochter. | hallo mama sagt struktury tochter |
| ¡Eine Frage! | eine frage |
| szwajcarski WIR, haben | haben szwajcarski WIR |
| Macht DAS $10 | DAS macht zehn kwoty |

### <a name="text-normalization-you-must-perform"></a>Normalizacji tekst, który należy wykonać

Przed zaimportowaniem go stosować następujące normalizacji tekstu.

*   Dziesiętnego powinien być ",", a nie".": 2,3% i nie % 2.3
*   Separator godziny między godzinach i minutach powinna być ":"i nie".": 12:00 Uhr
*   Skróty, takie jak "urzędu certyfikacji.", "bzw." nie zostaną zastąpione. Firma Microsoft zaleca się, że używasz pełnej postaci.
*   Pięć głównych operatorów matematycznych są usuwane: +, -, \*, /. Firma Microsoft zaleca zamianę ich literału formie: plus minus nieprawidłowo, geteilt.
*   Operatory porównania (<>, =) — samo dotyczy gleich, kleiner als, grösser als
*   Użyj ułamków, takie jak 3/4 w postaci word (na przykład "viertel drei" zamiast ¾)
*   Zastąp € symbol formularza word "EUR"

Oto kilka przykładów.

| Oryginalny tekst | Po normalizacji użytkownika | Po normalizacji systemu
|--------  | ----- | -------- |
| ES ist 12.23Uhr | ES ist 12:23Uhr | ES ist zwölf uhr drei i zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 i 3 — 4 | 2 i 3 minus 4 | zwei plus drei minus vier|
| Macht DAS € 12 | Macht DAS EUR 12 | DAS macht zwölf EUR |

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz wersję próbną subskrypcji mowy](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznawanie mowy w języku C#](quickstart-csharp-windows.md)
