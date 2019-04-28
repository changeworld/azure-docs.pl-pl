---
title: Wytyczne transkrypcji dla szkolenie modeli usług przetwarzania mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przygotować tekstu do dostosowania akustyczne i modeli językowych i głosów dla usług przetwarzania mowy.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: panosper
ms.openlocfilehash: 0d7508ed9cf1807fa05c57a1d60c804af7d2244f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461446"
---
# <a name="transcription-guidelines-for-using-the-speech-service"></a>Tekst wskazówki dotyczące używania usługi mowy

Aby dostosować **zamiana mowy na tekst** lub **zamiany tekstu na mowę**, musisz podać tekst i mowy. Każdy wiersz w tekście odnosi się do pojedynczego wypowiedź. Tekst powinien odpowiadać mowy w możliwie najlepszy sposób. Tekst jest nazywany *transkrypcji*, i należy ją utworzyć w określonym formacie.

Usługi mowy normalizacji danych wejściowych zapewnienie spójnego tekstu.

W tym artykule opisano oba rodzaje normalizations. Wytyczne się nieco różnić dla różnych języków.

## <a name="us-english-en-us"></a>Angielski (US) (en-us)

Tekst dane powinny zostać zapisane wypowiedź jeden na wiersz, w postaci zwykłego tekstu, przy użyciu zestawu znaków ASCII.

Unikaj stosowania rozszerzonych (Latin-1) i znaków interpunkcyjnych Unicode. Te znaki mogą być uwzględnione przypadkowo podczas przygotowywania danych w edytorze tekstu lub scrape danych na stronach sieci Web. Zastąp znaki odpowiednie podstawienia ASCII. Na przykład:

| Znaki, aby uniknąć | Podstawienia |
|----- | ----- |
| "Hello world" (Otwieranie i zamykanie podwójnego cudzysłowu) | "Hello world" (podwójny cudzysłów) |
| John's dnia (prawy pojedynczy cudzysłów) | John's dnia (apostrof) |
| było to dobrym — nie było wspaniałe! (pauza) | dobre — został nie, ale jest to doskonały! (Łączniki) |

### <a name="text-normalization-rules-for-english"></a>Tekst reguł normalizacji w języku angielskim

Usługi mowy wykonuje następujące reguły normalizacji:

* Przy użyciu małych liter dla całego tekstu
* Usunięcie wszystkich znaków interpunkcyjnych z wyjątkiem apostrofy wewnętrznego programu word
* Rozszerzanie liczb do mówionej formy, w tym dolarach

Oto kilka przykładów:

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| "Joseph krowy!" wspomniane Batman. | krowy Joseph powiedział batman |
| "Jak?" wspomniane Batman sidekick, działanie okrężne. | jakie wspomniane batman sidekick okrężnego |
| Przejdź pobieranie - em! | Wybierz pozycję Pobierz em |
| Jestem double-jointed | Jestem double jointed |
| 104 Elm Street | co o czterech wiąz ulicy |
| Dostosowywanie do 102.7 | Dostosowywanie do jednego o dwóch siedem |
| Pi jest około 3,14 | pi jest około trzy punktu 1 4 |
| To kosztuje $3,14 | to kosztuje trzech czternastu |

Stosuje się następujące normalizacji do Twojej zapisy tekstu:

* Skróty powinny być zapisany w słowach.
* Niestandardowe ciągi numeryczne (na przykład niektóre daty lub formularzy księgowości) powinien napisanych w słowach.
* Wyrazy z znaki spoza alfabetu lub mieszane znaki alfanumeryczne powinny transkrybowanego, jak wymowa.
* Pozostaw skróty, które są wymawiane jako słowa w charakterze (na przykład "radarowy", "laserowa," "RAM" lub "NATO").
* Zapis litery skrótów, które są wymawiane jako osobne, za pomocą litery rozdzielone spacjami (na przykład "IBM," "CPU", "FBI", "Do ustalenia" lub "NaN").

Oto kilka przykładów:

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| 14 NE 3 odzyskiwania po awarii. | czternastu dysku trzeci północno-wschodnie |
| Dr. Bruce transparentu | Transparent Bruce lekarzem |
| James Bond, 007 | Niestety dwukrotnie James Bond siedem |
| Ke$ha | Kesha |
| Jak długo trwa 2 x 4 | Jak długo trwa dwa, cztery |
| Spotkania przechodzi z 1-15: 00 | Spotkania przechodzi od jednej do trzech pm |
| Mój typ krwi jest O + | Mój typ krwi jest dodatnia O |
| to H20 | to O 2 godz. |
| Odtwórz OU812 przez Van Halen | Odtwórz U O 2 1 8 przez Van Halen |
| UTF-8 z BOM | U T F 8 z BOM |

