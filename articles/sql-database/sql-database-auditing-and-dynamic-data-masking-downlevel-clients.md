---
title: Tabela inspekcji, przekierowanie TDS i IP punktów końcowych usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o inspekcji TDS przekierowania i adres IP punktu końcowego zmiany podczas wykonywania inspekcji tabel w usłudze Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 02/25/2019
ms.openlocfilehash: 2c95ec4d88e55af0becc73719bcc6126501267db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61416530"
---
# <a name="sql-database----downlevel-clients-support-and-ip-endpoint-changes-for-table-auditing"></a>Obsługa klientów niższych poziomów bazy danych SQL — i punktu końcowego adresu IP zmienia się w przypadku inspekcji tabel

> [!IMPORTANT]
> Ten dokument ma zastosowanie tylko do inspekcji tabel, która jest **zakończyliśmy**.<br>
> Użyj nowej [inspekcji obiektów Blob](sql-database-auditing.md) metody, która **nie** wymaga modyfikacji ciągu połączenia klientów niższych poziomów. Dodatkowe informacje na temat inspekcji obiektów Blob można znaleźć w [Rozpoczynanie pracy z inspekcją bazy danych SQL](sql-database-auditing.md).

[Bazy danych inspekcji](sql-database-auditing.md) automatycznie współpracuje z usługą SQL klientów, które obsługują przekierowanie TDS. Pamiętaj, że przekierowanie nie ma zastosowania, korzystając z metody inspekcji obiektów Blob.

## <a id="subheading-1"></a>Obsługa klientów niższych poziomów

Dowolny klient, który implementuje TDS w wersji 7.4 powinien obsługiwać również przekierowania. Wyjątki od tej reguły obejmują JDBC 4.0, w którym funkcji przekierowania nie jest w pełni obsługiwana i Tedious dla środowiska Node.JS, w których przekierowania nie została zaimplementowana.

Dla "Klientów niższych poziomów" czyli które pomocy technicznej TDS w wersji 7.3 i poniżej. nazwa FQDN serwera w połączeniu parametry powinny być modyfikowane:

Oryginalna nazwa FQDN serwera w ciągu połączenia: <*nazwy serwera*>. database.windows.net

Nazwa FQDN serwera zmodyfikowane w ciągu połączenia: <*nazwy serwera*> .database. **bezpieczne**. windows.net

Częściowa lista "Klienci z obniżonym poziomem" obejmuje:

* Program .NET 4.0 i poniżej
* ODBC 10.0 i poniżej.
* JDBC (JDBC obsługują TDS w wersji 7.4, funkcja przekierowywania TDS nie jest w pełni obsługiwany)
* Tedious (dla środowiska Node.JS)

**Uwaga:** Powyższym serwerze modyfikacji w pełni kwalifikowaną nazwę domeny może być przydatne, również w odniesieniu do zasady inspekcji usługi SQL Server poziom bez potrzeby kroku konfiguracji, w każdej bazie danych (tymczasowe ograniczenie).

## <a id="subheading-2"></a>Punkt końcowy IP zmienia się podczas włączania inspekcji

Należy pamiętać, że po włączeniu inspekcji tabel punktu końcowego adresu IP bazy danych ulegnie zmianie. W przypadku ustawienia zapory ścisłe aktualizacji tych ustawień zapory odpowiednio.

Nowy punkt końcowy IP bazy danych zależy od regionu bazy danych:

| Region bazy danych | Możliwe IP punktów końcowych. |
| --- | --- |
| Chiny Północne |139.217.29.176, 139.217.28.254 |
| Chiny Wschodnie |42.159.245.65, 42.159.246.245 |
| Australia Wschodnia |104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Australia Południowo-Wschodnia |191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Brazylia Południowa |104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| Środkowe stany USA |104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| Środkowe stany USA — EUAP |52.180.178.16, 52.180.176.190 |
| Azja Wschodnia |23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| Wschodnie stany USA 2 |104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| Wschodnie stany USA |23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| Wschodnie stany USA — EUAP |52.225.190.86, 52.225.191.187 |
| Indie Środkowe |104.211.98.219, 104.211.103.71 |
| Indie Południowe |104.211.227.102, 104.211.225.157 |
| Indie Zachodnie |104.211.161.152, 104.211.162.21 |
| Japonia Wschodnia |104.41.179.1, 40.115.253.81, 23.102.64.207, 40.115.250.196 |
| Japonia Zachodnia |104.214.140.140, 104.214.146.31, 191.233.32.34, 104.214.146.198 |
| Środkowo-północne stany USA |191.236.155.178, 23.96.192.130, 23.96.177.169, 23.96.193.231 |
| Europa Północna |104.41.209.221, 40.85.139.245, 137.116.251.66, 40.85.142.176 |
| Środkowo-południowe stany USA |191.238.184.128, 40.84.190.84, 23.102.160.153, 40.84.186.66 |
| Azja Południowo-Wschodnia |104.215.198.156, 13.76.252.200, 23.97.51.109, 13.76.252.113 |
| Europa Zachodnia |104.40.230.120, 13.80.23.64, 137.117.171.161, 13.80.8.37, 104.47.167.215, 40.118.56.193, 104.40.176.73, 40.118.56.20 |
| Zachodnie stany USA |191.236.123.146, 138.91.163.240, 168.62.194.148, 23.99.6.91 |
| Zachodnie stany USA 2 |13.66.224.156, 13.66.227.8 |
| Środkowo-zachodnie stany USA |52.161.29.186, 52.161.27.213 |
| Kanada Środkowa |13.88.248.106, 13.88.248.110 |
| Kanada Wschodnia |40.86.227.82, 40.86.225.194 |
| Południowe Zjednoczone Królestwo |13.87.32.202, 13.87.32.226 |
