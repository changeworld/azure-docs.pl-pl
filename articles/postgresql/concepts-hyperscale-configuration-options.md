---
title: Azure Database for PostgreSQL — (Citus) (wersja zapoznawcza) na dużą skalę opcje wydajności
description: Opcje dla grupy serwerów na dużą skalę (Citus), łącznie z węzłów obliczeniowych, magazynu i regiony.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e8c1c2e51ca14ae9b17f0d7efb20552cdd55139b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077293"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-preview-performance-options"></a>Azure Database for PostgreSQL — (Citus) (wersja zapoznawcza) na dużą skalę opcje wydajności

## <a name="compute-and-storage"></a>Obliczenia i magazyn
 
W grupie serwerów na dużą skalę (Citus), możesz wybrać ustawienia zasobów obliczeniowych i magazynowych niezależnie dla węzłów procesu roboczego i węzeł koordynatora.  Obliczenia, że zasoby są dostarczane jako rdzeni wirtualnych, który reprezentuje logiczny procesor CPU bazowego sprzętu. Rozmiar magazynu do inicjowania obsługi odnosi się do możliwości do węzłów koordynatora i proces roboczy w Hiperskali (Citus) grupy serwerów. Magazyn zawiera pliki bazy danych, pliki tymczasowe, dzienników transakcji i Postgres dzienniki serwera. Łączną ilość pamięci masowej, które aprowizujesz też definiuje dostępnej pojemności we/wy dla każdego węzła procesu roboczego oraz koordynatora.
 
|                       | Węzeł procesu roboczego           | Węzeł koordynatora      |
|-----------------------|-----------------------|-----------------------|
| Moc obliczeniowa, rdzenie wirtualne       | 4, 8, 16, 32          | 4, 8, 16, 32          |
| Ilość pamięci na rdzeń wirtualny, GiB | 8                     | 4                     |
| Rozmiar magazynu, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Typ magazynu          | Ogólnego przeznaczenia (SSD) | Ogólnego przeznaczenia (SSD) |
| Operacje wejścia/wyjścia                  | Operacje We/Wy do 3/GiB      | Operacje We/Wy do 3/GiB      |


## <a name="regions"></a>Regiony
Grupy serwerów na dużą skalę (Citus) są dostępne w następujących regionach platformy Azure:
* Wschodnie stany USA 2
* Azja Południowo-Wschodnia
* Europa Zachodnia
* Zachodnie stany USA 2

## <a name="pricing"></a>Cennik
Aby uzyskać najbardziej aktualne informacje o cenach, zobacz usługę [stronę z cennikiem](https://azure.microsoft.com/pricing/details/postgresql/).
Aby zobaczyć kosztów dla konfiguracji, należy [witryny Azure portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) Wyświetla miesięczny koszt w **Konfiguruj** kartę na podstawie opcji wybierzesz. Jeśli nie masz subskrypcji platformy Azure, można użyć Kalkulatora cen platformy Azure można pobrać szacowana cena. Na [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) witryny sieci Web, wybierz opcję **Dodaj elementy**, rozwiń węzeł **baz danych** kategorii i wybierz polecenie **— Azure Database for PostgreSQL — Na dużą skalę (Citus)** Aby dostosować opcje.
 
## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [Utwórz grupę serwerów (Citus) na dużą skalę w portalu](quickstart-create-hyperscale-portal.md).
