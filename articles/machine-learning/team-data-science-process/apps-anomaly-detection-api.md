---
title: Usługi Azure Machine Learning interfejs API — zespołu danych dla celów naukowych wykrywania anomalii
description: Interfejs API wykrywania anomalii jest przykładem utworzonych za pomocą programu Microsoft Azure Machine Learning, która wykrywa anomalie w danych szeregów czasowych za pomocą wartości liczbowe, które są równomiernie rozłożone w czasie.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 06/05/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: b67028562a2c377e1dd99635bdf04cad14782341
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58793139"
---
# <a name="machine-learning-anomaly-detection-api"></a>W usłudze Machine Learning interfejs API wykrywania anomalii

> [!NOTE]
> Ten element jest w trakcie konserwacji. Firma Microsoft zachęca do użycia [usługi interfejsu API wykrywanie anomalii](https://azure.microsoft.com/en-us/services/cognitive-services/anomaly-detector/) obsługiwane przez algorytmy galerię usługi Machine Learning, w ramach usługi Azure Cognitive Services w celu wykrycia anomalii z biznesowe i operacyjne i metryki IoT.

## <a name="overview"></a>Omówienie
[Interfejs API wykrywania anomalii](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) znajduje się przykład utworzonych za pomocą usługi Azure Machine Learning, która wykrywa anomalie w danych szeregów czasowych za pomocą wartości liczbowe, które są równomiernie rozłożone w czasie.

Ten interfejs API może wykryć następujące rodzaje nietypowe wzorce w danych szeregów czasowych:

* **Trendy pozytywne i negatywne**: Na przykład podczas monitorowania użycia pamięci w obliczeniowej trend wzrostowy może być przedmiotem zainteresowania as może być wskazuje przeciek pamięci
* **Zmiany w dynamicznym zakresie wartości**: Na przykład podczas monitorowania wyjątki generowane przez usługę w chmurze, wszystkie zmiany wprowadzone w dynamicznym zakresie wartości może wskazywać niestabilność kondycję usługi, i
* **Gwałtowne wzrosty i spadki**: Na przykład podczas monitorowania liczby nieudanych prób zalogowania w usłudze lub liczbę wyewidencjonowań w witrynie handlu elektronicznego, wzrostów lub spadku może wskazywać nietypowego zachowania.

Detektory learning te maszyny śledzić takie zmiany w wartościach przez raport i czas zmiany zachodzące w ich wartości jako wyniki anomalii. Nie wymaga to dostosowywania progu ad hoc i ich oceny może służyć do kontrolowania wyników fałszywie dodatnich. Wykrywanie anomalii, interfejs API jest przydatne w kilku scenariuszach, takich jak monitorowanie usług, śledząc kluczowe wskaźniki wydajności wraz z upływem czasu, monitorowania użycia za pomocą metryk, takich jak Liczba wyszukiwań, liczby kliknięć, monitorowanie wydajności za pośrednictwem liczników, takich jak pamięć, procesor CPU, odczytuje plik itp. wraz z upływem czasu.

Oferta wykrywania anomalii jest dostarczany z przydatnymi narzędziami, które ułatwią Ci rozpoczęcie pracy.

* [Aplikacji sieci web](https://anomalydetection-aml.azurewebsites.net/) pomoże Ci ocenić i wizualizowania wyników wykrywania anomalii interfejsów API na podstawie posiadanych danych.

> [!NOTE]
> Spróbuj **rozwiązania IT Anomaly Insights** działającemu [tego interfejsu API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>Wdrażanie interfejsu API
Aby można było używać interfejsu API, należy wdrożyć je do subskrypcji platformy Azure, w którym będzie obsługiwana jako usługi sieci web Azure Machine Learning.  Można to zrobić z [galerii Azure AI](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Wdroży to dwie usługi Azure Machine Learning studio w sieci Web usługi (i ich powiązane zasoby) do subskrypcji platformy Azure — jeden dla wykrywanie anomalii przy użyciu wykrywania sezonowości i jedną bez wykrycia sezonowość.  Po zakończeniu wdrożenia można zarządzać z interfejsów API [usługi sieci web usługi Azure Machine Learning studio](https://services.azureml.net/webservices/) strony.  Na tej stronie można znaleźć swoje lokalizacje punktów końcowych, klucze interfejsu API, a także przykładowego kodu dla wywołania interfejsu API.  Bardziej szczegółowe instrukcje są dostępne [tutaj](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>Skalowanie interfejsu API
Domyślnie wdrożenie będzie bezpłatne tworzenie i testowanie planu rozliczeniowego zawierającego 1000 transakcji miesięcznie i obliczeń 2 godz. / miesiąc.  Można uaktualnić do innego planu, zgodnie z potrzebami.  Dostępne są szczegółowe informacje na temat cen różne plany [tutaj](https://azure.microsoft.com/pricing/details/machine-learning/) w obszarze "Cennik produkcyjnego interfejsu API sieci Web".

## <a name="managing-aml-plans"></a>Zarządzanie AML plany
Możesz zarządzać planem rozliczeniowym [tutaj](https://services.azureml.net/plans/).  Nazwa planu będzie zależeć od nazwy grupy zasobów, który został wybrany podczas wdrażania interfejsu API, a także ciąg, który jest unikatowy dla Twojej subskrypcji.  Dostępne są instrukcje dotyczące sposobu uaktualnić swój plan [tutaj](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) w sekcji "Zarządzanie plany rozliczeniowe".

## <a name="api-definition"></a>Definicja interfejsu API
Usługa sieci web udostępnia interfejs API oparty na protokole REST przy użyciu protokołu HTTPS, które mogą być używane na różne sposoby, w tym sieci web lub aplikacji mobilnej, R, Python, Excel, itp.  Wysyłanie danych szeregów czasowych do tej usługi za pośrednictwem wywołania interfejsu API REST i uruchomieniu kombinacji typów anomalii trzy opisane poniżej.

## <a name="calling-the-api"></a>Wywołanie interfejsu API
Aby można było wywołać interfejs API, należy znać lokalizację punktu końcowego i klucz interfejsu API.  Oba te, wraz z przykładowego kodu do wywoływania interfejsu API, są dostępne z [usługi sieci web usługi Azure Machine Learning studio](https://services.azureml.net/webservices/) strony.  Przejdź do żądanego interfejsu API, a następnie kliknij kartę "Wykorzystania", aby je znaleźć.  Należy pamiętać, wywołując interfejs API jako interfejsu API struktury Swagger (tj. z parametru adresu URL `format=swagger`) lub jako innego niż - interfejsu API struktury Swagger (czyli bez `format` parametr adresu URL).  Przykładowy kod korzysta z formatu Swagger.  Poniżej znajduje przykład żądań i odpowiedzi w formacie bez struktury Swagger.  Te przykłady są do endpoint sezonowość.  Punkt końcowy sezonowość nie jest podobny.

### <a name="sample-request-body"></a>Treść żądania próbki
Żądanie zawiera dwa obiekty: `Inputs` i `GlobalParameters`.  W przykładzie żądanie poniżej, niektóre parametry są wysyłane jawnie a inne nie (Przewiń w dół Aby uzyskać pełną listę parametrów dla każdego punktu końcowego).  Parametry, które nie są jawnie wysyłane w żądaniu użyje wartości domyślnych podanych poniżej.

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
Należy pamiętać, że, aby można było wyświetlić `ColumnNames` pole musi zawierać `details=true` jako parametr adresu URL w żądaniu.  Zobacz poniższe tabele zawierają znaczenie za każde z tych pól.

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
Interfejs API wynik jest używane do uruchamiania wykrywania anomalii w danych szeregów czasowych — sezonowym. Interfejs API działa szereg czujników anomalii w danych i zwraca wyniki anomalii.
Na poniższej ilustracji przedstawiono przykład anomalie, które mogą wykryć API wynik. Tej serii czasu ma 2 różne zmiany poziomu i wzrostów 3. Czerwone kropki pokazują czas, w którym zostanie wykryta zmiana poziomu, a czarne kropki zawierają wzrostów wykryte.
![Wynik interfejsu API][1]

### <a name="detectors"></a>Czujniki
Interfejs API wykrywania anomalii obsługuje detektory 3 szerokie kategorie. W poniższej tabeli znajdują się szczegółowe informacje na temat określone parametry wejściowe i wyjściowe dla każdego detektora.

| Wykrywanie kategorii | Wykrywanie | Opis | Parametry wejściowe | Dane wyjściowe |
| --- | --- | --- | --- | --- |
| Detektory kolekcji |Wykrywanie TSpike |Wykrywanie, wzrostów i spadków, na podstawie daleko wartości pochodzą z Kwartyle pierwszy i trzeci |*tspikedetector.sensitivity:* przyjmuje wartość całkowitą z zakresu 1-10, domyślny: 3; Wyższe wartości będzie przechwytywać więcej wartości skrajnych co mniej poufnych |TSpike: wartości binarne — "1", w przypadku wykrycia kolekcji/dip, "0" w przeciwnym razie |
| Detektory kolekcji | Wykrywanie ZSpike |Wykrywanie wzrostów i spadków, oparte na to, jak daleko są punktów danych od ich średniej |*zspikedetector.sensitivity:* zająć wartość całkowitą z zakresu 1-10, domyślny: 3; Wyższe wartości będzie przechwytywać więcej wartości skrajnych co mniej poufnych |ZSpike: wartości binarne — "1", w przypadku wykrycia kolekcji/dip, "0" w przeciwnym razie |
| Wykrywanie powolnego trendu |Wykrywanie powolnego trendu |Wykrywanie powolne trend dodatnią zgodnie z czułości zestawu |*trenddetector.sensitivity:* progu na wykrywanie wynik (domyślne: 3,25, 3,25 — 5 jest uzasadnione zakresu, aby wybrać ten program z; Im wyższa mniej wrażliwe) |tscore: liczbą zmiennoprzecinkową reprezentujący wyniku anomalii na tendencji |
| Detektory zmiany poziomu | Wykrywanie zmian poziom dwukierunkowego |Wykrywanie zmiany poziomu zarówno w górę, jak i w dół zgodnie z czułości zestawu |*bileveldetector.sensitivity:* progu na wykrywanie wynik (domyślne: 3,25, 3,25 — 5 jest uzasadnione zakresu, aby wybrać ten program z; Im wyższa mniej wrażliwe) |rpscore: liczbą zmiennoprzecinkową reprezentujący wyniku anomalii w górę i w dół zmiany poziomu |

### <a name="parameters"></a>Parametry
W poniższej tabeli znajduje się bardziej szczegółowych informacji na temat tych parametrów wejściowych:

| Parametry wejściowe | Opis | Ustawienie domyślne | Typ | Prawidłowy zakres | Sugerowany zakres |
| --- | --- | --- | --- | --- | --- |
| detectors.historywindow |Historii (liczba punktów danych) używane do obliczeń wyniku anomalii |500 |liczba całkowita |10-2000 |Szeregi czasowe zależnych od ustawień lokalnych |
| detectors.spikesdips | Czy należy wykrywać tylko gwałtowne wzrosty, tylko adresy DIP i / lub |Oba |wyliczenia |Zarówno wzrostów spadku |Oba |
| bileveldetector.sensitivity |Uwzględniania wykrywanie zmiany poziomu dwukierunkowy. |3.25 |double |Brak |3,25-5 (mniejsze wartości oznaczają w przypadku bardziej poufnych) |
| trenddetector.sensitivity |Uwzględniania wykrywanie trendów pozytywnych. |3.25 |double |Brak |3,25-5 (mniejsze wartości oznaczają w przypadku bardziej poufnych) |
| tspikedetector.sensitivity |Uwzględniania wykrywacz TSpike |3 |liczba całkowita |1-10 |3 – 5 (mniejsze wartości oznaczają w przypadku bardziej poufnych) |
| zspikedetector.sensitivity |Uwzględniania wykrywacz ZSpike |3 |liczba całkowita |1-10 |3 – 5 (mniejsze wartości oznaczają w przypadku bardziej poufnych) |
| postprocess.tailRows |Liczba najnowsze punkty danych mają być przechowywane w wynikach danych wyjściowych |0 |liczba całkowita |0 (Zachowaj wszystkie punkty danych), lub określ liczbę punktów do zachowania w wynikach |ND |

### <a name="output"></a>Dane wyjściowe
Interfejs API uruchamia wszystkie czujniki w danych szeregów czasowych i zwraca wyniki anomalii i wskaźniki binarne kolekcji dla każdego punktu w czasie. W poniższej tabeli wymieniono dane wyjściowe z interfejsu API.

| Dane wyjściowe | Opis |
| --- | --- |
| Time |Sygnatury czasowe z nieprzetworzonych danych albo dane zagregowane (i/lub) kalkulacyjne Jeśli agregacji (i/lub) brakuje przypisywania danych jest stosowany. |
| Dane |Wartości z kolekcji danych pierwotnych lub danych zagregowanych (i/lub) kalkulacyjne, jeśli agregacji (i/lub) brakuje przypisywania danych jest stosowany. |
| TSpike |Wskaźnik binarny, aby wskazać, czy kolekcja zostanie wykryty przez wykrywacz TSpike |
| ZSpike |Wskaźnik binarny, aby wskazać, czy kolekcja zostanie wykryty przez wykrywacz ZSpike |
| rpscore |Zmiennoprzecinkowy numer anomalii reprezentująca wynik w przypadku zmiany poziomu dwukierunkowe |
| rpalert |wartość 1/0, wskazujący, że istnieje poziom dwukierunkowego zmienić anomalii na podstawie poziomu poufności danych wejściowych |
| tscore |Zmiennoprzecinkowy numer anomalii reprezentująca wynik na dodatnią tendencji |
| talert |wartość 1/0, wskazującą, jest anomalii trendów pozytywnych, na podstawie poziomu poufności danych wejściowych |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality interfejsu API
Interfejs API ScoreWithSeasonality jest używane do uruchamiania wykrywania anomalii w serii czasu, która sezonowych wzorców. Ten interfejs API jest przydatne do wykrywania odchylenia we wzorcach sezonowe.
Poniższa ilustracja przedstawia przykład nieprawidłowości wykryto w szeregach czasowych sezonowym. Szeregi czasowe ma kolekcji jednego (1 czarne kropki (.)), dwa spadku (2nd czarna kropka, a drugi na końcu) i jeden zmiany poziomu (czerwoną kropkę). Należy zauważyć, że dip środku szeregów czasowych i zmiany poziomu tylko przyspieszenie po sezonowych składniki zostaną usunięte z tej serii.
![Sezonowość interfejsu API][2]

### <a name="detectors"></a>Czujniki
Detektory w punkcie końcowym sezonowości są podobne do tych w punkcie końcowym bez sezonowości, ale z nazwy parametru nieco (wymienione poniżej).

### <a name="parameters"></a>Parametry

W poniższej tabeli znajduje się bardziej szczegółowych informacji na temat tych parametrów wejściowych:

| Parametry wejściowe | Opis | Ustawienie domyślne | Typ | Prawidłowy zakres | Sugerowany zakres |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Interwał agregacji w ciągu kilku sekund do agregowania danych wejściowych szeregów czasowych |0 (Brak agregacji odbywa się) |liczba całkowita |0: w przeciwnym razie Pomiń agregacji > 0 |5 minut do 1 dzień, szeregów czasowych zależnych od ustawień lokalnych |
| preprocess.aggregationFunc |Funkcja używana do agregowania danych do określonego AggregationInterval |Średnia |wyliczenia |średnie, sum, długość |ND |
| preprocess.replaceMissing |Wartości używane do przypisują brakujące dane |lkv (Ostatnia znana wartość) |wyliczenia |zero, lkv, średnia |ND |
| detectors.historywindow |Historii (liczba punktów danych) używane do obliczeń wyniku anomalii |500 |liczba całkowita |10-2000 |Szeregi czasowe zależnych od ustawień lokalnych |
| detectors.spikesdips | Czy należy wykrywać tylko gwałtowne wzrosty, tylko adresy DIP i / lub |Oba |wyliczenia |Zarówno wzrostów spadku |Oba |
| bileveldetector.sensitivity |Uwzględniania wykrywanie zmiany poziomu dwukierunkowy. |3.25 |double |Brak |3,25-5 (mniejsze wartości oznaczają w przypadku bardziej poufnych) |
| postrenddetector.sensitivity |Uwzględniania wykrywanie trendów pozytywnych. |3.25 |double |Brak |3,25-5 (mniejsze wartości oznaczają w przypadku bardziej poufnych) |
| negtrenddetector.sensitivity |Uwzględniania wykrywacz trend ujemna. |3.25 |double |Brak |3,25-5 (mniejsze wartości oznaczają w przypadku bardziej poufnych) |
| tspikedetector.sensitivity |Uwzględniania wykrywacz TSpike |3 |liczba całkowita |1-10 |3 – 5 (mniejsze wartości oznaczają w przypadku bardziej poufnych) |
| zspikedetector.sensitivity |Uwzględniania wykrywacz ZSpike |3 |liczba całkowita |1-10 |3 – 5 (mniejsze wartości oznaczają w przypadku bardziej poufnych) |
| seasonality.enable |Czy ma być przeprowadzane sezonowości analizy |true |wartość logiczna |wartość true, false |Szeregi czasowe zależnych od ustawień lokalnych |
| seasonality.numSeasonality |Maksymalna liczba cykli okresowo, aby zostało wykryte |1 |liczba całkowita |1, 2 |1-2 |
| seasonality.transform |Czy sezonowych części trend należy usunąć przed zastosowaniem wykrywania anomalii (i) |deseason |wyliczenia |Brak, deseason deseasontrend |ND |
| postprocess.tailRows |Liczba najnowsze punkty danych mają być przechowywane w wynikach danych wyjściowych |0 |liczba całkowita |0 (Zachowaj wszystkie punkty danych), lub określ liczbę punktów do zachowania w wynikach |ND |

### <a name="output"></a>Dane wyjściowe
Interfejs API uruchamia wszystkie czujniki w danych szeregów czasowych i zwraca wyniki anomalii i wskaźniki binarne kolekcji dla każdego punktu w czasie. W poniższej tabeli wymieniono dane wyjściowe z interfejsu API.

| Dane wyjściowe | Opis |
| --- | --- |
| Time |Sygnatury czasowe z nieprzetworzonych danych albo dane zagregowane (i/lub) kalkulacyjne Jeśli agregacji (i/lub) brakuje przypisywania danych jest stosowany. |
| OriginalData |Wartości z kolekcji danych pierwotnych lub danych zagregowanych (i/lub) kalkulacyjne, jeśli agregacji (i/lub) brakuje przypisywania danych jest stosowany. |
| ProcessedData |Jedną z następujących czynności: <ul><li>Jeśli znaczące sezonowości został wykryty i deseason opcji; sezonowo szeregów czasowych</li><li>okresowo zmieniane i Beztrendowy szeregów czasowych, jeśli wykryto sezonowości znaczące i wybraną opcją deseasontrend</li><li>w przeciwnym razie jest taka sama jak OriginalData</li> |
| TSpike |Wskaźnik binarny, aby wskazać, czy kolekcja zostanie wykryty przez wykrywacz TSpike |
| ZSpike |Wskaźnik binarny, aby wskazać, czy kolekcja zostanie wykryty przez wykrywacz ZSpike |
| BiLevelChangeScore |Zmiennoprzecinkowy numer anomalii reprezentująca wynik w przypadku zmiany poziomu |
| BiLevelChangeAlert |1/0 wartość wskazującą brak jest anomalii zmiany poziomu, na podstawie poziomu poufności danych wejściowych |
| PosTrendScore |Zmiennoprzecinkowy numer anomalii reprezentująca wynik na dodatnią tendencji |
| PosTrendAlert |wartość 1/0, wskazującą, jest anomalii trendów pozytywnych, na podstawie poziomu poufności danych wejściowych |
| NegTrendScore |Zmiennoprzecinkowy numer anomalii reprezentująca wynik na trend ujemna |
| NegTrendAlert |wartość 1/0, wskazującą, jest anomalii trend ujemna, na podstawie poziomu poufności danych wejściowych |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

