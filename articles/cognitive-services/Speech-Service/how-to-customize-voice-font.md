---
title: Jak utworzyć niestandardową czcionkę głosową
titlesuffix: Azure Cognitive Services
description: W tym artykule przedstawiono omówienie procedury dostosowywania głosu zamiany tekstu na mowę, co pozwala na tworzenie głosem marki mówiącą, jedną dla typu.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: panosper
ms.openlocfilehash: 24b98ce8cd2c587f0d39390954eb8a64747ca2ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60654085"
---
# <a name="creating-custom-voice-fonts"></a>Tworzenie niestandardowych voice czcionek

Dostosowywania głosu zamiany tekstu na mowę (TTS) pozwala na tworzenie mówiącą, jeden z rodzajem głosu dla Twojej marki: *czcionka głosowa.*

Aby utworzyć czcionki głosowe, tworzenie nagrania studio i przekaż skojarzone skrypty jako dane szkoleniowe. Usługa tworzy następnie model unikatowy głosu dostosowana do Twojego nagrania. Ta czcionka głosowa służy do syntetyzowania mowy.

Możesz rozpocząć pracę z małą ilością danych do weryfikacji koncepcji. Jednak im więcej danych podasz, tym bardziej naturalnych i professional brzmi Twojego głosu.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebujesz konta platformy Azure i subskrypcji usługi mowy. [Utwórz je](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started) Jeśli jeszcze go. Twoja subskrypcja połączyć się z portalu usługi Custom Voice jak pokazano poniżej.

