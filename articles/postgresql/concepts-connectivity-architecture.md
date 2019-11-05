---
title: Architektura łączności w Azure Database for PostgreSQL
description: Opisuje architekturę łączności dla serwera Azure Database for PostgreSQL.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 5288c3025e523e767082f4ab7cfbb08805a74eaf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500084"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Architektura łączności w Azure Database for PostgreSQL
W tym artykule opisano architekturę Azure Database for PostgreSQL łączności oraz sposób kierowania ruchu do wystąpienia bazy danych Azure Database for PostgreSQL z klientów zarówno w ramach platformy Azure, jak i poza nią.

## <a name="connectivity-architecture"></a>Architektura łączności
Połączenie z Azure Database for PostgreSQL jest nawiązywane za pomocą bramy, która jest odpowiedzialna za kierowanie połączeń przychodzących do lokalizacji fizycznej serwera w naszych klastrach. Na poniższym diagramie przedstawiono przepływ ruchu.

![Przegląd architektury łączności](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Gdy klient nawiązuje połączenie z bazą danych, otrzymują parametry połączenia, które łączą się z bramą. Ta brama ma publiczny adres IP, który nasłuchuje na porcie 5432. Wewnątrz ruchu klastra bazy danych jest przekazywany do odpowiednich Azure Database for PostgreSQL. W związku z tym, aby nawiązać połączenie z serwerem, na przykład z sieci firmowej, należy otworzyć Zaporę po stronie klienta, aby zezwolić na ruch wychodzący z naszych bram. Poniżej znajdziesz pełną listę adresów IP używanych przez nasze bramy na region.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Adresy IP bramy Azure Database for PostgreSQL
W poniższej tabeli wymieniono podstawowe i pomocnicze adresy IP bramy Azure Database for PostgreSQL dla wszystkich obszarów danych. Podstawowy adres IP to bieżący adres IP bramy, a drugi adres IP to adres IP trybu failover w przypadku awarii podstawowej. Jak wspomniano, klienci powinni zezwolić na ruch wychodzący zarówno do adresów IP. Drugi adres IP nie nasłuchuje w żadnej usłudze, dopóki nie zostanie aktywowany przez Azure Database for PostgreSQL w celu zaakceptowania połączeń.

| **Nazwa regionu** | **Podstawowy adres IP** | **Pomocniczy adres IP** |
|:----------------|:-------------|:------------------------|
| Australia Wschodnia | 13.75.149.87 | 40.79.161.1 |
| Australia Południowo-Wschodnia | 191.239.192.109 | 13.73.109.251 |
| Brazylia Południowa | 104.41.11.5 | |
| Kanada Środkowa | 40.85.224.249 | |
| Kanada Wschodnia | 40.86.226.166 | |
| Środkowe stany USA | 23.99.160.139 | 13.67.215.62 |
| Chiny Wschodnie 1 | 139.219.130.35 | |
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
| Północno-środkowe stany USA | 23.98.55.75 | 23.96.178.199 |
| Europa Północna | 191.235.193.75 | 40.113.93.91 |
| Środkowo-południowe stany USA | 23.98.162.75 | 13.66.62.124 |
| Azja Południowo-Wschodnia | 23.100.117.95 | 104.43.15.0 |
| Północna Republika Południowej Afryki | 102.133.152.0 | |
| Północna Republika Południowej Afryki | 102.133.24.0 | |
| Północne Zjednoczone Emiraty Arabskie | 65.52.248.0 | |
| Południowe Zjednoczone Królestwo | 51.140.184.11 | |
| Zachodnie Zjednoczone Królestwo | 51.141.8.11| |
| Europa Zachodnia | 191.237.232.75 | 40.68.37.158 |
| Zachodnie stany USA 1 | 23.99.34.75 | 104.42.238.205 |
| Zachodnie stany USA 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *Wschodnie stany USA 2* również mają adres IP trzeciego rzędu `52.167.104.0`.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie reguł zapory Azure Database for PostgreSQL i zarządzanie nimi za pomocą Azure Portal](./howto-manage-firewall-using-portal.md)
* [Tworzenie reguł zapory Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure i zarządzanie nimi](./howto-manage-firewall-using-cli.md)
