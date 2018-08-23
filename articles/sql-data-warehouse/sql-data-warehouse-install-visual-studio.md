---
title: Instalacja programu Visual Studio i narzędzi SSDT dla usługi SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Instalacja programu Visual Studio i narzędzi SQL Server Data Tools (SSDT) dla usługi Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 11e4e6e887f7c182c1c321d8837e11f07afc2f6b
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444729"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Instalacja programu Visual Studio i narzędzi SSDT dla usługi SQL Data Warehouse
Do tworzenia aplikacji dla usługi SQL Data Warehouse, zaleca się używanie najnowszej wersji programu Visual Studio z najnowszej wersji programu SQL Server Data Tools (SSDT).  W celu zapewnienia zgodności z poprzednimi wersjami możliwa jest również obsługa programu Visual Studio 2013 Update 5 z narzędziami SSDT.  

Za pomocą programu Visual Studio z narzędziami SSDT pozwala na używanie Eksplorator obiektów SQL Server, aby wizualnie eksplorować tabele, widoki, procedury składowane i wiele innych obiektów w usłudze SQL Data Warehouse, a także uruchamianie zapytań.

> [!NOTE]
> Usługa SQL Data Warehouse jeszcze nie obsługuje projektów bazy danych programu Visual Studio. Aby otrzymywać okresowe aktualizacje na temat tej funkcji, można głosować w sprawie [UserVoice].
> 
> 

## <a name="step-1-install-visual-studio"></a>Krok 1: Zainstaluj program Visual Studio
Skorzystaj z poniższych linków, aby pobrać i zainstalować program Visual Studio. Jeśli masz już program Visual Studio 2013 lub nowszy jest zainstalowany, możesz przejść do kroku 2, należy zainstalować narzędzia SSDT.

1. [Pobierz program Visual Studio][].
2. Postępuj zgodnie z [Instalowanie programu Visual Studio] [ Installing Visual Studio] pomagające w witrynie MSDN i wybierz domyślne konfiguracje.

## <a name="step-2-install-ssdt"></a>Krok 2: instalowanie narzędzi SSDT
Aby zainstalować narzędzia SSDT dla programu Visual Studio, najpierw Wyszukaj aktualizację SSDT z poziomu programu Visual Studio wykonaj następujące czynności.

1. W programie Visual Studio kliknij **narzędzia** / **rozszerzenia i aktualizacje...** / **Aktualizacje**
2. Wybierz opcję **Aktualizacje produktu** i poszukaj **Microsoft SQL Server Update do oprzyrządowania bazy danych**

Jeśli aktualizacja nie zostanie znaleziona, zapewne masz już zainstalowaną najnowszą wersję.  Aby upewnić się, że narzędzia SSDT są już zainstalowane, kliknij menu **Pomoc** / **Microsoft Visual Studio — informacje** i poszukaj narzędzi SQL Server Data Tools na liście. Najnowsza wersja narzędzi SSDT to 14.0.60525.0. Jeśli opcja instalacji nie jest dostępny z poziomu programu Visual Studio, również możesz odwiedzić stronę [pobieranie narzędzi SSDT] [ SSDT Download] strony, aby pobrać i zainstalować narzędzia SSDT ręcznie.

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
