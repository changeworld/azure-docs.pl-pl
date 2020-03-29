---
title: Interfejs API wykrywania anomalii usługi Azure Machine Learning — proces nauki o danych zespołowych
description: Interfejs API wykrywania anomalii jest przykładem zbudowanym za pomocą usługi Microsoft Azure Machine Learning, który wykrywa anomalie w danych szeregów czasowych z wartościami liczbowymi, które są jednolicie rozmieszczone w czasie.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721884"
---
# <a name="machine-learning-anomaly-detection-api"></a>Interfejs API wykrywania anomalii uczenia maszynowego

> [!NOTE]
> Ten element jest w trakcie konserwacji. Zachęcamy do korzystania z [usługi interfejsu API detektora anomalii](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) obsługiwanej przez galerię algorytmów uczenia maszynowego w ramach usługi Azure Cognitive Services w celu wykrywania anomalii z metryk biznesowych, operacyjnych i IoT.

## <a name="overview"></a>Omówienie
[Interfejs API wykrywania anomalii](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) jest przykładem zbudowanym za pomocą usługi Azure Machine Learning, który wykrywa anomalie w danych szeregów czasowych z wartościami liczbowymi, które są jednolicie rozmieszczone w czasie.

Ten interfejs API może wykryć następujące typy nietypowych wzorców w danych szeregów czasowych:

* **Pozytywne i negatywne tendencje**: Na przykład podczas monitorowania wykorzystania pamięci w informatyce trend wzrostowy może być interesujący, ponieważ może to wskazywać na wyciek pamięci,
* **Zmiany w dynamicznym zakresie wartości**: Na przykład podczas monitorowania wyjątków zgłaszanych przez usługę w chmurze wszelkie zmiany w dynamicznym zakresie wartości mogą wskazywać na niestabilność kondycji usługi i
* **Skoki i spadki:** Na przykład podczas monitorowania liczby błędów logowania w usłudze lub liczby transakcji w witrynie handlu elektronicznego, skoki lub spadki mogą wskazywać na nieprawidłowe zachowanie.

Te detektory uczenia maszynowego śledzą takie zmiany wartości w czasie i zgłaszają bieżące zmiany ich wartości jako wyniki anomalii. Nie wymagają one dostrajania progu adhoc, a ich wyniki mogą służyć do kontrolowania fałszywie dodatniego wskaźnika. Interfejs API wykrywania anomalii jest przydatny w kilku scenariuszach, takich jak monitorowanie usług przez śledzenie kluczowych wskaźników wydajności w czasie, monitorowanie użycia za pomocą metryk, takich jak liczba wyszukiwań, liczba kliknięć, monitorowanie wydajności za pomocą liczników, takich jak pamięć, procesor, odczyty plików itp. z czasem.

Oferta wykrywania anomalii zawiera przydatne narzędzia na początek.

