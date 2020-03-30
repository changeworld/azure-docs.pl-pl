---
title: Jak korzystać z magazynu platformy Azure do tworzenia kopii zapasowych i przywracania programu SQL Server | Dokumenty firmy Microsoft
description: Dowiedz się, jak zrobić kopii zapasowej programu SQL Server w usłudze Azure Storage. W tym artykule wyjaśniono korzyści wynikające z tworzenia kopii zapasowych baz danych SQL w usłudze Azure Storage.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: cb19dc7262721e672bd3f54b32db9188dad7fee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101891"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Korzystanie z usługi Azure Storage dla programu SQL Server Backup and Restore
## <a name="overview"></a>Omówienie
Począwszy od SQL Server 2012 SP1 CU2, można teraz zapisywać kopie zapasowe programu SQL Server bezpośrednio do usługi magazynu obiektów Blob platformy Azure. Za pomocą tej funkcji można użyć do utworzenia kopii zapasowej i przywrócenia z usługi Azure Blob z lokalną bazą danych programu SQL Server lub bazą danych programu SQL Server na maszynie wirtualnej platformy Azure. Tworzenie kopii zapasowych w chmurze zapewnia korzyści z dostępności, nieograniczone replikowane geograficznie magazyn poza siedzibą firmy oraz łatwość migracji danych do i z chmury. Instrukcje BACKUP lub RESTORE można wydać przy użyciu funkcji Transact-SQL lub SMO.

