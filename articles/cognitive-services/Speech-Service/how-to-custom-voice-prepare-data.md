---
title: Jak przygotować dane dla niestandardowego głosu — usługi mowy
titlesuffix: Azure Cognitive Services
description: Utwórz niestandardowe głosu dla Twojej marki za pomocą usług przetwarzania mowy platformy Azure. Podaj studio nagrania i skojarzone skrypty, Usługa generuje modelu głosowego unikatowy dostosowana do nagrania głosowe. Użyj tego głosu syntetyzować mowy produktów, narzędzi i aplikacji.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 18e1bb486c47baf7648a74e31451e2db73f72250
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156861"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Przygotowywanie danych do tworzenia niestandardowych voice

Gdy wszystko będzie gotowe do tworzenia niestandardowych voice zamiany tekstu na mowę, aplikacji, pierwszym krokiem jest zbieranie nagrania audio i skojarzone skrypty, aby rozpocząć szkolenie modeli głosowych. Usługa używa tych danych, aby utworzyć unikatowy dostosowane do głosu w nagrań głosu. Po już skonfigurowanych pod kątem mowy, możesz rozpocząć Syntetyzujące mowy w aplikacjach.

Można uruchomić z małą ilością danych do tworzenia, weryfikacji koncepcji. Jednak im więcej danych, który należy podać, więcej fizyczna dźwiękowe swój głos niestandardowych. Aby możesz uczyć własnego modelu głosowego zamiany tekstu na mowę, trzeba mieć nagrania i transkrypcje skojarzony tekst. Na tej stronie omówimy, typy danych, sposobu ich używania i sposób zarządzania każdym.

## <a name="data-types"></a>Typy danych

Zestaw danych szkoleniowych głosu obejmuje nagrania audio i plik tekstowy z skojarzone transkrypcji. Każdy plik dźwiękowy powinien zawierać pojedynczy wypowiedź (jednym zdaniu lub pojedynczych Włączanie systemu okna dialogowego), a mniej niż 15 sekund.

W niektórych przypadkach może nie mieć prawo gotowy zestaw danych i będzie ma zostać przetestowana szkolenia głosu niestandardowe z dostępnych plików audio, krótkim lub długim z lub bez transkrypcji. Firma Microsoft zapewnia narzędzia (beta) ułatwiające segmentu audio do wypowiedzi i przygotuj transkrypcje przy użyciu [interfejsu API usługi Batch transkrypcji](batch-transcription.md).

Poniższa tabela zawiera listę typów danych i jak każdy obiekt jest używany do tworzenia modelu niestandardowego tekstu na mowę głosu.

| Typ danych | Opis | Kiedy stosować | Usługi dodatkowe wymagane | Ilość do uczenia modelu | Locale(s) |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Poszczególne wypowiedzi + pasującego transkrypcji** | Kolekcja (.zip) pliki audio (wav) jako pojedynczych wypowiedzi. Każdy plik dźwiękowy powinien być 15 sekund lub mniej znaków, z sformatowane transkrypcji (.txt). | Profesjonalne nagrań z pasującymi transkrypcji | Gotowe do szkolenia. | Nie bezwględnie wymagane dla en US i zh-CN. Więcej niż 2000 + distinct wypowiedzi dla innych ustawień regionalnych. | Wszystkie ustawienia regionalne Custom Voice |
| **Długie nagrania i transkrypcji (beta)** | Kolekcja (.zip) długie, bez segmentów pliki audio (więcej niż 20 sekund), jest powiązany z transkrypcji (.txt), który zawiera wszystkie wypowiadanych słów. | Masz pliki audio i zapisy dopasowania, ale nie są one podzielono ją na wypowiedzi. | Segmentacja (przy użyciu transkrypcji usługi batch).<br>Audio format przekształcania gdy jest to wymagane. | Nie bezwględnie wymagane dla en US i zh-CN. | `en-US` i `zh-CN` |
| **Tylko audio (beta)** | Kolekcja (.zip) plików audio, bez transkrypcji. | Wystarczy tylko pliki audio, które są dostępne, bez transkrypcji. | Segmentacja + Generowanie transkrypcji (przy użyciu transkrypcji usługi batch).<br>Audio format przekształcania gdy jest to wymagane.| Nie bezwględnie wymagane dla `en-US` i `zh-CN`. | `en-US` i `zh-CN` |

