---
title: Architektura łączności w usłudze Azure Database for PostgreSQL
description: W tym artykule opisano architekturę łączności dla usługi Azure Database for postgresql w warstwie serwera.
author: kummanish
ms.author: manishku
ms.service: PostgreSQL
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 73c23c471cb12ca3a3a7df4380779b464b8d86d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735739"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Architektura łączności w usłudze Azure Database for PostgreSQL
W tym artykule opisano usługi Azure Database for postgresql — architektura łączności również, jak ruch będzie kierowany do usługi Azure Database for postgresql w warstwie wystąpienie bazy danych z klientami zarówno wewnątrz jednej, jak i spoza platformy Azure.

## <a name="connectivity-architecture"></a>Architektura łączności
Połączenie z bazą danych Azure database for PostgreSQL zostanie nawiązane za pośrednictwem bramy, który jest odpowiedzialny za routing połączenia przychodzące do fizycznej lokalizacji serwera w naszym klastrów. Na poniższym diagramie przedstawiono przepływ ruchu.

![Przegląd architektury połączenia](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Jako klient połączenia z bazą danych, otrzymują parametry połączenia, które nawiązuje połączenie z bramą. Ta brama jest publiczny adres IP, który nasłuchuje na porcie 5432. Wewnątrz bazy danych clusterz ruch jest przekazywany do odpowiedniej usługi Azure Database for PostgreSQL. W związku z tym aby połączyć się z serwerem, takich jak z sieci firmowej, należy otworzyć zaporę po stronie klienta, aby zezwolić na ruch wychodzący można było nawiązać połączenie naszego bram. Poniżej można znaleźć pełną listę adresy IP używane przez naszych bramy na region.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Usługa Azure Database for postgresql — adresy IP
W poniższej tabeli wymieniono podstawowe i pomocnicze adresy IP usługi Azure Database for postgresql w warstwie bramy dla wszystkich obszarach danych. Podstawowy adres IP jest bieżący adres IP bramy, a drugi adres IP jest adresem IP trybu failover w przypadku awarii podstawowego. Jak wspomniano wcześniej, klienci powinna zezwalać na ruch wychodzący do adresów IP. Drugi adres IP nie będzie nasłuchiwać na wszystkie usługi, dopóki nie zostanie aktywowany przez usługę Azure Database for PostgreSQL do akceptowania połączeń.

| **Nazwa regionu** | **Adres IP podstawowego** | **Adres IP pomocniczego** |
|:----------------|:-------------|:------------------------|
| Australia Wschodnia | 13.75.149.87 | 40.79.161.1 |
| Australia Południowo-Wschodnia | 191.239.192.109 | 13.73.109.251 |
| Brazylia Południowa | 104.41.11.5 | |
| Kanada Środkowa | 40.85.224.249 | |
| Kanada Wschodnia | 40.86.226.166 | |
| Środkowe stany USA | 23.99.160.139 | 13.67.215.62 |
| Chiny wschodnie 1 | 139.219.130.35 | |
| Chiny Wschodnie 2 | 40.73.82.1 | |
| Chiny Północne 1 | 139.219.15.17 | |
| Chiny Północne 2 | 40.73.50.0 | |
| Azja Wschodnia | 191.234.2.139 | 52.175.33.150 |
| Wschodnie stany USA 1 | 191.238.6.43 | 40.121.158.30 |
| Wschodnie stany USA 2 | 191.239.224.107 | 40.79.84.180 * |
| Francja Środkowa | 40.79.137.0 | 40.79.129.1 |
| Niemcy Środkowe | 51.4.144.100 | |
| Indie Środkowe | 104.211.96.159 | |
| Indie Południowe | 104.211.224.146 | |
| Indie Zachodnie | 104.211.160.80 | |
| Japonia Wschodnia | 191.237.240.43 | 13.78.61.196 |
| Japonia Zachodnia | 191.238.68.11 | 104.214.148.156 |
| Korea Środkowa | 52.231.32.42 | |
| Korea Południowa | 52.231.200.86 |  |
| Środkowo-północne stany USA | 23.98.55.75 | 23.96.178.199 |
| Europa Północna | 191.235.193.75 | 40.113.93.91 |
| Środkowo-południowe stany USA | 23.98.162.75 | 13.66.62.124 |
| Azja Południowo-Wschodnia | 23.100.117.95 | 104.43.15.0 |
| Południowe Zjednoczone Królestwo | 51.140.184.11 | |
| Zachodnie Zjednoczone Królestwo | 51.141.8.11| |
| Europa Zachodnia | 191.237.232.75 | 40.68.37.158 |
| Zachodnie stany USA 1 | 23.99.34.75 | 104.42.238.205 |
| Zachodnie stany USA 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *Wschodnie stany USA 2* ma również trzeciorzędny adres IP `52.167.104.0`.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie i zarządzanie nimi — Azure Database for postgresql w warstwie reguł zapory przy użyciu witryny Azure portal](./howto-manage-firewall-using-portal.md)
* [Tworzenie i zarządzanie nimi — Azure Database for postgresql w warstwie reguł zapory przy użyciu wiersza polecenia platformy Azure](./howto-manage-firewall-using-cli.md)
