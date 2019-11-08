---
title: Kontrola dostępu do sieci
description: Omówienie kontroli dostępu do sieci dla Azure SQL Database i magazynu danych w celu zarządzania dostępem oraz konfigurowania jednej lub puli baz danych.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 16de1d9fcf86459b6bcadd9d8c372e436aad0915
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802939"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Azure SQL Database i kontrola dostępu do sieci hurtowni danych

> [!NOTE]
> Ten artykuł ma zastosowanie do programu Azure SQL Server oraz do baz danych SQL Database i SQL Data Warehouse utworzonych na serwerze SQL platformy Azure. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

> [!IMPORTANT]
> Ten artykuł *nie* dotyczy **Azure SQL Database wystąpienia zarządzanego**. Aby uzyskać więcej informacji na temat konfiguracji sieci, zobacz [nawiązywanie połączenia z wystąpieniem zarządzanym](sql-database-managed-instance-connect-app.md) .

Po utworzeniu nowego SQL Server platformy Azure [z Azure Portal](sql-database-single-database-get-started.md), wynik będzie publicznym punktem końcowym w formacie *yourservername.Database.Windows.NET*. Po zaprojektowaniu odmówiono dostępu do publicznego punktu końcowego. Następnie można użyć następujących kontroli dostępu do sieci, aby selektywnie zezwolić na dostęp do bazy danych SQl za pośrednictwem publicznego punktu końcowego
- Zezwalaj na usługi platformy Azure: — po ustawieniu na wartość włączone inne zasoby w ramach granicy platformy Azure, na przykład maszyna wirtualna platformy Azure, mogą uzyskać dostęp do SQL Database

- Reguły zapory adresów IP: Użyj tej funkcji, aby jawnie zezwolić na połączenia z określonego adresu IP, na przykład z maszyn lokalnych.

- Virtual Network reguł zapory: Użyj tej funkcji, aby zezwolić na ruch z określonego Virtual Network w ramach granicy platformy Azure

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Zezwalaj na usługi platformy Azure 
Po utworzeniu nowego SQL Server platformy Azure [z poziomu Azure Portal](sql-database-single-database-get-started.md)to ustawienie nie jest zaznaczone.

 ![Zrzut ekranu przedstawiający tworzenie nowego serwera][1]

Możesz również zmienić to ustawienie za pośrednictwem okienka Zapora po utworzeniu SQL Server platformy Azure w następujący sposób.
  
 ![Zrzut ekranu przedstawiający Zarządzanie zaporą serwera][2]

Po ustawieniu opcji **na** platformie Azure SQL Server umożliwia komunikację ze wszystkich zasobów w ramach granicy platformy Azure, która może być niedostępna w ramach subskrypcji.

W wielu przypadkach ustawienie **on** jest bardziej ograniczane niż to, czego chcą klienci. Mogą chcieć ustawić to ustawienie jako **wyłączone** i zamienić je na bardziej restrykcyjne reguły zapory IP lub Virtual Network reguły zapory. Takie działanie ma wpływ na następujące funkcje:

### <a name="import-export-service"></a>Importuj usługę eksportu

Usługa Azure SQL Database Import Export jest uruchamiana na maszynach wirtualnych na platformie Azure. Te maszyny wirtualne nie znajdują się w sieci wirtualnej, dlatego Uzyskaj adres IP platformy Azure podczas łączenia się z bazą danych. Po usunięciu **Zezwalaj usługom platformy Azure na dostęp do serwera** te maszyny wirtualne nie będą mogły uzyskać dostępu do baz danych.
Problem można obejść, uruchamiając BACPAC importowania lub eksportowania bezpośrednio w kodzie przy użyciu interfejsu API DACFx.

### <a name="sql-database-query-editor"></a>Edytor zapytań SQL Database

Edytor zapytań Azure SQL Database jest wdrażany na maszynach wirtualnych na platformie Azure. Te maszyny wirtualne nie znajdują się w sieci wirtualnej. W związku z tym maszyny wirtualne uzyskują adres IP platformy Azure podczas łączenia się z bazą danych. Po usunięciu **Zezwalaj usługom platformy Azure na dostęp do serwera**te maszyny wirtualne nie będą mogły uzyskać dostępu do baz danych.

### <a name="table-auditing"></a>Inspekcja tabeli

Obecnie istnieją dwa sposoby włączania inspekcji na SQL Database. Inspekcja tabeli kończy się niepowodzeniem po włączeniu punktów końcowych usługi w usłudze Azure SQL Server. Środki zaradcze w tym miejscu umożliwiają przejście do inspekcji obiektów BLOB.

### <a name="impact-on-data-sync"></a>Wpływ na synchronizację danych

Azure SQL Database zawiera funkcję synchronizacji danych, która łączy się z bazami danych przy użyciu adresów IP platformy Azure. W przypadku korzystania z punktów końcowych usługi należy wyłączyć opcję **Zezwól usługom platformy Azure na dostęp do serwera** na serwerze SQL Database i przerwać funkcję synchronizacji danych.

## <a name="ip-firewall-rules"></a>Reguły zapory adresów IP
Zapora oparta na protokole IP to funkcja platformy Azure SQL Server, która zapobiega wszystkim dostępowi do serwera bazy danych, dopóki nie zostaną jawnie [dodane adresy IP](sql-database-server-level-firewall-rule.md) komputerów klienckich.


