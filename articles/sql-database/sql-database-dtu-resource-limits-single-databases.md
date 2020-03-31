---
title: Zasoby jednostek DTU ograniczają pojedyncze bazy danych
description: Na tej stronie opisano niektóre typowe limity zasobów jednostek DTU dla pojedynczych baz danych w bazie danych SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: a4c435b4874301fe6fb804085c5b265954cd4f5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256317"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model"></a>Resource limits for single databases using the DTU purchasing model (Limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu DTU)

Ten artykuł zawiera szczegółowe limity zasobów dla pojedynczych baz danych usługi Azure SQL Database przy użyciu modelu zakupu jednostek DTU.

Aby uzyskać limity zasobów modelu zakupu przez jednostka DTU dla pul elastycznych, zobacz [Limity zasobów DTU — pule elastyczne](sql-database-dtu-resource-limits-elastic-pools.md). Aby zapoznać się z limitami zasobów w ramach programu vCore, zobacz [limity zasobów vCore — pojedyncze bazy danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów vCore — pule elastyczne](sql-database-vcore-resource-limits-elastic-pools.md). Aby uzyskać więcej informacji dotyczących różnych modeli zakupów, zobacz [Kupowanie modeli i warstw usług](sql-database-purchase-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Pojedyncza baza danych: rozmiary magazynu i rozmiary obliczeń

W poniższych tabelach przedstawiono zasoby dostępne dla pojedynczej bazy danych w każdej warstwie usług i rozmiar obliczeń. Warstwę usług, rozmiar obliczeń i ilość miejsca do magazynowania można ustawić dla pojedynczej bazy danych za pomocą [portalu Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)Portal , [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [interfejsu wiersza polecenia platformy Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)lub interfejsu API [REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Aby uzyskać wskazówki i zagadnienia dotyczące [skalowania, zobacz Skalowanie pojedynczej bazy danych](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>Warstwa usługi Podstawowa

| **Rozmiar obliczeń** | **Podstawowa (Basic)** |
| :--- | --: |
| Maksymalna liczba jednostek DTU | 5 |
| Dołączone miejsce do magazynowania (GB) | 2 |
| Maksymalna liczba opcji pamięci masowej (GB) | 2 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) |Nie dotyczy |
| Maksymalna liczba równoczesnych pracowników (żądania) | 30 |
| Maksymalna liczba współbieżnych sesji | 300 |
|||

> [!IMPORTANT]
> Warstwa usług Basic zapewnia mniej niż jeden rów wirtualny (CPU).  W przypadku obciążeń intensywnie korzystających z procesora CPU zalecana jest warstwa usług S3 lub większa. 
>
>Jeśli chodzi o przechowywanie danych, warstwa usługi Basic jest umieszczana w obiektach blob strony standardowej. Standardowe obiekty blob strony używają nośników pamięci masowej opartych na dysku twardym (HDD) i najlepiej nadają się do tworzenia, testowania i innych rzadko używanych obciążeń, które są mniej wrażliwe na zmienność wydajności.
>

### <a name="standard-service-tier"></a>Warstwa usługi Standardowa

| **Rozmiar obliczeń** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Maksymalna liczba jednostek DTU | 10 | 20 | 50 | 100 |
| Dołączone miejsce do magazynowania (GB) | 250 | 250 | 250 | 250 |
| Maksymalna liczba opcji pamięci masowej (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie dotyczy |
| Maksymalna liczba równoczesnych pracowników (żądania)| 60 | 90 | 120 | 200 |
| Maksymalna liczba współbieżnych sesji |600 | 900 | 1200 | 2400 |
||||||

> [!IMPORTANT]
> Standardowe warstwy S0, S1 i S2 zapewniają mniej niż jeden r&(CPU).  W przypadku obciążeń intensywnie korzystających z procesora CPU zalecana jest warstwa usług S3 lub większa. 
>
>Jeśli chodzi o przechowywanie danych, warstwy usług Standard S0 i S1 są umieszczane w obiektach blob strony standardowej. Standardowe obiekty blob strony używają nośników pamięci masowej opartych na dysku twardym (HDD) i najlepiej nadają się do tworzenia, testowania i innych rzadko używanych obciążeń, które są mniej wrażliwe na zmienność wydajności.
>

### <a name="standard-service-tier-continued"></a>Standardowa warstwa usług (ciąg dalszy)

| **Rozmiar obliczeń** | **S4** | **S6** | **S7** | **S9** | **S12 (w yszer.** |
| :--- |---:| ---:|---:|---:|---:|
| Maksymalna liczba jednostek DTU | 200 | 400 | 800 | 1600 | 3000 |
| Dołączone miejsce do magazynowania (GB) | 250 | 250 | 250 | 250 | 250 |
| Maksymalna liczba opcji pamięci masowej (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie dotyczy |Nie dotyczy |
| Maksymalna liczba równoczesnych pracowników (żądania)| 400 | 800 | 1600 | 3200 |6000 |
| Maksymalna liczba współbieżnych sesji |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Warstwa usługi Premium

| **Rozmiar obliczeń** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Maksymalna liczba jednostek DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Dołączone miejsce do magazynowania (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| Maksymalna liczba opcji pamięci masowej (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maksymalna liczba równoczesnych pracowników (żądania)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maksymalna liczba współbieżnych sesji | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\*Od 1024 GB do 4096 GB w przyrostach 256 GB

> [!IMPORTANT]
> Ponad 1 TB pamięci masowej w warstwie Premium jest obecnie dostępna we wszystkich regionach z wyjątkiem: China East, China North, Germany Central, Germany Northeast, West Central US, US DoD regiony i us Government Central. W tych regionach maksymalna wielkość magazynu w warstwie Premium jest ograniczona do 1 TB.  Aby uzyskać więcej informacji, zobacz [bieżące ograniczenia poziomów P11–P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!NOTE]
> Aby `tempdb` uzyskać limity, zobacz [limity tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Następne kroki

- W przypadku limitów zasobów w ramach vCore dla pojedynczej bazy danych zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu w ramach vCore](sql-database-vcore-resource-limits-single-databases.md)
- W przypadku limitów zasobów w ybramki dla puli elastycznych zobacz [limity zasobów dla pul elastycznych przy użyciu modelu zakupu p-po,](sql-database-vcore-resource-limits-elastic-pools.md)
- Aby uzyskać limity zasobów DTU dla pul elastycznych, zobacz [limity zasobów dla pul elastycznych przy użyciu modelu zakupu usługi DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- W przypadku limitów zasobów dla wystąpień zarządzanych zobacz [limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md).
- Aby uzyskać informacje na temat ogólnych limitów platformy Azure, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Aby uzyskać informacje na temat limitów zasobów na serwerze bazy danych, zobacz [omówienie limitów zasobów na serwerze bazy danych SQL, aby](sql-database-resource-limits-database-server.md) uzyskać informacje o limitach na poziomie serwera i subskrypcji.
