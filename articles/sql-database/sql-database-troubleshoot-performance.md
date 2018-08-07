---
title: Monitorowanie i dostrajanie wydajności — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Porady dotyczące dostrajania w usłudze Azure SQL Database do oceny i poprawy wydajności.
services: sql-database
author: danimir
manager: craigg
editor: ''
keywords: wydajność programu SQL dostrajania bazy danych dostrajanie wydajności, wskazówki dotyczące dostrajania wydajności oprogramowania sql dostrajanie wydajności bazy danych sql
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: v-daljep
ms.reviewer: carlrab
ms.openlocfilehash: 8d8b983a5304f227d09392198d0f1307fe6dafde
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524188"
---
# <a name="monitoring-and-performance-tuning"></a>Monitorowanie i dostrajanie wydajności

Usługa Azure SQL Database odbywa się automatycznie i usługi danych elastyczne, gdzie użytkownik może łatwo monitorować użycie, dodawanie lub usuwanie zasobów (procesor CPU, pamięć, we/wy), Znajdź zaleceń, które mogą zwiększyć wydajność bazy danych lub pozwolić dostosowywać się do obciążenia bazy danych i automatycznie zoptymalizować wydajność.

Ten artykuł zawiera omówienie monitorowania i opcje, które są dostępne w usłudze Azure SQL Database dostrajania wydajności.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="monitoring-and-troubleshooting-database-performance"></a>Monitorowanie i rozwiązywanie problemów z wydajnością bazy danych

Usługa Azure SQL Database pozwala łatwo monitorować użycie bazy danych i identyfikować zapytania, które mogą spowodować problemy z wydajnością. Można monitorować wydajność bazy danych przy użyciu usługi Azure portal lub system widoków. Masz następujące opcje monitorowania i rozwiązywanie problemów z wydajnością bazy danych:

1. W [witryny Azure portal](https://portal.azure.com), kliknij przycisk **baz danych SQL**wybierz bazy danych, a następnie użyć wykres monitorowanie wyszukiwać zasoby zbliża się do ich maksymalnej. Użycie jednostek DTU jest wyświetlana domyślnie. Kliknij przycisk **Edytuj** zmienić zakres czasu i wartości wyświetlane.
2. Użyj [Query Performance Insight](sql-database-query-performance.md) można identyfikować zapytania, które możesz wydać na w pełni korzystać z zasobów.
3. Możesz użyć dynamicznych widoków zarządzania (DMV) Extended Events (`XEvents`), a Store zapytania w programie SSMS można pobrać parametrów wydajności w czasie rzeczywistym.

Zobacz [tematu wskazówki dotyczące wydajności](sql-database-performance-guidance.md) można znaleźć technik, które można użyć, aby poprawić wydajność usługi Azure SQL Database, jeśli znajdziesz problem za pomocą tych raportów lub widoków.

> [!IMPORTANT] 
> Zalecane jest używanie najnowszej wersji programu Management Studio, aby zachować synchronizację z aktualizacjami platformy Microsoft Azure i usługi SQL Database. [Zaktualizuj program SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="optimize-database-to-improve-performance"></a>Optymalizowanie bazy danych w celu zwiększenia wydajności

Usługa Azure SQL Database umożliwia określenie możliwości w celu zwiększenia i zoptymalizowania wydajności zapytań bez konieczności zmieniania zasobów, przeglądając [zalecenia dotyczące dostrajania wydajności](sql-database-advisor.md). Częstymi przyczynami słabej wydajności bazy danych są brakujące indeksy i nieprawidłowo zoptymalizowane zapytania. Można zastosować te zalecenia dotyczące dostrajania, aby zwiększyć wydajność przetwarzania obciążenia.
Możesz również pozwalają bazy danych Azure SQL, aby [automatycznie zoptymalizować wydajność kwerend](sql-database-automatic-tuning.md) , stosując wszystkie zidentyfikowane zalecenia i weryfikowanie, czy poprawiają wydajność bazy danych. Aby zwiększyć wydajność bazy danych, można użyć następujących opcji:

1. Użyj [SQL Database Advisor](sql-database-advisor-portal.md) Aby wyświetlić zalecenia dotyczące tworzenia i usuwanie indeksów, parametryzacji zapytań i naprawia problemy ze schematem.
2. [Włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md) i pozwól SQL Azure, bazy danych automatycznego rozwiązywania zidentyfikowanych problemów z wydajnością.

## <a name="improving-database-performance-with-more-resources"></a>Poprawa wydajności bazy danych przy użyciu większej ilości zasobów

Na koniec Brak elementów informacje z możliwością działania, które może poprawić wydajność bazy danych, możesz zmienić ilość zasobów dostępnych w usłudze Azure SQL Database. Można przypisać więcej zasobów, zmieniając [warstwy usługi jednostki DTU](sql-database-service-tiers-dtu.md) autonomicznej bazy danych lub zwiększenie jednostek Edtu elastycznej puli w dowolnej chwili. Alternatywnie Jeśli używasz [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md), możesz zmienić warstwę usługi lub zwiększenie zasobów przydzielonych do bazy danych. 
1. W przypadku autonomicznych baz danych, możesz [zmienianie warstw usług](sql-database-service-tiers-dtu.md) lub [zasoby obliczeniowe](sql-database-service-tiers-vcore.md)na żądanie w celu poprawy wydajności bazy danych.
2. W przypadku wielu baz danych można rozważyć użycie [pul elastycznych](sql-database-elastic-pool-guidance.md) automatycznie skalować zasoby.

## <a name="tune-and-refactor-application-or-database-code"></a>Dostrajanie i refaktoryzacji aplikacji lub kodu bazy danych

Możesz zmienić kod aplikacji, aby bardziej optymalnie korzystania z bazy danych, zmień indeksów, wymusić plany lub użyj wskazówek, aby ręcznie dostosować bazy danych do obciążenia. Znajdź niektóre wskazówki i porady dotyczące ręcznego dostosowywania i ponowne napisanie kodu w [tematu wskazówki dotyczące wydajności](sql-database-performance-guidance.md) artykułu.


## <a name="next-steps"></a>Kolejne kroki

- Aby włączyć automatyczne dostrajanie w usłudze Azure SQL Database i umożliwiają zarządzanie obciążeniem pracą zapewnia w pełni funkcja dostrajania automatycznego, zobacz [Włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md).
- Aby korzystać z ręcznego dostrajania, możesz przejrzeć [dostrajania zalecenia w witrynie Azure portal](sql-database-advisor-portal.md) i ręcznie zastosować te, które poprawić wydajność zapytań.
- Zmienić zasoby, które są dostępne w bazie danych, zmieniając [warstwy usługi Azure SQL Database](sql-database-performance-guidance.md)
