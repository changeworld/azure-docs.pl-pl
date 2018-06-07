---
title: Tabela inspekcji, przekierowanie TDS punktów końcowych IP bazy danych SQL Azure i | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o inspekcji przekierowania TDS i adres IP punktu końcowego zmiany podczas wykonywania inspekcji tabeli w bazie danych SQL Azure.
services: sql-database
author: giladm
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: giladm
ms.openlocfilehash: b615e9ff67788c2f02c2fa0c33e0de3331e77885
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645633"
---
# <a name="sql-database----downlevel-clients-support-and-ip-endpoint-changes-for-table-auditing"></a>Baza danych SQL — klienci z obniżonym poziomem obsługuje i punkcie końcowym IP zmian dla tabeli inspekcji

> [!IMPORTANT]
> Ten dokument ma zastosowanie tylko do inspekcji tabeli, która jest **teraz przestarzałe**.<br>
> Użyj nowej [Inspekcja obiektów Blob](sql-database-auditing.md) — metoda, która **nie** wymagają modyfikacji ciągu połączenia klientów niższych poziomów. Dodatkowe informacje o inspekcji obiektu Blob można znaleźć w [wprowadzenie inspekcji bazy danych SQL](sql-database-auditing.md).

[Baza danych inspekcji](sql-database-auditing.md) działa automatycznie klientom SQL, które obsługują przekierowanie TDS. Należy pamiętać, że w tym przekierowania nie ma zastosowania, korzystając z metody Inspekcja obiektów Blob.

## <a id="subheading-1"></a>Obsługa klientów niższych poziomów
Dowolnego klienta, który implementuje TDS 7.4 powinny również obsługiwać przekierowania. Wyjątki od tej reguły obejmują JDBC 4.0, w którym funkcji przekierowania nie jest w pełni obsługiwane i Tedious dla środowiska Node.JS, w których przekierowania nie została zaimplementowana.

Dla "Klientów niższych poziomów" tj. które Obsługa TDS 7.3 i poniżej — nazwa FQDN serwera w połączeniu ciąg wersji powinno zostać zmodyfikowane:

Oryginalna nazwa FQDN serwera w ciągu połączenia: <*nazwy serwera*>. database.windows.net

Nazwa FQDN serwera zmodyfikowane w ciągu połączenia: <*nazwy serwera*> .database. **bezpieczne**. windows.net

Zawiera listę częściowej "Klienci z obniżonym poziomem":

* .NET 4.0 i poniżej,
* ODBC 10.0 i poniżej.
* JDBC (podczas JDBC obsługuje TDS 7.4, funkcji przekierowania TDS nie jest całkowicie obsługiwana)
* Niewygodny (dla środowiska Node.JS)

**Uwaga:** modyfikacji FDQN powyższym serwerze mogą być przydatne także stosowania zasad inspekcji programu SQL Server poziom bez potrzebę konfiguracji kroku w każdej bazie danych (ograniczenie tymczasowe).

## <a id="subheading-2"></a>Punkt końcowy IP zmienia podczas włączania inspekcji
Należy pamiętać, że po włączeniu inspekcji tabeli punkt końcowy IP bazy danych ulegnie zmianie. Jeśli ustawienia zapory strict, zaktualizuj tych ustawień zapory odpowiednio.

Nowy punkt końcowy IP bazy danych będzie zależeć od regionu bazy danych:

| Region bazy danych | Możliwe IP punktów końcowych. |
| --- | --- |
| Chiny Północne |139.217.29.176, 139.217.28.254 |
| Chiny Wschodnie |42.159.245.65, 42.159.246.245 |
| Australia Wschodnia |104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Australia Południowo-Wschodnia |191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Brazylia Południowa |104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| Środkowe stany USA |104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| Central US EUAP |52.180.178.16, 52.180.176.190 |
| Azja Wschodnia |23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| Wschodnie stany USA 2 |104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| Wschodnie stany USA |23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| Wschodnie stany USA 2 — EUAP |52.225.190.86, 52.225.191.187 |
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
| Północne Zjednoczone Królestwo |13.87.101.18, 13.87.100.232 |
| Południowe Zjednoczone Królestwo 2 |13.87.32.202, 13.87.32.226 |
