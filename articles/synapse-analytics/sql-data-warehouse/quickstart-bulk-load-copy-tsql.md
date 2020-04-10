---
title: 'Szybki start: zbiorcze ładowanie danych przy użyciu pojedynczej instrukcji T-SQL'
description: Dane obciążenia zbiorczego przy użyciu instrukcji COPY
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/08/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d03c7d72a0adf02959badac758f94e47fd81de5c
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992228"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Szybki start: dane ładowania zbiorczego przy użyciu instrukcji COPY

W tym przewodniku Szybki start zbiorcze załadować dane do puli SQL przy użyciu proste i elastyczne [COPY instrukcji](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) dla pozyskiwania danych o wysokiej przepływności. Instrukcja COPY jest zalecanym narzędziem ładowania, ponieważ umożliwia bezproblemowe i elastyczne ładowanie danych, zapewniając funkcjonalność:

- Zezwalaj użytkownikom o niższych uprawnieniach na ładowanie bez konieczności stosowania ścisłych uprawnień CONTROL w magazynie danych
- Wykorzystaj tylko jedną instrukcję T-SQL bez konieczności tworzenia dodatkowych obiektów bazy danych
- Korzystanie z drobniejszego modelu uprawnień bez ujawniania kluczy konta magazynu przy użyciu sygnatur dostępu współdzielonego (SAS)
- Określ inne konto magazynu dla lokalizacji ERRORFILE (REJECTED_ROW_LOCATION)
- Dostosowywanie wartości domyślnych dla każdej kolumny docelowej i określanie pól danych źródłowych do załadowania do określonych kolumn docelowych
- Określanie niestandardowego terminatora wiersza dla plików CSV
- Ograniczniki ciągów ucieczki, pól i wierszy dla plików CSV
- Korzystanie z formatów SQL Server Date dla plików CSV
- Określanie symboli wieloznacznych i wielu plików w ścieżce lokalizacji magazynu

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start zakłada, że masz już pulę SQL. Jeśli pula SQL nie została utworzona, użyj przewodnika Szybki start [utwórz i połącz — portal.](create-data-warehouse-portal.md)

## <a name="create-the-target-table"></a>Tworzenie tabeli docelowej

W tym przykładzie będziemy wczytywania danych z zestawu danych taksówki w Nowym Jorku. załadujemy tabelę o nazwie Trip, która reprezentuje przejazdy taksówką w ciągu jednego roku. Aby utworzyć tabelę, uruchom następujące czynności:

```sql
CREATE TABLE [dbo].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

## <a name="run-the-copy-statement"></a>Uruchamianie instrukcji COPY

Uruchom następującą instrukcję COPY, która będzie ładować dane z konta magazynu obiektów blob platformy Azure do tabeli Trip.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>Monitorowanie obciążenia

Sprawdź, czy obciążenie jest postęp, okresowo uruchamiając następującą kwerendę:

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać najlepsze rozwiązania dotyczące ładowania danych, zobacz [Najważniejsze wskazówki dotyczące ładowania danych](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data).
- Aby uzyskać informacje na temat zarządzania zasobami dla obciążeń danych, zobacz [Izolacja obciążenia](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql). 
