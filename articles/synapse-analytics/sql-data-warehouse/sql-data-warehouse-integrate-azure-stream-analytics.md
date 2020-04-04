---
title: Korzystanie z usługi Azure Stream Analytics
description: Porady dotyczące korzystania z usługi Azure Stream Analytics z magazynem danych w usłudze Azure Synapse w celu tworzenia rozwiązań w czasie rzeczywistym.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 2/5/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e281f8a1fb3959256d836134b4c59f5399deb9bd
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633297"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Korzystanie z usługi Azure Stream Analytics w usłudze Azure Synapse Analytics

Usługa Azure Stream Analytics to w pełni zarządzana usługa zapewniająca przetwarzanie zdarzeń o małym opóźnieniu, wysokiej dostępności i skalowalnym złożonym przetwarzaniu zdarzeń za pomocą danych przesyłanych strumieniowo w chmurze. Możesz poznać podstawy, czytając [wprowadzenie do usługi Azure Stream Analytics.](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Następnie można dowiedzieć się, jak utworzyć kompleksowe rozwiązanie za pomocą usługi Stream Analytics, wykonując samouczek [Wprowadzenie do usługi Azure Stream Analytics.](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

W tym artykule dowiesz się, jak używać magazynu danych jako ujścia danych dla zadań usługi Azure Stream Analytics.

## <a name="prerequisites"></a>Wymagania wstępne

* Zadanie analizy usługi Azure Stream — aby utworzyć zadanie usługi Azure Stream Analytics, wykonaj kroki opisane w samouczku [Wprowadzenie do usługi Azure Stream Analytics:](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)  

    1. Tworzenie danych wejściowych Centrum zdarzeń
    2. Konfigurowanie i uruchamianie aplikacji generatora zdarzeń
    3. Aprowizuj zadanie usługi Stream Analytics
    4. Określanie danych wejściowych i kwerendy zadania
* Magazyn danych puli SQL usługi Azure Synapse — aby utworzyć nowy magazyn danych, wykonaj kroki opisane w [przewodniku Szybki start, aby utworzyć nowy magazyn danych](create-data-warehouse-portal.md).

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>Określanie danych wyjściowych przesyłania strumieniowego, które mają być wskazywać do magazynu danych

### <a name="step-1"></a>Krok 1

W witrynie Azure portal przejdź do zadania usługi Stream Analytics i kliknij **dane wyjściowe** w menu **Topologia zadań.**

### <a name="step-2"></a>Krok 2

Kliknij przycisk **Dodaj** i wybierz z menu rozwijanego pozycję **Baza danych SQL.**

![Wybieranie bazy danych SQL](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutput.png)

### <a name="step-3"></a>Krok 3

Wprowadź następujące wartości:

* *Alias wyjściowy:* Wprowadź przyjazną nazwę dla tego zadania wyjściowego.
* *Subskrypcja*:
  * Jeśli magazyn danych znajduje się w tej samej subskrypcji co zadanie Usługi Stream Analytics, kliknij ***pozycję Wybierz bazę danych SQL z subskrypcji***.
  * Jeśli baza danych znajduje się w innej subskrypcji, kliknij ręcznie pozycję Podaj ustawienia bazy danych SQL.
* *Baza danych*: Wybierz docelową bazę danych z listy rozwijanej.
* *Nazwa użytkownika*: Określ nazwę użytkownika konta, które ma uprawnienia do zapisu bazy danych.
* *Hasło*: Podaj hasło dla określonego konta użytkownika.
* *Tabela*: Określ nazwę tabeli docelowej w bazie danych.
* kliknij przycisk **Zapisz**

![Ukończony formularz bazy danych SQL](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutputdbsettings.png)

### <a name="step-4"></a>Krok 4

Przed uruchomieniem testu należy utworzyć tabelę w magazynie danych.  Uruchom poniższy skrypt tworzenia tabeli przy użyciu programu SQL Server Management Studio (SSMS) lub wybranego narzędzia do wykonywania zapytań.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>Krok 5

W zadaniu usługi Azure portal for Stream Analytics kliknij nazwę zadania.  Kliknij przycisk ***Testuj*** w okienku ***Szczegóły wyjściowe.***

![Przycisk Testuj szczegóły](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) Outpout Gdy połączenie z bazą danych zakończy się pomyślnie, zobaczysz powiadomienie w portalu.

### <a name="step-6"></a>Krok 6

Kliknij menu ***Kwerenda*** w obszarze ***Topologia zadania*** i zmień kwerendę, aby wstawić dane do utworzonego strumienia danych.  Kliknij przycisk ***Testuj wybrane zapytanie,*** aby przetestować kwerendę.  Kliknij przycisk ***Zapisz kwerendę,*** gdy test kwerendy zakończy się pomyślnie.

![Zapisz kwerendę](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Krok 7

Uruchom zadanie usługi Azure Stream Analytics.  Kliknij przycisk ***Start*** w menu ***Przegląd.***

![Uruchamianie zadania usługi Stream Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Kliknij przycisk ***Start*** w okienku zadania początkowego.

![Kliknij przycisk Start](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem integracji, zobacz [Integrowanie innych usług](sql-data-warehouse-overview-integrate.md).
Aby uzyskać więcej wskazówek dotyczących programowania, zobacz [Projektowanie decyzji i technik kodowania dla magazynów danych.](sql-data-warehouse-overview-develop.md)
