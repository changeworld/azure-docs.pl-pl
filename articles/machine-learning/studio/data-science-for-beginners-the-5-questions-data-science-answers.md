---
title: Nauki 5 danych pytania - nauki danych dla początkujących — usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Połączenie analiz danych dla początkujących użytkowników jest zawiera podstawowe pojęcia związane z 5 krótkie wideo, zaczynając od 5 pytania dotyczące danych nauki odpowiedzi. Z uczenia maszynowego Azure.
keywords: podczas analizy danych, dla początkujących analizy danych, analizy danych dla początkujących podstaw analizy danych, pytania dotyczące danych nauki, wideo analizy danych, wprowadzenie nauki danych
services: machine-learning
documentationcenter: na
author: heatherbshapiro
ms.author: hshapiro
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
ms.openlocfilehash: 1d004eb16fcac13d6ba7592cbe432cbeac0401e8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834155"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Wideo 1: analiza danych dla początkujących — 5 pytań, na które analiza danych daje odpowiedzi
Pobierz szybkie wprowadzenie do analizy danych z *nauki danych dla początkujących* w pięciu krótkie filmy wideo z naukowca danych najwyższego. Tych klipów wideo są podstawowy, ale przydatne, czy interesują Cię wykonywania analizy danych lub pracować z analityków danych.

