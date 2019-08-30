---
title: Zainstaluj program Visual Studio 2019 dla SQL Data Warehouse | Microsoft Docs
description: Instalacja programu Visual Studio i narzędzi SQL Server Data Tools (SSDT) dla usługi Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e08d0a52903fb488359356707ecedfd81d49084a
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165066"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>Wprowadzenie do programu Visual Studio 2019 dla SQL Data Warehouse
Visual Studio **2019** SQL Server Data Tools (SSDT) to pojedyncze narzędzie, które umożliwia wykonywanie następujących czynności:

- Łączenie, wykonywanie zapytań i opracowywanie aplikacji dla SQL Data Warehouse 
- Skorzystaj z Eksploratora obiektów, aby wizualnie eksplorować wszystkie obiekty w modelu danych, w tym tabele, widoki, procedury składowane itd.
- Generowanie skryptów języka T-SQL Data Definition Language (DDL) dla obiektów
- Opracowywanie magazynu danych przy użyciu metody opartej na stanie z projektami bazy danych SSDT
- Integruj projekt bazy danych z systemami kontroli źródła, takimi jak Git, za pomocą repozytoriów usługi Azure DevOps
- Konfigurowanie potoków ciągłej integracji i wdrażania z serwerami automatyzacji, takimi jak Azure DevOps [dostępna wkrótce]

> [!NOTE]
> Obecnie projekty bazy danych programu Visual Studio SSDT są w wersji zapoznawczej. Aby otrzymywać okresowe aktualizacje dotyczące tej funkcji, zapoznaj się z usługą [UserVoice].

## <a name="install-visual-studio-2019-preview"></a>Zainstaluj program Visual Studio 2019 Preview
Zobacz [Pobierz wersję zapoznawczą programu Visual Studio 2019][] , aby pobrać i zainstalować program Visual Studio. Podczas instalacji wybierz obciążenie magazynu i przetwarzania danych. Instalacja autonomiczna SSDT nie jest już wymagana w programie Visual Studio 2019.

## <a name="reporting-issues-with-ssdt-visual-studio-2019-preview"></a>Raportowanie problemów z programem SSDT Visual Studio 2019 (wersja zapoznawcza)

Aby zgłosić problemy podczas korzystania z programu SSDT z SQL Data Warehouse, Wyślij pocztą e-mail następującą listę dystrybucyjną e-mail:<sqldwssdtpreview@service.microsoft.com>

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz najnowszą wersję programu SSDT, możesz [nawiązać połączenie][connect] z SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Pobierz wersję zapoznawczą programu Visual Studio 2019]: https://visualstudio.microsoft.com/vs/preview/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
