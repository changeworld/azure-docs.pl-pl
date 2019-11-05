---
title: Podsumowywanie danych
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak za pomocą modułu podsumowania danych w usłudze Azure Machine Learning wygenerować podstawowy raport statystyk opisowych dla kolumn w zestawie danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 503eb533b83cbeedcc16b73a9c1dbe821a4d4d94
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492574"
---
# <a name="summarize-data"></a>Podsumowywanie danych

W tym artykule opisano moduł programu Azure Machine Learning Designer (wersja zapoznawcza).

Moduł podsumowujące dane służy do tworzenia zestawu standardowych miar statystycznych, które opisują poszczególne kolumny w tabeli wejściowej.

Statystyki podsumowujące są przydatne, gdy chcesz zrozumieć charakterystykę kompletnego zestawu danych. Na przykład może być konieczne poznanie:

- Ile brakujących wartości znajduje się w każdej kolumnie?
- Ile unikatowych wartości znajduje się w kolumnie funkcji?
- Co to jest średnie i odchylenie standardowe dla każdej kolumny?

Moduł oblicza ważne wyniki dla każdej kolumny i zwraca wiersz statystyk podsumowujących dla każdej zmiennej (kolumny danych) podanej jako dane wejściowe.

## <a name="how-to-configure-summarize-data"></a>Jak skonfigurować dane podsumowujące  

1. Dodaj moduł **podsumowujące dane** do potoku. Ten moduł można znaleźć w kategorii **funkcje statystyczne** w projektancie.

1. Połącz zestaw danych, dla którego chcesz wygenerować raport.

    Jeśli chcesz utworzyć raport dotyczący tylko niektórych kolumn, użyj modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych](select-columns-in-dataset.md) ) do zaprojektowania podzestawu kolumn, z którym chcesz współpracować.

1. Nie są wymagane żadne dodatkowe parametry. Domyślnie moduł analizuje wszystkie kolumny, które są dostarczane jako dane wejściowe, i w zależności od typu wartości w kolumnach, wyprowadza odpowiedni zestaw statystyk zgodnie z opisem w sekcji [wyniki](#results) .

1. Uruchom potok lub kliknij prawym przyciskiem myszy moduł, a następnie wybierz pozycję **Uruchom wybrane**.

## <a name="results"></a>Wyniki

Raport z modułu może zawierać następujące dane statystyczne. 

|Nazwa kolumny|Opis|
|------|------|  
|**Funkcja**|Nazwa kolumny|
|**Liczbą**|Liczba wszystkich wierszy|
|**Liczba unikatowych wartości**|Liczba unikatowych wartości w kolumnie|
|**Brak liczby wartości**|Liczba unikatowych wartości w kolumnie|
|**Długości**|Najniższa wartość w kolumnie|  
|**Maksymalny**|Najwyższa wartość w kolumnie|
|**Zależność**|Średnia dla wszystkich wartości kolumn|
|**Odchylenie średnie**|Średnia odchylenia wartości kolumn|
|**1. kwartyl**|Wartość przy pierwszym kwartyl|
|**Symetri**|Wartość kolumny mediana|
|**3. kwartyl**|Wartość przy trzecim kwartyl|
|**Wyst**|Tryb wartości kolumn|
|**Zakres**|Liczba całkowita reprezentująca liczbę wartości z zakresu wartości maksymalnej i minimalnej|
|**WARIANCJA próbki**|WARIANCJA dla kolumny; Zobacz Uwaga|
|**Przykładowe odchylenie standardowe**|Odchylenie standardowe dla kolumny; Zobacz Uwaga|
|**Przykład skośności**|Skośność kolumny; Zobacz Uwaga|
|**Przykładowa kurtoza**|Kurtoza dla kolumny; Zobacz Uwaga|
|**P 0,5**|0,5% percentyl|
|**P1**|1% percentyl|
|**P5**|5% percentyl|
|**P95**|95% percentyl|
|**P 99,5**|99,5% percentyl |

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi.  
