---
title: Rozwiązywanie problemów Azure SQL Data Warehouse | Microsoft Docs
description: Rozwiązywanie problemów Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 290753b866f15e09a52572fdd7a43a60fc2812d6
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575546"
---
# <a name="troubleshooting-connectivity-issues"></a>Rozwiązywanie problemów z łącznością

W tym artykule wymieniono typowe techniki rozwiązywania problemów dotyczące łączenia się z SQL Data Warehouse.
- [Sprawdź dostępność usługi](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Sprawdź wstrzymanie lub skalowanie operacji](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Sprawdź ustawienia zapory](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Sprawdź ustawienia sieci wirtualnej/punktu końcowego usługi](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Sprawdź, czy są zainstalowane najnowsze sterowniki](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Sprawdź parametry połączenia](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Sporadyczne problemy z połączeniem](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Typowe komunikaty o błędach](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Sprawdź dostępność usługi

Sprawdź, czy usługa jest dostępna. W Azure Portal przejdź do SQL Data Warehouse, który próbujesz nawiązać połączenie. W panelu po lewej stronie kliknij pozycję **diagnozowanie i rozwiązywanie problemów**.

![Wybierz kondycję zasobu](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

W tym miejscu zostanie wyświetlony stan SQL Data Warehouse. Jeśli usługa nie jest wyświetlana jako **dostępna**, należy zapoznać się z kolejnymi krokami.

![Usługa dostępna](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Jeśli Kondycja zasobów pokazuje, że magazyn danych jest wstrzymany lub skalowany, postępuj zgodnie ze wskazówkami, aby wznowić działanie magazynu danych.

![Usługa wstrzymała](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) dodatkowe informacje na temat Resource Health można znaleźć tutaj.

## <a name="check-for-paused-or-scaling-operation"></a>Sprawdź wstrzymanie lub skalowanie operacji

Sprawdź Portal, aby sprawdzić, czy SQL Data Warehouse jest wstrzymana lub przeskalowana.

![Usługa została wstrzymana](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Jeśli zobaczysz, że usługa jest wstrzymana lub skalowania, sprawdź, czy nie jest ona wyświetlana w harmonogramie konserwacji. W portalu dla SQL Data Warehouse *Omówienie*zobaczysz wybrany harmonogram konserwacji.

![Harmonogram konserwacji — Omówienie](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

W przeciwnym razie skontaktuj się z administratorem IT, aby sprawdzić, czy ta konserwacja nie jest zaplanowana. Aby wznowić SQL Data Warehouse, wykonaj kroki opisane [tutaj](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Sprawdź ustawienia zapory

Usługa SQL Data Warehouse komunikuje się przez port 1433.   Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być blokowany przez zaporę sieciową. W takim przypadku nie można nawiązać połączenia z serwerem Azure SQL Database, chyba że dział IT otworzy port 1433. Dodatkowe informacje na temat konfiguracji zapory można znaleźć [tutaj](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#manage-server-level-ip-firewall-rules-using-the-azure-portal).

## <a name="check-your-vnetservice-endpoint-settings"></a>Sprawdź ustawienia sieci wirtualnej/punktu końcowego usługi

Jeśli otrzymujesz błędy 40914 i 40615, zobacz [Opis błędu i rozwiązanie tutaj](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Sprawdź, czy są zainstalowane najnowsze sterowniki

### <a name="software"></a>Oprogramowanie

Upewnij się, że używasz najnowszych narzędzi do nawiązywania połączenia z SQL Data Warehouse:

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Sterowniki

Upewnij się, że korzystasz z najnowszej wersji sterownika.  Używanie starszej wersji sterowników może spowodować nieoczekiwane zachowanie, ponieważ starsze sterowniki mogą nie obsługiwać nowych funkcji.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Sprawdź parametry połączenia

Upewnij się, że parametry połączenia zostały prawidłowo ustawione.  Poniżej przedstawiono przykłady.  Dodatkowe informacje o [ciągach połączeń](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings)można znaleźć tutaj.

Parametry połączenia sterownika ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Parametry połączenia ODBC

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

## <a name="intermittent-connection-issues"></a>Sporadyczne problemy z połączeniem

Sprawdź, czy na serwerze występują duże obciążenie z dużą liczbą żądań umieszczonych w kolejce. Może być konieczne skalowanie magazynu danych w górę w celu uzyskania dodatkowych zasobów.

## <a name="common-error-messages"></a>Typowe komunikaty o błędach

Błędy 40914 i 40615, zobacz [Opis i rozdzielczość błędu tutaj](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Nadal występują problemy z łącznością?
Utwórz [bilet pomocy technicznej](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) , aby zespół inżynieryjny mógł je obsługiwać.
