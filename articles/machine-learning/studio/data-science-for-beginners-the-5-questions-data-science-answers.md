---
title: Analiza danych dla początkujących
titleSuffix: ML Studio (classic) - Azure
description: Data Science dla początkujących uczy podstawowych pojęć w 5 krótkich filmach, począwszy od odpowiedzi 5 questions Data Science. Z usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: af0611e121228a587e159bd4e6529c807132d16d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204447"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Wideo 1: analiza danych dla początkujących — 5 pytań, na które analiza danych daje odpowiedzi
Szybkie wprowadzenie do nauki o danych z *data science dla początkujących* w pięciu krótkich filmach od najlepszego analityka danych. Te filmy są podstawowe, ale przydatne, niezależnie od tego, czy chcesz dowiedzieć się więcej, czy pracujesz z analitykami danych.

Ten pierwszy film wideo dotyczy pytań, na które nauka o danych może odpowiedzieć. Aby w pełni wykorzystać wyniki serii, obejrzyj je wszystkie. [Przejdź do listy filmów](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Inne filmy z tej serii
*Data Science dla początkujących* to szybkie wprowadzenie do nauki o danych, które zajmują łącznie około 25 minut. Sprawdź wszystkie pięć filmów:

* Film 1: Odpowiedzi na 5 pytań do nauki o danych
* Film 2: [Czy Twoje dane są gotowe do nauki o danych?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek.)*
* Wideo 3: [Zadaj pytanie, na które możesz odpowiedzieć danymi](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 s)*
* Wideo 4: [Przewidywanie odpowiedzi za pomocą prostego modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Wideo 5: [Kopiowanie pracy innych osób w celu nauki o danych](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 s)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Transkrypcja: Odpowiedzi na 5 pytań do nauki o danych
Witaj! Witamy w serii wideo *Data Science dla początkujących*.

Data Science może być zastraszające, więc przedstawię podstawy tutaj bez równań lub żargonu programowania komputerowego.

W tym pierwszym filmie porozmawiamy o odpowiedziach na 5 pytań do nauki o danych.

Data Science używa liczb i nazw (nazywanych również kategoriami lub etykietami) do przewidywania odpowiedzi na pytania.

To może cię zaskoczyć, ale *jest tylko pięć pytań, na które odpowiedzi nauki o danych:*

* Czy to A czy B?
* Czy to dziwne?
* Ile – lub – Ile?
* Jak to jest zorganizowane?
* Co mam robić dalej?

Na każde z tych pytań odpowiada oddzielna rodzina metod uczenia maszynowego, zwana algorytmami.

Warto myśleć o algorytmie jako przepisie, a dane jako o składnikach. Algorytm informuje, jak połączyć i wymieszać dane w celu uzyskania odpowiedzi. Komputery są jak blender. Wykonują większość ciężkiej pracy algorytmu dla Ciebie i robią to dość szybko.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Pytanie 1: Czy to A czy B? wykorzystuje algorytmy klasyfikacji
Zacznijmy od pytania: Czy to A czy B?

![Algorytmy klasyfikacji: Czy to A czy B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Ta rodzina algorytmów jest nazywana klasyfikacją dwuklasową.

Jest to przydatne dla każdego pytania, które ma tylko dwie możliwe odpowiedzi.

Przykład:

* Czy ta opona zawiedzie w ciągu najbliższych 1000 mil: Tak czy nie?
* Co przynosi więcej klientów: $5 kupon lub 25% rabat?

To pytanie można również przeformułować, aby uwzględnić więcej niż dwie opcje: Czy to A lub B lub C lub D, itp.?  Nazywa się to klasyfikacją wieloklasową i jest to przydatne, gdy masz kilka - lub kilka tysięcy - możliwych odpowiedzi. Klasyfikacja wieloklasowa wybiera najbardziej prawdopodobną.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Pytanie 2: Czy to dziwne? wykorzystuje algorytmy wykrywania anomalii
Następne pytanie, na które nauka o danych może odpowiedzieć, brzmi: Czy to dziwne? Na to pytanie odpowiada rodzina algorytmów zwanych wykrywaniem anomalii.

![Algorytmy wykrywania anomalii: Czy to dziwne?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Jeśli masz kartę kredytową, korzystasz już z wykrywania anomalii. Twoja firma obsługująca kartę kredytową analizuje wzorce zakupów, dzięki czemu może ostrzegać Cię o możliwych oszustwach. Opłaty, które są "dziwne", mogą być zakupem w sklepie, w którym zwykle nie robisz zakupów lub kupujesz niezwykle drogi przedmiot.

To pytanie może być przydatne na wiele sposobów. Przykład:

* Jeśli masz samochód z manometrami, warto wiedzieć: Czy ten manometr odczytuje się normalnie?
* Jeśli monitorujesz Internet, chcesz wiedzieć: Czy ta wiadomość z Internetu jest typowa?

Wykrywanie anomalii oznacza nieoczekiwane lub nietypowe zdarzenia lub zachowania. Daje wskazówki, gdzie szukać problemów.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Pytanie 3: Ile? lub Ile? używa algorytmów regresji
Uczenie maszynowe może również przewidzieć odpowiedź na Ile? lub Ile? Rodzina algorytmów, która odpowiada na to pytanie, jest nazywana regresją.

![Algorytmy regresji: Ile? lub Ile?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Algorytmy regresji tworzą prognozy numeryczne, takie jak:

* Jaka będzie temperatura w najbliższy wtorek?  
* Jaka będzie moja sprzedaż w czwartym kwartale?

Pomagają odpowiedzieć na każde pytanie, które wymaga numeru.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Pytanie 4: Jak to jest zorganizowane? wykorzystuje algorytmy klastrowania
Teraz dwa ostatnie pytania są nieco bardziej zaawansowane.

Czasami chcesz zrozumieć strukturę zestawu danych - Jak to jest zorganizowane? Na to pytanie, nie masz przykładów, które już znasz wyniki.

Istnieje wiele sposobów, aby drażnić strukturę danych. Jednym z podejść jest klastrowanie. Oddziela dane na naturalne "grudki", aby ułatwić interpretację. W klastrowaniu nie ma jednej właściwej odpowiedzi.

![Algorytmy klastrowania: Jak to jest zorganizowane?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Typowe przykłady pytań klastrowania to:

* Którzy widzowie lubią te same typy filmów?
* Które modele drukarek zawodzą w ten sam sposób?

Dzięki zrozumieniu sposobu organizowania danych można lepiej zrozumieć i przewidzieć zachowania i zdarzenia.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Pytanie 5: Co mam teraz zrobić? wykorzystuje algorytmy uczenia się wzmacniania
Ostatnie pytanie – Co mam teraz zrobić? – wykorzystuje rodzinę algorytmów zwanych uczeniem wzmacnianym.

Uczenie się wzmacniające było inspirowane tym, jak mózgi szczurów i ludzi reagują na kary i nagrody. Algorytmy te uczą się na podstawie wyników i decydują o następnej akcji.

Zazwyczaj uczenie się wzmacniające jest dobrym rozwiązaniem dla zautomatyzowanych systemów, które muszą podejmować wiele małych decyzji bez wskazówek człowieka.

![Algorytmy uczenia wzmacniania: Co należy zrobić dalej?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Pytania, na które odpowiada, zawsze dotyczą tego, jakie działania należy podjąć - zwykle przez maszynę lub robota. Oto przykłady:

* Jeśli jestem systemem kontroli temperatury w domu: Dostosować temperaturę lub pozostawić go tam, gdzie jest?  
* Jeśli jestem samojeżdżący samochód: Na żółtym świetle, hamulec lub przyspieszenie?  
* W przypadku próżni robota: Kontynuuj odkurzanie lub wróć do stacji ładującej?

Algorytmy uczenia wzmacniającego zbierają dane w miarę ich procesu, ucząc się na podstawie prób i błędów.

Więc to wszystko - 5 pytań data science może odpowiedzieć.

## <a name="next-steps"></a>Następne kroki
* [Wypróbuj pierwszy eksperyment nauki o danych z Machine Learning Studio (klasyczny)](create-experiment.md)
* [Wprowadzenie do uczenia maszynowego na platformie Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
