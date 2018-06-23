---
title: Dostosowywanie mowy do modeli tekstu | Dokumentacja firmy Microsoft
description: Dostosowywanie mowy do modeli tekstu, aby zwiększyć rozpoznawania mowy.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 380c585f57737c0aa4ec99303c52d4567500b5f4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348720"
---
# <a name="customize-speech-to-text-models-using-speech-service"></a>Dostosowywanie modeli "Mowy na tekst" przy użyciu usługi mowy

Aby uzyskać lepsze wyniki rozpoznawania mowy usługi mowy umożliwia dostosowanie trzy modele używane przez **mowy na tekst** interfejsu API. Modele są uczone automatycznie z przykładowych danych, podane przez użytkownika.

| Modelowanie | Dane przykładowe | Przeznaczenie |
|-------|---------------|---------|
| Modelu akustycznego      | Mowa, tekst | Dostosowywanie dźwięków (fonemów) skojarzony z określonym słów. Szkolenie nowy motyw lub dialekt mówiąc środowiska itp. |
| Model języka      | Tekst | Dostosowywanie słowa, które są znane do usługi i jak są używane w zniesławiających. Dodaj terminów, nazwy lokalnej itp. |
| Wymowy | Tekst | Rozpoznanie powodującymi wyrazy, związków i skrótów. Na przykład można rozpoznać jako "C3PO" mapy "Zobacz threepio" |

Po utworzeniu nowych modeli, można utworzyć niestandardowe punktu końcowego, który używa modelu dla co najmniej jednego z powyższych celów. Można również wybrać model podstawowy, pod warunkiem przez usługę mowy Jeśli chcesz użyć, na przykład niestandardowe modelu akustycznego i nie modelu standard języka. Następnie należy użyć niestandardowego punktu końcowego zamiast standardowego punktu końcowego dla żądań REST. Każdy punkt końcowy ma skojarzoną *identyfikator wdrożenia* tak, aby można go przy użyciu zestawu SDK mowy.