## <a name="virtual-network-firewall-rules"></a>Virtual Network reguły zapory

Oprócz reguł IP Zapora usługi Azure SQL Server umożliwia definiowanie *reguł sieci wirtualnej*.  
Aby dowiedzieć się więcej, zobacz [Virtual Network punkty końcowe usługi i reguły dla Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md) lub Obejrzyj ten film wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Terminologia dotycząca sieci platformy Azure  
Należy pamiętać o następujących kwestiach dotyczących sieci platformy Azure podczas eksplorowania Virtual Network regułami zapory

**Sieć wirtualna:** Możesz mieć sieci wirtualne skojarzone z subskrypcją platformy Azure 

**Podsieć:** Sieć wirtualna zawiera **podsieci**. Wszystkie maszyny wirtualne platformy Azure, które są przypisane do podsieci. Jedna podsieć może zawierać wiele maszyn wirtualnych lub innych węzłów obliczeniowych. Węzły obliczeniowe znajdujące się poza siecią wirtualną nie mogą uzyskać dostępu do sieci wirtualnej, chyba że skonfigurowano zabezpieczenia, aby zezwolić na dostęp.

**Virtual Network punkt końcowy usługi:** [Virtual Network Service Endpoint] [VM-Virtual-Network-Service-Endpoints-Overview-649d] jest podsiecią, której wartości właściwości zawierają co najmniej jedną formalną nazwę typu usługi platformy Azure. W tym artykule interesuje Cię nazwę typu **Microsoft. SQL**, która odnosi się do usługi platformy Azure o nazwie SQL Database.

**Reguła sieci wirtualnej:** Reguła sieci wirtualnej dla serwera SQL Database to podsieć wymieniona na liście kontroli dostępu (ACL) serwera SQL Database. Aby można było uzyskać listę ACL SQL Database, podsieć musi zawierać nazwę typu **Microsoft. SQL** . Reguła sieci wirtualnej instruuje serwer SQL Database, aby akceptował komunikację z każdego węzła znajdującego się w podsieci.


## <a name="ip-vs-virtual-network-firewall-rules"></a>Reguły zapory dla protokołu IP a Virtual Network

Zapora SQL Server platformy Azure umożliwia określenie zakresów adresów IP, z których ma zostać zaakceptowana komunikacja, SQL Database. To podejście jest odpowiednie dla stabilnych adresów IP, które są poza siecią prywatną platformy Azure. Jednak maszyny wirtualne w sieci prywatnej platformy Azure są skonfigurowane przy użyciu *dynamicznych* adresów IP. Dynamiczne adresy IP mogą ulec zmianie po ponownym uruchomieniu maszyny wirtualnej i w wyniku unieważnienia reguły zapory opartej na protokole IP. Folly do określenia dynamicznego adresu IP w regule zapory w środowisku produkcyjnym.

To ograniczenie można obejść, uzyskując *statyczny* adres IP dla maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie prywatnych adresów IP dla maszyny wirtualnej przy użyciu Azure Portal] [VM-Configure-Private-IP-Address-for-a-Virtual-Machine-using-Azure-Portal-321w]. Jednak podejście ze statycznym adresem IP może być trudne do zarządzania i jest kosztowne, gdy jest wykonywane w odpowiedniej skali. 

Reguły sieci wirtualnej są łatwiejsze do ustanowienia i zarządzania dostępem z określonej podsieci zawierającej maszyny wirtualne.

> [!NOTE]
> Nie można jeszcze mieć SQL Database w podsieci. Jeśli serwer Azure SQL Database był węzłem w podsieci w sieci wirtualnej, wszystkie węzły w sieci wirtualnej mogą komunikować się z SQL Database. W takim przypadku maszyny wirtualne mogą komunikować się z SQL Database bez konieczności używania reguł sieci wirtualnej ani reguł adresów IP.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z przewodnikiem Szybki Start dotyczącym tworzenia reguły zapory adresów IP na poziomie serwera, zobacz [Tworzenie bazy danych Azure SQL Database](sql-database-single-database-get-started.md).

- Aby zapoznać się z przewodnikiem Szybki Start dotyczącym tworzenia reguły zapory sieci wirtualnej na poziomie serwera, zobacz [Virtual Network punkty końcowe usługi i reguły dla Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

- Aby uzyskać pomoc dotyczącą łączenia się z bazą danych Azure SQL Database z aplikacji Open Source lub innych firm, zobacz [przykłady kodu dla klientów z przewodnikiem Szybki Start do SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Aby uzyskać informacje na temat dodatkowych portów, które mogą być konieczne do otwarcia, zobacz sekcję **SQL Database: zewnątrz i wewnątrz** sekcji [portów powyżej 1433 for ADO.NET 4,5 i SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)

- Omówienie łączności Azure SQL Database można znaleźć w temacie [Architektura łączności usługi Azure SQL](sql-database-connectivity-architecture.md)

- Aby zapoznać się z omówieniem zabezpieczeń Azure SQL Database, zobacz [Zabezpieczanie bazy danych](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png
