---
title: Jak przygotować dane dla usługi Custom Voice - Speech
titleSuffix: Azure Cognitive Services
description: Utwórz niestandardowy głos dla swojej marki za pomocą usługi Mowy. Udostępniasz nagrania studyjne i powiązane skrypty, usługa generuje unikalny model głosu dostrojony do nagranego głosu. Ten głos umożliwia syntezę mowy w produktach, narzędziach i aplikacjach.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 5427e9f996fb77d455aa8064fc7cb1c65e1fcf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74805981"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Przygotowywanie danych do utworzenia niestandardowego głosu

Gdy będziesz gotowy do utworzenia niestandardowego głosu zamiany tekstu na mowę dla aplikacji, pierwszym krokiem jest zebranie nagrań audio i skojarzonych skryptów, aby rozpocząć szkolenie modelu głosu. Usługa Mowy używa tych danych do utworzenia unikatowego głosu dostrojonego do głosu w nagraniach. Po przeszkoleniu głosu można rozpocząć syntezę mowy w aplikacjach.

Można rozpocząć od niewielkiej ilości danych, aby utworzyć dowód koncepcji. Jednak im więcej danych podasz, tym bardziej naturalny będzie twój niestandardowy głos. Aby można było trenować własny model głosu zamiany tekstu na mowę, potrzebne będą nagrania audio i związane z nimi transkrypcje tekstu. Na tej stronie przejrzymy typy danych, sposób ich użycia i sposób zarządzania nimi.

## <a name="data-types"></a>Typy danych

Zestaw danych szkolenia głosowego zawiera nagrania audio i plik tekstowy z powiązanymi transkrypcjami. Każdy plik audio powinien zawierać pojedynczy wypowiedź (pojedyncze zdanie lub pojedynczy obrót dla systemu dialogowego) i mieć mniej niż 15 sekund.

W niektórych przypadkach może nie mieć odpowiedniego zestawu danych gotowy i będzie chcesz przetestować niestandardowe szkolenie głosowe z dostępnych plików audio, krótkie lub długie, z lub bez transkrypcji. Dostarczamy narzędzia (beta), które pomogą Ci podzielić dźwięk na wypowiedzi i przygotować transkrypcje za pomocą [interfejsu API transkrypcji wsadowej.](batch-transcription.md)

W tej tabeli wymieniono typy danych oraz sposób, w jaki każdy z nich jest używany do tworzenia niestandardowego modelu głosu zamiany tekstu na mowę.

