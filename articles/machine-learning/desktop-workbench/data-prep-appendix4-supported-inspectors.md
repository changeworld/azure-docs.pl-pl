---
title: Obsługiwane inspektorzy dostępne usługi Azure Machine Learning danych przygotowanie | Dokumentacja firmy Microsoft
description: Ten dokument zawiera listę wszystkich inspektorzy dostępne podczas przygotowywania danych usługi Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: e6af524d7f80104a34831f576a14d2dbf15e384f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987207"
---
# <a name="supported-inspectors-for-the-azure-machine-learning-data-preparation-preview"></a>Obsługiwane inspektorzy dla usługi Azure Machine Learning przygotowywania danych w wersji zapoznawczej

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


W tym dokumencie przedstawiono zestaw inspektorzy, które są dostępne w tej wersji zapoznawczej.

## <a name="the-halo-effect"></a>Efekt obwódki 
Niektóre inspektorzy obsługuje obwódkę. Ten efekt używa dwóch różnych kolorów, aby natychmiast wyświetlić zmiany wizualne z transformacji. Kolor szary reprezentuje wartość przed Przekształcanie najnowsze i niebieski pokazuje bieżącą wartość. Ten efekt można włączyć i wyłączyć w opcjach.

## <a name="graphical-filtering"></a>Filtrowanie graficzne 
Niektóre inspektorów obsługuje filtrowanie danych przy użyciu Inspektora jako edytora. Inspektor jako edytor, którego obejmuje wybierania elementów graficznych, a następnie przy użyciu narzędzi w prawej górnej części okna inspektora do filtrowania wewnątrz lub na zewnątrz wybranych wartości. 

## <a name="column-statistics"></a>Statystyki kolumn
W przypadku kolumny liczbowe Ten inspektor zapewnia szereg różnych statystyki dotyczące kolumny. Statystyka zawiera następujące pomiary: 
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
Oblicza i wyświetla histogram pojedynczej kolumny liczbowej. Domyślna liczba przedziałów jest obliczana przy użyciu reguły Scotta. Jednak reguły można zastąpić za pomocą opcji.

To narzędzie Inspector obsługuje obwódkę.


### <a name="options"></a>Opcje
- Minimalna liczba przedziałów (ma zastosowanie, nawet gdy obsługą zasobników domyślny jest zaznaczone)
- Domyślna liczba zasobników (reguła Scotta) 
- Pokaż obwódkę
- Nakładka wykres gęstość jądra (Gaussa jądra) 
- Użyj skali logarytmicznej


### <a name="actions"></a>Akcje
To narzędzie inspector obsługuje filtrowanie za pomocą zasobników, które mogą obejmować zasobników pojedynczego lub wielokrotnego wyboru. Zastosuj filtry, jak opisano wcześniej.

## <a name="value-counts"></a>Wartość liczby
Ten inspektor przedstawia tabelę częstotliwość wartości dla kolumny, która jest aktualnie wybrany. Największe wartości sześciu jest domyślnym wyświetlaniem. Można jednak zmienić limit na dowolną liczbę. Można również ustawić wyświetlany obraz do potrzeb są liczone od dołu zamiast u góry. To narzędzie inspector obsługuje obwódkę.

### <a name="options"></a>Opcje 
- Liczba górny — wartości
- Malejąco
- Zawierają wartości null/błąd
- Pokaż obwódkę
- Użyj skali logarytmicznej


### <a name="actions"></a>Akcje 
To narzędzie inspector obsługuje filtrowanie za pomocą pasków, które mogą obejmować paski pojedynczego lub wielokrotnego wyboru. Zastosuj filtry, jak opisano wcześniej.

## <a name="box-plot"></a>Wykres skrzynkowy 
Wykres whisker pole kolumny liczbowej.

### <a name="options"></a>Opcje 
- Grupuj według kolumny

## <a name="scatter-plot"></a>Wykres punktowy
Wykres punktowy dla dwóch kolumn liczbowych. Dane są próbkowana w dół ze względu na wydajność. Rozmiar próbki mogą zostać zastąpione w opcjach.

### <a name="options"></a>Opcje  
- Kolumna osi x
- Kolumna osi y
- Rozmiar próbki
- Grupuj według kolumny


## <a name="time-series"></a>Szeregów czasowych
Wykres liniowy, przy użyciu rozpoznawania godziny na osi x.

### <a name="options"></a>Opcje
- Kolumna dat
- Kolumny liczbowej
- Rozmiar próbki


### <a name="actions"></a>Akcje
To narzędzie inspector obsługuje filtrowanie za pomocą metody wybierz kliknij i przeciągnij, aby wybrać zakres na wykresie. Po zakończeniu wyboru Zastosuj filtry, jak opisano wcześniej.


## <a name="map"></a>Mapa 
Mapa punkty, które są oznaczane na wykresach, przy założeniu, że długość i szerokość geograficzną, które zostały określone. Najpierw należy wybrać szerokości geograficznej.

### <a name="options"></a>Opcje
- Szerokość kolumny
- Kolumna szerokości geograficznej
- Klastrowanie w
- Grupuj według kolumny


### <a name="actions"></a>Akcje
To narzędzie inspector obsługuje filtrowanie za pośrednictwem punktu zaznaczenia na mapie. Naciśnij klawisz **Ctrl** klucz, a następnie kliknij i przeciągnij myszą w celu utworzenia kwadrat wokół punktów. Następnie Zastosuj filtry, jak opisano wcześniej.

Rozmiar można szybko mapę, aby pokazać tylko możliwych punktów, naciskając klawisz **E** po lewej stronie mapy.


## <a name="pattern-frequency"></a>Częstotliwość wzorzec 

To narzędzie inspector to lista wzorców w zaznaczonej kolumnie ciągu. Wzorce są reprezentowane za pomocą wyrażeń regularnych, takie jak składnia. Przenosząc kursor myszy na wzorcu przedstawiono przykładowe wartości, reprezentowane przez ten wzorzec. Wraz z wzorców jest również wyświetlany przybliżony ubezpieczenia w postaci wartości procentowej.

![Obraz inspektora wzorzec](media/data-prep-appendix4-supported-inspectors/PatternInspectorProductNumber.png)

### <a name="options"></a>Opcje
- Liczba górny — wartości
- Malejąco
- Pokaż obwódkę

### <a name="actions"></a>Akcje
To narzędzie inspector obsługuje filtrowanie na podstawie wzorców wyświetlane. Naciśnij klawisz **Ctrl** klucza, a następnie wybierz wypełnionymi paskami w Inspektorze wzorca. Następnie Zastosuj filtry, jak opisano wcześniej. W wyniku acion użytkownika zostanie dodany do kroku filtru zaawansowanego. Można wyświetlić i zmodyfikować wygenerowany kod języka Python za pomocą wywołania opcji Edytuj krok filtru zaawansowanego.
