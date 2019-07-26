---
title: Zainstaluj program Visual Studio i SSDT dla SQL Data Warehouse | Microsoft Docs
description: Instalacja programu Visual Studio i narzędzi SQL Server Data Tools (SSDT) dla usługi Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/05/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: aa904ac62ce41cc89945ddfdef2e426143e42bb4
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479493"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Zainstaluj program Visual Studio i SSDT for SQL Data Warehouse
Użyj programu Visual Studio 2019 do tworzenia aplikacji do SQL Data Warehouse. Obecnie program Visual Studio 2019 SSDT nie jest obsługiwany w przypadku SQL Data Warehouse. 

Za pomocą programu Visual Studio with SSDT można korzystać z Eksplorator obiektów SQL Server, aby eksplorować tabele, widoki, procedury składowane i wiele innych obiektów wizualnie w SQL Data Warehouse. Umożliwia również uruchamianie zapytań.

> [!NOTE]
> Usługa SQL Data Warehouse jeszcze nie obsługuje projektów bazy danych programu Visual Studio. Aby otrzymywać okresowe aktualizacje dotyczące tej funkcji, zapoznaj się z usługą [UserVoice].
> 
> 

## <a name="step-1-install-visual-studio"></a>Krok 1: Instalacja programu Visual Studio
Skorzystaj z poniższych linków, aby pobrać i zainstalować program Visual Studio. Jeśli masz już zainstalowaną Visual Studio 2013 lub nowszą, możesz przejść do kroku 2, zainstalować SSDT.

1. [Pobierz program Visual Studio][].
2. Postępuj zgodnie z przewodnikiem [Instalowanie programu Visual Studio][Installing Visual Studio] w witrynie MSDN i wybierz domyślne konfiguracje.

## <a name="step-2-install-ssdt"></a>Krok 2: Zainstaluj SSDT
Aby zainstalować SSDT dla programu Visual Studio, najpierw sprawdź dostępność aktualizacji SSDT w programie Visual Studio, wykonując następujące kroki.

1. W programie Visual Studio kliknij pozycję **Narzędzia** / **rozszerzenia i aktualizacje...** / **Dostępności**
2. Wybierz opcję **Aktualizacje produktu** i poszukaj **Microsoft SQL Server Update do oprzyrządowania bazy danych**

Jeśli aktualizacja nie zostanie znaleziona, należy zainstalować najnowszą wersję. Aby upewnić się, że narzędzia SSDT są już zainstalowane, kliknij menu **Pomoc** / **Microsoft Visual Studio — informacje** i poszukaj narzędzi SQL Server Data Tools na liście. Jeśli opcja Zainstaluj nie jest dostępna w programie Visual Studio, możesz odwiedzić stronę [pobierania SSDT][SSDT Download] , aby ręcznie pobrać i zainstalować SSDT.

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy masz najnowszą wersję programu SSDT, możesz [nawiązać połączenie][connect] z SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Pobierz program Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
