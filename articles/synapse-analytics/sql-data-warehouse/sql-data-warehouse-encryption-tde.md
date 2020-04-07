---
title: Przezroczyste szyfrowanie danych (portal)
description: Przezroczyste szyfrowanie danych (TDE) w usłudze Azure Synapse Analytics
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 247691326e3aa2c8027dd0318b23a2cbfcba1efe
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745230"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Wprowadzenie do szyfrowania danych przezroczystych (TDE)

> [!div class="op_single_selector"]
>
> * [Omówienie zabezpieczeń](sql-data-warehouse-overview-manage-security.md)
> * [Authentication](sql-data-warehouse-authentication.md)
> * [Szyfrowanie (portal)](sql-data-warehouse-encryption-tde.md)
> * [Szyfrowanie (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby włączyć przezroczyste szyfrowanie danych (TDE), musisz być administratorem lub członkiem roli dbmanager.

## <a name="enabling-encryption"></a>Włączanie szyfrowania

Aby włączyć TDE, wykonaj poniższe czynności:

1. Otwieranie bazy danych w [witrynie Azure portal](https://portal.azure.com)
2. W bloku bazy danych kliknij przycisk **Ustawienia**
3. Wybieranie ustawień portalu ![opcji **szyfrowanie danych przezroczystych**](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Wybierz **On** ustawienie ![Na ustawieniach portalu na](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. Wybieranie **zapisz**
   ![ustawienia portalu zapisz](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Wyłączanie szyfrowania

Aby wyłączyć TDE, wykonaj poniższe czynności:

1. Otwieranie bazy danych w [witrynie Azure portal](https://portal.azure.com)
2. W bloku bazy danych kliknij przycisk **Ustawienia**
3. Wybieranie ustawień portalu ![opcji **szyfrowanie danych przezroczystych**](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Wyłącz **Off** ustawienia ![portalu z ustawieniem Off](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Wybierz **zapisz**
   ![ustawienie portalu zapisz 2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>Szyfrowanie dmv

Szyfrowanie można potwierdzić za pomocą następujących dmv:

* [Sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
