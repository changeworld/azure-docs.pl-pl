---
title: Przyrostowe kopiowanie danych przy użyciu usługi Azure Data Factory | Microsoft Docs
description: W tych samouczkach przedstawiono sposób przyrostowego kopiowania danych ze źródłowego magazynu danych do docelowego magazynu danych. W pierwszym z nich opisane jest kopiowanie danych z jednej tabeli.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: 8097ee9ccf8efe5f4bba00110578d29f4f92ac14
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020898"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Przyrostowe ładowanie danych ze źródłowego do docelowego magazynu danych

W rozwiązaniu integracji danych przyrostowe (lub różnicowe) ładowanie danych po początkowym pełnym ładowaniu danych to powszechnie używany scenariusz. W samouczkach w tej sekcji opisano różne sposoby przyrostowego ładowania danych przy użyciu usługi Azure Data Factory.

## <a name="delta-data-loading-by-using-a-watermark"></a>Ładowanie danych różnicowych przy użyciu limitu
W takim przypadku należy zdefiniować limit w źródłowej bazie danych. Limit to kolumna, która zawiera znacznik czasu ostatniej aktualizacji lub klucz o zwiększającej się wartości. Rozwiązanie ładowania różnicowego służy do ładowania zmienionych danych między starą a nową wartością limitu. Na poniższym diagramie przedstawiono przepływ pracy dla tej metody: 

![Przepływ pracy dotyczący używania limitu](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Aby uzyskać instrukcje krok po kroku, zobacz następujące samouczki: 

- [Przyrostowe kopiowanie danych z jednej tabeli w bazie danych Azure SQL Database do magazynu Azure Blob Storage](tutorial-incremental-copy-powershell.md)
- [Przyrostowe kopiowanie danych z wielu tabel na lokalnym serwerze SQL Server do bazy danych Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)

## <a name="delta-data-loading-by-using-the-change-tracking-technology"></a>Ładowanie danych różnicowych przy użyciu technologii Change Tracking
Technologia Change Tracking to rozwiązanie o małych wymaganiach w programie SQL Server i bazie danych Azure SQL Database, które zapewnia wydajny mechanizm śledzenia zmian na potrzeby aplikacji. Dzięki temu w aplikacji można łatwo zidentyfikować dane, które zostały wstawione, zaktualizowane lub usunięte. 

Na poniższym diagramie przedstawiono przepływ pracy dla tej metody:

![Przepływ pracy dotyczący używania technologii Change Tracking](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Aby uzyskać instrukcje krok po kroku, zobacz następujące samouczki: <br/>
[Przyrostowe kopiowanie danych z bazy danych Azure SQL Database do magazynu Azure Blob Storage z użyciem technologii Change Tracking](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="next-steps"></a>Następne kroki
Przejdź do następującego samouczka: 

> [!div class="nextstepaction"]
>[Przyrostowe kopiowanie danych z jednej tabeli w bazie danych Azure SQL Database do magazynu Azure Blob Storage](tutorial-incremental-copy-powershell.md)
