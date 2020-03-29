---
title: Sprawdzanie jakości danych pod kątem niestandardowej mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Mowa niestandardowa udostępnia narzędzia, które umożliwiają wizualną kontrolę jakości rozpoznawania modelu przez porównanie danych audio z odpowiednim wynikiem rozpoznawania. Możesz odtworzyć przesłany dźwięk i określić, czy podany wynik rozpoznawania jest poprawny.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: 60b415f69800885f37ee53cc4f090098f5ebf1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806083"
---
# <a name="inspect-custom-speech-data"></a>Sprawdzanie danych mowy niestandardowej

> [!NOTE]
> Na tej stronie założono, że przeczytałeś ["Przygotuj dane testowe dla mowy niestandardowej"](how-to-custom-speech-test-data.md) i prześlesz zestaw danych do inspekcji.

Mowa niestandardowa udostępnia narzędzia, które umożliwiają wizualną kontrolę jakości rozpoznawania modelu przez porównanie danych audio z odpowiednim wynikiem rozpoznawania. Z [portalu Mowy niestandardowej](https://speech.microsoft.com/customspeech)można odtwarzać przesłany dźwięk i określać, czy podany wynik rozpoznawania jest poprawny. To narzędzie umożliwia szybkie sprawdzanie jakości bazowego modelu mowy do tekstu firmy Microsoft lub wyszkolonego modelu niestandardowego bez konieczności transkrypcji jakichkolwiek danych audio.

W tym dokumencie dowiesz się, jak wizualnie sprawdzić jakość modelu przy użyciu wcześniej przesłanych danych szkoleniowych.

Na tej stronie dowiesz się, jak wizualnie sprawdzić jakość bazowego modelu mowy do tekstu firmy Microsoft i/lub modelu niestandardowego, który został przeszkolony. Użyjesz danych przekazanych na karcie **Dane** do testowania.

## <a name="create-a-test"></a>Tworzenie testu

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć test:

1. Zaloguj się do [portalu mowy niestandardowej](https://speech.microsoft.com/customspeech).
2. Przejdź do **> testowania mowy niestandardowej >** mowy mowy mowy .
3. Kliknij **pozycję Dodaj test**.
4. Wybierz **pozycję Sprawdź jakość (dane tylko audio).** Nadaj testowi nazwę, opis i wybierz zestaw danych audio.
5. Wybierz maksymalnie dwa modele, które chcesz przetestować.
6. Kliknij przycisk **Utwórz**.

Po pomyślnym utworzeniu testu można porównać modele obok siebie.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Porównania modeli side-by-side

Gdy stan testu zakończy _się pomyślnie,_ kliknij nazwę elementu testowego, aby wyświetlić szczegóły testu. Ta strona szczegółów zawiera listę wszystkich wypowiedzi w zestawie danych, wskazując wyniki rozpoznawania dwóch modeli wraz z transkrypcji z przesłanego zestawu danych.

Aby sprawdzić porównanie side-by-side, można przełączać różne typy błędów, w tym wstawianie, usuwanie i podstawianie. Słuchając dźwięku i porównując wyniki rozpoznawania w każdej kolumnie (pokazując transkrypcję z etykietą człowieka i wyniki dwóch modeli zamiany mowy na tekst), można zdecydować, który model spełnia Twoje potrzeby i gdzie potrzebne są ulepszenia.

Sprawdzanie jakości testowania jest przydatne do sprawdzania poprawności, czy jakość punktu końcowego rozpoznawania mowy jest wystarczająca dla aplikacji. Aby uzyskać obiektywną miarę dokładności, wymagającą transkrypcji dźwięku, postępuj zgodnie z instrukcjami zawartymi w [polu Ocena dokładności](how-to-custom-speech-evaluate-data.md).

## <a name="next-steps"></a>Następne kroki

- [Oceń swoje dane](how-to-custom-speech-evaluate-data.md)
- [Trenowanie modelu](how-to-custom-speech-train-model.md)
- [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Przygotowywanie danych testowych dla mowy niestandardowej](how-to-custom-speech-test-data.md)
