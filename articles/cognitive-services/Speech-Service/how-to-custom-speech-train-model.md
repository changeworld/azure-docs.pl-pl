---
title: Trenuj model dla niestandardowej mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Szkolenie modelu zamiany mowy na tekst może zwiększyć dokładność rozpoznawania dla modelu bazowego firmy Microsoft lub modelu niestandardowego. Model jest szkolony przy użyciu transkrypcji oznaczonych przez człowieka i powiązanych tekstów.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: a2bc39a35299f56ba52a0143ce123560bd4d88fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77137767"
---
# <a name="train-a-model-for-custom-speech"></a>Trenuj model mowy niestandardowej

Szkolenie modelu zamiany mowy na tekst może zwiększyć dokładność rozpoznawania dla modelu bazowego firmy Microsoft. Model jest szkolony przy użyciu transkrypcji oznaczonych przez człowieka i powiązanych tekstów. Te zestawy danych wraz z wcześniej przesłane dane audio, są używane do uściślania i szkolenia modelu mowy do tekstu do rozpoznawania słów, fraz, akronimów, nazw i innych terminów specyficznych dla produktu. Im więcej zestawów danych w domenie podasz (dane związane z tym, co użytkownicy powiedzą i czego oczekujesz rozpoznać), tym dokładniejszy będzie model, co skutkuje lepszym rozpoznawaniem. Należy pamiętać, że poprzez podawanie niepowiązanych danych do szkolenia, można zmniejszyć lub zranić dokładność modelu.

## <a name="use-training-to-resolve-accuracy-issues"></a>Rozwiązywanie problemów z dokładnością do obsługi obsługi szkoleniowej

Jeśli napotkasz problemy z rozpoznawaniem modelu, użycie transkrypcji oznaczonych etykietą ludzką i powiązanych danych do dodatkowego szkolenia może pomóc poprawić dokładność. Ta tabela służy do określania, który zestaw danych ma być używany do rozwiązywania problemów:

| Przypadek użycia | Typ danych |
| -------- | --------- |
| Popraw dokładność rozpoznawania słownictwa i gramatyki specyficznej dla branży, takiej jak terminologia medyczna lub żargon IT. | Tekst pokrewny (zdania/wypowiedzi) |
| Zdefiniuj fonetyczną i wyświetlaną formę wyrazu lub terminu o niestandardowej wymowie, taką jak nazwy produktów lub akronimy. | Tekst pokrewny (wymowa) |
| Zwiększ dokładność rozpoznawania stylów mówienia, akcentów lub określonych odgłosów tła. | Transkrypcje audio + oznaczone przez człowieka |

> [!IMPORTANT]
> Jeśli nie przesłałeś zestawu danych, zobacz [Przygotowywanie i testowanie danych](how-to-custom-speech-test-data.md). Ten dokument zawiera instrukcje przekazywania danych i wskazówki dotyczące tworzenia zestawów danych wysokiej jakości.

## <a name="train-and-evaluate-a-model"></a>Trenowanie i ocenianie modelu

Pierwszym krokiem do szkolenia modelu jest przekazywanie danych szkoleniowych. Użyj [przygotować i przetestować dane](how-to-custom-speech-test-data.md) dla instrukcji krok po kroku, aby przygotować transkrypcje oznaczone przez człowieka i powiązanego tekstu (wypowiedzi i wymowy). Po przesłaniu danych szkoleniowych postępuj zgodnie z poniższymi instrukcjami, aby rozpocząć szkolenie modelu:

1. Zaloguj się do [portalu mowy niestandardowej](https://speech.microsoft.com/customspeech).
2. Przejdź do **sekcji Ćwiczenie > mowy > niestandardowej mowy >**.
3. Kliknij **pozycję Model pociągu**.
4. Następnie nadaj treningowi **nazwę** i **opis**.
5. Z menu rozwijanego **Scenariusz i model linii bazowej** wybierz scenariusz, który najlepiej pasuje do Twojej domeny. Jeśli nie masz pewności, który scenariusz wybrać, wybierz **opcję Ogólne**. Model linii bazowej jest punktem wyjścia do szkolenia. Jeśli nie masz preferencji, możesz użyć najnowszych.
6. Na stronie **Wybierz dane szkoleniowe** wybierz jeden lub wiele zestawów danych transkrypcyjnych oznaczonych etykietą człowieka, których chcesz użyć do szkolenia.
7. Po zakończeniu szkolenia można wykonać testowanie dokładności na nowo przeszkolonym modelu. Ten krok jest opcjonalny.
8. Wybierz **pozycję Utwórz,** aby utworzyć model niestandardowy.

W tabeli Szkolenia jest wyświetlany nowy wpis odpowiadający temu nowo utworzonemu modelowi. W tabeli wyświetlany jest również stan: Przetwarzanie, Powodzenie, Niepowodzenie.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Ocena dokładności wyszkolonego modelu

Można sprawdzić dane i ocenić dokładność modelu za pomocą następujących dokumentów:

- [Sprawdź swoje dane](how-to-custom-speech-inspect-data.md)
- [Oceń swoje dane](how-to-custom-speech-evaluate-data.md)

Jeśli zdecydujesz się przetestować dokładność, ważne jest, aby wybrać zestaw danych akustycznych, który różni się od używanego w modelu, aby uzyskać realistyczne poczucie wydajności modelu.

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Przygotowanie i przetestowanie danych](how-to-custom-speech-test-data.md)
- [Sprawdź swoje dane](how-to-custom-speech-inspect-data.md)
- [Oceń swoje dane](how-to-custom-speech-evaluate-data.md)
- [Trenowanie modelu](how-to-custom-speech-train-model.md)
