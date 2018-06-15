---
title: Migrowanie bazy danych programu SQL Server do programu SQL Server na maszynie Wirtualnej | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat przeprowadzania migracji bazy danych użytkowników lokalnych z programem SQL Server w maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: jroth
ms.openlocfilehash: 64245a968eca94518d2e4238b4bc5c93c952563a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
ms.locfileid: "29809441"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrowanie bazy danych programu SQL Server do programu SQL Server na maszynie wirtualnej platformy Azure

Istnieje wiele metod, aby przeprowadzić migrację bazy danych użytkowników lokalnych programu SQL Server do programu SQL Server w maszynie Wirtualnej platformy Azure. W tym artykule krótko będzie omówienia różnych metod i zaleca najlepszej metody dla różnych scenariuszy.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="what-are-the-primary-migration-methods"></a>Jakie są metody głównej migracji?
Dostępne są następujące metody głównej migracji:

* Wykonaj kopię zapasową lokalnej za pomocą metody kompresji i ręcznie skopiować plik kopii zapasowej do maszyny wirtualnej platformy Azure
* Wykonywanie kopii zapasowej do adresu URL i przywracania na maszynie wirtualnej platformy Azure z adresu URL
* Odłącz i następnie skopiuj pliki danych i dziennika do magazynu obiektów blob platformy Azure, a następnie dołącz do programu SQL Server w maszynie Wirtualnej platformy Azure z adresu URL
* Konwertuj na lokalnej maszynie fizycznej do wirtualnego dysku twardego funkcji Hyper-V, przekazać do magazynu obiektów Blob platformy Azure, a następnie Wdróż zgodnie z nowej maszyny Wirtualnej za pomocą przekazać wirtualnego dysku twardego
* Wyślij dysk twardy za pomocą usługi Import/Eksport systemu Windows
* Jeśli masz wdrożenie AlwaysOn lokalnie, należy użyć [Kreatora dodawania repliki Azure](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) do utworzenia repliki w Azure, a następnie trybu failover, wskazujący użytkowników do wystąpienia bazy danych platformy Azure
* Użyj programu SQL Server [replikacji transakcyjnej](https://msdn.microsoft.com/library/ms151176.aspx) skonfiguruj wystąpienie serwera SQL Azure jako subskrybent i następnie wyłącz replikację, wskazujący użytkowników do wystąpienia bazy danych platformy Azure

> [!TIP]
> Umożliwia także te same techniki przenoszenia baz danych między maszynami wirtualnymi serwera SQL na platformie Azure. Na przykład jest obsługiwana można uaktualnić maszyny Wirtualnej galerii obrazu programu SQL Server z jednej wersji/wydania do innego. W takim przypadku należy utworzyć nową maszynę Wirtualną programu SQL Server z nowej wersji/wydania, a następnie użyj jednej z metod migracji w tym artykule, aby przenieść bazy danych. 

## <a name="choosing-your-migration-method"></a>Wybieranie metody migracji
Dla danych optymalną wydajność transferu migracja pliki bazy danych do maszyny Wirtualnej platformy Azure przy użyciu skompresowany plik kopii zapasowej.

Aby zminimalizować czas przestoju podczas procesu migracji bazy danych, należy użyć opcji zawsze włączone albo opcji replikacji transakcyjnej.

Jeśli nie jest możliwe użycie metod powyżej, ręcznej migracji bazy danych. Za pomocą tej metody, będzie zwykle zaczyna się od kopii zapasowej bazy danych, a następnie kopii zapasowej bazy danych na platformie Azure i następnie wykonaj operację przywracania bazy danych. Można także skopiować pliki bazy danych, samodzielnie na platformie Azure, a następnie dołącz je. Istnieje kilka metod, które można wykonać ten ręczny proces migracji bazy danych do maszyny Wirtualnej platformy Azure.

> [!NOTE]
> Po uaktualnieniu do programu SQL Server 2014 lub SQL Server 2016 ze starszych wersji programu SQL Server, należy rozważyć, czy zmiany są potrzebne. Zaleca się zajęcie się wszystkie zależności w funkcji nie są obsługiwane przez nową wersję programu SQL Server w ramach migracji projektu. Aby uzyskać więcej informacji o obsługiwanych wersjach i scenariuszy, zobacz [uaktualnienia do programu SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

Poniższa tabela zawiera listę metod migracji podstawowego oraz omówiono, gdy korzystanie z każdej z metod jest najbardziej odpowiednia.

| Metoda | Wersja bazy danych źródła | Docelowej wersji bazy danych | Ograniczenia rozmiaru kopii zapasowej bazy danych źródła | Uwagi |
| --- | --- | --- | --- | --- |
| [Wykonaj kopię zapasową lokalnej za pomocą metody kompresji i ręcznie skopiować plik kopii zapasowej do maszyny wirtualnej platformy Azure](#backup-and-restore) |SQL Server 2005 lub nowszy |SQL Server 2005 lub nowszy |[Limit magazynu maszyny Wirtualnej platformy Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Jest to bardzo proste i dobrze przetestowane technika przenoszenia baz danych na komputerach. |
| [Wykonywanie kopii zapasowej do adresu URL i przywracania na maszynie wirtualnej platformy Azure z adresu URL](#backup-to-url-and-restore) |SQL Server 2012 z dodatkiem SP1 CU2 lub nowszej |SQL Server 2012 z dodatkiem SP1 CU2 lub nowszej |< 12,8 TB dla programu SQL Server 2016, w przeciwnym razie < 1 TB | Jest to po prostu inną sposób, aby przenieść plik kopii zapasowej maszyny Wirtualnej za pomocą usługi Azure storage. |
| [Odłącz i następnie skopiuj pliki danych i dziennika do magazynu obiektów blob platformy Azure, a następnie dołącz do programu SQL Server na maszynie wirtualnej platformy Azure z adresu URL](#detach-and-attach-from-url) |SQL Server 2005 lub nowszy |SQL Server 2014 lub nowszy |[Limit magazynu maszyny Wirtualnej platformy Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Użyj tej metody, gdy planujesz [przechowywania tych plików przy użyciu usługi magazynu obiektów Blob platformy Azure](https://msdn.microsoft.com/library/dn385720.aspx) i dołącz je do programu SQL Server uruchomionego na maszynie Wirtualnej Azure, szczególnie w przypadku bardzo dużych baz danych |
| [Konwersji lokalne maszyny wirtualne dyski twarde funkcji Hyper-V, przekazać do magazynu obiektów Blob platformy Azure, a następnie wdrożyć maszynę wirtualną przy użyciu przekazywanego wirtualnego dysku twardego](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 lub nowszy |SQL Server 2005 lub nowszy |[Limit magazynu maszyny Wirtualnej platformy Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Używany, gdy [dostarczają licencję programu SQL Server](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md), podczas migracji bazy danych, które zostanie uruchomione na starszej wersji programu SQL Server lub podczas migrowania systemu i użytkownika bazy danych ze sobą w ramach migracji bazy danych jest zależny od innych bazy danych użytkownika i/lub systemowych bazach danych. |
| [Wyślij dysk twardy za pomocą usługi Import/Eksport systemu Windows](#ship-hard-drive) |SQL Server 2005 lub nowszy |SQL Server 2005 lub nowszy |[Limit magazynu maszyny Wirtualnej platformy Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Użyj [usługi Import/Eksport Windows](../../../storage/common/storage-import-export-service.md) gdy Metoda ręcznego kopiowania jest zbyt wolno, takie jak w przypadku bardzo dużych baz danych |
| [Użyj repliki Azure Kreator dodawania](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 lub nowszy |SQL Server 2012 lub nowszy |[Limit magazynu maszyny Wirtualnej platformy Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Minimalizuje przestojów, używania wdrożenie lokalnymi zawsze włączone |
| [Użyj Replikacja transakcyjna programu SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 lub nowszy |SQL Server 2005 lub nowszy |[Limit magazynu maszyny Wirtualnej platformy Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Używać, gdy konieczne w celu zminimalizowania przestojów i nie są zawsze włączone lokalnego wdrożenia |

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie
Utwórz kopię zapasową bazy danych z kompresją pliku kopii zapasowej do maszyny Wirtualnej, a następnie przywróć bazę danych. Jeśli w pliku kopii zapasowej jest większy niż 1 TB, musi paskowych ponieważ maksymalny rozmiar dysku maszyny Wirtualnej wynosi 1 TB. Aby przeprowadzić migrację bazy danych użytkownika za pomocą tej metody ręcznego, należy użyć następujące ogólne czynności:

1. Wykonanie kopii zapasowej pełnej bazy danych do lokalizacji lokalnej.
2. Utwórz lub Przekaż maszynę wirtualną z wersją programu SQL Server na potrzeby.
3. Konfiguracja połączenia, w zależności od wymagań. Zobacz [połączenie z maszyną wirtualną programu SQL Server na platformie Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
4. Skopiuj pliki kopii zapasowej do maszyny Wirtualnej za pomocą pulpitu zdalnego, Eksploratora Windows lub polecenia kopiowania w wierszu polecenia.

## <a name="backup-to-url-and-restore"></a>Wykonywanie kopii zapasowej na adres URL i przywracania
Zamiast tworzenie kopii zapasowych pliku lokalnego, możesz użyć [kopii zapasowej do adresu URL](https://msdn.microsoft.com/library/dn435916.aspx) , a następnie Przywróć z adresu URL do maszyny Wirtualnej. Z programem SQL Server 2016 rozłożone zestawy kopii zapasowych są obsługiwane, są zalecane w przypadku wydajności i wymaganą do przekroczenia limitów rozmiaru dla obiekt blob. W przypadku bardzo dużych baz danych, korzystanie z [usługi Import/Eksport Windows](../../../storage/common/storage-import-export-service.md) jest zalecane.

## <a name="detach-and-attach-from-url"></a>Odłącz i Dołącz z adresu URL
Odłączanie plików dziennika i bazy danych i przenieść je na [magazynu obiektów Blob Azure](https://msdn.microsoft.com/library/dn385720.aspx). Następnie można dołączyć bazy danych z adresu URL na maszynie Wirtualnej platformy Azure. Użyj, jeśli chcesz, aby pliki fizycznej bazy danych do znajdują się w magazynie obiektów Blob. Może to być przydatne w przypadku bardzo dużych baz danych. Aby przeprowadzić migrację bazy danych użytkownika za pomocą tej metody ręcznego, należy użyć następujące ogólne czynności:

1. Odłączyć pliki bazy danych z lokalnego wystąpienia bazy danych.
2. Skopiuj pliki odłączono bazę danych do magazynu obiektów blob platformy Azure za pomocą [wiersza polecenia azcopy](../../../storage/common/storage-use-azcopy.md).
3. Dołącz pliki bazy danych z adresu URL platformy Azure do wystąpienia programu SQL Server w maszynie Wirtualnej platformy Azure.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Przeprowadź konwersję na maszynę wirtualną, przekaż ją do adresu URL i wdróż ją jako nową maszynę wirtualną
Ta metoda umożliwia migrację wszystkich baz danych systemu i użytkownika w lokalnym wystąpieniem programu SQL Server do maszyny wirtualnej platformy Azure. Umożliwia migrację całego wystąpienia programu SQL Server za pomocą tej metody ręcznego następujące ogólne czynności:

1. Konwertuj maszyn fizycznych lub wirtualnych na wirtualne dyski twarde funkcji Hyper-V za pomocą [Microsoft Virtual Machine Converter](https://technet.microsoft.com/library/dn874008(v=ws.11).aspx).
2. Przekazywanie plików wirtualnego dysku twardego do magazynu Azure za pomocą [polecenia cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Wdrażanie nowej maszyny wirtualnej przy użyciu przekazywanego wirtualnego dysku twardego.

> [!NOTE]
> Aby przeprowadzić migrację całej aplikacji, należy rozważyć użycie [usługi Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Wyślij dysk twardy
Użyj [usługi Import/Eksport Windows metody](../../../storage/common/storage-import-export-service.md) na przesyłanie dużych ilości danych pliku do magazynu obiektów Blob platformy Azure w sytuacjach, gdy przekazywania przez sieć jest zbyt duży i nie jest możliwe. Z tą usługą możesz wysłać co najmniej jeden dyski twarde zawierające dane do centrum danych Azure, w którym dane zostaną przekazane do konta magazynu.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](virtual-machines-windows-sql-server-iaas-overview.md).

> [!TIP]
> Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [— często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).

Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej Azure SQL Server z przechwyconego obrazu, zobacz [porady i sztuczki w klonowania maszyn wirtualnych Azure SQL z przechwyconych obrazów](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) na blogu inżynierów serwera SQL CSS.

