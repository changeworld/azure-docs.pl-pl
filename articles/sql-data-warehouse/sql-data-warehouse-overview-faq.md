---
title: Azure SQL Data Warehouse — często zadawane pytania | Dokumentacja firmy Microsoft
description: W tym artykule wymieniono się często zadawane pytania dotyczące usługi Azure SQL Data Warehouse, klientów i deweloperów
services: sql-data-warehouse
author: happynicolle
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/17/2018
ms.author: nicw
ms.reviewer: igorstan
ms.openlocfilehash: c16d95ea15fc358cb81b17b42570cb35f2e8c52d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65795563"
---
# <a name="sql-data-warehouse-frequently-asked-questions"></a>SQL Data Warehouse często zadawane pytania

## <a name="general"></a>Ogólne

PYTANIE: Co oferuje magazyn danych SQL dla danych zabezpieczeń?

A. SQL data Warehouse oferuje kilka rozwiązań do ochrony danych, takich jak funkcja TDE i inspekcji. Aby uzyskać więcej informacji, zobacz [Zabezpieczenia].

PYTANIE: Gdzie mogę znaleźć się, jakie normy prawnych lub biznesowych SQL DW zgodne z?

A. Odwiedź stronę [Microsoft Compliance] strony dla różnych ofert zgodności według produktu, takich jak SOC i ISO. Najpierw wybierz według tytułu zgodności, a następnie rozwiń węzeł platformy Azure w sekcji Microsoft cloud w zakresie usług z prawej strony, aby zobaczyć, jakie usługi są usługi Azure services są zgodne.

PYTANIE: Czy można połączyć usługi Power BI?

A. Tak! Chociaż usługi Power BI obsługuje zapytania bezpośredniego w usłudze SQL DW, nie jest przeznaczony dla dużej liczby użytkowników lub danych w czasie rzeczywistym. Do użytku produkcyjnego usługi Power BI firma Microsoft zaleca, przy użyciu usługi Power BI na podstawie usług Azure Analysis Services lub analizy usługi IaaS. 

PYTANIE: Jakie są limity pojemności magazynu danych SQL?

A. Zobacz nasz bieżący [limitów pojemności] strony. 

PYTANIE: Dlaczego moja skalowania/wstrzymywanie i wznawianie trwa tak długo?

A. Różne czynniki mogą mieć wpływ na czas operacji zarządzania obliczeń. Typowy przypadek długotrwałych operacji jest wycofywania transakcji. Po rozpoczęciu operacji skalowania lub Wstrzymaj są zablokowane wszystkie przychodzące sesje i zapytania są opróżniane. Aby opuścić system stabilna, transakcje zostać wycofana ponownie, zanim operacja może zostać rozpoczęte. Większa liczba i większe rozmiaru dziennika transakcji, zostanie zablokowany im dłuższy operacji przywracania systemu do stanu stabilnego.

## <a name="user-support"></a>Pomoc dla użytkowników

PYTANIE: Jest masz wniosek dotyczący funkcji, gdzie jest przesyłać?

A. Jeśli masz wniosek dotyczący funkcji, prześlij ją na naszych [UserVoice] strony

PYTANIE: Jak mogę wykonać x?

A. Aby uzyskać pomoc w rozwijaniu z usługą SQL Data Warehouse, możesz zadawać pytania na naszych [Stack Overflow] strony. 

PYTANIE: Jak przesłać bilet pomocy technicznej?

A. [Biletami pomocy technicznej] można zgłosić za pomocą witryny Azure portal.

## <a name="sql-languagefeature-support"></a>Obsługa funkcji/języka SQL 

PYTANIE: Jakie typy danych obsługuje usługa SQL Data Warehouse?

A. Zobacz SQL Data Warehouse [typy danych].

PYTANIE: Jakie funkcje tabeli są obsługiwane?

A. Usługa SQL Data Warehouse obsługuje wiele funkcji, niektóre nie są obsługiwane i są udokumentowane w artykule [Funkcje nieobsługiwaną tabelę].

## <a name="tooling-and-administration"></a>Narzędzia i Administracja

PYTANIE: Projekty bazy danych są obsługiwane w programie Visual Studio.

A. Obecnie nie obsługujemy projektów bazy danych w programie Visual Studio dla usługi SQL Data Warehouse. Jeśli chcesz oddanych głosów można pobrać z tej funkcji, odwiedź nasz User Voice [Żądania funkcji projektów bazy danych].

PYTANIE: Usługa SQL Data Warehouse obsługuje interfejsy API REST?

A. Tak. Większość funkcji REST, które mogą być używane z bazą danych SQL jest również dostępna w usłudze SQL Data Warehouse. Można znaleźć informacje o interfejsu API w ramach strony dokumentacji REST lub [MSDN].


## <a name="loading"></a>Ładowanie

PYTANIE: Jakie sterowniki klienta są obsługiwane?

A. Sterownik obsługujący magazyn danych znajduje się na [Parametry połączeń] strony

Pyt.: Jakie formaty plików są obsługiwane przez aparat PolyBase w usłudze SQL Data Warehouse?

Odp.: Orc, RC, Parquet i prostego tekstu rozdzielanego

Pyt.: Co można podłączyć do z magazynu danych SQL przy użyciu technologii PolyBase? 

Odp.: [Azure Data Lake Store] i [Azure Storage Blobs]

Pyt.: Nawiązywanie połączenia z obiektów blob usługi Azure Storage lub Azure Data Lake Store jest możliwe obliczenie przekazywania? 

Odp.: Nie, PolyBase magazynu danych SQL interakcja składników magazynu. 

Pyt.: Można podłączyć do usługi HDI?

Odp.: HDI służy jako warstwa systemu plików HDFS WASB lub ADLS. Jeśli masz jako warstwa systemu plików HDFS, można załadować tych danych do usługi SQL DW. Jednak nie można wygenerować obliczeń przekazywania do wystąpienia usługi HDI. 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji w usłudze SQL Data Warehouse jako całości, zobacz nasze [Omówienie] strony.


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Parametry połączeń]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Biletami pomocy technicznej]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Zabezpieczenia]: ./sql-data-warehouse-overview-manage-security.md
[Microsoft Compliance]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[limitów pojemności]: ./sql-data-warehouse-service-capacity-limits.md
[typy danych]: ./sql-data-warehouse-tables-data-types.md
[Funkcje nieobsługiwaną tabelę]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Azure Storage Blobs]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Żądania funkcji projektów bazy danych]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[Omówienie]: ./sql-data-warehouse-overview-faq.md
