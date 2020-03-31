---
title: Instalowanie programu Visual Studio 2019
description: Instalowanie programów Visual Studio i SQL Server Development Tools (SSDT) dla usługi SQL Analytics
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: d0a1772706fa838f51322d5f5d5bd1b46eb9144a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351628"
---
# <a name="getting-started-with-visual-studio-2019"></a>Wprowadzenie do programu Visual Studio 2019
Visual Studio **2019** SQL Server Data Tools (SSDT) to jedno narzędzie umożliwiające następujące czynności:

- Łączenie, wykonywanie zapytań i tworzenie aplikacji
- Skorzystaj z eksploratora obiektów, aby wizualnie eksplorować wszystkie obiekty w modelu danych, w tym tabele, widoki, procedury przechowywane itp.
- Generowanie skryptów języka DDL (T-SQL data definition) dla obiektów
- Tworzenie magazynu danych przy użyciu podejścia opartego na stanie dzięki projektom bazy danych SSDT
- Zintegruj projekt bazy danych z systemami kontroli źródła, takimi jak Git z repozytorium platformy Azure
- Konfigurowanie ciągłej integracji i wdrażania potoków za pomocą serwerów automatyzacji, takich jak Azure DevOps

## <a name="install-visual-studio-2019"></a>Instalowanie programu Visual Studio 2019
Zobacz [Pobieranie programu Visual Studio 2019][] w celu pobrania i zainstalowania programu Visual Studio **16.3 lub wyższej**. Podczas instalacji wybierz obciążenie magazynowania i przetwarzania danych. Autonomiczna instalacja SSDT nie jest już wymagana w programie Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Nieobsługiwały funkcje w SSDT

Istnieją chwile, kiedy wersje funkcji dla usługi SQL Analytics mogą nie obejmować obsługi dysku SSDT. Następujące funkcje są obecnie nieobsługiwały:

- [Zmaterializowane widoki](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (w toku)
- [Uporządkowane indeksy magazynu kolumn klastrowanych](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (w toku)
- [Instrukcja COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (w toku)
- [Zarządzanie obciążeniem](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) — grupy i klasyfikatory obciążenia (w toku)
- [Zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - Prześlij zgłoszenie do pomocy technicznej lub zagłosuj [tutaj,](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) aby uzyskać tę funkcję.
- [Dynamiczne maskowanie danych](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - Prześlij zgłoszenie do pomocy technicznej lub zagłosuj [tutaj,](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) aby uzyskać tę funkcję. 
- [PREDICT,](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) funkcja 
- [Tabele z ograniczeniami](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints) nie są obsługiwane. Dla tych obiektów tabeli ustaw akcję kompilacji na "Brak".

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz najnowszą wersję narzędzia SSDT, możesz [połączyć się z](sql-data-warehouse-query-visual-studio.md) pulą SQL.




<!--Other-->

[Pobierz program Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