Jest to pierwszy wideo o rodzajach nauki danych pozwala uzyskać odpowiedzi na pytania. Aby uzyskać wykorzystanie serii, obejrzyj je wszystkie. [Przejdź do listy filmów wideo](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Inne pliki wideo w tej serii
*Nauki danych dla początkujących* jest szybkie wprowadzenie do danych nauki biorąc całkowita około 25 minut. Wyewidencjonuj wszystkich pięciu wideo:

* Wideo 1: 5 danych nauki odpowiedzi na pytania
* Wideo 2: [jest gotowy do analizy danych danych?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 s 56 min)*
* Wideo 3: [Zadaj pytanie może odpowiedzieć z danymi](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 s 17 min)*
* Wideo 4: [prognozowania odpowiedzi z modelu prostego](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 s 42 min)*
* Wideo 5: [skopiuj pracy innych osób nauki danych](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek.)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Zapis: 5 pytania dotyczące danych nauki odpowiedzi
Cześć! Seria filmów-Zapraszamy *nauki danych dla początkujących*.

Połączenie analiz danych może mieć OPORY, więc I będzie wprowadzenie podstawy tutaj bez równania lub programistycznych żargonu.

W tym pierwszym wideo omawianiu będzie "5 pytania odpowiedzi nauki danych".

Połączenie analiz danych są używane nazwy (znanej także jako kategorii lub etykiety) i numery do prognozowania odpowiedzi na pytania.

Może on niespodzianek, ale *istnieją tylko pięć pytań tej odpowiedzi nauki danych*:

* Jest to A lub B?
* Jest to nieco dziwne?
* Ile — lub — ile?
* To organizację?
* Co należy zrobić dalej?

Każdej z nich te pytania jest odbierane przez oddzielne rodziny metod learning maszyny, nazywany algorytmów.

Warto pomyśleć o algorytm jako przepisu i dane jako składników. Algorytm zawiera informacje dotyczące łączenia i zestawu danych, aby uzyskać odpowiedzi. Komputery są podobne do mieszarce. Jak większość pracy twardych algorytmu dla Ciebie i jak go dość szybkie.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Pytanie 1: Jest to A lub B? używa algorytmów klasyfikacji
Zacznijmy od kwestii: jest to A lub B?

![Algorytmy klasyfikacji: jest to A lub B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Ta rodziną takich algorytmów jest nazywany dwuklasowych klasyfikacji.

Jest to przydatne w przypadku każde pytanie, który ma tylko dwie możliwe odpowiedzi.

Na przykład:

* Ta opona nie powiedzie się w następnym milach 1000: Yes lub no?
* Dostarcza na więcej klientów: papieru wartościowego $5 lub 25% zniżki?

To pytanie może być również rephrased Aby dołączyć więcej niż dwie opcje: jest to A lub B i C lub D, itp.?  Jest to wieloklasowej klasyfikacji i jej przydatne, gdy istnieje kilka — lub kilka tysięcy — możliwych odpowiedzi. Wieloklasowej klasyfikacji wybiera jeden najprawdopodobniej.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Pytanie 2: Jest to nieco dziwne? używa algorytmów wykrywania anomalii
Następne pytanie nauki danych pozwala uzyskać odpowiedzi na to: jest to dziwne? To pytanie jest odbierane przez rodziną takich algorytmów o nazwie wykrywania anomalii.

![Algorytmy wykrywania anomalii: jest to dziwne?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Jeśli masz karty kredytowej, zostały już korzystali z wykrywania anomalii. Karty kredytowej analizuje Twoich wzorców zakupu, tak aby ich może generować alerty do możliwym oszustwie. Opłaty, które są "nieco dziwne" może być zakupu magazynu, w którym zwykle nie zakupów lub kupowanie niezwykle kosztownych elementu.

To pytanie może być przydatna w inne sposoby. Na wystąpienie:

* Jeśli masz samochodu z mierników wykorzystania, warto wiedzieć: jest tego miernika wykorzystania odczytywania normalne?
* Jeśli jest monitorowanie przez internet, czy chcesz wiedzieć: jest typowy tego komunikatu z Internetu?

Wykrywanie anomalii flagi nieoczekiwany lub nietypowe zdarzenia lub zachowania. Udostępnia wskazówek miejsce wyszukiwania problemów.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Pytanie 3: Ile? lub jak wiele? używa algorytmów regresji
Znacznie uczenia maszynowego można również przewidzieć odpowiedź jak? lub jak wiele? Rodziny algorytmu, który zawiera odpowiedzi na to pytanie jest nazywany regresji.

![Algorytmy regresji: ile? lub jak wiele?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Algorytmy regresji przewidzieć numeryczne, takich jak:

* Jakie temperatury będą dalej wtorek?  
* Jaki będzie Moje czwarty kwartał sprzedaży

One odpowiedzieć na każde pytanie, w którym wprowadza się liczbą.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Pytanie 4: To organizację? używa algorytmów klastra
Ostatnie dwa pytania są teraz bit bardziej zaawansowane.

Czasami chcesz poznać strukturę zestawu danych - to organizację? Na to pytanie nie ma przykłady, które znasz już wyników.

Istnieje wiele sposobów, aby tease limit struktury danych. Klaster jest jednym z podejść. Umożliwia oddzielenie od danych do fizycznych "są łączone," interpretacji łatwiejsze. Z klastra, nie ma żadnych jedna prawidłowa odpowiedź.

![Klastrowanie algorytmy: to organizację?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Typowe przykłady klastrowania pytania:

* Które przeglądarki, takich jak te same rodzaje filmów?
* Modele drukarek, które nie są tak samo?

Zrozumienie, jak dane są organizowane, można lepiej zrozumieć - i przewidywanie - zachowania i zdarzeń.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Pytanie 5: Co należy zrobić teraz? używa wzmocnienie algorytmów uczenia
Ostatni pytanie — co należy zrobić? — używa rodziny algorytmów o nazwie wzmocnienie learning.

Wzmocnienie learning został inspirowana przez jak mózgiem rat i ludzi odpowiadać na kary i korzyści. Algorytmy te informacje z wyników i podejmowanie decyzji o następnej akcji.

Zazwyczaj learning wzmocnienie jest odpowiedni dla zautomatyzowanych systemów, które trzeba tworzyć małych decyzje bez wskazówki człowieka.

![Wzmocnienie algorytmów uczenia: co teraz zrobić?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Pytań, na które odpowiada są zawsze, o jaką akcję należy podjąć — zwykle przez maszynę lub robotem. Przykłady to:

* Jeśli jestem systemem kontroli temperatury domu: Dopasuj temperatury lub pozostawić ją, jeśli jest to?  
* Mam własnym pobudzenie samochodu: światło żółty hamulca lub przyspieszyć?  
* Dla próżni robota: Zachowaj vacuuming lub wróć do stacji ładowania?

Wzmocnienie algorytmów uczenia zbierania danych, zgodnie z ich przejść uczenia z metodą prób i błędów.

Taki sposób, aby go - 5 pytania dotyczące danych nauki może odpowiedzieć.

## <a name="next-steps"></a>Kolejne kroki
* [Spróbuj pierwszego eksperymentu analizy danych z usługi Machine Learning Studio](create-experiment.md)
* [Wprowadzenie do uczenia maszynowego w systemie Microsoft Azure](what-is-machine-learning.md)