## <a name="chinese-zh-cn"></a>Chiński (zh-cn)

Dane tekstowe, który zostanie przekazany do usług przetwarzania mowy niestandardowego należy używać kodowania UTF-8 za pomocą znacznika kolejności bajtów. Wypowiedź jeden na wiersz można zapisać pliku.

Unikaj stosowania znaków interpunkcyjnych połowę szerokości. Te znaki mogą być uwzględnione przypadkowo podczas przygotowywania danych w edytorze tekstu lub scrape danych na stronach sieci Web. Zastąp je odpowiednie podstawienia pełnej szerokości. Na przykład:

| Znaki, aby uniknąć | Podstawienia |
|----- | ----- |
| "你好" (Otwieranie i zamykanie podwójnego cudzysłowu) | "你好" (cudzysłów) |
| 需要什么帮助? (znak zapytania) | 需要什么帮助？ |

### <a name="text-normalization-rules-for-chinese"></a>Tekst reguł normalizacji chiński

Usługi mowy wykonuje następujące reguły normalizacji:

* Usuwanie wszystkie znaki interpunkcyjne
* Rozszerzanie liczb do mówionej formy
* Konwertowanie liter połowę szerokości znaków o pełnej szerokości
* Przy użyciu wielkich liter dla wszystkie wyrazy w języku angielskim

Oto kilka przykładów:

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

Przed zaimportowaniem tekstu, należy stosować następujące normalizacji do niej:

* Skróty powinny napisanych w słowach (jak mówionej formy).
* Zapisać ciągów liczbowych w programie mówionej formy.

Oto kilka przykładów:

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Inne języki

Przekazane dane tekstowe **zamiana mowy na tekst** usługi należy użyć kodowania UTF-8 za pomocą znacznika kolejności bajtów. Wypowiedź jeden na wiersz można zapisać pliku.

> [!NOTE]
> W poniższych przykładach używane jest niemiecki. Jednak wytycznych dla wszystkich języków, które nie są US English lub chińskich.

### <a name="text-normalization-rules-for-german"></a>Tekst reguł normalizacji dla języka niemieckiego

Usługi mowy wykonuje następujące reguły normalizacji:

* Przy użyciu małych liter dla całego tekstu
* Usuwanie wszystkie znaki interpunkcyjne, w tym różne rodzaje cudzysłowów ("test", "test", "test" i "test" są dozwolone)
* Odrzucanie wierszy przy użyciu znaków specjalnych z są zestaw ¤ §... ą © ¬­ ª® ° granicach ² m x ÿ Ø¬¬
* Rozszerzanie liczb do formularza programu word, w tym Dolar lub Euro kwoty
* Akceptowanie umlaut tylko w przypadku, o, a; inne zostaną zastąpione przez "th" lub odrzucone

Oto kilka przykładów:

| Oryginalny tekst | Po normalizacji |
|----- | ----- |
| Pierścień Frankfurter | pierścień Frankfurter |
| ¡Eine Frage! | eine frage |
| szwajcarski WIR, haben | haben szwajcarski WIR |

Przed zaimportowaniem tekstu, należy stosować następujące normalizacji do niej:

* Separatorów dziesiętnych powinien być ","i nie".".
* Separatory czas między godzinach i minutach powinien być ":"i nie"." (na przykład, 12:00 Uhr).
* Skróty, takie jak "ca". nie są zastępowane. Zaleca się, że używasz pełnej postaci.
* Cztery główne operatory matematyczne (+, -, \*, i /) są usuwane. Firma Microsoft zaleca zamianę literału formularza: "znak plus," "ujemny," "nieprawidłowo" i "geteilt."
* Ta sama zasada dotyczy operatory porównania (=, <, a >). Firma Microsoft zaleca, zastępując "gleich", "kleiner als," i "grösser als."
* Użyj jako ułamki, takie jak 3/4 w postaci programu word (na przykład "drei viertel" zamiast ¾).
* Zamień symbol — możliwość ciągłego dostosowywania formularza słowo "Euro."

Oto kilka przykładów:

| Oryginalny tekst | Po jego normalizacji | Po normalizacji systemu |
|--------  | ----- | -------- |
| ES ISTA 12.23 Uhr | ES ISTA 12:23 Uhr | ES ISTA zwölf uhr drei i zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 2 + 3 - 4 | 2 i 3 minus 4 | zwei plus drei minus vier|

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz subskrypcję wersji próbnej usługi Speech Services](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
