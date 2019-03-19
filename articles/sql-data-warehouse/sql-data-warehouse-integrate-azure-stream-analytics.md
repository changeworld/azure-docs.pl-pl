---
title: Użyj usługi Azure Stream Analytics z usługą SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące korzystania z usługi Azure Stream Analytics za pomocą usługi Azure SQL Data Warehouse do opracowywania rozwiązań.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 605598d1470cbb535d626c15a5e8e4e08aa4d571
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "57883818"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Użyj usługi Azure Stream Analytics z usługą SQL Data Warehouse
Usługa Azure Stream Analytics jest w pełni zarządzaną usługę, zapewniając przetwarzanie złożonych zdarzeń o małych opóźnieniach, wysokiej dostępności i skalowalności za pośrednictwem przesyłania strumieniowego danych w chmurze. Dowiedz się podstawowe informacje, zapoznając [wprowadzenie do usługi Azure Stream Analytics][Introduction to Azure Stream Analytics]. Można następnie dowiesz się, jak utworzyć rozwiązanie end-to-end za pomocą usługi Stream Analytics, postępując zgodnie z [rozpoczęcie korzystania z usługi Azure Stream Analytics] [ Get started using Azure Stream Analytics] samouczka.

W tym artykule dowiesz się, jak używać bazy danych Azure SQL Data Warehouse jako ujścia danych wyjściowych dla zadań usługi Stream Analytics.

## <a name="prerequisites"></a>Wymagania wstępne
Po pierwsze, wykonaj następujące kroki w [rozpoczęcie korzystania z usługi Azure Stream Analytics] [ Get started using Azure Stream Analytics] samouczka.  

1. Tworzenie Centrum zdarzeń wejściowych
2. Skonfigurować i uruchomić aplikację generatora zdarzeń
3. Aprowizowanie zadania usługi Stream Analytics
4. Określ dane wejściowe zadania i zapytania

Następnie utwórz bazę danych Azure SQL Data Warehouse

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Określ dane wyjściowe zadania: Usługa Azure SQL Data Warehouse bazy danych.
### <a name="step-1"></a>Krok 1
W ramach zadania usługi Stream Analytics kliknij **dane wyjściowe** w górnej części strony, a następnie kliknij przycisk **Dodaj dane wyjściowe**.

### <a name="step-2"></a>Krok 2
Wybierz bazę danych SQL, a następnie kliknij przycisk Dalej.

![][add-output]

### <a name="step-3"></a>Krok 3
Na następnej stronie, wprowadź następujące wartości:

* *Dane wyjściowe Alias*: Wprowadź przyjazną nazwę dla tych danych wyjściowych zadania.
* *Subskrypcja*:
  * Jeśli baza danych SQL Data Warehouse znajduje się w tej samej subskrypcji co zadanie usługi Stream Analytics, należy wybrać korzystaj z bazy danych SQL z bieżącej subskrypcji.
  * Jeśli baza danych znajduje się w innej subskrypcji, wybierz Korzystaj z bazy danych SQL z innej subskrypcji.
* *Baza danych*: Podaj nazwę docelowej bazy danych.
* *Nazwa serwera*: Określ nazwę serwera bazy danych, która właśnie została określona. Można użyć witryny Azure portal, aby ją znaleźć.

![][server-name]

* *Nazwa użytkownika*: Określ nazwę użytkownika konta które ma uprawnienia do bazy danych zapisu.
* *Hasło*: Podaj hasło dla określonego konta użytkownika.
* *Tabela*: Określ nazwę docelowej tabeli w bazie danych.

![][add-database]

### <a name="step-4"></a>Krok 4
Kliknij przycisk wyboru, aby dodać te dane wyjściowe zadania i sprawdź, czy usługi Stream Analytics można pomyślnie nawiązać połączenie bazy danych.

![][test-connection]

Gdy połączenie z bazą danych powiedzie się, zostanie wyświetlone powiadomienie u dołu portalu. Na dole, aby przetestować połączenie z bazą danych, możesz kliknąć Testuj połączenie.

## <a name="next-steps"></a>Kolejne kroki
Omówienie integracji, zobacz [Omówienie integracji usługa SQL Data Warehouse][SQL Data Warehouse integration overview].

Więcej porad dla deweloperów znajduje się w artykule [Omówienie programowania w usłudze SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: https://azure.microsoft.com/documentation/services/stream-analytics/
