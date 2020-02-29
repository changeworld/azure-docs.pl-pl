---
title: Zainstaluj program Visual Studio 2019
description: Zainstaluj program Visual Studio i narzędzia deweloperskie SQL Server (SSDT) dla usługi SQL Analytics
services: sql-data-warehouse
ms.custom: azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b9ad0f4aedc5a095bfa70dec1b67e52cd4d152e0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195343"
---
# <a name="getting-started-with-visual-studio-2019"></a>Wprowadzenie do programu Visual Studio 2019
Visual Studio **2019** SQL Server Data Tools (SSDT) to pojedyncze narzędzie, które umożliwia wykonywanie następujących czynności:

- Łączenie, wykonywanie zapytań i opracowywanie aplikacji
- Skorzystaj z Eksploratora obiektów, aby wizualnie eksplorować wszystkie obiekty w modelu danych, w tym tabele, widoki, procedury składowane itd.
- Generowanie skryptów języka T-SQL Data Definition Language (DDL) dla obiektów
- Opracowywanie magazynu danych przy użyciu metody opartej na stanie z projektami bazy danych SSDT
- Zintegruj projekt bazy danych z systemami kontroli źródła, takimi jak Git, za pomocą Azure Repos
- Konfigurowanie potoków ciągłej integracji i wdrażania z serwerami automatyzacji, takimi jak Azure DevOps

## <a name="install-visual-studio-2019"></a>Zainstaluj program Visual Studio 2019
Zobacz [Pobierz program Visual Studio 2019][] , aby pobrać i zainstalować program visual Studio **16,3 lub nowszy**. Podczas instalacji wybierz obciążenie magazynu i przetwarzania danych. Instalacja autonomiczna SSDT nie jest już wymagana w programie Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Nieobsługiwane funkcje w programie SSDT

Istnieją przypadki, w których wersje funkcji usługi SQL Analytics mogą nie obejmować obsługi SSDT. Następujące funkcje nie są obecnie obsługiwane:

- [Widoki z materiałami](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (w toku)
- [Uporządkowane klastrowane indeksy magazynu kolumn](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (w toku)
- [Copy — instrukcja](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (w toku)
- [Zarządzanie obciążeniami](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) — grupy obciążeń i klasyfikatory (w toku)
- [Zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - Prześlij bilet pomocy technicznej lub zagłosuj [tutaj](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) , aby uzyskać obsługiwaną funkcję.
- [Dynamiczne maskowanie danych](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - Prześlij bilet pomocy technicznej lub zagłosuj [tutaj](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) , aby uzyskać obsługiwaną funkcję. 
- Funkcja [przewidywania](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) 
- [Tabele z ograniczeniami](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints) nie są obsługiwane. Dla tych obiektów tabeli Ustaw dla akcji kompilacja wartość "Brak".

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz najnowszą wersję programu SSDT, możesz [nawiązać połączenie][connect] z pulą SQL.

<!--Anchors-->

<!--Image references-->

<!--Articles-->

[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->

[Pobierz program Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
