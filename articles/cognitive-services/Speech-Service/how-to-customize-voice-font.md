---
title: Co to jest niestandardowa głosu? -Usługi azure kognitywnych | Dokumentacja firmy Microsoft
description: Ten artykuł Omówienie Microsoft tekst na mowę głosu dostosowanie, co pozwala na tworzenie głosu brand rozpoznawalnych, jeden z typu.
services: cognitive-services
author: noellelacharite
manager: nolach
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2018
ms.author: nolach
ms.openlocfilehash: ad5af799fd46dc51b85432999f986de8cdb056ec
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349348"
---
# <a name="creating-custom-voice-fonts"></a>Tworzenie niestandardowych głosu czcionek

Dostosowywanie głosu Microsoft tekst na mowę (TTS) umożliwia tworzenie rozpoznawalnych, jeden z rodzajem głosu użytkownika marki: *czcionki głosu.* 

Aby utworzyć czcionki głosowych, tworzenie nagrania studio i przekaż skojarzone skrypty jako dane szkoleniowe. Usługa tworzy następnie model unikatowy głosu dostosowana do nagrania. Następnie można tę czcionkę głosu syntetyzowania mowy. 

Możesz rozpocząć pracę z niewielką ilość danych do weryfikacji koncepcji. Ale większej ilości danych podasz, tym bardziej naturalnych i professional dźwięki głosu.

Dostosowywanie głos jest dostępny na US English (en US) i lądu chiński (zh-CN).

## <a name="prerequisites"></a>Wymagania wstępne

Funkcja dostosowywania głos tekst na mowę jest obecnie w podglądzie prywatnym. [Wypełnij formularz aplikacji](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0N8Vcdi8MZBllkZb70o6KdURjRaUzhBVkhUNklCUEMxU0tQMEFPMjVHVi4u) wziąć pod uwagę dostępu.

Wymagane są również:

* Konto platformy Azure ([Załóż bezpłatne konto](https://azure.microsoft.com/free/ai/) Jeśli nie masz jeszcze jeden).

* Subskrypcja usługi mowy. [Utworzyć](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) Jeśli jeszcze.

    ![Utwórz panelu](media/custom-voice/create-panel.png)

Po utworzeniu subskrypcji, możesz znaleźć dwa klucze subskrypcji na panelu Szybki Start lub omówienie nowej subskrypcji. Możesz użyć albo klucza.

Na koniec subskrypcji połączyć się z portalu głosu niestandardowe w następujący sposób.

1. Zaloguj się do [niestandardowe głosu portalu](https://customvoice.ai) przy użyciu tego samego konta Microsoft, używana do stosowania dostępu.

2. Przejdź do "Subskrypcje" w ramach nazwę konta w górnym rogu.

    ![Subskrypcje](media/custom-voice/subscriptions.png)

3. Na stronie "Subskrypcji" wybierz pozycję "Połącz istniejącą subskrypcję".

     ![Połącz istniejącą subskrypcję](media/custom-voice/connect-existing-sub.png)

4. Wklej klucz subskrypcji do tabeli, jak pokazano poniżej.

     ![Dodaj subskrypcję](media/custom-voice/add-subscription.png)

Możesz teraz przejść!

## <a name="prepare-recordings-and-transcripts"></a>Przygotowanie nagrań i zapisy

Zestaw danych szkoleniowych głosu składa się z zestawu plików audio, wraz z pliku tekstowego zawierającego zapisy wszystkich tych plików audio.

Należy przygotować tych plików w żadnym kierunku: albo napisać skrypt i odczytywane przez talenty głosu lub użyć publicznie dostępnych audio i wykonać ich transkrypcji na tekst. W drugim przypadku należy edytować disfluencies z plików audio, takich jak "um" i innych dźwięków wypełniacza stutters, słowa mumbled albo mispronunciations.

Do tworzenia czcionki dobrej głosowych, ważne jest, że nagrania są wykonywane w pokoju quiet mikrofon wysokiej jakości. Spójne woluminu mówiąc szybkość mówiąc gęstość i obszerne mannerisms mowy są niezbędne do tworzenia niezwykłych głosu cyfrowego. Aby utworzyć głosu do użytku produkcyjnego, firma Microsoft zaleca się, że używasz talenty professional nagrywanie głosu i studio.

### <a name="audio-files"></a>Pliki dźwiękowe

Każdy plik dźwiękowy powinien zawierać pojedynczy utterance (na przykład pojedynczy zdania lub pojedynczy Włącz systemu okna dialogowego). Wszystkie pliki muszą znajdować się w tym samym języku (wielojęzyczne głosy niestandardowe nie są obsługiwane). Pliki dźwiękowe musi również mieć unikatową nazwę liczbowych pliku z rozszerzeniem `.wav`.

Pliki dźwiękowe powinna być przygotowana w następujący sposób. Inne formaty są obsługiwane i zostaną odrzucone.

| **Właściwość** | **Wartość** |
| ------------ | --------- |
| Format pliku  | RIFF (WAV)|
| Częstotliwość próbkowania| 16 000 Hz |
| Kanały     | 1 (monophonic)  |
| Przykładowy Format| PCM 16-bitowych |
| Nazwa pliku    | Numeric ze `.wav` rozszerzenia |
| Format archiwum| ZIP      |
| Rozmiar maksymalny archiwum|200 MB|

Umieść zestaw plików audio w jednym folderze bez podkatalogi i pakietu całego zestawu jako pojedynczego pliku archiwum ZIP.

> [!NOTE]
> Portalu obecnie importuje ZIP archiwa do 200 MB. Jednak wiele archiwów mogą być przekazywane. Maksymalna liczba zestawów danych może to pliki 10 ZIP w bezpłatnej subskrypcji użytkowników i 50 użytkowników standardowych subskrypcji.

### <a name="transcripts"></a>Zapisy

Plik przekształcenia jest zwykły plik tekst Unicode (UTF-16 little-endian). Każdy wiersz w pliku przekształcenia musi mieć nazwę pliku audio, następuje znak tabulacji (punkt kodu 9), a na końcu jego zapis. Nie puste wiersze są dozwolone.

Na przykład:

```
0000000001  This is the waistline, and it's falling.
0000000002  We have trouble scoring.
0000000003  It was Janet Maslin.
```

System niestandardowych głosu normalizuje zapisy konwertowanie tekst na małe i usuwając nadmiarowe znaki interpunkcyjne. Jest ważne, czy zapisy z dokładnością do odpowiedniego nagrania audio 100%.

> [!TIP]
> Podczas tworzenia produkcji tekst na mowę głosy, wybierz zniesławiających (lub napisać skrypty) uwzględnieniu zarówno fonetyczne pokrycia, jak i wydajności.

## <a name="upload-your-datasets"></a>Przekaż zestawów danych

Po przygotowaniu Twoje archiwum plików audio i zapisy, przekazać je za pomocą [niestandardowe głosu portalu](https://customvoice.ai).

> [!NOTE]
> Po zostały przekazane, nie można edytować zestaw danych. Jeśli nie pamiętasz obejmują zapisy niektórych plików audio, na przykład lub przypadkowo wybierz niewłaściwy płci, możesz ponownie przekazać cały zestaw danych. Sprawdź ustawienia i zestawu danych, dokładnie przed rozpoczęciem przekazywania.

1. Zaloguj się do portalu.

2. Wybierz **danych** w obszarze głosu niestandardowych na stronie głównej. 

    ![Moje projekty](media/custom-voice/my-projects.png)

    Zostanie wyświetlona tabela Moje danych głosowych. Jest pusty, jeśli nie zostały jeszcze przekazane wszystkie zestawy danych głosowych.

3. Kliknij przycisk **importowania danych** aby otworzyć stronę o przekazywaniu nowy zestaw danych.

    ![Importowanie danych głosowych](media/custom-voice/import-voice-data.png)

4. Wprowadź nazwę i opis w dostępnych polach. 

5. Wybierz ustawienia regionalne czcionek głosu. Upewnij się, że informacji o ustawieniach regionalnych zgodnego z językiem danych rejestrowania i skrypty. 

6. Wybierz rodzaj prelegenta głosowych, którego używasz.

7. Wybierz skrypt i audio plików do przekazania. 

8. Kliknij przycisk **importu** przekazać dane. Dla większych zestawów danych importowania może potrwać kilka minut.

> [!NOTE]
> Użytkownicy bezpłatnej subskrypcji można przekazać dwóch zestawów danych w czasie. Użytkowników standardowych subskrypcji można jednocześnie przekazać pięć zestawów danych. Jeśli osiągnie limit, zaczekaj na co najmniej jeden z zestawów danych zakończeniu importowania, a następnie spróbuj ponownie.

Tabela danych głosowych Moje pojawi się ponownie po zakończeniu przekazywania. Wpis odpowiadający powinny pojawić się do zestawu danych just przekazany. 

Zestawy danych są weryfikowane automatycznie po przekazywania. Sprawdzanie poprawności danych zawiera szereg kontroli plików audio, aby sprawdzić ich format pliku, rozmiar i częstotliwość próbkowania. Kontroli nad plikami przekształcania Sprawdź format pliku i wykonywania niektórych normalizacji tekstu. Zniesławiających są zapisywane rozpoznawanie mowy i tekst wynikowy jest porównywana z wykaz, podane.

![Dane głosu](media/custom-voice/my-voice-data.png)

W poniższej tabeli przedstawiono przetwarzania stanów importowane zestawy danych. 

| Stan | Znaczenie
| ----- | -------
| `NotStarted` | Zestaw danych zostało odebrane i jest w kolejce do przetworzenia
| `Running` | Zestaw danych jest sprawdzana
| `Succeeded` | Zestaw danych została zweryfikowana i może teraz używać do tworzenia czcionki głosu

Po zakończeniu sprawdzania poprawności widać łączna liczba pasujących zniesławiających dla każdego z zestawów danych w kolumnie Utterance.

Można pobrać raportu, aby sprawdzić wyniki wymowy i poziomu hałasu dla każdego nagrań. Wymowa wynik w zakresie od 0 do 100; wynik poniżej 70 zazwyczaj wskazuje błąd lub skryptu niezgodność mowy. Duże akcent można zmniejszyć wynik wymowy i wpływ wygenerowanego głosu cyfrowego.

Wyższy stosunek sygnału do szumu (SNR) wskazuje niższe hałas w audio. Można zwykle osiągnąć SNR 50 + przez rejestrowanie za pośrednictwem Studio professional. Dźwięk z SNR poniżej 20 może spowodować szumu widocznych w wygenerowanym głosu.

Należy rozważyć ponowne rejestrowanie wszelkich zniesławiających z niskim wymowy wyniki lub niska stosunek sygnału do szumu. Jeśli nie jest możliwe ponowne nagrywanie, może wyłączyć tych zniesławiających z zestawu danych.

## <a name="build-your-voice-font"></a>Tworzenie czcionki głosu

Po sprawdzeniu poprawności zestawu danych można użyć go do tworzenia czcionki niestandardowe głosu. 

1. Wybierz **modeli** w menu rozwijanym "Głosu niestandardowa". 
 
    Zostanie wyświetlone tabeli Moje czcionki głosu wyświetlone czcionek głosu niestandardowych, które zostały już utworzone.

1. Kliknij przycisk **utworzyć głosy** pod tytułem tabeli. 

    Zostanie wyświetlona strona tworzenia czcionki głosu. Bieżące ustawienia regionalne jest wyświetlany w pierwszym wierszu tabeli. Zmień ustawienia regionalne, aby utworzyć głosu w innym języku. Ustawienia regionalne musi być taka sama jak zestawy danych używane do utworzenia głosu.

1. Tak samo jak po przekazaniu zestawu danych, wprowadź nazwę i opis, aby pomóc w zidentyfikowaniu tego modelu. 

    Nazwa wprowadzona tutaj będzie nazwę używaną do określ głosu w żądaniu dla syntezy mowy jako część dane wejściowe SSML, więc należy wybrać uważnie. Tylko litery, cyfry i kilku znaków interpunkcyjnych, takich jak "-", "_" "(",")" są dozwolone.

    Zazwyczaj pola Opis jest używane do rejestrowania nazw zestawów danych, które były używane w celu utworzenia modelu.

1. Wybierz płeć czcionki głosu. Musi on być zgodny z płci zestawu danych.

1. Wybierz zestawów danych, który ma być używany na potrzeby uczenia czcionki głosu. Wszystkich zestawów danych użytych muszą pochodzić z tej samej osoby mówiącej.

1. Kliknij przycisk **Utwórz** aby rozpocząć tworzenie czcionki głosu.

    ![Tworzenie modelu](media/custom-voice/create-model.png)

Nowy model pojawia się w tabeli Moje czcionki głosu. 

![Czcionki głosu](media/custom-voice/my-voice-fonts.png)

Wyświetlany stan odzwierciedla proces konwersji zestawu danych na czcionkę głosu, jak pokazano poniżej.

| Stan | Znaczenie
| ----- | -------
| `NotStarted` | Żądania tworzenia czcionki głos jest przechowywane w kolejce do przetworzenia
| `Running` | Trwa tworzenie czcionki głosu
| `Succeeded` | Czcionki głos został utworzony i mogą zostać wdrożone

Uczenie czasu różni się w zależności od ilości przetworzonych danych audio. Typowy czas należeć do zakresu od o setki zniesławiających na 30 minut do 40 godzin zniesławiających 20 000.

> [!NOTE]
> Bezpłatna subskrypcja użytkowników można uczenia dwie czcionki głosu naraz. Użytkownicy standardowe subskrypcji można jednocześnie uczenia trzy głosy. Jeśli osiągnie limit, poczekaj na zakończenie co najmniej jeden z czcionkami głosu szkolenia, a następnie spróbuj ponownie.

## <a name="test-your-voice-font"></a>Testowanie czcionki głosu

Po pomyślnym utworzeniu czcionki głosowych, przetestować go przed jego wdrożeniem. Kliknij przycisk **testu** w kolumnie operacji. Czcionki wybrany głos zostanie wyświetlona strona testu. Tabela jest pusta, jeśli jeszcze nie zostało przesłane żądań testu dla głosu.

![Czcionki głosowych, część 2](media/custom-voice/my-voice-fonts2.png)

Kliknij przycisk **testu z tekstem** przycisku w obszarze tytuł tabeli, aby wyświetlić menu rozwijane do przesyłania żądań tekstu. Można przesłać żądania testu w postaci zwykłego tekstu lub SSML. Maksymalny rozmiar wejściowe to 1024 znaków, w tym wszystkie tagi dla żądania SSML. Język tekstu musi być taka sama jak język czcionki głosu.

![Głosowych testowania czcionki](media/custom-voice/voice-font-testing.png)

Po wypełnieniu w polu tekstowym i potwierdzenie tryb wprowadzania, kliknij przycisk **tak** Aby przesłać żądanie testu i wrócić do strony testowej. Tabela zawiera teraz wpis odpowiadający nowe żądanie, a w kolumnie Stan znane obecnie. Może upłynąć kilka minut, aby z syntetyzowania mowy. Gdy w kolumnie Stan odczytuje zakończyło się pomyślnie, możesz pobrać wprowadzanie tekstu ( `.txt` plików) i wyjścia audio ( `.wav` plików) i audition ostatnie o wysokiej jakości.

![Testowanie czcionki głosowych, część 2](media/custom-voice/voice-font-testing2.png)

## <a name="create-and-use-a-custom-endpoint"></a>Tworzenie i używanie niestandardowej punktu końcowego

Po pomyślnie utworzone i przetestowane modelu głosowych, wdrożyć ją w punkt końcowy niestandardowy tekst na mowę. Następnie należy użyć tego punktu końcowego zamiast standardowego punktu końcowego w przypadku wysyłania żądań tekst na mowę za pośrednictwem interfejsu API REST. Niestandardowe punktu końcowego może być wywoływany tylko przez subskrypcję, która umożliwia wdrażanie czcionki.

Aby utworzyć nowy punkt końcowy niestandardowe, wybierz **punkty końcowe** z menu niestandardowych głosowe w górnej części strony. Wdrożenie zostanie wyświetlona strona, z jego tabeli bieżący głos niestandardowe punkty końcowe, jeśli istnieją.

Kliknij przycisk **wdrażanie głosy** przycisk, aby utworzyć nowy punkt końcowy. Strona w punkcie końcowym tworzenie"bieżące ustawienia regionalne jest widoczny w pierwszym wierszu tabeli. Aby utworzyć wdrożenie w innym języku, należy zmienić wyświetlane ustawienia regionalne. (Musi być zgodna głosowych, które wdrażasz.) Wprowadź nazwę i opis niestandardowe punktu końcowego.

Z menu subskrypcji Wybierz subskrypcję, która ma być używany. Bezpłatna subskrypcja użytkownicy mogą mieć tylko jeden model wdrożone w czasie. Standardowa subskrypcji użytkownicy mogą tworzyć maksymalnie 20 punktów końcowych, każda z własnego niestandardowego głosu.

![Tworzenie punktu końcowego](media/custom-voice/create-endpoint.png)

Po wybraniu modelu, który ma zostać wdrożony, kliknij przycisk **Utwórz**. Strona wdrażania zostanie wyświetlony ponownie, teraz wpis dla nowego punktu końcowego. Może upłynąć kilka minut, można utworzyć nowy punkt końcowy. Gdy stan wdrożenia jest zakończyło się powodzeniem, punkt końcowy jest gotowy do użycia.

![Moje wdrożonej głosy](media/custom-voice/my-deployed-voices.png)

Gdy stan wdrożenia jest zakończyło się powodzeniem, punkt końcowy czcionki wdrożonej głosu pojawia się w tabeli głosy wdrożone. Ten identyfikator URI można użyć bezpośrednio w żądaniu HTTP.

Testowanie online punktu końcowego jest również dostępna za pośrednictwem portalu niestandardowych głosu. Aby przetestować punktu końcowego, wybierz **testowania punktów końcowych** z menu rozwijanego głosu niestandardowe. Punkt końcowy testowania strony zostanie wyświetlone. Wybierz głosu, które zostały wdrożone, a następnie wprowadź tekst, który ma być wymawiane (w postaci zwykłego tekstu lub SSML format) w polu tekstowym.

> [!NOTE] 
> Korzystając z SSML, `<voice>` tagu należy określić nazwę nadaną niestandardowych głosu podczas jego tworzenia.

Kliknij przycisk **odtwarzanie** usłyszeć tekst używany w czcionki niestandardowe głosu.

![Testowanie punktu końcowego](media/custom-voice/endpoint-testing.png)

Niestandardowe punkt końcowy jest funkcjonalnie taki sam jak standardowy punkt końcowy dla żądań tekst na mowę. Zobacz [interfejsu API REST](rest-apis.md) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz wersję próbną subskrypcji mowy](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznawanie mowy w języku C#](quickstart-csharp-windows.md)