Pliki powinny pogrupowane według typu do zestawu danych i przekazać w formie pliku zip. Każdy zestaw danych może zawierać tylko jednego typu danych.

> [!NOTE]
> Maksymalna liczba zestawów danych, można zaimportować na subskrypcję jest 10 zip za darmo plików użytkownicy subskrypcji (F0) oraz 500 użytkowników subskrypcja standardowa (S0).

## <a name="individual-utterances--matching-transcript"></a>Poszczególne wypowiedzi + pasującego transkrypcji

Można przygotować nagrania poszczególnych wypowiedzi i dopasowania transkrypcji na dwa sposoby. Albo napisać skrypt i odczytywane przez talent głosu lub użyć publicznie dostępnych audio i transkrypcja ją na tekst. Jeśli jednak ten ostatni edytować disfluencies z plików audio, takie jak "um" i inne wypełniacza dźwięki, stutters, mumbled słowa lub mispronunciations.

Aby wygenerować czcionka głosowa dobre, należy utworzyć nagrania w pokoju cichy z mikrofonu wysokiej jakości. Spójne woluminu wypowiedzi szybkości, wypowiedzi gęstość i ekspresyjny mannerisms mowy mają kluczowe znaczenie.

> [!TIP]
> Aby utworzyć głosu w środowisku produkcyjnym, zalecamy możesz talent studio i głosu profesjonalnych rejestrowania. Aby uzyskać więcej informacji, zobacz [jak głos przykłady dla niestandardowych voice](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/record-custom-voice-samples).

### <a name="audio-files"></a>Pliki dźwiękowe

Każdy plik dźwiękowy powinien zawierać pojedynczy wypowiedź (jednym zdaniu lub pojedynczych Włączanie systemu okna dialogowego), mniej niż 15 sekund. Wszystkie pliki muszą znajdować się w ten sam język mówiony. Wiele języków niestandardowych głosów zamiany tekstu na mowę nie są obsługiwane z wyjątkiem chiński angielska bi języków. Każdy plik dźwiękowy musi mieć unikatową nazwę liczbowych pliku z rozszerzeniem nazwy pliku .wav.

Należy przestrzegać następujących wytycznych podczas przygotowywania audio.

| Właściwość | Wartość |
| -------- | ----- |
| Format pliku | RIFF (wav) pogrupowane w pliku zip |
| Częstotliwość próbkowania | co najmniej 16 000 Hz |
| Przykładowy format | PCM, 16-bitowych |
| Nazwa pliku | Liczbowe z rozszerzeniem wav. Nie nazwy zduplikowanych plików są dozwolone. |
| Długość audio | Mniej niż 15 sekund |
| Format archiwum | zip |
| Rozmiar maksymalny archiwum | 200 MB |

> [!NOTE]
> .wav — pliki przy użyciu mniejszej niż 16 000 Hz częstotliwości próbkowania zostanie odrzucone. Jeśli plik zip zawiera pliki przy użyciu stawki za różne przykładowe, zostaną zaimportowane tylko tych równą lub większą niż 16 000 Hz. Portal aktualnie zip Importy archiwa do 200 MB. Jednak można przekazać wiele archiwów.

### <a name="transcripts"></a>Zapisy

Plik transkrypcji jest zwykły plik tekstowy. Za pomocą następujących wytycznych przygotować swoje transkrypcji.

