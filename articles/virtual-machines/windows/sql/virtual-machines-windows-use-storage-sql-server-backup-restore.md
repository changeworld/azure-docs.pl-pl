---
title: Jak używać usługi Azure storage dla kopii zapasowej serwera SQL i przywracania | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia kopii zapasowych programu SQL Server do magazynu Azure. Opis korzyści wykonywanie kopii zapasowych baz danych do magazynu Azure.
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
ms.openlocfilehash: 39d4f452143454a345bd91f550e44c93651ff933
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399053"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Użyj magazynu Azure dla programu SQL Server z kopii zapasowej i przywracania
## <a name="overview"></a>Przegląd
Począwszy od programu SQL Server 2012 z dodatkiem SP1 CU2, można teraz zapisać kopii zapasowych programu SQL Server bezpośrednio do usługi magazynu obiektów Blob platformy Azure. Ta funkcja służy do kopii zapasowych i przywracania usługi obiektów Blob platformy Azure z lokalną bazą danych serwera SQL lub bazy danych programu SQL Server w maszynie wirtualnej platformy Azure. Kopii zapasowej w chmurze zapewnia korzyści dostępności, nieograniczona replikacją geograficzną poza nim magazynu i łatwość migracji danych do i z chmury. Przy użyciu języka Transact-SQL lub SMO można wydać instrukcji BACKUP lub RESTORE.

