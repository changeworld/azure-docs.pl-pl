---
title: Jak używać usługi Azure storage do tworzenia kopii zapasowych programu SQL Server i Przywróć | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć kopię zapasową programu SQL Server do usługi Azure Storage. W tym artykule wyjaśniono zalety tworzenia kopii zapasowych baz danych SQL do usługi Azure Storage.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: 1b6660a1565b3c119cc1dec0823870c7dd5bd24f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477148"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Użyj usługi Azure Storage dla programu SQL Server z kopii zapasowej i przywracania
## <a name="overview"></a>Omówienie
Począwszy od pakietu CU2 programu SQL Server 2012 z dodatkiem SP1, można teraz zapisywania kopii zapasowych programu SQL Server bezpośrednio do usługi Azure Blob storage. Ta funkcja służy do kopii zapasowych i przywracania usługi obiektów Blob platformy Azure przy użyciu lokalnej bazy danych programu SQL Server lub bazy danych programu SQL Server na maszynie wirtualnej platformy Azure. Kopia zapasowa w chmurze oferuje zalety dostępność, nieograniczone możliwości magazynowania poza siedzibą firmy replikowana geograficznie i łatwość migracji danych do i z chmury. Za pomocą języka Transact-SQL lub SMO można wydać instrukcji tworzenia kopii zapasowej lub przywracania.

