---
title: Podsumowywanie danych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Sumuj dane w usłudze Azure Machine Learning do generowania podstawowego raportu statystyk opisowych dla kolumn w zestawie danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: b0def12582dd3795e1b17334406e28d77c3c5656
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477446"
---
# <a name="summarize-data"></a>Podsumowywanie danych

W tym artykule opisano moduł projektanta usługi Azure Machine Learning (wersja zapoznawcza).

Moduł Sumuj dane służy do tworzenia zestawu standardowych miar statystycznych opisujących każdą kolumnę w tabeli wprowadzania.

Statystyki podsumowania są przydatne, gdy chcesz zrozumieć cechy pełnego zestawu danych. Na przykład może być konieczne:

- Ile brakujących wartości znajduje się w każdej kolumnie?
- Ile unikatowych wartości znajduje się w kolumnie obiektowej?
- Jaka jest średnia i odchylenie standardowe dla każdej kolumny?

Moduł oblicza ważne wyniki dla każdej kolumny i zwraca wiersz statystyk podsumowania dla każdej zmiennej (kolumny danych) dostarczonej jako dane wejściowe.

## <a name="how-to-configure-summarize-data"></a>Jak skonfigurować sumuj dane  

1. Dodaj **moduł Sumuj dane** do potoku. Ten moduł można znaleźć w kategorii **Funkcje statystyczne** w projektancie.

1. Połącz zestaw danych, dla którego chcesz wygenerować raport.

    Jeśli chcesz raportować tylko niektóre kolumny, użyj wybierz kolumny w module [Zestaw danych,](select-columns-in-dataset.md) aby rzutować podzbiór kolumn do pracy.

1. Nie są wymagane żadne dodatkowe parametry. Domyślnie moduł analizuje wszystkie kolumny, które są dostarczane jako dane wejściowe i w zależności od typu wartości w kolumnach, wyprowadza odpowiedni zestaw statystyk, zgodnie z opisem w sekcji [Wyniki.](#results)

1. Prześlij potok.

## <a name="results"></a>Wyniki

Raport z modułu może zawierać następujące statystyki. 

|Nazwa kolumny|Opis|
|------|------|  
|**Funkcja**|Nazwa kolumny|
|**Liczba**|Liczba wszystkich wierszy|
|**Unikatowa liczba wartości**|Liczba unikatowych wartości w kolumnie|
|**Liczba brakujących wartości**|Liczba unikatowych wartości w kolumnie|
|**Min**|Najniższa wartość w kolumnie|  
|**Max**|Najwyższa wartość w kolumnie|
|**Średnia**|Średnia wszystkich wartości kolumn|
|**Średnie odchylenie**|Średnie odchylenie wartości kolumn|
|**1. kwartyl**|Wartość w pierwszym kwartylu|
|**Mediana**|Mediana wartości kolumny|
|**3. kwartyl**|Wartość w trzecim kwartylu|
|**Tryb**|Tryb wartości kolumn|
|**Zakres**|Liczba całkowita reprezentująca liczbę wartości między wartościami maksymalnymi i minimalnymi|
|**Odchylenie próbki**|Odchylenie dla kolumny; patrz Uwaga|
|**Odchylenie standardowe próbki**|Odchylenie standardowe dla kolumny; patrz Uwaga|
|**Pochylenie próbki**|Skośność dla kolumny; patrz Uwaga|
|**Kurtoza próbki**|Kurtoza dla kolumny; patrz Uwaga|
|**P0,5**|0,5% percentyli|
|**P1**|1% percentyli|
|**P5**|5% percentyl|
|**P95**|95% percentyl|
|**P99.5**|99,5% percentyli |

## <a name="technical-notes"></a>Uwagi techniczne

- W przypadku kolumn nielicznych obliczane są tylko wartości count, unique value count i missing value count. W przypadku innych statystyk zwracana jest wartość null.

- Kolumny zawierające wartości logiczne są przetwarzane przy użyciu następujących reguł:

    - Podczas obliczania Min, logiczne I jest stosowany.
    
    - Podczas obliczania max, logiczne OR jest stosowany
    
    - Podczas obliczania zakresu moduł najpierw sprawdza, czy liczba unikatowych wartości w kolumnie jest równa 2.
    
    - Podczas obliczania wszelkich statystyk, które wymagają obliczeń zmiennoprzecinkowych, wartości True są traktowane jako 1.0, a wartości False są traktowane jako 0.0.

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning.  
