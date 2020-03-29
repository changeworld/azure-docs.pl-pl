---
title: Informacje o agencie MARS
description: Dowiedz się, jak agent MARS obsługuje scenariusze tworzenia kopii zapasowych
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2cc8e32152f6930c9c250e2811668cc2c924616
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673291"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Agent usług odzyskiwania platformy Microsoft Azure (MARS) – informacje

W tym artykule opisano, jak usługa Azure Backup używa agenta usług odzyskiwania platformy Microsoft Azure (MARS) do tworzenia kopii zapasowych i przywracania plików, folderów oraz woluminu lub stanu systemu z komputera lokalnego na platformie Azure.

Agent MARS obsługuje następujące scenariusze tworzenia kopii zapasowych:

![Scenariusze tworzenia kopii zapasowych mars](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Pliki i foldery**: Selektywne chronić pliki i foldery systemu Windows.
- **Poziom głośności:** Chroń całą objętość komputera w systemie Windows.
- **Poziom systemu**: Ochrona całego stanu systemu Windows.

Agent MARS obsługuje następujące scenariusze przywracania:

![Scenariusze odzyskiwania MARS](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Ten sam serwer:** serwer, na którym utworzono kopię zapasową.
  - **Pliki i foldery**: Wybierz poszczególne pliki i foldery, które chcesz przywrócić.
  - **Poziom głośności:** Wybierz punkt głośności i odzyskiwania, który chcesz przywrócić, a następnie przywróć go do tej samej lokalizacji lub alternatywnej lokalizacji na tym samym komputerze.  Utwórz kopię istniejących plików, zastąp istniejące pliki lub pomiń odzyskiwanie istniejących plików.
  - **Poziom systemu:** Wybierz stan systemu i punkt odzyskiwania, aby przywrócić go do tego samego komputera w określonej lokalizacji.

- **Serwer alternatywny:** Serwer inny niż serwer, na którym została pobrana kopia zapasowa.
  - **Pliki i foldery**: Wybierz poszczególne pliki i foldery, których punkt odzyskiwania chcesz przywrócić na komputerze docelowym.
  - **Poziom woluminu:** Wybierz punkt głośności i odzyskiwania, który chcesz przywrócić w innej lokalizacji. Utwórz kopię istniejących plików, zastąp istniejące pliki lub pomiń odzyskiwanie istniejących plików.
  - **Poziom systemu:** Wybierz stan systemu i punkt odzyskiwania, aby przywrócić go jako plik stanu systemu na komputerze alternatywnym.

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

1. W witrynie Azure portal utwórz [magazyn usług odzyskiwania](install-mars-agent.md#create-a-recovery-services-vault)i wybierz pliki, foldery i stan systemu z celów kopii zapasowej.
2. [Pobierz poświadczenia magazynu usług recovery services i instalatora agenta](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) na komputerze lokalnym.

    Aby chronić komputer lokalny, wybierając opcję Kopia zapasowa, wybierz pliki, foldery i stan systemu, a następnie pobierz agenta MARS.

3. Przygotowanie infrastruktury:

    a. Uruchom instalatora, aby [zainstalować agenta](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent).

    b. Użyj pobranych poświadczeń magazynu, aby zarejestrować komputer w magazynie usług odzyskiwania.
4. Z konsoli agenta na [kliencie skonfiguruj kopię zapasową](https://docs.microsoft.com/azure/backup/backup-windows-with-mars-agent#create-a-backup-policy). Określ zasady przechowywania danych kopii zapasowej, aby rozpocząć ich ochronę.

![Diagram agenta usługi Azure Backup](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-scenarios"></a>Dodatkowe scenariusze

- **Tworzenie kopii zapasowych określonych plików i folderów w maszynach wirtualnych platformy Azure:** Podstawową metodą tworzenia kopii zapasowych maszyn wirtualnych platformy Azure (VM) jest użycie rozszerzenia usługi Azure Backup na maszynie wirtualnej. Rozszerzenie kopii zapasowej całej maszyny Wirtualnej. Jeśli chcesz zrobić kopie zapasowe określonych plików i folderów w ramach maszyny Wirtualnej, możesz zainstalować agenta MARS na maszynach wirtualnych platformy Azure. Aby uzyskać więcej informacji, zobacz [Architektura: Wbudowana kopia zapasowa maszyny Wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

- **Rozmieszczanie w trybie offline:** Początkowe pełne kopie zapasowe danych na platformę Azure zazwyczaj przesyłają duże ilości danych i wymagają większej przepustowości sieci. Kolejne kopie zapasowe transfer tylko delta lub przyrostowe, ilość danych. Usługa Azure Backup kompresuje początkowe kopie zapasowe. Dzięki procesowi *rozmieszczania w trybie offline*usługa Azure Backup może używać dysków do przekazywania skompresowanych początkowych danych kopii zapasowej w trybie offline na platformę Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowej w trybie offline usługi Azure przy użyciu usługi Azure Data Box](offline-backup-azure-data-box.md).

## <a name="next-steps"></a>Następne kroki

[Macierz obsługi agenta usługi MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Mars agent często zadawane pytania](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
