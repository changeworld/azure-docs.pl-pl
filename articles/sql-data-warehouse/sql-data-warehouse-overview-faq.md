---
title: Azure SQL Data Warehouse — często zadawane pytania | Dokumentacja firmy Microsoft
description: W tym artykule wymieniono się często zadawane pytania dotyczące usługi Azure SQL Data Warehouse, klientów i deweloperów
services: sql-data-warehouse
author: acomet
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: acomet
ms.reviewer: igorstan
ms.openlocfilehash: 7a570dfa6e0a2812a8dd3a25b8903c70fe07befc
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971707"
---
# <a name="sql-data-warehouse-frequently-asked-questions"></a>SQL Data Warehouse często zadawane pytania

## <a name="general"></a>Ogólne

PYTANIA I ODPOWIEDZI. Co oferuje magazyn danych SQL dla danych zabezpieczeń?

A. SQL data Warehouse oferuje kilka rozwiązań do ochrony danych, takich jak funkcja TDE i inspekcji. Aby uzyskać więcej informacji, zobacz [Bezpieczeństwo].

PYTANIA I ODPOWIEDZI. Gdzie mogę znaleźć się, jakie normy prawnych lub biznesowych SQL DW zgodne z?

A. Odwiedź stronę [Zgodność firmy Microsoft] strony dla różnych ofert zgodności według produktu, takich jak SOC i ISO. Najpierw wybierz według tytułu zgodności, a następnie rozwiń węzeł platformy Azure w sekcji Microsoft cloud w zakresie usług z prawej strony, aby zobaczyć, jakie usługi są usługi Azure services są zgodne.

PYTANIA I ODPOWIEDZI. Czy można połączyć usługi Power BI?

A. Tak! Chociaż usługi Power BI obsługuje zapytania bezpośredniego w usłudze SQL DW, nie jest przeznaczony dla dużej liczby użytkowników lub danych w czasie rzeczywistym. Do użytku produkcyjnego usługi Power BI firma Microsoft zaleca, przy użyciu usługi Power BI na podstawie usług Azure Analysis Services lub analizy usługi IaaS. 

PYTANIA I ODPOWIEDZI. Jakie są limity pojemności magazynu danych SQL?

A. Zobacz nasz bieżący [limitów pojemności] strony. 

PYTANIA I ODPOWIEDZI. Dlaczego moja skalowania/wstrzymywanie i wznawianie trwa tak długo?

A. Różne czynniki mogą mieć wpływ na czas operacji zarządzania obliczeń. Typowy przypadek długotrwałych operacji jest wycofywania transakcji. Po rozpoczęciu operacji skalowania lub Wstrzymaj są zablokowane wszystkie przychodzące sesje i zapytania są opróżniane. Aby opuścić system stabilna, transakcje zostać wycofana ponownie, zanim operacja może zostać rozpoczęte. Większa liczba i większe rozmiaru dziennika transakcji, zostanie zablokowany im dłuższy operacji przywracania systemu do stanu stabilnego.

## <a name="user-support"></a>Pomoc dla użytkowników

PYTANIA I ODPOWIEDZI. Jest masz wniosek dotyczący funkcji, gdzie jest przesyłać?

A. Jeśli masz wniosek dotyczący funkcji, prześlij ją na naszych [UserVoice] strony

PYTANIA I ODPOWIEDZI. Jak mogę wykonać x?

A. Aby uzyskać pomoc w rozwijaniu z usługą SQL Data Warehouse, możesz zadawać pytania na naszych [Stack Overflow] strony. 

PYTANIA I ODPOWIEDZI. Jak przesłać bilet pomocy technicznej?

A. [Biletami pomocy technicznej] można zgłosić za pomocą witryny Azure portal.

## <a name="sql-languagefeature-support"></a>Obsługa funkcji/języka SQL 

PYTANIA I ODPOWIEDZI. Jakie typy danych obsługuje usługa SQL Data Warehouse?

A. Zobacz SQL Data Warehouse [typy danych].

PYTANIA I ODPOWIEDZI. Jakie funkcje tabeli są obsługiwane?

A. Usługa SQL Data Warehouse obsługuje wiele funkcji, niektóre nie są obsługiwane i są udokumentowane w artykule [Funkcje nieobsługiwaną tabelę].

## <a name="tooling-and-administration"></a>Narzędzia i Administracja

PYTANIA I ODPOWIEDZI. Projekty bazy danych są obsługiwane w programie Visual Studio.

A. Obecnie nie obsługujemy projektów bazy danych w programie Visual Studio dla usługi SQL Data Warehouse. Jeśli chcesz oddanych głosów można pobrać z tej funkcji, odwiedź nasz User Voice [Żądania funkcji projektów bazy danych].

PYTANIA I ODPOWIEDZI. Usługa SQL Data Warehouse obsługuje interfejsy API REST?

A. Tak. Większość funkcji REST, które mogą być używane z bazą danych SQL jest również dostępna w usłudze SQL Data Warehouse. Można znaleźć informacje o interfejsu API w ramach strony dokumentacji REST lub [MSDN].


## <a name="loading"></a>Ładowanie

PYTANIA I ODPOWIEDZI. Jakie sterowniki klienta są obsługiwane?

A. Sterownik obsługujący magazyn danych znajduje się na [Parametry połączeń] strony

P: jakie formaty plików są obsługiwane przez aparat PolyBase w usłudze SQL Data Warehouse?

A: Orc, RC, Parquet i prostego tekstu rozdzielanego

P: co można podłączyć do z magazynu danych SQL przy użyciu technologii PolyBase? 

Odp.: [Azure Data Lake Store] i [usługi Azure Storage blob]

P: czy przekazywanie obliczeń możliwe podczas nawiązywania połączenia z obiektów blob usługi Azure Storage lub Azure Data Lake Store? 

Odp.: nie PolyBase magazynu danych SQL interakcja składników magazynu. 

P: czy można połączyć z usługą HDI?

Odp.: HDI służy WASB lub ADLS warstwy system plików HDFS. Jeśli masz jako warstwa systemu plików HDFS, można załadować tych danych do usługi SQL DW. Jednak nie można wygenerować obliczeń przekazywania do wystąpienia usługi HDI. 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji w usłudze SQL Data Warehouse jako całości, zobacz nasze [Omówienie] strony.


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Parametry połączeń]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Biletami pomocy technicznej]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Bezpieczeństwo]: ./sql-data-warehouse-overview-manage-security.md
[Zgodność firmy Microsoft]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[limitów pojemności]: ./sql-data-warehouse-service-capacity-limits.md
[typy danych]: ./sql-data-warehouse-tables-data-types.md
[Funkcje nieobsługiwaną tabelę]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Usługi Azure Storage blob]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Żądania funkcji projektów bazy danych]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[Omówienie]: ./sql-data-warehouse-overview-faq.md
