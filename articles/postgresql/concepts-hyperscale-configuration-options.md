---
title: Opcje konfiguracji — hiperskala (Citus) — usługa Azure Database for PostgreSQL
description: Opcje dla grupy serwerów w hiperskali (Citus), w tym obliczenia węzłów, magazyn i regiony.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 4/6/2020
ms.openlocfilehash: a2c376ec2bd1f03b626c11b0d6a6c3850c9ef8c4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804592"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Usługa Azure Database for PostgreSQL — opcje konfiguracji w hiperskali (Citus)

## <a name="compute-and-storage"></a>Obliczenia i magazyn
 
Ustawienia obliczeń i magazynu można wybrać niezależnie dla węzłów procesu roboczego i węzła koordynatora w grupie serwerów w hiperskali (Citus).  Zasoby obliczeniowe są dostarczane jako rdzenie wirtualne, które reprezentują logiczny procesor procesora podstawowego sprzętu. Rozmiar magazynu do inicjowania obsługi administracyjnej odnosi się do pojemności dostępnej dla koordynatora i węzłów procesu roboczego w grupie serwerów w hiperskali (Citus). Magazyn zawiera pliki bazy danych, pliki tymczasowe, dzienniki transakcji i dzienniki serwera Postgres.
 
|                       | Węzeł pracownika           | Węzeł koordynatora      |
|-----------------------|-----------------------|-----------------------|
| Moc obliczeniowe, cory wirtualne       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Pamięć na vCore, GiB | 8                     | 4                     |
| Rozmiar pamięci masowej, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Typ magazynu          | Ogólnego przeznaczenia (SSD) | Ogólnego przeznaczenia (SSD) |
| Liczba operacji we/wy na sekundę                  | Do 3 IOPS/GiB      | Do 3 IOPS/GiB      |

Całkowita ilość pamięci RAM w jednym węźle hiperskali (Citus) jest oparta na wybranej liczbie koperów wirtualnych.

| Rejy wirtualne | Jeden węzeł pracownika, GiB RAM | Węzeł koordynatora, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

Całkowita ilość aprowizowania magazynu definiuje również pojemność we/wy dostępną dla każdego pracownika i węzła koordynatora.

| Rozmiar pamięci masowej, TiB | Maksymalna liczba we/wy |
|-------------------|--------------|
| 0,5               | 1536        |
| 1                 | 3072        |
| 2                 | 6,148        |

Dla całego klastra Hiperskali (Citus) zagregowane operacje We/Wy działają na następujące wartości:

| Węzły procesu roboczego | 0.5 TiB, całkowite IOPS | 1 TiB, całkowita liczba IOPS | 2 TiB, całkowite IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3072               | 6144             | 12,296            |
| 3            | 4,608               | 9,216             | 18,444            |
| 4            | 6144               | 12,288            | 24,592            |
| 5            | 7,680               | 15,360            | 30,740            |
| 6            | 9,216               | 18,432            | 36,888            |
| 7            | 10,752              | 21,504            | 43,036            |
| 8            | 12,288              | 24,576            | 49,184            |
| 9            | 13,824              | 27,648            | 55,332            |
| 10           | 15,360              | 30,720            | 61,480            |
| 11           | 16,896              | 33,792            | 67,628            |
| 12           | 18,432              | 36,864            | 73,776            |
| 13           | 19,968              | 39,936            | 79,924            |
| 14           | 21,504              | 43,008            | 86,072            |
| 15           | 23,040              | 46,080            | 92,220            |
| 16           | 24,576              | 49,152            | 98,368            |
| 17           | 26,112              | 52,224            | 104,516           |
| 18           | 27,648              | 55,296            | 110,664           |
| 19           | 29,184              | 58,368            | 116,812           |
| 20           | 30,720              | 61,440            | 122,960           |

## <a name="regions"></a>Regiony
Grupy serwerów o przeskalowej (Citus) są dostępne w następujących regionach platformy Azure:

* Ameryka Północna i Południowa: 
    * Kanada Środkowa
    * Środkowe stany USA
    * Wschodnie stany USA
    * Wschodnie stany USA 2
    * Północno-środkowe stany USA
    * Zachodnie stany USA 2
* Azja i Pacyfik:
    * Australia Wschodnia
    * Japonia Wschodnia
    * Korea Środkowa
    * Azja Południowo-Wschodnia
* Europie:
    * Europa Północna
    * Południowe Zjednoczone Królestwo
    * Europa Zachodnia

Niektóre z tych regionów mogą nie być początkowo aktywowane we wszystkich subskrypcjach platformy Azure. Jeśli chcesz użyć regionu z powyższej listy i nie widzisz go w subskrypcji lub jeśli chcesz użyć regionu nienajmnika na tej liście, otwórz [żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pricing"></a>Cennik
Aby uzyskać najbardziej aktualne informacje o cenach, zobacz [stronę cennika](https://azure.microsoft.com/pricing/details/postgresql/)usług .
Aby wyświetlić koszt żądanej konfiguracji, [witryna Azure portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) pokazuje koszt miesięczny na karcie **Konfigurowanie** na podstawie wybranych opcji. Jeśli nie masz subskrypcji platformy Azure, możesz użyć kalkulatora cen platformy Azure, aby uzyskać szacunkową cenę. W [witrynie kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) wybierz pozycję **Dodaj elementy**, rozwiń kategorię **Bazy danych** i wybierz pozycję Azure Database **for PostgreSQL — Hyperscale (Citus),** aby dostosować opcje.
 
## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [utworzyć grupę serwerów o przeskalowej (Citus) w portalu.](quickstart-create-hyperscale-portal.md)
