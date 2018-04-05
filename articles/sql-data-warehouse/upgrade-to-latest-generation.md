---
title: Uaktualnij do najnowszej generacji Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Kroki niezbędne do uaktualnienia usługi Azure SQL Data Warehouse najnowszej generacji Azure architektury sprzętu i magazynowania.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.services: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 42b716274e655bf91f72c1b3ab207b8a5f1ccee0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="upgrade-to-latest-generation-of-azure-sql-data-warehouse-in-the-azure-portal"></a>Uaktualnij do najnowszej generacji Azure SQL Data Warehouse w portalu Azure

Użyj portalu Azure, aby uaktualnić magazyn danych SQL Azure, tak aby użyć najnowszej generacji Azure architektura sprzętu i magazynowania. Uaktualnienie, można korzystać z większą wydajność, większą skalowalnością i nieograniczony magazyn dla indeksów magazynu kolumn.  

## <a name="applies-to"></a>Dotyczy
To uaktualnienie dotyczy hurtowni danych w zoptymalizowane dla warstwy wydajności elastyczność.  Instrukcje uaktualniania magazynu danych z zoptymalizowane dla warstwy wydajności elastyczność do zoptymalizowane dla warstwy wydajności obliczeniowej. 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

1. Jeśli zoptymalizowane dla magazynu danych elastyczność do uaktualnienia jest wstrzymana, [hurtowni danych wznowić](pause-and-resume-compute-portal.md).
2. Należy przygotować kilka minut przestoju. 
3. Proces uaktualniania Kasuje wszystkie sesje i porzuca wszystkie połączenia. Przed rozpoczęciem uaktualniania upewnij się, że zapytania została ukończona. Jeśli uaktualnienia można rozpocząć transakcji w trakcie wykonywania, podczas wycofywania można szeroką gamę. 

## <a name="start-the-upgrade"></a>Uruchom operację uaktualniania

1. Otwórz magazyn danych w portalu Azure, a następnie kliknij przycisk **uaktualnienia do zoptymalizowana dla obliczania**.
2. Zwróć uwagę, zoptymalizowane dla opcji warstwy wydajności obliczeniowej. Ustawieniem domyślnym jest porównywalna bieżący poziom przed uaktualnieniem.
3. Wybierz warstwę wydajności. Cena zoptymalizowane dla warstwy wydajności obliczeniowe jest obecnie połowie — wyłączone okresie używania wersji zapoznawczej.
4. Kliknij przycisk **uaktualnienia**.
5. Sprawdź stan w portalu Azure.
6. Poczekaj, aż hurtowni danych zmienić online.

## <a name="rebuild-columnstore-indexes"></a>Odbuduj indeksy magazynu kolumn

Gdy magazyn danych jest w trybie online, można załadować danych i uruchamianie zapytań. Jednakże wydajność może być wolne na początku, ponieważ proces w tle jest migracja danych na nowy sprzęt. 

Aby wymusić danych do migracji tak szybko, jak to możliwe, zaleca się ponowne tworzenie indeksów magazynu kolumn. Aby to zrobić, zobacz wskazówki dotyczące [odbudowywania indeksów magazynu kolumn, aby poprawić jakość segmentu](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). 

## <a name="next-steps"></a>Następne kroki
Magazyn danych jest w trybie online. Aby korzystać z nowych funkcji wydajności, zobacz [klasy zasobów do zarządzania obciążenia](resource-classes-for-workload-management.md).
 