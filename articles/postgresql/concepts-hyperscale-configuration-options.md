---
title: Opcje wydajności — skalowanie (Citus) — Azure Database for PostgreSQL
description: Opcje grupy serwerów ze skalą (Citus), w tym obliczenia węzłów, magazyn i regiony.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462415"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Opcje wydajności Azure Database for PostgreSQL i skalowania (Citus)

## <a name="compute-and-storage"></a>Obliczenia i magazyn
 
Ustawienia obliczeń i magazynu można wybrać niezależnie dla węzłów procesu roboczego i węzła koordynatora w grupie serwerów Citus.  Zasoby obliczeniowe są udostępniane jako rdzeni wirtualnych, które reprezentują logicznego procesora bazowego sprzętu. Rozmiar magazynu dla aprowizacji odnosi się do pojemności dostępnej dla koordynatora i węzłów procesu roboczego w grupie serwerów Citus. Magazyn obejmuje pliki bazy danych, pliki tymczasowe, dzienniki transakcji i Dzienniki serwera Postgres. Całkowita ilość dostępnego miejsca w magazynie definiuje również wydajność we/wy dostępną dla każdego węzła procesu roboczego i koordynatora.
 
|                       | Węzeł procesu roboczego           | Węzeł koordynatora      |
|-----------------------|-----------------------|-----------------------|
| Obliczenia, rdzeni wirtualnych       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Pamięć na rdzeń wirtualny, GiB | 8                     | 4                     |
| Rozmiar magazynu, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Typ magazynu          | Ogólnego przeznaczenia (SSD) | Ogólnego przeznaczenia (SSD) |
| Liczba operacji we/wy na sekundę                  | Do 3 operacji we/wy na sekundę/GiB      | Do 3 operacji we/wy na sekundę/GiB      |


## <a name="regions"></a>Regiony
Grupy serwerów ze skalowaniem (Citus) są dostępne w następujących regionach platformy Azure:

* Południowej
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
* Terenie
    * Europa Północna
    * Południowe Zjednoczone Królestwo
    * Europa Zachodnia

Niektóre z tych regionów mogą nie być początkowo aktywowane we wszystkich subskrypcjach platformy Azure. Jeśli chcesz użyć regionu z powyższej listy i nie widzisz go w Twojej subskrypcji, lub jeśli chcesz użyć regionu, którego nie ma na tej liście, Otwórz [żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pricing"></a>Ceny
Najbardziej aktualne informacje o cenach można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/postgresql/)usługi.
Aby wyświetlić koszt dla wybranej konfiguracji, [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) przedstawia miesięczny koszt na karcie **Konfiguracja** na podstawie wybranych opcji. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z kalkulatora cen platformy Azure, aby uzyskać szacowaną cenę. W witrynie sieci Web [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) wybierz pozycję **Dodaj elementy**, rozwiń kategorię **bazy danych** , a następnie wybierz pozycję **Azure Database for PostgreSQL — Citus** , aby dostosować opcje.
 
## <a name="next-steps"></a>Następne kroki
Dowiedz się [, jak utworzyć grupę serwerów ze skalą (Citus) w portalu](quickstart-create-hyperscale-portal.md).
