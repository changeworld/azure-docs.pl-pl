---
title: Azure Machine Learning wykrywania anomalii interfejsu API | Dokumentacja firmy Microsoft
description: Interfejs API wykrywania anomalii to przykład skompilowanej za pomocą programu Microsoft Azure Machine Learning wykrycia anomalii w czasie danych serii za pomocą wartości liczbowe, które są równomiernie rozłożone w czasie.
services: machine-learning
documentationcenter: ''
author: alokkirpal
manager: jhubbard
editor: cgronlun
ms.assetid: 52fafe1f-e93d-47df-a8ac-9a9a53b60824
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/05/2017
ms.author: alok
ms.openlocfilehash: 7633d2bd15e5bc4620a4980623f3883c162f4331
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="machine-learning-anomaly-detection-api"></a>Wykrywanie anomalii interfejsu API uczenia maszynowego
## <a name="overview"></a>Przegląd
[Interfejs API wykrywania anomalii](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) przykład skompilowanej za pomocą usługi Azure Machine Learning, która wykrywa anomalii w czasie danych serii za pomocą wartości liczbowe, które są równomiernie rozłożone w czasie.

Ten interfejs API może wykryć następujące typy nietypowe wzorce w danych w serii. czas:

* **Dodatnie i ujemne trendów**: na przykład po monitorowanie wykorzystania pamięci w przypadku komputerów trendów może być przydatne jako może być świadczy o przeciek pamięci
* **Zmiany w zakresie dynamicznym wartości**: na przykład w przypadku monitorowania wyjątków zgłaszanych przez usługi w chmurze, wszelkie zmiany w zakresie dynamicznym wartości może wskazywać niestabilności kondycji usługi, i
* **Wzrósł i DIP**: na przykład w przypadku monitorowania, liczba niepowodzeń logowania w usłudze lub liczbę wyewidencjonowań w witrynie handlu elektronicznego, nagłego lub DIP może wskazywać nietypowe zachowanie.

Te machine learning detektory śledzić takie zmiany wartości przez raport i czas zmiany zachodzące w ich wartości jako wyniki anomalii. Nie wymaga strojenia próg ad hoc i wyniki można kontrolować szybkość dodatnią wartość false. Wykrywanie anomalii interfejsu API jest przydatne w kilku scenariuszach, takich jak monitorowanie usługi poprzez śledzenie wskaźników KPI w czasie, monitorowania użycia za pomocą metryk, takich jak liczbę wyszukiwań, liczby kliknięć, monitorowanie wydajności za pośrednictwem liczników, takie jak procesor CPU, pamięci odczytuje plik itp. wraz z upływem czasu.

Wykrywanie anomalii oferty jest dostarczany z przydatnych narzędzi ułatwiających rozpoczęcie pracy.

