---
title: Co to jest Custom Voice? — Platforma azure Cognitive Services
description: Ten artykuł omówienia zamiany tekstu na mowę firmy Microsoft głosu dostosowywania, co pozwala na tworzenie głosem marki mówiącą, jeden z rodzajem.
services: cognitive-services
author: noellelacharite
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2018
ms.author: nolach
ms.openlocfilehash: 042216d03749273c590ce5ba812f7a6d609f8e83
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987910"
---
# <a name="creating-custom-voice-fonts"></a>Tworzenie niestandardowych voice czcionek

Dostosowywania głosu Microsoft tekst na mowę (TTS) pozwala na tworzenie mówiącą, jeden z rodzajem głosu dla Twojej marki: *czcionka głosowa.* 

Aby utworzyć czcionki głosowe, tworzenie nagrania studio i przekaż skojarzone skrypty jako dane szkoleniowe. Usługa tworzy następnie model unikatowy głosu dostosowana do Twojego nagrania. Następnie można ten czcionka głosowa syntetyzowania mowy. 

Możesz rozpocząć pracę z małą ilością danych do weryfikacji koncepcji. Jednak im więcej danych podasz, tym bardziej naturalnych i professional brzmi Twojego głosu.


## <a name="prerequisites"></a>Wymagania wstępne

**Zamiany tekstu na mowę** funkcja dostosowywania głosu jest obecnie dostępna w prywatnej wersji zapoznawczej. [Wypełnij formularz zgłoszeniowy](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0N8Vcdi8MZBllkZb70o6KdURjRaUzhBVkhUNklCUEMxU0tQMEFPMjVHVi4u) wziąć pod uwagę dostępu.

Należy również konto platformy Azure i subskrypcji usługi mowy. [Utwórz je](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started) Jeśli jeszcze go. Twoja subskrypcja połączyć się z portalu usługi Custom Voice w następujący sposób.

