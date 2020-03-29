---
title: Tworzenie usługi niestandardowego głosu — mowę
titleSuffix: Azure Cognitive Services
description: Gdy będziesz gotowy do przekazywania danych, przejdź do portalu Niestandardowy głos. Utwórz lub wybierz projekt Niestandardowy głos. Projekt musi współużytkować odpowiedni język/ustawienia regionalne i właściwości płci jako dane, których zamierzasz użyć do treningu głosowego.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: bbe1d651a7d2d2cac1b1aa78b815b2797ad185c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717326"
---
# <a name="create-a-custom-voice"></a>Tworzenie niestandardowego głosu

W [obszarze Przygotuj dane dla niestandardowego głosu](how-to-custom-voice-prepare-data.md)opisaliśmy różne typy danych, których można użyć do szkolenia niestandardowego głosu i wymagania dotyczące różnych formatów. Po przygotowaniu danych można rozpocząć przesyłanie ich do [portalu Custom Voice](https://aka.ms/custom-voice-portal)lub za pośrednictwem interfejsu API szkolenia Niestandardowego Głosu. Poniżej opisujemy kroki szkolenia niestandardowy głos za pośrednictwem portalu.

> [!NOTE]
> Na tej stronie przyjęto założenie, że przeczytałeś [artykuł Wprowadzenie do pracy z niestandardowym głosem](how-to-custom-voice.md) i przygotuj dane dla [niestandardowego głosu](how-to-custom-voice-prepare-data.md)i utworzono projekt Niestandardowy głos.

Sprawdź języki obsługiwane dla niestandardowego głosu: [język dostosowywania](language-support.md#customization).

## <a name="upload-your-datasets"></a>Przekazywanie zestawów danych

Gdy będziesz gotowy do przesłania danych, przejdź do [portalu Niestandardowy głos](https://aka.ms/custom-voice-portal). Utwórz lub wybierz projekt Niestandardowy głos. Projekt musi współużytkować odpowiedni język/ustawienia regionalne i właściwości płci jako dane, których zamierzasz użyć do treningu głosowego. Na przykład `en-GB` wybierz, czy nagrania audio, które masz, są wykonywane w języku angielskim z brytyjskim akcentem.

Przejdź do karty **Dane** i kliknij pozycję **Przekaż dane**. W kreatorze wybierz poprawny typ danych, który pasuje do przygotowanego.

Każdy przesłany zestaw danych musi spełniać wymagania dotyczące wybranego typu danych. Ważne jest, aby poprawnie sformatować dane przed ich przesłaniem. Gwarantuje to, że dane będą dokładnie przetwarzane przez usługę Custom Voice. Przejdź do [strony Przygotuj dane dla niestandardowego głosu](how-to-custom-voice-prepare-data.md) i upewnij się, że dane zostały słusznie sformatowane.

> [!NOTE]
> Użytkownicy bezpłatnej subskrypcji (F0) mogą przesyłać dwa zestawy danych jednocześnie. Użytkownicy subskrypcji standardowej (S0) mogą przesyłać pięć zestawów danych jednocześnie. Jeśli osiągniesz limit, poczekaj, aż co najmniej jeden z zestawów danych zakończy importowanie. Następnie spróbuj ponownie.

> [!NOTE]
> Maksymalna liczba zestawów danych, które mogą być importowane na subskrypcję, to 10 plików zip dla użytkowników korzystających z bezpłatnej subskrypcji (F0) i 500 dla użytkowników subskrypcji standardowej (S0).

Zestawy danych są automatycznie sprawdzane po naciśnięciu przycisku przesyłania. Sprawdzanie poprawności danych obejmuje serię kontroli plików audio w celu zweryfikowania ich formatu pliku, rozmiaru i częstotliwości próbkowania. Naprawić błędy, jeśli istnieją i przesłać ponownie. Po pomyślnym zainicjowaniu żądania importowania danych w tabeli danych powinien zostać wyświetlony wpis odpowiadający właśnie przesłanego zestawu danych.

W poniższej tabeli przedstawiono stany przetwarzania importowanych zestawów danych:

| Stan | Znaczenie |
| ----- | ------- |
| Przetwarzanie | Twój zestaw danych został odebrany i jest przetwarzany. |
| Powodzenie | Zestaw danych został zweryfikowany i może być teraz używany do tworzenia modelu głosu. |
| Niepowodzenie | Zestaw danych nie powiódł się podczas przetwarzania z wielu powodów, na przykład błędów plików, problemów z danymi lub problemów z siecią. |

Po zakończeniu sprawdzania poprawności, można zobaczyć całkowitą liczbę dopasowanych wypowiedzi dla każdego z zestawów danych w **wypowiedzi** kolumny. Jeśli wybrany typ danych wymaga segmentacji długiego dźwięku, ta kolumna odzwierciedla tylko wypowiedzi, które dzieliliśmy na segmenty na podstawie transkrypcji lub za pośrednictwem usługi transkrypcji mowy. Można dodatkowo pobrać zestaw danych zweryfikowane, aby wyświetlić szczegółowe wyniki wypowiedzi pomyślnie zaimportowane i ich transkrypcje mapowania. Wskazówka: segmentacja długiego dźwięku może potrwać ponad godzinę, aby zakończyć przetwarzanie danych.

W przypadku zestawów danych en-US i zh-CN można dodatkowo pobrać raport, aby sprawdzić wyniki wymowy i poziom hałasu dla każdego nagrania. Wynik wymowy waha się od 0 do 100. Wynik poniżej 70 zwykle wskazuje błąd mowy lub niezgodność skryptu. Ciężki akcent może zmniejszyć wynik wymowy i wpłynąć na wygenerowany cyfrowy głos.

Wyższy stosunek sygnału do szumu (SNR) oznacza niższy poziom szumu w dźwięku. Zazwyczaj możesz osiągnąć 50+ SNR, nagrywając w profesjonalnych studiach. Dźwięk z SNR poniżej 20 może spowodować oczywisty hałas w generowanym głosie.

Należy rozważyć ponowne rejestrowanie wypowiedzi z niskimi wynikami wymowy lub słabymi współczynnikami sygnału do szumu. Jeśli nie można ponownie zarejestrować, można wykluczyć te wypowiedzi z zestawu danych.

## <a name="build-your-custom-voice-model"></a>Zbuduj swój niestandardowy model głosu

Po sprawdzeniu poprawności zestawu danych można go użyć do utworzenia niestandardowego modelu głosu.

1.  Przejdź do szkolenia > > > > tekstu na **mowę**.

2.  Kliknij **pozycję Model pociągu**.

3.  Następnie wprowadź **nazwę** i **opis,** aby ułatwić identyfikację tego modelu.

    Wybierz nazwę ostrożnie. Nazwa, którą wprowadzisz w tym miejscu, będzie nazwą używana do określenia głosu w żądaniu syntezy mowy jako część danych wejściowych SSML. Dozwolone są tylko litery, cyfry i kilka znaków \_interpunkcyjnych, takich jak -, , i (', ') . Używaj różnych nazw dla różnych modeli głosowych.

    Typowym zastosowaniem pola **Opis** jest rejestrowanie nazw zestawów danych, które zostały użyte do utworzenia modelu.

4.  Na stronie **Wybierz dane szkoleniowe** wybierz jeden lub wiele zestawów danych, których chcesz użyć do szkolenia. Sprawdź liczbę wypowiedzi przed ich przesłaniem. Można rozpocząć od dowolnej liczby wypowiedzi dla modeli głosowych en-US i zh-CN. W przypadku innych ustawień regionalnych należy wybrać więcej niż 2000 wypowiedzi, aby móc trenować głos.

    > [!NOTE]
    > Zduplikowane nazwy audio zostaną usunięte ze szkolenia. Upewnij się, że wybrane zestawy danych nie zawierają tych samych nazw audio w wielu plikach zip.

    > [!TIP]
    > Korzystanie z zestawów danych z tego samego głośnika jest wymagane dla wyników jakości. Gdy zestawy danych przesłane do szkolenia zawierają łączną liczbę mniej niż 6000 odrębnych wypowiedzi, będzie szkolić model głosu za pomocą statystycznej techniki syntezy parametrycznej. W przypadku, gdy dane szkolenia przekracza całkowitą liczbę 6000 odrębnych wypowiedzi, rozpocznie się proces szkolenia z techniką syntezy konkadacyjnej. Zwykle technologia łączenia może spowodować bardziej naturalne i wyższe wyniki głosu wierności. [Jeśli](https://go.microsoft.com/fwlink/?linkid=2108737) chcesz wyszkolić model z najnowszą technologią Neural TTS, można uzyskać cyfrowy głos odpowiadający publicznie dostępnym [głosom neuronowym.](language-support.md#neural-voices)

5.  Kliknij **przycisk Trenuj,** aby rozpocząć tworzenie modelu głosu.

W tabeli Szkolenia jest wyświetlany nowy wpis odpowiadający temu nowo utworzonemu modelowi. W tabeli wyświetlany jest również stan: Przetwarzanie, Powodzenie, Niepowodzenie.

Wyświetlany stan odzwierciedla proces konwersji zestawu danych na model głosowy, jak pokazano poniżej.

| Stan | Znaczenie |
| ----- | ------- |
| Przetwarzanie | Tworzony jest model głosu. |
| Powodzenie | Model głosu został utworzony i można go wdrożyć. |
| Niepowodzenie | Model głosu nie został powiódł się w szkoleniu z wielu powodów, na przykład niewidocznych problemów z danymi lub problemów z siecią. |

Czas szkolenia zależy od ilości przetwarzanych danych audio. Typowe czasy wahają się od około 30 minut dla setek wypowiedzi do 40 godzin dla 20 000 wypowiedzi. Po pomyślnym zakończeniu szkolenia modelu można rozpocząć jego testowanie.

> [!NOTE]
> Użytkownicy bezpłatnej subskrypcji (F0) mogą jednocześnie szkolić jedną czcionkę głosową. Użytkownicy subskrypcji standardowej (S0) mogą trenować trzy głosy jednocześnie. Jeśli osiągniesz limit, poczekaj, aż co najmniej jedna z czcionek głosowych zakończy szkolenie, a następnie spróbuj ponownie.

> [!NOTE]
> Maksymalna liczba modeli głosowych, które mogą być przeszkolone na subskrypcję, to 10 modeli dla użytkowników korzystających z bezpłatnej subskrypcji (F0) i 100 dla użytkowników subskrypcji standardowej (S0).

Jeśli korzystasz z funkcji szkolenia głosu neuronowego, możesz wyszkolić model zoptymalizowany pod kątem scenariuszy przesyłania strumieniowego w czasie rzeczywistym lub model neuronowy HD zoptymalizowany pod kątem asynchronizującej [syntezy długiego dźwięku.](long-audio-api.md)  

## <a name="test-your-voice-model"></a>Testowanie modelu głosu

Po pomyślnym sbudowaniu czcionki głosowej można ją przetestować przed wdrożeniem do użycia.

1.  Przejdź do **> testowania > > tekstu na mowę**.

2.  Kliknij **pozycję Dodaj test**.

3.  Wybierz jeden lub wiele modeli, które chcesz przetestować.

4.  Podaj tekst, który ma być głosem.In provide the text you want the voice(s) to speak.Provide the text you want the voice(s) to speak.Provide the text Jeśli wybrano do testowania wielu modeli w tym samym czasie, ten sam tekst będzie używany do testowania dla różnych modeli.

    > [!NOTE]
    > Język tekstu musi być taki sam jak język czcionki głosowej. Tylko pomyślnie przeszkolonych modeli mogą być testowane. W tym kroku obsługiwany jest tylko zwykły tekst.

5.  Kliknij przycisk **Utwórz**.

Po przesłaniu żądania testu powrócisz do strony testowej. Tabela zawiera teraz wpis odpowiadający nowemu żądaniu i kolumnie stanu. Synteza mowy może potrwać kilka minut. Gdy kolumna stanu mówi **Powiódł się,** można odtworzyć dźwięk lub pobrać dane wejściowe tekstu (plik txt) i wyjście audio (plik .wav) i dodatkowo przesłuchać go pod kątem jakości.

Wyniki testów można również znaleźć na stronie szczegółów każdego modelu wybranego do testowania. Przejdź do karty **Szkolenie** i kliknij nazwę modelu, aby wejść na stronę szczegółów modelu.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Tworzenie niestandardowego punktu końcowego głosu i używanie ich

Po pomyślnym utworzeniu i przetestowaniu modelu głosu można go wdrożyć w niestandardowym punkcie końcowym zamiany tekstu na mowę. Następnie należy użyć tego punktu końcowego zamiast zwykłego punktu końcowego podczas wysyłania żądań zamiany tekstu na mowę za pośrednictwem interfejsu API REST. Niestandardowy punkt końcowy można wywołać tylko przez subskrypcję, która została użyta do wdrożenia czcionki.

Aby utworzyć nowy niestandardowy punkt końcowy głosu, przejdź do funkcji Zamiana > > **> niestandardowego >.** Wybierz **pozycję Dodaj punkt końcowy** i wprowadź **nazwę** i **opis** niestandardowego punktu końcowego. Następnie wybierz niestandardowy model głosu, który chcesz skojarzyć z tym punktem końcowym.

Po kliknięciu przycisku **Dodaj** w tabeli punktu końcowego zostanie wyświetlony wpis dla nowego punktu końcowego. Może upłynąć kilka minut, aby utworzyć wystąpienie nowego punktu końcowego. Gdy stan wdrożenia jest **pomyślnie**, punkt końcowy jest gotowy do użycia.

> [!NOTE]
> Użytkownicy bezpłatnej subskrypcji (F0) mogą mieć wdrożony tylko jeden model. Użytkownicy subskrypcji standardowej (S0) mogą tworzyć maksymalnie 50 punktów końcowych, z których każdy ma własny głos niestandardowy.

> [!NOTE]
> Aby użyć niestandardowego głosu, należy określić nazwę modelu głosowego, użyć niestandardowego identyfikatora URI bezpośrednio w żądaniu HTTP i użyć tej samej subskrypcji, aby przejść przez uwierzytelnianie usługi TTS.

Po wdrożeniu punktu końcowego nazwa punktu końcowego jest wyświetlana jako łącze. Kliknij łącze, aby wyświetlić informacje specyficzne dla punktu końcowego, takie jak klucz punktu końcowego, adres URL punktu końcowego i przykładowy kod.

Testowanie online punktu końcowego jest również dostępne za pośrednictwem niestandardowego portalu głosowego. Aby przetestować punkt końcowy, wybierz pozycję **Sprawdź punkt końcowy** na stronie szczegółów punktu **końcowego.** Zostanie wyświetlona strona testowania punktu końcowego. Wprowadź tekst, który ma być wypowiedziany (w formacie zwykłego tekstu lub [SSML](speech-synthesis-markup.md) w polu tekstowym. Aby usłyszeć tekst wymawiany niestandardową czcionką głosową, wybierz opcję **Odtwórz**. Ta funkcja testowania zostanie obciążona niestandardowym użyciem syntezy mowy.

Niestandardowy punkt końcowy jest funkcjonalnie identyczny ze standardowym punktem końcowym, który jest używany dla żądań zamiany tekstu na mowę. Aby uzyskać więcej informacji, zobacz [INTERFEJS API REST.](rest-text-to-speech.md)

## <a name="next-steps"></a>Następne kroki

* [Przewodnik: Nagrywanie próbek głosu](record-custom-voice-samples.md)
* [Odwołanie do interfejsu API zamiany tekstu na mowę](rest-text-to-speech.md)
* [Długi interfejs API audio](long-audio-api.md)