* [Aplikacja internetowa](https://anomalydetection-aml.azurewebsites.net/) pomaga oceniać i wizualizować wyniki interfejsów API wykrywania anomalii na danych.

> [!NOTE]
> Wypróbuj **rozwiązanie IT Anomaly Insights** oparte na tym [interfejsie API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>Wdrażanie interfejsu API
Aby korzystać z interfejsu API, należy wdrożyć go w ramach subskrypcji platformy Azure, gdzie będzie hostowany jako usługa sieci web usługi Azure Machine Learning.  Można to zrobić z [Galerii sztucznej inteligencji platformy Azure](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Spowoduje to wdrożenie dwóch usług Azure Machine Learning Studio (klasycznych) usług sieci Web (i powiązanych z nimi zasobów) w ramach subskrypcji platformy Azure — jednej do wykrywania anomalii z wykrywaniem sezonowości i jednej bez wykrywania sezonowości.  Po zakończeniu wdrażania będzie można zarządzać interfejsami API za pomocą strony [usług sieci Web usługi Azure Machine Learning Studio (klasycznej).](https://services.azureml.net/webservices/)  Na tej stronie będzie można znaleźć lokalizacje punktu końcowego, klucze interfejsu API, a także przykładowy kod do wywoływania interfejsu API.  Bardziej szczegółowe instrukcje są dostępne [tutaj](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>Skalowanie interfejsu API
Domyślnie wdrożenie będzie miało bezpłatny plan rozliczeń deweloperskich/testowych, który obejmuje 1000 transakcji/miesiąc i 2 godziny obliczeniowe/miesiąc.  Możesz uaktualnić do innego planu zgodnie z potrzebami.  Szczegółowe informacje na temat cen różnych planów są dostępne [tutaj](https://azure.microsoft.com/pricing/details/machine-learning/) w obszarze "Cennik interfejsu API sieci produkcyjnej".

## <a name="managing-aml-plans"></a>Zarządzanie planami AML
Plan rozliczeniowy można zarządzać [tutaj](https://services.azureml.net/plans/).  Nazwa planu będzie oparta na nazwie grupy zasobów wybranej podczas wdrażania interfejsu API oraz ciągu, który jest unikatowy dla subskrypcji.  Instrukcje dotyczące uaktualniania planu są dostępne [tutaj](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) w sekcji "Zarządzanie planami rozliczeniowymi".

## <a name="api-definition"></a>Definicja interfejsu API
Usługa sieci web zapewnia interfejs API oparty na rest za pośrednictwem protokołu HTTPS, który może być zużywany na różne sposoby, w tym w aplikacji internetowej lub mobilnej, R, Python, Excel itp.  Wysyłasz dane szeregów czasowych do tej usługi za pośrednictwem wywołania interfejsu API REST i uruchamia kombinację trzech typów anomalii opisanych poniżej.

## <a name="calling-the-api"></a>Wywoływanie interfejsu API
Aby wywołać interfejs API, należy znać lokalizację punktu końcowego i klucz interfejsu API.  Te dwa wymagania, wraz z przykładowym kodem do wywoływania interfejsu API, są dostępne na stronie [usług sieci Web usługi Azure Machine Learning Studio (klasyczne).](https://services.azureml.net/webservices/)  Przejdź do żądanego interfejsu API, a następnie kliknij kartę "Konsumuj", aby je znaleźć.  Interfejs API można wywołać jako interfejs API Swagger `format=swagger`(czyli z parametrem URL) lub jako interfejs `format` API niebędący Swaggerem (czyli bez parametru ADRESU URL).  Przykładowy kod używa formatu Swagger.  Poniżej znajduje się przykładowe żądanie i odpowiedź w formacie innych niż Swagger.  Te przykłady są do punktu końcowego sezonowości.  Punkt końcowy niesezonowości jest podobny.

### <a name="sample-request-body"></a>Treść żądania przykładu
Żądanie zawiera dwa `Inputs` obiekty: i `GlobalParameters`.  W poniższym przykładzie żądania niektóre parametry są wysyłane jawnie, podczas gdy inne nie są (przewiń w dół, aby uzyskać pełną listę parametrów dla każdego punktu końcowego).  Parametry, które nie są wysyłane jawnie w żądaniu użyje wartości domyślnych podanych poniżej.

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
Aby wyświetlić `ColumnNames` to pole, `details=true` musisz uwzględnić jako parametr adresu URL w żądaniu.  Zobacz poniższe tabele, aby poznać znaczenie każdego z tych pól.

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


## <a name="score-api"></a>Api wyników
Interfejs API wynik jest używany do uruchamiania wykrywania anomalii na niesezonowych danych szeregów czasowych. Interfejs API uruchamia szereg detektorów anomalii na danych i zwraca ich wyniki anomalii.
Na poniższym rysunku przedstawiono przykład anomalii, które interfejs API wynik może wykryć. Ta seria czasowa ma dwie różne zmiany poziomu i trzy kolce. Czerwone kropki pokazują czas, w którym wykryto zmianę poziomu, podczas gdy czarne kropki pokazują wykryte kolce.
![Api wyników][1]

### <a name="detectors"></a>Detektory
Interfejs API wykrywania anomalii obsługuje detektory w trzech szerokich kategoriach. Szczegółowe informacje na temat określonych parametrów wejściowych i wyjść dla każdego detektora można znaleźć w poniższej tabeli.

| Kategoria detektora | Detektor | Opis | Parametry wejściowe | Dane wyjściowe |
| --- | --- | --- | --- | --- |
| Detektory kolców |Detektor TSpike |Wykrywaj skoki i spadki na podstawie daleko wartości są z pierwszego i trzeciego kwartyla |*tspikedetector.sensitivity:* przyjmuje wartość całkowitą w zakresie 1-10, domyślnie: 3; Wyższe wartości będą łapać bardziej skrajne wartości, dzięki czemu będą mniej czułe |TSpike: wartości binarne – "1", jeśli zostanie wykryty skok/dip, "0" w przeciwnym razie |
| Detektory kolców | Detektor ZSpike |Wykrywaj skoki i spadki na podstawie tego, jak daleko znajdują się punkty danych od ich średniej |*zspikedetector.sensitivity:* wziąć wartość całkowitą w zakresie 1-10, domyślnie: 3; Wyższe wartości będą przechwytywniejsze wartości skrajne, dzięki czemu są mniej czułe |ZSpike: wartości binarne – "1", jeśli zostanie wykryty skok/dip, "0" w przeciwnym razie |
| Powolny czujnik trendu |Powolny czujnik trendu |Wykrywaj powolny trend dodatni zgodnie z ustawioną czułością |*trenddetector.sensitivity:* próg na wynik detektora (domyślnie: 3,25, 3,25 – 5 jest rozsądnym zakresem do wyboru; Im wyższa, tym mniej wrażliwa) |tscore: pływająca liczba reprezentująca wynik anomalii na trendzie |
| Wykrywacze zmiany poziomu | Dwukierunkowy detektor zmiany poziomu |Wykrywaj zarówno zmianę poziomu w górę, jak i w dół zgodnie z ustawioną czułością |*bileveldetector.sensitivity:* próg na wynik detektora (domyślnie: 3,25, 3,25 – 5 jest rozsądnym zakresem do wyboru; Im wyższa, tym mniej wrażliwa) |rpscore: zmienna liczba reprezentująca wynik anomalii na zmianie poziomu w górę i w dół |

### <a name="parameters"></a>Parametry
Bardziej szczegółowe informacje na temat tych parametrów wejściowych są wymienione w poniższej tabeli:

| Parametry wejściowe | Opis | Ustawienie domyślne | Typ | Prawidłowy zakres | Sugerowany zakres |
| --- | --- | --- | --- | --- | --- |
| czujki.historywindow |Historia (w liczbach punktów danych) używana do obliczania wyników anomalii |500 |liczba całkowita |10-2000 |Zależne od szeregów czasowych |
| czujki.kolsy | Czy wykryć tylko kolce, tylko spadki, czy oba |Obie |Wyliczane |Zarówno, Kolce, Spadki |Obie |
| żółci.sensitivity |Czułość dwukierunkowego detektora zmiany poziomu. |3.25 |double |Brak |3.25-5 (Mniejsze wartości oznaczają większą wrażliwość) |
| trenddetector.sensitivity |Czułość dla dodatniego detektora trendu. |3.25 |double |Brak |3.25-5 (Mniejsze wartości oznaczają większą wrażliwość) |
| tspikedetector.sensitivity |Czułość detektora TSpike |3 |liczba całkowita |1-10 |3-5 (Mniejsze wartości oznaczają większą wrażliwość) |
| zspikedetector.sensitivity |Czułość detektora ZSpike |3 |liczba całkowita |1-10 |3-5 (Mniejsze wartości oznaczają większą wrażliwość) |
| postprocess.tailRows |Liczba najnowszych punktów danych, które mają być przechowywane w wynikach wyjściowych |0 |liczba całkowita |0 (zachować wszystkie punkty danych) lub określić liczbę punktów, które mają być w wynikach |Nie dotyczy |

### <a name="output"></a>Dane wyjściowe
Interfejs API uruchamia wszystkie detektory danych szeregów czasowych i zwraca wyniki anomalii i wskaźniki binarne spike dla każdego punktu w czasie. W poniższej tabeli wymieniono dane wyjściowe z interfejsu API.

| Dane wyjściowe | Opis |
| --- | --- |
| Time |Sygnatury czasowe z nieprzetworzonych danych lub zagregowane (i/lub) przypisane dane, jeśli stosuje się agregację (i/lub) brakujące przypisanie danych |
| Dane |Wartości z nieprzetworzonych danych lub zagregowane (i/lub) przypisane dane, jeśli stosuje się agregację (i/lub) brakujące przypisanie danych |
| Tspike ( TSpike ) |Wskaźnik binarny wskazujący, czy detektor TSpike wykrywa skok |
| Okręg wyborczy ZSpike |Wskaźnik binarny wskazujący, czy detektor ZSpike został wykryty przez detektor ZSpike |
| wyniki rpscore |Zmiennoprzecinkowa liczba reprezentująca wynik anomalii na dwukierunkowej zmianie poziomu |
| rpalert |Wartość 1/0 wskazująca, że występuje dwukierunkowa anomalia zmiany poziomu oparta na czułości wejściowej |
| tscore (własówce) |Zmienna liczba reprezentująca wynik anomalii w pozytywnym trendzie |
| talert (talert) |Wartość 1/0 wskazująca, że istnieje dodatnia anomalia trendu oparta na czułości wejściowej |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
Interfejs API ScoreWithSeasonality służy do uruchamiania wykrywania anomalii w szeregach czasowych, które mają wzorce sezonowe. Ten interfejs API jest przydatny do wykrywania odchyleń w sezonowych wzorcach.
Na poniższej ilustracji przedstawiono przykład anomalii wykrytych w sezonowych szeregach czasowych. Szeregi czasowe mają jeden skok (pierwsza czarna kropka), dwa spadki (druga czarna kropka i jedna na końcu) i jedną zmianę poziomu (czerwona kropka). Zarówno spadek w środku szeregów czasowych, jak i zmiana poziomu są zauważalne dopiero po usunięciu składników sezonowych z serii.
![Interfejs API sezonowości][2]

### <a name="detectors"></a>Detektory
Detektory w punkcie końcowym sezonowości są podobne do tych w punkcie końcowym niesezonowości, ale z nieco innymi nazwami parametrów (wymienionymi poniżej).

### <a name="parameters"></a>Parametry

Bardziej szczegółowe informacje na temat tych parametrów wejściowych są wymienione w poniższej tabeli:

| Parametry wejściowe | Opis | Ustawienie domyślne | Typ | Prawidłowy zakres | Sugerowany zakres |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Interwał agregacji w sekundach do agregowania wejściowych szeregów czasowych |0 (nie jest wykonywana agregacja) |liczba całkowita |0: pomiń agregację, > 0 w przeciwnym razie |5 minut do 1 dnia, zależne od szeregów czasowych |
| preprocess.aggregationFunc |Funkcja używana do agregowania danych do określonego AgregacjiInterval |średnia |Wyliczane |średnia, suma, długość |Nie dotyczy |
| preprocess.replaceMissing |Wartości używane do przypisywania brakujących danych |lkv (ostatnia znana wartość) |Wyliczane |zero, lkv, średnia |Nie dotyczy |
| czujki.historywindow |Historia (w liczbach punktów danych) używana do obliczania wyników anomalii |500 |liczba całkowita |10-2000 |Zależne od szeregów czasowych |
| czujki.kolsy | Czy wykryć tylko kolce, tylko spadki, czy oba |Obie |Wyliczane |Zarówno, Kolce, Spadki |Obie |
| żółci.sensitivity |Czułość dwukierunkowego detektora zmiany poziomu. |3.25 |double |Brak |3.25-5 (Mniejsze wartości oznaczają większą wrażliwość) |
| postrenddetector.sensitivity |Czułość dla dodatniego detektora trendu. |3.25 |double |Brak |3.25-5 (Mniejsze wartości oznaczają większą wrażliwość) |
| negtrenddetector.sensitivity |Czułość dla negatywnego detektora trendu. |3.25 |double |Brak |3.25-5 (Mniejsze wartości oznaczają większą wrażliwość) |
| tspikedetector.sensitivity |Czułość detektora TSpike |3 |liczba całkowita |1-10 |3-5 (Mniejsze wartości oznaczają większą wrażliwość) |
| zspikedetector.sensitivity |Czułość detektora ZSpike |3 |liczba całkowita |1-10 |3-5 (Mniejsze wartości oznaczają większą wrażliwość) |
| seasonality.enable |Czy należy przeprowadzić analizę sezonowości |true |wartość logiczna |wartość true, false |Zależne od szeregów czasowych |
| seasonality.numSezonność |Maksymalna liczba cykli okresowych do wykrycia |1 |liczba całkowita |1, 2 |1-2 |
| seasonality.transform |Czy sezonowe (i) składniki trendu są usuwane przed zastosowaniem wykrywania anomalii |deseason |Wyliczane |brak, deseason, deseasontrend |Nie dotyczy |
| postprocess.tailRows |Liczba najnowszych punktów danych, które mają być przechowywane w wynikach wyjściowych |0 |liczba całkowita |0 (zachować wszystkie punkty danych) lub określić liczbę punktów, które mają być w wynikach |Nie dotyczy |

### <a name="output"></a>Dane wyjściowe
Interfejs API uruchamia wszystkie detektory danych szeregów czasowych i zwraca wyniki anomalii i wskaźniki binarne spike dla każdego punktu w czasie. W poniższej tabeli wymieniono dane wyjściowe z interfejsu API.

| Dane wyjściowe | Opis |
| --- | --- |
| Time |Sygnatury czasowe z nieprzetworzonych danych lub zagregowane (i/lub) przypisane dane, jeśli stosuje się agregację (i/lub) brakujące przypisanie danych |
| OriginalData (Dane oryginalne) |Wartości z nieprzetworzonych danych lub zagregowane (i/lub) przypisane dane, jeśli stosuje się agregację (i/lub) brakujące przypisanie danych |
| Przetworzonedane |Jedną z następujących opcji: <ul><li>Szeregi czasowe dostosowane sezonowo, jeśli wykryto znaczną sezonowość i wybrano opcję deseason;</li><li>sezonowo i rozdzielone szeregi czasowe, jeśli wykryto znaczną sezonowość i wybrano opcję deseasontrend</li><li>w przeciwnym razie ta opcja jest taka sama jak OriginalData</li> |
| Tspike ( TSpike ) |Wskaźnik binarny wskazujący, czy detektor TSpike wykrywa skok |
| Okręg wyborczy ZSpike |Wskaźnik binarny wskazujący, czy detektor ZSpike został wykryty przez detektor ZSpike |
| BiLevelChangeScore |Zmiennoprzecinkowa liczba reprezentująca wynik anomalii przy zmianie poziomu |
| BiLevelChangeAlert (BiLevelChangeAlert) |Wartość 1/0 wskazująca, że występuje anomalia zmiany poziomu w oparciu o czułość wejścia |
| PosTrendScore (PosTrendScore) |Zmienna liczba reprezentująca wynik anomalii w pozytywnym trendzie |
| PosTrendAlert (PosTrendAlert) |Wartość 1/0 wskazująca, że istnieje dodatnia anomalia trendu oparta na czułości wejściowej |
| NegTrendScore (NegTrendScore) |Zmienna liczba reprezentująca wynik anomalii w negatywnym trendzie |
| NegTrendAlert (NegTrendAlert) |Wartość 1/0 wskazująca, że istnieje ujemna anomalia trendu oparta na czułości wejściowej |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

