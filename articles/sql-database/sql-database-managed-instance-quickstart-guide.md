---
title: Szybki start — wystąpienie zarządzane usługi Azure SQL Database | Microsoft Docs
description: Dowiedz się, jak szybko rozpocząć pracę z usługą Azure SQL Database — wystąpienie zarządzane
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 77deed43c106a451d3de768989233c749e1280e1
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468176"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Wprowadzenie do wystąpienia zarządzanego usługi Azure SQL Database

[Wystąpienie zarządzane usługi Azure SQL Database](sql-database-managed-instance-index.yml) to w pełni zarządzana wersja PaaS programu SQL Server hostowana w chmurze platformy Azure, umieszczona w Twojej sieci wirtualnej i dostępna za pośrednictwem prywatnego adresu IP. W tej sekcji dowiesz się, jak szybko skonfigurować i utworzyć wystąpienie zarządzane i zmigrować bazy danych.

## <a name="quickstart-overview"></a>Omówienie przewodnika Szybki start

Ta sekcja zawiera przegląd dostępnych artykułów, które ułatwiają szybkie rozpoczęcie pracy z wystąpieniami zarządzanymi. Najprostszym sposobem na utworzenie pierwszego wystąpienia zarządzanego jest użycie [witryny Azure Portal](sql-database-managed-instance-get-started.md), w której można skonfigurować wymagane parametry (nazwa użytkownika, hasło, liczba rdzeni, maksymalny rozmiar magazynu) i automatycznie utworzyć środowisko sieciowe platformy Azure bez znajomości szczegółów sieci i wymagań dotyczących infrastruktury. Upewnij się, że używany [typ subskrypcji](sql-database-managed-instance-resource-limits.md#supported-subscription-types) ma możliwość utworzenia wystąpienia.

Jeśli chcesz użyć własnej sieci lub dostosować sieć, zobacz instrukcje [konfigurowania środowiska sieciowego](#configure-network-environment) na potrzeby wystąpienia zarządzanego.

Podczas tworzenia wystąpienia zarządzanego jest konieczne nawiązanie połączenia z wystąpieniem przy użyciu jednej z następujących metod:

* Utworzenie [maszyny wirtualnej platformy Azure](sql-database-managed-instance-configure-vm.md) z zainstalowanym programem SQL Server Management Studio i innymi aplikacjami, których można użyć do uzyskania dostępu do wystąpienia zarządzanego w podsieci tej samej sieci wirtualnej, w której znajduje się wystąpienie zarządzane. Maszyna wirtualna nie może znajdować się w tej samej podsieci co wystąpienia zarządzane.
* Skonfigurowanie [połączenia punkt-lokacja](sql-database-managed-instance-configure-p2s.md) na komputerze, co umożliwi przyłączenie komputera do sieci wirtualnej, w której znajduje się wystąpienie zarządzane, i używanie wystąpienia zarządzanego tak jak innych programów SQL Server w używanej sieci.

Możesz też użyć usługi ExpressRoute lub połączenia lokacja-lokacja z sieci lokalnej, lecz te podejścia są poza zakresem tych przewodników Szybki start.

Podczas tworzenia wystąpienia zarządzanego i konfigurowania dostępu możesz rozpocząć migrowanie baz danych w ramach programów SQL Server pracujących lokalnie lub na maszynach wirtualnych platformy Azure. Należy pamiętać, że migracja zakończy się niepowodzeniem, jeśli migrowana źródłowa baza danych obejmuje nieobsługiwane funkcje. Aby uniknąć błędów i sprawdzić zgodność, możesz zainstalować narzędzie [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595), które przeanalizuje wszystkie bazy danych w programie SQL Server i znajdzie wszelkie problemy mogące blokować migrację do wystąpień zarządzanych, takie jak obecność funkcji FileStream lub wiele plików dziennika. Po rozwiązaniu tych problemów bazy danych będą gotowe na przejście do wystąpienia zarządzanego. [Asystent eksperymentowania z bazą danych](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) to kolejne przydatne narzędzie, które umożliwia rejestrowanie obciążeń programu SQL Server i odtwarzanie ich na wystąpieniu zarządzanym, co pozwala na określenie, czy po migracji do wystąpienia zarządzanego będą miały miejsce jakiekolwiek problemy z wydajnością.

Po upewnieniu się, że migracja bazy danych do wystąpienia zarządzanego jest możliwa, możesz użyć [natywnej funkcji RESTORE](sql-database-managed-instance-get-started-restore.md), która pozwala na utworzenie kopii zapasowej bazy danych za pomocą polecenia języka Transact-SQL, przekazanie jej do usługi Azure Blob Storage i przywrócenie z magazynu obiektów blob za pomocą polecenia RESTORE języka Transact-SQL.

Te przewodniki Szybki start umożliwiają szybkie skonfigurowanie, utworzenie i umieszczenie baz danych w wystąpieniach zarządzanych. W przypadku niektórych scenariuszy należy dostosować lub zautomatyzować wdrożenie wystąpienia zarządzanego i wymaganego środowiska sieciowego. Te scenariusze są opisane poniżej.

## <a name="customizing-network-environment"></a>Dostosowywanie środowiska sieciowego

Mimo że sieć wirtualną i podsieć można automatycznie skonfigurować podczas tworzenia wystąpienia przy użyciu [witryny Azure Portal](sql-database-managed-instance-get-started.md), przydatne może być utworzenie ich przed rozpoczęciem tworzenia wystąpień zarządzanych, ponieważ można wtedy skonfigurować parametry sieci wirtualnej i podsieci. Najprostszym sposobem utworzenia i skonfigurowania środowisko sieciowego jest użycie szablonu [wdrażania zasobów platformy Azure](sql-database-managed-instance-create-vnet-subnet.md), który utworzy oraz skonfiguruje sieć i podsieć w miejscu przeznaczonym dla wystąpienia. Wystarczy nacisnąć przycisk wdrażania usługi Azure Resource Manager i podać parametry w formularzu. Jako alternatywy możesz użyć [skryptu programu PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) do zautomatyzowania tworzenia sieci.

Jeśli masz już sieć wirtualną i podsieć, w których chcesz wdrożyć wystąpienie zarządzane, upewnij się, że sieć wirtualna i podsieć spełniają [wymagania dotycząc sieci](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Użyj tego [skryptu programu PowerShell, aby zweryfikować, czy podsieć jest prawidłowo skonfigurowana](sql-database-managed-instance-configure-vnet-subnet.md). Ten skrypt nie tylko zweryfikuje sieć i zgłosi problemy, lecz także określi, co należy zmienić, oraz zaoferuje możliwość wprowadzenia koniecznych zmian sieci wirtualnej lub podsieci. Uruchom ten skrypt, jeśli nie chcesz ręcznie konfigurować sieci wirtualnej i podsieci. Należy go także uruchomić po każdej znacznej zmianie konfiguracji infrastruktury sieciowej. Jeśli chcesz utworzyć i skonfigurować własną sieć, przeczytaj [dokumentację wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md) i [ten przewodnik](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="automating-creation-of-managed-instance"></a>Automatyzowanie tworzenia wystąpienia zarządzanego

 Jeśli nie utworzono środowiska sieciowego zgodnie z opisem w poprzednim kroku, witryna Azure Portal może to zrobić za Ciebie — jedyną wadą jest fakt, że zostanie ono skonfigurowane przy użyciu wartości domyślnych dla niektórych parametrów, a tych wartości nie będzie można zmienić później. Możesz też użyć [programu PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/), [programu PowerShell z szablonem usługi Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md) lub [interfejsu wiersza polecenia platformy Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/).

## <a name="migrating-to-managed-instance-with-minimal-downtime"></a>Migrowanie do wystąpienia zarządzanego z minimalnym przestojem

Artykuły w tych przewodnikach Szybki start umożliwiają szybkie skonfigurowanie wystąpienia zarządzanego i przeniesienie baz danych. Jednak w przypadku użycia natywnej funkcji przywracania będzie konieczne czekanie na przywrócenie bazy danych, co może spowodować przestój aplikacji, szczególnie jeśli baza danych jest duża. Jeśli przenosisz produkcyjną bazę danych, prawdopodobnie potrzebujesz lepszego sposobu migracji, który gwarantuje minimalny przestój. Usługa [Data Migration Service](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) to usługa migracji, która umożliwia zmigrowanie bazy danych z minimalnym przestojem przez przyrostowe wypychanie zmian wprowadzanych w źródłowej bazie danych do bazy danych odtwarzanej do wystąpienia zarządzanego. Dzięki niej można szybko przełączyć aplikację ze źródłowej do docelowej bazy danych z możliwie najkrótszym przestojem.

## <a name="next-steps"></a>Następne kroki

* Przejrzyj [ogólną listę obsługiwanych funkcji wystąpienia zarządzanego](sql-database-features.md) oraz [informacje szczegółowe i znane problemy](sql-database-managed-instance-transact-sql-information.md). 
* Dowiedz się więcej o [charakterystykach technicznych wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits). 
* Bardziej zaawansowane samouczki znajdują się w [sekcji instrukcji](sql-database-howto-managed-instance.md). 
