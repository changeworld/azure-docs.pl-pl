---
title: Azure SQL Database skalowanie zasobów | Microsoft Docs
description: W tym artykule wyjaśniono, jak skalować bazę danych, dodając lub usuwając przydzielone zasoby.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: abc6f8a7a2fda3578bbcf2947188752f8f3373cd
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566829"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Dynamiczne skalowanie zasobów bazy danych przy minimalnym przestoju

Azure SQL Database umożliwia dynamiczne dodawanie większej liczby zasobów do bazy danych z minimalnym [czasem przestoju](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/); Jednak istnieje przedział czasu, w którym połączenie jest tracone do bazy danych przez krótki czas, co można wyeliminować przy użyciu logiki ponawiania.

## <a name="overview"></a>Omówienie

Gdy zapotrzebowanie na aplikację rośnie od kilku urządzeń i klientów do milionów, Azure SQL Database skalowanie na bieżąco przy minimalnym przestoju. Skalowalność to jedna z najważniejszych cech PaaS, które umożliwiają dynamiczne dodawanie większej liczby zasobów do usługi w razie potrzeby. Azure SQL Database umożliwia łatwe zmienianie zasobów (mocy procesora CPU, pamięci, przepływności we/wy i magazynu) przyznanych do baz danych.

Możesz ograniczyć problemy z wydajnością z powodu zwiększonego użycia aplikacji, których nie można naprawić przy użyciu metod indeksowania lub zapisywania zapytań. Dodanie większej liczby zasobów pozwala szybko reagować, gdy baza danych osiągnie bieżące limity zasobów i potrzebuje większej mocy do obsługi przychodzącego obciążenia. Azure SQL Database również umożliwia skalowanie zasobów w dół, gdy nie są one potrzebne do obniżenia kosztów.

Nie musisz martwić się o zakup sprzętu i zmienianie podstawowej infrastruktury. Skalowanie bazy danych można łatwo wykonać za pośrednictwem Azure Portal za pomocą suwaka.

![Skalowanie wydajności bazy danych](media/sql-database-scalability/scale-performance.svg)

Azure SQL Database oferuje [model zakupu oparty na](sql-database-service-tiers-dtu.md) jednostkach DTU oraz [model zakupu oparty na rdzeń wirtualny](sql-database-service-tiers-vcore.md).

- [Model zakupu oparty na](sql-database-service-tiers-dtu.md) jednostkach DTU oferuje mieszankę zasobów obliczeniowych, pamięci i we/wy w trzech warstwach usług, co zapewnia obsługę lekkich i ciężkich obciążeń bazy danych: Podstawowa, Standardowa i Premium. Poziomy wydajności w każdej warstwie udostępniają różne kombinacje tych zasobów i można do nich dodawać kolejne zasoby magazynu.
- [Model zakupu oparty na rdzeń wirtualny](sql-database-service-tiers-vcore.md) umożliwia wybranie liczby rdzeni wirtualnych, ilości lub pamięci oraz ilości i szybkości magazynu. Ten model zakupów oferuje trzy warstwy usługi: Ogólnego przeznaczenia, Krytyczne dla działania firmy i skalowanie.

Możesz utworzyć swoją pierwszą aplikację w małej, pojedynczej bazie danych przy niskich kosztach miesięcznie w warstwie usług podstawowa, standardowa lub Ogólnego przeznaczenia, a następnie zmienić jej warstwę usług ręcznie lub programowo w dowolnym momencie do warstwy usług premium lub Krytyczne dla działania firmy w celu spełnienia wartości ne EDS dla Twojego rozwiązania. Wydajność można dostosować bez przestoju aplikacji i przerwy w świadczeniu usługi klientom. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów oraz płacenie tylko za potrzebne zasoby i tylko gdy są używane.

> [!NOTE]
> Dynamiczna skalowalność różni się od skalowania automatycznego. Skalowanie automatyczne polega na tym, że usługa jest skalowana automatycznie na podstawie kryteriów, a dynamiczna skalowalność umożliwia skalowanie ręczne z minimalnym czasem przestoju.

