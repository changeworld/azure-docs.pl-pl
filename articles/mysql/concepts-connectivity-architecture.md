---
title: Architektura łączności — Azure Database for MySQL
description: Opisuje architekturę łączności dla serwera Azure Database for MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 8d4713cba94971971558a72c680e3c8f80331662
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760338"
---
# <a name="connectivity-architecture-in-azure-database-for-mysql"></a>Architektura łączności w Azure Database for MySQL
W tym artykule opisano architekturę Azure Database for MySQL łączności oraz sposób kierowania ruchu do wystąpienia Azure Database for MySQL z klientów zarówno w ramach platformy Azure, jak i poza nią.

## <a name="connectivity-architecture"></a>Architektura łączności
Połączenie z Azure Database for MySQL jest nawiązywane za pomocą bramy, która jest odpowiedzialna za kierowanie połączeń przychodzących do lokalizacji fizycznej serwera w naszych klastrach. Na poniższym diagramie przedstawiono przepływ ruchu.

![Przegląd architektury łączności](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Gdy klient nawiązuje połączenie z bazą danych, otrzymują parametry połączenia, które łączą się z bramą. Ta brama ma publiczny adres IP, który nasłuchuje na porcie 3306. W klastrze bazy danych ruch jest przekazywany do odpowiednich Azure Database for MySQL. W związku z tym, aby nawiązać połączenie z serwerem, na przykład z sieci firmowej, należy otworzyć Zaporę po stronie klienta, aby zezwolić na ruch wychodzący z naszych bram. Poniżej znajdziesz pełną listę adresów IP używanych przez nasze bramy na region.

## <a name="azure-database-for-mysql-gateway-ip-addresses"></a>Adresy IP bramy Azure Database for MySQL
W poniższej tabeli wymieniono podstawowe i pomocnicze adresy IP bramy Azure Database for MySQL dla wszystkich obszarów danych. Podstawowy adres IP to bieżący adres IP bramy, a drugi adres IP to adres IP trybu failover w przypadku awarii podstawowej. Jak wspomniano, klienci powinni zezwolić na ruch wychodzący zarówno do adresów IP. Drugi adres IP nie nasłuchuje w żadnej usłudze, dopóki nie zostanie aktywowany przez Azure Database for MySQL w celu zaakceptowania połączeń.

| **Nazwa regionu** | **Adresy IP bramy** |
|:----------------|:-------------|
| Australia Środkowa| 20.36.105.0     |
| Australia Central2     | 20.36.113.0   |
| Australia Wschodnia | 13.75.149.87, 40.79.161.1     |
| Australia Południowo-Wschodnia |191.239.192.109, 13.73.109.251   |
| Brazylia Południowa | 104.41.11.5, 191.233.201.8, 191.233.200.16  |
| Kanada Środkowa |40.85.224.249  |
| Kanada Wschodnia | 40.86.226.166    |
| Środkowe stany USA | 23.99.160.139, 13.67.215.62   |
| Chiny Wschodnie | 139.219.130.35    |
| Chiny Wschodnie 2 | 40.73.82.1  |
| Chiny Północne | 139.219.15.17    |
| Chiny Północne 2 | 40.73.50.0     |
| Azja Wschodnia | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| Wschodnie stany USA | 40.121.158.30, 191.238.6.43  |
| Wschodnie stany USA 2 |40.79.84.180, 191.239.224.107, 52.177.185.181   |
| Francja Środkowa | 40.79.137.0, 40.79.129.1  |
| Niemcy Środkowe | 51.4.144.100     |
| Niemcy Północne wschód | 51.5.144.179  |
| Indie Środkowe | 104.211.96.159     |
| Indie Południowe | 104.211.224.146  |
| Indie Zachodnie | 104.211.160.80    |
| Japonia Wschodnia | 13.78.61.196, 191.237.240.43  |
| Japonia Zachodnia | 104.214.148.156, 191.238.68.11    |
| Korea Środkowa | 52.231.32.42   |
| Korea Południowa | 52.231.200.86    |
| Północno-środkowe stany USA | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    |
| Europa Północna | 40.113.93.91, 191.235.193.75    |
| Północna Republika Południowej Afryki  | 102.133.152.0    |
| Północna Republika Południowej Afryki | 102.133.24.0   |
| Południowo-środkowe stany USA |13.66.62.124, 23.98.162.75   |
| Azja Południowo-wschodnia | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| Środkowe Zjednoczone Emiraty Arabskie | 20.37.72.64  |
| Północne Zjednoczone Emiraty Arabskie | 65.52.248.0    |
| Południowe Zjednoczone Królestwo | 51.140.184.11   |
| Zachodnie Zjednoczone Królestwo | 51.141.8.11  |
| Zachodnio-środkowe stany USA | 13.78.145.25     |
| Europa Zachodnia | 40.68.37.158, 191.237.232.75     |
| Zachodnie stany USA | 104.42.238.205, 23.99.34.75  |
| Zachodnie stany USA 2 | 13.66.226.202  |
||||

## <a name="next-steps"></a>Następne kroki

* [Tworzenie reguł zapory Azure Database for MySQL i zarządzanie nimi za pomocą Azure Portal](./howto-manage-firewall-using-portal.md)
* [Tworzenie reguł zapory Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure i zarządzanie nimi](./howto-manage-firewall-using-cli.md)
