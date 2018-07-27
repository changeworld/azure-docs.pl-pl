---
title: Tekst wskazówki dotyczące szkolenie
description: Dowiedz się, jak przygotować tekstu, aby dostosować akustyczne i modeli językowych i czcionki głosowe do usługi rozpoznawania mowy.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/01/2018
ms.author: panosper
ms.openlocfilehash: a219db14d659348ef3ed4de1dffa640a948f1954
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283342"
---
# <a name="transcription-guidelines-for-using-speech-service"></a>Tekst wskazówki dotyczące używania usługi mowy

Aby dostosować **zamiana mowy na tekst** lub **zamiany tekstu na mowę**, musisz podać tekst i mowy. Każdy wiersz w tekście odnosi się do pojedynczego wypowiedź. Tekst powinien dokładnie mowy tak jak to możliwe. Tekst jest nazywany *transkrypcji*, i należy ją utworzyć w określonym formacie.

Usługa rozpoznawania mowy normalizuje dane wejściowe zapewnienie spójnego tekstu. 

W tym artykule opisano oba rodzaje normalizations. Wytyczne się nieco różnić dla różnych języków.

## <a name="us-english-en-us"></a>Angielski (US) (en US)

Tekst dane powinny zostać zapisane wypowiedź jeden na wiersz, w postaci zwykłego tekstu, przy użyciu zestawu znaków ASCII.

Unikaj stosowania rozszerzonych (Latin-1) i znaków interpunkcyjnych Unicode. Te znaki mogą być uwzględniane przypadkowo podczas przygotowywania danych w edytorze tekstu lub oskrobaniu danych ze stron sieci web. Zastąp te znaki odpowiednie podstawienia ASCII. Na przykład:

| Znaki, aby uniknąć | Podstawienia |
|----- | ----- |
| "Hello world" (Otwórz i Zamknij cudzysłów) | "Hello world" (podwójne cudzysłowy) |
| John's dnia (prawy pojedynczy cudzysłów) | John's dnia (apostrof) |
| było to dobrym — nie było wspaniałe! (pauza) | dobre — został nie, ale jest to doskonały! (Łączniki) |

### <a name="text-normalization-rules-for-english"></a>Tekst reguł normalizacji w języku angielskim

Usługa rozpoznawania mowy wykonuje następujące reguły normalizacji.

*   Wielkości liter na małe cały tekst
*   Usunięcie wszystkich znaków interpunkcyjnych z wyjątkiem apostrofy wewnętrznego programu word
*   Rozszerzanie liczb do mówionej formy, w tym dolarach

Poniżej przedstawiono kilka przykładów

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| "Joseph krowy!" wspomniane Batman. | krowy Joseph powiedział batman |
| "Jak?" wspomniane Batman sidekick, działanie okrężne. | jakie wspomniane batman sidekick okrężnego |
| Przejdź pobieranie - em! | Wybierz pozycję Pobierz em |
| Jestem double-jointed | Jestem double jointed |
| Ulica wiąz 104 | co o czterech wiąz ulicy |
| Dostosowywanie do 102.7 | Dostosowywanie do jednego o dwóch siedem |
| Pi jest około 3,14 | pi jest około trzy punktu 1 4 |
| To kosztuje $3,14 | to kosztuje trzech czternastu |

Mają zastosowanie następujące normalizacji do Twojego tekstu transkrypcji.

*   Skróty powinny być zapisany w słowach
*   Niestandardowe ciągi numeryczne (na przykład niektóre daty lub formularzy księgowości) zapisywane w słowach
*   Wyrazy z znaki spoza alfabetu lub mieszane znaki alfanumeryczne powinny transkrybowanego, jak wymawiane
*   Pozostaw skróty wymawiany wyrazów bez zmian. Na przykład radarowy laserowych, pamięci RAM, NATO.
*   Napisz skróty wymawiany oddzielne litery, za pomocą litery rozdzielone spacjami. Na przykład, IBM, procesora CPU, FBI, do ustalenia, NaN. 

Oto kilka przykładów:

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| 14 NE 3 odzyskiwania po awarii. | czternastu dysku trzeci północno-wschodnie |
| Dr Bruce transparentu | Transparent Bruce lekarzem |
| James Bond, 007 | Niestety dwukrotnie James Bond siedem |
| KE$ zaświadczanie o kondycji | Kesha |
| Jak długo trwa 2 x 4 | Jak długo trwa dwa, cztery |
| Spotkania przechodzi z 1-15: 00 | Spotkania przechodzi od jednej do trzech pm |
| Mój typ krwi jest O + | Mój typ krwi jest dodatnia O |
| to H20 | to O 2 godz. |
| Odtwórz OU812 przez Van Halen | Odtwórz U O 2 1 8 przez Van Halen |
| UTF-8 z BOM | U T F 8 z BOM |