1. Zaloguj się do [portal Custom Voice](https://customvoice.ai) przy użyciu tego samego konta Microsoft, służy do zastosowania do uzyskiwania dostępu.

2. Przejdź do wszystkich subskrypcji, pod nazwą Twojego konta w prawym górnym rogu.

    ![Subskrypcje](media/custom-voice/subscriptions.png)

3. Na stronie "Subskrypcje" wybierz pozycję "Połącz istniejącą subskrypcję".

4. Wklej swój klucz subskrypcji do tabeli, jak pokazano poniżej. Każda subskrypcja ma dwa klucze i mogą korzystać z każdej z nich.

     ![Dodaj subskrypcję](media/custom-voice/add-subscription.png)

Jesteś gotowy!

> [!IMPORTANT]
> Na etapie prywatnej wersji zapoznawczej subskrypcji musi być umieszczona na białej liście, aby korzystać z funkcji głosowych niestandardowe. Postępuj zgodnie z instrukcjami na stronie można pobrać listy dozwolonych Twojej subskrypcji.

## <a name="prepare-recordings-and-transcripts"></a>Przygotowania, nagrania i transkrypcje

Zestaw danych szkoleniowych głosu składa się z zestawu plików audio, wraz z pliku tekstowego zawierającego transkrypcji wszystkich tych plików audio.

Należy przygotować te pliki w dowolnym kierunku: albo napisać skrypt i odczytywane przez głosu talent, lub użyć publicznie dostępnych audio i transkrypcja ją na tekst. W tym drugim przypadku należy edytować disfluencies z plików audio, takie jak "um" i inne wypełniacza dźwięki, stutters, mumbled słowa lub mispronunciations.

Aby wygenerować czcionka głosowa dobre, ważne jest, nagrania są wykonywane w pokoju cichy z mikrofonu wysokiej jakości. Spójne woluminu wypowiedzi szybkości, wypowiedzi gęstość i ekspresyjny mannerisms mowy są niezbędne do kompilowania doskonały cyfrowy głosu. Aby utworzyć głosu w środowisku produkcyjnym, zalecamy możesz talent studio i głosu profesjonalnych rejestrowania. Aby uzyskać więcej informacji, zobacz [jak głos przykłady dla niestandardowych voice](record-custom-voice-samples.md).

### <a name="audio-files"></a>Pliki dźwiękowe

Każdy plik dźwiękowy powinien zawierać pojedynczy wypowiedź, (na przykład jednym zdaniu lub pojedynczego Włącz systemu okna dialogowego). Wszystkie pliki muszą znajdować się w tym samym języku (wielojęzyczny głosów niestandardowych nie są obsługiwane). Pliki audio musi również mieć unikatową nazwę liczbowych pliku z rozszerzeniem nazwy pliku `.wav`.

Pliki audio powinna być przygotowana w następujący sposób. Inne formaty są obsługiwane i zostaną odrzucone.

| **Właściwość** | **Wartość** |
| ------------ | --------- |
| Format pliku  | RIFF (WAV)|
| Częstotliwość próbkowania| co najmniej 16 000 Hz |
| Format próbki| PCM, 16-bitowych |
| Nazwa pliku    | Numeryczne, za pomocą `.wav` rozszerzenia |
| Format archiwum| Zip      |
| Maksymalny rozmiar archiwum|200 MB|

> [!NOTE]
> Pliki Wave przy użyciu mniejszej niż 16 000 Hz częstotliwości próbkowania zostanie odrzucone. W przypadkach, gdy plik zip zawiera poprzedzającym przy użyciu różnych współczynników pobierania próbek zostanie on zaimportowany tylko tych równą lub większą niż 16 000 Hz.
> Portal aktualnie importuje ZIP archiwa do 200 MB. Jednak można przekazać wiele archiwów. Maksymalna liczba zestawów danych, dozwolone jest pliki 10 ZIP na bezpłatnie subskrypcji użytkowników i 50 dla użytkowników subskrypcji standardowej.

### <a name="transcripts"></a>Zapisy

Plik transkrypcji jest zwykły plik tekstowy (ANSI/UTF-8/UTF-8-BOM/UTF-16-LE/UTF-16-BE). Każdy wiersz plik transkrypcji musi mieć nazwę pliku audio, następuje znak tabulacji (punkt kodowy 9), a na końcu jego transkrypcję. Nie puste wiersze są dozwolone.

Na przykład:

```
0000000001  This is the waistline, and it's falling.
0000000002  We have trouble scoring.
0000000003  It was Janet Maslin.
```

System niestandardowych voice normalizuje zapisy Konwertowanie tekstu na małe i usuwając nadmiarowe znaki interpunkcyjne. Należy pamiętać, że zapisy są w 100% dokładności do odpowiedniego nagrania audio.

> [!TIP]
> Podczas tworzenia produkcyjnych zamiany tekstu na mowę głosów, wybierz wypowiedzi (lub skryptów), biorąc pod uwagę fonetycznych pokrycia i wydajność. Wystąpił problem podczas pobierania wyników chcesz? [Skontaktuj się z zespołem Custom Voice](mailto:tts@microsoft.com) można znaleźć poza więcej informacji na temat o nas zapoznaj się z.

## <a name="upload-your-datasets"></a>Przekazywanie swoich zestawów danych

Po przygotowaniu usługi archiwum plików audio i zapisy, przekazać je za pośrednictwem [Custom Voice portalu](https://customvoice.ai).

> [!NOTE]
> Nie można edytować zestawów danych, po zostały przekazane. Jeśli nie pamiętasz do obejmują zapisy niektórych plików audio, na przykład lub przypadkowo wybierz problem płeć, cały zestaw danych należy przekazać ponownie. Sprawdź ustawienia i zestaw danych, dokładnie przed rozpoczęciem przekazywania.

1. Zaloguj się do portalu.

2. Wybierz **danych** w obszarze Custom Voice na stronie głównej. 

    Zostanie wyświetlona tabela Moje danych głosowych. Jest puste, jeśli jeszcze nie przekazano żadnych zestawów danych głosowych.

3. Kliknij przycisk **importowania danych** aby otworzyć stronę przekazywania nowy zestaw danych.

    ![Importowanie danych głosowych](media/custom-voice/import-voice-data.png)

4. Wprowadź nazwę i opis w dostępnych polach. 

5. Wybierz ustawienia regionalne umieszczać czcionki głosowe. Upewnij się, że informacji o ustawieniach regionalnych jest zgodny z językiem danych rejestrowania i skrypty. 

6. Wybierz rodzaj osoby mówiącej głosowej, którego używasz.

7. Wybierz skrypt i pliki audio do przekazania. 

8. Kliknij przycisk **importu** do przekazania danych. Dla większych zestawów danych importowania może potrwać kilka minut.

> [!NOTE]
> Użytkownicy bezpłatnej subskrypcji można przekazać dwa zestawy danych w danym momencie. Użytkownicy subskrypcji standardowej jednocześnie przekazać pięć zestawów danych. Jeśli przekroczysz limit, zaczekaj co najmniej jeden z zestawów danych zakończeniu importowania, spróbuj ponownie.

Po zakończeniu przekazywania tabeli Moje danych głosowych pojawi się ponownie. Powinien zostać wyświetlony wpis, który odnosi się do zestawu danych po prostu przekazać.

Zestawy danych są automatycznie zweryfikowana, po przekazywania. Sprawdzanie poprawności danych obejmuje szereg kontroli plików audio, aby sprawdzić ich format pliku, rozmiar i częstotliwość próbkowania. Kontroli nad plikami transkrypcji Sprawdź format pliku i wykonywania niektórych normalizacji tekstu. Wypowiedzi są zapisywane przy użyciu funkcji rozpoznawania mowy, a tekst wynikowy jest porównywana z transkrypcji, podane.

![Moje dane głosu](media/custom-voice/my-voice-data.png)

W poniższej tabeli przedstawiono stany przetwarzania dla zaimportowanych zestawów danych. 

| Stan | Znaczenie
| ----- | -------
| `NotStarted` | Zestaw danych zostało odebrane i znajduje się w kolejce do przetworzenia
| `Running` | Zestaw danych jest weryfikowany
| `Succeeded` | Zestaw danych został zweryfikowany, a teraz może służyć do tworzenia czcionka głosowa

Po zakończeniu sprawdzania poprawności widać łączną liczbę wypowiedzi dopasowane do wszystkich zestawów danych w kolumnie wypowiedź.

Możesz pobrać raport, aby sprawdzić wyniki Wymowa i poziomu hałasu dla każdego z nagrań. Wymowa wynik z zakresu od 0 do 100; współczynnik poniżej 70 zwykle wskazuje niezgodność mowy błąd lub skryptu. Duże akcentu można zmniejszyć ocenę Wymowa i wpływu na wygenerowany głosu cyfrowych.

Większy współczynnik sygnał do szumu (SNR) wskazuje niższe szumu w audio. Zwykle mogą skorzystać z SNR ponad 50 przez rejestrowanie za pośrednictwem professional studios. Pliku dźwiękowego z SNR poniżej 20 może spowodować oczywiste szumu w wygenerowanym głosu.

Należy rozważyć ponowne nagrywanie wypowiedzi Wymowa niskie oceny lub niską współczynniki sygnał do szumu. Jeśli ponowne rejestrowanie nie jest możliwe, może być wykluczyć te wypowiedzi z zestawu danych.

## <a name="build-your-voice-font"></a>Twórz swoje czcionka głosowa

Po sprawdzeniu poprawności zestawu danych służy do tworzenia swojej niestandardowej czcionki głosowej. 

1. Wybierz **modeli** w menu rozwijanym "Custom Voice". 
 
    Zostanie wyświetlone tabeli Moje czcionki głosowe zawierające czcionek niestandardowych voice już utworzony.

1. Kliknij przycisk **tworzenia głosów** pod tytułem tabeli. 

    Zostanie wyświetlona strona tworzenia czcionka głosowa. Bieżących ustawień regionalnych jest wyświetlany w pierwszym wierszu tabeli. Zmień ustawienia regionalne, aby utworzyć głosu w innym języku. Ustawienia regionalne musi być taka sama jak zestawy danych używane do tworzenia głosu.

1. Tak jak w przypadku został przekazany zestaw danych, wprowadź nazwę i opis, aby pomóc w zidentyfikowaniu tego modelu. 

    Wprowadzona nazwa będzie nazwa umożliwia określanie głosu w Twoje żądanie dotyczące synteza mowy w ramach danych wejściowych SSML, więc należy wybrać uważnie. Tylko litery, cyfry i kilka znaków interpunkcyjnych, takich jak "-", "_" "(",")" są dozwolone.

    Typowym zastosowaniem pole opisu jest służy do rejestrowania nazw zestawów danych, które zostały użyte do utworzenia modelu.

1. Wybierz płeć czcionki głosowe. Musi on być zgodny płeć zestawu danych.

1. Wybierz zestawach danych, którego chcesz użyć do trenowania czcionka głosowa. Wszystkie zestawy danych używane muszą pochodzić z tej samej osoby mówiącej.

1. Kliknij przycisk **Utwórz** umożliwiającą utworzenie czcionki głosowe.

    ![Tworzenie modelu](media/custom-voice/create-model.png)

Nowy model pojawia się w tabeli Moje czcionki głosowe. 

![Czcionki głosowe](media/custom-voice/my-voice-fonts.png)

Pokazanym stanem odzwierciedla proces konwersji zestawu danych czcionki głosowej, jak pokazano poniżej.

| Stan | Znaczenie
| ----- | -------
| `NotStarted` | Twoje żądanie dotyczące tworzenia czcionki głosowe znajduje się w kolejce do przetworzenia
| `Running` | Trwa tworzenie czcionki głosowe
| `Succeeded` | Czcionki głosowe został utworzony i czy można wdrażać

Szkolenie czasu różni się zależnie od ilości przetworzonych danych audio. Typowy czas do zakresu od o setki wypowiedzi na 30 minut do 40 godzin wypowiedzi 20 000.

> [!NOTE]
> Użytkownicy bezpłatnej subskrypcji uczyć jeden czcionka głosowa w danym momencie. Standardowa subskrypcja użytkowników jednocześnie uczyć trzy głosów. Jeśli przekroczysz limit, zaczekaj co najmniej jeden umieszczać czcionki głosowe szkoleń, a następnie spróbuj ponownie.

## <a name="test-your-voice-font"></a>Testowanie czcionki głosowe

Po pomyślnym utworzeniu czcionki głosowe mogli ją przetestować, przed jego wdrożeniem. Kliknij przycisk **testu** w kolumnie operacje tabeli Moje czcionki głosowe. Strona testowa pojawia się dla czcionka głosowa wybrane. Tabela jest pusta, jeśli jeszcze nie przesłano żadnych żądań testu dla głosu.

Kliknij przycisk **testu z tekstem** pod tytuł tabeli, aby wyświetlić menu rozwijane do przesyłania żądań tekstu przycisku. Możesz przesłać żądanie testu w postaci zwykłego tekstu lub SSML. Maksymalny rozmiar danych wejściowych wynosi 1024 znaków, w tym wszystkie tagi SSML żądania. Język tekstu musi być taka sama jak język czcionki głosowe.

Po wypełniając pole tekstowe i potwierdzając tryb wprowadzania, kliknij przycisk **tak** przesłać żądanie testu, a następnie wróć do strony testowej. Tabela zawiera teraz wpis, który odnosi się do nowego żądania, a w kolumnie Stan znane obecnie. Może potrwać kilka minut, aby syntetyzowania mowy. Gdy w kolumnie Stan odczytuje zakończone powodzeniem, możesz pobrać wprowadzanie tekstu ( `.txt` pliku) i danych wyjściowych audio ( `.wav` pliku) i audition jego jakość.

## <a name="create-and-use-a-custom-endpoint"></a>Tworzenie i używanie niestandardowego punktu końcowego

Po pomyślnie tworzone i testowane modelu głosowego, wdrożyć ją w punkt końcowy niestandardowego tekstu na mowę. Następnie należy użyć tego punktu końcowego zamiast zwykle punktu końcowego w przypadku wysyłania żądań zamiany tekstu na mowę przy użyciu interfejsu API REST. Niestandardowy punkt końcowy można wywołać tylko przez subskrypcję, która umożliwia wdrażanie czcionki.

Aby utworzyć nowy niestandardowy punkt końcowy, wybierz **punktów końcowych** z Custom Voice menu w górnej części strony. Na stronie Moje wdrożone głosów pojawi się jego tabeli bieżącego głosu niestandardowych punktów końcowych, ewentualne. Bieżących ustawień regionalnych jest widoczny w pierwszym wierszu tabeli. Aby utworzyć wdrożenie w innym języku, należy zmienić wyświetlane ustawienia regionalne. (Musi być zgodna głosowych, które są wdrażane.)

Kliknij przycisk **wdrażanie głosów** przycisk, aby utworzyć nowy punkt końcowy. Wprowadź nazwę i Opis niestandardowego punktu końcowego.

Z menu subskrypcji Wybierz subskrypcję, której chcesz użyć. Użytkownicy bezpłatnej subskrypcji mogą mieć tylko jeden model wdrożone w danym momencie. Użytkownicy subskrypcji standardowej można utworzyć maksymalnie 20 punktów końcowych, każdy z własną niestandardowych voice.

![Tworzenie punktu końcowego](media/custom-voice/create-endpoint.png)

Po wybraniu modelu, który ma zostać wdrożona, kliknij przycisk **Utwórz**. Na stronie Moje wdrożone głosów pojawi się ponownie, teraz wpis dla nowego punktu końcowego. Może upłynąć kilka minut utworzyć nowy punkt końcowy. Gdy stan wdrożenia to Powodzenie, punkt końcowy jest gotowy do użycia.

![Moje wdrożone głosów](media/custom-voice/my-deployed-voices.png)

Gdy stan wdrożenia to Powodzenie, punkt końcowy usługi czcionka głosowa wdrożonej pojawia się w tabeli Moje wdrożone głosów. Można użyć tego identyfikatora URI, bezpośrednio w żądaniu HTTP.

Testowanie online punktu końcowego jest również dostępna za pośrednictwem portalu niestandardowych voice. Aby przetestować punktu końcowego usługi, wybierz **testowania punktów końcowych** z menu rozwijanego Custom Voice. Punkt końcowy testowania strony pojawi się. Wybierz wdrożonej głosu niestandardowe, a następnie wprowadź tekst, który ma być używany (w postaci zwykłego tekstu lub SSML format) w polu tekstowym.

> [!NOTE] 
> Korzystając z SSML, `<voice>` tag należy określić nazwę nadaną swój głos niestandardowych podczas jego tworzenia. Jeśli prześlesz zwykłego tekstu, głosu niestandardowego jest zawsze używana.

Kliknij przycisk **Odtwórz** poznać tekstu mówionego w swojej niestandardowej czcionki głosowej.

![Testowanie punktu końcowego](media/custom-voice/endpoint-testing.png)

Niestandardowy punkt końcowy jest funkcjonalnie identyczny standardowy punkt końcowy, używany do żądania zamiany tekstu na mowę. Zobacz [interfejsu API REST](rest-apis.md) Aby uzyskać więcej informacji.

## <a name="language-support"></a>Obsługa języków

Dostosowanie głos jest dostępna dla US English (en US), kontynent, chiński (zh-CN) i włoski (it-IT).

> [!NOTE]
> Szkolenia włoskojęzycznego rozpoczyna się od zestaw ponad 2000 wypowiedzi. Modele dwujęzyczny chiński angielski są również obsługiwane z zestawu danych w ponad 2000 wypowiedzi.

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
