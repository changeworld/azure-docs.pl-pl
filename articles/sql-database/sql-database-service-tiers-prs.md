---
title: Usługa Azure SQL Database Premium RS usługi warstwy wycofanie | Dokumentacja firmy Microsoft
description: W warstwie usług Premium RS jest wycofywana i pomoc techniczna dotycząca kończy się — Zobacz Opcje migracji.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: e12b89d0469587d7d7326bbee30f6467ada06bd5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64574085"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Warstwy usług w usłudze Azure SQL Database Premium RS (wersja zapoznawcza) zostanie wycofana — opcje migracji

W lutym 2018 roku firma Microsoft ogłosiła, że warstwy Premium RS usługi w usłudze Azure SQL Database nie zostaną zwolnione za usługi ogólnie dostępne i czy nie jest już obsługiwana po 31 stycznia 2019 r. Ten końcowy termin pomocy technicznej został rozszerzony do 30 czerwca 2019 r. W tym artykule opisano opcje migracji z warstwy Premium RS usługi do innej warstwy usług. Po 30 czerwca 2019 r firmy Microsoft będą automatycznie migrować bazy danych Premium RS do warstwy jest ogólnie dostępna usługa, która najlepiej odpowiada wymaganiom wydajności bazy danych Premium RS.

Poniżej przedstawiono miejsc docelowych migracji i opcje cenowe, które mogą być wprawdzie odpowiednie dla warstwy Premium RS klientów:

- warstwy usług (rdzeń wirtualny)

  **Ogólnego przeznaczenia** i **krytyczne dla działania firmy** warstwach usługi [model zakupowy oparty na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md). Warstwy te dwie usługi są ogólnie dostępne. Oferuje również modelu zakupu opartego na rdzeniach wirtualnych **Hiperskali** warstwy usług (w publicznej wersji zapoznawczej), która dostosowuje się na żądanie do potrzeb Twojego obciążenia za pomocą automatycznego skalowania do 100 TB dla bazy danych. Warstwy usługi w Hiperskali zapewnia wydajność operacji We/Wy do warstwy Premium w [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) cenie bliżej do warstwy usług z warstwy Premium RS.
