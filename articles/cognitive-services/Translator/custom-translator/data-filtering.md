---
title: Filtrowanie danych — niestandardowe w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Po przesłaniu dokumentów służący do szkolenia niestandardowego systemu dokumenty przejść serię przetwarzania i filtrowanie kroków, aby przygotować się do szkolenia.
author: jann-skotdal
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 12/18/2018
ms.author: v-jansko
ms.topic: article
ms.openlocfilehash: bf5dc911fc41cac63c28e5d77434402c04332dfc
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2018
ms.locfileid: "53609320"
---
# <a name="data-filtering"></a>Filtrowanie danych 

Po przesłaniu dokumentów służący do szkolenia niestandardowego systemu dokumenty przejść serię przetwarzania i filtrowanie kroków, aby przygotować się do szkolenia. Te kroki zostały omówione w tym miejscu. Znajomość filtrowania mogą pomóc Ci zrozumieć liczba zdania wyświetlane w niestandardowych w usłudze translator, a także kroków, może potrwać do przygotowywania dokumentów do szkolenie przy użyciu niestandardowych w usłudze Translator. 

## <a name="sentence-alignment"></a>Wyrównanie zdań 
Jeśli dokument nie jest w formacie XLIFF, TMX lub WYRÓWNANIA, niestandardowe w usłudze Translator wyrównuje zdania źródłowe i docelowe dokumentów ze sobą, zdanie po zdaniu. W usłudze Translator nie wykonuje wyrównanie dokumentu — jest zgodna z usługi nazw dokumentów można znaleźć pasującego dokumentu innego języka. W tym dokumencie niestandardowe w usłudze Translator próbuje znaleźć odpowiedniego zdanie w innym języku. Używa ona dokumentu znaczników, takich jak HTML osadzonego tagi ułatwiające wykonywanie wyrównania.  

Jeśli Zobacz duża rozbieżność między liczbę zdań w źródle i docelowej po stronie dokumentów, dokument nie mogły być równoległe w pierwszej kolejności lub z innych przyczyn, które nie są dobrze alignable. Dokument współdziała z duża różnica (> 10%) zdań na każdej stronie oświadcza drugi wygląd się upewnić, że są one w rzeczywistości równoległych. Niestandardowe w usłudze Translator wyświetla ostrzeżenie obok dokumentu, jeśli liczba zdania podejrzanie różni się.  


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
Normalizuj zduplikowane znaki japońskie: Konwertuj na połowę szerokości znaków pełnej szerokości. 

 
## <a name="unescaped-xml-tags"></a>O niezmienionym znaczeniu tagi XML 
Filtrowanie tagów o niezmienionym znaczeniu przekształceń na o zmienionym znaczeniu znaczniki: 
* `&lt;` staje się `&amp;lt;` 
* `&gt;` staje się `&amp;gt;` 
* `&amp;` staje się `&amp;amp;` 

 
## <a name="invalid-characters"></a>Nieprawidłowe znaki 
Niestandardowe w usłudze Translator usuwa zdania zawierające znak Unicode U + FFFD. Znaku U + FFFD wskazuje niepowodzenie Konwersja kodowania. 

## <a name="next-steps"></a>Kolejne kroki

- [Uczenie modelu](how-to-train-model.md) w niestandardowych w usłudze Translator.