1. Zaloguj się do [portal Custom Voice](https://customvoice.ai) przy użyciu tego samego konta Microsoft, który służy do zastosowania do uzyskiwania dostępu.

2. Pod nazwą Twojego konta w prawym górnym rogu, przejdź do **subskrypcje**.

    ![Subscriptions](media/custom-voice/subscriptions.png)

3. Na stronie subskrypcji wybierz **połączyć z istniejącą subskrypcją**. Należy pamiętać, że usługi mowy obsługują różnych regionach. Sprawdź region, gdzie został utworzony klucz subskrypcji i upewnij się, Połącz swój klucz do portalu podrzędnych poprawne.  

4. Wklej swój klucz subskrypcji do tabeli, jak pokazano w poniższym przykładzie. Każda subskrypcja ma dwa klucze, a następnie można użyć dowolnego z nich.

     ![Dodaj subskrypcję](media/custom-voice/add-subscription.png)

Jesteś gotowy!

## <a name="prepare-recordings-and-transcripts"></a>Przygotowania, nagrania i transkrypcje

Zestaw danych szkoleniowych głosu składa się z zestawu plików audio, wraz z pliku tekstowego zawierającego transkrypcji audio plików.

Należy przygotować te pliki na dwa sposoby. Albo napisać skrypt i odczytywane przez talent głosu lub użyć publicznie dostępnych audio i transkrypcja ją na tekst. Jeśli jednak ten ostatni edytować disfluencies z plików audio, takie jak "um" i inne wypełniacza dźwięki, stutters, mumbled słowa lub mispronunciations.

Aby wygenerować czcionka głosowa dobre, dzięki nagrania w pokoju cichy mikrofon wysokiej jakości. Spójne woluminu wypowiedzi szybkości, wypowiedzi gęstość i ekspresyjny mannerisms mowy są niezbędne do kompilowania doskonały cyfrowy głosu.

Aby utworzyć głosu w środowisku produkcyjnym, zalecamy możesz talent studio i głosu profesjonalnych rejestrowania. Aby uzyskać więcej informacji, zobacz [jak głos przykłady dla niestandardowych voice](record-custom-voice-samples.md).

### <a name="audio-files"></a>Pliki dźwiękowe

Każdy plik dźwiękowy powinien zawierać pojedynczy wypowiedź, (na przykład jednym zdaniu lub pojedynczego Włącz systemu okna dialogowego). Wszystkie pliki muszą znajdować się w tym samym języku. (Wielojęzyczny głosów niestandardowych nie są obsługiwane.) Pliki audio musi również mieć unikatową nazwę liczbowych pliku z rozszerzeniem nazwy pliku `.wav`.

Pliki audio powinna być przygotowana w następujący sposób. Inne formaty są obsługiwane i zostaną odrzucone.

| **Właściwość** | **Wartość** |
| ------------ | --------- |
| Format pliku  | RIFF (wav)|
| Częstotliwość próbkowania| co najmniej 16 000 Hz |
| Przykładowy format| PCM, 16-bitowych |
| Nazwa pliku    | Numeryczne, za pomocą `.wav` rozszerzenia |
| Format archiwum| zip      |
| Rozmiar maksymalny archiwum|200 MB|

> [!NOTE]
> .wav — pliki przy użyciu mniejszej niż 16 000 Hz częstotliwości próbkowania zostanie odrzucone. Jeśli plik zip zawiera poprzedzającym przy użyciu różnych współczynników pobierania próbek, zostanie on zaimportowany tylko tych równą lub większą niż 16 000 Hz.
> Portal aktualnie zip Importy archiwa do 200 MB. Jednak można przekazać wiele archiwów. Maksymalną liczbę zestawów danych, dozwolone jest 10 zip za darmo pliki subskrypcji użytkowników i 50 dla użytkowników subskrypcji standardowej.

### <a name="transcripts"></a>Zapisy

Plik transkrypcji jest zwykły plik tekstowy (ANSI, UTF-8, UTF-8-BOM, UTF-16-LE lub UTF-16-BE). Każdy wiersz plik transkrypcji musi mieć nazwę pliku audio, następuje znak tabulacji (punkt kodowy 9), a na końcu jego transkrypcję. Nie puste wiersze są dozwolone.

Na przykład:

```
0000000001  This is the waistline, and it's falling.
0000000002  We have trouble scoring.
0000000003  It was Janet Maslin.
```

System niestandardowych voice normalizuje zapisy, dokonując przekonwertowania tekst na małe litery i usunąć nadmiarowe znaki interpunkcyjne. Jest ważne, czy zapisy są transkrypcje dokładne 100% odpowiedniego nagrań audio.

> [!TIP]
> Gdy kompilowanie produkcji zamiany tekstu na mowę głosów, wybierz wypowiedzi (lub skryptów), która uwzględniać konta fonetycznych pokrycia i wydajność. Wystąpił problem podczas pobierania wyników chcesz? [Skontaktuj się z zespołem Custom Voice](mailto:speechsupport@microsoft.com) można znaleźć poza więcej informacji na temat o nas zapoznaj się z.

## <a name="upload-your-datasets"></a>Przekazywanie swoich zestawów danych

Po przygotowaniu usługi archiwum plików audio i zapisy, przekazać je przy użyciu [portal usługi Custom Voice](https://customvoice.ai).

> [!NOTE]
> Nie można edytować zestawów danych, po zostały przekazane. Jeśli zapomnisz obejmują zapisy niektórych plików audio, na przykład lub przypadkowo wybierz problem płeć, możesz ponownie przekazać cały zestaw danych. Sprawdź ustawienia i zestaw danych, dokładnie przed rozpoczęciem przekazywania.

1. Zaloguj się do portalu.

2. Na stronie głównej w obszarze **Custom Voice**, wybierz opcję **danych**.   

    **Moje głosu** zostanie wyświetlona tabela. Jest puste, jeśli nie został jeszcze przekazany żadnych zestawów danych głosowych.

3. Aby otworzyć stronę przekazywania nowy zestaw danych, wybierz **importowania danych**.

    ![Importowanie danych głosowych](media/custom-voice/import-voice-data.png)

4. Wprowadź nazwę i opis w polach, które są dostarczane.

5. Wybierz ustawienia regionalne umieszczać czcionki głosowe. Upewnij się, że informacji o ustawieniach regionalnych jest zgodny z językiem danych rejestrowania i skrypty.

6. Wybierz rodzaj osoby mówiącej głosowej, którego używasz.

7. Wybierz skrypt i pliki audio do przekazania.

8. Wybierz **importu** do przekazania danych. Dla większych zestawów danych importowania może potrwać kilka minut.

> [!NOTE]
> Użytkownicy bezpłatnej subskrypcji można przekazać dwa zestawy danych w danym momencie. Użytkownicy subskrypcji standardowej jednocześnie przekazać pięć zestawów danych. Jeśli przekroczysz limit, poczekaj na zakończenie co najmniej jeden z zestawów danych, importowania. Następnie spróbuj ponownie.

Po zakończeniu przekazywania **Moje dane głosu** tabeli pojawi się ponownie. Powinien zostać wyświetlony wpis, który odnosi się do zestawu danych, który został przekazany.

Zestawy danych są automatycznie zweryfikowana, po przekazywania. Sprawdzanie poprawności danych obejmuje szereg kontroli plików audio, aby sprawdzić ich format pliku, rozmiar i częstotliwość próbkowania. Kontrole pliki transkrypcji Sprawdź format pliku i do niektórych normalizacji tekstu. Wypowiedzi są zapisywane przy użyciu funkcji rozpoznawania mowy. Następnie tekst wynikowy jest porównywany z transkrypcji, podane.

![My Voice Data](media/custom-voice/my-voice-data.png)

W poniższej tabeli przedstawiono stany przetwarzania dla zaimportowanych zestawów danych:

| Stan | Znaczenie
| ----- | -------
| `NotStarted` | Zestaw danych zostało odebrane i znajduje się w kolejce do przetworzenia.
| `Running` | Zestaw danych jest weryfikowany.
| `Succeeded` | Zestaw danych został zweryfikowany, a teraz może służyć do tworzenia czcionka głosowa.

Po zakończeniu sprawdzania poprawności widać łączną liczbę wypowiedzi dopasowane do wszystkich zestawów danych w **wypowiedź** kolumny.

Możesz pobrać raport, aby sprawdzić wyniki Wymowa i poziomu hałasu dla każdego z nagrań. Wymowa wynik z zakresu od 0 do 100. Współczynnik poniżej 70 zwykle wskazuje niezgodność mowy błąd lub skryptu. Duże akcentu można zmniejszyć ocenę Wymowa i wpływu na wygenerowany głosu cyfrowych.

Większy współczynnik sygnał do szumu (SNR) wskazuje niższe szumu w audio. Zwykle mogą skorzystać z SNR ponad 50 przez rejestrowanie w Studio professional. Pliku dźwiękowego z SNR poniżej 20 może spowodować oczywiste szumu w wygenerowanym głosu.

Należy rozważyć ponowne nagrywanie wypowiedzi Wymowa niskie oceny lub niską współczynniki sygnał do szumu. Jeśli nie można ponownie zarejestrować, może być wykluczyć te wypowiedzi z zestawu danych.

## <a name="build-your-voice-font"></a>Twórz swoje czcionka głosowa

Po sprawdzeniu poprawności zestawu danych służy do tworzenia swojej niestandardowej czcionki głosowej.

1.  W **Custom Voice** menu rozwijanego wybierz **modeli**.

    **Moje czcionki głosowe** zostanie wyświetlona tabela, lista czcionek niestandardowych voice został już utworzony.

1. W obszarze tytuł tabeli wybierz **tworzenia głosów**.

    Zostanie wyświetlona strona tworzenia czcionka głosowa. Bieżących ustawień regionalnych jest wyświetlany w pierwszym wierszu tabeli. Zmień ustawienia regionalne, aby utworzyć głosu w innym języku. Ustawienia regionalne musi być taka sama jak w przypadku zestawów danych, które są używane do tworzenia głosu.

1. Tak jak w przypadku został przekazany zestaw danych, wprowadź nazwę i opis, aby pomóc w zidentyfikowaniu tego modelu.

    Wybierz nazwę, dokładnie. Wprowadzona nazwa będzie nazwa, używanej do określania głosu w Twoje żądanie dotyczące synteza mowy w ramach SSML danych wejściowych. Tylko litery, cyfry i kilka znaków interpunkcyjnych, takie jak `-`, `_`, i `(', ')` są dozwolone.

    Typowym zastosowaniem **opis** pole służy do rejestrowania nazw zestawów danych, które zostały użyte do utworzenia modelu.

1. Wybierz płeć czcionki głosowe. Musi on być zgodny płeć zestawu danych.

1. Wybierz zestawach danych, którego chcesz użyć do trenowania czcionka głosowa. Wszystkie zestawy danych, których używasz, musi pochodzić z tej samej osoby mówiącej.

1. Kliknij przycisk **Utwórz** umożliwiającą utworzenie czcionki głosowe.

    ![Tworzenie modelu](media/custom-voice/create-model.png)

Nowy model pojawia się w **Moje czcionki głosowe** tabeli.

![Czcionki głosowe](media/custom-voice/my-voice-fonts.png)

Stan, który jest wyświetlany odzwierciedla proces konwersji zestawu danych czcionki głosowej, jak pokazano poniżej.

| Stan | Znaczenie
| ----- | -------
| `NotStarted` | Twoje żądanie dotyczące tworzenia czcionki głosowe w kolejce na przetworzenie.
| `Running` | Trwa tworzenie Twojego czcionka głosowa.
| `Succeeded` | Czcionki głosowe została utworzona i można wdrażać.

Szkolenie czasu różni się zależnie od ilości przetworzonych danych audio. Typowy czas do zakresu od o setki wypowiedzi na 30 minut do 40 godzin wypowiedzi 20 000.

> [!NOTE]
> Użytkownicy bezpłatnej subskrypcji uczyć jeden czcionka głosowa w danym momencie. Standardowa subskrypcja użytkowników jednocześnie uczyć trzy głosów. Jeśli przekroczysz limit, zaczekaj co najmniej jeden umieszczać czcionki głosowe szkoleń, a następnie spróbuj ponownie.

## <a name="test-your-voice-font"></a>Testowanie czcionki głosowe

Po pomyślnym utworzeniu czcionki głosowe, można ją przetestować, przed jego wdrożeniem. W **operacji** kolumny wybierz **testu**. Strona testowa pojawia się dla czcionka głosowa wybrane. Tabela jest pusta, jeśli jeszcze nie przesłano żadnych żądań testu dla głosu.

Aby wyświetlić menu podręcznego przesyłania żądania tekstowe, wybierz **testu z tekstem** przycisk pod tytułem tabeli. Możesz przesłać żądanie testu w postaci zwykłego tekstu lub SSML. Maksymalny rozmiar danych wejściowych wynosi 1024 znaków, w tym wszystkie tagi dla żądania SSML. Język tekstu musi być taka sama jak język czcionki głosowe.

Po wypełniając pole tekstowe i Potwierdzanie tryb wprowadzania, wybrać **tak** przesłać żądanie testu, a następnie wróć do strony testowej. Tabela zawiera teraz wpis, który odpowiada nowe żądanie i w kolumnie Stan. Może potrwać kilka minut, aby syntetyzowania mowy. Gdy w kolumnie Stan jest wyświetlany komunikat **Powodzenie**, możesz pobrać wprowadzanie tekstu ( `.txt` pliku) i danych wyjściowych audio ( `.wav` pliku) i audition jego jakości.

## <a name="create-and-use-a-custom-endpoint"></a>Tworzenie i używanie niestandardowego punktu końcowego

Po pomyślnie tworzone i testowane modelu głosowego, wdrożyć ją w niestandardowy punkt końcowy zamiany tekstu na mowę. Następnie należy użyć tego punktu końcowego zamiast zwykle punktu końcowego w przypadku wysyłania żądań zamiany tekstu na mowę przy użyciu interfejsu API REST. Niestandardowy punkt końcowy można wywołać tylko przez subskrypcję, która umożliwia wdrażanie czcionki.

Aby utworzyć nowy niestandardowy punkt końcowy, wybierz **punktów końcowych** z **Custom Voice** menu w górnej części strony. **Głosów wdrożony Mój** zostanie wyświetlona strona, z jego tabeli bieżącego głosu niestandardowych punktów końcowych, jeśli istnieje. Bieżących ustawień regionalnych jest widoczny w pierwszym wierszu tabeli. Aby utworzyć wdrożenie w innym języku, należy zmienić wyświetlane ustawienia regionalne. (Musi być zgodna głosowych, które wdrażasz.)

Aby utworzyć nowy punkt końcowy, wybierz **wdrażanie głosów** przycisku. Wprowadź nazwę i Opis niestandardowego punktu końcowego.

Z **subskrypcji** menu, wybierz subskrypcję, której chcesz użyć. Użytkownicy bezpłatnej subskrypcji mogą mieć tylko jeden model wdrożone w danym momencie. Użytkownicy subskrypcji standardowej można utworzyć maksymalnie 20 punktów końcowych, każdy z własną niestandardowych voice.

![Tworzenie punktu końcowego](media/custom-voice/create-endpoint.png)

Po wybraniu modelu, który ma zostać wdrożony, wybierz **Utwórz**. **Głosów wdrożony Mój** strony, pojawi się teraz z wpisu dla nowego punktu końcowego. Może upłynąć kilka minut utworzyć nowy punkt końcowy. Gdy stan wdrożenia jest **Powodzenie**, punkt końcowy jest gotowy do użycia.

![Moje wdrożone głosów](media/custom-voice/my-deployed-voices.png)

Gdy stan wdrożenia jest **Powodzenie**, punkt końcowy usługi czcionka głosowa wdrożonej pojawia się w **głosów wdrożony Mój** tabeli. Można użyć tego identyfikatora URI, bezpośrednio w żądaniu HTTP.

Testowanie online punktu końcowego jest również dostępna za pośrednictwem portalu niestandardowych voice. Aby przetestować punktu końcowego usługi, wybierz **testowania punktów końcowych** z **Custom Voice** menu rozwijanego. Punkt końcowy testowania strony pojawi się. Wybierz wdrożonej głosu niestandardowe, a następnie wprowadź tekst, który ma być używany (w postaci zwykłego tekstu lub SSML format) w polu tekstowym.

> [!NOTE]
> Korzystając z SSML, `<voice>` tag należy określić nazwę nadaną swój głos niestandardowych, podczas jego tworzenia. Jeśli prześlesz zwykłego tekstu, głosu niestandardowego jest zawsze używana.

Aby usłyszeć tekstu mówionego w swojej niestandardowej czcionki głosowej, wybierz **Odtwórz**.

![Testowanie punktu końcowego](media/custom-voice/endpoint-testing.png)

Niestandardowy punkt końcowy jest funkcjonalnie identyczny standardowy punkt końcowy, który służy do żądania zamiany tekstu na mowę. Zobacz [interfejsu API REST](rest-apis.md) Aby uzyskać więcej informacji.

## <a name="language-support"></a>Obsługa języków

Dostosowanie głos jest dostępny w tych językach:

| Język | Ustawienia regionalne |
|----------|--------|
| Chiński (kontynent) | zh-CN |
| English (US) | en-US |
| Francuski | fr-FR |
| Niemiecki | de-DE. |
| Włoski | IT-IT |

> [!NOTE]
> Francuski, niemiecki i włoski szkolenia głosu rozpoczyna się od zestaw ponad 2000 wypowiedzi. Modele dwujęzyczny chiński angielski są również obsługiwane z zestawu danych w ponad 2000 wypowiedzi.

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Zarejestruj swoje próbki głosu](record-custom-voice-samples.md)