SQL Server 2016 wprowadzono nowe funkcje; Możesz użyć [kopii zapasowych migawki plików](https://msdn.microsoft.com/library/mt169363.aspx) do wykonywania niemal natychmiastowych kopii zapasowych i niezwykle szybkie przywracanie danych.

W tym temacie opisano, dlaczego warto używać usługi Azure storage do przechowywania kopii zapasowych programu SQL, a następnie w tym artykule opisano składniki zaangażowane. Dostęp do przewodników i dodatkowe informacje, aby rozpocząć korzystanie z tej usługi kopii zapasowych programu SQL Server, można użyć zasobów udostępnianych na końcu tego artykułu.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Korzyści z używania usługi obiektów Blob platformy Azure do przechowywania kopii zapasowych programu SQL Server
Istnieje kilka kwestii, które są spowodowane podczas wykonywania kopii zapasowych programu SQL Server. Te problemy obejmują zarządzanie magazynem, ryzyko awarii magazynu, dostępu do magazynu poza siedzibą firmy i konfiguracji sprzętu. Wiele z tych wyzwań są adresowane za pomocą usługi magazynu obiektów Blob platformy Azure do przechowywania kopii zapasowych programu SQL Server. Należy wziąć pod uwagę następujące korzyści:

* **Łatwość użycia**: Przechowywanie kopii zapasowych w obiektach blob platformy Azure może być wygodny sposób, elastyczne i łatwe do dostępu do opcji poza siedzibą firmy. Tworzenie magazynu poza siedzibą firmy, dla kopii zapasowych programu SQL Server może być bardzo proste — wystarczy modyfikowania istniejących skryptów/zadań do użycia **BACKUP TO URL** składni. Zazwyczaj należy magazynowania poza siedzibą firmy daleko z lokalizacji bazy danych produkcyjnych w celu zapobiegania awarii jednej, mogą mieć wpływ na zarówno poza siedzibą firmy i produkcyjne bazy danych w lokalizacji. Wybierając polecenie [w obiektach blob platformy Azure replikacja geograficzna](../../../storage/common/storage-redundancy.md), masz dodatkową warstwę ochrony awarii, które mogą mieć wpływ na cały region.
* **Archiwum kopii zapasowej**: Usługa Azure Blob Storage oferuje lepszą alternatywą do opcji taśmy często używane do archiwizacji kopii zapasowych. Magazynu taśm może wymagać fizycznych transportu mechanizm poza siedzibą firmy i środki w celu ochrony nośnika. Przechowywanie kopii zapasowych w usłudze Azure Blob Storage zapewnia natychmiastowe o wysokiej dostępności i niezawodny archiwizacji opcji.
* **Zarządzane sprzętu**: Nie ma żadnych koszty zarządzania sprzętem z usługami platformy Azure. Usługi platformy Azure Zarządzanie sprzętem i zapewnia replikację geograficzną nadmiarowość i ochronę przed awariami sprzętu.
* **Nieograniczony magazyn**: Włączając bezpośrednie tworzenie kopii zapasowych do obiektów blob platformy Azure, masz dostęp do praktycznie nieograniczone miejsce magazynowania. Alternatywnie kopie zapasowe na dysku maszyny wirtualnej platformy Azure ma zależności od rozmiaru maszyny. Ma limitu liczby dysków, które można dołączyć do maszyny wirtualnej platformy Azure do przechowywania kopii zapasowych. Ten limit jest 16 dysków dla bardzo duże wystąpienie i mniej wystąpień mniejsze.
* **Wykonaj kopię zapasową dostępności**: Kopie zapasowe przechowywane w obiektach blob platformy Azure są dostępne z dowolnego miejsca i w dowolnym momencie i łatwo dostępne dla przywracania programu SQL Server w środowisku lokalnym lub inny program SQL Server w maszynie wirtualnej platformy Azure, bez konieczności dołączania/odłączania bazy danych lub pobranie i dołączanie wirtualnego dysku twardego.
* **Koszt**: Płacisz tylko za usługi, która jest używana. Może być ekonomiczne jako opcja archiwum poza siedzibą firmy i wykonywania kopii zapasowych. Zobacz [kalkulatora cen platformy Azure](https://go.microsoft.com/fwlink/?LinkId=277060 "Kalkulator cen")i [artykuł cennik usługi Azure](https://go.microsoft.com/fwlink/?LinkId=277059 "artykuł cennik") Aby uzyskać więcej informacji.
* **Migawek magazynu**: Jeśli używasz programu SQL Server 2016 pliki bazy danych są przechowywane w usłudze Azure blob, możesz użyć [kopii zapasowych migawki plików](https://msdn.microsoft.com/library/mt169363.aspx) do wykonywania niemal natychmiastowych kopii zapasowych i niezwykle szybkie przywracanie danych.

Aby uzyskać więcej informacji, zobacz [kopii zapasowej programu SQL Server i przywracania w usłudze Azure Blob Storage](https://go.microsoft.com/fwlink/?LinkId=271617).

Następujące dwie sekcje wprowadzenie usługi Azure Blob storage, w tym wymagane składniki programu SQL Server. Jest ważne poznać składniki i ich interakcję, aby pomyślnie użyć kopii zapasowej i przywracanie z usługą Azure Blob storage.

## <a name="azure-blob-storage-service-components"></a>Składniki usługi magazynu obiektów Blob platformy Azure
Następujące składniki platformy Azure są używane podczas wykonywania kopii zapasowej do usługi Azure Blob storage.

| Składnik | Opis |
| --- | --- |
| **Konto magazynu** |Konto magazynu jest punktem wyjścia dla wszystkich usług magazynu. Aby uzyskać dostęp do usługi Azure Blob Storage, należy najpierw utworzyć konto usługi Azure Storage. Aby uzyskać więcej informacji na temat usługi Azure Blob storage, zobacz [jak używać usługi Azure Blob Storage](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Kontener** |Kontener zawiera grupowanie zestawu obiektów blob i może przechowywać nieograniczoną liczbę obiektów blob. Do zapisu w programu SQL Server kopii zapasowej do usługi obiektów Blob platformy Azure, możesz musi mieć co najmniej kontener głównego tworzone. |
| **Obiekt blob** |Plik dowolnego typu i rozmiaru. Obiekty BLOB są adresy, przy użyciu następującego formatu adresu URL: **https://[storage account].blob.core.windows.net/[container]/[blob]**. Aby uzyskać więcej informacji na temat stronicowych obiektów blob, zobacz [omówienie blokowych i stronicowych obiektów blob](https://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Składniki programu SQL Server
Następujące składniki programu SQL Server są używane podczas wykonywania kopii zapasowej do usługi Azure Blob storage.

| Składnik | Opis |
| --- | --- |
| **Adres URL** |Adres URL określa jednolity identyfikator (URI) do unikatowego pliku kopii zapasowej. Adres URL jest używany do zapewnienia lokalizację i nazwę pliku kopii zapasowej programu SQL Server. Adres URL musi wskazywać na rzeczywistych obiektów blob nie tylko kontenera. Jeśli obiekt blob nie istnieje, zostanie utworzony. Jeśli istniejący obiekt blob jest określona, kopii zapasowej zakończy się niepowodzeniem, chyba że > określono opcji klauzuli WITH FORMAT. Oto przykład adres URL należy określić w poleceniu BACKUP: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. HTTPS jest zalecane, ale nie jest wymagane. |
| **Poświadczenie** |Informacje, który jest wymagany do nawiązywania połączenia i uwierzytelniania usługi Azure Blob storage są przechowywane jako poświadczenie.  Aby dla programu SQL Server do zapisywania obiektów Blob platformy Azure lub przywracania kopii zapasowych z niego można utworzyć poświadczeń programu SQL Server. Aby uzyskać więcej informacji, zobacz [poświadczeń serwera SQL](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> SQL Server 2016 został zaktualizowany do obsługi blokowych obiektów blob. Zobacz [samouczka: Za pomocą usługi Microsoft Azure Blob storage z bazami danych programu SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx) Aby uzyskać więcej informacji.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
1. Utwórz konto platformy Azure, jeśli nie masz jeszcze jeden. Jeśli dokonujesz oceny usługi Azure, należy wziąć pod uwagę [bezpłatna wersja próbna](https://azure.microsoft.com/free/).
2. Następnie wykonaj jedną z następujących samouczków, które przeprowadzą Cię przez tworzenie konta magazynu i operacji przywracania.
   
   * **SQL Server 2014**: [Samouczek: Kopia zapasowa programu SQL Server 2014 i przywracania na platformie Microsoft Azure Blob usługi Storage](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [Samouczek: Za pomocą usługi Microsoft Azure Blob storage z bazami danych programu SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)
3. Przejrzyj dokumentację, zaczynając od [kopii zapasowej programu SQL Server i przywracania w usłudze Microsoft Azure Blob Storage](https://msdn.microsoft.com/library/jj919148.aspx).

Jeśli masz jakiekolwiek problemy, przejrzyj temat [kopię zapasową serwera SQL do adresu URL najlepszych rozwiązań i rozwiązywanie problemów](https://msdn.microsoft.com/library/jj919149.aspx).

Dla innych programu SQL Server należy utworzyć kopię zapasową i opcje przywracania, zobacz [kopia zapasowa i przywracanie programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