- Cennik dotyczący tworzenia i testowania

  [Ceny deweloperskie/testowe](https://azure.microsoft.com/pricing/dev-test/) zapewnia oszczędności 55% w stosunku do stawek oferowała objęte licencją w ramach subskrypcji programu Visual Studio.
- Korzyść użycia hybrydowego platformy Azure i ceny rezerwowanie pojemności

  [Korzyść użycia hybrydowego platformy Azure i ceny rezerwowanie pojemności](https://azure.microsoft.com/pricing/details/sql-database/) zapewniają oszczędności nawet 80% w stosunku do stawek oferowała objęte licencją. Aby uzyskać więcej informacji na temat tych opcji, zobacz [korzyść użycia hybrydowego platformy Azure dla programu SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) i [wydajności rezerwowej usługi Azure SQL Database](sql-database-reserved-capacity.md).

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Podejmij działanie, aby migrować swoje bazy danych Premium RS do alternatywnego warstwy usługi SQL Database

Zapoznaj się ze wskazówkami w tym artykule, wraz z naszym ceny i dokumentację, aby określić przeznaczenie migracji odpowiednie dla obciążeń warstwa Premium RS.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Migrowanie obciążeń intensywnie korzystających z obliczeń i Zapisz

Dla obciążeń intensywnie korzystających z obliczeń warstwa Premium RS zaleca się migrację do naszych jest ogólnie dostępna oparty na rdzeniach wirtualnych ogólnego przeznaczenia warstwy usług i Oszczędzaj więcej w porównaniu z dołączonymi licencjami stawki za pomocą korzyści użycia hybrydowego platformy Azure dla programu SQL Server i oferuje rezerwowanie pojemności. Jeśli zamiast pozostanie na temat opcji zakupu jednostek DTU możesz migrację baz danych Premium RS intensywnych obliczeń do warstwy standardowa i nadal zapisywać i cennik wersji ogólnie dostępnej warstwy Premium RS (jeśli jest on zdecydowali do ogólnej dostępności).

> [!WARNING]
> Migrowanie obciążeń warstwa Premium RS do warstwy usług oparte na jednostkach DTU Premium może zwiększyć miesięcznych kosztów w porównaniu z obecnego modelu cen warstwy Premium RS. Firma Microsoft zaleca, biorąc pod uwagę warstwy na dużą skalę lub krytyczne dla działania firmy dzięki korzyści użycia hybrydowego platformy Azure i rezerwowanie pojemności, ceny, aby zachować podobnych lub niższe koszty niż warstwa Premium RS.

### <a name="premium-rs-databases"></a>Bazy danych Premium RS

|**Jeśli jesteś obecnie włączone...**|**Migrowanie do porównywalnych oparty na rdzeniach wirtualnych...**|**Migrowanie do porównywalnych oparty na jednostkach DTU...**|
|---|---|---|
|Premium RS 1|Ogólnego przeznaczenia 1 rdzeń wirtualny (4. generacji)|Standard 3|
|Premium RS 2|Ogólnego przeznaczenia, 2 rdzenie wirtualne (4. generacji)|Standard 4|
|Premium RS 4|Ogólnego przeznaczenia 4 rdzenie wirtualne (4. generacji)|Standardowa 6|
|Premium RS 6|Ogólnego przeznaczenia 6 rdzeni (4. generacji)|Standard 7|

### <a name="premium-rs-pools"></a>Pule w warstwie Premium RS

|**Jeśli jesteś obecnie włączone...**|**Migrowanie do porównywalnych oparty na rdzeniach wirtualnych...**|**Migrowanie do porównywalnych oparty na jednostkach DTU...**|
|---|---|---|
|Warstwa Premium RS puli 125 jednostek DTU|Ogólnego przeznaczenia 1 rdzeń wirtualny (4. generacji)|Pula w warstwie standardowa 100 jednostek Edtu|
|Warstwa Premium RS puli 250 jednostek DTU|Ogólnego przeznaczenia, 2 rdzenie wirtualne (4. generacji)|Jednostki Edtu puli standardowej 250|
|Warstwa Premium RS puli 500 jednostek DTU|Ogólnego przeznaczenia 4 rdzenie wirtualne (4. generacji)|Jednostki Edtu puli standardowej 500|
|Warstwa Premium RS puli 1000 jednostek DTU|Ogólnego przeznaczenia 8 rdzeni (4. generacji)|Pula w warstwie standardowa 1000 jednostek Edtu|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Optymalizowanie oszczędności i wydajności dla obciążeń intensywnie korzystających z operacji We/Wy

Zalecamy przeprowadzenie migracji pojedynczej bazy danych intensywnie korzystających z operacji We/Wy na warstwie oparty na rdzeniach wirtualnych o dużej skali, obecnie w wersji zapoznawczej i intensywnie korzystających z operacji We/Wy puli bazy danych do naszych ogólnie dostępne warstwy krytyczne dla działania firmy, w celu optymalnego połączenia wydajności i kosztów.  Następujące oparty na rdzeniach wirtualnych opcje będzie obsługa lub poprawić wydajność bieżącej i może zaoszczędzić pieniądze, po połączeniu z korzyścią użycia hybrydowego platformy Azure i zastrzeżone ceny pojemności.

|**Jeśli jesteś obecnie włączone...**|**Migrowanie do porównywalnych oparty na rdzeniach wirtualnych...**|**Migrowanie do porównywalnych oparty na jednostkach DTU...**|
|---|---|---|
|Premium RS 1|(Wersja zapoznawcza) 1 na dużą skalę — rdzeń wirtualny (4. generacji) lub Business krytyczne 1 rdzeń wirtualny (4. generacji)|W wersjach Premium 1|
|Premium RS 2|(Wersja zapoznawcza) W Hiperskali 2 rdzenie wirtualne (4. generacji) lub biznesowe krytyczne 2 rdzenie wirtualne (4. generacji|Premium 2|
|Premium RS 4|(Wersja zapoznawcza) W Hiperskali 4 rdzenie wirtualne (4. generacji) lub biznesowe krytyczne 4 rdzenie wirtualne (4. generacji)|Premium 4
|Premium RS 6|(Wersja zapoznawcza) Rdzenie wirtualne w Hiperskali 6 (4. generacji) lub rdzeni wirtualnych 6 krytyczne biznesowych (4. generacji)|Premium 6|

|**Jeśli jesteś obecnie włączone...**|**Migrowanie do porównywalnych oparty na rdzeniach wirtualnych...**|**Migrowanie do porównywalnych oparty na jednostkach DTU...**|
|---|---|---|
|Warstwa Premium RS puli 125 jednostek DTU|Rdzenie 2 krytyczne biznesowych (4. generacji)|Jednostki Edtu puli 125 — wersja Premium|
|Warstwa Premium RS puli 250 jednostek DTU|Rdzenie 2 krytyczne biznesowych (4. generacji)|Jednostki Edtu puli 250 — wersja Premium|
|Warstwa Premium RS puli 500 jednostek DTU|Rdzenie wirtualne 4 krytyczne biznesowych (4. generacji)|Jednostki Edtu puli 500 — wersja Premium|
|Warstwa Premium RS puli 1000 jednostek DTU|Biznesowe krytyczne 8 rdzeni (4. generacji)|Jednostki Edtu puli 1000 — wersja Premium|

## <a name="take-advantage-of-our-new-offers"></a>Skorzystaj z zalet naszej nowej oferty

Nasze warstwy usług, w modelu zakupu opartego na rdzeniach wirtualnych są uprawnieni do skorzystania z oferty specjalne, które mogą zaoszczędzić do 80% w porównaniu ze cenowej dołączona licencja. Użyj licencji programu SQL Server Standard lub Enterprise edition z pakietem Software Assurance można zapisać do 55% w porównaniu ze oferowała objęte licencją ceny [korzyść użycia hybrydowego platformy Azure dla programu SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Można połączyć korzyści użycia hybrydowego za pomocą [wydajności rezerwowej usługi Azure SQL Database](sql-database-reserved-capacity.md) ceny i zapisać maksymalnie 80%, gdy zatwierdzisz ponoszonych z góry się na konto lub trzy lata termin.  Aktywuj korzyści z obu już dziś w witrynie Azure portal.

Jeśli masz jakieś pytania lub uwagi w sprawie tej zmiany, lub jeśli potrzebujesz pomocy migracji, skontaktuj się z [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migracja z warstwy Premium RS usługi do warstwy usług w liczby jednostek DTU lub modelu rdzenia wirtualnego

### <a name="migration-of-a-database"></a>Migracja bazy danych

Migrowanie bazy danych z usługi Premium RS warstwy do warstwy usług, albo wartość DTU lub modelu rdzenia wirtualnego jest podobny do uaktualnienia lub zmiany na starszą wersję między warstwami usług w warstwie Premium RS.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Użycie kopii bazy danych w celu przekonwertowania bazy danych Premium RS do bazy danych oparty na jednostkach DTU lub oparty na rdzeniach wirtualnych

Możesz skopiować dowolną bazę danych o rozmiarze obliczeniowych warstwy Premium RS do bazy danych o rozmiarze obliczeniowych oparty na jednostkach DTU lub oparty na rdzeniach wirtualnych bez ograniczeń lub specjalne sekwencjonowania, tak długo, jak rozmiar obliczeń docelowej obsługuje maksymalny rozmiar bazy danych źródłowej bazy danych. Kopiowanie bazy danych tworzy migawkę danych od godziny rozpoczęcia operacji kopiowania i nie wykonuje synchronizację danych między źródłowym a docelowym.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szczegółowe informacje na temat określonych zasobów obliczeniowych, rozmiary i opcje rozmiaru magazynu jest dostępny dla pojedynczej bazy danych, zobacz [limity zasobów opartych na rdzeniach wirtualnych bazy danych SQL Database dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)
- Aby uzyskać szczegółowe informacje na temat określonych zasobów obliczeniowych, rozmiary i opcje rozmiaru magazynu jest dostępne dla pul elastycznych, zobacz [bazy danych SQL Database oparty na rdzeniach wirtualnych zasobów limity dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md).
