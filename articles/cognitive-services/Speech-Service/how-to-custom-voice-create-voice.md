---
title: Tworzenie niestandardowych Voice - usług przetwarzania mowy
titlesuffix: Azure Cognitive Services
description: Gdy wszystko będzie gotowe do przekazania danych, przejdź do portalu usługi Custom Voice. Utwórz lub wybierz projekt Custom Voice. Projekt musi udostępniać prawo języka/ustawienia regionalne i właściwości płeć jako dane chcesz użyć do trenowania Twojego głosu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 6189ea2866d1c16f994179df0179e29353e6c47d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65410709"
---
# <a name="create-a-custom-voice"></a>Tworzenie niestandardowych Voice

W [przygotowuje dane do Custom Voice](how-to-custom-voice-prepare-data.md), firma Microsoft opisano różne typy danych można użyć do szkolenia niestandardowych połączeń głosowych i wymagania dotyczące innego formatu. Po przygotowaniu danych można uruchomić przekazują je do [portal Custom Voice](https://aka.ms/custom-voice-portal), lub za pośrednictwem interfejsu API szkolenia Custom Voice. Tutaj opisano kroki szkolenia niestandardowych voice za pośrednictwem portalu.

> [!NOTE]
> Na tej stronie przyjęto założenie, przeczytanie [wprowadzenie Custom Voice](how-to-custom-voice.md) i [przygotowania danych dla niestandardowych Voice](how-to-custom-voice-prepare-data.md)i utworzony projekt Custom Voice.

Sprawdź języki obsługiwane w przypadku niestandardowych voice: [języka do dostosowania](language-support.md#customization).

## <a name="upload-your-datasets"></a>Przekazywanie swoich zestawów danych

Gdy wszystko będzie gotowe do przekazania danych, przejdź do [portal Custom Voice](https://aka.ms/custom-voice-portal). Utwórz lub wybierz projekt Custom Voice. Projekt musi udostępniać prawo języka/ustawienia regionalne i właściwości płeć jako dane chcesz użyć do trenowania Twojego głosu. Na przykład wybierz `en-GB` Jeśli nagrania audio, masz odbywa się w języku angielskim z motywem Zjednoczone Królestwo.

Przejdź do **danych** kartę, a następnie kliknij przycisk **przekazywanie danych**. W kreatorze Wybierz prawidłowy typ danych zgodną zostały przygotowane.

Każdy zestaw danych, które zostaną przesłane, musi spełniać wymagania dla typu danych, który wybierzesz. Jest to ważne, aby poprawnie sformatować dane przed przesłaniem. Gwarantuje to, że dane będą dokładnie przetwarzane przez usługę Custom Voice. Przejdź do [przygotowania danych dla niestandardowych Voice](how-to-custom-voice-prepare-data.md) i upewnij się, gamę sformatowane dane.

> [!NOTE]
> Użytkownicy bezpłatnej subskrypcji (F0) można jednocześnie przekazać dwóch zestawów danych. Subskrypcja standardowa (S0) użytkowników jednocześnie przekazać pięć zestawów danych. Jeśli przekroczysz limit, poczekaj na zakończenie co najmniej jeden z zestawów danych, importowania. Następnie spróbuj ponownie.

> [!NOTE]
> Maksymalna liczba zestawów danych, można zaimportować na subskrypcję jest 10 zip za darmo plików użytkownicy subskrypcji (F0) oraz 500 użytkowników subskrypcja standardowa (S0).

Zestawy danych są automatycznie zweryfikowana, po naciśnięciu przycisku przekazywania. Sprawdzanie poprawności danych zawiera serię kontroli plików audio, aby sprawdzić ich format pliku, rozmiar i częstotliwość próbkowania. Napraw błędy, jeśli istnieje i Prześlij ponownie. Po pomyślnym zainicjowaniu żądania importowanie danych, powinien zostać wyświetlony wpis w tabeli danych, która odnosi się do zestawu danych, który właśnie został przekazany.

W poniższej tabeli przedstawiono stany przetwarzania dla zaimportowanych zestawów danych:

| Stan | Znaczenie |
| ----- | ------- |
| Przetwarzanie | Zestaw danych zostało odebrane i Trwa przetwarzanie. |
| Powodzenie | Zestaw danych został zweryfikowany i może teraz służyć do tworzenia modeli głosowych. |
| Niepowodzenie | Zestaw danych nie powiodła się podczas przetwarzania z powodu wielu przyczyn, na przykład błędy w pliku danych problemy lub problemy z siecią. |

Po zakończeniu sprawdzania poprawności widać łączną liczbę wypowiedzi dopasowane do wszystkich zestawów danych w **wypowiedzi** kolumny. Jeśli typ danych, który wybrano wymaga długich audio segmentacji, ta kolumna tylko odzwierciedla wypowiedzi, które firma Microsoft ma segmentowanych dla Ciebie albo na podstawie Twojej transkrypcji lub za pośrednictwem usługi transkrypcja mowy. Dodatkowo można pobrać weryfikowane tak, aby wyświetlić szczegółowe wyniki wypowiedzi zostały pomyślnie zaimportowane i ich zapisy mapowanie zestawu danych. Wskazówka: long-audio segmentacji może potrwać ponad godzinę na zakończenie przetwarzania danych.

W przypadku zestawów danych en US i zh-CN Dodatkowo można pobrać raport, aby sprawdzić wyniki Wymowa i poziomu hałasu dla każdego z nagrań. Wymowa wynik z zakresu od 0 do 100. Współczynnik poniżej 70 zwykle wskazuje niezgodność mowy błąd lub skryptu. Duże akcentu można zmniejszyć ocenę Wymowa i wpływu na wygenerowany głosu cyfrowych.

Większy współczynnik sygnał do szumu (SNR) wskazuje niższe szumu w audio. Zwykle mogą skorzystać z SNR ponad 50 przez rejestrowanie w Studio professional. Pliku dźwiękowego z SNR poniżej 20 może spowodować oczywiste szumu w wygenerowanym głosu.

Należy rozważyć ponowne nagrywanie wypowiedzi Wymowa niskie oceny lub niską współczynniki sygnał do szumu. Jeśli nie można ponownie zarejestrować, może być wykluczyć te wypowiedzi z zestawu danych.

## <a name="build-your-custom-voice-model"></a>Tworzenie modelu niestandardowego głosu

Po sprawdzeniu poprawności zestawu danych służy do tworzenia modelu niestandardowego głosu.

1.  Przejdź do **zamiany tekstu na mowę > Custom Voice > szkolenia**.

2.  Kliknij przycisk **Train model**.

3.  Następnie wprowadź **nazwa** i **opis** umożliwia identyfikowanie tego modelu.

    Wybierz nazwę, dokładnie. Wprowadzona nazwa będzie nazwa, używanej do określania głosu w Twoje żądanie dotyczące synteza mowy w ramach SSML danych wejściowych. Tylko litery, cyfry i kilka znaków interpunkcyjnych takich, jak - \_oraz (',') są dozwolone. Używać różnych nazw modeli głosowych różne.

    Typowym zastosowaniem **opis** pole służy do rejestrowania nazw zestawów danych, które zostały użyte do utworzenia modelu.

4.  Z **wybierz dane szkoleniowe** wybierz jednego lub wielu zestawów danych, które chcesz użyć do trenowania. Sprawdź liczbę wypowiedzi przed ich przesłaniem. Można uruchomić z dowolnej liczby wypowiedzi en US i zh-CN modeli głosowych. Dla innych ustawień regionalnych możesz wybrać więcej niż 2000 wypowiedzi, aby mieć możliwość nauczenia głosu.

    > [!NOTE]
    > Zduplikowane nazwy audio zostaną usunięte z szkolenia. Upewnij się, że zestawy danych, którą wybierzesz nie zawierają tych samych nazw audio na wiele plików zip.

    > [!TIP]
    > Przy użyciu zestawów danych z tej samej osoby mówiącej jest wymagana dla jakości wyników. W przypadku zestawów danych, który prześlesz do trenowania zawierają łączna liczba mniejsza niż 6000 wypowiedzi distinct, będzie uczenie modelu głosowych za pomocą techniki statystyczne syntezy parametrycznych. W przypadku, gdy danych szkoleniowych przekracza łączna liczba 6000 wypowiedzi distinct będzie uruchamiał proces szkolenia technika syntezy łączenia. Zwykle technologii łączenia może spowodować stosować bardziej naturalne i pozwala uzyskać większą wierność tonu wyników. [Skontaktuj się z zespołem Custom Voice](mailto:speechsupport@microsoft.com) mają uczenia modelu przy użyciu najnowszych technologii neuronowych TTS osiągalnych cyfrowego głosu odpowiednikiem dostępny publicznie [neuronowych głosów](language-support.md#neural-voices).

5.  Kliknij przycisk **Train** umożliwiającą utworzenie modelu głosu.

Tabela szkolenia wyświetla nowy wpis, który odpowiada na tę nowo utworzoną modelu. Tabela pokazuje również stanu: Przetwarzania, zakończyło się powodzeniem, zakończone niepowodzeniem.

Stan, który jest wyświetlany odzwierciedla proces konwersji zestawu danych do modelu głosowego, jak pokazano poniżej.

| Stan | Znaczenie |
| ----- | ------- |
| Przetwarzanie | Trwa tworzenie modelu głosu. |
| Powodzenie | Model głos został utworzony i którą można wdrożyć. |
| Niepowodzenie | Model głosu nie powiodła się w szkolenia, ze względu na wiele przyczyn, na przykład niewidzianych danych problemy lub problemy z siecią. |

Szkolenie czasu różni się zależnie od ilości przetworzonych danych audio. Typowy czas do zakresu od o setki wypowiedzi na 30 minut do 40 godzin wypowiedzi 20 000. Gdy instalacja się powiodła szkolenia modelu można uruchomić ją przetestować.

> [!NOTE]
> Użytkownicy bezpłatnej subskrypcji (F0) można jednocześnie przeszkolić jeden czcionka głosowa. Subskrypcja standardowa (S0) użytkowników jednocześnie uczyć trzy głosów. Jeśli przekroczysz limit, zaczekaj co najmniej jeden umieszczać czcionki głosowe szkoleń, a następnie spróbuj ponownie.

> [!NOTE]
> Maksymalna liczba modeli głosowych może być uczony każdej subskrypcji wynosi 10 modeli dla użytkowników bezpłatnej subskrypcji (F0) do 100 użytkowników subskrypcja standardowa (S0).

## <a name="test-your-voice-model"></a>Testowanie modelu głosowego

Po pomyślnym utworzeniu czcionki głosowe, można ją przetestować, przed jego wdrożeniem.

1.  Przejdź do **zamiany tekstu na mowę > Custom Voice > testowania**.

2.  Kliknij przycisk **Dodaj test**.

3.  Wybierz jednego lub wielu modeli, które chcesz przetestować.

4.  Podaj tekst, który ma voice(s) mówić. Jeśli wybrano testowanie wielu modeli, w tym samym czasie, ten sam tekst będzie służyć do testowania dla różnych modeli.

    > [!NOTE]
    > Język tekstu musi być taka sama jak język czcionki głosowe. Można przetestować tylko pomyślnie przeszkolone modele. Tylko zwykły tekst jest obsługiwany w tym kroku.

5.  Kliknij pozycję **Utwórz**.

Po przesłaniu żądania testu, nastąpi powrót do strony testowej. Tabela zawiera teraz wpis, który odpowiada nowe żądanie i w kolumnie Stan. Może potrwać kilka minut, aby syntetyzowania mowy. Gdy w kolumnie Stan jest wyświetlany komunikat **Powodzenie**, możesz odtwarzać dźwięk lub pobrać wprowadzanie tekstu (pliku txt), jak i audio danych wyjściowych (plik WAV), a dodatkowo audition jego jakości.

Można również znaleźć na stronie szczegółów każdego modeli, wybrana przez Ciebie wyniki testu do testowania. Przejdź do **szkolenia** kartę, a następnie kliknij nazwę modelu, aby wprowadzić na stronie szczegółów modelu.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Tworzenie i używanie punktu końcowego niestandardowych voice

Po pomyślnie tworzone i testowane modelu głosowego, wdrożyć ją w niestandardowy punkt końcowy zamiany tekstu na mowę. Następnie należy użyć tego punktu końcowego zamiast zwykle punktu końcowego w przypadku wysyłania żądań zamiany tekstu na mowę przy użyciu interfejsu API REST. Niestandardowego punktu końcowego można wywoływać tylko za pomocą użytej do wdrożenia czcionki subskrypcji.

Aby utworzyć nowy punkt końcowy niestandardowego głosu, przejdź do **zamiany tekstu na mowę > Custom Voice > wdrożenia**. Wybierz **Dodaj punkt końcowy** i wprowadź **nazwa** i **opis** do niestandardowego punktu końcowego. Następnie wybierz model głosowy niestandardowych, które chcesz skojarzyć z tym punktem końcowym.

Po kliknięciu **Dodaj** przycisku w tabeli punktów końcowych, zostanie wyświetlony wpis dla nowego punktu końcowego. Może upłynąć kilka minut utworzyć nowy punkt końcowy. Gdy stan wdrożenia jest **Powodzenie**, punkt końcowy jest gotowy do użycia.

> [!NOTE]
> Użytkownicy bezpłatnej subskrypcji (F0) mogą mieć tylko jeden model wdrożone. Subskrypcja standardowa (S0) użytkownicy mogą tworzyć maksymalnie 50 punktów końcowych, każdy z własną niestandardowych voice.

> [!NOTE]
> Aby użyć niestandardowego głosu, musi określić nazwę modelu głosowego, użyć niestandardowy identyfikator URI bezpośrednio w żądaniu HTTP i Użyj tej samej subskrypcji, aby przekazać za pomocą uwierzytelniania usługi TTS.

Po wdrożeniu punkt końcowy, nazwę punktu końcowego, który pojawia się jako link. Kliknij link, aby wyświetlić informacje dotyczące punktu końcowego usługi, takich jak klucza punktu końcowego adresu URL punktu końcowego i przykładowy kod.

Testowanie online punktu końcowego jest również dostępna za pośrednictwem portalu niestandardowych voice. Aby przetestować punktu końcowego usługi, wybierz **Sprawdź punkt końcowy** z **szczegóły punktu końcowego** strony. Punkt końcowy testowania strony pojawi się. Wprowadź tekst, który ma być używany (albo jako zwykły tekst lub [SSML format](speech-synthesis-markup.md) w polu tekstowym. Aby usłyszeć tekstu mówionego w swojej niestandardowej czcionki głosowej, wybierz **Odtwórz**. Ta funkcja testowania zostanie obciążona użycie synteza mowy niestandardowej.

Niestandardowy punkt końcowy jest funkcjonalnie identyczny standardowy punkt końcowy, który służy do żądania zamiany tekstu na mowę. Zobacz [interfejsu API REST](rest-text-to-speech.md) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki

* [Przewodnik: Zarejestruj swoje próbki głosu](record-custom-voice-samples.md)
* [Dokumentacja interfejsu API zamiany tekstu na mowę](rest-text-to-speech.md)