| Typ danych | Opis | Kiedy stosować | Wymagana dodatkowa usługa | Ilość do szkolenia modelu | Ustawienia regionalne |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Poszczególne wypowiedzi + pasująca transkrypcja** | Kolekcja (.zip) plików audio (.wav) jako pojedyncze wypowiedzi. Każdy plik audio powinien mieć długość 15 sekund lub mniejszą, w połączeniu ze sformatowaną transkrypcją (.txt). | Profesjonalne nagrania z pasującymi transkrypcjami | Gotowy do treningu. | Nie ma twardych wymagań dla en-US i zh-CN. Ponad 2000+ odrębne wypowiedzi dla innych ustawień regionalnych. | [Wszystkie ustawienia regionalne niestandardowego głosu](language-support.md#customization) |
| **Długi dźwięk + transkrypcja (beta)** | Kolekcja (.zip) długich, niesegmentowanych plików audio (dłuższych niż 20 sekund), sparowana z transkrypcją (.txt), która zawiera wszystkie wypowiedziane słowa. | Masz pliki audio i pasujące transkrypcje, ale nie są one podzielone na wypowiedzi. | Segmentacja (przy użyciu transkrypcji partii).<br>Transformacja formatu audio w razie potrzeby. | Brak twardych wymagań  | [Wszystkie ustawienia regionalne niestandardowego głosu](language-support.md#customization) |
| **Tylko dźwięk (wersja beta)** | Kolekcja (.zip) plików audio bez transkrypcji. | Dostępne są tylko pliki audio bez transkrypcji. | Segmentacja + generowanie transkrypcji (przy użyciu transkrypcji partii).<br>Transformacja formatu audio w razie potrzeby.| Brak twardych wymagań | [Wszystkie ustawienia regionalne niestandardowego głosu](language-support.md#customization) |

Pliki powinny być pogrupowane według typu do zestawu danych i przekazywane jako plik zip. Każdy zestaw danych może zawierać tylko jeden typ danych.

> [!NOTE]
> Maksymalna liczba zestawów danych, które mogą być importowane na subskrypcję, to 10 plików zip dla użytkowników korzystających z bezpłatnej subskrypcji (F0) i 500 dla użytkowników subskrypcji standardowej (S0).

## <a name="individual-utterances--matching-transcript"></a>Poszczególne wypowiedzi + pasująca transkrypcja

Można przygotować nagrania poszczególnych wypowiedzi i pasujące transkrypcji na dwa sposoby. Albo napisać skrypt i odczytać przez talent głosowy lub użyć publicznie dostępnego dźwięku i transkrypcji go do tekstu. Jeśli to zrobisz, edytuj disfluencies z plików audio, takich jak "um" i inne dźwięki wypełniacza, zacina się, mumbled słowa lub mispronunciations.

Aby uzyskać dobrą czcionkę głosową, utwórz nagrania w cichym pomieszczeniu z wysokiej jakości mikrofonem. Spójna objętość, szybkość mówienia, mówienie i ekspresyjne maniery mowy są niezbędne.

> [!TIP]
> Aby utworzyć głos do użytku produkcyjnego, zalecamy użycie profesjonalnego studia nagraniowego i talentu głosowego. Aby uzyskać więcej informacji, zobacz [Jak nagrywać próbki głosu dla niestandardowego głosu](record-custom-voice-samples.md).

### <a name="audio-files"></a>Pliki audio

Każdy plik audio powinien zawierać pojedynczą wypowiedź (pojedyncze zdanie lub pojedynczy obrót systemu dialogowego), o długości mniejszej niż 15 sekund. Wszystkie pliki muszą być w tym samym języku mówionym. Wielojęzyczne niestandardowe głosy zamiany tekstu na mowę nie są obsługiwane, z wyjątkiem języka chińsko-angielskiego dwujeziętnego. Każdy plik audio musi mieć unikatową numeryczną nazwę pliku z rozszerzeniem nazwy pliku .wav.

Podczas przygotowywania dźwięku należy postępować zgodnie z tymi wskazówkami.

| Właściwość | Wartość |
| -------- | ----- |
| Format pliku | RIFF (.wav), zgrupowane w pliku zip |
| Częstotliwość próbkowania | Co najmniej 16 000 Hz |
| Przykładowy format | PCM, 16-bitowy |
| Nazwa pliku | Numeryczne, z rozszerzeniem .wav. Nie są dozwolone zduplikowane nazwy plików. |
| Długość dźwięku | Krótszy niż 15 sekund |
| Format archiwum | zip |
| Maksymalny rozmiar archiwum | 2048 MB |

> [!NOTE]
> Pliki .wav o częstotliwości próbkowania niższej niż 16 000 Hz zostaną odrzucone. Jeśli plik .zip zawiera pliki .wav o różnych szybkościach próbkowania, zaimportowane zostaną tylko te równe lub wyższe niż 16 000 Hz. Portal importuje obecnie archiwa .zip do 200 MB. Można jednak przesłać wiele archiwów.

### <a name="transcripts"></a>Transkrypcje

Plik transkrypcji jest plikiem tekstowym. Skorzystaj z tych wskazówek, aby przygotować transkrypcje.

| Właściwość | Wartość |
| -------- | ----- |
| Format pliku | Zwykły tekst (txt) |
| Format kodowania | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE lub UTF-16-BE. Kodowania zh-CN, ANSI/ASCII i UTF-8 nie są obsługiwane. |
| Liczba wypowiedzi w wierszu | **Po pierwsze** — każdy wiersz pliku transkrypcji powinien zawierać nazwę jednego z plików audio, a następnie odpowiednią transkrypcję. Nazwę pliku i transkrypcję należy rozdzielać przy użyciu tabulatora (\t). |
| Maksymalna wielkość pliku | 2048 MB |

Poniżej znajduje się przykład, jak transkrypcje są zorganizowane wypowiedź przez wypowiedź w jednym pliku txt:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
Ważne jest, aby transkrypcje były w 100% dokładne transkrypcje odpowiedniego dźwięku. Błędy w transkrypcjach wprowadzą utratę jakości podczas treningu.

> [!TIP]
> Podczas tworzenia produkcji text-to-speech głosy, wybierz wypowiedzi (lub skrypty zapisu), które biorą pod uwagę zarówno fonetyczne zasięgu i wydajności. Masz problemy z uzyskaniem wyników, które chcesz? [Skontaktuj się z zespołem Custom Voice,](mailto:speechsupport@microsoft.com) aby dowiedzieć się więcej o konieczności konsultacji z nami.

## <a name="long-audio--transcript-beta"></a>Długi dźwięk + transkrypcja (beta)

W niektórych przypadkach dźwięk segmentowy może nie być dostępny. Zapewniamy usługę (beta) za pośrednictwem niestandardowego portalu głosowego, aby pomóc Ci segmentować długie pliki audio i tworzyć transkrypcje. Pamiętaj, że ta usługa zostanie naliczona do użycia subskrypcji zamiany mowy na tekst.

> [!NOTE]
> Usługa segmentacji długiego dźwięku będzie korzystać z funkcji transkrypcji partii zamiany mowy na tekst, który obsługuje tylko standardowych subskrypcji (S0) użytkowników. Podczas przetwarzania segmentacji pliki audio i transkrypcje zostaną również wysłane do usługi Mowy niestandardowej w celu zawężenia modelu rozpoznawania, dzięki czemu można poprawić dokładność danych. Podczas tego procesu żadne dane nie będą przechowywane. Po zakończeniu segmentacji tylko wypowiedzi podzielone na segmenty i ich transkrypcje mapowania będą przechowywane do pobierania i szkolenia.

### <a name="audio-files"></a>Pliki audio

Podczas przygotowywania dźwięku do segmentacji należy postępować zgodnie z tymi wskazówkami.

| Właściwość | Wartość |
| -------- | ----- |
| Format pliku | RIFF (.wav) z częstotliwością próbkowania co najmniej 16 khz-16-bit w PCM lub .mp3 z szybkością transmisji bitów co najmniej 256 KB/s, pogrupowane w plik .zip |
| Nazwa pliku | Obsługiwane znaki ASCII i Unicode. Nie wolno duplikatów nazw. |
| Długość dźwięku | Dłużej niż 20 sekund |
| Format archiwum | zip |
| Maksymalny rozmiar archiwum | 2048 MB |

Wszystkie pliki audio powinny być pogrupowane w plik zip. Jest OK, aby umieścić pliki .wav i .mp3 plików w jednym zip audio. Na przykład możesz przesłać plik zip zawierający plik audio o nazwie "kingstory.wav", 45-sekundowy i inny dźwięk o nazwie "queenstory.mp3", 200-sekundowy. Wszystkie pliki .mp3 zostaną przekształcone w formacie .wav po przetworzeniu.

### <a name="transcripts"></a>Transkrypcje

Transkrypcje muszą być przygotowane zgodnie ze specyfikacjami wymienionymi w niniejszej tabeli. Każdy plik audio musi być dopasowany do transkrypcji.

| Właściwość | Wartość |
| -------- | ----- |
| Format pliku | Zwykły tekst (txt), pogrupowany w plik .zip |
| Nazwa pliku | Użyj tej samej nazwy co pasujący plik audio |
| Format kodowania | Tylko UTF-8-BOM |
| Liczba wypowiedzi w wierszu | Bez ograniczeń |
| Maksymalna wielkość pliku | 2048 MB |

Wszystkie pliki transkrypcji tego typu danych powinny być pogrupowane w plik zip. Na przykład, masz przesłane plik zip zawierający plik audio o nazwie "kingstory.wav", 45 sekund długości, a inny o nazwie "queenstory.mp3", 200 sekund. Będziesz musiał przesłać inny plik zip zawierający dwa transkrypcje, jeden o nazwie "kingstory.txt", drugi "queenstory.txt". W każdym pliku zwykłego tekstu, można podać pełną poprawną transkrypcję dla pasującego dźwięku.

Po pomyślnym przesłaniu zestawu danych pomożemy Ci podzielić plik audio na wypowiedzi na podstawie dostarczonej transkrypcji. Można sprawdzić segmentacji wypowiedzi i pasujące transkrypcje, pobierając zestaw danych. Unikatowe identyfikatory zostaną automatycznie przypisane do wypowiedzi segmentowanych. Ważne jest, aby upewnić się, że transkrypcje, które podajesz, są w 100% dokładne. Błędy w transkrypcjach mogą zmniejszyć dokładność podczas segmentacji dźwięku i dodatkowo wprowadzić utratę jakości w fazie szkolenia, która nastąpi później.

## <a name="audio-only-beta"></a>Tylko dźwięk (wersja beta)

Jeśli nie masz transkrypcji nagrań audio, użyj opcji **Tylko dźwięk,** aby przesłać dane. Nasz system może pomóc w segmentacji i transkrypcji plików audio. Pamiętaj, że ta usługa będzie wliczane do użycia subskrypcji zamiany mowy na tekst.

Podczas przygotowywania dźwięku należy postępować zgodnie z tymi wskazówkami.

> [!NOTE]
> Usługa segmentacji długiego dźwięku będzie korzystać z funkcji transkrypcji partii zamiany mowy na tekst, który obsługuje tylko standardowych subskrypcji (S0) użytkowników.

| Właściwość | Wartość |
| -------- | ----- |
| Format pliku | RIFF (.wav) z częstotliwością próbkowania co najmniej 16 khz-16-bit w PCM lub .mp3 z szybkością transmisji bitów co najmniej 256 KB/s, pogrupowane w plik .zip |
| Nazwa pliku | Obsługiwane znaki ASCII i Unicode. Nie dozwolona jest zduplikowana nazwa. |
| Długość dźwięku | Dłużej niż 20 sekund |
| Format archiwum | zip |
| Maksymalny rozmiar archiwum | 2048 MB |

Wszystkie pliki audio powinny być pogrupowane w plik zip. Po pomyślnym przesłaniu zestawu danych pomożemy Ci podzielić plik audio na wypowiedzi na podstawie naszej usługi transkrypcji partii mowy. Unikatowe identyfikatory zostaną automatycznie przypisane do wypowiedzi segmentowanych. Pasujące transkrypcje zostaną wygenerowane za pomocą rozpoznawania mowy. Wszystkie pliki .mp3 zostaną przekształcone w formacie .wav po przetworzeniu. Można sprawdzić segmentacji wypowiedzi i pasujące transkrypcje, pobierając zestaw danych.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie niestandardowego głosu](how-to-custom-voice-create-voice.md)
- [Przewodnik: Nagrywanie próbek głosu](record-custom-voice-samples.md)
