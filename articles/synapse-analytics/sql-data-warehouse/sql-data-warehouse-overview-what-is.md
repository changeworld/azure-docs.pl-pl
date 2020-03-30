---
title: Co to jest usługa Azure Synapse Analytics (dawniej SQL DW)?
description: Usługa Azure Synapse Analytics (dawniej SQL DW) to nieograniczona usługa analityczna, która łączy w sobie hurtownię danych w przedsiębiorstwie i analizę dużych zbiorów danych.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: a118d028cc85eb858dd0dc1fa6d5d2268f7db43b
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350402"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Co to jest usługa Azure Synapse Analytics (dawniej SQL DW)?

Azure Synapse to nieograniczona usługa analizy, która łączy magazynowanie danych przedsiębiorstwa z analizą danych big data. Zapewnia swobodę wykonywania zapytań dotyczących danych na Twoich warunkach, korzystając z bezserwerowych zasobów na żądanie lub aprowizowanych zasobów — w dużej skali. Usługa Azure Synapse łączy te dwa światy z ujednoliconym doświadczeniem w zakresie pozyskiwania, przygotowywania, zarządzania i obsługi danych na potrzeby natychmiastowej analizy biznesowej i uczenia maszynowego

Usługa Azure Synapse ma cztery składniki:
- ANALIZA SQL: Kompletna analiza oparta na T-SQL — ogólnie dostępna
    - Pula SQL (płatność za dwu aprowizowana) 
    - SQL na żądanie (płatność za TB przetwarzane) - (Wersja zapoznawcza)
- Iskra: Głęboko zintegrowana platforma Apache Spark (wersja zapoznawcza) 
- Integracja danych: hybrydowa integracja danych (wersja zapoznawcza)
- Studio: Ujednolicone środowisko użytkownika.  (Wersja zapoznawcza)

> [!NOTE]
> Aby uzyskać dostęp do funkcji w wersji zapoznawczej platformy Azure Synapse, poproś o dostęp [tutaj](https://aka.ms/synapsepreview). Firma Microsoft będzie klasyfikować wszystkie żądania i odpowiadać tak szybko, jak to możliwe.

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>Analiza SQL i pula SQL w usłudze Azure Synapse

Usługa SQL Analytics odnosi się do funkcji magazynowania danych w przedsiębiorstwie, które są ogólnie dostępne w usłudze Azure Synapse. 

Pula SQL reprezentuje zbiór zasobów analitycznych, które są aprowizacji podczas korzystania z analizy SQL Analytics. Rozmiar puli SQL jest określany przez jednostki magazynowania danych (DWU).

Importuj duże zbiory danych za pomocą prostych zapytań [T-SQL PolyBase,](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) a następnie korzystaj z mocy MPP do uruchamiania analizy o wysokiej wydajności. Podczas integrowania i analizowania usługa SQL Analytics stanie się pojedynczą wersją prawdy, na którą Twoja firma może liczyć, aby uzyskać szybsze i bardziej niezawodne informacje.  

## <a name="key-component-of-a-big-data-solution"></a>Kluczowy składnik rozwiązania do dużych zbiorów danych

Hurtownia danych jest kluczowym składnikiem opartego na chmurze, kompleksowego rozwiązania do przetwarzania dużych zbiorów danych.

![Rozwiązanie magazynu danych](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

W rozwiązaniu danych w chmurze dane są pozyskiwane do magazynów danych big data z różnych źródeł. Platformy Hadoop, Spark i algorytmy uczenia maszynowego przygotowują i przystosowują dane, gdy te już znajdą się w magazynie danych big data. Gdy dane są gotowe do złożonej analizy, usługa SQL Analytics korzysta z technologii PolyBase, aby wykonywać zapytania dotyczące magazynów danych big data. Technologia PolyBase wykorzystuje standardowe zapytania języka T-SQL w celu wprowadzenia danych do tabel usług SQL Analytics.
 
Usługa SQL Analytics przechowuje dane w tabelach relacyjnych z magazynem kolumnowym. Ten format znacznie zmniejsza koszty przechowywania danych i poprawia wydajność zapytań. Gdy dane znajdują się w magazynie, można wykonywać analizy na wielką skalę. W porównaniu do tradycyjnych systemów bazy danych, zapytania analizy kończą się w ciągu sekund zamiast minut lub godzin zamiast dni. 

Wyniki analizy można odnieść do ogólnoświatowych baz danych raportowania lub aplikacji. Analitycy biznesowi mogą zatem uzyskiwać wyniki analizy w celu świadomego podejmowania decyzji biznesowych.

## <a name="next-steps"></a>Następne kroki

- Poznaj [architekturę Usługi Azure Synapse](massively-parallel-processing-mpp-architecture.md)
- Szybkie [tworzenie puli SQL](create-data-warehouse-portal.md)
- [Załaduj przykładowe dane](load-data-from-azure-blob-storage-using-polybase.md).
- Przeglądaj [filmy](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Lub spójrz na niektóre z tych innych zasobów usługi Azure Synapse.  
* Szukaj [blogów](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* Przesyłanie [żądań funkcji](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Tworzenie biletu pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md)
* Szukaj [na forum MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
* Szukaj [na forum Przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure-sqldw)
