---
title: Zmianę Przewidywanie opóźnień lotów
titleSuffix: Azure Machine Learning service
description: W tym artykule opisano sposób tworzenia modelu uczenia maszynowego w celu przewidywania opóźnień lotów przy użyciu graficznego interfejsu typu "przeciągnij i upuść" oraz niestandardowego kodu języka R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 07/02/2019
ms.openlocfilehash: f2ef5fd17d6c6a91fa5f3c5d62700b68c5fbca24
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855961"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>Przykład 6 — Klasyfikacja: Przewidywanie opóźnień lotów przy użyciu języka R

Ten eksperyment używa historycznych danych lot i Pogoda do przewidywania, czy zaplanowany lot pasażera zostanie opóźniony o ponad 15 minut.

Ten problem może być opisany jako problem klasyfikacji, przewidywanie dwóch klas — opóźnionych lub w czasie. Aby skompilować klasyfikator, ten model korzysta z dużej liczby przykładów z danych lotów historycznej.

Oto wykres z ostatnim eksperymentem:

[![Wykres eksperymentu](media/ui-sample-classification-predict-flight-delay/experiment-graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Wybierz przycisk **Otwórz** dla eksperymentu przykładowego 6:

    ![Otwieranie eksperymentu](media/ui-sample-classification-predict-flight-delay/open-sample6.png)

## <a name="get-the-data"></a>Pobieranie danych

W tym doświadczeniu jest wykorzystywany zestaw **danych opóźnienie lotu** . Jest częścią zbierania danych TranStats z USA Departament transportu. Zestaw danych zawiera informacje o opóźnieniu lotów od kwietnia do października 2013. Przed przekazaniem danych do interfejsu wizualizacji został on wstępnie przetworzony w następujący sposób:

* Filtrowany w celu uwzględnienia portów lotniczych najgorętszym 70 w kontynentalnej Stany Zjednoczone.
* W przypadku anulowanych lotów, które są oznaczone jako opóźnione o więcej niż 15 minut.
* Odfiltrowane przekierowane loty.
* Wybrano 14 kolumn.

Aby uzupełnić dane lotu, używany jest **zestaw danych pogody** . Dane o pogodzie zawierają jednogodzinowe obserwacje pogody z NOAA i przedstawiają obserwacje z stacji pogodowych lotnisk, obejmujących ten sam okres z kwietnia do 2013 października. Przed przekazaniem do interfejsu wizualizacji usługi Azure ML został wstępnie przetworzony w następujący sposób:

* Identyfikatory stacji pogody zostały zamapowane na odpowiednie identyfikatory portów lotniczych.
* Nie zostały usunięte stacje pogody z najgorętszym portów lotniczych 70.
* Kolumna Date została podzielona na oddzielne kolumny: Rok, miesiąc i dzień.
* Wybrano 26 kolumn.

## <a name="pre-process-the-data"></a>Wstępnie przetwórz dane

Zestaw danych wymaga zwykle przetworzenia wstępnego, zanim będzie można go przeanalizować.

![przetwarzanie danych](media/ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>Dane lotu

Kolumny **Carrier**, **OriginAirportID**i **DestAirportID** są zapisywane jako liczby całkowite. Jednak są one atrybutami kategorii, za pomocą modułu **Edytowanie metadanych** można konwertować je na kategorii.

![edit-metadata](media/ui-sample-classification-predict-flight-delay/edit-metadata.png)

Następnie użyj modułu **Wybieranie kolumn** w zestawie danych, aby wykluczyć z kolumn zestawu danych, które są możliwymi przeciekami docelowymi: **DepDelay**, **DepDel15**, **ArrDelay**, **anulowane**, **Year**. 

Aby dołączać rekordy lotu z rekordami godzinową pogody, użyj zaplanowanego czasu wyruszenia jako jednego z kluczy sprzężenia. Aby wykonać sprzężenie, kolumna CSRDepTime musi zostać zaokrąglona w dół do najbliższej godziny, która jest wykonywana w module **wykonywania skryptu języka R** . 

### <a name="weather-data"></a>Dane pogodowe

Kolumny, które mają dużą część brakujących wartości, są wykluczone przy użyciu modułu **kolumn projektu** . Te kolumny zawierają wszystkie kolumny wartości ciągów: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**i **StationPressure**.

Czysty, nieobecny moduł **danych** zostanie zastosowany do pozostałych kolumn w celu usunięcia wierszy z brakującymi danymi.

Czasy obserwacji pogody są zaokrąglane do najbliższej pełnej godziny. Czasy zaplanowanych lotów i czas obserwacji pogodowej są zaokrąglane w przeciwnych kierunkach, aby zapewnić, że model używa tylko pogody przed czasem lotu. 

Ponieważ dane pogody są raportowane w czasie lokalnym, są uwzględniane różnice strefy czasowej przez odjęcie kolumn strefy czasowej od zaplanowanego czasu wyruszenia oraz czasu obserwacji pogodowej. Te operacje są wykonywane za pomocą modułu **skryptu Execute języka R** .

### <a name="joining-datasets"></a>Sprzęganie zestawów danych

Rekordy lotu są dołączane do danych pogodowych, które znajdują się na początku lotu (**OriginAirportID**) przy użyciu modułu **Join Data** .

 ![Dołącz do lotu i pogody według pochodzenia](media/ui-sample-classification-predict-flight-delay/join-origin.png)


Rekordy lotu są przyłączone do danych pogody przy użyciu miejsca docelowego lotu (**DestAirportID**).

 ![Dołącz do lotu i pogody według miejsca docelowego](media/ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Przygotowywanie szkoleń i próbek testowych

Moduł **Split Data** dzieli dane na rekordy z kwietnia na potrzeby szkoleń i rejestrów z października na potrzeby testu.

 ![Podziel dane szkoleniowe i testowe](media/ui-sample-classification-predict-flight-delay/split.png)

Kolumny Year, month i timezone są usuwane z zestawu danych szkoleniowych przy użyciu modułu SELECT Columns.

## <a name="define-features"></a>Definiowanie funkcji

W uczeniu maszynowym funkcje są indywidualnymi właściwościami interesujących rzeczy. Znalezienie silnego zestawu funkcji wymaga eksperymentowania i znajomości domeny. Pewne cechy lepiej nadają się do prognozowania danych docelowych. Ponadto niektóre funkcje mogą mieć silną korelację z innymi funkcjami i nie będą dodawać do modelu nowych informacji. Te funkcje można usunąć.

Aby skompilować model, możesz użyć wszystkich dostępnych funkcji lub wybrać podzestaw funkcji.

## <a name="choose-and-apply-a-learning-algorithm"></a>Wybieranie i stosowanie algorytmu uczenia

Utwórz model przy użyciu dwuklasowego modułu **regresji logistycznej** i pouczenie go w zestawie danych szkoleniowych. 

Wynik modułu uczenie **modelu** jest przeszkolonym modelem klasyfikacji, który może służyć do oceny nowych próbek do prognozowania. Użyj zestawu testów, aby wygenerować wyniki z modeli szkolonych. Następnie należy użyć modułu **Oceń model** do analizowania i porównywania jakości modeli.

Po uruchomieniu eksperymentu możesz wyświetlić dane wyjściowe z modułu **wyniku modelu** , klikając port wyjściowy i wybierając opcję **Wizualizuj**. Dane wyjściowe zawierają etykiety z wynikami i prawdopodobieństwa etykiet.

Na koniec w celu przetestowania jakości wyników Dodaj moduł **Oceń model** do kanwy eksperymentu i Połącz lewy port wejściowy z danymi wyjściowymi modułu modelu wyników. Uruchom eksperyment i Wyświetl dane wyjściowe modułu **Oceń model** , klikając port wyjściowy i wybierając opcję **Wizualizuj**.

## <a name="evaluate"></a>Szacuj
Model regresji logistycznej ma AUC 0,631 w zestawie testów.

 ![evaluate](media/ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi przykładami dostępnymi dla interfejsu wizualizacji:

- [Przykład 1 — regresja: Przewidywanie ceny samochodów](ui-sample-regression-predict-automobile-price-basic.md)
- [Przykład 2 — Regresja: Porównanie algorytmów do prognozowania cen samochodów](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Przykład 3 — Klasyfikacja: Przewidywanie ryzyka kredytowego](ui-sample-classification-predict-credit-risk-basic.md)
- [Przykład 4 — Klasyfikacja: Przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Przykład 5 — Klasyfikacja: Prognozowanie zmian](ui-sample-classification-predict-churn.md)
