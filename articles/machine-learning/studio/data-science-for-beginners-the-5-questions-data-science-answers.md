---
title: Analiza danych 5 pytań — analiza danych dla początkujących — Azure Machine Learning Studio | Dokumentacja firmy Microsoft
description: Do nauki o danych dla początkujących jest omawia podstawowe pojęcia związane z 5 krótkie filmy wideo, począwszy od 5 pytań analiza danych daje odpowiedzi. Z usługi Azure Machine Learning.
keywords: wykonując analizy danych, początkujących do nauki o danych, do nauki o danych dla początkujących podstawy do nauki o danych, pytania do nauki o danych, wideo do nauki o danych, wprowadzenie do nauki o danych
services: machine-learning
documentationcenter: na
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cjgronlund
ms.assetid: a01f93ee-01eb-4afe-abbd-cfa035c119b0
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.openlocfilehash: c59b4783c59c44604738a3056b3484e11ff90363
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314170"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Wideo 1: analiza danych dla początkujących — 5 pytań, na które analiza danych daje odpowiedzi
Skorzystaj z krótkiego wprowadzenia do nauki o danych z *przetwarzanie danych dla początkujących* w pięciu krótkich filmach wideo z najważniejszych danych. Te wideo stanowią podstawowy, ale przydatne, czy interesuje Cię nauki o danych, czy pracujesz z analityków danych.

