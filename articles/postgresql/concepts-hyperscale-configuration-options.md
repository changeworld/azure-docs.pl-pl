---
title: Opcje wydajności — hiperskala (Citus) — usługa Azure Database for PostgreSQL
description: Opcje dla grupy serwerów w hiperskali (Citus), w tym obliczenia węzłów, magazyn i regiony.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462415"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Usługa Azure Database for PostgreSQL — opcje wydajności w hiperskali (Citus)

## <a name="compute-and-storage"></a>Obliczenia i magazyn
 
Ustawienia obliczeń i magazynu można wybrać niezależnie dla węzłów procesu roboczego i węzła koordynatora w grupie serwerów w hiperskali (Citus).  Zasoby obliczeniowe są dostarczane jako rdzenie wirtualne, które reprezentują logiczny procesor procesora podstawowego sprzętu. Rozmiar magazynu do inicjowania obsługi administracyjnej odnosi się do pojemności dostępnej dla koordynatora i węzłów procesu roboczego w grupie serwerów w hiperskali (Citus). Magazyn zawiera pliki bazy danych, pliki tymczasowe, dzienniki transakcji i dzienniki serwera Postgres. Całkowita ilość aprowizowania magazynu definiuje również pojemność we/wy dostępną dla każdego pracownika i węzła koordynatora.
 
|                       | Węzeł pracownika           | Węzeł koordynatora      |
|-----------------------|-----------------------|-----------------------|
| Moc obliczeniowe, cory wirtualne       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Pamięć na vCore, GiB | 8                     | 4                     |
| Rozmiar pamięci masowej, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Typ magazynu          | Ogólnego przeznaczenia (SSD) | Ogólnego przeznaczenia (SSD) |
| Liczba operacji we/wy na sekundę                  | Do 3 IOPS/GiB      | Do 3 IOPS/GiB      |


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
