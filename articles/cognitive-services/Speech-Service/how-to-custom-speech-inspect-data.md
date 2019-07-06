---
title: Zbadaj dobrej jakości danych dla niestandardowych głosu — usługi mowy
titlesuffix: Azure Cognitive Services
description: Niestandardowa zamiana mowy udostępnia narzędzia, które pozwalają wizualnie badać jakości rozpoznawania modelu, porównując dane audio z odpowiedni wynik rozpoznawania. Z poziomu portalu Custom Speech można odtwarzać dźwięk przekazane i określić, czy wynik rozpoznawania podana jest poprawna.  To narzędzie umożliwia szybkie sprawdzanie jakości modelu mowy na tekst linii bazowej firmy Microsoft lub uczonego modelu niestandardowego bez konieczności transkrypcja jakiekolwiek dane audio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 900d9b032b4ed121589d904a8ad18059b3283661
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603127"
---
# <a name="inspect-custom-speech-data"></a>Sprawdzanie danych Custom Speech

> [!NOTE]
> Na tej stronie zakłada użytkownik przeczytał [przygotowywania danych testowych dla Custom Speech](how-to-custom-speech-test-data.md) i przesłaniu zestawu danych w celu przeprowadzenia inspekcji.

Niestandardowa zamiana mowy udostępnia narzędzia, które pozwalają wizualnie badać jakości rozpoznawania modelu, porównując dane audio z odpowiedni wynik rozpoznawania. Z poziomu portalu Custom Speech można odtwarzać dźwięk przekazane i określić, czy wynik rozpoznawania podana jest poprawna. To narzędzie umożliwia szybkie sprawdzanie jakości modelu mowy na tekst linii bazowej firmy Microsoft lub uczonego modelu niestandardowego bez konieczności transkrypcja jakiekolwiek dane audio.

W tym dokumencie dowiesz się, jak przeprowadzać inspekcję wizualnie jakość modelu z użyciem danych szkoleniowych, który został wcześniej przekazany.

Na tej stronie dowiesz się, jak przeprowadzać inspekcję wizualnie jakość modelu mowy na tekst linii bazowej firmy Microsoft i/lub który przeprowadzono już uczenie modelu niestandardowego. Użyjemy danych przekazanych do **danych** kartę do testowania.

## <a name="create-a-test"></a>Tworzenie testu

Wykonaj te instrukcje, aby utworzyć test:

1. Przejdź do **mowy na tekst > Custom Speech > testowania**.
2. Kliknij przycisk **Dodaj Test**.
3. Wybierz **kontroli jakości (dane tylko dźwięk)** . Nadaj nazwę, opis, test i wybierz zestaw danych audio.
4. Wybierz maksymalnie dwa modele, które chcesz przetestować.
5. Kliknij pozycję **Utwórz**.

Po pomyślnym utworzeniu testu można porównać modeli obok siebie.

## <a name="side-by-side-model-comparisons"></a>Porównania Side-by-side modelu

Gdy stan testu jest *Powodzenie*, kliknij nazwę elementu testu, aby wyświetlić szczegóły testu. Ta strona szczegółów zawiera listę wszystkich wypowiedzi w zestawie danych, informujący o wyniki rozpoznawania dwa modele obok transkrypcji z przesłano zestawu danych.

Aby sprawdzić porównania side-by-side, można przełączać się różne typy błędów, tym wstawiania, usuwania i podstawienia. Nasłuchiwanie audio i porównanie wyników rozpoznawania w każdej kolumnie (wyświetlanie oznaczone przez człowieka transkrypcji oraz wyniki z dwóch modeli mowy na tekst), można zdecydować, gdzie wymaganą ulepszenia i model, który odpowiada Twoim potrzebom.

Procedury kontroli jakości testowania przydaje się do sprawdzania, czy jakość punkt końcowy rozpoznawania mowy jest wystarczający dla aplikacji.  Dla celu miary dokładności, wymagających uzyskanego audio, postępuj zgodnie z instrukcjami w [oceny dokładności](how-to-custom-speech-evaluate-data.md).

## <a name="next-steps"></a>Kolejne kroki

* [Oceń swoje dane](how-to-custom-speech-evaluate-data.md)
* [Uczenie modelu](how-to-custom-speech-train-model.md)
* [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Przygotowanie danych testowych do Custom Speech](how-to-custom-speech-test-data.md)
