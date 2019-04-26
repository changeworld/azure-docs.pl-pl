---
title: Migrowanie bazy danych programu SQL Server do programu SQL Server na maszynie Wirtualnej | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat migracji lokalnej bazy danych użytkownika programu SQL Server na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.devlang: na
ms.topic: article
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 95acda60935e82b226a1a0e860b5fa8effb8e47e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60325702"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrowanie bazy danych programu SQL Server do programu SQL Server na maszynie wirtualnej platformy Azure

Istnieje kilka metod migracji lokalnej bazy danych użytkownika programu SQL Server do programu SQL Server na Maszynie wirtualnej platformy Azure. Ten artykuł będzie krótko omówiono różne metody i zaleca się najlepszej metody dla różnych scenariuszy.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="what-are-the-primary-migration-methods"></a>Jakie są metody migracji głównej?
Dostępne są następujące metody migracji głównej:

* Wykonaj kopię zapasową w środowisku lokalnym przy użyciu kompresji i ręcznie skopiować plik kopii zapasowej do maszyny wirtualnej platformy Azure
* Wykonaj kopię zapasową do adresu URL i przywracania do maszyny wirtualnej platformy Azure z adresu URL
* Odłącz kopiowania plików dziennika i danych do usługi Azure blob storage i następnie dołączyć do programu SQL Server na maszynie Wirtualnej platformy Azure z adresu URL
* Konwertuj maszynę fizyczną w środowisku lokalnym do wirtualnego dysku twardego funkcji Hyper-V, Przekaż do usługi Azure Blob storage i następnie wdrożyć nowe maszyny Wirtualnej przy użyciu przekazany wirtualny dysk twardy
* Dostarczaj dysku twardego za pomocą usługi Import/Export Windows
* Jeśli masz wdrożenia zawsze włączonej grupy dostępności w środowisku lokalnym, użyj [Kreatora dodawania repliki platformy Azure](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) tworzenia replik w Azure, a następnie trybu failover, wskazując użytkowników do wystąpienia usługi Azure database
* Użyj programu SQL Server [replikacji transakcyjnej](https://msdn.microsoft.com/library/ms151176.aspx) do skonfiguruj wystąpienie serwera SQL Azure jako subskrybenta, a następnie wyłącz replikację, wskazując użytkowników do wystąpienia usługi Azure database

> [!TIP]
> Można również użyć tych samych technik przenoszenia baz danych między maszyny wirtualne SQL Server na platformie Azure. Na przykład nie ma obsługiwane możliwości uaktualnienia obrazu z galerii Maszynę wirtualną programu SQL Server z jednej wersji/wydania do innego. W takim przypadku należy utworzyć nową maszynę Wirtualną programu SQL Server przy użyciu nowej wersji/wydania i następnie użyj jednej z technik migracji w tym artykule, aby przenieść bazy danych. 

## <a name="choosing-your-migration-method"></a>Wybierając metodę migracji
W celu uzyskania optymalnego danych wydajności transferu migracji plików bazy danych do maszyny Wirtualnej platformy Azure przy użyciu skompresowany plik kopii zapasowej.

Aby skrócić czas przestoju podczas procesu migracji bazy danych, należy użyć opcji zawsze włączone lub opcji replikacji transakcyjnej.

Jeśli nie jest możliwe użycie powyżej metody ręcznego przeprowadzania migracji bazy danych. Za pomocą tej metody, będzie ogólnie rozpoczynać kopię zapasową bazy danych, a następnie kopii zapasowej bazy danych na platformę Azure i następnie wykonaj operację przywracania bazy danych. Możesz także skopiować pliki bazy danych, samodzielnie na platformę Azure, a następnie dołącz je. Istnieje kilka metod, które można wykonać ten ręczny proces migracji bazy danych na Maszynie wirtualnej platformy Azure.

> [!NOTE]
> Po uaktualnieniu do programu SQL Server 2014 lub SQL Server 2016 ze starszych wersji programu SQL Server, należy rozważyć, czy zmiany są potrzebne. Firma Microsoft zaleca adresu wszystkie zależności od funkcji nie są obsługiwane przez nową wersję programu SQL Server w ramach projektu migracji. Aby uzyskać więcej informacji na temat obsługiwanych wersji i scenariuszy, zobacz [uaktualnienia do programu SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

Poniższa tabela zawiera listę metod migracji głównej i w tym artykule omówiono, kiedy korzystanie z każdej metody jest najbardziej odpowiednie.

| Metoda | Wersja bazy danych źródłowych | Bazy danych w wersji docelowej | Ograniczenie rozmiaru kopii zapasowej bazy danych źródłowych | Uwagi |
| --- | --- | --- | --- | --- |
| [Wykonaj kopię zapasową w środowisku lokalnym przy użyciu kompresji i ręcznie skopiować plik kopii zapasowej do maszyny wirtualnej platformy Azure](#backup-and-restore) |Program SQL Server 2005 lub nowszej |Program SQL Server 2005 lub nowszej |[Usługa Azure limit przestrzeni dyskowej maszyny Wirtualnej](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Jest to bardzo proste i dobrze przetestowanych techniki dotyczące przenoszenia baz danych na komputerach. |
| [Wykonaj kopię zapasową do adresu URL i przywracania do maszyny wirtualnej platformy Azure z adresu URL](#backup-to-url-and-restore) |Pakietu CU2 programu SQL Server 2012 z dodatkiem SP1 lub nowszej |Pakietu CU2 programu SQL Server 2012 z dodatkiem SP1 lub nowszej |< 12,8 TB dla programu SQL Server 2016, w przeciwnym razie < 1 TB | Ta metoda jest po prostu inny sposób, aby przenieść plik kopii zapasowej maszyny Wirtualnej, przy użyciu usługi Azure storage. |
| [Odłącz kopiowania plików dziennika i danych do usługi Azure blob storage i następnie dołączyć do programu SQL Server na maszynie wirtualnej platformy Azure z adresu URL](#detach-and-attach-from-url) |Program SQL Server 2005 lub nowszej |Program SQL Server 2014 lub nowszego |[Usługa Azure limit przestrzeni dyskowej maszyny Wirtualnej](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Użyj tej metody, jeśli planujesz [przechowywania tych plików przy użyciu usługi Azure Blob storage](https://msdn.microsoft.com/library/dn385720.aspx) i dołącz je do serwera SQL uruchomionego na Maszynie wirtualnej platformy Azure, zwłaszcza w przypadku bardzo dużych baz danych |
| [Konwertuj na komputerze lokalnym do wirtualnych dysków twardych z funkcją Hyper-V, Przekaż do usługi Azure Blob storage i następnie wdrożyć nową maszynę wirtualną przy użyciu przekazanych wirtualnego dysku twardego](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |Program SQL Server 2005 lub nowszej |Program SQL Server 2005 lub nowszej |[Usługa Azure limit przestrzeni dyskowej maszyny Wirtualnej](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Zastosowania [użycie własnej licencji programu SQL Server](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md), podczas migracji bazy danych, która uruchomi się ze starszą wersją programu SQL Server lub podczas migrowania baz danych z systemu i użytkownika ze sobą w ramach migracji bazy danych jest zależny od innych baz danych użytkownika i/lub systemowych bazach danych. |
| [Dostarczaj dysku twardego za pomocą usługi Import/Export Windows](#ship-hard-drive) |Program SQL Server 2005 lub nowszej |Program SQL Server 2005 lub nowszej |[Usługa Azure limit przestrzeni dyskowej maszyny Wirtualnej](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Użyj [usługi Import/Export Windows](../../../storage/common/storage-import-export-service.md) kiedy Metoda ręcznego kopiowania jest zbyt wolno, takie jak w przypadku bardzo dużych baz danych |
| [Użyj repliki platformy Azure Kreator dodawania](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |Program SQL Server 2012 lub nowszej |Program SQL Server 2012 lub nowszej |[Usługa Azure limit przestrzeni dyskowej maszyny Wirtualnej](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Minimalizuje przestoje, użyj w przypadku wdrożenia zawsze włączonej w środowisku lokalnym |
| [Użyj replikację transakcyjną programu SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) |Program SQL Server 2005 lub nowszej |Program SQL Server 2005 lub nowszej |[Usługa Azure limit przestrzeni dyskowej maszyny Wirtualnej](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Używać, gdy potrzebujesz zminimalizować przestoje i nie są zawsze na lokalne wdrożenie |

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie
Utwórz kopię zapasową bazy danych z kompresją pliku kopii zapasowej na maszynie wirtualnej, a następnie przywróć bazy danych. Jeśli plik kopii zapasowej jest większy niż 1 TB, musi ona stripe, ponieważ maksymalny rozmiar dysku maszyny Wirtualnej wynosi 1 TB. Aby przeprowadzić migrację bazy danych użytkownika za pomocą tej metody ręcznego, należy użyć następujące ogólne kroki:

1. Wykonaj pełną kopię zapasową bazy w lokalizacji lokalnej.
2. Utwórz lub maszynę wirtualną przy użyciu wersji programu SQL Server na potrzeby przekazywania.
3. Konfiguracja łączności, w zależności od wymagań. Zobacz [nawiązać połączenie z maszyną wirtualną programu SQL Server na platformie Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
4. Skopiuj pliki kopii zapasowej dla maszyny wirtualnej przy użyciu pulpitu zdalnego, Eksploratora Windows lub polecenia kopiowania w wierszu polecenia.

## <a name="backup-to-url-and-restore"></a>Tworzenie kopii zapasowej na adres URL i przywracania
Zamiast wykonywania kopii zapasowych pliku lokalnego, możesz użyć [kopii zapasowej do adresu URL](https://msdn.microsoft.com/library/dn435916.aspx) , a następnie Przywróć z adresu URL do maszyny Wirtualnej. Za pomocą programu SQL Server 2016 rozłożone zestawów kopii zapasowych są obsługiwane, są zalecane w przypadku wydajności i wymaganą do przekroczenia limitów rozmiaru na obiekt blob. W przypadku bardzo dużych baz danych korzystanie z [usługi Import/Export Windows](../../../storage/common/storage-import-export-service.md) jest zalecane.

## <a name="detach-and-attach-from-url"></a>Odłącz i dołączyć z adresu URL
Odłącz pliki dziennika i bazy danych i przenieść je na [usługi Azure Blob storage](https://msdn.microsoft.com/library/dn385720.aspx). Następnie można dołączyć bazy danych z adresu URL na maszynie Wirtualnej platformy Azure. Użyj, jeżeli chcesz pliki fizycznej bazy danych, znajdują się w magazynie obiektów Blob. Może to być przydatne w przypadku bardzo dużych baz danych. Aby przeprowadzić migrację bazy danych użytkownika za pomocą tej metody ręcznego, należy użyć następujące ogólne kroki:

1. Odłącz pliki bazy danych z lokalnego wystąpienia bazy danych.
2. Skopiuj pliki odłączono bazę danych do magazynu obiektów blob platformy Azure przy użyciu [wiersza polecenia azcopy](../../../storage/common/storage-use-azcopy.md).
3. Dołącz pliki bazy danych z adresu URL platformy Azure do wystąpienia programu SQL Server na maszynie Wirtualnej platformy Azure.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Przeprowadź konwersję na maszynę wirtualną, przekaż ją do adresu URL i wdróż ją jako nową maszynę wirtualną
Ta metoda umożliwia migrację wszystkich systemowych i użytkownika bazy danych w wystąpieniu programu SQL Server w środowisku lokalnym do maszyny wirtualnej platformy Azure. Do całego wystąpienia programu SQL Server przy użyciu tej metody ręcznej migracji, należy użyć następujące ogólne kroki:

1. Konwertowanie maszyn fizycznych lub wirtualnych do wirtualnych dysków twardych z funkcją Hyper-V.
2. Przekazywanie plików wirtualnego dysku twardego do usługi Azure Storage za pomocą [polecenia cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Wdrożyć nową maszynę wirtualną przy użyciu przekazanych wirtualnego dysku twardego.

> [!NOTE]
> Aby przeprowadzić migrację całej aplikacji, należy rozważyć użycie [usługi Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Dostarczaj dysku twardego
Użyj [metody usługi Import/Export Windows](../../../storage/common/storage-import-export-service.md) przesyłanie dużych ilości danych plików do usługi Azure Blob storage w sytuacjach, w którym przekazywanie za pośrednictwem sieci jest zbyt duży lub nie jest to możliwe. Z tą usługą są wysyłane co najmniej jeden dyski twarde zawierające dane do centrum danych platformy Azure, w którym dane zostaną przekazane do konta magazynu.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](virtual-machines-windows-sql-server-iaas-overview.md).

> [!TIP]
> Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).

Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej Azure SQL Server z przechwyconego obrazu, zobacz [porady i wskazówki na klonowania maszyn wirtualnych usługi Azure SQL z przechwyconych obrazów](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) blogu Engineers serwera SQL CSS.

