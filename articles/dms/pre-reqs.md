---
title: Przegląd wymagań wstępnych dotyczących używania usługi migracji bazy danych Azure | Dokumentacja firmy Microsoft
description: Zapoznaj się z omówieniem wymagań wstępnych do przeprowadzania migracji bazy danych przy użyciu usługi Azure bazy danych migracji.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/19/2018
ms.openlocfilehash: 3dc449724e405f83ce976b9f8b01a89c25d693fe
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221254"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Przegląd wymagań wstępnych dotyczących używania usługi Azure bazy danych migracji
Istnieje kilka wymagań wstępnych dotyczących upewnić, że usługa migracji bazy danych Azure przeprowadzane bezproblemowego podczas przeprowadzania migracji bazy danych. Niektóre wymagania wstępne są stosowane we wszystkich scenariuszach (pary źródłowy i docelowy), obsługiwane przez usługę, podczas gdy inne wymagania wstępne są unikatowe dla danego scenariusza.

Wymagania wstępne skojarzone z korzystania z usługi migracji bazy danych Azure są wymienione w poniższych sekcjach.

## <a name="prerequisites-common-across-migration-scenarios"></a>Wymagania wstępne dotyczące typowych scenariuszy migracji
Azure wymagania wstępne usługi migracji bazy danych, które są wspólne dla wszystkich obsługiwane scenariusze migracji obejmują konieczność:
- Tworzenie sieci Wirtualnej dla usługi Azure migracji bazy danych przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia połączenie lokacja lokacja z serwerami lokalnymi źródła przy użyciu [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Upewnij się, że Twoje Azure (VNET) sieciowej grupy zabezpieczeń sieci wirtualnej reguły blok następujący komunikat porty 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółów na filtrowanie ruchu NSG sieci Wirtualnej Azure, zobacz artykuł [filtrowania ruchu sieciowego z grup zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Podczas korzystania z urządzenia zapory przed baz danych z źródła, może być konieczne dodanie reguły zapory, aby umożliwić dostęp do bazy danych źródłowego do migracji z usługi migracji bazy danych Azure.
- Konfigurowanie sieci [zapory systemu Windows dla dostępu aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Włącz protokół TCP/IP, które jest domyślnie wyłączone podczas instalacji programu SQL Server Express przez postępując zgodnie z instrukcjami w artykule [włączyć lub wyłączyć protokół sieciowy serwera](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Wymagania wstępne dotyczące migracji serwera SQL z bazą danych SQL Azure 
Oprócz wymagań wstępnych usługi migracji bazy danych Azure, które są wspólne dla wszystkich scenariuszy migracji są również wymagania wstępne, które dotyczą przede wszystkim jednego scenariusza lub innej.

Podczas korzystania z usługi migracji bazy danych Azure do wykonania programu SQL Server do migracji bazy danych SQL Azure, oprócz wymagań wstępnych, które są wspólne dla wszystkich scenariuszy migracji, należy rozwiązać następujące dodatkowe wymagania wstępne:

- Utwórz wystąpienie wystąpienia bazy danych SQL Azure, co zrobić, wykonując szczegółowo w artykule C[Utwórz bazę danych Azure SQL w portalu Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Pobierz i zainstaluj [Asystenta migracji danych](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 lub nowszym.
- Otwórz Zaporę systemu Windows, aby umożliwić usłudze migracji bazy danych Azure dostępu do źródła SQL Server, która domyślnie jest TCP port 1433.
- Jeśli używasz wielu wystąpień programu SQL Server o nazwie przy użyciu portów dynamicznych, możesz włączyć usługę Przeglądarka SQL i umożliwiają dostęp do portu UDP 1434 na zaporach, dzięki czemu usługa migracji bazy danych Azure mogą łączyć się nazwanego wystąpienia w źródle serwer.
- Tworzenie z poziomu serwera [reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) dla serwera bazy danych SQL Azure, aby umożliwić dostęp z migracji bazy danych Azure do docelowymi bazami danych. Podaj zakres podsieci sieci wirtualnej używane przez usługę Azure migracji bazy danych.
- Upewnij się, że poświadczenia użyte do nawiązania połączenia źródła wystąpienia programu SQL Server [serwera kontroli](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) uprawnienia.
- Sprawdź, czy poświadczenia użyte do nawiązania połączenia docelowego wystąpienia bazy danych SQL Azure mają uprawnienia bazy danych kontroli na docelowymi bazami danych Azure SQL.

   > [!NOTE]
   > Aby uzyskać pełną listę wymagań wstępnych dotyczących korzystania z usługi migracji bazy danych Azure do przeprowadzenia migracji z programu SQL Server do bazy danych SQL Azure, zobacz samouczek [migracji programu SQL Server do bazy danych SQL Azure](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database-managed-instance"></a>Wymagania wstępne dotyczące migracji programu SQL Server do wystąpienia zarządzane bazy danych SQL Azure
- Utwórz wystąpienie wystąpienia zarządzane bazy danych SQL Azure, wykonując szczegółowo w artykule [utworzyć wystąpienia zarządzanego bazy danych SQL Azure w portalu Azure](https://aka.ms/sqldbmi).
- Otwórz z zapory do zezwalania na ruch związany z protokołem SMB przez port 445 w zakresie adresu lub podsieci IP usługi migracji bazy danych Azure.
- Otwórz Zaporę systemu Windows, aby umożliwić usłudze migracji bazy danych Azure dostępu do źródła SQL Server, która domyślnie jest TCP port 1433.
- Jeśli używasz wielu wystąpień programu SQL Server o nazwie przy użyciu portów dynamicznych, możesz włączyć usługę Przeglądarka SQL i umożliwiają dostęp do portu UDP 1434 na zaporach, dzięki czemu usługa migracji bazy danych Azure mogą łączyć się nazwanego wystąpienia w źródle serwer.
- Sprawdź, czy dane logowania umożliwiający połączenie źródła programu SQL Server i zarządzane wystąpienia docelowego są członkowie roli serwera sysadmin.
- Utwórz udział sieciowy, który usługa migracji bazy danych Azure umożliwia tworzenie kopii zapasowej źródłowej bazy danych.
- Upewnij się, że konto usługi uruchamiania źródła ma wystąpienie programu SQL Server uprawnienia do zapisu na udział sieciowy, który został utworzony i czy konto komputera serwera źródłowego ma dostęp do odczytu i zapisu do tego samego udziału.
- Zwróć uwagę użytkownika systemu Windows (i hasło), który ma uprawnienia pełnej kontroli w udziale sieciowym, która została wcześniej utworzona. Usługa Azure bazy danych migracji personifikuje poświadczenia użytkownika, aby przekazać pliki kopii zapasowej do kontenera magazynu systemu Azure dla operacji przywracania.
- Tworzenie kontenera obiektów blob i pobrać jego identyfikatora URI połączenia SAS, wykonując kroki opisane w artykule [zasobów Zarządzanie magazynu obiektów Blob Azure przy użyciu Eksploratora usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Należy wybrać wszystkie uprawnienia (do odczytu, zapisu i usuwania, listy) w oknie zasad podczas tworzenia identyfikatora URI połączenia SAS.

   > [!NOTE]
   > Aby uzyskać pełną listę wymagań wstępnych dotyczących korzystania z usługi migracji bazy danych Azure do przeprowadzenia migracji z programu SQL Server do wystąpienia zarządzane bazy danych SQL Azure, zobacz samouczek [migracji programu SQL Server do wystąpienia zarządzane bazy danych SQL Azure ](https://aka.ms/migratetomiusingdms).

## <a name="next-steps"></a>Kolejne kroki
Omówienie usługi migracji bazy danych Azure i regionalnych dostępności, zobacz artykuł [co to jest usługa Azure bazy danych migracji](dms-overview.md). 