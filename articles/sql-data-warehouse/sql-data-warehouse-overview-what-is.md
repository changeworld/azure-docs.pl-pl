---
title: Co to jest Azure Synapse Analytics (dawniej SQL DW)?
description: Azure Synapse Analytics (dawniej SQL DW) to nieograniczona Usługa analityczna, która łączy magazyn danych w przedsiębiorstwie i analizę danych Big Data.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 68d39b4f363794d50fd05c2067502fc55d5d0170
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153251"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Co to jest Azure Synapse Analytics (dawniej SQL DW)?

Azure Synapse to nieograniczona usługa analizy, która łączy magazynowanie danych przedsiębiorstwa z analizą danych big data. Zapewnia swobodę wykonywania zapytań dotyczących danych na Twoich warunkach, korzystając z bezserwerowych zasobów na żądanie lub aprowizowanych zasobów — w dużej skali. Usługa Azure Synapse udostępnia te dwa światy wraz z ujednoliconym doświadczeniem w zakresie pozyskiwania, przygotowywania i zarządzania danymi oraz do obsługi danych w celu natychmiastowej analizy biznesowej i uczenia maszynowego

Usługa Azure Synapse ma cztery składniki:
- Analiza SQL: Kompletna Analiza oparta na języku T-SQL — ogólnie dostępna
    - Pula SQL (płatność za jednostek dwu z obsługą administracyjną) 
    - SQL na żądanie (płatność za 1 TB przetworzonych) — (wersja zapoznawcza)
- Spark: głębokie zintegrowane Apache Spark (wersja zapoznawcza) 
- Integracja danych: integracja danych hybrydowych (wersja zapoznawcza)
- Studio: ujednolicone środowisko użytkownika.  (Wersja zapoznawcza)

> [!NOTE]
> Aby uzyskać dostęp do funkcji w wersji zapoznawczej usługi Azure Synapse, zażądaj dostępu [tutaj](https://aka.ms/synapsepreview). Firma Microsoft będzie Klasyfikacja wszystkie żądania i odpowiadać tak szybko, jak to możliwe.

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>Analiza SQL i Pula SQL na platformie Azure Synapse

Usługa SQL Analytics odwołuje się do funkcji magazynowania danych przedsiębiorstwa, które są ogólnie dostępne w usłudze Azure Synapse. 

Pula SQL reprezentuje kolekcję zasobów analitycznych, które są obsługiwane podczas korzystania z usługi SQL Analytics. Rozmiar puli SQL jest określany przez jednostki magazynowania danych (jednostek dwu).

Zaimportuj dane Big Data za [pomocą prostych zapytań](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL, a następnie użyj możliwości MPP, aby uruchomić analizę o wysokiej wydajności. Podczas integrowania i analizowania, analiza SQL stanie się jedną wersją prawdy, na którą firma może liczyć, aby szybciej i bardziej niezawodnie analizować szczegółowe informacje.  

## <a name="key-component-of-a-big-data-solution"></a>Kluczowy składnik rozwiązania danych Big Data

Magazynowanie danych to kluczowy składnik oparty na chmurze, kompleksowe rozwiązanie do obsługi danych Big Data.

![Rozwiązanie magazynu danych](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

W rozwiązaniu danych w chmurze dane są pozyskiwane do magazynów danych big data z różnych źródeł. Platformy Hadoop, Spark i algorytmy uczenia maszynowego przygotowują i przystosowują dane, gdy te już znajdą się w magazynie danych big data. Gdy dane są gotowe do złożonej analizy, program SQL Analytics używa bazy danych do wykonywania zapytań dotyczących magazynów z danymi Big Data. Baza danych używa standardowych zapytań T-SQL, aby przenieść dane do tabel analitycznych SQL.
 
Analiza SQL przechowuje dane w tabelach relacyjnych z magazynem kolumnowym. Ten format znacznie zmniejsza koszty przechowywania danych i poprawia wydajność zapytań. Gdy dane są przechowywane, możesz uruchomić analizę na ogromną skalę. W porównaniu do tradycyjnych systemów bazy danych, zapytania analizy kończą się w ciągu sekund zamiast minut lub godzin zamiast dni. 

Wyniki analizy można odnieść do ogólnoświatowych baz danych raportowania lub aplikacji. Analitycy biznesowi mogą zatem uzyskiwać wyniki analizy w celu świadomego podejmowania decyzji biznesowych.

## <a name="next-steps"></a>Następne kroki

- Poznaj [architekturę usługi Azure Synapse](massively-parallel-processing-mpp-architecture.md)
- Szybkie [Tworzenie puli SQL](create-data-warehouse-portal.md)
- [Ładowanie przykładowych danych](sql-data-warehouse-load-sample-databases.md).
- Eksploruj [wideo](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Lub zapoznaj się z innymi zasobami usługi Azure Synapse.  
* Wyszukaj [Blogi](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* Prześlij [żądania funkcji](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Tworzenie biletu pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md)
* Wyszukaj [forum MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
* [Forum wyszukiwania Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)