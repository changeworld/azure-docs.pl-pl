---
title: Przygotować dane testowe do niestandardowego mowy — usługi mowy
titlesuffix: Azure Cognitive Services
description: Czy testujesz Zobacz jak dokładny rozpoznawanie mowy firmy Microsoft jest lub szkolenia własnych modeli, konieczne będzie danych (w formie audio i/lub tekst). Na tej stronie omówione typy danych, sposobu ich używania oraz jak nimi zarządzać.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: af801600eebed7c0d4ff01dd1edf01fa595840eb
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785772"
---
# <a name="prepare-data-for-custom-speech"></a>Przygotowuje dane do Custom Speech

Czy testujesz Zobacz jak dokładny rozpoznawanie mowy firmy Microsoft jest lub szkolenia własnych modeli, będzie potrzebne są dane w postaci audio i tekst. Na tej stronie omówione typy danych, sposobu ich używania oraz jak nimi zarządzać.

## <a name="data-types"></a>Typy danych

Poniższa tabela zawiera listę akceptowane typy danych, podczas każdego typu danych powinny być używane, a zalecana ilość. Nie każdy typ danych jest wymagany do tworzenia modelu. Wymagania dotyczące danych różnią się w zależności od tego, czy podczas tworzenia testu lub uczenia modelu.

