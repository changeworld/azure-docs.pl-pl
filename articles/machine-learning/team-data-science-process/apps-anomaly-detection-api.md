---
title: Usługi Azure Machine Learning interfejs API — zespołu danych dla celów naukowych wykrywania anomalii
description: Interfejs API wykrywania anomalii jest przykładem utworzonych za pomocą programu Microsoft Azure Machine Learning, która wykrywa anomalie w danych szeregów czasowych za pomocą wartości liczbowe, które są równomiernie rozłożone w czasie.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: a09094cf0d1bd3c2e299e968d7de8410dcd9c3cb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721884"
---
# <a name="machine-learning-anomaly-detection-api"></a>W usłudze Machine Learning interfejs API wykrywania anomalii

> [!NOTE]
> Ten element jest w trakcie konserwacji. Zachęcamy do używania [usługi interfejsu API wykrywania anomalii](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) obsługiwanej przez galerię Machine Learning algorytmów w usłudze Azure Cognitive Services w celu wykrywania anomalii z metryk biznesowych, operacyjnych i IoT.

## <a name="overview"></a>Omówienie
[Interfejs API wykrywania anomalii](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) jest przykładem opartym na Azure Machine Learning, które wykrywa anomalie w danych szeregów czasowych z wartościami liczbowymi, które są jednolicie przemieszczone w czasie.

Ten interfejs API może wykryć następujące rodzaje nietypowe wzorce w danych szeregów czasowych:

* **Trendy pozytywne i negatywne**: na przykład podczas monitorowania użycia pamięci podczas obliczania trendu w górę może być istotne, ponieważ może to stanowić wskazówkę przecieku pamięci.
* **Zmiany w dynamicznym zakresie wartości**: na przykład podczas monitorowania wyjątków zgłoszonych przez usługę w chmurze wszelkie zmiany w dynamicznym zakresie wartości mogą wskazywać na niestabilność kondycji usługi i
* Liczby **i wartości DIP**: na przykład podczas monitorowania liczby niepowodzeń logowania w usłudze lub liczby wyewidencjonowania w witrynie handlu elektronicznego, skoki lub DIP mogą wskazywać nietypowe zachowanie.

Detektory learning te maszyny śledzić takie zmiany w wartościach przez raport i czas zmiany zachodzące w ich wartości jako wyniki anomalii. Nie wymaga to dostosowywania progu ad hoc i ich oceny może służyć do kontrolowania wyników fałszywie dodatnich. Wykrywanie anomalii, interfejs API jest przydatne w kilku scenariuszach, takich jak monitorowanie usług, śledząc kluczowe wskaźniki wydajności wraz z upływem czasu, monitorowania użycia za pomocą metryk, takich jak Liczba wyszukiwań, liczby kliknięć, monitorowanie wydajności za pośrednictwem liczników, takich jak pamięć, procesor CPU, odczytuje plik itp. wraz z upływem czasu.

Oferta wykrywania anomalii jest dostarczany z przydatnymi narzędziami, które ułatwią Ci rozpoczęcie pracy.

