---
title: Użyj Azure Stream Analytics
description: Porady dotyczące używania Azure Stream Analytics z Azure SQL Data Warehouse do tworzenia rozwiązań.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 63803f3ac477e48d8d1c14a72e2ee9b9d4860047
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685719"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Użyj Azure Stream Analytics z SQL Data Warehouse
Azure Stream Analytics to w pełni zarządzana usługa dostarczająca wysoce opóźnienia i skalowalne, skomplikowane przetwarzanie złożone przetwarzania zdarzeń na danych przesyłanych strumieniowo w chmurze. Podstawowe informacje można uzyskać, odczytując [wprowadzenie do Azure Stream Analytics][Introduction to Azure Stream Analytics]. Następnie można dowiedzieć się, jak utworzyć kompleksowe rozwiązanie przy użyciu Stream Analytics, postępując zgodnie z samouczkiem [Rozpoczynanie pracy przy użyciu Azure Stream Analytics][Get started using Azure Stream Analytics] .

W tym artykule dowiesz się, jak korzystać z bazy danych Azure SQL Data Warehouse jako ujścia danych wyjściowych dla Stream Analytics zadań.

## <a name="prerequisites"></a>Wymagania wstępne
Najpierw wykonaj następujące kroki w samouczku wprowadzenie do [korzystania z Azure Stream Analytics][Get started using Azure Stream Analytics] .  

1. Tworzenie danych wejściowych centrum zdarzeń
2. Konfigurowanie i uruchamianie aplikacji generatora zdarzeń
3. Inicjowanie obsługi zadania Stream Analytics
4. Określ dane wejściowe i zapytania dotyczące zadania

Następnie Utwórz bazę danych Azure SQL Data Warehouse

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Określ dane wyjściowe zadania: Azure SQL Data Warehouse Database
### <a name="step-1"></a>Krok 1
W Stream Analytics zadania kliknij pozycję **dane wyjściowe** w górnej części strony, a następnie kliknij przycisk **Dodaj**.

### <a name="step-2"></a>Krok 2
Wybierz SQL Database.

### <a name="step-3"></a>Krok 3
Wprowadź następujące wartości na następnej stronie:

* *Alias wyjściowy*: wprowadź przyjazną nazwę dla tego wyjścia zadania.
* *Subskrypcja*:
  * Jeśli SQL Data Warehouse baza danych znajduje się w tej samej subskrypcji co zadanie Stream Analytics, wybierz opcję Użyj SQL Database z bieżącej subskrypcji.
  * Jeśli baza danych znajduje się w innej subskrypcji, wybierz pozycję Użyj SQL Database z innej subskrypcji.
* *Baza danych*: Określ nazwę docelowej bazy danych.
* *Nazwa serwera*: Określ nazwę serwera dla bazy danych, którą właśnie określisz. Możesz użyć Azure Portal, aby je znaleźć.

![][server-name]

* *Nazwa użytkownika*: Określ nazwę użytkownika konta, które ma uprawnienia do zapisu w bazie danych.
* *Hasło*: Podaj hasło do określonego konta użytkownika.
* *Tabela*: Określ nazwę docelowej tabeli w bazie danych.

![][add-database]

### <a name="step-4"></a>Krok 4
Kliknij przycisk wyboru, aby dodać dane wyjściowe zadania i sprawdzić, czy Stream Analytics może pomyślnie nawiązać połączenie z bazą danych.

Po pomyślnym nawiązaniu połączenia z bazą danych zobaczysz powiadomienie w portalu. Możesz kliknąć pozycję Testuj, aby przetestować połączenie z bazą danych.

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z omówieniem integracji, zobacz [Omówienie integracji SQL Data Warehouse][SQL Data Warehouse integration overview].

Więcej porad programistycznych znajdziesz w artykule [Omówienie programowania w usłudze SQL Data Warehouse][SQL Data Warehouse development overview].

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