## <a name="chinese-zh-cn"></a>Chiński (zh-CN)

Dane przekazywane do Custom Speech Service należy używać kodowania UTF-8 za pomocą znacznika kolejności bajtów. Wypowiedź jeden na wiersz można zapisać pliku.

Unikaj stosowania znaków interpunkcyjnych połowę szerokości. Te znaki mogą być uwzględniane przypadkowo podczas przygotowywania danych w edytorze tekstu lub oskrobaniu danych ze stron sieci web. Zastąp je odpowiednie podstawienia pełnej szerokości. Na przykład:

| Znaki, aby uniknąć | Podstawienia |
|----- | ----- |
| "你好" (otwierających i zamykających podwójne cudzysłowy) | "你好" (cudzysłów) |
| 需要什么帮助? (znak zapytania) | 需要什么帮助? |

### <a name="text-normalization-rules-for-chinese"></a>Tekst reguł normalizacji chiński

Usługa rozpoznawania mowy wykonuje następujące reguły normalizacji.

*   Usuwanie wszystkie znaki interpunkcyjne
*   Rozszerzanie liczb do mówionej formy
*   Konwertowanie liter połowę szerokości znaków o pełnej szerokości
*   Wielkości liter górnego wszystkie wyrazy w języku angielskim

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

Dotyczy następujących normalizacji tekstu przed jego zaimportowaniem.

*   Skróty powinny zapisywane w słowach (jak mówionej formy)
*   Zapisać ciągów liczbowych w programie mówionej formy.

Oto kilka przykładów.

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Inne języki

Przekazane dane tekstowe **zamiana mowy na tekst** usługi należy użyć kodowania UTF-8 za pomocą znacznika kolejności bajtów. Wypowiedź jeden na wiersz można zapisać pliku.

> [!NOTE]
> Te przykłady użycia niemiecki. Jednak te wytyczne dotyczą wszystkich języków, które nie są angielskie US lub chińskim.

### <a name="text-normalization-rules-for-german"></a>Tekst reguł normalizacji dla języka niemieckiego

Usługa rozpoznawania mowy wykonuje następujące reguły normalizacji.

*   Wielkości liter na małe cały tekst
*   Usunięcie wszystkich znaków interpunkcyjnych, w tym różne rodzaje cudzysłowów ("test", "test", "test" lub "test" jest ok)
*   Odrzucanie wierszy przy użyciu znaków specjalnych z są zestaw ¤ §... ą © ¬­ ª® ° granicach ² m x ÿ Ø¬¬
*   Rozszerzanie liczb do formularza programu word, w tym Dolar lub euro kwot
*   Umlaut są akceptowane tylko w przypadku, o, u; inne zostaną zastąpione przez "th" lub odrzucone

Poniżej przedstawiono kilka przykładów

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| Pierścień Frankfurter | pierścień Frankfurter |
| ¡Eine Frage! | eine frage |
| szwajcarski WIR, haben | haben szwajcarski WIR |

Dotyczy następujących normalizacji tekstu przed jego zaimportowaniem.

*   Punkt dziesiętny powinien być ","i nie"."
*   Separator godziny między godzinach i minutach powinien być ":"i nie".": 12:00 Uhr
*   Skróty, takie jak "ca". nie są zastępowane. Zaleca się, że używasz pełnej postaci.
*   Pięć głównych operatorów matematycznych zostaną usunięte: +, -, \*, /. Firma Microsoft zaleca zamianę literału formularza: plusa/minusa nieprawidłowo, geteilt.
*   Operatory porównania (<>, =) — dotyczy gleich, kleiner als, grösser als
*   Użyj jako ułamki, takie jak 3/4 w postaci programu word (na przykład "viertel drei" zamiast ¾)
*   Zamień symbol — możliwość ciągłego dostosowywania formularza słowo "Euro"

Oto kilka przykładów.

| Oryginalny tekst | Po jego normalizacji | Po normalizacji systemu
|--------  | ----- | -------- |
| ES ISTA 12.23Uhr | 12:23Uhr ISTA ES | ES ISTA zwölf uhr drei i zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf ||
| 2 + 3 – 4 | 2 i 3 minus 4 | zwei oraz drei minus vier|

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
