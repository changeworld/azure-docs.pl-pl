---
title: Monitorowanie i dostrajanie wydajności — baza danych SQL Azure | Dokumentacja firmy Microsoft
description: Porady dotyczące dostrajania w bazie danych SQL Azure za pośrednictwem oceny i poprawa wydajności.
services: sql-database
author: v-shysun
manager: craigg
editor: ''
keywords: dostrajanie wydajności bazy danych dostrajanie dostrajanie wskazówki dotyczące wydajności programu sql wydajności programu SQL dostrajania wydajności bazy danych sql
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: v-shysun
ms.openlocfilehash: 710d517621cb9d4d9d2e9bf29e4facf7d75ab481
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="monitoring-and-performance-tuning"></a>Monitorowanie i dostrajanie wydajności

Azure SQL Database odbywa się automatycznie i usługa elastyczne danych, gdzie można łatwo monitorować użycie, dodawane lub usuwane zasobów (Procesora, pamięci, we/wy), Znajdź zaleceń, które mogą poprawić wydajność bazy danych lub umożliwić dostosowanie do obciążenie i automatycznie optymalizacji wydajności bazy danych.

Ten artykuł zawiera omówienie monitorowania i opcje, które są dostępne w bazie danych SQL Azure dostrajania wydajności.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="monitoring-and-troubleshooting-database-performance"></a>Monitorowanie i rozwiązywanie problemów z wydajnością bazy danych

Baza danych SQL Azure pozwala łatwo monitorować użycie bazy danych i identyfikowania zapytania, które mogą powodować problemy z wydajnością. Można monitorować wydajność bazy danych przy użyciu usługi Azure widoków portalu lub systemu. Masz następujące opcje monitorowania i rozwiązywania problemów z wydajnością bazy danych:

1. W [portalu Azure](https://portal.azure.com), kliknij przycisk **baz danych**, wybierz bazę danych, a następnie użyj wykresu monitorowanie ma zostać wyszukane zasoby zbliża się ich maksymalna. Użycie jednostek DTU jest wyświetlane domyślnie. Kliknij przycisk **Edytuj** zmienić zakres czasu i wartości wyświetlane.
2. Użyj [szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) do identyfikacji zapytania, które spędzają na w pełni korzystać z zasobów.
3. Można użyć dynamicznych widoków zarządzania (widoków DMV), zdarzeń rozszerzonych (`XEvents`), a Magazyn zapytań w programie SSMS do pobrania parametrów wydajności w czasie rzeczywistym.

Zobacz [tematu wskazówki dotyczące wydajności](sql-database-performance-guidance.md) można znaleźć techniki, które można użyć w celu poprawy wydajności bazy danych SQL Azure po zidentyfikowaniu niektórych problem przy użyciu tych raportów lub widoków.

> [!IMPORTANT] 
> Zalecane jest używanie najnowszej wersji programu Management Studio, aby zachować synchronizację z aktualizacjami platformy Microsoft Azure i usługi SQL Database. [Zaktualizuj program SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="optimize-database-to-improve-performance"></a>Optymalizacji bazy danych w celu zwiększenia wydajności

Baza danych SQL Azure umożliwia identyfikowanie możliwości poprawy i zoptymalizować wydajność zapytań bez zmieniania zasobów, przeglądając [zalecenia dotyczące dostrajania wydajności](sql-database-advisor.md). Częstymi przyczynami słabej wydajności bazy danych są brakujące indeksy i nieprawidłowo zoptymalizowane zapytania. Można stosować te zalecenia dostrajania, aby poprawić wydajność obciążenia.
Możesz również let bazy danych Azure SQL do [automatycznie zoptymalizować wydajność kwerend](sql-database-automatic-tuning.md) , stosując wszystkie zidentyfikowane zalecenia i sprawdzanie, czy poprawiają wydajność bazy danych. Następujące opcje można użyć do zwiększenia wydajności bazy danych:

1. Użyj [doradcy bazy danych SQL](sql-database-advisor-portal.md) Aby wyświetlić zalecenia dotyczące tworzenia i usuwanie indeksów, parametryzacja zapytań i rozwiązywania problemów ze schematu.
2. [Włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md) i pozwól Azure SQL bazy danych automatycznie poprawka zidentyfikowane problemy z wydajnością.

## <a name="improving-database-performance-with-more-resources"></a>Poprawa wydajności bazy danych przy użyciu więcej zasobów

Na koniec Jeśli nie ma żadnych towarów działań, które może poprawić wydajność bazy danych, można zmienić ilość zasobów dostępnych w bazie danych SQL Azure. Można przypisać więcej zasobów, zmieniając [DTU warstwy usług](sql-database-service-tiers-dtu.md) autonomiczna baza danych lub zwiększenie wartości Edtu elastycznej puli w dowolnym momencie. Alternatywnie Jeśli używasz [na podstawie vCore model kupna (wersja zapoznawcza)](sql-database-service-tiers-vcore.md), można zmienić warstwę usługi lub zwiększ zasoby przydzielone do bazy danych. 
1. W przypadku autonomicznej bazy danych, możesz [zmiana warstw usług](sql-database-service-tiers-dtu.md) lub [zasoby obliczeniowe](sql-database-service-tiers-vcore.md)na żądanie w celu poprawy wydajności bazy danych.
2. W przypadku wielu baz danych, rozważ zastosowanie [pule elastyczne](sql-database-elastic-pool-guidance.md) do zasoby są skalowane automatycznie.

## <a name="tune-and-refactor-application-or-database-code"></a>Dostrajanie i zrefaktoryzuj aplikacji lub kodu bazy danych

Kod aplikacji w celu bardziej optymalnie użycia bazy danych, zmienić indeksów, wymusić planów lub użyj wskazówek ręcznie dostosowanie bazy danych, aby obciążenie, można zmienić. Znajdź pewne wskazówki i porady ręczne dostosowywania i poprawiania kodu w [tematu wskazówki dotyczące wydajności](sql-database-performance-guidance.md) artykułu.


## <a name="next-steps"></a>Kolejne kroki

- Aby włączyć automatyczne dostrajanie w bazie danych SQL Azure i umożliwić funkcji automatycznego dostrajania obciążenie w pełni zarządzać, zobacz [Włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md).
- Umożliwia ręczne dostrajania, możesz przejrzeć [dostrajanie zalecenia w portalu Azure](sql-database-advisor-portal.md) i ręcznie zastosować te, które poprawić wydajność kwerend.
- Zmień zasoby, które są dostępne w bazie danych, zmieniając [warstwach usług bazy danych SQL Azure](sql-database-performance-guidance.md)
