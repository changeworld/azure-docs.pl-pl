---
title: Limity zasobów vCore - pojedyncza baza danych
description: Na tej stronie opisano niektóre typowe limity zasobów vCore dla pojedynczej bazy danych w bazie danych SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/11/2020
ms.openlocfilehash: 38d44f5a2ce22de15cb14ea5aa7a9ca1ea7c03cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481069"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu w ramach vCore

Ten artykuł zawiera szczegółowe limity zasobów dla pojedynczych baz danych usługi Azure SQL Database przy użyciu modelu zakupu vCore.

Aby uzyskać limity modelu zakupu jednostek DTU dla pojedynczych baz danych na serwerze bazy danych SQL, zobacz [Omówienie limitów zasobów na serwerze bazy danych SQL](sql-database-resource-limits-database-server.md).

Warstwę usług, rozmiar obliczeń i ilość miejsca do magazynowania można ustawić dla pojedynczej bazy danych za pomocą [portalu Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)Portal , [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [interfejsu wiersza polecenia platformy Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)lub interfejsu API [REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Aby uzyskać wskazówki i zagadnienia dotyczące skalowania, zobacz [Skalowanie pojedynczej bazy danych](sql-database-single-database-scale.md).

## <a name="general-purpose---serverless-compute---gen5"></a>Przeznaczenie ogólne — obliczenia bezserwerowe - Gen5

Warstwa [obliczeniowa bezserwerowa](sql-database-serverless.md) jest obecnie dostępna tylko na sprzęcie Gen5.

### <a name="gen5-compute-generation-part-1"></a>Generacja mocy obliczeniowej Gen5 (część 1)

|Rozmiar obliczeń|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Generowanie obliczeń|Gen5|Gen5|Gen5|Gen5|Gen5|
|Min-max rów wirtualnych|0.5-1|0.5-2|0.5-4|0.75-6|1.0-8|
|Pamięć min-max (GB)|2.02-3|2.05-6|2.10-12|2.25-18|3.00-24|
|Min opóźnienie automatycznej pauzy (minuty)|60|60|60|60|60|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|512|1024|1024|1024|1536|
|Maksymalny rozmiar dziennika (GB)|154|307|307|307|461|
|Maksymalny rozmiar danych tempDB (GB)|32|64|128|192|256|
|Typ magazynu|Zdalny ssd|Zdalny ssd|Zdalny ssd|Zdalny ssd|Zdalny ssd|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|
|Maksymalna liczba IOPS danych *|320|640|1280|1920|2560|
|Maksymalna szybkość dzienniku (MB/s)|3.8|7,5|15|22.5|30|
|Maksymalna liczba równoczesnych pracowników (żądania)|75|150|300|450|600|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|
|Liczba replik|1|1|1|1|1|
|Wielu AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|

\*Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generacja mocy obliczeniowej Gen5 (część 2)

|Rozmiar obliczeń|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Generowanie obliczeń|Gen5|Gen5|Gen5|Gen5|
|Min-max rów wirtualnych|1.25-10|1.50-12|1.75-14|2.00-16|
|Pamięć min-max (GB)|3.75-30|4.50-36|5.25-42|6.00-48|
|Min opóźnienie automatycznej pauzy (minuty)|60|60|60|60|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|1536|3072|3072|3072|
|Maksymalny rozmiar dziennika (GB)|461|461|461|922|
|Maksymalny rozmiar danych tempDB (GB)|320|384|448|512|
|Typ magazynu|Zdalny ssd|Zdalny ssd|Zdalny ssd|Zdalny ssd|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|
|Maksymalna liczba IOPS danych *|3200|3840|4480|5120|
|Maksymalna szybkość dzienniku (MB/s)|30|30|30|30|
|Maksymalna liczba równoczesnych pracowników (żądania)|750|900|1050|1200|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|
|Liczba replik|1|1|1|1|
|Wielu AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|

\*Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="hyperscale---provisioned-compute---gen4"></a>Hiperskala — aprowizowana moc obliczeniowa — Gen4

### <a name="gen4-compute-generation-part-1"></a>Generacja mocy obliczeniowej Gen4 (część 1)

|Poziom wydajności|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Rejy wirtualne|1|2|3|4|5|6|
|Pamięć (GB)|7|14|21|28|35|42|
|[RBPEX ( RBPEX )](sql-database-service-tier-hyperscale.md#compute) Rozmiar|3-krotna pamięć|3-krotna pamięć|3-krotna pamięć|3-krotna pamięć|3-krotna pamięć|3-krotna pamięć|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (TB)|100 |100 |100 |100 |100 |100|
|Maksymalny rozmiar dziennika (TB)|1 |1 |1 |1 |1 |1 |
|Maksymalny rozmiar danych tempDB (GB)|32|64|96|128|160|192|
|Typ magazynu| [Uwaga 1](#notes) |[Uwaga 1](#notes)|[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |
|Maksymalna liczba IOPS danych *|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|
|Maksymalna szybkość dzienniku (MB/s)|100 |100 |100 |100 |100 |100 |
|Opóźnienie we/wy (przybliżone)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|
|Maksymalna liczba równoczesnych pracowników (żądania)|200|400|600|800|1000|1200|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|
|Repliki pomocnicze|0-4|0-4|0-4|0-4|0-4|0-4|
|Wielu AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|
|Przechowywanie kopii zapasowych magazynu|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|
|||

### <a name="gen4-compute-generation-part-2"></a>Generacja mocy obliczeniowej Gen4 (część 2)

|Poziom wydajności|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Generowanie obliczeń|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Rejy wirtualne|7|8|9|10|16|24|
|Pamięć (GB)|49|56|63|70|112|159.5|
|[RBPEX ( RBPEX )](sql-database-service-tier-hyperscale.md#compute) Rozmiar|3-krotna pamięć|3-krotna pamięć|3-krotna pamięć|3-krotna pamięć|3-krotna pamięć|3-krotna pamięć|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (TB)|100 |100 |100 |100 |100 |100 |
|Maksymalny rozmiar dziennika (TB)|1 |1 |1 |1 |1 |1 |
|Maksymalny rozmiar danych tempDB (GB)|224|256|288|320|512|768|
|Typ magazynu| [Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |
|Maksymalna liczba IOPS danych *|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|
|Maksymalna szybkość dzienniku (MB/s)|100 |100 |100 |100 |100 |100 |
|Opóźnienie we/wy (przybliżone)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|
|Maksymalna liczba równoczesnych pracowników (żądania)|1400|1600|1800|2000|3200|4800|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|
|Repliki pomocnicze|0-4|0-4|0-4|0-4|0-4|0-4|
|Wielu AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|
|Przechowywanie kopii zapasowych magazynu|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|
|||

\*Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="hyperscale---provisioned-compute---gen5"></a>Hiperskala — aprowizowana moc obliczeniowa — Gen5

### <a name="gen5-compute-generation-part-1"></a>Generacja mocy obliczeniowej Gen5 (część 1)

|Poziom wydajności|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Generowanie obliczeń|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Rejy wirtualne|2|4|6|8|10|12|14|
|Pamięć (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|[RBPEX ( RBPEX )](sql-database-service-tier-hyperscale.md#compute) Rozmiar|3-krotna pamięć|3-krotna pamięć|3-krotna pamięć|3-krotna pamięć|3-krotna pamięć|3-krotna pamięć|3-krotna pamięć|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (TB)|100 |100 |100 |100 |100 |100 |100|
|Maksymalny rozmiar dziennika (TB)|1 |1 |1 |1 |1 |1 |1 |
|Maksymalny rozmiar danych tempDB (GB)|64|128|192|256|320|384|448|
|Typ magazynu| [Uwaga 1](#notes) |[Uwaga 1](#notes)|[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |
|Maksymalna liczba IOPS danych *|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|
|Maksymalna szybkość dzienniku (MB/s)|100 |100 |100 |100 |100 |100 |100 |
|Opóźnienie we/wy (przybliżone)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|
|Maksymalna liczba równoczesnych pracowników (żądania)|200|400|600|800|1000|1200|1400|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Repliki pomocnicze|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Wielu AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Przechowywanie kopii zapasowych magazynu|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|
|||

\*Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generacja mocy obliczeniowej Gen5 (część 2)

|Poziom wydajności|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Generowanie obliczeń|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Rejy wirtualne|16|18|20|24|32|40|80|
|Pamięć (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|[RBPEX ( RBPEX )](sql-database-service-tier-hyperscale.md#compute) Rozmiar|3-krotna pamięć|3-krotna pamięć|3-krotna pamięć|3-krotna pamięć|3-krotna pamięć|3-krotna pamięć|3-krotna pamięć|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (TB)|100 |100 |100 |100 |100 |100 |100 |
|Maksymalny rozmiar dziennika (TB)|1 |1 |1 |1 |1 |1 |1 |
|Maksymalny rozmiar danych tempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ magazynu| [Uwaga 1](#notes) |[Uwaga 1](#notes)|[Uwaga 1](#notes)|[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |
|Maksymalna liczba IOPS danych *|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|
|Maksymalna szybkość dzienniku (MB/s)|100 |100 |100 |100 |100 |100 |100 |
|Opóźnienie we/wy (przybliżone)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|
|Maksymalna liczba równoczesnych pracowników (żądania)|1600|1800|2000|2400|3200|4000|8000|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Repliki pomocnicze|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Wielu AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Przechowywanie kopii zapasowych magazynu|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|
|||

\*Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

#### <a name="notes"></a>Uwagi

**Uwaga 1:** Hiperskala to wielowarstwowa architektura z oddzielnymi składnikami obliczeniowymi i magazynowymi: [Architektura warstwy usługi hiperskali](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**Uwaga 2:** Architektura wielowarstwowa na dużą skalę ma buforowanie na wielu poziomach. Skuteczne we/wy będą zależeć od obciążenia.

**Uwaga 3:** Opóźnienie wynosi 1-2 ms dla danych w pamięci podręcznej opartej na SSD RBPEX na replikach obliczeniowych, która buforuje najczęściej używane strony danych. Większe opóźnienie danych pobieranych z serwerów stron.

## <a name="general-purpose---provisioned-compute---gen4"></a>Przeznaczenie ogólne — aprowizowana moc obliczeniowa — Gen4

> [!IMPORTANT]
> Nowe bazy danych Gen4 nie są już obsługiwane w regionach Australia Wschodnia lub Brazylia Południowa.

### <a name="gen4-compute-generation-part-1"></a>Generacja mocy obliczeniowej Gen4 (część 1)

|Rozmiar obliczeń|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Rejy wirtualne|1|2|3|4|5|6|
|Pamięć (GB)|7|14|21|28|35|42|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|1536|1536|3072|
|Maksymalny rozmiar dziennika (GB)|307|307|461|461|461|922|
|Maksymalny rozmiar danych tempDB (GB)|32|64|96|128|160|192|
|Typ magazynu|Zdalny ssd|Zdalny ssd|Zdalny ssd|Zdalny ssd|Zdalny ssd|Zdalny ssd|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|
|Maksymalna liczba IOPS danych *|320|640|960|1280|1600|1920|
|Maksymalna szybkość dzienniku (MB/s)|3,75|7,5|11.25|15|18.75|22.5|
|Maksymalna liczba równoczesnych pracowników (żądania)|200|400|600|800|1000|1200|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|
|Liczba replik|1|1|1|1|1|1|
|Wielu AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|

\*Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Generacja mocy obliczeniowej Gen4 (część 2)

|Rozmiar obliczeń|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Rejy wirtualne|7|8|9|10|16|24|
|Pamięć (GB)|49|56|63|70|112|159.5|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|3072|3072|3072|3072|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|922|922|1229|1229|
|Maksymalny rozmiar danych tempDB (GB)|224|256|288|320|512|768|
|Typ magazynu|Zdalny ssd|Zdalny ssd|Zdalny ssd|Zdalny ssd|Zdalny ssd|Zdalny ssd|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)
|Maksymalna liczba IOPS danych *|2240|2560|2880|3200|5120|7680|
|Maksymalna szybkość dzienniku (MB/s)|26.3|30|30|30|30|30|
|Maksymalna liczba równoczesnych pracowników (żądania)|1400|1600|1800|2000|3200|4800|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|
|Liczba replik|1|1|1|1|1|1|
|Wielu AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|

\*Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---gen5"></a>Przeznaczenie ogólne — aprowizowana moc obliczeniowa — Gen5

### <a name="gen5-compute-generation-part-1"></a>Generacja mocy obliczeniowej Gen5 (część 1)

|Rozmiar obliczeń|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Rejy wirtualne|2|4|6|8|10|12|14|
|Pamięć (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maksymalny rozmiar dziennika (GB)|307|307|461|461|461|922|922|
|Maksymalny rozmiar danych tempDB (GB)|64|128|192|256|320|384|384|
|Typ magazynu|Zdalny ssd|Zdalny ssd|Zdalny ssd|Zdalny ssd|Zdalny ssd|Zdalny ssd|Zdalny ssd|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|
|Maksymalna liczba IOPS danych *|640|1280|1920|2560|3200|3840|4480|
|Maksymalna szybkość dzienniku (MB/s)|7,5|15|22.5|30|30|30|30|
|Maksymalna liczba równoczesnych pracowników (żądania)|200|400|600|800|1000|1200|1400|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Liczba replik|1|1|1|1|1|1|1|
|Wielu AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|

\*Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generacja mocy obliczeniowej Gen5 (część 2)

|Rozmiar obliczeń|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Rejy wirtualne|16|18|20|24|32|40|80|
|Pamięć (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|922|1229|1229|1229|1229|
|Maksymalny rozmiar danych tempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ magazynu|Zdalny ssd|Zdalny ssd|Zdalny ssd|Zdalny ssd|Zdalny ssd|Zdalny ssd|Zdalny ssd|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|
|Maksymalna liczba IOPS danych *|5120|5760|6400|7680|10240|12800|25600|
|Maksymalna szybkość dzienniku (MB/s)|30|30|30|30|30|30|30|
|Maksymalna liczba równoczesnych pracowników (żądania)|1600|1800|2000|2400|3200|4000|8000|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Liczba replik|1|1|1|1|1|1|1|
|Wielu AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|

\*Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Przeznaczenie ogólne — aprowizowana moc obliczeniowa — seria Fsv2

### <a name="fsv2-series-compute-generation-preview"></a>Generowanie obliczeń serii Fsv2 (wersja zapoznawcza)

|Rozmiar obliczeń|GP_Fsv2_72|
|:--- | --: |
|Generowanie obliczeń|Seria Fsv2|
|Rejy wirtualne|72|
|Pamięć (GB)|136.2|
|Obsługa magazynu kolumn|Tak|
|Pamięć OLTP w pamięci (GB)|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|4096|
|Maksymalny rozmiar dziennika (GB)|1024|
|Maksymalny rozmiar danych tempDB (GB)|333|
|Typ magazynu|Zdalny ssd|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (czytaj)|
|Maksymalna liczba IOPS danych *|12,800|
|Maksymalna szybkość dzienniku (MB/s)|30|
|Maksymalna liczba równoczesnych pracowników (żądania)|3600|
|Maksymalna liczba współbieżnych logowań|3600|
|Maksymalna liczba współbieżnych sesji|30,000|
|Liczba replik|1|
|Wielu AZ|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|

\*Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen4"></a>Krytyczne dla firmy — obliczenia aprowizacji — Gen4

> [!IMPORTANT]
> Nowe bazy danych Gen4 nie są już obsługiwane w regionach Australia Wschodnia lub Brazylia Południowa.

### <a name="gen4-compute-generation-part-1"></a>Generacja mocy obliczeniowej Gen4 (część 1)

|Rozmiar obliczeń|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Rejy wirtualne|1|2|3|4|5|6|
|Pamięć (GB)|7|14|21|28|35|42|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|1|2|3|4|5|6|
|Typ magazynu|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|1024|1024|
|Maksymalny rozmiar dziennika (GB)|307|307|307|307|307|307|
|Maksymalny rozmiar danych tempDB (GB)|32|64|96|128|160|192|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|
|Maksymalna liczba IOPS danych *|4000|8000|12 000|16 000|20 000|24,000|
|Maksymalna szybkość dzienniku (MB/s)|8|16|24|32|40|48|
|Maksymalna liczba równoczesnych pracowników (żądania)|200|400|600|800|1000|1200|
|Maksymalna liczba współbieżnych logowań|200|400|600|800|1000|1200|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|
|Liczba replik|4|4|4|4|4|4|
|Wielu AZ|Tak|Tak|Tak|Tak|Tak|Tak|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|

\*Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Generacja mocy obliczeniowej Gen4 (część 2)

|Rozmiar obliczeń|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Rejy wirtualne|7|8|9|10|16|24|
|Pamięć (GB)|49|56|63|70|112|159.5|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|7|8|9.5|11|20|36|
|Typ magazynu|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|1024|1024|
|Maksymalny rozmiar dziennika (GB)|307|307|307|307|307|307|
|Maksymalny rozmiar danych tempDB (GB)|224|256|288|320|512|768|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|
|Maksymalna liczba iOPS danych |28,000|32,000|36,000|40 000|64,000|76 800|
|Maksymalna szybkość dzienniku (MB/s)|56|64|64|64|64|64|
|Maksymalna liczba równoczesnych pracowników (żądania)|1400|1600|1800|2000|3200|4800|
|Maksymalna liczba równoczesnych logowań (żądań)|1400|1600|1800|2000|3200|4800|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|
|Liczba replik|4|4|4|4|4|4|
|Wielu AZ|Tak|Tak|Tak|Tak|Tak|Tak|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|

\*Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen5"></a>Krytyczne dla firmy — obliczenia aprowizacji — Gen5

### <a name="gen5-compute-generation-part-1"></a>Generacja mocy obliczeniowej Gen5 (część 1)

|Rozmiar obliczeń|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Rejy wirtualne|2|4|6|8|10|12|14|
|Pamięć (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|1.57|3.14|4.71|6.28|8.65|11.02|13.39|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maksymalny rozmiar dziennika (GB)|307|307|461|461|461|922|922|
|Maksymalny rozmiar danych tempDB (GB)|64|128|192|256|320|384|448|
|Typ magazynu|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|
|Maksymalna liczba IOPS danych *|8000|16 000|24,000|32,000|40 000|48,000|56,000|
|Maksymalna szybkość dzienniku (MB/s)|24|48|72|96|96|96|96|
|Maksymalna liczba równoczesnych pracowników (żądania)|200|400|600|800|1000|1200|1400|
|Maksymalna liczba współbieżnych logowań|200|400|600|800|1000|1200|1400|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Liczba replik|4|4|4|4|4|4|4|
|Wielu AZ|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|

\*Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generacja mocy obliczeniowej Gen5 (część 2)

|Rozmiar obliczeń|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Rejy wirtualne|16|18|20|24|32|40|80|
|Pamięć (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|15,77|18.14|20.51|25.25|37.94|52.23|131.64|
|Maksymalny rozmiar danych (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|922|1229|1229|1229|1229|
|Maksymalny rozmiar danych tempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ magazynu|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|
|Maksymalna liczba IOPS danych *|64,000|72,000|80 000|96,000|128,000|160 000|204,800|
|Maksymalna szybkość dzienniku (MB/s)|96|96|96|96|96|96|96|
|Maksymalna liczba równoczesnych pracowników (żądania)|1600|1800|2000|2400|3200|4000|8000|
|Maksymalna liczba współbieżnych logowań|1600|1800|2000|2400|3200|4000|8000|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Liczba replik|4|4|4|4|4|4|4|
|Wielu AZ|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|

\*Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---m-series"></a>Krytyczne dla firmy — obliczenia aprowizacji — seria M

### <a name="m-series-compute-generation-preview"></a>Generowanie obliczeń serii M (wersja zapoznawcza)

|Rozmiar obliczeń|BC_M_128|
|:--- | --: |
|Generowanie obliczeń|Seria M|
|Rejy wirtualne|128|
|Pamięć (GB)|3767.1|
|Obsługa magazynu kolumn|Tak|
|Pamięć OLTP w pamięci (GB)|1768|
|Maksymalny rozmiar danych (GB)|4096|
|Maksymalny rozmiar dziennika (GB)|2048|
|Maksymalny rozmiar danych tempDB (GB)|4096|
|Typ magazynu|Lokalny system SSD|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (czytaj)|
|Maksymalna liczba IOPS danych *|160 000|
|Maksymalna szybkość dzienniku (MB/s)|264|
|Maksymalna liczba równoczesnych pracowników (żądania)|12,800|
|Maksymalna liczba współbieżnych logowań|12,800|
|Maksymalna liczba współbieżnych sesji|30000|
|Liczba replik|4|
|Wielu AZ|Tak|
|Skalowanie w górę odczytu|Tak|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|

\*Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych, aby odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [Zarządzanie miejscem na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać limity zasobów jednostek DTU dla pojedynczej bazy danych, zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu jednostek DTU](sql-database-dtu-resource-limits-single-databases.md)
- W przypadku limitów zasobów w ybramki dla puli elastycznych zobacz [limity zasobów dla pul elastycznych przy użyciu modelu zakupu p-po,](sql-database-vcore-resource-limits-elastic-pools.md)
- Aby uzyskać limity zasobów DTU dla pul elastycznych, zobacz [limity zasobów dla pul elastycznych przy użyciu modelu zakupu usługi DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- W przypadku limitów zasobów dla wystąpień zarządzanych zobacz [limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md).
- Aby uzyskać informacje na temat ogólnych limitów platformy Azure, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Aby uzyskać informacje na temat limitów zasobów na serwerze bazy danych, zobacz [omówienie limitów zasobów na serwerze bazy danych SQL, aby](sql-database-resource-limits-database-server.md) uzyskać informacje o limitach na poziomie serwera i subskrypcji.
