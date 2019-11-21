---
title: 'Incrementally copy data by using Azure Data Factory '
description: W tych samouczkach przedstawiono sposób przyrostowego kopiowania danych ze źródłowego magazynu danych do docelowego magazynu danych. W pierwszym z nich opisane jest kopiowanie danych z jednej tabeli.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: 42dcd6ecc6df1c9877581d89bf22724054e305d0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217287"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Przyrostowe ładowanie danych ze źródłowego do docelowego magazynu danych

W rozwiązaniu integracji danych przyrostowe (lub różnicowe) ładowanie danych po początkowym pełnym ładowaniu danych to powszechnie używany scenariusz. W samouczkach w tej sekcji opisano różne sposoby przyrostowego ładowania danych przy użyciu usługi Azure Data Factory.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Ładowanie danych różnicowych z bazy danych przy użyciu limitu
W takim przypadku należy zdefiniować limit w źródłowej bazie danych. Limit to kolumna, która zawiera znacznik czasu ostatniej aktualizacji lub klucz o zwiększającej się wartości. Rozwiązanie ładowania różnicowego służy do ładowania zmienionych danych między starą a nową wartością limitu. Na poniższym diagramie przedstawiono przepływ pracy dla tej metody: 

![Przepływ pracy dotyczący używania limitu](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Aby uzyskać instrukcje krok po kroku, zobacz następujące samouczki: 
- [Przyrostowe kopiowanie danych z jednej tabeli w bazie danych Azure SQL Database do magazynu Azure Blob Storage](tutorial-incremental-copy-powershell.md)
- [Przyrostowe kopiowanie danych z wielu tabel na lokalnym serwerze SQL Server do bazy danych Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)

For templates, see the following:
- [Delta copy with control table](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Ładowanie danych różnicowych z bazy danych SQL przy użyciu technologii Change Tracking
Technologia Change Tracking to rozwiązanie o małych wymaganiach w programie SQL Server i bazie danych Azure SQL Database, które zapewnia wydajny mechanizm śledzenia zmian na potrzeby aplikacji. Dzięki temu w aplikacji można łatwo zidentyfikować dane, które zostały wstawione, zaktualizowane lub usunięte. 

Na poniższym diagramie przedstawiono przepływ pracy dla tej metody:

![Przepływ pracy dotyczący używania technologii Change Tracking](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Aby uzyskać instrukcje krok po kroku, zobacz następujące samouczki: <br/>
- [Przyrostowe kopiowanie danych z bazy danych Azure SQL Database do magazynu Azure Blob Storage z użyciem technologii Change Tracking](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Ładowanie nowych i zmienionych plików tylko przy użyciu parametru LastModifiedDate
You can copy the new and changed files only by using LastModifiedDate to the destination store. ADF will scan all the files from the source store, apply the file filter by their LastModifiedDate, and only copy the new and updated file since last time to the destination store.  Please be aware if you let ADF scan huge amounts of files but only copy a few files to destination, you would still expect the long duration due to file scanning is time consuming as well.   

Aby uzyskać instrukcje krok po kroku, zobacz następujące samouczki: <br/>
- [Przyrostowe kopiowanie nowych i zmienionych plików na podstawie parametru LastModifiedDate z usługi Azure Blob Storage do usługi Azure Blob Storage](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

For templates, see the following:
- [Copy new files by LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Załaduj nowe pliki tylko za pomocą partycjonowanego na podstawie czasu folderu lub nazwy pliku.
Możesz kopiować tylko nowe pliki, gdy pliki lub foldery zostały już poddane partycjonowaniu na podstawie czasu za pomocą informacji o kwancie czasu jako części nazwy pliku lub folderu (na przykład /rrrr/mm/dd/plik.csv). It is the most performant approach for incrementally loading new files. 

Aby uzyskać instrukcje krok po kroku, zobacz następujące samouczki: <br/>
- [Przyrostowe kopiowanie nowych plików na podstawie partycjonowanego na podstawie czasu folderu lub nazwy pliku z usługi Azure Blob Storage do usługi Azure Blob Storage](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>Następne kroki
Przejdź do następującego samouczka: 

> [!div class="nextstepaction"]
>[Przyrostowe kopiowanie danych z jednej tabeli w bazie danych Azure SQL Database do magazynu Azure Blob Storage](tutorial-incremental-copy-powershell.md)