SQL Server 2016 wprowadzono nowe funkcje; można użyć [kopii zapasowej migawki pliku](http://msdn.microsoft.com/library/mt169363.aspx) do wykonywania niemal natychmiastowe tworzenie kopii zapasowych i przywracania bardzo szybki.

W tym temacie objaśniono, dlaczego można użyć magazynu Azure do przechowywania kopii zapasowych SQL, a następnie w tym artykule opisano składniki zaangażowane. Dostęp do wskazówki i dodatkowe informacje, aby rozpocząć korzystanie z tej usługi z kopii zapasowych programu SQL Server, można użyć zasobów udostępnianych na końcu tego artykułu.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Korzyści wynikające z korzystania z usługi obiektów Blob platformy Azure dla kopii zapasowych serwera SQL
Istnieje kilka wyzwania, które czoła podczas wykonywania kopii zapasowych programu SQL Server. Te problemy obejmują zarządzanie magazynem, ryzyko wystąpienia awarii magazynu, dostęp do innej lokalizacji magazynu i konfiguracji sprzętu. Wiele z tych problemów są adresowane za pomocą usługi magazynu obiektów Blob platformy Azure do przechowywania kopii zapasowych programu SQL Server. Należy wziąć pod uwagę następujące korzyści:

* **Łatwość użycia**: przechowywanie kopii zapasowych w obiektach blob Azure może być wygodny sposób, elastyczne i łatwe na dostęp do opcji poza nim. Tworzenie innej lokalizacji magazynu kopii zapasowych programu SQL Server może wynosić maksymalnie modyfikowanie istniejących skryptów/zadań do użycia **BACKUP TO URL** składni. Poza miejscem przechowywania zwykle powinna być wystarczająco daleko od lokalizacji bazy danych produkcyjnej zapobiegające pojedynczego po awarii, które mogą wpływać na obu zdalną i produkcji lokalizacje bazy danych. Przez wybranie [obiektów blob platformy Azure replikacja geograficzna](../../../storage/common/storage-redundancy.md), masz dodatkową warstwę ochrony w przypadku awarii, które mogą wpłynąć na całego regionu.
* **Kopia zapasowa archiwum**: Usługa magazynu obiektów Blob Azure oferuje lepszą alternatywę do opcji taśmy często używane do archiwizacji kopii zapasowych. Magazynu taśm może wymagać fizycznego transportu do innej lokalizacji i środki w celu ochrony nośnika. Przechowywania kopii zapasowych w magazynie obiektów Blob Azure udostępnia moment, wysokiej dostępności i niezawodny archiwizacji opcji.
* **Zarządzane sprzętu**: się nie dodatkowe zarządzania sprzętem z usługami Azure. Usług Azure Zarządzanie sprzętem i podaj — replikacja geograficzna nadmiarowości i ochronę przed awariami sprzętu.
* **Nieograniczony magazyn**: przez włączenie kopii zapasowej bezpośrednio do obiektów blob platformy Azure, musisz mieć dostęp do niemal nieograniczonego magazynu. Alternatywnie wykonywanie kopii zapasowych na dysku maszyny wirtualnej platformy Azure ma ograniczenia na podstawie rozmiaru maszyny. Istnieje ograniczona liczba dysków, które można dołączyć do maszyny wirtualnej platformy Azure do przechowywania kopii zapasowych. Ten limit jest 16 dysków dla bardzo dużych wystąpienia i mniej wystąpień mniejsze.
* **Wykonaj kopię zapasową dostępności**: kopie zapasowe przechowywane w obiektach blob Azure są dostępne z dowolnego miejsca i w dowolnym momencie i łatwo dostępne dla przywracania do lokalnego programu SQL Server lub innego serwera SQL działającego w maszynę wirtualną platformy Azure, bez konieczności dołączania/odłączania bazy danych lub pobierania i dołączanie wirtualnego dysku twardego.
* **Koszt**: zapłać tylko za to usługa, która jest używana. Może być ekonomicznego jako opcja archiwum poza nim i tworzenia kopii zapasowej. Zobacz [Kalkulator cen platformy Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Kalkulator cen")i [cennik usługi Azure artykułu](http://go.microsoft.com/fwlink/?LinkId=277059 "artykułu cennik") Aby uzyskać więcej informacji.
* **Magazyn migawek**: Jeśli pliki bazy danych są przechowywane w obiekcie blob Azure i korzystasz z programu SQL Server 2016, możesz użyć [kopii zapasowej migawki pliku](http://msdn.microsoft.com/library/mt169363.aspx) do wykonywania niemal natychmiastowe tworzenie kopii zapasowych i przywracania bardzo szybki.

Aby uzyskać więcej informacji, zobacz [programu SQL Server z kopii zapasowej i przywracania usługi magazynu obiektów Blob Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

Następujące dwie sekcje wprowadzenie usługi magazynu obiektów Blob platformy Azure, w tym wymagane składniki programu SQL Server. Należy zrozumieć składników i ich interakcji pomyślnie użyj kopii zapasowej i przywracania usługi magazynu obiektów Blob platformy Azure.

## <a name="azure-blob-storage-service-components"></a>Azure Blob Storage Service Components
Następujące składniki platformy Azure są używane podczas wykonywania kopii zapasowej do usługi magazynu obiektów Blob platformy Azure.

| Składnik | Opis |
| --- | --- |
| **Konto magazynu** |Konto magazynu jest punkt startowy dla wszystkich usług magazynu. Aby uzyskać dostęp do usługi Magazyn obiektów Blob Azure, należy najpierw utworzyć konto usługi Azure Storage. Aby uzyskać więcej informacji na temat usługi Magazyn obiektów Blob platformy Azure, zobacz [jak używać usługi magazynu obiektów Blob Azure](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| Kontener |Kontener zawiera grupowanie zestawu obiektów blob i przechowywać nieograniczoną liczbę obiektów blob. Można zapisać programu SQL Server kopii zapasowych do usługi obiektów Blob platformy Azure, możesz musi mieć przynajmniej nadrzędny kontener utworzone. |
| **Blob** |Typ i rozmiar pliku. Obiekty BLOB mają następujący format adresu URL: **https://[storage account].blob.core.windows.net/[container]/[blob]**. Aby uzyskać więcej informacji na temat stronicowe obiekty BLOB, zobacz [opis blokowych i stronicowych obiektów blob](http://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Składniki programu SQL Server
Następujące składniki programu SQL Server są używane podczas wykonywania kopii zapasowej do usługi magazynu obiektów Blob platformy Azure.

| Składnik | Opis |
| --- | --- |
| **Adres URL** |Adres URL określa zasobów identyfikator URI (Uniform) do unikatowego pliku kopii zapasowej. Adres URL służy do zapewnienia lokalizację i nazwę pliku kopii zapasowej programu SQL Server. Adres URL musi wskazywać rzeczywistych obiektów blob nie tylko kontenerem. Jeśli obiekt blob nie istnieje, jest tworzony. Jeśli istniejący obiekt blob jest określony, kopii zapasowej nie powiedzie się, chyba że > określono opcję klauzuli WITH FORMAT. Poniżej przedstawiono przykład adresu URL, należy określić w poleceniu kopii zapasowej: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. HTTPS jest zalecane, ale nie jest wymagane. |
| **poświadczenia** |Informacje wymagane do połączenia i uwierzytelnienia usługi magazynu obiektów Blob platformy Azure są przechowywane jako poświadczenie.  Aby program SQL Server do zapisu obiektów Blob platformy Azure lub przywracania kopii zapasowych z niego można utworzyć poświadczeń programu SQL Server. Aby uzyskać więcej informacji, zobacz [poświadczenia serwera SQL](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> Jeśli zdecydujesz się na kopiowanie i przekazać plik kopii zapasowej do usługi magazynu obiektów Blob platformy Azure, musi używać typu obiektu blob strony możliwość przechowywania, jeśli planujesz używanie tego pliku dla operacji przywracania. PRZYWRÓCENIE z typem danych blob bloku zakończy się niepowodzeniem z powodu błędu.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
1. Utwórz konto platformy Azure, jeśli nie został wcześniej. Jeśli dokonujesz oceny usługi Azure, należy wziąć pod uwagę [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
2. Następnie wykonaj jedną z następujących samouczków, które opisano tworzenie konta magazynu i operacji przywracania.
   
   * **Program SQL Server 2014**: [samouczek: SQL Server 2014 w kopii zapasowej i przywracania na platformie Microsoft Azure Blob usługi magazynu](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [samouczek: Korzystanie z usługi magazynu obiektów Blob Microsoft Azure z bazami danych programu SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)
3. Przejrzyj, począwszy od dodatkowej dokumentacji [programu SQL Server z kopii zapasowej i przywracania usługi magazynu obiektów Blob platformy Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

Jeśli masz problemy, przejrzyj temat [kopii zapasowej serwera SQL do adresu URL najlepsze rozwiązania i rozwiązywanie problemów](https://msdn.microsoft.com/library/jj919149.aspx).

Dla innego serwera SQL z kopii zapasowej i przywracanie opcji, zobacz [kopii zapasowej i przywracania dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