| Właściwość | Wartość |
| -------- | ----- |
| Format pliku | Zwykły tekst (txt) |
| Format kodowania | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE lub być UTF-16. Aby uzyskać nazwy zh-CN kodowania ANSI/ASCII i UTF-8 nie są obsługiwane. |
| Liczba wypowiedzi w wierszu | **Jeden** -każdego wiersza w pliku transkrypcji powinno zawierać nazwę jednego z plików audio, następuje odpowiedni tekst. Nazwę pliku i transkrypcję należy rozdzielać przy użyciu tabulatora (\t). |
| Maksymalna wielkość pliku | 50 MB |

Poniżej przedstawiono przykład sposobu zapisy organizacji wypowiedź przez wypowiedź w jednym pliku txt:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
Należy pamiętać, że zapisy są 100% dokładne transkrypcji audio odpowiednie. Błędy w transkrypcji zostaną wprowadzone obniżenie jakości podczas szkolenia.

> [!TIP]
> Podczas tworzenia produkcyjnych głosów zamiany tekstu na mowę, wybierz opcję wypowiedzi (lub skryptów) uwzględniające konta fonetycznych pokrycia i wydajność. Wystąpił problem podczas pobierania wyników chcesz? [Skontaktuj się z pomocą głosu niestandardowe](mailto:speechsupport@microsoft.com) zespołu, aby znaleźć poza więcej informacji na temat o nas zapoznaj się z.

## <a name="long-audio--transcript-beta"></a>Długie nagrania i transkrypcji (beta)

W niektórych przypadkach możesz może nie mieć segmentowanych audio dostępne. Firma Microsoft oferuje usługi (wersja beta) za pośrednictwem portalu niestandardowych voice ułatwiające podzielić pliki długo audio i utworzyć transkrypcji. Należy pamiętać, ta usługa jest naliczana kierunku wykorzystanie subskrypcji mowy na tekst.

> [!NOTE]
> Usługa segmentacji long-audio będzie korzystać partii funkcję transkrypcja mowy na tekst, który obsługuje tylko użytkowników subskrypcja standardowa (S0). Podczas przetwarzania segmentacji plików audio i zapisy również otrzymasz w usłudze Custom Speech uściślić model rozpoznawania, dzięki czemu można zwiększyć dokładność danych. Żadne dane nie zostaną zachowane w trakcie tego procesu. Po wykonaniu czynności segmentacji wypowiedzi segmentowanych i ich zapisy mapowania będą przechowywane dla pobierania i szkolenia.

### <a name="audio-files"></a>Pliki dźwiękowe

Należy przestrzegać następujących wytycznych podczas przygotowywania audio segmentacji.

| Właściwość | Wartość |
| -------- | ----- |
| Format pliku | RIFF (wav) za pomocą próbkowania co najmniej 16 khz-16-bitowych w module PCM lub MP3 za pomocą szybkość transmisji bitów, co najmniej 256 KB/s, pogrupowane według plik .zip |
| Nazwa pliku | Tylko znaki ASCII. Znaki Unicode w nazwie zakończy się niepowodzeniem (na przykład znakami języka chińskiego lub symbole, takie jak "—"). Nie zduplikowane nazwy są dozwolone. |
| Długość audio | Dłuższy niż 20 sekund |
| Format archiwum | zip |
| Rozmiar maksymalny archiwum | 200 MB |

Wszystkie pliki dźwiękowe, powinny zostać utworzone w pliku zip. Zgadzam się umieścić .wav — pliki i pliki MP3 w jednym zip audio, ale podfolder nie jest dozwolone w pliku zip. Na przykład możesz przekazać plik zip zawierający plik dźwiękowy o nazwie "kingstory.wav", 45 sekundę długich i innym jeden o nazwie "queenstory.mp3", 200-second-long, bez żadnych podfolderów. Wszystkie pliki MP3 będą przekształcane w formacie .wav po zakończeniu przetwarzania.

### <a name="transcripts"></a>Zapisy

Zapisy musi być przygotowana do specyfikacji wymienione w poniższej tabeli. Każdy plik dźwiękowy muszą się zgadzać z transkrypcji.

