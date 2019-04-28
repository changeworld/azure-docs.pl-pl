---
title: Instalacja programu Visual Studio i narzędzi SSDT dla usługi SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Instalacja programu Visual Studio i narzędzi SQL Server Data Tools (SSDT) dla usługi Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/05/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: f67c2a4547ee923e5c1b49302c38693e9ffe87c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474253"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Instalacja programu Visual Studio i narzędzi SSDT dla usługi SQL Data Warehouse
Visual Studio 2017 umożliwia tworzenie aplikacji dla usługi SQL Data Warehouse. SSDT 2019 r w usłudze Visual Studio nie jest obecnie obsługiwane dla usługi SQL Data Warehouse. 

Za pomocą programu Visual Studio z narzędziami SSDT pozwala na używanie Eksplorator obiektów SQL Server, aby wizualnie eksplorować tabele, widoki, procedury składowane i wiele innych obiektów w usłudze SQL Data Warehouse, a także uruchamianie zapytań.

> [!NOTE]
> Usługa SQL Data Warehouse jeszcze nie obsługuje projektów bazy danych programu Visual Studio. Aby otrzymywać okresowe aktualizacje na temat tej funkcji, można głosować w sprawie [UserVoice].
> 
> 

## <a name="step-1-install-visual-studio"></a>Krok 1: Instalacja programu Visual Studio
Skorzystaj z poniższych linków, aby pobrać i zainstalować program Visual Studio. Jeśli masz już program Visual Studio 2013 lub nowszy jest zainstalowany, możesz przejść do kroku 2, należy zainstalować narzędzia SSDT.

1. [Pobierz program Visual Studio][].
2. Postępuj zgodnie z [Instalowanie programu Visual Studio] [ Installing Visual Studio] pomagające w witrynie MSDN i wybierz domyślne konfiguracje.

## <a name="step-2-install-ssdt"></a>Krok 2: Instalowanie narzędzi SSDT
Aby zainstalować narzędzia SSDT dla programu Visual Studio, najpierw Wyszukaj aktualizację SSDT z poziomu programu Visual Studio wykonaj następujące czynności.

1. W programie Visual Studio kliknij **narzędzia** / **rozszerzenia i aktualizacje...** / **Aktualizacje**
2. Wybierz opcję **Aktualizacje produktu** i poszukaj **Microsoft SQL Server Update do oprzyrządowania bazy danych**

Jeśli aktualizacja nie zostanie znaleziona, zapewne masz już zainstalowaną najnowszą wersję.  Aby upewnić się, że narzędzia SSDT są już zainstalowane, kliknij menu **Pomoc** / **Microsoft Visual Studio — informacje** i poszukaj narzędzi SQL Server Data Tools na liście. Jeśli opcja instalacji nie jest dostępny z poziomu programu Visual Studio, również możesz odwiedzić stronę [pobieranie narzędzi SSDT] [ SSDT Download] strony, aby pobrać i zainstalować narzędzia SSDT ręcznie.

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy masz najnowszą wersję narzędzi SSDT, możesz przystąpić do [połączyć] [ connect] do usługi SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Pobierz program Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
