---
title: Zainstaluj program Visual Studio 2019
description: Instalacja programu Visual Studio i narzędzi SQL Server Data Tools (SSDT) dla usługi Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: seo-lt-2019
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 11/06/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 96d66e6190732d88ba9a33094b6c27bbadfd4aae
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747808"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>Wprowadzenie do programu Visual Studio 2019 dla SQL Data Warehouse
Visual Studio **2019** SQL Server Data Tools (SSDT) to pojedyncze narzędzie, które umożliwia wykonywanie następujących czynności:

- Łączenie, wykonywanie zapytań i opracowywanie aplikacji dla SQL Data Warehouse 
- Skorzystaj z Eksploratora obiektów, aby wizualnie eksplorować wszystkie obiekty w modelu danych, w tym tabele, widoki, procedury składowane itd.
- Generowanie skryptów języka T-SQL Data Definition Language (DDL) dla obiektów
- Opracowywanie magazynu danych przy użyciu metody opartej na stanie z projektami bazy danych SSDT
- Zintegruj projekt bazy danych z systemami kontroli źródła, takimi jak Git, za pomocą Azure Repos
- Konfigurowanie potoków ciągłej integracji i wdrażania z serwerami automatyzacji, takimi jak Azure DevOps

## <a name="install-visual-studio-2019"></a>Zainstaluj program Visual Studio 2019
Zobacz [Pobierz program Visual Studio 2019][] , aby pobrać i zainstalować program visual Studio **16,3 lub nowszy**. Podczas instalacji wybierz obciążenie magazynu i przetwarzania danych. Instalacja autonomiczna SSDT nie jest już wymagana w programie Visual Studio 2019.

## <a name="next-steps"></a>Następne kroki
Teraz, gdy masz najnowszą wersję programu SSDT, możesz [nawiązać połączenie][connect] z SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Pobierz program Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