| Właściwość | Wartość |
| -------- | ----- |
| Format pliku | Zwykły tekst (txt), pogrupowane według plik zip |
| Nazwa pliku | Użyj takiej samej nazwy jak pasującego pliku audio |
| Format kodowania | UTF-8-BOM tylko |
| Liczba wypowiedzi w wierszu | Bez ograniczeń |
| Maksymalna wielkość pliku | 50 MLN |

Wszystkie pliki transkrypcji, w tym typie danych powinny zostać utworzone w pliku zip. Nie podfolderów jest dozwolone w pliku zip. Na przykład został przekazany plik zip zawierający plik dźwiękowy o nazwie "kingstory.wav" 45 sekund, a inny jeden o nazwie "queenstory.mp3", 200 sekund długo. Należy przekazać inny plik zip zawierający dwóch zapisów, jeden o nazwie "kingstory.txt", inne jeden "queenstory.txt". W ramach każdego pliku w postaci zwykłego tekstu należy podać pełną poprawne transkrypcji dla zgodnego audio.

Po pomyślnym przekazaniu zestawu danych, pomożemy Ci segmentu plik dźwiękowy do wypowiedzi oparte na transkrypcjach, pod warunkiem. Możesz sprawdzić segmentowanych wypowiedzi i zapisy dopasowania, pobierając zestaw danych. Unikatowe identyfikatory zostanie automatycznie przypisany do segmentowanych wypowiedzi. Jest ważne przez należy upewnić się, że zapisy, których udzielasz są dokładne w 100%. Błędy w transkrypcji można zmniejszyć dokładność podczas audio segmentacji i wprowadzić obniżenie jakości w fazie szkolenia, która powstaje później.

## <a name="audio-only-beta"></a>Tylko audio (beta)

Jeśli nie masz transkrypcji dla Twojego nagrania audio, użyj **tylko Audio** opcję, aby przekazać dane. Nasz system może pomóc w segmentu, a także plików audio. Należy pamiętać, ta usługa będą przekładać wykorzystanie subskrypcji mowy na tekst.

Należy przestrzegać następujących wytycznych podczas przygotowywania audio.

> [!NOTE]
> Usługa segmentacji long-audio będzie korzystać partii funkcję transkrypcja mowy na tekst, który obsługuje tylko użytkowników subskrypcja standardowa (S0).

| Właściwość | Wartość |
| -------- | ----- |
| Format pliku | RIFF (wav) za pomocą próbkowania co najmniej 16 khz-16-bitowych w module PCM lub MP3 za pomocą szybkość transmisji bitów, co najmniej 256 KB/s, pogrupowane według plik .zip |
| Nazwa pliku | Tylko znaki ASCII. Znaki Unicode w nazwie zakończy się niepowodzeniem (na przykład znakami języka chińskiego lub symbole, takie jak "—"). Nie zduplikowane nazwy dozwolone. |
| Długość audio | Dłuższy niż 20 sekund |
| Format archiwum | zip |
| Rozmiar maksymalny archiwum | 200 MB |

Wszystkie pliki dźwiękowe, powinny zostać utworzone w pliku zip. Nie podfolderów jest dozwolone w pliku zip. Po pomyślnym przekazaniu zestawu danych, pomożemy Ci segmentu plik dźwiękowy do wypowiedzi w oparciu o usługa transkrypcja mowy usługi batch. Unikatowe identyfikatory zostanie automatycznie przypisany do segmentowanych wypowiedzi. Dopasowywanie transkrypcji zostanie wygenerowany za pomocą funkcji rozpoznawania mowy. Wszystkie pliki MP3 będą przekształcane w formacie .wav po zakończeniu przetwarzania. Możesz sprawdzić segmentowanych wypowiedzi i zapisy dopasowania, pobierając zestaw danych.

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie niestandardowych Voice](how-to-custom-voice-create-voice.md)
- [Przewodnik: Zarejestruj swoje próbki głosu](record-custom-voice-samples.md)
