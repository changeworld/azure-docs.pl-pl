---
title: Instalacja programu Visual Studio i narzędzi SSDT dla usługi SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Instalacja programu Visual Studio i narzędzi SQL Server Data Tools (SSDT) dla usługi Azure SQL Data Warehouse
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: a2f01424dedb977000d0e4150f4a31c1a9a21cfb
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790648"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Instalacja programu Visual Studio i narzędzi SSDT dla usługi SQL Data Warehouse
Do tworzenia aplikacji dla usługi SQL Data Warehouse, firma Microsoft zaleca używanie najnowszej wersji programu Visual Studio z najnowszej wersji programu SQL Server Data Tools (SSDT).  W celu zapewnienia zgodności z poprzednimi wersjami możliwa jest również obsługa programu Visual Studio 2013 Update 5 z narzędziami SSDT.  

Za pomocą programu Visual Studio z narzędziami SSDT pozwala na Użyj Eksplorator obiektów SQL Server, aby wizualnie eksplorować tabele, widoki, procedury składowane i wiele innych obiektów w magazynie danych SQL, a także uruchamianie zapytań.

> [!NOTE]
> Usługa SQL Data Warehouse jeszcze nie obsługuje projektów bazy danych programu Visual Studio. Aby otrzymywać okresowe aktualizacje na tej funkcji, sprawdź oddawać głosy na [UserVoice].
> 
> 

## <a name="step-1-install-visual-studio"></a>Krok 1: Zainstaluj program Visual Studio
Skorzystaj z poniższych linków, aby pobrać i zainstalować program Visual Studio. Jeśli masz już programu Visual Studio 2013 lub nowszy, możesz przejść do kroku 2, należy zainstalować narzędzia SSDT.

1. [Pobierz program Visual Studio][].
2. Postępuj zgodnie z [Instalowanie programu Visual Studio] [ Installing Visual Studio] prowadzą w witrynie MSDN i wybierz domyślne konfiguracje.

## <a name="step-2-install-ssdt"></a>Krok 2: instalowanie narzędzi SSDT
Aby zainstalować narzędzia SSDT dla programu Visual Studio, najpierw Wyszukaj aktualizację SSDT z poziomu programu Visual Studio, wykonując następujące kroki.

1. W programie Visual Studio kliknij **narzędzia** / **rozszerzenia i aktualizacje...** / **Aktualizacje**
2. Wybierz opcję **Aktualizacje produktu** i poszukaj **Microsoft SQL Server Update do oprzyrządowania bazy danych**

Jeśli aktualizacja nie zostanie znaleziona, zapewne masz już zainstalowaną najnowszą wersję.  Aby upewnić się, że narzędzia SSDT są już zainstalowane, kliknij menu **Pomoc** / **Microsoft Visual Studio — informacje** i poszukaj narzędzi SQL Server Data Tools na liście. Najnowsza wersja narzędzi SSDT to 14.0.60525.0. Jeśli opcja instalacji nie jest dostępna w programie Visual Studio, możesz też użytkownik może odwiedzić [pobieranie narzędzi SSDT] [ SSDT Download] strony, aby pobrać i zainstalować narzędzia SSDT ręcznie.

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy masz najnowszą wersję narzędzi SSDT, możesz przystąpić do [połączyć] [ connect] do magazynu danych SQL.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Pobierz program Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