* [Aplikacja sieci Web](https://anomalydetection-aml.azurewebsites.net/) pomaga w ocenie i wizualizowaniu wyników interfejsów API wykrywania anomalii na danych.

> [!NOTE]
> Wypróbuj **rozwiązanie "anomalia Insights** " obsługiwane przez [ten interfejs API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>Wdrażanie interfejsu API
Aby można było używać interfejsu API, należy wdrożyć je do subskrypcji platformy Azure, w którym będzie obsługiwana jako usługi sieci web Azure Machine Learning.  Można to zrobić z poziomu [Azure AI Gallery](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Spowoduje to wdrożenie dwóch Azure Machine Learning Studio (klasycznych) usług sieci Web (i ich powiązanych zasobów) w ramach subskrypcji platformy Azure — jednej do wykrywania anomalii z wykrywaniem sezonowości i jednego bez wykrywania sezonowości.  Po zakończeniu wdrożenia będziesz mieć możliwość zarządzania interfejsami API na stronie [usług sieci web Azure Machine Learning Studio (klasyczna)](https://services.azureml.net/webservices/) .  Na tej stronie można znaleźć swoje lokalizacje punktów końcowych, klucze interfejsu API, a także przykładowego kodu dla wywołania interfejsu API.  Bardziej szczegółowe instrukcje są dostępne [tutaj](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>Skalowanie interfejsu API
Domyślnie wdrożenie będzie miało bezpłatny plan rozliczania dla tworzenia i testowania, który obejmuje 1 000 transakcji na miesiąc i 2 godziny obliczeniowe miesięcznie.  Można uaktualnić do innego planu, zgodnie z potrzebami.  Szczegółowe informacje na temat cen różnych planów są dostępne w [tym miejscu](https://azure.microsoft.com/pricing/details/machine-learning/) w obszarze "ceny produkcyjnego interfejsu API sieci Web".

## <a name="managing-aml-plans"></a>Zarządzanie AML plany
Plan rozliczeniowy możesz zarządzać [tym miejscu](https://services.azureml.net/plans/).  Nazwa planu będzie zależeć od nazwy grupy zasobów, który został wybrany podczas wdrażania interfejsu API, a także ciąg, który jest unikatowy dla Twojej subskrypcji.  Instrukcje dotyczące sposobu uaktualniania planu są dostępne w [tym miejscu](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) w sekcji "Zarządzanie planami rozliczeń".

## <a name="api-definition"></a>Definicja interfejsu API
Usługa sieci Web udostępnia interfejs API oparty na protokole REST za pośrednictwem protokołu HTTPS, który można wykorzystać na różne sposoby, w tym aplikacje internetowe lub mobilne, R, Python, Excel itd.  Dane szeregów czasowych są wysyłane do tej usługi za pośrednictwem wywołania interfejsu API REST i uruchamiają kombinację trzech typów anomalii opisanych poniżej.

## <a name="calling-the-api"></a>Wywołanie interfejsu API
Aby można było wywołać interfejs API, należy znać lokalizację punktu końcowego i klucz interfejsu API.  Te dwa wymagania oraz przykładowy kod służący do wywoływania interfejsu API są dostępne na stronie [usług sieci web Azure Machine Learning Studio (klasycznych)](https://services.azureml.net/webservices/) .  Przejdź do żądanego interfejsu API, a następnie kliknij kartę "Wykorzystania", aby je znaleźć.  Interfejs API można wywołać jako interfejs API struktury Swagger (czyli przy użyciu parametru adresu URL `format=swagger`) lub jako interfejs API niebędący strukturą Swagger (czyli bez parametru adresu URL `format`).  Przykładowy kod korzysta z formatu Swagger.  Poniżej znajduje przykład żądań i odpowiedzi w formacie bez struktury Swagger.  Te przykłady są do endpoint sezonowość.  Punkt końcowy sezonowość nie jest podobny.

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
Aby wyświetlić pole `ColumnNames`, w żądaniu należy uwzględnić `details=true` jako parametr adresu URL.  Zobacz poniższe tabele zawierają znaczenie za każde z tych pól.

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
Na poniższej ilustracji przedstawiono przykład anomalie, które mogą wykryć API wynik. Ta seria czasowa ma dwie zmiany odrębnego poziomu i trzy skoki. Czerwone kropki pokazują czas, w którym zostanie wykryta zmiana poziomu, a czarne kropki zawierają wzrostów wykryte.
![][1] API oceny

### <a name="detectors"></a>Czujniki
Interfejs API wykrywania anomalii obsługuje detektory w trzech różnych kategoriach. W poniższej tabeli znajdują się szczegółowe informacje na temat określone parametry wejściowe i wyjściowe dla każdego detektora.

| Wykrywanie kategorii | Wykrywanie | Opis | Parametry wejściowe | Dane wyjściowe |
| --- | --- | --- | --- | --- |
| Detektory kolekcji |Wykrywanie TSpike |Wykrywanie, wzrostów i spadków, na podstawie daleko wartości pochodzą z Kwartyle pierwszy i trzeci |*tspikedetector. czułość:* Pobiera wartość całkowitą z zakresu 1-10, wartość domyślna: 3; Wyższe wartości będą przechwytywać bardziej rygorystyczne wartości w taki sposób, aby była mniej wrażliwa |TSpike: wartości binarne — "1", w przypadku wykrycia kolekcji/dip, "0" w przeciwnym razie |
| Detektory kolekcji | Wykrywanie ZSpike |Wykrywanie wzrostów i spadków, oparte na to, jak daleko są punktów danych od ich średniej |*zspikedetector. czułość:* Weź wartość całkowitą z zakresu 1-10, wartość domyślna: 3; Wyższe wartości spowodują przechwycenie bardziej rygorystycznych wartości, co zmniejsza ich czułość |ZSpike: wartości binarne — "1", w przypadku wykrycia kolekcji/dip, "0" w przeciwnym razie |
| Wykrywanie powolnego trendu |Wykrywanie powolnego trendu |Wykrywanie powolne trend dodatnią zgodnie z czułości zestawu |*trenddetector. czułość:* próg dla wyniku czujnika (wartość domyślna: 3,25, 3,25 – 5 jest rozsądnym zakresem do wyboru; Im wyższa mniejsza czułość) |tscore: liczbą zmiennoprzecinkową reprezentujący wyniku anomalii na tendencji |
| Detektory zmiany poziomu | Wykrywanie zmian poziom dwukierunkowego |Wykrywanie zmiany poziomu zarówno w górę, jak i w dół zgodnie z czułości zestawu |*bileveldetector. czułość:* próg dla wyniku czujnika (wartość domyślna: 3,25, 3,25 – 5 jest rozsądnym zakresem do wyboru; Im wyższa mniejsza czułość) |rpscore: liczbą zmiennoprzecinkową reprezentujący wyniku anomalii w górę i w dół zmiany poziomu |

### <a name="parameters"></a>Parametry
W poniższej tabeli znajduje się bardziej szczegółowych informacji na temat tych parametrów wejściowych:

| Parametry wejściowe | Opis | Ustawienie domyślne | Typ | Prawidłowy zakres | Sugerowany zakres |
| --- | --- | --- | --- | --- | --- |
| detectors.historywindow |Historii (liczba punktów danych) używane do obliczeń wyniku anomalii |500 |liczba całkowita |10-2000 |Szeregi czasowe zależnych od ustawień lokalnych |
| detectors.spikesdips | Czy należy wykrywać tylko gwałtowne wzrosty, tylko adresy DIP i / lub |Obie |wyliczony |Zarówno wzrostów spadku |Obie |
| bileveldetector.sensitivity |Uwzględniania wykrywanie zmiany poziomu dwukierunkowy. |3.25 |double |None |3,25-5 (mniejsze wartości oznaczają w przypadku bardziej poufnych) |
| trenddetector.sensitivity |Uwzględniania wykrywanie trendów pozytywnych. |3.25 |double |None |3,25-5 (mniejsze wartości oznaczają w przypadku bardziej poufnych) |
| tspikedetector.sensitivity |Uwzględniania wykrywacz TSpike |3 |liczba całkowita |1-10 |3 – 5 (mniejsze wartości oznaczają w przypadku bardziej poufnych) |
| zspikedetector.sensitivity |Uwzględniania wykrywacz ZSpike |3 |liczba całkowita |1-10 |3 – 5 (mniejsze wartości oznaczają w przypadku bardziej poufnych) |
| postprocess.tailRows |Liczba najnowsze punkty danych mają być przechowywane w wynikach danych wyjściowych |0 |liczba całkowita |0 (Zachowaj wszystkie punkty danych), lub określ liczbę punktów do zachowania w wynikach |Nie dotyczy |

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
Poniższa ilustracja przedstawia przykład nieprawidłowości wykryto w szeregach czasowych sezonowym. Szeregi czasowe ma jeden skok (pierwsza czarna kropka), dwa DIP (druga czarna kropka i jeden na końcu) i jedną zmianę poziomu (czerwona kropka). Zarówno adres DIP w środku szeregów czasowych, jak i zmiana poziomu są discernable po usunięciu składników sezonowych z serii.
![interfejs API sezonowości][2]

### <a name="detectors"></a>Czujniki
Detektory w punkcie końcowym sezonowości są podobne do tych w punkcie końcowym bez sezonowości, ale z nazwy parametru nieco (wymienione poniżej).

### <a name="parameters"></a>Parametry

W poniższej tabeli znajduje się bardziej szczegółowych informacji na temat tych parametrów wejściowych:

| Parametry wejściowe | Opis | Ustawienie domyślne | Typ | Prawidłowy zakres | Sugerowany zakres |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Interwał agregacji w ciągu kilku sekund do agregowania danych wejściowych szeregów czasowych |0 (Brak agregacji odbywa się) |liczba całkowita |0: w przeciwnym razie Pomiń agregacji > 0 |5 minut do 1 dzień, szeregów czasowych zależnych od ustawień lokalnych |
| preprocess.aggregationFunc |Funkcja używana do agregowania danych do określonego AggregationInterval |Średnia |wyliczony |średnie, sum, długość |Nie dotyczy |
| preprocess.replaceMissing |Wartości używane do przypisują brakujące dane |lkv (Ostatnia znana wartość) |wyliczony |zero, lkv, średnia |Nie dotyczy |
| detectors.historywindow |Historii (liczba punktów danych) używane do obliczeń wyniku anomalii |500 |liczba całkowita |10-2000 |Szeregi czasowe zależnych od ustawień lokalnych |
| detectors.spikesdips | Czy należy wykrywać tylko gwałtowne wzrosty, tylko adresy DIP i / lub |Obie |wyliczony |Zarówno wzrostów spadku |Obie |
| bileveldetector.sensitivity |Uwzględniania wykrywanie zmiany poziomu dwukierunkowy. |3.25 |double |None |3,25-5 (mniejsze wartości oznaczają w przypadku bardziej poufnych) |
| postrenddetector.sensitivity |Uwzględniania wykrywanie trendów pozytywnych. |3.25 |double |None |3,25-5 (mniejsze wartości oznaczają w przypadku bardziej poufnych) |
| negtrenddetector.sensitivity |Uwzględniania wykrywacz trend ujemna. |3.25 |double |None |3,25-5 (mniejsze wartości oznaczają w przypadku bardziej poufnych) |
| tspikedetector.sensitivity |Uwzględniania wykrywacz TSpike |3 |liczba całkowita |1-10 |3 – 5 (mniejsze wartości oznaczają w przypadku bardziej poufnych) |
| zspikedetector.sensitivity |Uwzględniania wykrywacz ZSpike |3 |liczba całkowita |1-10 |3 – 5 (mniejsze wartości oznaczają w przypadku bardziej poufnych) |
| seasonality.enable |Czy ma być przeprowadzane sezonowości analizy |true |wartość logiczna |wartość true, false |Szeregi czasowe zależnych od ustawień lokalnych |
| seasonality.numSeasonality |Maksymalna liczba cykli okresowo, aby zostało wykryte |1 |liczba całkowita |1, 2 |1-2 |
| seasonality.transform |Czy sezonowych części trend należy usunąć przed zastosowaniem wykrywania anomalii (i) |deseason |wyliczony |Brak, deseason deseasontrend |Nie dotyczy |
| postprocess.tailRows |Liczba najnowsze punkty danych mają być przechowywane w wynikach danych wyjściowych |0 |liczba całkowita |0 (Zachowaj wszystkie punkty danych), lub określ liczbę punktów do zachowania w wynikach |Nie dotyczy |

### <a name="output"></a>Dane wyjściowe
Interfejs API uruchamia wszystkie czujniki w danych szeregów czasowych i zwraca wyniki anomalii i wskaźniki binarne kolekcji dla każdego punktu w czasie. W poniższej tabeli wymieniono dane wyjściowe z interfejsu API.

| Dane wyjściowe | Opis |
| --- | --- |
| Time |Sygnatury czasowe z nieprzetworzonych danych albo dane zagregowane (i/lub) kalkulacyjne Jeśli agregacji (i/lub) brakuje przypisywania danych jest stosowany. |
| OriginalData |Wartości z kolekcji danych pierwotnych lub danych zagregowanych (i/lub) kalkulacyjne, jeśli agregacji (i/lub) brakuje przypisywania danych jest stosowany. |
| ProcessedData |Jedną z następujących opcji: <ul><li>Jeśli znaczące sezonowości został wykryty i deseason opcji; sezonowo szeregów czasowych</li><li>okresowo zmieniane i Beztrendowy szeregów czasowych, jeśli wykryto sezonowości znaczące i wybraną opcją deseasontrend</li><li>w przeciwnym razie ta opcja jest taka sama jak OriginalData</li> |
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