Jest to pierwszy film wideo o rodzajach pytań, spełniające do nauki o danych. Aby maksymalnie wykorzystać możliwości serii, obejrzyj je wszystkie. [Przejdź do listy filmów wideo](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Inne filmy wideo w tej serii
*Przetwarzanie danych dla początkujących* jest szybkie wprowadzenie do danych do analizy trwa około 25 minut łączna liczba. Zapoznaj się z pięciu wszystkie filmy wideo dotyczące:

* Wideo 1: 5 pytań, analiza danych daje odpowiedzi
* Wideo 2: [czy Twoje dane są gotowe na analizę danych?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek.)*
* Wideo 3: [Zadaj pytanie, można odpowiedzieć za pomocą danych](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sek.)*
* Wideo 4: [Prognozowanie odpowiedzi za pomocą prostego modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sek.)*
* Wideo 5: [kopiowanie pracy innych osób w celu przeprowadzenia analizy danych](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek.)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Zapis: 5 pytań, analiza danych daje odpowiedzi
Cześć! Witamy w serii filmów wideo *przetwarzanie danych dla początkujących*.

Do nauki o danych mogą być OPORY przed, czy zostaną wprowadzone w podstawy, w tym miejscu bez równania lub komputerze programowania żargonu.

W tym filmie pierwszy omówimy "5 pytań, analiza danych daje odpowiedzi".

Do nauki o danych używane są numery i nazwy (znany także jako kategorii lub etykiety) w celu przewidywania odpowiedzi na pytania.

Zaskakujące, ale *istnieją tylko pięć pytań na to, że analiza danych daje odpowiedzi*:

* Jest to A lub B?
* Jest to nieco dziwne?
* Ile — lub — ile?
* To organizację?
* Co należy zrobić dalej?

Każdej z nich te pytania jest odbierane przez oddzielne rodziny machine learning metod, co nazywanych algorytmami.

Warto wziąć pod uwagę algorytm jako młyna i dane jako składników. Algorytm informuje, jak łączyć i łączyć dane w celu uzyskania odpowiedzi. Komputery są podobne blender. Jak najbardziej trudną pracę algorytmu dla Ciebie i jak ona dość szybko.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Pytanie 1: Jest to A lub B? używa algorytmów klasyfikacji
Zacznijmy od kwestii: jest to A lub B?

![Algorytmy klasyfikacji: jest to A lub B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Tej rodziny algorytmy nosi nazwę klasyfikacji dwóch klas.

Jest to przydatne w przypadku każde pytanie, który ma tylko dwa możliwe odpowiedzi.

Na przykład:

* Ta opona nie powiedzie się w następnym milach 1000: Yes lub no?
* Który udostępnia w większej liczby klientów: 5 USD kuponu lub rabat w wysokości 25%?

To pytanie może również być rephrased do uwzględnienia więcej niż dwie opcje: jest to A lub B lub C lub D, itp.?  Jest to nazywane wieloklasowej klasyfikacji i jego przydatne w przypadku kilku — lub kilka tysięcy — możliwe odpowiedzi. Klasyfikacji wieloklasowej wybiera ten, który najprawdopodobniej.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Pytanie 2: Jest to nieco dziwne? używa algorytmy wykrywania anomalii
Następne pytanie do nauki o danych pozwala uzyskać odpowiedzi na to: jest to brzmienia? Zostanie udzielona przez rodzinę algorytmów o nazwie wykrywania anomalii.

![Algorytmy wykrywania anomalii: jest to brzmienia?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

W przypadku karty kredytowej został już korzystali z wykrywania anomalii. Karty kredytowej analizuje Twoich wzorców zakupu, tak aby ich można informujące o możliwym oszustwie. Opłaty, które są "nieco dziwne" może być nabyć pod adresem magazynu, w którym zwykle nie zakupów lub kupić niezwykle kosztownych elementu.

To pytanie może być przydatne na wiele różnych sposobów. Na wystąpienie:

* Jeśli masz samochodu z ciśnieniem mierników, warto wiedzieć: jest tego miernika wykorzystanie odczytywanie normalne?
* Jeśli monitorujesz Internetu, czy chcesz wiedzieć: jest typowy tego komunikatu z Internetu?

Wykrywanie anomalii flagi nieoczekiwane lub nietypowe zdarzenia lub zachowania. Ta funkcja zapewnia wskazówki gdzie szukać problemów.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Pytanie 3: Ile? i wiele? używa algorytmów regresji
Machine learning można również znacznie przewidywanie odpowiedzi sposób? i wiele? Rodzina algorytmu, który odpowiada na to pytanie jest nazywany regresji.

![Algorytmy regresji: ile? i wiele?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Regresja algorytmów prognozowania wartości liczbowych, takich jak:

* Jaka temperatura będzie dalej wtorek?  
* Jaki będzie moją sprzedaż w czwarty kwartał

Mogą pomóc w odpowiedzi na każde pytanie, która poprosi o podanie numeru.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Pytanie 4: To organizację? zastosowań klastrowania algorytmów
Teraz ostatnie dwa pytania są nieco bardziej zaawansowane.

Czasami trzeba poznać strukturę zestawu danych — to organizację? Na to pytanie nie masz przykładów, które już znasz wyników.

Istnieje wiele sposobów, aby tease struktura danych. Jedno z podejść jest klastrowania. Rozdziela danych do naturalnym "są łączone," ułatwia tłumaczenie. Przy użyciu klastrowania nie ma żadnych jedna prawidłowa odpowiedź.

![Klastrowanie algorytmów: to organizację?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Typowe przykłady klastrowania pytania:

* Które przeglądarki, takich jak te same typy filmy?
* Modele drukarek, które nie jest taki sam sposób?

Zrozumienie, jak dane są organizowane, można lepiej zrozumieć — i przewidywanie - zachowań i zdarzenia.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>5 pytanie: Co należy zrobić teraz? używa algorytmów uczenia przez wzmacnianie
Ostatnie pytanie — co należy zrobić? — używa rodziny o nazwie uczenia przez wzmacnianie algorytmów.

Uczenia przez wzmacnianie został zainspirować jak mózgu rat i ludzie zareagować kar i korzyści. Te algorytmy ucz się od wyniki i podjąć decyzję w sprawie następnej akcji.

Zazwyczaj uczenia przez wzmacnianie jest odpowiednia dla zautomatyzowanych systemów, które mają się wiele małych decyzji bez wskazówki ludzi.

![Algorytmy uczenia przez wzmacnianie: co zrobić dalej?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Pytań, na które odpowiada są zawsze, o jaką akcję należy podjąć — zwykle przez maszynę lub robota. Oto przykłady:

* Jeśli jestem systemem kontroli temperatury budynku: Dopasuj temperatura lub pozostawić je, gdy jest?  
* Mam własnym opracowuje samochód: żółty światła, hamulca lub przyspieszyć?  
* Aby uzyskać robot odkurzający: Zachowaj vacuuming lub wróć do ładowania stacji?

Algorytmów uczenia przez wzmacnianie zbierania danych, zgodnie z ich przejść uczysz się na podstawie prób i błędów.

Dlatego — może odpowiadać na pytania 5 do nauki o danych.

## <a name="next-steps"></a>Kolejne kroki
* [Spróbuj pierwszy eksperyment dotyczący przetwarzania danych, za pomocą usługi Machine Learning Studio](create-experiment.md)
* [Wprowadzenie do usługi Machine Learning na Microsoft Azure](what-is-machine-learning.md)
