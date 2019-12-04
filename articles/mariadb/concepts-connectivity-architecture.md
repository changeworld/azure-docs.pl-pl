---
title: Architektura łączności — Azure Database for MariaDB
description: Opisuje architekturę łączności dla serwera Azure Database for MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 6e2e39381e1500f86bce55726dda0286385d1674
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772896"
---
# <a name="connectivity-architecture-in-azure-database-for-mariadb"></a>Architektura łączności w Azure Database for MariaDB
W tym artykule opisano architekturę Azure Database for MariaDB łączności oraz sposób kierowania ruchu do wystąpienia Azure Database for MariaDB z klientów zarówno w ramach platformy Azure, jak i poza nią.

## <a name="connectivity-architecture"></a>Architektura łączności

Połączenie z Azure Database for MariaDB jest nawiązywane za pomocą bramy, która jest odpowiedzialna za kierowanie połączeń przychodzących do lokalizacji fizycznej serwera w naszych klastrach. Na poniższym diagramie przedstawiono przepływ ruchu.

![Przegląd architektury łączności](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Gdy klient nawiązuje połączenie z bazą danych, otrzymują parametry połączenia, które łączą się z bramą. Ta brama ma publiczny adres IP, który nasłuchuje na porcie 3306. W klastrze bazy danych ruch jest przekazywany do odpowiednich Azure Database for MariaDB. W związku z tym, aby nawiązać połączenie z serwerem, na przykład z sieci firmowej, należy otworzyć Zaporę po stronie klienta, aby zezwolić na ruch wychodzący z naszych bram. Poniżej znajdziesz pełną listę adresów IP używanych przez nasze bramy na region.

## <a name="azure-database-for-mariadb-gateway-ip-addresses"></a>Adresy IP bramy Azure Database for MariaDB

W poniższej tabeli wymieniono podstawowe i pomocnicze adresy IP bramy Azure Database for MariaDB dla wszystkich obszarów danych. Podstawowy adres IP to bieżący adres IP bramy, a drugi adres IP to adres IP trybu failover w przypadku awarii podstawowej. Jak wspomniano, klienci powinni zezwolić na ruch wychodzący zarówno do adresów IP. Drugi adres IP nie nasłuchuje w żadnej usłudze, dopóki nie zostanie aktywowany przez Azure Database for MariaDB w celu zaakceptowania połączeń.

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
| Południowo-środkowe stany USA | 23.98.162.75 | 13.66.62.124 |
| Azja Południowo-wschodnia | 23.100.117.95 | 104.43.15.0 |
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

* [Tworzenie reguł zapory Azure Database for MariaDB i zarządzanie nimi za pomocą Azure Portal](./howto-manage-firewall-portal.md)
* [Tworzenie reguł zapory Azure Database for MariaDB przy użyciu interfejsu wiersza polecenia platformy Azure i zarządzanie nimi](./howto-manage-firewall-cli.md)
