---
title: Filtrowanie danych — niestandardowe w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Po przesłaniu dokumentów służący do szkolenia niestandardowego systemu dokumenty przejść serię przetwarzania i filtrowanie kroków, aby przygotować się do szkolenia.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: ba6d1ed5bc26f121f32fad548e7a17c1f4998851
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389355"
---
# <a name="data-filtering"></a>Filtrowanie danych

Po przesłaniu dokumentów służący do szkolenia niestandardowego systemu dokumenty przejść serię przetwarzania i filtrowanie kroków, aby przygotować się do szkolenia. Te kroki zostały omówione w tym miejscu. Znajomość filtrowania mogą pomóc Ci zrozumieć liczba zdania wyświetlane w niestandardowych w usłudze translator, a także kroków, może potrwać do przygotowywania dokumentów do szkolenie przy użyciu niestandardowych w usłudze Translator.

## <a name="sentence-alignment"></a>Wyrównanie zdań
Jeśli dokument nie jest w formacie XLIFF, TMX lub WYRÓWNANIA, niestandardowe w usłudze Translator wyrównuje zdania źródłowe i docelowe dokumentów ze sobą, zdanie po zdaniu. W usłudze Translator nie wykonuje wyrównanie dokumentu — jest zgodna z usługi nazw dokumentów można znaleźć pasującego dokumentu innego języka. W tym dokumencie niestandardowe w usłudze Translator próbuje znaleźć odpowiedniego zdanie w innym języku. Używa ona dokumentu znaczników, takich jak HTML osadzonego tagi ułatwiające wykonywanie wyrównania.  

Zobacz w przypadku dużych różnic między liczbę zdań w źródle i docelowej po stronie dokumentów, dokument nie mogły być równoległe w pierwszej kolejności, czy innego powodu nie można wyrównać. Dokument współdziała z duża różnica (> 10%) zdań na każdej stronie oświadcza drugi wygląd, aby upewnić się, że są one w rzeczywistości równoległych. Niestandardowe w usłudze Translator wyświetla ostrzeżenie obok dokumentu, jeśli liczba zdania podejrzanie różni się.  


## <a name="deduplication"></a>Deduplikacja
Niestandardowe w usłudze Translator usuwa zdań, które znajdują się w testów i dostrajanie dokumentów z danych szkoleniowych. Usunięcie odbywa się dynamicznie w szkolenia, uruchomić, nie w kroku przetwarzania danych. Niestandardowe w usłudze Translator liczności zdanie do Ciebie w ramach przeglądu projektu przed takie usunięcie.  

## <a name="length-filter"></a>Długość filtru
* Usuń zdania z tylko jednego słowa po obu stronach.
* Usuń zdania z więcej niż 100 słowa po obu stronach.  Chiński, japoński, koreański, są wykluczone.
* Usuń zdania z mniej niż 3 znaki. Chiński, japoński, koreański, są wykluczone.
* Usunąć zdania z więcej niż 2000 znaków chińskich, japoński, koreański.
* Usuń zdania z mniej niż 1% znaków alfanumerycznych.
* Usuń wpisy słownika zawierającego więcej niż 50 wyrazów.

## <a name="white-space"></a>Biały znak
* Zastąp dowolnej sekwencji znaków odstępu, w tym karty i sekwencje CR/LF pojedynczy znak.
* Usuń wiodących ani końcowych spacji w zdaniu

## <a name="sentence-end-punctuation"></a>Interpunkcja zdania
Zastąp wielu znaków interpunkcyjnych koniec zdania przy użyciu jednego wystąpienia.  

## <a name="japanese-character-normalization"></a>Normalizacja japońskich znaków
Znaki o połowę szerokości, należy przekonwertować całą szerokość liter i cyfr.

## <a name="unescaped-xml-tags"></a>O niezmienionym znaczeniu tagi XML
Filtrowanie tagów o niezmienionym znaczeniu przekształceń na o zmienionym znaczeniu znaczniki:
* `&lt;` staje się `&amp;lt;`
* `&gt;` staje się `&amp;gt;`
* `&amp;` staje się `&amp;amp;`

## <a name="invalid-characters"></a>Nieprawidłowe znaki
Niestandardowe w usłudze Translator usuwa zdania zawierające znak Unicode U + FFFD. Znaku U + FFFD wskazuje niepowodzenie Konwersja kodowania.

## <a name="next-steps"></a>Kolejne kroki

- [Uczenie modelu](how-to-train-model.md) w niestandardowych w usłudze Translator.
