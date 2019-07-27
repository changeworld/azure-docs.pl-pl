---
title: Przygotowywanie danych testowych dla usługi Custom Speech-Speech
titleSuffix: Azure Cognitive Services
description: Bez względu na to, czy testujesz, jak Precyzyjna funkcja rozpoznawania mowy firmy Microsoft jest zgodna z własnymi modelami, będziesz potrzebować danych (w postaci audio i/lub tekstu). Na tej stronie omówiono typy danych, sposób ich użycia i sposób zarządzania nimi.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 8821ce46c65ac8bca36f006ef77bcaf475b0573d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559621"
---
# <a name="prepare-data-for-custom-speech"></a>Przygotuj dane dla Custom Speech

Bez względu na to, czy testujesz, jak Precyzyjna funkcja rozpoznawania mowy firmy Microsoft jest zgodna z własnymi modelami, będziesz potrzebować danych w postaci audio i tekstu. Na tej stronie omówiono typy danych, sposób ich użycia i sposób zarządzania nimi.

## <a name="data-types"></a>Typy danych

Ta tabela zawiera listę akceptowanych typów danych, gdy należy użyć poszczególnych typów danych i zalecanej ilości. Do utworzenia modelu nie jest wymagany każdy typ danych. Wymagania dotyczące danych będą się różnić w zależności od tego, czy tworzysz test czy uczenie modelu.

