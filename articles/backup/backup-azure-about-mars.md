---
title: Informacje o agencie MARS
description: Dowiedz się, jak Agent MARS obsługuje scenariusze tworzenia kopii zapasowych
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: c036d93c09195c0c330cfe86f307d28866131d9f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897327"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Informacje o agencie Microsoft Azure Recovery Services (MARS)

W tym artykule opisano, jak usługa Azure Backup używa agenta Microsoft Azure Recovery Services (MARS) do tworzenia kopii zapasowych i przywracania plików/folderów, woluminu lub stanu systemu z komputera lokalnego na platformę Azure.

Agent MARS obsługuje następujące scenariusze tworzenia kopii zapasowych:

![Pulpit nawigacyjny magazynu usług Recovery Services](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Pliki i foldery**: selektywnie Chroń pliki i foldery systemu Windows.
- **Poziom głośności**: Chroń cały wolumin systemu Windows na komputerze.
- **Poziom systemu**: Chroń cały stan systemu Windows.

Agent MARS obsługuje następujące scenariusze przywracania:

![Pulpit nawigacyjny magazynu usług Recovery Services](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

-   **Ten sam serwer**: ten sam serwer, na którym utworzono kopię zapasową.
    -    **Pliki i foldery**: możesz przeglądać i wybierać poszczególne pliki i foldery, które chcesz przywrócić.
    -    **Poziom głośności**: można wybrać wolumin i punkt odzyskiwania, które mają zostać przywrócone, i przywrócić je do tej samej lokalizacji lub lokalizacji alternatywnej na tym samym komputerze.  Można utworzyć kopię istniejących plików, zastąpić istniejące pliki lub pominąć odzyskiwanie istniejących plików.
    -    **Poziom systemu**: można wybrać stan systemu i punkt odzyskiwania do przywrócenia na ten sam komputer w określonej lokalizacji.


-   **Serwer alternatywny**: inny serwer, czyli nie ten sam serwer, na którym wykonano kopię zapasową.
    -    **Pliki i foldery**: można przeglądać i wybierać poszczególne pliki i foldery, które mają zostać przywrócone na komputerze docelowym.
    -    **Poziom głośności**: można wybrać wolumin i punkt odzyskiwania, które mają zostać przywrócone do alternatywnej lokalizacji przez utworzenie kopii istniejących plików, zastąpienie istniejących plików lub pominięcie odzyskiwania istniejących plików.
    -    **Poziom systemu**: można wybrać stan systemu i punkt odzyskiwania do przywrócenia jako plik stanu systemu do alternatywnej maszyny.

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

1.  Z Azure Portal Utwórz [Magazyn usługi Recovery Service](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-recovery-services-vault) i wybierz pozycję pliki i foldery i/lub stan systemu z celów tworzenia kopii zapasowych.
2.  [Pobierz](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) poświadczenia magazynu usługi Recovery Service i instalatora agenta na maszynę lokalną. Aby chronić maszynę lokalną przez wybranie opcji Utwórz kopię zapasową, wybierz pozycję pliki i foldery i stan systemu i pobierz agenta MARS.
3.  Przygotuj infrastrukturę:

    a.    Uruchom Instalatora, aby [zainstalować](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) agenta.

    b.  Użyj pobranych poświadczeń magazynu, aby zarejestrować maszynę w magazynie Recovery Services.
4.  W konsoli agenta programu na kliencie Użyj [harmonogramu tworzenia kopii](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-backup-policy) zapasowej, aby skonfigurować kopię. Określ zasady przechowywania danych kopii zapasowej i Rozpocznij ochronę.

![Pulpit nawigacyjny magazynu usług Recovery Services](./media/backup-try-azure-backup-in-10-mins/backup-process.png)


### <a name="additional-scenarios"></a>Dodatkowe scenariusze
-   **Tworzenie kopii zapasowych plików i folderów na maszynie wirtualnej platformy Azure** — podstawowa metoda tworzenia kopii zapasowych maszyn wirtualnych platformy Azure polega na użyciu rozszerzenia Azure Backup na maszynie wirtualnej. Spowoduje to utworzenie kopii zapasowej całej maszyny wirtualnej. Jeśli chcesz utworzyć kopię zapasową określonych plików i folderów w ramach maszyny wirtualnej, możesz zainstalować agenta MARS na maszynach wirtualnych platformy Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

-   Umieszczanie **w trybie offline** — wstępne pełne kopie zapasowe danych na platformie Azure, zazwyczaj transfer dużych ilości danych i wymaga większej przepustowości sieci w porównaniu z kolejnymi kopiami zapasowymi, które przesyłają tylko różnice/przyrosty. Azure Backup kompresuje początkowe kopie zapasowe. W procesie umieszczania w trybie offline, Azure Backup mogą używać dysków do przekazywania skompresowanych danych początkowej kopii zapasowej w trybie offline do platformy Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).


## <a name="next-steps"></a>Następne kroki
[Macierz obsługi agenta MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Często zadawane pytania — Agent MARS](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
