---
title: Szybki Start — Azure SQL Database wystąpienie zarządzane | Microsoft Docs
description: Dowiedz się, jak szybko rozpocząć pracę z wystąpieniem zarządzanym Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 07/11/2019
ms.openlocfilehash: 65b6b503d107b36813d1716348ce5f11fa840cc0
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937216"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Wprowadzenie do Azure SQL Database wystąpienia zarządzanego

Opcja wdrożenia [wystąpienia zarządzanego](sql-database-managed-instance-index.yml) powoduje utworzenie bazy danych o prawie 100% zgodności z najnowszym SQL Server lokalnego aparatu bazy danych (Enterprise Edition), zapewniającym natywną implementację [sieci wirtualnej (VNET)](../virtual-network/virtual-networks-overview.md) , która dotyczy Typowe problemy z zabezpieczeniami i [model biznesowy](https://azure.microsoft.com/pricing/details/sql-database/) preferowany dla klientów lokalnych SQL Server. W tym artykule dowiesz się, jak szybko skonfigurować i utworzyć wystąpienie zarządzane i przeprowadzić migrację baz danych.

## <a name="quickstart-overview"></a>Przegląd szybkiego startu

Poniższe przewodniki szybki start umożliwiają szybkie tworzenie wystąpienia zarządzanego, Konfigurowanie maszyny wirtualnej lub Nawiązywanie połączenia sieci VPN dla aplikacji klienckiej oraz przywracanie bazy danych do nowego wystąpienia zarządzanego przy użyciu pliku `.bak`.

### <a name="configure-environment"></a>Konfigurowanie środowiska

Pierwszym krokiem jest utworzenie pierwszego wystąpienia zarządzanego w środowisku sieciowym, w którym zostaną umieszczone, i włączenie połączenia z komputera lub maszyny wirtualnej, w której są wykonywane zapytania do wystąpienia zarządzanego. Można użyć następujących przewodników:

- [Utwórz wystąpienie zarządzane przy użyciu Azure Portal](sql-database-managed-instance-get-started.md). W Azure Portal można skonfigurować niezbędne parametry (nazwa użytkownika/hasło, liczba rdzeni i maksymalny rozmiar magazynu) i automatycznie utworzyć środowisko sieciowe platformy Azure bez konieczności poznania szczegółów sieci i wymagań dotyczących infrastruktury. Wystarczy upewnić się, że masz [Typ subskrypcji](sql-database-managed-instance-resource-limits.md#supported-subscription-types) , który aktualnie może utworzyć wystąpienie zarządzane. Jeśli używasz własnej sieci, która ma być używana, lub chcesz dostosować sieć, zobacz [Konfigurowanie istniejącej sieci wirtualnej dla Azure SQL Database wystąpienia zarządzanego](sql-database-managed-instance-configure-vnet-subnet.md) lub [Tworzenie sieci wirtualnej dla Azure SQL Database wystąpienia zarządzanego](sql-database-managed-instance-create-vnet-subnet.md).
- Wystąpienie zarządzane jest tworzone w własnej sieci wirtualnej bez publicznego punktu końcowego. Aby uzyskać dostęp do aplikacji klienckiej, można **utworzyć maszynę wirtualną w tej samej sieci wirtualnej (innej podsieci)** lub **utworzyć połączenie sieci VPN typu punkt-lokacja z siecią wirtualną z komputera klienckiego** przy użyciu jednego z następujących przewodników szybki start:
  - Włącz [publiczny punkt końcowy](sql-database-managed-instance-public-endpoint-configure.md) w wystąpieniu zarządzanym, aby uzyskać dostęp do danych bezpośrednio ze środowiska.
  - Utwórz [maszynę wirtualną platformy Azure w sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-configure-vm.md) na potrzeby łączności aplikacji klienckiej, w tym SQL Server Management Studio.
  - Skonfiguruj [połączenie sieci VPN typu punkt-lokacja z wystąpieniem zarządzanym](sql-database-managed-instance-configure-p2s.md) z komputera klienckiego, na którym masz SQL Server Management Studio i inne aplikacje łączności klienta. Jest to inne dwie opcje łączności z wystąpieniem zarządzanym i jego siecią wirtualną.

  > [!NOTE]
  > Możesz również użyć usługi Express Route lub połączenia typu lokacja-lokacja z sieci lokalnej, ale te podejścia są poza zakresem tych przewodników Szybki Start.

Alternatywą dla ręcznego tworzenia wystąpienia zarządzanego jest używanie [programu PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md), [programu PowerShell z szablonem Menedżer zasobów](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) do tworzenia skryptów i automatyzowania tego procesu.

### <a name="migrate-your-databases"></a>Migrowanie baz danych

Po utworzeniu wystąpienia zarządzanego i skonfigurowaniu dostępu można rozpocząć migrację baz danych z SQL Server lokalnych lub maszyn wirtualnych platformy Azure. Migracja nie powiedzie się, jeśli w źródłowej bazie danych są dostępne Nieobsługiwane funkcje, które chcesz zmigrować. Aby uniknąć błędów i sprawdzania zgodności, można zainstalować [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) , które analizują bazy danych na SQL Server i odnajdują wszelkie problemy, które mogą blokować migrację do wystąpienia zarządzanego, takie jak obecność [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) lub wiele plików dziennika. W przypadku rozwiązania tych problemów bazy danych są gotowe do migracji do wystąpienia zarządzanego. [Asystent eksperymentowania z bazą danych](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) to kolejne przydatne narzędzie, które może rejestrować obciążenie na SQL Server i powtarzać je w wystąpieniu zarządzanym, dzięki czemu można określić, czy podczas migracji do wystąpienia zarządzanego będą dostępne jakiekolwiek problemy z wydajnością.

Po upewnieniu się, że można przeprowadzić migrację bazy danych do wystąpienia zarządzanego, możesz użyć funkcji przywracania natywnego SQL Server, aby przywrócić bazę danych do wystąpienia zarządzanego z pliku `.bak`. Za pomocą tej metody można migrować bazy danych z aparatu bazy danych SQL Server zainstalowane lokalnie lub na maszynie wirtualnej platformy Azure. Aby zapoznać się z przewodnikiem Szybki Start, zobacz [przywracanie z kopii zapasowej do wystąpienia zarządzanego](sql-database-managed-instance-get-started-restore.md). W tym przewodniku szybki start można przywrócić plik `.bak` zapisany w usłudze Azure Blob Storage za pomocą polecenia języka Transact-SQL `RESTORE`.

> [!TIP]
> Aby utworzyć kopię zapasową bazy danych w usłudze Azure Blob Storage za pomocą polecenia `BACKUP` Transact-SQL, zobacz [SQL Server kopia zapasowa do adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Te Przewodniki Szybki Start pozwalają szybko tworzyć, konfigurować i przywracać kopie zapasowe bazy danych do wystąpienia zarządzanego. W niektórych scenariuszach należy dostosować lub zautomatyzować wdrożenie wystąpień zarządzanych i wymaganego środowiska sieciowego. Te scenariusze zostaną opisane poniżej.

## <a name="customize-network-environment"></a>Dostosuj środowisko sieciowe

Mimo że sieci wirtualnej/podsieci można automatycznie skonfigurować podczas tworzenia wystąpienia przy użyciu [Azure Portal](sql-database-managed-instance-get-started.md), warto je utworzyć przed rozpoczęciem tworzenia wystąpień zarządzanych, ponieważ można skonfigurować parametry sieci wirtualnej i podsieci. Najprostszym sposobem tworzenia i konfigurowania środowiska sieciowego jest użycie szablonu [wdrażania zasobów platformy Azure](sql-database-managed-instance-create-vnet-subnet.md) , który utworzy i skonfiguruje sieć i podsieć, w której zostanie umieszczone wystąpienie. Wystarczy nacisnąć przycisk Azure Resource Manager Wdróż i wypełnić formularz parametrami.

Alternatywnie można również użyć tego [skryptu programu PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) do zautomatyzowania tworzenia sieci.

Jeśli masz już sieć wirtualną i podsieci, w której chcesz wdrożyć swoje wystąpienie zarządzane, musisz upewnić się, że sieć wirtualna i podsieci spełniają [wymagania sieciowe](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Użyj tego [skryptu programu PowerShell, aby sprawdzić, czy podsieć jest prawidłowo skonfigurowana](sql-database-managed-instance-configure-vnet-subnet.md). Ten skrypt sprawdza poprawność sieci i zgłasza wszelkie problemy oraz informuje, co należy zmienić, a następnie oferuje możliwość wprowadzenia niezbędnych zmian w sieci wirtualnej/podsieci. Uruchom ten skrypt, jeśli nie chcesz ręcznie konfigurować sieci wirtualnej/podsieci. Można go również uruchomić po każdej istotnej ponownej konfiguracji infrastruktury sieciowej. Jeśli chcesz utworzyć i skonfigurować własną sieć, zapoznaj się z artykułem [Architektura łączności](sql-database-managed-instance-connectivity-architecture.md) i ten [ostateczny Przewodnik dotyczący tworzenia i konfigurowania środowiska wystąpienia zarządzanego](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="migrate-to-a-managed-instance"></a>Migrowanie do wystąpienia zarządzanego

Artykuły w tych przewodnikach szybki start umożliwiają szybką konfigurację wystąpienia zarządzanego i przenoszenie baz danych przy użyciu funkcji natywnej `RESTORE`. Jest to dobry punkt wyjścia, jeśli chcesz zakończyć szybkie sprawdzanie koncepcji i sprawdzić, czy rozwiązanie może współpracować z wystąpieniem zarządzanym. 

Jednak w celu migrowania produkcyjnej bazy danych, a nawet tworzenia/testowania baz danych, które mają być używane w przypadku niektórych testów wydajności, należy rozważyć użycie pewnych dodatkowych technik, takich jak:
- Testowanie wydajności — należy mierzyć wydajność linii bazowej na źródłowym wystąpieniu SQL Server i porównać je z wydajnością w docelowym wystąpieniu zarządzanym, w którym przeprowadzono migrację bazy danych. Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących porównywania wydajności](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).
- Migracja w trybie online — z natywną `RESTORE` opisanym w tym artykule musisz poczekać, aż bazy danych mają zostać przywrócone (i skopiowane do usługi Azure Blob Storage, jeśli jeszcze nie są przechowywane). Powoduje to pewne przestoje aplikacji szczególnie w przypadku większych baz danych. Aby przenieść produkcyjną bazę danych, użyj [usługi Data Migration Service (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) do migracji bazy danych o minimalnym przestoju. Usługa DMS osiąga to przez przyrostowe wypychanie zmian wprowadzonych w źródłowej bazie danych do przywracanej bazy danych wystąpienia zarządzanego. Dzięki temu można szybko przełączać aplikację z lokalizacji źródłowej do docelowej bazy danych o minimalnym przestoju.

Dowiedz się więcej o [zalecanym procesie migracji](sql-database-managed-instance-migrate.md).

## <a name="next-steps"></a>Następne kroki

- Znajdź [tutaj listę obsługiwanych funkcji w wystąpieniu zarządzanym](sql-database-features.md) oraz [szczegóły i znane problemy w tym miejscu](sql-database-managed-instance-transact-sql-information.md).
- Dowiedz się więcej o [cechach technicznych wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).
- Dowiedz się więcej na temat [sposobu używania wystąpienia zarządzanego w Azure SQL Database](sql-database-howto-managed-instance.md).
- [Zidentyfikuj odpowiednią Azure SQL Database/jednostkę SKU wystąpienia zarządzanego dla lokalnej bazy danych](/sql/dma/dma-sku-recommend-sql-db/).