| Typ danych | Używane do testowania | Ilość | Używany do szkolenia | Ilość |
|-----------|-----------------|----------|-------------------|----------|
| [Audio](#audio-data-for-testing) | Tak<br>Używane na potrzeby inspekcji wizualnej | 5 plików audio | Nie | Nie dotyczy |
| [Zapisy audio + oznakowane przez człowieka](#audio--human-labeled-transcript-data-for-testingtraining) | Yes<br>Służy do obliczania dokładności | 0,5 – 5 godzin audio | Yes | 1 – 1 000 godzin audio |
| [Powiązany tekst](##related-text-data-for-training) | Nie | Nie dotyczy | Yes | 1-200 MB powiązanego tekstu |

Pliki powinny być pogrupowane według typu w zestawie danych i przekazywane jako plik zip. Każdy zestaw danych może zawierać tylko jeden typ danych.

## <a name="upload-data"></a>Przekazywanie danych

Gdy wszystko będzie gotowe do przekazania danych, kliknij przycisk **Przekaż dane** , aby uruchomić kreatora i utworzyć swój pierwszy zestaw danych. Przed umożliwieniem przekazania danych użytkownik zostanie poproszony o wybranie typu danych mowy dla zestawu danych.

![Wybierz dźwięk z portalu mowy](./media/custom-speech/custom-speech-select-audio.png)

Każdy przekazywany zestaw danych musi spełniać wymagania dotyczące wybranego typu danych. Ważne jest, aby poprawnie sformatować dane przed ich przekazaniem. Gwarantuje to, że dane będą prawidłowo przetwarzane przez usługę Custom Speech. Wymagania są wymienione w poniższych sekcjach.

Po przekazaniu zestawu danych można korzystać z kilku opcji:

* Możesz przejść do karty **testowanie** i wizualnie sprawdzić dane dotyczące transkrypcji audio lub audio oraz ludzkich etykiet.
* Możesz przejść do karty **uczenie** i użyć danych audio + ludzkiego transkrypcji lub powiązanych danych tekstowych do uczenia modelu niestandardowego.

## <a name="audio-data-for-testing"></a>Dane audio do testowania

Dane audio są optymalne do testowania dokładności linii bazowej "zamiany mowy na tekst" firmy Microsoft lub modelu niestandardowego. Należy pamiętać, że dane audio są używane do sprawdzenia dokładności mowy w odniesieniu do wydajności określonego modelu. Jeśli chcesz określić liczbę dokładności dla modelu, użyj [audio + oznakowane dane transkrypcji](#audio--human-labeled-transcript-data-for-testingtraining).

Użyj tej tabeli, aby upewnić się, że pliki audio są poprawnie sformatowane do użytku z Custom Speech:

| Właściwość | Value |
|----------|-------|
| Format pliku | RIFF (WAV) |
| Częstotliwość próbkowania | 8 000 Hz lub 16 000 Hz |
| Kanały | 1 (mono) |
| Maksymalna długość na dźwięk | 2 godziny |
| Przykładowy format | PCM, 16-bitowe |
| Format archiwum | .zip |
| Maksymalny rozmiar archiwum | 2 GB |

Jeśli dźwięk nie spełnia tych właściwości lub chcesz sprawdzić, czy jest to konieczne, zalecamy pobranie [SOX](http://sox.sourceforge.net) w celu sprawdzenia lub przekonwertowania dźwięku. Poniżej przedstawiono kilka przykładów działania poszczególnych działań za pomocą wiersza polecenia:

| Działanie | Opis | SOX — polecenie |
|----------|-------------|-------------|
| Sprawdź format dźwięku | Użyj tego polecenia, aby sprawdzić format pliku dźwiękowego. | `sox --i <filename>` |
| Konwertuj format audio | Użyj tego polecenia, aby skonwertować plik audio na pojedynczy kanał, 16-bitowy, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Audio + oznakowane przez człowieka dane zapisu do testowania/uczenia

Aby zmierzyć dokładność dokładności zamiany mowy na tekst firmy Microsoft podczas przetwarzania plików audio, należy zapewnić, aby porównanie było oznaczone przez człowieka (słowo-by-Word). Chociaż transkrypcja przez człowieka jest często czasochłonna, konieczne jest oszacowanie dokładności i uczenie modelu dla przypadków użycia. Należy pamiętać, że ulepszenia w zakresie rozpoznawania będą tylko tak dobre jak dostarczone dane. Z tego powodu ważne jest, aby przekazywać tylko transkrypcje o wysokiej jakości.  

| Właściwość | Wartość |
|----------|-------|
| Format pliku | RIFF (WAV) |
| Częstotliwość próbkowania | 8 000 Hz lub 16 000 Hz |
| Kanały | 1 (mono) |
| Maksymalna długość na dźwięk | 60 s |
| Przykładowy format | PCM, 16-bitowe |
| Format archiwum | .zip |
| Maksymalny rozmiar pliku zip | 2 GB |

Aby rozwiązać problemy, takie jak usuwanie lub podstawianie wyrazów, wymagana jest znaczna ilość danych w celu usprawnienia rozpoznawania. Ogólnie rzecz biorąc, zaleca się dostarczenie transkrypcji słów-by-Word dla około 10 do 1 000 godzin. Transkrypcje dla wszystkich plików WAV powinny znajdować się w jednym pliku tekstowym (zwykły tekst). Każdy wiersz pliku z transkrypcją powinien zawierać nazwę jednego z plików dźwiękowych, a następnie odpowiednią transkrypcję. Nazwę pliku i transkrypcję należy rozdzielać przy użyciu tabulatora (\t).

  Przykład:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> Transkrypcja powinna być kodowana za pomocą kodowania UTF-8 ze znacznikiem kolejności bajtów (BOM).

Transkrypcje są normalizowane pod względem tekstu, aby mogły być przetwarzane przez system. Istnieją jednak pewne istotne normalizacje, które muszą być wykonane przez użytkownika _przed_ przekazaniem danych do usługi Custom Speech Service. Aby uzyskać odpowiedni język do użycia podczas przygotowywania transkrypcji, zobacz [jak utworzyć transkrypcję](how-to-custom-speech-human-labeled-transcriptions.md) z oznaczeniem ludzkim

Po zebraniu plików audio i odpowiednich transkrypcji należy je spakować jako jeden plik zip przed przekazaniem do portalu Custom Speech. Jest to przykładowy zestaw danych z trzema plikami audio i plik transkrypcji z etykietą o podanych przez człowieka:

![Wybierz dźwięk z portalu mowy](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Powiązane dane tekstowe do szkolenia

Jeśli posiadasz nazwy produktów lub funkcje, które są unikatowe, i chcesz się upewnić, że są one rozpoznawane prawidłowo, ważne jest, aby uwzględnić powiązane dane tekstowe do szkolenia. W celu usprawnienia rozpoznawania można podać dwa typy powiązanych danych tekstowych:

| Typ danych | Jak te dane ulepszają rozpoznawanie |
|-----------|------------------------------------|
| Wyrażenia długości i/lub zdania | Mogą one zwiększyć dokładność podczas rozpoznawania nazw produktów lub słownika właściwych dla branż w kontekście zdania. |
| Wymowy | Mogą one poprawić wymowę nietypowych warunków, akronimów lub innych słów z niezdefiniowanymi wymowiemi. |

Wyrażenia długości można podać jako jeden lub wiele plików tekstowych. Im bliżej danych tekstowych jest wypowiadane, tym większe prawdopodobieństwo poprawy dokładności. Wymowy należy dostarczyć jako pojedynczy plik tekstowy. Wszystkie elementy można spakować jako jeden plik zip i przekazać je do portalu Custom Speech.

### <a name="guidelines-to-create-an-utterances-file"></a>Wskazówki dotyczące tworzenia pliku wyrażenia długości

Aby utworzyć niestandardowy model przy użyciu pokrewnego tekstu, należy podać listę przykładowych wyrażenia długości. Te wyrażenia długości nie muszą być pełnymi zdaniami ani gramatycznie prawidłowymi, ale muszą dokładnie odzwierciedlać dane wejściowe, których oczekujesz w środowisku produkcyjnym. Jeśli chcesz, aby niektóre terminy miały zwiększoną wagę, możesz dodać kilka zdań do powiązanego pliku danych, które zawierają te określone warunki.

Użyj tej tabeli, aby upewnić się, że plik powiązane dane dla wyrażenia długości został poprawnie sformatowany:

| Właściwość | Value |
|----------|-------|
| Kodowanie tekstu | UTF-8 BOM |
| Liczba wypowiedzi w wierszu | 1 |
| Maksymalna wielkość pliku | 200 MB |

Ponadto należy uwzględnić następujące ograniczenia:

* Unikaj powtarzania znaków więcej niż cztery razy. Na przykład: "aaaa" lub "uuuu".
* Nie używaj znaków specjalnych ani znaków UTF-8 powyżej U + 00A1.
* Identyfikatory URI zostaną odrzucone.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Wskazówki dotyczące tworzenia pliku wymowy

Jeśli istnieją nietypowe terminy bez standardowych wymowy, których użytkownicy będą napotykać lub których używają, możesz podać niestandardowy plik wymowy, aby poprawić rozpoznawanie.

> [!IMPORTANT]
> Nie zaleca się korzystania z tej funkcji w celu zmiany wymowy typowych wyrazów.

Obejmuje to przykłady mówionych wypowiedź i niestandardową wymowę dla każdej z nich:

| Rozpoznany/wyświetlany formularz | Mówionej formy |
|--------------|--------------------------|
| 3CPO | trzy c p o |  
| CNTK | c n t k |
| IEEE | Potrójna e |

Mówiony formularz jest wyszukiwaną sekwencją fonetyczną. Może składać się z litery, słów, sylab lub kombinacji wszystkich trzech.

Dostosowana wymowa jest dostępna w języku angielskim (EN-US) i niemiecki (de-DE). W tej tabeli przedstawiono obsługiwane znaki według języka:

| Język | Ustawienia regionalne | Znaki |
|----------|--------|------------|
| Angielski | en-US | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| niemiecki | de-DE. | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

Użyj tej tabeli, aby upewnić się, że plik powiązanych danych dla wymowy jest sformatowany prawidłowo. Pliki wymowy są małe i nie powinny przekraczać kilku artykułów bazy wiedzy.

| Właściwość | Wartość |
|----------|-------|
| Kodowanie tekstu | BOM UTF-8 (ANSI jest również obsługiwany w języku angielskim) |
| Liczba wymowy na wiersz | 1 |
| Maksymalna wielkość pliku | 1 MB (1 KB dla warstwy Bezpłatna) |

## <a name="next-steps"></a>Kolejne kroki

* [Inspekcja danych](how-to-custom-speech-inspect-data.md)
* [Oceń dane](how-to-custom-speech-evaluate-data.md)
* [Uczenie modelu](how-to-custom-speech-train-model.md)
* [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)
