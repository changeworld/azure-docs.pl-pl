---
title: Migrowanie bazy danych programu SQL Server do programu SQL Server na maszynie Wirtualnej | Dokumenty firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację lokalnej bazy danych użytkownika do programu SQL Server na maszynie wirtualnej platformy Azure.
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
ms.topic: article
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c8314b04c05e2ecba2715b807171b5c1a2fa988a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646867"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrowanie bazy danych programu SQL Server do programu SQL Server na maszynie wirtualnej platformy Azure

Istnieje wiele metod migracji lokalnej bazy danych użytkowników programu SQL Server do programu SQL Server na maszynie Wirtualnej platformy Azure. W tym artykule pokrótce omówimy różne metody i zaleci najlepszą metodę dla różnych scenariuszy.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > Sql Server 2008 i SQL Server 2008 R2 zbliżają się do [końca ich cyklu pomocy technicznej](https://www.microsoft.com/sql-server/sql-server-2008) dla wystąpień lokalnych. Aby rozszerzyć pomoc techniczną, można przeprowadzić migrację wystąpienia programu SQL Server do maszyny Wirtualnej platformy Azure lub kupić rozszerzone aktualizacje zabezpieczeń, aby zachować je lokalnie. Aby uzyskać więcej informacji, zobacz [Rozszerzanie pomocy technicznej dla programów SQL Server 2008 i 2008 R2 za pomocą platformy Azure](virtual-machines-windows-sql-server-2008-eos-extend-support.md)

## <a name="what-are-the-primary-migration-methods"></a>Jakie są podstawowe metody migracji?
Podstawowe metody migracji to:

* Wykonywanie lokalnej kopii zapasowej przy użyciu kompresji i ręczne kopiowanie pliku kopii zapasowej na maszynę wirtualną platformy Azure
* Wykonywanie kopii zapasowej do adresu URL i przywracanie do maszyny wirtualnej platformy Azure z adresu URL
* Odłączaj, a następnie skopiuj pliki danych i dzienników do magazynu obiektów blob platformy Azure, a następnie dołącz do programu SQL Server na maszynie Wirtualnej platformy Azure z adresu URL
* Konwertuj lokalny komputer fizyczny na dysk VHD funkcji Hyper-V, przekaż do magazynu obiektów blob platformy Azure, a następnie wdrażaj jako nową maszynę wirtualną przy użyciu przekazanej dyski VHD
* Wysyłaj dysk twardy przy użyciu usługi importu/eksportowania systemu Windows
* Jeśli masz wdrożenie grupy dostępności AlwaysOn lokalnie, użyj [Kreatora dodawania repliki platformy Azure,](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) aby utworzyć replikę na platformie Azure, a następnie w pracy awaryjnej, wskazując użytkownikom wystąpienie bazy danych platformy Azure
* Użyj [replikacji transakcyjnej](https://msdn.microsoft.com/library/ms151176.aspx) programu SQL Server, aby skonfigurować wystąpienie programu Azure SQL Server jako subskrybenta, a następnie wyłączyć replikację, wskazując użytkownikom wystąpienie bazy danych platformy Azure

> [!TIP]
> Można również użyć tych samych technik do przenoszenia baz danych między maszynami wirtualnymi programu SQL Server na platformie Azure. Na przykład nie istnieje obsługiwany sposób uaktualnienia maszyny Wirtualnej obrazu galerii programu SQL Server z jednej wersji/wersji do innej. W takim przypadku należy utworzyć nową maszynę wirtualną programu SQL Server z nową wersją/wersją, a następnie użyć jednej z technik migracji w tym artykule, aby przenieść bazy danych. 

## <a name="choosing-your-migration-method"></a>Wybór metody migracji
Aby uzyskać optymalną wydajność transferu danych, migruj pliki bazy danych do maszyny Wirtualnej platformy Azure przy użyciu skompresowanego pliku kopii zapasowej.

Aby zminimalizować przestoje podczas procesu migracji bazy danych, należy użyć opcji Zawszeon lub replikacji transakcyjnej.

Jeśli nie jest możliwe użycie powyższych metod, ręcznie migruj bazę danych. Za pomocą tej metody zazwyczaj rozpocznie się od kopii zapasowej bazy danych, a następnie kopię kopii zapasowej bazy danych na platformie Azure, a następnie wykonać przywracanie bazy danych. Można również skopiować pliki bazy danych samodzielnie na platformę Azure, a następnie dołączyć je. Istnieje kilka metod, za pomocą których można wykonać ten ręczny proces migracji bazy danych do maszyny Wirtualnej platformy Azure.

> [!NOTE]
> Podczas uaktualniania do programu SQL Server 2014 lub SQL Server 2016 ze starszych wersji programu SQL Server należy rozważyć, czy potrzebne są zmiany. Zaleca się adres wszystkie zależności od funkcji nie obsługiwanych przez nową wersję programu SQL Server w ramach projektu migracji. Aby uzyskać więcej informacji na temat obsługiwanych wersji i scenariuszy, zobacz [Uaktualnianie do programu SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

W poniższej tabeli wymieniono każdą z metod migracji podstawowej i omówiono, kiedy użycie każdej metody jest najbardziej odpowiednie.

| Metoda | Wersja źródłowej bazy danych | Docelowa wersja bazy danych | Ograniczenie rozmiaru kopii zapasowej bazy danych źródłowych | Uwagi |
| --- | --- | --- | --- | --- |
| [Wykonywanie lokalnej kopii zapasowej przy użyciu kompresji i ręczne kopiowanie pliku kopii zapasowej na maszynę wirtualną platformy Azure](#backup-and-restore) |Program SQL Server 2005 lub większy |Program SQL Server 2005 lub większy |[Limit magazynu maszyn wirtualnych platformy Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Jest to bardzo prosta i dobrze przetestowana technika przenoszenia baz danych na różnych komputerach. |
| [Wykonywanie kopii zapasowej do adresu URL i przywracanie do maszyny wirtualnej platformy Azure z adresu URL](#backup-to-url-and-restore) |SQL Server 2012 z dodatkiem SP1 CU2 lub większy |SQL Server 2012 z dodatkiem SP1 CU2 lub większy |< 12,8 TB dla programu SQL Server 2016, w przeciwnym razie < 1 TB | Ta metoda jest tylko inny sposób, aby przenieść plik kopii zapasowej do maszyny Wirtualnej przy użyciu magazynu platformy Azure. |
| [Odłącz, a następnie skopiuj pliki danych i dzienników do magazynu obiektów blob platformy Azure, a następnie dołącz do programu SQL Server na maszynie wirtualnej platformy Azure z adresu URL](#detach-and-attach-from-url) |Program SQL Server 2005 lub większy |Program SQL Server 2014 lub większy |[Limit magazynu maszyn wirtualnych platformy Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Tej metody należy używać podczas [przechowywania tych plików przy użyciu usługi azure blob storage](https://msdn.microsoft.com/library/dn385720.aspx) i dołączania ich do programu SQL Server uruchomionego na maszynie Wirtualnej platformy Azure, szczególnie w przypadku bardzo dużych baz danych. |
| [Konwertuj komputer lokalny na dyski VHD funkcji Hyper-V, przekazuje do magazynu obiektów blob platformy Azure, a następnie wdraża nową maszynę wirtualną przy użyciu przekazanego dysku VHD](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |Program SQL Server 2005 lub większy |Program SQL Server 2005 lub większy |[Limit magazynu maszyn wirtualnych platformy Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Użyj podczas [przynoszenia własnej licencji programu SQL Server](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md), podczas migracji bazy danych, która będzie uruchamiana w starszej wersji programu SQL Server lub podczas migracji baz danych systemu i użytkowników w ramach migracji bazy danych zależnej od innych baz danych użytkowników i/lub systemowych baz danych. |
| [Wysyłaj dysk twardy przy użyciu usługi importu/eksportowania systemu Windows](#ship-hard-drive) |Program SQL Server 2005 lub większy |Program SQL Server 2005 lub większy |[Limit magazynu maszyn wirtualnych platformy Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Użyj [usługi importu/eksportu systemu Windows,](../../../storage/common/storage-import-export-service.md) gdy metoda kopiowania ręcznego jest zbyt wolna, na przykład w przypadku bardzo dużych baz danych |
| [Korzystanie z Kreatora dodawania replik platformy Azure](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |Program SQL Server 2012 lub większy |Program SQL Server 2012 lub większy |[Limit magazynu maszyn wirtualnych platformy Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Minimalizuje przestoje, używane, gdy masz wdrożenie lokalne zawsze włączone |
| [Korzystanie z replikacji transakcyjnej programu SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) |Program SQL Server 2005 lub większy |Program SQL Server 2005 lub większy |[Limit magazynu maszyn wirtualnych platformy Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Używaj, gdy trzeba zminimalizować przestoje i nie mieć wdrożenia lokalnego zawsze włączone |

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie
Utwórz kopię zapasową bazy danych za pomocą kompresji, skopiuj kopię zapasową do maszyny Wirtualnej, a następnie przywróć bazę danych. Jeśli plik kopii zapasowej jest większy niż 1 TB, należy go rozebrać, ponieważ maksymalny rozmiar dysku maszyny Wirtualnej wynosi 1 TB. Aby przeprowadzić migrację bazy danych użytkowników przy użyciu tej metody ręcznej, należy wykonać następujące ogólne kroki:

1. Wykonaj pełną kopię zapasową bazy danych w lokalizacji lokalnej.
2. Utwórz lub przekaż maszynę wirtualną z żądaną wersją programu SQL Server.
3. Skonfiguruj łączność na podstawie wymagań. Zobacz [Łączenie się z maszyną wirtualną programu SQL Server na platformie Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
4. Skopiuj pliki kopii zapasowej na maszynę wirtualną za pomocą pulpitu zdalnego, Eksploratora Windows lub polecenia kopiowania z wiersza polecenia.

## <a name="backup-to-url-and-restore"></a>Tworzenie kopii zapasowej do adresu URL i przywracanie
Zamiast tworzenia kopii zapasowej do pliku lokalnego, można użyć [kopii zapasowej do adresu URL,](https://msdn.microsoft.com/library/dn435916.aspx) a następnie przywrócić z adresu URL do maszyny Wirtualnej. W programie SQL Server 2016 obsługiwane są rozłożone zestawy kopii zapasowych, są zalecane pod względem wydajności i muszą przekraczać limity rozmiaru na obiekt blob. W przypadku bardzo dużych baz danych zaleca się korzystanie z [usługi importu/eksportu systemu Windows.](../../../storage/common/storage-import-export-service.md)

## <a name="detach-and-attach-from-url"></a>Odłącz i dołącz z adresu URL
Odłącz pliki bazy danych i dziennika i przenieś je do [magazynu obiektów Blob platformy Azure](https://msdn.microsoft.com/library/dn385720.aspx). Następnie dołącz bazę danych z adresu URL na maszynie Wirtualnej platformy Azure. Użyj tego, jeśli chcesz, aby pliki fizycznej bazy danych znajdują się w magazynie obiektów Blob. Może to być przydatne w przypadku bardzo dużych baz danych. Aby przeprowadzić migrację bazy danych użytkowników przy użyciu tej metody ręcznej, należy wykonać następujące ogólne kroki:

1. Odłącz pliki bazy danych z wystąpienia lokalnej bazy danych.
2. Skopiuj odłączone pliki bazy danych do magazynu obiektów blob platformy Azure za pomocą [narzędzia wiersza polecenia AZCopy](../../../storage/common/storage-use-azcopy.md).
3. Dołącz pliki bazy danych z adresu URL platformy Azure do wystąpienia programu SQL Server na maszynie Wirtualnej platformy Azure.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Przeprowadź konwersję na maszynę wirtualną, przekaż ją do adresu URL i wdróż ją jako nową maszynę wirtualną
Ta metoda służy do migracji wszystkich baz danych systemu i użytkowników w lokalnym wystąpieniu programu SQL Server do maszyny wirtualnej platformy Azure. Aby przeprowadzić migrację całego wystąpienia programu SQL Server, należy wykonać następujące ogólne kroki, korzystając z tej metody ręcznej:

1. Konwertuj maszyny fizyczne lub wirtualne na dyski VHD funkcji Hyper-V.
2. Przekazywanie plików VHD do usługi Azure Storage przy użyciu [polecenia cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Wdrażanie nowej maszyny wirtualnej przy użyciu przekazanego dysku VHD.

> [!NOTE]
> Aby przeprowadzić migrację całej aplikacji, należy rozważyć użycie [usługi Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Wyślij dysk twardy
Użyj [metody Usługi importowania/eksportu systemu Windows,](../../../storage/common/storage-import-export-service.md) aby przenieść duże ilości danych plików do magazynu obiektów blob platformy Azure w sytuacjach, gdy przekazywanie przez sieć jest zbyt kosztowne lub niewykonalne. Dzięki tej usłudze wysyłasz co najmniej jeden dysk twardy zawierający te dane do centrum danych platformy Azure, gdzie dane zostaną przekazane na twoje konto magazynu.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [omówienie programu SQL Server na maszynach wirtualnych platformy Azure.](virtual-machines-windows-sql-server-iaas-overview.md)

> [!TIP]
> Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).

Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej programu Azure SQL Server na podstawie przechwyconego obrazu, zobacz [Porady & wskazówki dotyczące "klonowania" maszyn wirtualnych SQL platformy Azure z przechwyconych obrazów](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) w blogu css SQL Server Engineers.

