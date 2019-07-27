---
title: Uczenie modelu dla usługi Custom Speech-Speech
titleSuffix: Azure Cognitive Services
description: Szkolenie zamiany mowy na tekst jest niezbędne w celu zwiększenia dokładności rozpoznawania dla modelu linii bazowej firmy Microsoft lub modelu niestandardowego, który ma zostać utworzony. Model jest szkolony z zastosowaniem transkrypcji i powiązanego tekstu. Te zestawy danych wraz z wcześniej przekazanymi danymi audio są używane do udoskonalania i uczenia modelu zamiany mowy na tekst w celu rozpoznawania wyrazów, fraz, akronimów, nazw i innych warunków specyficznych dla produktu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b5893b4f07444b07bf142971a5df4776e549d307
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562814"
---
# <a name="train-a-model-for-custom-speech"></a>Uczenie modelu dla Custom Speech

Szkolenie zamiany mowy na tekst jest niezbędne w celu zwiększenia dokładności rozpoznawania dla modelu linii bazowej firmy Microsoft lub modelu niestandardowego, który ma zostać utworzony. Model jest szkolony z zastosowaniem transkrypcji i powiązanego tekstu. Te zestawy danych wraz z wcześniej przekazanymi danymi audio są używane do udoskonalania i uczenia modelu zamiany mowy na tekst w celu rozpoznawania wyrazów, fraz, akronimów, nazw i innych warunków specyficznych dla produktu. Im bardziej szczegółowe zestawy danych w domenie, które zapewniasz (dane związane z tym, co użytkownicy będą wiedzieć i jakie są oczekiwane do rozpoznania), to dokładniejszy model, który będzie wynikiem ulepszonego rozpoznawania. Należy pamiętać, że przez podawanie niepowiązanych danych do szkolenia, można zmniejszyć lub obniżyć dokładność modelu.

## <a name="use-training-to-resolve-accuracy-issues"></a>Rozwiązywanie problemów z dokładnością przy użyciu szkoleń

Jeśli napotykasz problemy z rozpoznawaniem z modelem, używanie transkrypcji z etykietami i powiązane dane dla dodatkowego szkolenia może pomóc poprawić dokładność. Użyj tej tabeli, aby określić zestaw danych, który ma być używany do rozwiązywania problemów:

| Przypadek użycia | Typ danych |
|----------|-----------|
| Poprawianie dokładności rozpoznawania w przypadku słownictwa i gramatyki właściwych dla branży, takich jak Terminologia medyczna lub żargon IT | Pokrewny tekst (zdania/wyrażenia długości) |
| Zdefiniuj tekst fonetyczny i wyświetloną wyrazu lub terminu, który ma niestandardową wymowę, taką jak nazwy produktów lub akronimy. | Pokrewny tekst (wymowa) |
| Poprawianie dokładności rozpoznawania w przypadku stylów, akcentów lub określonych szumów tła | Zapisy audio + oznakowane przez człowieka |
> [!IMPORTANT]
> Jeśli zestaw danych nie został przekazany, zapoznaj się [z tematem przygotowywanie i testowanie danych](how-to-custom-speech-test-data.md). Ten dokument zawiera instrukcje dotyczące przekazywania danych oraz wskazówki dotyczące tworzenia zestawów danych o wysokiej jakości.

## <a name="train-and-evaluate-a-model"></a>Uczenie i testowanie modelu

Pierwszym krokiem do uczenia modelu jest przekazanie danych szkoleniowych. Użyj opcji [Przygotuj i przetestuj dane](how-to-custom-speech-test-data.md) , aby uzyskać instrukcje krok po kroku w celu przygotowania oznakowania i powiązanego tekstu (wyrażenia długości i wymowy). Po przesłaniu danych szkoleniowych postępuj zgodnie z poniższymi instrukcjami, aby rozpocząć uczenie modelu:

1. Przejdź do **> mowy Custom Speech > szkolenia**.
2. Kliknij pozycję **uczenie modelu**.
3. Następnie Nadaj swojemu szkoleniowi **nazwę** i **Opis**.
4. Z menu rozwijanego **model scenariusza i linia bazowa** Wybierz scenariusz, który najlepiej pasuje do domeny. Jeśli nie masz pewności, który scenariusz wybrać, wybierz pozycję **Ogólne**. Model linii bazowej jest punktem początkowym szkolenia. Jeśli nie masz preferencji, możesz użyć najnowszej.
5. Na stronie **Wybierz dane szkoleniowe** wybierz jeden lub wiele zestawów danych, które mają być używane do szkoleń.
6. Po zakończeniu szkolenia możesz wybrać przeprowadzenie testowania dokładności dla nowo nauczonego modelu. Ten krok jest opcjonalny.
7. Wybierz pozycję **Utwórz** , aby skompilować niestandardowy model.

W tabeli szkoleń zostanie wyświetlony nowy wpis, który odnosi się do nowo utworzonego modelu. W tabeli jest również wyświetlany stan:  Przetwarzanie zakończone powodzeniem, zakończone niepowodzeniem.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Oceń dokładność przeszkolonego modelu

Możesz sprawdzić dane i oszacować dokładność modeli przy użyciu tych dokumentów:

* [Inspekcja danych](how-to-custom-speech-inspect-data.md)
* [Oceń dane](how-to-custom-speech-evaluate-data.md)


Jeśli zdecydowano się na przetestowanie dokładności, ważne jest, aby wybrać akustyczny zestaw danych, który różni się od tego, który był używany z modelem w celu uzyskania realistycznego sensu wydajności modelu.

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Przygotowywanie i testowanie danych](how-to-custom-speech-test-data.md)
* [Inspekcja danych](how-to-custom-speech-inspect-data.md)
* [Oceń dane](how-to-custom-speech-evaluate-data.md)
* [Uczenie modelu](how-to-custom-speech-train-model.md)