* [Aplikacji sieci web](http://anomalydetection-aml.azurewebsites.net/) pomaga ocenić i wizualizować wyniki wykrywania anomalii interfejsów API na podstawie danych.

> [!NOTE]
> Spróbuj **rozwiązania IT anomalii Insights** obsługiwane przez [tego interfejsu API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
> 
> Aby uzyskać to kompleksowe rozwiązania wdrożone do subskrypcji platformy Azure <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank"> **zacznij tutaj >**</a>
> 
>

## <a name="api-deployment"></a>Wdrażania interfejsu API
Aby korzystać z interfejsu API, należy ją wdrożyć do Twojej subskrypcji platformy Azure, w którym będzie obsługiwana jako usługi sieci web uczenie maszynowe Azure.  Można to zrobić z [galerii Azure AI](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Będzie to wdrożenie dwóch usług sieci Web uczenie maszynowe Azure (i ich powiązane zasoby) do subskrypcji platformy Azure — jeden do wykrywania anomalii z wykrywaniem sezonowości, a drugi bez wykrywania sezonowości.  Po zakończeniu wdrożenia będzie mógł zarządzać swoje interfejsy API z [usług sieci web uczenie maszynowe Azure](https://services.azureml.net/webservices/) strony.  Na tej stronie można znaleźć z punktami końcowymi, klucze interfejsu API, a także przykładowy kod wywołanie interfejsu API.  Bardziej szczegółowe informacje są dostępne [tutaj](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>Skalowanie interfejsu API
Domyślnie wdrożenia mają bezpłatne i testowania plan rozliczeniowy obejmującej 1000 transakcji miesięcznie i obliczeń 2 godziny/miesiąc.  Można uaktualnić do innego planu, zgodnie z potrzebami.  Nie są dostępne informacje o cenach różne plany [tutaj](https://azure.microsoft.com/pricing/details/machine-learning/) w obszarze "Cennik produkcji interfejsu API sieci Web".

## <a name="managing-aml-plans"></a>Zarządzanie AML plany 
Możesz zarządzać planu rozliczeniowego [tutaj](https://services.azureml.net/plans/).  Nazwa planu będzie opierać się na nazwę grupy zasobów, wybrana podczas wdrażania interfejsu API, a także ciąg, który jest unikatowy dla Twojej subskrypcji.  Instrukcje dotyczące uaktualniania planu są dostępne [tutaj](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) w sekcji "Zarządzanie planami rozliczeń".

## <a name="api-definition"></a>Definicja interfejsu API
Usługa sieci web udostępnia interfejs API opartego na interfejsie REST za pośrednictwem protokołu HTTPS, które mogą być używane w różnych sposobów, łącznie z sieci web lub aplikacji mobilnej, R, Python, Excel, itp.  Wysyłanie danych serii czas do tej usługi za pośrednictwem wywołania interfejsu API REST i uruchomieniu kombinacji typów anomalii trzech opisanych poniżej.

## <a name="calling-the-api"></a>Wywołanie interfejsu API
Aby można było wywołać interfejs API, należy znać lokalizacji punktu końcowego i klucz interfejsu API.  Oba te, wraz z przykładowy kod wywołanie interfejsu API, są dostępne z [usług sieci web uczenie maszynowe Azure](https://services.azureml.net/webservices/) strony.  Przejdź do żądanego interfejsu API, a następnie kliknij kartę "Consume", aby je znaleźć.  Należy pamiętać, że można wywołać interfejsu API jako interfejsu API programu Swagger (tj. z parametru adresu URL `format=swagger`) lub jako nie - interfejsu API programu Swagger (tzn. bez `format` parametr adresu URL).  Przykładowy kod w formacie struktury Swagger.  Poniżej jest przykładowe żądanie i odpowiedź w formacie bez struktury Swagger.  Te przykłady są do punktu końcowego sezonowości.  Punkt końcowy z systemem innym niż sezonowości jest podobne.

### <a name="sample-request-body"></a>Treść żądania próbki
Żądanie zawiera dwa obiekty: `Inputs` i `GlobalParameters`.  W żądaniu przykładzie poniżej, niektóre parametry są wysyłane jawnie a inni nie (Przewiń w dół pełną listę parametrów dla każdego punktu końcowego).  Parametry, które nie są jawnie wysłanych w żądaniu użyje wartości domyślnych, podane poniżej.

    {
                "Inputs": {
                        "input1": {
                                "ColumnNames": ["Time", "Data"],
                                "Values": [
                                        ["5/30/2010 18:07:00", "1"],
                                        ["5/30/2010 18:08:00", "1.4"],
                                        ["5/30/2010 18:09:00", "1.1"]
                                ]
                        }
                },
        "GlobalParameters": {
            "tspikedetector.sensitivity": "3",
            "zspikedetector.sensitivity": "3",
            "bileveldetector.sensitivity": "3.25",
            "detectors.spikesdips": "Both"
        }
    }

### <a name="sample-response"></a>Przykładowa odpowiedź
Należy pamiętać, że, aby zobaczyć `ColumnNames` pole musi zawierać `details=true` jako parametr adresu URL w żądaniu.  Zobacz w poniższych tabelach znaczenie za każde z tych pól.

    {
        "Results": {
            "output1": {
                "type": "table",
                "value": {
                    "Values": [
                        ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                        ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                        ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                    ],
                    "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                    "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
                }
            }
        }
    }


## <a name="score-api"></a>Wynik interfejsu API
Interfejs API wynik jest używane do uruchamiania wykrywania anomalii-okresach czasu serii danych. Interfejs API przeprowadza liczbą czujników anomalii danych i zwraca wyniki anomalii. Na poniższej ilustracji przedstawiono przykład anomalii wykrywanych w interfejsie API wynik. Ta szeregów czasowych ma 2 różne zmiany na poziomie, a nagłego 3. Czerwone kropki Pokaż czasu, jaką poziomu zmiana zostaje wykryta, podczas czarne kropki Pokaż nagłego wykryte.
![Wynik interfejsu API][1]

### <a name="detectors"></a>Detektory
Wykrywanie anomalii interfejs API obsługuje detektory w 3 szerokie kategorie. Szczegółowe informacje o określonych parametrów wejściowych i wyjściowych dla każdego detektora znajdują się w poniższej tabeli.

| Wykrywacz kategorii | Wykrywanie | Opis | Parametry wejściowe | Dane wyjściowe |
| --- | --- | --- | --- | --- |
| Detektory kolekcji |Wykrywanie TSpike |Wykryj wartości szczytowe i DIP na podstawie daleko wartości pochodzą z Kwartyle pierwszy i trzeci |*tspikedetector.sensitivity:* przyjmuje wartość całkowitą z zakresu 1-10, domyślne: 3; Wyższe wartości będzie przechwytywać więcej wartości skrajne, dzięki czemu mniej poufne |TSpike: wartości binarne — "1" w przypadku wykrycia kolekcji/dip, "0" w inny sposób |
| Detektory kolekcji | Wykrywanie ZSpike |Wykryj wartości szczytowe i DIP oparte na to, jak daleko punktów danych są od ich średniej |*zspikedetector.sensitivity:* zająć liczbę całkowitą z zakresu 1-10, domyślne: 3; Wyższe wartości będzie przechwytywać więcej wartości skrajne, dzięki czemu mniej poufne |ZSpike: wartości binarne — "1" w przypadku wykrycia kolekcji/dip, "0" w inny sposób | |
| Wykrywanie powolnego Trend |Wykrywanie powolnego Trend |Wykrywanie powolnego trend dodatnią zgodnie z harmonogramem czułości zestawu |*trenddetector.sensitivity:* próg na wynik detektora (domyślne: 3,25, 3,25 – 5 jest uzasadnione zakres, wybierz od; Im wyższa mniej wielkość liter) |tscore: liczbą zmiennoprzecinkową reprezentujący wynik anomalii na tendencji |
| Detektory zmiany poziomu | Poziom dwukierunkowego wykrywanie zmian |Wykryj zmiany poziomu zarówno w górę, jak i w dół zgodnie z harmonogramem czułości zestawu |*bileveldetector.sensitivity:* próg na wynik detektora (domyślne: 3,25, 3,25 – 5 jest uzasadnione zakres, wybierz od; Im wyższa mniej wielkość liter) |rpscore: liczba zmiennoprzecinkowa reprezentująca wynik anomalii w górę i w dół zmiany poziomu | |

### <a name="parameters"></a>Parametry
Bardziej szczegółowe informacje dotyczące tych parametrów wejściowych jest wymienione w poniższej tabeli:

| Parametry wejściowe | Opis | Ustawienie domyślne | Typ | Prawidłowy zakres | Sugerowany zakres |
| --- | --- | --- | --- | --- | --- |
| detectors.historyWindow |Historia (w # punktów danych) używane do obliczeń wynik anomalii |500 |liczba całkowita |10-2000 |Zależne od szeregu czasowego |
| detectors.spikesdips | Czy można wykryć tylko wzrósł, tylko DIP i/lub |Oba |Wyliczenie |Oba nagłego, DIP |Oba |
| bileveldetector.sensitivity |Czułość na poziomie dwukierunkowego zmienić detektora. |3.25 |double |Brak |3,25-5 (mniejsze wartości oznaczają więcej liter) |
| trenddetector.sensitivity |Czułość dla detektora trend dodatnią. |3.25 |double |Brak |3,25-5 (mniejsze wartości oznaczają więcej liter) |
| tspikedetector.sensitivity |Czułość dla detektora TSpike |3 |liczba całkowita |1-10 |3 – 5 (mniejsze wartości oznaczają więcej liter) |
| zspikedetector.sensitivity |Czułość dla detektora ZSpike |3 |liczba całkowita |1-10 |3 – 5 (mniejsze wartości oznaczają więcej liter) |
| postprocess.tailRows |Liczba najnowszych punktów danych przechowywanych w wynikach danych wyjściowych |0 |liczba całkowita |0 (Zachowaj wszystkie punkty danych), lub określ liczbę punktów do zachowania w wynikach |ND |

### <a name="output"></a>Dane wyjściowe
Interfejs API uruchamia wszystkie detektory na podstawie czasu serii danych i zwraca wyniki anomalii i wskaźniki kolekcji binarne dla każdego punktu w czasie. W poniższej tabeli przedstawiono dane wyjściowe z interfejsu API. 

| Dane wyjściowe | Opis |
| --- | --- |
| Time |Sygnatury czasowe z danych pierwotnych lub dane zagregowane (i/lub) kalkulacyjne Jeśli agregacji (i/lub) brakuje przypisywania danych jest stosowany. |
| Dane |Wartości od danych pierwotnych lub dane zagregowane (i/lub) kalkulacyjne w przypadku agregacji (i/lub) brakuje zastosowano przypisywania danych |
| TSpike |Wskaźnik binarny, aby wskazać, czy kolekcja jest wykrywane przez wykrywacz TSpike |
| ZSpike |Wskaźnik binarny, aby wskazać, czy kolekcja jest wykrywane przez wykrywacz ZSpike |
| rpscore |Przestawne numer anomalii reprezentująca wynik na zmiany poziomu dwukierunkowych |
| rpalert |wartość 1/0 wskazującą, czy istnieje poziom dwukierunkowego zmienić anomalii oparte na czułości wejściowych |
| tscore |Przestawne numer anomalii reprezentująca wynik na dodatnią tendencji |
| talert |wartość 1/0 wskazującą jest anomalii trend dodatnią, oparte na czułości wejściowych |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality interfejsu API
Interfejs API ScoreWithSeasonality jest używane do uruchamiania wykrywania anomalii na czas serii, która ma okresach wzorce. Ten interfejs API jest przydatne do wykrywania odchylenia w okresach wzorce.  
Na poniższej ilustracji przedstawiono przykład nieprawidłowości wykryte w serii okresach czasu. Szeregów czasowych ma kolekcji jednego (1 czarne kropki (.)), DIP dwóch (2 czarna kropka i jeden na końcu) i jeden zmiany poziomu (czerwoną kropkę). Należy zauważyć, że dip środku szeregów czasowych i zmiany poziomu tylko discernable po okresach składniki są usuwane z serii.
![Sezonowości interfejsu API][2]

### <a name="detectors"></a>Detektory
Detektory w punkcie końcowym sezonowości są podobne do tych z systemem innym niż sezonowości punktu końcowego, ale nieco różne nazwy parametrów (wymienione poniżej).

### <a name="parameters"></a>Parametry

Bardziej szczegółowe informacje dotyczące tych parametrów wejściowych jest wymienione w poniższej tabeli:

| Parametry wejściowe | Opis | Ustawienie domyślne | Typ | Prawidłowy zakres | Sugerowany zakres |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Interwał agregacji w sekundach do agregowania danych wejściowych szeregów czasowych |0 (nie agregacji jest wykonywane) |liczba całkowita |0: w przeciwnym razie Pomiń agregacji, > 0 |5 minut do 1 dnia, zależne od szeregu czasowego |
| preprocess.aggregationFunc |Funkcja używana do agregowania danych w określonym AggregationInterval |Średnia |Wyliczenie |Średnia, sum, długość |ND |
| preprocess.replaceMissing |Wartości używane do przypisują brakujących danych |lkv (Ostatnia znana wartość) |Wyliczenie |zero, lkv, średnia |ND |
| detectors.historyWindow |Historia (w # punktów danych) używane do obliczeń wynik anomalii |500 |liczba całkowita |10-2000 |Zależne od szeregu czasowego |
| detectors.spikesdips | Czy można wykryć tylko wzrósł, tylko DIP i/lub |Oba |Wyliczenie |Oba nagłego, DIP |Oba |
| bileveldetector.sensitivity |Czułość na poziomie dwukierunkowego zmienić detektora. |3.25 |double |Brak |3,25-5 (mniejsze wartości oznaczają więcej liter) |
| postrenddetector.sensitivity |Czułość dla detektora trend dodatnią. |3.25 |double |Brak |3,25-5 (mniejsze wartości oznaczają więcej liter) |
| negtrenddetector.sensitivity |Czułość dla detektora trend ujemna. |3.25 |double |Brak |3,25-5 (mniejsze wartości oznaczają więcej liter) |
| tspikedetector.sensitivity |Czułość dla detektora TSpike |3 |liczba całkowita |1-10 |3 – 5 (mniejsze wartości oznaczają więcej liter) |
| zspikedetector.sensitivity |Czułość dla detektora ZSpike |3 |liczba całkowita |1-10 |3 – 5 (mniejsze wartości oznaczają więcej liter) |
| seasonality.enable |Określa, czy ma być wykonywane sezonowości analizy |true |wartość logiczna |wartość true, false |Zależne od szeregu czasowego |
| seasonality.numSeasonality |Maksymalna liczba cykli okresowe wykrycie |1 |liczba całkowita |1, 2 |1-2 |
| seasonality.transform |Czy okresach trend części należy usunąć przed zastosowaniem wykrywania anomalii (i) |deseason |Wyliczenie |Brak, deseason deseasontrend |ND |
| postprocess.tailRows |Liczba najnowszych punktów danych przechowywanych w wynikach danych wyjściowych |0 |liczba całkowita |0 (Zachowaj wszystkie punkty danych), lub określ liczbę punktów do zachowania w wynikach |ND |

### <a name="output"></a>Dane wyjściowe
Interfejs API uruchamia wszystkie detektory na podstawie czasu serii danych i zwraca wyniki anomalii i wskaźniki kolekcji binarne dla każdego punktu w czasie. W poniższej tabeli przedstawiono dane wyjściowe z interfejsu API. 

| Dane wyjściowe | Opis |
| --- | --- |
| Time |Sygnatury czasowe z danych pierwotnych lub dane zagregowane (i/lub) kalkulacyjne Jeśli agregacji (i/lub) brakuje przypisywania danych jest stosowany. |
| OriginalData |Wartości od danych pierwotnych lub dane zagregowane (i/lub) kalkulacyjne w przypadku agregacji (i/lub) brakuje zastosowano przypisywania danych |
| ProcessedData |Jedną z następujących czynności: <ul><li>Sezonowo szeregów czasowych, jeśli wykryto znaczących sezonowości deseason opcji;</li><li>sezonowo dostosowane i Beztrendowy szeregów czasowych, jeśli wykryto znaczących sezonowości i wybrana opcja deseasontrend</li><li>w przeciwnym razie jest to ten sam OriginalData</li> |
| TSpike |Wskaźnik binarny, aby wskazać, czy kolekcja jest wykrywane przez wykrywacz TSpike |
| ZSpike |Wskaźnik binarny, aby wskazać, czy kolekcja jest wykrywane przez wykrywacz ZSpike |
| BiLevelChangeScore |Przestawne numer anomalii reprezentująca wynik na zmiany poziomu |
| BiLevelChangeAlert |1/0 wartość wskazującą jest anomalii zmiany poziomu, oparte na czułości wejściowych |
| PosTrendScore |Przestawne numer anomalii reprezentująca wynik na dodatnią tendencji |
| PosTrendAlert |wartość 1/0 wskazującą jest anomalii trend dodatnią, oparte na czułości wejściowych |
| NegTrendScore |Przestawne numer anomalii reprezentująca wynik na ujemnej tendencji |
| NegTrendAlert |wartość 1/0 wskazującą jest anomalii trend ujemna, oparte na czułości wejściowych |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

