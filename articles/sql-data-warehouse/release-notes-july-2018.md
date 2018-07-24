---
title: Usługa Azure SQL Data Warehouse — informacje o wersji czerwca 2018 r. | Dokumentacja firmy Microsoft
description: Informacje o wersji dla usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 5f4d39c6aa1a5c2c30e84fbf26535fe3ee7799a6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216518"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Co nowego w usłudze Azure SQL Data Warehouse? Lipca 2018 r.
Usługa Azure SQL Data Warehouse odbiera ulepszenia stale. W tym artykule opisano nowe funkcje i zmiany, które zostały wprowadzone w lipca 2018 r.


## <a name="finer-granularity-for-cross-region-and-server-restores"></a>Bardziej szczegółowy dla wielu regionów i serwera
Możesz teraz przywrócić różnych regionach i serwerami przy użyciu dowolnego punktu przywracania zamiast zaznaczania geograficznie nadmiarowych kopii zapasowych, które są wykonywane co 24 godziny. Krzyżowe przywracania region i serwera są obsługiwane w przypadku obu punktów przywracania zdefiniowane przez użytkownika lub automatyczne, włączenie bardziej szczegółowy dla dodatkowej ochrony danych. Za pomocą więcej dostępnych punktów przywracania możesz mieć pewność, że magazyn danych będzie logicznie spójnego Przywracanie między regionami.

![Przywracanie usługi Azure SQL Data Warehouse Command -](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![opcje przywracania — usługa Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

Aby uzyskać więcej informacji, zobacz [Accelerated i punkty przywracania elastyczne](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) wpis w blogu.

## <a name="20-minute-restorations"></a>Operacje przywracania 20 minut
Usługa SQL Data Warehouse oferuje teraz Przywracanie każdy magazyn danych jest **mniej niż 20 minut** w tym samym regionie, niezależnie od rozmiaru bazy danych. Czas przywracania dotyczy zarówno przywracania jest w tej samej lub różnych serwera logicznego w ramach tego samego regionu. Ponadto proces migawki został ulepszony, aby zmniejszyć ilość czasu, jaki zajmuje utworzenie punktu przywracania. W dolnym jest ulepszanie poziomy wydajności (niższe ustawień jednostek DWU) *redukcji 2 x* w czasie tworzenia migawki.

Aby uzyskać więcej informacji, zobacz [Accelerated i punkty przywracania elastyczne](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) wpis w blogu.

## <a name="custom-restoration-configurations"></a>Konfiguracje niestandardowe przywracania
Podczas przywracania w witrynie Azure portal, można teraz zmienić poziom wydajności (magazynu danych DWU). Możesz również przywrócić uaktualnionego Gen2 data warehouse. Przywracając wystąpienia Gen 2, możesz teraz ocenić wpływ Gen2 przed [Uaktualnianie magazynu danych Gen1](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation).

![Konfiguracja przywracania niestandardowe — Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
[Sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) procedury składowanej jest często używana przez narzędzia można uzyskać metadanych o parametrach w zadaniu wsadowym języka Transact-SQL. Procedura zwraca jeden wiersz dla każdego parametru w usłudze batch z typem ustalonym informacje dla tego parametru. 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

Zestaw wyników zawiera metadane dotyczące `@id` parametr (wyniki częściowe pokazano)
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```