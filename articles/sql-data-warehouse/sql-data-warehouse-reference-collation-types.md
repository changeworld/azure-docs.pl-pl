---
title: Sortowanie — Azure SQL Data Warehouse | Microsoft Docs
description: Typy sortowania obsługiwane w Azure SQL Data Warehouse.
services: sql-data-warehouse
author: antvgski
manager: igorstan
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: af88d2f0ce0e26fe67dda265b59684083c133dd5
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297597"
---
# <a name="database-collation-support-for-azure-sql-data-warehouse"></a>Obsługa sortowania bazy danych dla Azure SQL Data Warehouse

Podczas tworzenia nowej bazy danych Azure SQL Data Warehouse można zmienić domyślne sortowanie bazy danych z Azure Portal. Ta funkcja ułatwia tworzenie nowej bazy danych przy użyciu jednego z 3800 obsługiwanych w ramach sortowania baz danych dla SQL Data Warehouse.
Sortowania zapewniają ustawienia regionalne, strony kodowej, porządek sortowania i reguły czułości znaków dla typów danych opartych na znakach. Po wybraniu tej opcji wszystkie kolumny i wyrażenia wymagające informacji o sortowaniu dziedziczą wybrane sortowanie z poziomu bazy danych. Dziedziczenie domyślne można przesłonić, jawnie podając inne sortowanie dla typu danych opartego na znakach.

## <a name="changing-collation"></a>Zmiana sortowania
Aby zmienić domyślne sortowanie, można wykonać prostą aktualizację pola sortowania w środowisku aprowizacji.

Na przykład jeśli chcesz zmienić domyślne sortowanie na uwzględniające wielkość liter, po prostu zmień nazwę sortowania z SQL_Latin1_General_CP1_CI_AS na SQL_Latin1_General_CP1_CS_AS. 

#<a name="list-of-unsupported-collation-types"></a>Lista nieobsługiwanych typów sortowania
*   Japanese_Bushu_Kakusu_140_BIN
*   Japanese_Bushu_Kakusu_140_BIN2
*   Japanese_Bushu_Kakusu_140_CI_AI_VSS
*   Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AI_VSS
*   Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AI
*   Japanese_Bushu_Kakusu_140_CI_AI_WS
*   Japanese_Bushu_Kakusu_140_CI_AI_KS
*   Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*   Japanese_Bushu_Kakusu_140_CI_AS
*   Japanese_Bushu_Kakusu_140_CI_AS_WS
*   Japanese_Bushu_Kakusu_140_CI_AS_KS
*   Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*   Japanese_Bushu_Kakusu_140_CS_AI
*   Japanese_Bushu_Kakusu_140_CS_AI_WS
*   Japanese_Bushu_Kakusu_140_CS_AI_KS
*   Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*   Japanese_Bushu_Kakusu_140_CS_AS
*   Japanese_Bushu_Kakusu_140_CS_AS_WS
*   Japanese_Bushu_Kakusu_140_CS_AS_KS
*   Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*   Japanese_XJIS_140_BIN
*   Japanese_XJIS_140_BIN2
*   Japanese_XJIS_140_CI_AI_VSS
*   Japanese_XJIS_140_CI_AI_WS_VSS
*   Japanese_XJIS_140_CI_AI_KS_VSS
*   Japanese_XJIS_140_CI_AI_KS_WS_VSS
*   Japanese_XJIS_140_CI_AS_VSS
*   Japanese_XJIS_140_CI_AS_WS_VSS
*   Japanese_XJIS_140_CI_AS_KS_VSS
*   Japanese_XJIS_140_CI_AS_KS_WS_VSS
*   Japanese_XJIS_140_CS_AI_VSS
*   Japanese_XJIS_140_CS_AI_WS_VSS
*   Japanese_XJIS_140_CS_AI_KS_VSS
*   Japanese_XJIS_140_CS_AI_KS_WS_VSS
*   Japanese_XJIS_140_CS_AS_VSS
*   Japanese_XJIS_140_CS_AS_WS_VSS
*   Japanese_XJIS_140_CS_AS_KS_VSS
*   Japanese_XJIS_140_CS_AS_KS_WS_VSS
*   Japanese_XJIS_140_CI_AI
*   Japanese_XJIS_140_CI_AI_WS
*   Japanese_XJIS_140_CI_AI_KS
*   Japanese_XJIS_140_CI_AI_KS_WS
*   Japanese_XJIS_140_CI_AS
*   Japanese_XJIS_140_CI_AS_WS
*   Japanese_XJIS_140_CI_AS_KS
*   Japanese_XJIS_140_CI_AS_KS_WS
*   Japanese_XJIS_140_CS_AI
*   Japanese_XJIS_140_CS_AI_WS
*   Japanese_XJIS_140_CS_AI_KS
*   Japanese_XJIS_140_CS_AI_KS_WS
*   Japanese_XJIS_140_CS_AS
*   Japanese_XJIS_140_CS_AS_WS
*   Japanese_XJIS_140_CS_AS_KS
*   Japanese_XJIS_140_CS_AS_KS_WS
*   SQL_EBCDIC1141_CP1_CS_AS
*   SQL_EBCDIC277_2_CP1_CS_AS

## <a name="checking-the-current-collation"></a>Sprawdzanie bieżącego sortowania
Aby sprawdzić bieżące sortowanie bazy danych, można uruchomić następujące fragmenty kodu T-SQL: Wybierz DATABASEPROPERTYEX (DB_NAME (), "Collation") jako sortowanie; Po przekazaniu elementu "Collation" jako parametru właściwości funkcja DatabasePropertyEx zwraca bieżące sortowanie dla określonej bazy danych. Więcej informacji na temat funkcji DatabasePropertyEx można znaleźć w witrynie MSDN.