Pojedyncza baza danych Azure SQL Database obsługuje ręczną dynamiczną skalowalność, ale nie skalowanie automatyczne. Aby zapewnić środowisko bardziej *zautomatyzowane*, rozważ użycie elastycznych pul baz danych, które umożliwiają bazom danych udostępnianie zasobów w puli w zależności od potrzeb.
Istnieją jednak skrypty, które mogą ułatwić automatyzację skalowalności dla jednego Azure SQL Database. Na przykład zobacz temat [Monitorowanie i skalowanie pojedynczej bazy danych SQL za pomocą programu PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

W dowolnym momencie możesz zmienić [warstwy usług DTU](sql-database-service-tiers-dtu.md) lub [rdzeń wirtualny](sql-database-vcore-resource-limits-single-databases.md) z minimalnym czasem przestoju aplikacji (zwykle jest to przeciętne w ciągu czterech sekund). W przypadku wielu firm i aplikacji możliwość tworzenia baz danych i dostosowywania wydajności na żądanie jest wystarczająca, zwłaszcza jeśli wzorce użycia są względnie przewidywalne. Jednak w przypadku nieprzewidywalnych wzorców zarządzanie kosztami i modelem biznesowym może być trudne. W tym scenariuszu używana jest pula elastyczna z określoną liczbą jednostek eDTU, które są współużytkowane przez wiele baz danych w puli.

![Wprowadzenie do SQL Database: DTU pojedynczej bazy danych według warstwy i poziomu](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Wszystkie trzy rodzaje Azure SQL Database oferują możliwość dynamicznego skalowania baz danych:

- Za pomocą [pojedynczej bazy danych](sql-database-single-database-scale.md)można używać modeli [DTU](sql-database-dtu-resource-limits-single-databases.md) lub [rdzeń wirtualny](sql-database-vcore-resource-limits-single-databases.md) do definiowania maksymalnej ilości zasobów, które zostaną przypisane do poszczególnych baz danych.
- [Wystąpienie zarządzane](sql-database-managed-instance.md) używa trybu [rdzeni wirtualnych](sql-database-managed-instance.md#vcore-based-purchasing-model) i umożliwia zdefiniowanie maksymalnej liczby rdzeni procesora CPU i maksymalnego miejsca do magazynowania przystosowanego do wystąpienia. Wszystkie bazy danych w ramach tego wystąpienia będą współużytkować zasoby przydzielono do wystąpienia.
- [Pule elastyczne](sql-database-elastic-pool-scale.md) umożliwiają zdefiniowanie maksymalnego limitu zasobów dla każdej grupy baz danych w puli.

> [!NOTE]
> Po zakończeniu procesu skalowania w górę/w dół można oczekiwać krótkiego przerwania połączenia. Jeśli wdrożono logikę ponowień [dla standardowych błędów przejściowych](sql-database-connectivity-issues.md#retry-logic-for-transient-errors), nie będzie można wyszukać trybu failover.

## <a name="alternative-scale-methods"></a>Alternatywne metody skalowania

Skalowanie zasobów jest najłatwiejszym i najbardziej efektywnym sposobem poprawy wydajności bazy danych bez konieczności zmieniania kodu bazy danych ani aplikacji. W niektórych przypadkach nawet najwyższa warstwa usług, rozmiary obliczeniowe i optymalizacje wydajności mogą nie obsłużyć obciążenia na pomyślne i ekonomiczne. W takich przypadkach dostępne są następujące dodatkowe opcje skalowania bazy danych:

- Skalowanie w poziomie do [odczytu](sql-database-read-scale-out.md) jest funkcją dostępną w miejscu, w którym jest pobierana jedna replika tylko do odczytu danych, w której można wykonywać żądania zapytań tylko do odczytu, takie jak raporty. Replika tylko do odczytu będzie obsługiwać obciążenie tylko do odczytu bez wpływu na użycie zasobów w podstawowej bazie danych.
- [Fragmentowania Database](sql-database-elastic-scale-introduction.md) to zestaw technik, które umożliwiają dzielenie danych na kilka baz danych i ich niezależne skalowanie.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacje o ulepszaniu wydajności bazy danych przez zmianę kodu bazy danych, zobacz [Znajdowanie i stosowanie zaleceń dotyczących wydajności](sql-database-advisor-portal.md).
- Aby uzyskać informacje na temat zapewnienia, że wbudowana Analiza bazy danych optymalizuje bazę danych, zobacz [dostrajanie automatyczne](sql-database-automatic-tuning.md).
- Aby uzyskać informacje na temat skalowania w poziomie w usłudze Azure SQL Database, zobacz jak [używać replik tylko do odczytu do równoważenia obciążenia zapytań tylko do odczytu](sql-database-read-scale-out.md).
- Aby uzyskać informacje dotyczące fragmentowania bazy danych, zobacz [skalowanie w dół przy użyciu Azure SQL Database](sql-database-elastic-scale-introduction.md).
