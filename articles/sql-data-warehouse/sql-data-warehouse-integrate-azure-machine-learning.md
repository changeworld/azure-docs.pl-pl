---
title: "Uczenie maszynowe Azure za pomocą usługi SQL Data Warehouse | Dokumentacja firmy Microsoft"
description: "Samouczek korzystania z usługi Azure Machine Learning z usługą Azure SQL Data Warehouse w celu opracowywania rozwiązań."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: barbkess
editor: 
ms.assetid: ac6bc731-6add-47a9-b3fe-68996e656f4d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: c19860c6b5b1c15d1e29ddc67f9cf9ad4618725b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-machine-learning-with-sql-data-warehouse"></a>Użyj usługi Azure Machine Learning z usługą Magazyn danych SQL
Usługa Azure Machine Learning jest usługą analizy predykcyjnej w pełni zarządzana, która służy do tworzenia modeli predykcyjnych względem danych w magazynie danych SQL, a następnie opublikować jako gotowe do użycia usług sieci web. Możesz nauczyć się podstaw analizy predykcyjnej i uczenia maszynowego, odczytując [wprowadzenie do usługi Machine Learning na platformie Azure][Introduction to Machine Learning on Azure].  Następnie Dowiedz się jak tworzenie, uczenie, wynik i testowanie machine learning model przy użyciu [tworzenie eksperymentu samouczek][Create experiment tutorial].

W tym artykule dowiesz jak to zrobić, używając następujących [Azure Machine Learning Studio][Azure Machine Learning Studio]:

* Odczytywanie danych z bazy danych do tworzenia, nauczenia i score model predykcyjny
* Zapisu danych do bazy danych

## <a name="read-data-from-sql-data-warehouse"></a>Odczyt danych z magazynu danych SQL
Firma Microsoft będzie odczytywać dane z tabeli produktu w bazie danych AdventureWorksDW.

### <a name="step-1"></a>Krok 1
Uruchom nowy eksperyment, klikając + nowy u dołu okna Machine Learning Studio, wybierz EKSPERYMENTU, a następnie wybierz, pusty eksperymentu. Wybierz domyślną nazwę eksperymentu w górnej części obszaru roboczego i zmień jego nazwę, wpisując tekst opisowy, na przykład Prognozowanie cen roweru.

### <a name="step-2"></a>Krok 2
Poszukaj moduł czytnika w palecie zestawów danych i moduły z lewej strony obszaru roboczego eksperymentu. Przeciągnij moduł do obszaru roboczego eksperymentu.
![][drag_reader]

### <a name="step-3"></a>Krok 3
Wybierz moduł czytnika i Wypełnij w okienku właściwości.

1. Wybierz bazę danych Azure SQL jako źródła danych.
2. Nazwa serwera bazy danych: wpisz nazwę serwera. Można użyć [portalu Azure] [ Azure portal] Aby znaleźć tę wartość.

![][server_name]

1. Nazwa bazy danych: wpisz nazwę bazy danych na serwerze, który został właśnie zostało określone.
2. Nazwa konta użytkownika serwera: Wprowadź nazwę użytkownika konta, które ma uprawnienia dostępu do bazy danych.
3. Hasło konta użytkownika serwera: Podaj hasło dla określonego konta użytkownika.
4. Dowolny certyfikat serwera Zaakceptuj: Użyj tej opcji, które (mniej bezpieczna opcja), jeśli chcesz pominąć przeglądanie certyfikatu witryny, przed przeczytaniem danych.
5. Zapytanie bazy danych: wprowadź instrukcję SQL opisujący danych, który chcesz odczytać. W takim przypadku firma Microsoft będzie odczytywać dane z tabel produktu za pomocą następującej kwerendy.

```SQL
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### <a name="step-4"></a>Krok 4
1. Uruchom eksperyment, klikając polecenie Uruchom na kanwie eksperymentu.
2. Po zakończeniu eksperymentu, moduł czytnika ma zielony znacznik wyboru, aby wskazać, czy zostało zakończone pomyślnie. Spójrz również Zakończ bieżący stan w prawym górnym rogu.

![][run]

1. Aby wyświetlić zaimportowane danych, kliknij port wyjściowy w dolnej części samochodów zestawu danych i wybierz wizualizacja.

## <a name="create-train-and-score-a-model"></a>Tworzenie, uczenie i score model
Teraz można używać tego elementu dataset do:

* Tworzenie modelu: przetwarzanie danych i zdefiniować funkcji
* Nauczenia modelu: Wybieranie i stosowanie algorytmu uczenia
* Generowanie wyników i testowanie modelu: przewidywanie nowych cen rowerów

![][model]

Aby dowiedzieć się więcej na temat sposobu tworzenia, uczenia, generowanie wyników i testowanie Użyj modelu uczenia maszynowego [tworzenie eksperymentu samouczek][Create experiment tutorial].

## <a name="write-data-to-azure-sql-data-warehouse"></a>Zapisu danych do usługi Azure SQL Data Warehouse
Firma Microsoft będzie zapisywać zestaw wyników do tabeli ProductPriceForecast w bazie danych AdventureWorksDW.

### <a name="step-1"></a>Krok 1
Poszukaj moduł zapisu w palecie zestawów danych i moduły z lewej strony obszaru roboczego eksperymentu. Przeciągnij moduł do obszaru roboczego eksperymentu.

![][drag_writer]

### <a name="step-2"></a>Krok 2
Wybierz moduł, zapisywania i Wypełnij w okienku właściwości.

1. Wybierz bazę danych Azure SQL jako miejsce docelowe danych.
2. Nazwa serwera bazy danych: wpisz nazwę serwera. Można użyć [portalu Azure] [ Azure portal] Aby znaleźć tę wartość.
3. Nazwa bazy danych: wpisz nazwę bazy danych na serwerze, który został właśnie zostało określone.
4. Nazwa konta użytkownika serwera: Wprowadź nazwę użytkownika konta, które ma uprawnienia do zapisu dla bazy danych.
5. Hasło konta użytkownika serwera: Podaj hasło dla określonego konta użytkownika.
6. Zaakceptuj wszystkie certyfikatu serwera (niebezpieczne): Wybierz tę opcję, jeśli nie chcesz wyświetlić certyfikat.
7. Rozdzielana przecinkami lista kolumn do zapisania: Podaj listę zestawu danych lub wynik kolumn, które ma zostać wyprowadzony.
8. Nazwa tabeli danych: Określ nazwę tabeli danych.
9. Rozdzielana przecinkami lista kolumn elementu datatable: określ nazwy kolumny, które mają być używane w nowej tabeli. Nazwy kolumn mogą być inne niż w źródłowym zestawie danych, ale musi zawierać taką samą liczbę kolumn w tym miejscu zdefiniowane dla tabeli wyjściowej.
10. Liczba wierszy, zapisany dla operacji SQL Azure: można skonfigurować liczbę wierszy, które są zapisywane w bazie danych SQL w ramach jednej operacji.

![][writer_properties]

### <a name="step-3"></a>Krok 3
1. Uruchom eksperyment, klikając polecenie Uruchom na kanwie eksperymentu.
2. Po zakończeniu eksperymentu, wszystkie moduły będą mieć zielony znacznik wyboru, aby wskazać, że zostały ukończone pomyślnie.

## <a name="next-steps"></a>Następne kroki
Więcej porad dla deweloperów znajduje się w artykule [Omówienie programowania w usłudze SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Create experiment tutorial]: https://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/
[Introduction to machine learning on Azure]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[Azure Machine Learning Studio]: https://studio.azureml.net/Home
[Azure portal]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/
