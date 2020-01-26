---
title: Azure Synapse Analytics (dawniej SQL DW) — często zadawane pytania
description: Ten artykuł zawiera listę często zadawanych pytań dotyczących usługi Azure Synapse Analytics (dawniej SQL DW) od klientów i deweloperów
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1a4fbfa87756914f169bb20387702cac69d8736d
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759486"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure Synapse Analytics (dawniej SQL DW) — często zadawane pytania

## <a name="general"></a>Ogólne

PYTANIE: Co to jest usługa Azure Synapse?

A. Azure Synapse to nieograniczona Usługa analityczna, która łączy magazyn danych i analizę danych Big Data. Zapewnia to swobodę wykonywania zapytań dotyczących danych na Twoich warunkach, korzystając z niezależnych od serwera zasobów na żądanie lub aprowizacji. Usługa Azure Synapse łączy te dwa światy w ujednolicone środowisko umożliwiające pozyskiwanie, przygotowywanie i udostępnianie danych oraz zarządzanie nimi na potrzeby natychmiastowej analizy biznesowej oraz uczenia maszynowego. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

PYTANIE: Co się stało z usługą Azure SQL Data Warehouse?

A. Usługa Azure Synapse jest rozwijająca się Azure SQL Data Warehouse (SQL DW). Ten sam wiodący w branży magazyn danych jest poświęcony na cały nowy poziom wydajności i możliwości. Możesz nadal uruchamiać istniejące obciążenia magazynu danych w środowisku produkcyjnym za pomocą usługi Azure Synapse i automatycznie korzystać z nowych funkcji, które są dostępne w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

PYTANIE: Co to jest analiza SQL?

A. Usługa SQL Analytics odwołuje się do funkcji magazynowania danych przedsiębiorstwa, które są ogólnie dostępne w usłudze Azure Synapse. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

PYTANIE: Jak mogę rozpocząć pracę z usługą Azure Synapse?

A. Aby uzyskać więcej informacji, możesz zacząć korzystać z [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/sql-data-warehouse/) lub [skontaktować się z działem sprzedaży](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html). 

PYTANIE: Co to jest usługa Azure Synapse w celu zapewnienia bezpieczeństwa danych?

A. Usługa Azure Synapse oferuje kilka rozwiązań do ochrony danych, takich jak TDE i inspekcja. Aby uzyskać więcej informacji, zobacz [zabezpieczenia](sql-data-warehouse-overview-manage-security.md).

PYTANIE: Gdzie można dowiedzieć się, jakie standardy prawne i biznesowe są zgodne z usługą Azure Synapse?

A. Odwiedź stronę [zgodności firmy Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings) , aby uzyskać różne oferty zgodności według produktów, takich jak SOC i ISO. Najpierw wybierz pozycję według tytułu zgodności. Następnie rozwiń pozycję Azure w sekcji usługi Cloud Services w zasięgu firmy Microsoft znajdującej się po prawej stronie, aby zobaczyć, jakie usługi są zgodne z platformą Azure Synapse.

PYTANIE: Czy mogę połączyć Power BI?

A. Tak. Mimo że Power BI obsługuje bezpośrednie zapytania za pomocą usługi Azure Synapse, nie jest ona przeznaczona dla dużej liczby użytkowników ani danych w czasie rzeczywistym. Aby zwiększyć wydajność Power BI, należy rozważyć użycie Power BI na platformie Azure Analysis Services lub Analysis Service IaaS.

PYTANIE: Co to są limity pojemności usługi SQL Analytics?

A. Zobacz nasze bieżące [limity pojemności](sql-data-warehouse-service-capacity-limits.md) strony. 

PYTANIE: Dlaczego moje skalowanie/wstrzymywanie/wznawianie trwa tak długo?

A. Kilka czynników może mieć wpływ na czas operacji zarządzania obliczeniami. Typowym przypadkiem długotrwałych operacji jest wycofywanie transakcyjne. Po zainicjowaniu operacji skalowania lub wstrzymania wszystkie sesje przychodzące są blokowane, a kwerendy są opróżniane. Aby pozostawić system w stanie stabilnym, należy ponownie wykonać transakcje przed rozpoczęciem operacji. Im większa liczba i większy rozmiar dziennika transakcji, tym dłużej operacja zostanie wstrzymana, przywracając system do stanu stabilnego.

## <a name="user-support"></a>Obsługa użytkowników

PYTANIE: Mam wniosek o funkcję, gdzie go przesłać?

A. Jeśli masz żądanie dotyczące funkcji, prześlij je na naszej stronie usługi [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse)

PYTANIE: Jak mogę zrobić x?

A. Aby uzyskać pomoc dotyczącą tworzenia aplikacji za pomocą usługi Azure Synapse, możesz zadawać pytania na naszej stronie [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw) . 

PYTANIE: Jak mogę przesłać bilet pomocy technicznej?

A. [Bilety pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) mogą być zgłaszane przez Azure Portal.

## <a name="sql-languagefeature-support"></a>Obsługa języka/funkcji SQL 

PYTANIE: Jakie typy danych są obsługiwane?

A. Zobacz [typy danych](sql-data-warehouse-tables-data-types.md).

PYTANIE: Jakie funkcje tabel są obsługiwane?

A. Obsługiwane są wiele funkcji. Funkcje, które nie są obsługiwane, można znaleźć w [nieobsługiwanych funkcjach tabel](sql-data-warehouse-tables-data-types.md).

## <a name="tooling-and-administration"></a>Narzędzia i administracja

PYTANIE: Czy usługa SQL Analytics obsługuje interfejsy API REST?

A. Tak. Większość funkcji REST, które mogą być używane z SQL Database, jest również dostępna w usłudze SQL Analytics. Informacje o interfejsie API można znaleźć na stronach dokumentacji REST lub w [witrynie MSDN](https://msdn.microsoft.com/library/azure/mt163685.aspx).


## <a name="loading"></a>Ładowanie

PYTANIE: Jakie sterowniki klientów są obsługiwane?

A. Obsługę sterowników dla analiz SQL można znaleźć na stronie [Parametry połączenia](sql-data-warehouse-connection-strings.md) .

P: Jakie formaty plików są obsługiwane przez bazę wielobazową?

Odp.: Orc, RC, Parquet i prosty rozdzielony tekst

P: jakie źródła danych można połączyć z użyciem bazy 

Odp.: [Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) i [obiekty blob usługi Azure Storage](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

P: Czy możliwe jest przekazywanie obliczeń podczas łączenia z obiektami BLOB usługi Azure Storage lub ADLS? 

Odp.: nie, baza nie działa tylko z składnikami magazynu. 

P: Czy można nawiązać połączenie z usługą HDI?

Odp.: HDI może użyć ADLS lub WASB jako warstwy systemu plików HDFS. Jeśli masz albo jako warstwę systemu plików HDFS, możesz załadować te dane do programu SQL DW. Nie można jednak generować obliczeń przekazywania do wystąpienia HDI. 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat usługi Azure Synapse jako całości, zobacz naszą stronę z [omówieniem](sql-data-warehouse-overview-faq.md) .