SQL Server 2016 wprowadza nowe możliwości; można użyć [kopii zapasowej migawki pliku](https://msdn.microsoft.com/library/mt169363.aspx) do wykonywania niemal natychmiastowych kopii zapasowych i niezwykle szybkich przywracania.

W tym temacie wyjaśniono, dlaczego można użyć magazynu platformy Azure dla kopii zapasowych SQL, a następnie opisano składniki. Zasobów podanych na końcu artykułu można użyć, aby uzyskać dostęp do instruktaży i dodatkowych informacji, aby rozpocząć korzystanie z tej usługi z kopiami zapasowymi programu SQL Server.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Korzyści z korzystania z usługi Azure Blob dla kopii zapasowych programu SQL Server
Istnieje kilka wyzwań, które można napotkać podczas tworzenia kopii zapasowej programu SQL Server. Wyzwania te obejmują zarządzanie pamięcią masową, ryzyko awarii magazynu, dostęp do magazynu poza siedzibą firmy i konfigurację sprzętu. Wiele z tych problemów są rozwiązywane przy użyciu usługi magazynu obiektów Blob platformy Azure dla kopii zapasowych programu SQL Server. Należy wziąć pod uwagę następujące korzyści:

* **Łatwość użycia:** przechowywanie kopii zapasowych w obiektach blob platformy Azure może być wygodną, elastyczną i łatwo dostępną opcją poza siedzibą firmy. Tworzenie magazynu poza siedzibą firmy dla kopii zapasowych programu SQL Server może być tak proste, jak modyfikowanie istniejących skryptów/zadań w celu użycia składni **kopii zapasowej do adresu URL.** Magazyn poza siedzibą firmy powinien zazwyczaj znajdować się wystarczająco daleko od lokalizacji produkcyjnej bazy danych, aby zapobiec pojedynczej awarii, która może mieć wpływ zarówno na lokalizacje bazy danych poza siedzibą firmy, jak i na produkcyjną bazę danych. Wybierając [replikację geograficzną obiektów blob platformy Azure,](../../../storage/common/storage-redundancy.md)masz dodatkową warstwę ochrony w przypadku awarii, która może mieć wpływ na cały region.
* **Archiwum kopii zapasowych:** Usługa Usługi Azure Blob Storage oferuje lepszą alternatywę dla często używanej opcji taśmy do archiwizacji kopii zapasowych. Przechowywanie taśm może wymagać fizycznego transportu do obiektu poza siedzibą firmy i środków ochrony nośników. Przechowywanie kopii zapasowych w usłudze Azure Blob Storage zapewnia natychmiastową, wysoką dostępną i trwałą opcję archiwizacji.
* **Sprzęt zarządzany:** Nie ma żadnych narzutu zarządzania sprzętem za pomocą usług platformy Azure. Usługi platformy Azure zarządzają sprzętem i zapewniają replikację geograficzną w celu zapewnienia nadmiarowości i ochrony przed awariami sprzętu.
* **Nieograniczona ilość miejsca do magazynowania:** dzięki umożliwieniu bezpośredniej kopii zapasowej obiektów blob platformy Azure masz dostęp do praktycznie nieograniczonej ilości miejsca. Alternatywnie tworzenie kopii zapasowej na dysku maszyny wirtualnej platformy Azure ma limity na podstawie rozmiaru maszyny. Istnieje limit liczby dysków, które można dołączyć do maszyny wirtualnej platformy Azure do tworzenia kopii zapasowych. Ten limit wynosi 16 dysków dla bardzo dużego wystąpienia i mniej dla mniejszych wystąpień.
* **Dostępność kopii zapasowych:** Kopie zapasowe przechowywane w obiektach blob platformy Azure są dostępne z dowolnego miejsca i w dowolnym czasie i można łatwo uzyskać dostęp do przywracania do lokalnego programu SQL Server lub innego programu SQL Server uruchomionego na maszynie wirtualnej platformy Azure, bez konieczności dołączania/odłączania bazy danych lub pobierania i dołączania dysku wirtualnego.
* **Koszt:** Płać tylko za usługę, która jest używana. Może być opłacalna jako opcja archiwum poza siedzibą firmy i kopii zapasowej. Aby uzyskać więcej informacji, zobacz [kalkulator cen platformy Azure](https://go.microsoft.com/fwlink/?LinkId=277060 "Kalkulator cen")i artykuł o cenach platformy [Azure.](https://go.microsoft.com/fwlink/?LinkId=277059 "Artykuł cenowy")
* **Migawki magazynu:** Gdy pliki bazy danych są przechowywane w obiekcie blob platformy Azure i używasz programu SQL Server 2016, można użyć [kopii zapasowej migawki pliku](https://msdn.microsoft.com/library/mt169363.aspx) do wykonywania niemal natychmiastowych kopii zapasowych i niezwykle szybkich przywracania.

Aby uzyskać więcej informacji, zobacz [SQL Server Backup and Restore with Azure Blob Storage Service](https://go.microsoft.com/fwlink/?LinkId=271617).

W poniższych dwóch sekcjach przedstawiono usługę magazynu obiektów Blob platformy Azure, w tym wymagane składniki programu SQL Server. Ważne jest, aby zrozumieć składniki i ich interakcji, aby pomyślnie użyć kopii zapasowej i przywracania z usługi Azure Blob storage.

## <a name="azure-blob-storage-service-components"></a>Składniki usługi usługi Azure Blob Storage
Następujące składniki platformy Azure są używane podczas tworzenia kopii zapasowej w usłudze Azure Blob storage.

| Składnik | Opis |
| --- | --- |
| **Konto magazynu** |Konto magazynu jest punktem wyjścia dla wszystkich usług magazynu. Aby uzyskać dostęp do usługi Azure Blob Storage, należy najpierw utworzyć konto usługi Azure Storage. Aby uzyskać więcej informacji na temat usługi Azure Blob storage, zobacz [Jak korzystać z usługi Azure Blob Storage](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Kontener** |Kontener udostępnia grupowanie zestawu obiektów blob i może przechowywać nieograniczoną liczbę obiektów blob. Aby napisać kopię zapasową programu SQL Server w usłudze Azure Blob, musisz utworzyć co najmniej kontener główny. |
| **Obiekt blob** |Plik dowolnego typu i rozmiaru. Obiekty BLOB można adresować przy użyciu następującego formatu adresu URL: **https://[konto magazynu].blob.core.windows.net/[container]/[blob]**. Aby uzyskać więcej informacji na temat obiektów blob strony, zobacz [Opis bloków i obiektów blob strony](https://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Składniki programu SQL Server
Następujące składniki programu SQL Server są używane podczas tworzenia kopii zapasowej w usłudze Azure Blob storage.

| Składnik | Opis |
| --- | --- |
| **Adres URL** |Adres URL określa jednolity identyfikator zasobu (URI) do unikatowego pliku kopii zapasowej. Adres URL służy do podawania lokalizacji i nazwy pliku kopii zapasowej programu SQL Server. Adres URL musi wskazywać rzeczywisty obiekt blob, a nie tylko kontener. Jeśli obiekt blob nie istnieje, jest tworzony. Jeśli określono istniejący obiekt blob, kopia zapasowa kończy się niepowodzeniem, chyba że określono opcję > WITH FORMAT. Poniżej przedstawiono przykład adresu URL określonego w poleceniu KOPIA ZAPASOWA: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. Protokół HTTPS jest zalecany, ale nie jest wymagany. |
| **Poświadczenie** |Informacje, które są wymagane do łączenia się i uwierzytelniania usługi magazynu obiektów Blob platformy Azure jest przechowywany jako poświadczenie.  Aby program SQL Server zapisywał kopie zapasowe w obiekcie blob platformy Azure lub przywracał z niego, należy utworzyć poświadczenie programu SQL Server. Aby uzyskać więcej informacji, zobacz [Sql Server Credential](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> Program Sql Server 2016 został zaktualizowany do obsługi bloków obiektów blob. Zobacz [samouczek: Korzystanie z usługi magazynu obiektów Blob platformy Microsoft Azure z bazami danych programu SQL Server 2016,](https://msdn.microsoft.com/library/dn466438.aspx) aby uzyskać więcej informacji.
> 
> 

## <a name="next-steps"></a>Następne kroki
1. Utwórz konto platformy Azure, jeśli jeszcze go nie masz. Jeśli oceniasz platformę Azure, rozważ [bezpłatną wersję próbną.](https://azure.microsoft.com/free/)
2. Następnie przejdź przez jeden z następujących samouczków, które prześrzają Cię przez tworzenie konta magazynu i wykonywanie przywracania.
   
   * **SQL Server 2014**: [Samouczek: SQL Server 2014 Kopia zapasowa i przywracanie do usługi magazynu obiektów Blob platformy Microsoft Azure](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [Samouczek: Korzystanie z usługi magazynu obiektów Blob platformy Microsoft Azure z bazami danych programu SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)
3. Przejrzyj dodatkową dokumentację, zaczynając od [programu SQL Server Backup and Restore za pomocą usługi Microsoft Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148.aspx).

Jeśli masz jakiekolwiek problemy, zapoznaj się z tematem [SQL Server Backup to URL Best Practices and Troubleshooting](https://msdn.microsoft.com/library/jj919149.aspx).

Aby uzyskać inne opcje tworzenia kopii zapasowych i przywracania programu SQL Server, zobacz [Tworzenie kopii zapasowych i przywracanie dla programu SQL Server w maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-backup-recovery.md).

