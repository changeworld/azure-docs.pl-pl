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
ms.openlocfilehash: a655ada93cd9db9db95295d445c0b4f27d772148
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721204"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Używanie Azure Stream Analytics z usługą Azure Synapse Analytics
Azure Stream Analytics to w pełni zarządzana usługa dostarczająca wysoce opóźnienia i skalowalne, skomplikowane przetwarzanie złożone przetwarzania zdarzeń na danych przesyłanych strumieniowo w chmurze. Podstawowe informacje można uzyskać, odczytując [wprowadzenie do Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md). Następnie można dowiedzieć się, jak utworzyć kompleksowe rozwiązanie przy użyciu Stream Analytics, postępując zgodnie z samouczkiem [Rozpoczynanie pracy przy użyciu Azure Stream Analytics](../stream-analytics/stream-analytics-real-time-fraud-detection.md) .

W tym artykule dowiesz się, jak korzystać z bazy danych magazynu danych jako ujścia danych wyjściowych dla zadań Stream Analytics.

## <a name="prerequisites"></a>Wymagania wstępne
Najpierw wykonaj następujące kroki w samouczku wprowadzenie do [korzystania z Azure Stream Analytics](../stream-analytics/stream-analytics-real-time-fraud-detection.md) .  

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

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png)

* *Nazwa użytkownika*: Określ nazwę użytkownika konta, które ma uprawnienia do zapisu w bazie danych.
* *Hasło*: Podaj hasło do określonego konta użytkownika.
* *Tabela*: Określ nazwę docelowej tabeli w bazie danych.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png)

### <a name="step-4"></a>Krok 4
Kliknij przycisk wyboru, aby dodać dane wyjściowe zadania i sprawdzić, czy Stream Analytics może pomyślnie nawiązać połączenie z bazą danych.

Po pomyślnym nawiązaniu połączenia z bazą danych zobaczysz powiadomienie w portalu. Możesz kliknąć pozycję Testuj, aby przetestować połączenie z bazą danych.

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z omówieniem integracji, zobacz [integrowanie innych usług](sql-data-warehouse-overview-integrate.md).
Aby uzyskać więcej porad programistycznych, zobacz [decyzje projektowe i techniki kodowania dla hurtowni danych](sql-data-warehouse-overview-develop.md).