Dostosowywanie wszystkich modeli odbywa się za pośrednictwem [mowy niestandardowe portal](https://www.cris.ai/).

## <a name="language-support"></a>Obsługa języków

Następujące języki są obsługiwane w przypadku niestandardowych **mowy na tekst** modeli języka.

| Kod | Język |
|-|-|
| pl-PL | Angielski (Stany Zjednoczone) 
| zh-CN | Chiński 
| SP SP | Hiszpański (Hiszpania) 
| fr-FR | Francuski (Francja) 
| IT-IT | Włoski 
| de-DE | Niemiecki
| pt-BR | Portugalski (Brazylia)
| ru-RU | Rosyjski
| JP JP | Japoński
| ar np | Arabski (Egipt)

Niestandardowe akustycznego modele obsługują tylko US English (en US). Jednak chińskiej wersji językowej akustycznego zestawy danych mogą być importowane do testowania modeli języka chińskiego.

Niestandardowe wymowy obsługuje tylko US English (en US) i niemiecki (de-DE) w tej chwili.

## <a name="prepare-data-sets"></a>Przygotowanie zestawów danych

Każdy typ modelu wymaga nieco inne dane i formatowanie, zgodnie z opisem w tym miejscu.

| Modelowanie | Co musisz zapewnić      |
|-------|-----------------------|
| Akustycznego | Plik ZIP zawierający plików dźwiękowych z pełną zniesławiających, a plik tekstowy zawierający transcriptions tych plików. Każdy wiersz pliku musi zawierać nazwę pliku, karta (ASCII 9) i tekst.|
| Język | Plik tekstowy zawierający utterance jeden na wiersz. |
| Wymowy | Plik tekstowy zawierający wskazówkę wymowy w każdym wierszu. Każda wskazówka jest formularza wyświetlania (word lub skrót), a następnie na karcie (ASCII 9) i mówionej formy (Wymowa żądaną).  |

Pliki tekstowe należy stosować [wytyczne przekształcania tekstu](prepare-transcription.md) dla języka modelu.

## <a name="prepare-audio-files"></a>Przygotowanie plików audio

Pliki dźwiękowe dla modeli akustycznego powinny być rejestrowane w lokalizacji reprezentatywny przez różnych użytkowników reprezentatywny (chyba że jest zoptymalizować rozpoznawania dla jednej osoby mówiącej), za pomocą podobnie jak użytkownicy mają mikrofonu. Wymagany format wszystkie próbki audio została opisana w tej tabeli.

| Właściwość | Wymagana wartość |
|----------|------|
Format pliku | RIFF (WAV)
Częstotliwość próbkowania | 8000 Hz lub 16 000 Hz
Kanały | 1 (mono)
Przykładowy format | PCM 16-bitową liczbę całkowitą
Czas trwania pliku | Między 0,1 a 60 sekund
Pierścień wyciszenia | 0,1 sekundę
Format archiwum | ZIP
Rozmiar maksymalny archiwum | 2 GB

Jeśli są uczenie modelu do pracy w zakłócenia tła, takich jak w fabryce lub samochodu, należy uwzględnić kilka sekund szum typowy tła na początku lub na końcu niektóre przykłady. Nie dołączaj tylko szumu próbek.

## <a name="upload-data-sets"></a>Przekaż zestawów danych

Aby utworzyć niestandardowy model, najpierw przekazać dane, a następnie rozpocząć szkolenie.

1.  Zaloguj się do [mowy niestandardowe portal](https://www.cris.ai/).

1.  Wybierz typ zestawu danych, które ma zostać utworzona z **mowy niestandardowe** menu - dostosowania danych dla modeli akustycznego, język danych modeli języka lub wymowy. Zostanie wyświetlona lista istniejących zestawów danych tego typu (jeśli istnieje).

1. Wybierz język, klikając **zmiany ustawień regionalnych**.

1.  Kliknij przycisk **importowania nowego** i określ nazwę i opis nowego zestawu danych.

1. Wybierz pliki danych, które zostały przygotowane.

1. Kliknij przycisk **importu** Aby przekazać dane i rozpocząć sprawdzania poprawności. Sprawdzanie poprawności zapewnia, że wszystkie pliki są w poprawnym formacie. Walidacja może potrwać kilka minut.

## <a name="create-a-model"></a>Tworzenie modelu

 Po zweryfikowaniu zestaw danych można nauczenia modelu w następujący sposób.

> [!NOTE]
> Dane wymowy nie muszą być szkoleni.

1. Wybierz typ modelu jest tworzony z **mowy niestandardowe** menu, następnie kliknij przycisk **Utwórz nowy.**

1. Wybierz ustawienia regionalne dla modelu.

1. Wybierz model podstawowy dla nowego modelu. Wybór model podstawowy określa tryby rozpoznawania, dla których modelu mogą być używane, a także służy jako rezerwowe dla dowolnych danych nie znajduje się w zestawie danych.

1.  Wybierz zestaw danych, z którego ma być tworzony model. Zestaw danych mogą być używane przez dowolną liczbę modeli.

1. Kliknij przycisk **Utwórz** aby rozpocząć szkolenie nowego modelu.

## <a name="test-a-model"></a>Testowanie modelu

W ramach procesu tworzenia modelu należy przetestować modelu akustycznego zestawem danych. Nowy model jest używany do rozpoznawania mowy w zestawie danych plików audio i wyniki testowana odpowiedni tekst. Aby uzyskać najlepsze wyniki Użyj innego zbioru danych akustycznych niż ten, który został użyty do utworzenia modelu.

## <a name="custom-endpoint"></a>Niestandardowy punkt końcowy

Po utworzeniu niestandardowych modeli akustycznego lub języka modeli, można je wdrożyć do niestandardowego **mowy na tekst** punktu końcowego. Tylko konto, które utworzono punkt końcowy jest dozwolony w celu wykonywania wywołań do niego.

Aby utworzyć punkt końcowy, wybierz **wdrożeń** z **mowy niestandardowe** menu w górnej części strony. **Wdrożeń** strona zawiera tabelę bieżącego niestandardowe punkty końcowe, jeśli utworzono żadnego. Kliknij przycisk **Utwórz nowy** Aby utworzyć nowy punkt końcowy.

Wybierz modele mają być używane w **modelu akustycznego** i **Model języka** listy. Dostępne opcje zawsze należy uwzględniać modeli bazowych firmy Microsoft. Nie można mieszać konwersacji modeli wyszukiwania i modeli jest wymagane przez, więc wybranie modelu akustycznego ograniczenie modeli dostępny język i na odwrót. Modeli można używać w dowolnej liczby punktów końcowych.

Kliknij przycisk **Utwórz** po wybraniu modeli. Nowego punktu końcowego może potrwać do 30 minut, aby udostępnić.

Gdy punkt końcowy jest gotowe, wybierz go w **wdrożeń** tabeli, aby wyświetlić identyfikator URI i wdrożenia. Można użyć niestandardowe punkty końcowe z [interfejsu API Rest](rest-apis.md#speech-to-text) i [mowy SDK](speech-sdk.md). [Przykłady kodu](samples.md) obejmują przykładem przy użyciu niestandardowych mowy do punktu końcowego tekstu.

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz wersję próbną subskrypcji mowy](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznawanie mowy w języku C#](quickstart-csharp-windows.md)
