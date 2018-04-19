---
title: "Obsługiwane inspektorzy dostępna w programie Azure Machine Learning danych przygotowanie | Dokumentacja firmy Microsoft"
description: "Ten dokument zawiera pełną listę inspektorzy dostępne w celu przygotowania danych usługi Azure Machine Learning"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 5d5797ede15be0779873f0a023433f0a915dd74a
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
---
# <a name="supported-inspectors-for-the-azure-machine-learning-data-preparation-preview"></a>Obsługiwane inspektorzy dla usługi Azure Machine Learning przygotowywania danych z wersji zapoznawczej
W tym dokumencie przedstawiono zbiór inspektorzy, które są dostępne w tej wersji zapoznawczej.

## <a name="the-halo-effect"></a>Efekt obwódki 
Niektóre inspektorzy obsługuje otoczek. W tym celu używa dwóch różnych kolorów, aby natychmiast wyświetlić zmianę wizualnie transformacji. Kolor szary reprezentuje wartość przed najnowsze transformacji i niebieski pokazuje bieżącą wartość. W tym celu może być włączony i wyłączone w opcji.

## <a name="graphical-filtering"></a>Filtrowanie graficznego 
Filtrowanie danych za pomocą Inspektora edytorem niektóre inspektorzy obsługiwane. Za pomocą Inspektora edytorem obejmuje zaznaczając elementy graficzne, a następnie za pomocą narzędzi w prawej górnej części okna inspektora do filtrowania przychodzący lub wychodzący wybranych wartości. 

## <a name="column-statistics"></a>Statystyk kolumny
Dla kolumny liczbowe Ten inspektor zawiera szereg różnych statystyki dotyczące kolumny. Statystyka zawiera następujące pomiary: 
- Minimalne
- Niższe kwartyl
- Mediana
- Górny kwartyl
- Maksimum
- Średnia
- Odchylenie standardowe


### <a name="options"></a>Opcje 
- Brak

## <a name="histogram"></a>Histogram 
Oblicza i wyświetla histogram pojedynczej kolumny liczbowe. Domyślna liczba zasobników jest obliczana przy użyciu reguły Scotta. Jednak można zastąpić reguły za pomocą opcji.

Ten inspektor obsługuje otoczek.


### <a name="options"></a>Opcje
- Minimalna liczba zasobników (ma zastosowanie, nawet gdy bucketing domyślny jest zaznaczone)
- Domyślna liczba zasobników (Scotta reguła) 
- Pokaż halo
- Nakładka kreślenia gęstość jądra (Gaussa jądra) 
- Użyj skali logarytmicznej


### <a name="actions"></a>Akcje
Ten inspektor obsługuje filtrowanie za pośrednictwem pakiety, które mogą obejmować zasobników pojedynczego lub wielokrotnego wyboru. Zastosuj filtry, jak opisano wcześniej.

## <a name="value-counts"></a>Wartość liczby
Ten inspektor przedstawia częstotliwość tabeli wartości dla aktualnie wybranej kolumny. Górny sześciu wartości są wyświetlane domyślne. Można jednak zmienić limit na dowolną liczbę. Można również ustawić widok, aby liczba od dołu zamiast podklauzuli top. Ten inspektor obsługuje otoczek.

### <a name="options"></a>Opcje 
- Liczba wartości górnej
- Malejąco
- Zawierają wartości null/błędów
- Pokaż halo
- Użyj skali logarytmicznej


### <a name="actions"></a>Akcje 
Ten inspektor obsługuje filtrowanie za pośrednictwem paski, które mogą obejmować paski pojedynczego lub wielokrotnego wyboru. Zastosuj filtry, jak opisano wcześniej.

## <a name="box-plot"></a>Wykres skrzynkowy 
Pole kreślenia wąsami kolumny liczbowej.

### <a name="options"></a>Opcje 
- Grupuj według kolumny

## <a name="scatter-plot"></a>Wykres punktowy
Wykres punktowy dla dwóch kolumn liczbowych. Dane są próbkowane w dół ze względu na wydajność. Rozmiar próbki mogą zostać zastąpione w opcjach.

### <a name="options"></a>Opcje  
- Kolumna osi x
- Kolumna osi y
- Rozmiar próbki
- Grupuj według kolumny


## <a name="time-series"></a>Szeregów czasowych
Wykres liniowy z funkcją rozpoznawanie czasu na osi x.

### <a name="options"></a>Opcje
- Data kolumny
- Kolumny liczbowej
- Rozmiar próbki


### <a name="actions"></a>Akcje
Ten inspektor obsługuje filtrowanie przy użyciu metody select kliknij i przeciągnij, aby wybrać zakres na wykresie. Po zakończeniu wyboru Zastosuj filtry, jak opisano wcześniej.


## <a name="map"></a>Mapa 
Mapa punktów, które są kreślone, przy założeniu, że zostały określone współrzędne geograficzne. Najpierw należy wybrać szerokości geograficznej.

### <a name="options"></a>Opcje
- Szerokość kolumny
- Długość geograficzna kolumny
- Klastrowanie w
- Grupuj według kolumny


### <a name="actions"></a>Akcje
Ten inspektor obsługuje filtrowanie za pośrednictwem wyboru punktu na mapie. Naciśnij klawisz **Ctrl** klucza, a następnie kliknij i przeciągnij za pomocą myszy do utworzenia kwadrat wokół punktów. Następnie Zastosuj filtry, jak opisano wcześniej.

Można szybko rozmiar mapę, aby pokazać tylko te punkty możliwe przez naciśnięcie przycisku **E** po lewej stronie mapy.


## <a name="pattern-frequency"></a>Częstotliwość wzorca 

Ten inspektor pokazuje listę wzorców w zaznaczonej kolumnie ciągu. Wzorce są przedstawiane za pomocą składni wyrażenia regularnego. Kursor myszy na wzorcu przedstawiono przykładowe wartości reprezentowany przez ten wzorzec. Wraz z wzorców wyświetlane są również przybliżonej ubezpieczenia w postaci wartości procentowej.

![Obraz inspektora wzorca](media/data-prep-appendix4-supported-inspectors/PatternInspectorProductNumber.png)

### <a name="options"></a>Opcje
- Liczba wartości górnej
- Malejąco
- Pokaż halo

### <a name="actions"></a>Akcje
Ten inspektor obsługuje filtrowanie na podstawie wzorców wyświetlane. Naciśnij klawisz **Ctrl** klucza, a następnie wybierz wypełniony paski inspektora wzorca. Następnie Zastosuj filtry, jak opisano wcześniej. W wyniku acion użytkownik zostanie dodany do kroku filtru zaawansowanego. Możesz wyświetlić i zmodyfikować wygenerowanego kodu Python za pomocą opcji edycji filtru zaawansowanego etapu.
