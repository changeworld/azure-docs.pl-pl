---
title: Opcje wydajności — skalowanie (Citus) — Azure Database for PostgreSQL
description: Opcje grupy serwerów ze skalą (Citus), w tym obliczenia węzłów, magazyn i regiony.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 2ee3e661d6c01aa2e4f37ac9a70e00be5da5f794
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975639"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Opcje wydajności Azure Database for PostgreSQL i skalowania (Citus)

## <a name="compute-and-storage"></a>Obliczenia i magazyn
 
Ustawienia obliczeń i magazynu można wybrać niezależnie dla węzłów procesu roboczego i węzła koordynatora w grupie serwerów Citus.  Zasoby obliczeniowe są udostępniane jako rdzeni wirtualnych, które reprezentują logicznego procesora bazowego sprzętu. Rozmiar magazynu dla aprowizacji odnosi się do pojemności dostępnej dla koordynatora i węzłów procesu roboczego w grupie serwerów Citus. Magazyn obejmuje pliki bazy danych, pliki tymczasowe, dzienniki transakcji i Dzienniki serwera Postgres. Całkowita ilość dostępnego miejsca w magazynie definiuje również wydajność we/wy dostępną dla każdego węzła procesu roboczego i koordynatora.
 
|                       | Węzeł procesu roboczego           | Węzeł koordynatora      |
|-----------------------|-----------------------|-----------------------|
| Obliczenia, rdzeni wirtualnych       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Pamięć na rdzeń wirtualny, GiB | 8                     | 4                     |
| Rozmiar magazynu, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Typ usługi Storage          | Ogólnego przeznaczenia (SSD) | Ogólnego przeznaczenia (SSD) |
| Operacje wejścia/wyjścia                  | Do 3 operacji we/wy na sekundę/GiB      | Do 3 operacji we/wy na sekundę/GiB      |


## <a name="regions"></a>Regiony
Grupy serwerów ze skalowaniem (Citus) są dostępne w następujących regionach platformy Azure:

* Południowej
    * Kanada środkowa *
    * Wschodnie stany USA
    * Wschodnie stany USA 2
    * Północno-środkowe stany USA *
    * Zachodnie stany USA 2
* Azja i Pacyfik:
    * Australia Wschodnia *
    * Azja Południowo-Wschodnia
* Terenie
    * Europa Północna
    * Południowe Zjednoczone Królestwo
    * Europa Zachodnia

Regiony z gwiazdką (\*) nie obsługują jeszcze [wysokiej dostępności](concepts-hyperscale-high-availability.md).

## <a name="pricing"></a>Cennik
Najbardziej aktualne informacje o cenach można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/postgresql/)usługi.
Aby wyświetlić koszt dla wybranej konfiguracji, [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) przedstawia miesięczny koszt na karcie **Konfiguracja** na podstawie wybranych opcji. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z kalkulatora cen platformy Azure, aby uzyskać szacowaną cenę. W witrynie sieci Web [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) wybierz pozycję **Dodaj elementy**, rozwiń kategorię **bazy danych** , a następnie wybierz pozycję **Azure Database for PostgreSQL — Citus** , aby dostosować opcje.
 
## <a name="next-steps"></a>Następne kroki
Dowiedz się [, jak utworzyć grupę serwerów ze skalą (Citus) w portalu](quickstart-create-hyperscale-portal.md).