| Typ danych | Używane do testowania | Ilość | Używany na potrzeby szkolenia | Ilość |
|-----------|-----------------|----------|-------------------|----------|
| [Audio](#audio-data-for-testing) | Tak<br>Używane do inspekcji wizualnego | pliki audio 5 + | Nie | n/d |
| [Transkrypcje audio i oznaczone przez człowieka](#audio--human-labeled-transcript-data-for-testingtraining) | Tak<br>Używane do oceny dokładności | 0,5 - 5 godzin audio | Tak | 1 – 1000 godzin danych audio |
| [Dowolny tekst związany z](##related-text-data-for-training) | Nie | n/d | Tak | 1 – 200 MB dowolny tekst związany z |

Pliki powinny pogrupowane według typu do zestawu danych i przekazać w formie pliku zip. Każdy zestaw danych może zawierać tylko jednego typu danych.

## <a name="upload-data"></a>Przekazywanie danych

Gdy wszystko będzie gotowe do przekazania danych, kliknij przycisk **przekazywanie danych** Aby uruchomić kreatora i utworzyć swój pierwszy zestaw danych. Zostanie wyświetlony monit o wybranie mowy typu danych dla zestawu danych, przed zezwoleniem na przekazywanie danych.

![Wybieranie urządzenia audio z poziomu portalu mowy](./media/custom-speech/custom-speech-select-audio.png)

Każdy zestaw danych, które zostaną przesłane, musi spełniać wymagania dla typu danych, który wybierzesz. Jest to ważne, aby poprawnie sformatować dane przed przesłaniem. Gwarantuje to, że dane będą dokładnie przetwarzane przez usługę Custom Speech. Wymagania są wymienione w poniższych sekcjach.

Po przekazaniu zestawu danych, możesz skorzystać z kilku opcji:

* Możesz przejść do **testowania** karcie oraz wizualnie badać tylko audio lub danych transkrypcji audio i oznaczone przez człowieka.
* Możesz przejść do **szkolenia** kartę i nam danych transkrypcji audio + przez człowieka lub dowolny tekst związany z danych do nauczenia modelu niestandardowego.

## <a name="audio-data-for-testing"></a>Dane audio do testowania

Dane audio jest optymalna do testowania dokładności modelu mowy na tekst linii bazowej firmy Microsoft lub modelu niestandardowego. Należy pamiętać, dane audio są używane do kontroli dokładności rozpoznawania mowy w odniesieniu do wydajności określonego modelu. Jeśli szukasz określają ilościowo dokładności modelu, należy użyć [danych transkrypcji audio i oznaczone przez człowieka](#audio--human-labeled-transcript-data-for-testingtraining).

Upewnij się, czy plików audio są prawidłowo sformatowane do użycia z usługą mowy niestandardowego przy użyciu tej tabeli:

| Właściwość | Wartość |
|----------|-------|
| Format pliku | RIFF (WAV) |
| Częstotliwość próbkowania | 8000 Hz lub 16 000 Hz |
| Kanały | 1 (mono) |
| Maksymalna długość dla audio | 2 godziny |
| Przykładowy format | PCM, 16-bitowych |
| Format archiwum | .zip |
| Rozmiar maksymalny archiwum | 2 GB |

Jeśli chcesz sprawdzić, jeśli nie audio nie spełniają tych właściwości, zalecamy pobranie [sox](http://sox.sourceforge.net) Sprawdź lub konwertowanie dźwięku. Poniżej przedstawiono przykłady jak każdy z tych działań może odbywać się za pośrednictwem wiersza polecenia:

| Działanie | Opis | Polecenie SOx |
|----------|-------------|-------------|
| Sprawdź audio format | Użyj tego polecenia, aby sprawdzić format pliku audio. | `soxi <filename>.wav` |
| Konwertuj audio format | Użyj tego polecenia, aby przekonwertować plik dźwiękowy pojedynczy kanał, 16-bitowych 48 KHz. | `sox <filename>.wav -b 16 -3 signed-integer -c l -r 48k -t wav <filename>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Dane transkrypcji audio i oznaczone przez człowieka testowania szkolenia

Do mierzenia dokładności dokładność mowy na tekst firmy Microsoft, podczas przetwarzania plików audio, musisz podać oznaczone przez człowieka transkrypcje (word, word) do porównania. Oznaczone przez człowieka transkrypcji często jest czasochłonne, konieczne jest do oceny dokładności i do nauczenia modelu, dla Twojego przypadków użycia. Należy pamiętać, ulepszenia rozpoznawanie tylko dobrą podanych danych. Z tego powodu jest ważne, czy tylko wysokiej jakości zapisy są przekazywane.  

| Właściwość | Wartość |
|----------|-------|
| Format pliku | RIFF (WAV) |
| Częstotliwość próbkowania | 8000 Hz lub 16 000 Hz |
| Kanały | 1 (mono) |
| Maksymalna długość dla audio | 60 s |
| Przykładowy format | PCM, 16-bitowych |
| Format archiwum | .zip |
| Rozmiar maksymalny zip | 2 GB |

Aby rozwiązać problemy z, takie jak word usunięcie lub zastąpienie, znacznej ilości danych jest wymagana w celu rozpoznawania. Ogólnie rzecz biorąc zaleca się podanie transkrypcje programu word, wyraz około 10 do 1000 godzin audio. Transkrypcje dla wszystkich plików WAV powinny znajdować się w jednym pliku tekstowym (zwykły tekst). Każdy wiersz pliku z transkrypcją powinien zawierać nazwę jednego z plików dźwiękowych, a następnie odpowiednią transkrypcję. Nazwę pliku i transkrypcję należy rozdzielać przy użyciu tabulatora (\t).

  Na przykład:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> Transkrypcja powinna być kodowana za pomocą kodowania UTF-8 ze znacznikiem kolejności bajtów (BOM).

Transkrypcje są normalizowane pod względem tekstu, aby mogły być przetwarzane przez system. Istnieją jednak pewne istotne normalizacje, które muszą być wykonane przez użytkownika _przed_ przekazaniem danych do usługi Custom Speech Service. Dla odpowiedniego języka do użycia podczas przygotowywania usługi transkrypcji, zobacz [jak utworzyć tekst z etykietą człowieka](how-to-custom-speech-human-labeled-transcriptions.md)

Po zebrane pliki audio i odpowiednie transkrypcje, powinny być dostarczana w jednego pliku zip przed przekazaniem do portalu usługi Custom Speech. Jest to zestaw przykładzie trzy pliki audio i plik transkrypcji oznaczone przez człowieka:

![Wybieranie urządzenia audio z poziomu portalu mowy](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Dowolny tekst związany z dane szkoleniowe

Jeśli masz nazw produktów lub funkcje, które są unikatowe, i chcesz upewnij się, że są rozpoznawane prawidłowo, należy uwzględniają dowolny tekst związany z dane szkoleniowe. Można podać dwa rodzaje dowolny tekst związany z danych w celu rozpoznawania:

| Typ danych | Jak te dane zwiększa rozpoznawania |
|-----------|------------------------------------|
| Wypowiedzi i/lub zdania | Te zwiększenia dokładności, przy rozpoznawaniu nazw produktów lub branżowych słownictwa w kontekście zdania. |
| Wymowy | Te można poprawić Wymowa wyrazów niczym niezwykłym, akronimów lub innymi słowy wymowy niezdefiniowane. |

Wypowiedzi można podać jako jedną lub wiele plików. Im bliżej tekst, który ma co będą wypowiadane dane, tym większe prawdopodobieństwo zwiększona dokładności. Należy podać wymowy jako pojedynczego pliku tekstowego. Wszystko, czego można spakowany jako plik zip pojedynczego i przekazywane do portalu usługi Custom Speech.

### <a name="guidelines-to-create-an-utterances-file"></a>Wytyczne dotyczące tworzenia pliku wypowiedzi

Aby utworzyć niestandardowy model przy użyciu dowolny tekst związany z, należy podać listę wypowiedzi próbki. Te wypowiedzi nie trzeba być w postaci kompletnych zdań lub gramatycznie poprawić, ale musi to dokładnie odzwierciedlać dane głosowe, których oczekujesz w środowisku produkcyjnym. Chcąc większą wagę niektórych terminów, można dodać kilka zdań do pliku powiązanych danych, które zawierają te określone warunki.

Upewnij się, jest poprawnie sformatowany plik powiązanych danych dla wypowiedzi przy użyciu tej tabeli:

| Właściwość | Wartość |
|----------|-------|
| Kodowanie tekstu | UTF-8 BOM |
| Liczba wypowiedzi w wierszu | 1 |
| Maksymalna wielkość pliku | 200 MB |

Ponadto należy uwzględnić następujące ograniczenia:

* Należy unikać powtarzania znaków więcej niż cztery razy. Na przykład: "aaaa" lub "uuuu".
* Nie należy używać znaków specjalnych ani znaków UTF-8 powyżej 00A1 U +.
* Identyfikatory URI zostanie odrzucone.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Wskazówek, aby utworzyć plik Wymowa

W przypadku nietypowych warunków bez wymowy standardowych, które użytkownicy będą występować lub użyj można udostępnić plik Wymowa niestandardowych w celu rozpoznawania.

> [!IMPORTANT]
> Nie zaleca się używać tej funkcji, aby zmienić Wymowa popularne wyrazy.

Obejmuje to przykłady prowadzone wypowiedź i Wymowa niestandardowe dla każdego:

| Mówionej formy | Rozpoznany wyświetlony formularz |
|--------------|--------------------------|
| o trzy p c | 3CPO |  
| k k, n t | CNTK |
| mogę potrójne e | IEEE |

Mówionej formy jest sekwencja fonetycznych województw. Mogą się składać list, słów, sylab lub kombinacji tych trzech miejsc.

Wymowa dostosowane jest dostępna w języku angielskim (en US) i niemiecki (de-DE). W poniższej tabeli przedstawiono znaki obsługiwane przez język:

| Język | Ustawienia regionalne | Znaki |
|----------|--------|------------|
| Angielski | en-US | , b, c, d, e, f, g, h, i, "j", k, l, m, n, o, p, pytania i odpowiedzi, r, s, t, u, v, w, x, y, z |
| Niemiecki | de-DE. | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

Upewnij się, jest poprawnie sformatowany plik powiązanych danych dla wymowy przy użyciu tej tabeli. Wymowa pliki są małe i nie powinna przekraczać kilka artykułów bazy wiedzy.

| Właściwość | Wartość |
|----------|-------|
| Kodowanie tekstu | Znak BOM UTF-8 (ANSI jest również obsługiwany w języku angielskim) |
| Liczba wymowy na wiersz | 1 |
| Maksymalna wielkość pliku | 1 MB (1 KB w warstwie bezpłatna) |

## <a name="next-steps"></a>Kolejne kroki

* [Sprawdzanie danych](how-to-custom-speech-inspect-data.md)
* [Oceń swoje dane](how-to-custom-speech-evaluate-data.md)
* [Uczenie modelu](how-to-custom-speech-train-model.md)
* [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)
