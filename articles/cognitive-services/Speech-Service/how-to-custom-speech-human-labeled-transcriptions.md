---
title: Wytyczne oznaczone przez człowieka transkrypcje — usługi mowy
titlesuffix: Azure Cognitive Services
description: Jeśli szukasz w celu zwiększenia dokładności rozpoznawania, szczególnie w przypadku problemów, które powstają, gdy są niepoprawnie zastąpiona, lub usunąć słów należy użyć oznaczone przez człowieka transkrypcji oraz dane audio. Co to są oznaczone przez człowieka transkrypcje? Można w prosty sposób, są one programu word, wyraz, verbatim transkrypcje pliku audio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: ba6d137a7eb5fc2c56bfc39aa8f831d76411fb46
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060857"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Jak utworzyć transkrypcje oznaczone przez człowieka

Jeśli szukasz w celu zwiększenia dokładności rozpoznawania, szczególnie w przypadku problemów, które powstają, gdy są niepoprawnie zastąpiona, lub usunąć słów należy użyć oznaczone przez człowieka transkrypcji oraz dane audio. Co to są oznaczone przez człowieka transkrypcje? Można w prosty sposób, są one programu word, wyraz, verbatim transkrypcje pliku audio.

Duże przykładowych danych transkrypcji jest wymagany w celu rozpoznawania, Sugerujemy, zapewniając od 10 do 1000 godzin danych transkrypcji. Na tej stronie omówimy wskazówki ułatwiają tworzenie wysokiej jakości transkrypcji. Ten przewodnik został podzielony przez ustawienia regionalne z sekcjami US English, mandaryński chiński i niemieckim.

## <a name="us-english-en-us"></a>Angielski (US) (en US)

Oznaczone przez człowieka transkrypcji dla angielskiego audio, musi być podana jako zwykły tekst, tylko przy użyciu znaków ASCII. Unikaj stosowania Latin-1 lub Unicode znaki interpunkcyjne. Następujące znaki są często nieumyślnie dodawane, gdy kopiowanie tekstu z edytora lub oskrobaniu danych ze stron sieci web. Jeśli te znaki są obecne, pamiętaj je zaktualizować za pomocą odpowiednich podstawienia ASCII.

Oto kilka przykładów:

| Znaki, aby uniknąć | Podstawienia | Uwagi |
|---------------------|--------------|-------|
| "Hello world" | "Hello world" | Znaki cudzysłowu otwierający i zamykający zostać podstawione odpowiednie znaki ASCII. |
| John's dnia | John's dnia | Apostrof został zastąpiony z odpowiedni znak ASCII. |
| było to dobrym — nie było wspaniałe! | dobre — został nie, ale jest to doskonały! | Znak pauzy zostało zastąpione dwa łączniki. |

### <a name="text-normalization-for-us-english"></a>Normalizacja tekstu dla US English

Tekst normalizacji jest przekształcenie słów w jednolitym formacie używane podczas uczenia modelu. Niektóre reguły normalizacji są automatycznie stosowane do tekstu, jednak zaleca się używanie tych wytycznych podczas przygotowywania danych oznaczone przez człowieka transkrypcji:

* Zapisać skróty w słowach.
* Zapisać niestandardowych ciągów liczbowych w słowach (na przykład księgowości).
* Jak wymowa powinien transkrybowanego znaki spoza alfabetu lub mieszane znaki alfanumeryczne.
* Skróty, które są wymawiane jako słowa nie należy edytować (na przykład "radarowy", "laserowej", "RAM" lub "NATO").
* Zapis się skróty, które są wymawiane jako osobne litery od każdej litery rozdzielonych spacją.

Poniżej przedstawiono kilka przykładów normalizacji, które należy wykonać na transkrypcji:

| Oryginalny tekst | Tekst po normalizacji |
|---------------|--------------------------|
| Dr. Bruce transparentu | Transparent Bruce lekarzem |
| James Bond, 007 | Niestety dwukrotnie James Bond siedem |
| Ke$ha | Kesha |
| Jak długo trwa 2 x 4 | Jak długo trwa dwa, cztery |
| Spotkania przechodzi z 1-15: 00 | Spotkania przechodzi od jednej do trzech pm |
| Mój typ krwi jest O + | Mój typ krwi jest dodatnia O |
| to H20 | to O 2 godz. |
| Odtwórz OU812 przez Van Halen | Odtwórz U O 2 1 8 przez Van Halen |
| UTF-8 z BOM | U T F 8 z BOM |

Następujące reguły normalizacji są automatycznie stosowane do transkrypcje:

* Używać małych liter.
* Usuń wszystkie znaki interpunkcyjne, z wyjątkiem apostrofy w wyrazach.
* Rozwiń liczb w formie wymawiane/słów, takich jak wartości pieniężne.

Poniżej przedstawiono kilka przykładów normalizacji automatycznie są wykonywane na transkrypcji:

| Oryginalny tekst | Tekst po normalizacji |
|---------------|--------------------------|
| "Joseph krowy!" wspomniane Batman. | krowy Joseph powiedział batman |
| "Jak?" wspomniane Batman sidekick, działanie okrężne. | jakie wspomniane batman sidekick okrężnego |
| Przejdź pobieranie - em! | Wybierz pozycję Pobierz em |
| Jestem double-jointed | Jestem double jointed |
| 104 Elm Street | co o czterech wiąz ulicy |
| Dostosowywanie do 102.7 | Dostosowywanie do jednego o dwóch siedem |
| Pi jest około 3,14 | pi jest około trzy punktu 1 4 |
To kosztuje $3,14| to kosztuje trzech czternastu |

## <a name="mandarin-chinese-zh-cn"></a>Mandaryński chiński (zh-CN)

