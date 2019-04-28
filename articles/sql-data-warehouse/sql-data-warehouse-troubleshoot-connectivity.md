---
title: Rozwiązywanie problemów z usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 03/27/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 5115ffbc3568c87c37bae4a3e65c37f8504f1fb8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475363"
---
# <a name="troubleshooting-connectivity-issues"></a>Rozwiązywanie problemów z łącznością

W tym artykule wymieniono typowe techniki rozwiązywania problemów dotyczących nawiązywania połączenia z usługi SQL Data Warehouse.
- [Sprawdź dostępność usługi](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Sprawdź, czy operacja wstrzymania lub skalowania](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Sprawdź ustawienia zapory](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Sprawdź ustawienia punktu końcowego sieci wirtualnej/usługi](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Wyszukaj najnowsze sterowniki](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Sprawdź parametry połączenia](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Problemy z połączeniem tymczasowy](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Typowe komunikaty o błędach](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Sprawdź dostępność usługi

Sprawdź, czy usługa jest dostępna. W witrynie Azure portal przejdź do SQL data warehouse, w którym próbujesz się połączyć. Na panelu spisu treści po lewej stronie kliknij pozycję **diagnozowanie i rozwiązywanie problemów**.

![Wybierz opcję Usługa Resource health](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Stan usługi SQL data warehouse będą wyświetlane w tym miejscu. Jeśli usługa nie jest wyświetlany jako **dostępne**, sprawdź dalszych czynności.

![Usługa dostępna](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Jeśli Twoja usługa Resource health wskazuje, że magazyn danych jest wstrzymany lub skalowania, postępuj zgodnie ze wskazówkami, aby wznowić magazynu danych.

![Usługa wstrzymana](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) dodatkowe informacje na temat kondycji zasobu można znaleźć tutaj.

## <a name="check-for-paused-or-scaling-operation"></a>Sprawdź, czy operacja wstrzymania lub skalowania

Sprawdź portal aby zobaczyć, czy usługi SQL data warehouse jest wstrzymane lub skalowania.

![Usługa wstrzymana](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Jeśli zobaczysz, że usługa jest wstrzymana lub skalowania, sprawdź, czy nie jest w harmonogramie konserwacji. W portalu dla usługi SQL data warehouse *Przegląd*, zostanie wyświetlony harmonogram konserwacji wybrane.

![Harmonogram konserwacji — omówienie](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

W przeciwnym razie skontaktuj się z administratorem IT, aby sprawdzić, czy ta konserwacja nie jest zaplanowane zdarzenie. Aby wznowić SQL data warehouse, wykonaj czynności opisane [tutaj](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Sprawdź ustawienia zapory

Usługa SQL Data Warehouse komunikuje się przez port 1433.   Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być blokowany przez zaporę sieciową. W takim przypadku nie można połączyć z serwerem usługi Azure SQL Database, chyba że dział IT otworzy port 1433. Dodatkowe informacje na temat konfiguracji zapory można znaleźć [tutaj](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json#manage-ip-firewall-rules-using-the-azure-portal).

## <a name="check-your-vnetservice-endpoint-settings"></a>Sprawdź ustawienia punktu końcowego sieci wirtualnej/usługi

Jeśli otrzymujesz błędy 40914 i 40615 [opis błędu i rozwiązanie tutaj](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Wyszukaj najnowsze sterowniki

### <a name="software"></a>Oprogramowanie

Sprawdź, upewnij się, że używasz najnowszych narzędzi, aby nawiązać połączenie z usługi SQL data warehouse:

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Sterowniki

Zaznacz, aby upewnić się, że używasz najnowszej wersji sterowników.  Używanie starszej wersji sterowników może spowodować nieoczekiwane wyniki jako starsze sterowniki mogą nie obsługiwać nowe funkcje.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Sprawdź parametry połączenia

Sprawdź, upewnij się, że parametry połączenia są ustawione poprawnie.  Poniżej przedstawiono kilka przykładów.  Można znaleźć dodatkowe informacje na temat [ciągi połączenia, w tym miejscu](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

Parametry połączenia sterownika ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Ciąg połączenia ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

Parametry połączenia PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Parametry połączenia sterownika JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Problemy z połączeniem tymczasowy

Sprawdź, jeśli występują duże obciążenie na serwerze z dużą liczbą żądań w kolejce. Może być konieczne skalowanie w górę magazynu danych, aby uzyskać dodatkowe zasoby.

## <a name="common-error-messages"></a>Typowe komunikaty o błędach

Zobacz błędy 40914 i 40615, [opis błędu i rozwiązanie tutaj](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Nadal masz problemy z łącznością?
Tworzenie [bilet pomocy technicznej](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) więc zespół inżynierów może obsługiwać użytkownik.