Oznaczone przez człowieka transkrypcji dla audio chińskich mandaryński muszą być zakodowane za pomocą znacznika kolejności bajtów UTF-8. Unikaj stosowania znaków interpunkcyjnych połowę szerokości. Te znaki mogą być uwzględniane przypadkowo podczas przygotowywania danych w edytorze tekstu lub scrape danych ze stron sieci web. Jeśli te znaki są obecne, pamiętaj je zaktualizować za pomocą odpowiednich podstawienia pełnej szerokości.

Oto kilka przykładów:

| Znaki, aby uniknąć | Podstawienia | Uwagi |
|---------------------|--------------|-------|
| "你好" | "你好" | Znakami odpowiednie zostać podstawione znaki cudzysłowu otwierający i zamykający. |
| 需要什么帮助? | 需要什么帮助？ | Znak zapytania został zastąpiony odpowiedniego znaku. |

### <a name="text-normalization-for-mandarin-chinese"></a>Normalizacja tekstu dla języka chińskiego mandaryński

Tekst normalizacji jest przekształcenie słów w jednolitym formacie używane podczas uczenia modelu. Niektóre reguły normalizacji są automatycznie stosowane do tekstu, jednak zaleca się używanie tych wytycznych podczas przygotowywania danych oznaczone przez człowieka transkrypcji:

* Zapisać skróty w słowach.
* Zapisać ciągów liczbowych w programie mówionej formy.

Poniżej przedstawiono kilka przykładów normalizacji, które należy wykonać na transkrypcji:

| Oryginalny tekst | Tekst po normalizacji |
|---------------|--------------------------|
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

Następujące reguły normalizacji są automatycznie stosowane do transkrypcje:

* Usuń wszystkie znaki interpunkcyjne
* Rozszerz numery mówionej formy
* Konwertuj znaków o pełnej szerokości na połowę szerokości litery
* Przy użyciu wielkich liter dla wszystkie wyrazy w języku angielskim

Poniżej przedstawiono kilka przykładów normalizacji automatycznie są wykonywane na transkrypcji:

| Oryginalny tekst | Tekst po normalizacji |
|---------------|--------------------------|
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z |W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Niemiecki (de-DE) i innych języków

Oznaczone przez człowieka transkrypcji audio niemiecki (i inne innej niż angielska lub językami chińskimi mandaryński) muszą być zakodowane za pomocą znacznika kolejności bajtów UTF-8. Należy podać jedną etykietą człowieka transkrypcji dla każdego pliku audio.

### <a name="text-normalization-for-german"></a>Normalizacja tekstu dla języka niemieckiego

Tekst normalizacji jest przekształcenie słów w jednolitym formacie używane podczas uczenia modelu. Niektóre reguły normalizacji są automatycznie stosowane do tekstu, jednak zaleca się używanie tych wytycznych podczas przygotowywania danych oznaczone przez człowieka transkrypcji:

*   Zapis separatorów dziesiętnych jako ","i nie".".
*   Zapis separatory czas jako ":"i nie"." (na przykład: 12:00 Uhr).
*   Skróty, takie jak "ca". nie są zastępowane. Zaleca się, że używasz pełnej mówionej formy.
*   Cztery główne operatory matematyczne (+, -, \*, i /) są usuwane. Firma Microsoft zaleca, zastępując je w formie pisemnej: "znak plus," "ujemny," "nieprawidłowo" i "geteilt."
*   Operatory porównania są usuwane (=, <, a >). Firma Microsoft zaleca, zastępując "gleich", "kleiner als," i "grösser als."
*   Zapis ułamki, takie jak 3/4 w formie pisemnej (na przykład: "drei viertel", a nie 3/4).
*   Zamień symbol "—" swoją formę tekstową "Euro."

Poniżej przedstawiono kilka przykładów normalizacji, które należy wykonać na transkrypcji:

| Oryginalny tekst | Tekst po użytkownik normalizacji | Tekst po normalizacji systemu |
|---------------|-------------------------------|---------------------------------|
| ES ISTA 12.23 Uhr | ES ISTA 12:23 Uhr | ES ISTA zwölf uhr drei i zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 2 + 3 - 4 | 2 i 3 minus 4 | zwei plus drei minus vier |

Następujące reguły normalizacji są automatycznie stosowane do transkrypcje:

* Używać małych liter dla całego tekstu.
* Usuń wszystkie znaki interpunkcyjne, w tym różne rodzaje cudzysłowów ("test", "test", "test" i "test" są dozwolone).
* Odrzuć wierszy przy użyciu znaków specjalnych, z tego zestawu: są ¤ §... ą © ¬­ ª® ° granicach ² m x ÿ Ø¬¬.
* Rozwiń numery mówionej formy, tym Dolar lub ilości Euro.
* Zaakceptuj umlaut tylko w przypadku, o, a. Inne osoby zostanie zastąpione przez "th" lub odrzucone.

Poniżej przedstawiono kilka przykładów normalizacji automatycznie są wykonywane na transkrypcji:

| Oryginalny tekst | Tekst po normalizacji |
|---------------|--------------------------|
| Pierścień Frankfurter | pierścień Frankfurter |
| ¡Eine Frage! | eine frage |
| szwajcarski WIR, haben | haben szwajcarski WIR |

## <a name="next-steps"></a>Następne kroki

* [Przygotuj i przetestować danych](how-to-custom-speech-test-data.md)
* [Sprawdzanie danych](how-to-custom-speech-inspect-data.md)
* [Oceń swoje dane](how-to-custom-speech-evaluate-data.md)
* [Uczenie modelu](how-to-custom-speech-train-model.md)
* [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)
