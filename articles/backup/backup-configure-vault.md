---
title: Tworzenie kopii zapasowych maszyn Windows za pomocą agenta usług MARS kopia zapasowa Azure
description: Tworzenie kopii zapasowych maszyn Windows przy użyciu agenta usługi Azure Backup Microsoft Recovery Services (MARS).
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: raynew
ms.openlocfilehash: 7a1bd6da68b49481429709c7e4fd37dd5c07ae2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810366"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>Tworzenie kopii zapasowych maszyn Windows za pomocą agenta usług MARS kopia zapasowa Azure

W tym artykule wyjaśniono, jak tworzyć kopie zapasowe maszyn Windows przy użyciu [kopia zapasowa Azure](backup-overview.md) usługę i agenta usługi Microsoft Azure Recovery Services (MARS), znany także jako agenta usługi Azure Backup.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Sprawdzenie wymagań wstępnych i utworzyć magazyn usługi Recovery Services.
> * Pobieranie i konfigurowanie agenta usług MARS
> * Utworzenie zasad kopii zapasowych i harmonogramu.
> * Tworzenie kopii zapasowej, należy wykonać ad hoc.

## <a name="about-the-mars-agent"></a>Temat agenta usług MARS

Agenta usług MARS jest używany przez usługę Azure Backup do tworzenia kopii zapasowych plików, folderów i stanu systemu z maszyn lokalnych i maszyn wirtualnych platformy Azure do tworzenia kopii zapasowej magazynu usługi Recovery Services na platformie Azure. Agenta można uruchomić w następujący sposób:

- Uruchom agenta bezpośrednio na maszynach Windows w środowisku lokalnym, tak aby ich kopię zapasową można wykonać bezpośrednio do tworzenia kopii zapasowej magazynu usługi Recovery Services na platformie Azure.
- Uruchamianie agenta Azure maszyn wirtualnych z systemem Windows (side-by-side przy użyciu rozszerzenia kopii zapasowej maszyny Wirtualnej platformy Azure), aby utworzyć kopię zapasową określonych plików i folderów na maszynie Wirtualnej.
- Uruchom agenta na Microsoft Azure Backup Server (MABS) lub programu System Center Data Protection - server Manager (DPM). W tym scenariuszu maszyn i obciążeń tworzenie kopii zapasowej serwera usługi Mab/DPM, a następnie serwera usługi Mab/DPM tworzy kopie zapasowe w magazynie na platformie Azure przy użyciu agenta usług MARS.
Co można utworzyć kopię, zależy od tego, gdzie agent jest zainstalowany.

> [!NOTE]
> Jest podstawową metodą tworzenia kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu rozszerzenia usługi Azure Backup na maszynie Wirtualnej. To wykonuje kopię zapasową całej maszyny Wirtualnej. Można zainstalować i używać agenta usług MARS obok rozszerzenia, jeśli chcesz utworzyć kopię zapasową określonych plików i folderów na maszynie Wirtualnej. [Dowiedz się więcej](backup-architecture.md#architecture-direct-backup-of-azure-vms).

![Kroki procesu tworzenia kopii zapasowej](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Przed rozpoczęciem

- [Dowiedz się, jak](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) usługi Azure Backup tworzy kopię zapasową maszyn Windows za pomocą agenta usług MARS.
- [Dowiedz się więcej o](backup-architecture.md#architecture-back-up-to-dpmmabs) architektura kopii zapasowych uruchamiania agenta usług MARS na serwerze pomocniczym serwera usługi Mab lub programu DPM.
- [Przegląd](backup-support-matrix-mars-agent.md) elementy obsługiwane i co można utworzyć kopię za pomocą agenta usług MARS.
- Sprawdź dostęp do Internetu na maszynach, które chcesz utworzyć kopię zapasową.
- Aby utworzyć kopię zapasową serwera lub klienta na platformie Azure, potrzebne jest konto platformy Azure. Jeśli nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/) w zaledwie kilka minut.

### <a name="verify-internet-access"></a>Sprawdź dostęp do Internetu

Jeśli komputer ma ograniczony dostęp do Internetu, należy się upewnić, że ustawienia zapory na komputerze lub serwer proxy zezwalać na te adresy URL i adresu IP:

**Adresy URL**

- www\.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

**Adres IP**

- 20.190.128.0/18
- 40.126.0.0/18


## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn usługi Recovery Services przechowuje kopie zapasowe i punkty odzyskiwania, które utworzyć wraz z upływem czasu i zawiera zasad tworzenia kopii zapasowej stosowane do kopii zapasowej maszyn. Utwórz magazyn w następujący sposób:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) przy użyciu subskrypcji platformy Azure.
2. W polu wyszukiwania wpisz **usługi Recovery Services** i kliknij przycisk **Magazyny usługi Recovery Services**.

    ![Tworzenie magazynu usługi Recovery Services — krok 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png)

3. Na **Magazyny usługi Recovery Services** menu, kliknij przycisk **+ Dodaj**.

    ![Tworzenie magazynu Usług odzyskiwania — krok 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. W polu **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn.

   - Nazwa musi być unikalna w tej subskrypcji platformy Azure.
   - Może on zawierać 2 do 50 znaków.
   - Musi zaczynać się literą i może zawierać tylko litery, cyfry i łączniki.

5. Wybierz subskrypcję platformy Azure, grupę zasobów i region geograficzny, w którym można utworzyć magazynu. Dane kopii zapasowej są wysyłane do magazynu. Następnie kliknij pozycję **Utwórz**.

    ![Tworzenie magazynu usługi Recovery Services — krok 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   - Może upłynąć trochę czasu utworzenie magazynu.
   - Monitoruj powiadomienia o stanie wyświetlane w obszarze prawym górnym rogu portalu. Jeśli po kilku minutach nie widzisz magazynu, kliknij przycisk **Odśwież**.

     ![Klikanie pozycji Odśwież](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>Zestaw nadmiarowości magazynu

Usługa Azure Backup automatycznie obsługuje magazynu dla magazynu. Należy określić sposób replikowania tego magazynu.

1. W bloku **Magazyny usług Recovery Services** kliknij nowy magazyn. W obszarze **ustawienia** kliknij **właściwości**.
2. W **właściwości**w obszarze **konfiguracji kopii zapasowej**, kliknij przycisk **aktualizacji**.

3. Wybierz typ replikacji magazynu, a następnie kliknij przycisk **Zapisz**.

      ![Ustawianie konfiguracji przechowywania dla nowego magazynu](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

- Zaleca się, jeśli używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych w dalszym ciągu używać ustawień domyślnych **magazynu geograficznie nadmiarowego** ustawienie.
- Jeśli nie używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, wybierz pozycję **Lokalnie nadmiarowy**, co zmniejszy koszty magazynów platformy Azure.
- Dowiedz się więcej o [geograficznie](../storage/common/storage-redundancy-grs.md) i [lokalnego](../storage/common/storage-redundancy-lrs.md) nadmiarowości.

## <a name="download-the-mars-agent"></a>Pobierz agenta usług MARS

Pobierz agenta usług MARS zainstalowany na maszynach, które chcesz utworzyć kopię zapasową.

- Jeśli po zainstalowaniu agenta na maszynach, upewnij się, że używasz najnowszej wersji.
- Najnowsza wersja jest dostępna w portalu lub przy użyciu [pobieranie bezpośrednie](https://aka.ms/azurebackup_agent)

1. W magazynie w obszarze **wprowadzenie**, kliknij przycisk **kopii zapasowej**.

    ![Otwieranie bloku celu kopii zapasowej](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. W **gdzie jest uruchomione Twoje obciążenie?**, wybierz opcję **On-premises**. Należy wybierz tę opcję, nawet jeśli chcesz zainstalować agenta MARS na Maszynie wirtualnej platformy Azure.
3. W **jakich elementów chcesz utworzyć kopię zapasową?**, wybierz opcję **pliki i foldery** i/lub **stanu systemu**. Istnieje wiele innych opcji, ale są one obsługiwane tylko, jeśli korzystasz z pomocniczego serwera kopii zapasowych. Kliknij przycisk **przygotowanie infrastruktury**.

      ![Konfigurowanie plików i folderów](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. Na **przygotowanie infrastruktury**w obszarze **agenta usługi Recovery Services Zainstaluj**, Pobierz agenta usług MARS.

    ![Przygotowywanie infrastruktury](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. W menu rozwijanym pobierania kliknij pozycję **Zapisz**. Domyślnie plik **MARSagentinstaller.exe** jest zapisywany w folderze Pobrane.

6. Sprawdź **już albo Pobierz lub przy użyciu najnowszego agenta usług odzyskiwania**, a następnie Pobierz poświadczenia magazynu.

    ![Pobieranie poświadczeń magazynu](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. Kliknij pozycję **Zapisz**. Plik jest pobierany do folderu pobierania. Nie można otworzyć pliku poświadczeń magazynu.

## <a name="install-and-register-the-agent"></a>Instalowanie i rejestrowanie agenta

1. Uruchom **MARSagentinstaller.exe** plik na komputerach, które chcesz utworzyć kopię zapasową.
2. W Kreatorze instalacji agenta usług MARS > **ustawienia instalacji**, określ, w którym chcesz zainstalować agenta i lokalizację do użycia dla pamięci podręcznej. Następnie kliknij przycisk **Next** (Dalej).
   - Usługa Azure Backup używa pamięci podręcznej do przechowywania migawki danych przed wysłaniem ich do platformy Azure.
   - Lokalizacja pamięci podręcznej powinien mieć miejsca równa co najmniej 5% rozmiaru danych, które będzie można utworzyć kopię zapasową.

     ![Ustawienia instalacji Kreator MARS](./media/backup-configure-vault/mars1.png)

2. W **konfigurację serwera Proxy**, określ, jak agenta uruchomionego na komputerze Windows połączy się z Internetem. Następnie kliknij przycisk **Next** (Dalej).

   - Jeśli używasz niestandardowego serwera proxy, określ ustawienia serwera proxy i poświadczenia, jeśli to konieczne.
   - Należy pamiętać, że agent musi mieć dostęp do [tych adresów URL](#verify-internet-access).

     ![Dostęp do Internetu kreatora MARS](./media/backup-configure-vault/mars2.png)

3. W **instalacji** Przejrzyj sprawdzania wymagań wstępnych, a następnie kliknij przycisk **zainstalować**.
4. Po zainstalowaniu agenta kliknij **przejść do rejestracji**.
5. W **kreatora rejestrowania serwera** > **identyfikacji magazynu**Wyszukaj i wybierz pobrany plik poświadczeń. Następnie kliknij przycisk **Next** (Dalej).

    ![Register — poświadczenia magazynu](./media/backup-configure-vault/register1.png)

6. W **ustawienie szyfrowania**, określ hasło, który będzie używany do szyfrowania i odszyfrowywania kopii zapasowych dla maszyny.

    - Zapisz hasło szyfrowania w bezpiecznej lokalizacji.
    - Jeśli utracisz lub zapomnisz hasło, Microsoft nie może odzyskać danych kopii zapasowej. Zapisz plik w bezpiecznej lokalizacji. Będziesz ich potrzebować do przywrócenia kopii zapasowej.

7. Kliknij przycisk **Zakończ**. Agent jest teraz zainstalowany, a maszyna zarejestrowana w magazynie. Wszystko jest gotowe do skonfigurowania kopii zapasowej i zaplanowania jej tworzenia.

## <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady kopii zapasowych określa, kiedy migawek danych do tworzenia punktów odzyskiwania i czas przechowywania punktów odzyskiwania.

- Możesz skonfigurować zasady tworzenia kopii zapasowych za pomocą agenta usług MARS.
- Usługa Azure Backup nie automatycznie uwzględnia czasu letniego (DST). Może to spowodować pewne rozbieżności między rzeczywisty czas i według harmonogramu wykonywania kopii zapasowej.

Utwórz zasady w następujący sposób:

1. Na każdym komputerze należy otworzyć agenta usług MARS. Aby go znaleźć, wyszukaj na maszynie łańcuch **Microsoft Azure Backup**.
2. W **akcje**, kliknij przycisk **Zaplanuj wykonywanie kopii zapasowej**.

    ![Planowanie tworzenia kopii zapasowej systemu Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

3. W Kreatorze harmonogramu kopii zapasowej > **wprowadzenie**, kliknij przycisk **dalej**.
4. W **Wybieranie elementów do wykonywania kopii zapasowych**, kliknij przycisk **Dodaj elementy**.
5. W **wybierz elementy**, wybierz program do tworzenia kopii zapasowych. Następnie kliknij przycisk **OK**.
6. W **Wybieranie elementów do wykonywania kopii zapasowych** kliknij **dalej**.
7. W **Określ harmonogram tworzenia kopii zapasowych** Określ, kiedy chcesz korzystać z kopii zapasowych codziennie lub co tydzień. Następnie kliknij przycisk **Next** (Dalej).

    - Punkt odzyskiwania jest tworzony, gdy jest wykonywana kopia zapasowa.
    - Liczba punktów odzyskiwania utworzonych w środowisku jest zależny od harmonogramu tworzenia kopii zapasowych.

1. Można zaplanować codzienne kopie zapasowe, maksymalnie trzy razy dziennie. Na przykład na zrzucie ekranu przedstawiono dwa codzienne wykonywanie kopii zapasowych — jedną o północy, a jedna po godzinie 18: 00.

    ![Dzienny harmonogram](./media/backup-configure-vault/day-schedule.png)

9. Możesz uruchomić cotygodniowe kopie zapasowe za. Na przykład na zrzucie ekranu przedstawiono kopii zapasowych wykonanych każdej alternatywny niedziela & środa 9:30:00 i 1:00:00.

    ![Harmonogram tygodniowy](./media/backup-configure-vault/week-schedule.png)

8. Na **wybierz zasady przechowywania** Określ, jak przechowywać historyczne kopie danych. Następnie kliknij przycisk **Next** (Dalej).

   - Ustawienia przechowywania określ punkty odzyskiwania, które powinny być przechowywane i jak długo są przechowywane dla.
   - Na przykład ustawiając dzienny ustawienia przechowywania, wskazujesz, czy w chwili określony do przechowywania codziennych najnowszego punktu odzyskiwania będą przechowywane przez określoną liczbę dni. Lub inny przykład można określić miesięczne zasady przechowywania, aby wskazać, że punkt odzyskiwania utworzony na 30 dnia każdego miesiąca powinny znajdować się w ciągu 12 miesięcy.
   - Czas przechowywania punktu odzyskiwania dzienne i tygodniowe pokrywa się zazwyczaj z harmonogramu tworzenia kopii zapasowych. Co oznacza, że podczas tworzenia kopii zapasowej jest wyzwalane zgodnie z harmonogramem, punkt odzyskiwania utworzony przez tworzenie kopii zapasowej jest przechowywany na czas trwania czcionką codziennie lub co tydzień zasady przechowywania.
   - Na przykład na poniższym zrzucie ekranu:
     - Codzienne wykonywanie kopii zapasowych o północy i 18: 00 są utrzymywane przez siedem dni.
     - Kopie zapasowe wykonane w sobotę o północy i 18: 00 są utrzymywane przez 4 tygodnie.
     - Kopie zapasowe wykonane w sobotę w ostatnim tygodniu miesiąca o północy i 18: 00, są przechowywane w ciągu 12 miesięcy. — Kopie zapasowe wykonane w sobotę w ostatnim tygodniu marca są zachowywane przez okres 10 lat.

   ![Przykład przechowywania](./media/backup-configure-vault/retention-example.png)

11. W **wybierz typ początkowej kopii zapasowej** Określ sposób wykonania początkowej kopii zapasowej, za pośrednictwem sieci lub w trybie offline. Następnie kliknij przycisk **Next** (Dalej).

10. W **potwierdzenie**, zapoznaj się z informacjami, a następnie kliknij przycisk **Zakończ**.
11. Po ukończeniu harmonogramu tworzenia kopii zapasowej przez kreatora kliknij przycisk **Zamknij**.

### <a name="perform-the-initial-backup-offline"></a>Wykonania początkowej kopii zapasowej w trybie offline

Możesz uruchomić początkowej kopii zapasowej automatycznie za pośrednictwem sieci lub w trybie offline. Rozmieszczanie offline początkowa kopia zapasowa jest przydatne w przypadku dużych ilości danych, które wymagają dużej ilości przepustowości sieci do transferu. W trybie offline przeniesienia wykonaj następujące czynności:

1. Piszesz dane kopii zapasowej do lokalizacji tymczasowej.
2. Narzędzie AzureOfflineBackupDiskPrep Aby skopiować dane z lokalizacji tymczasowej do co najmniej jeden dysk SATA.
3. Narzędzie tworzy zadanie usługi Azure Import. [Dowiedz się więcej](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) dotyczące usługi Azure import i eksport.
4. Dyski SATA wysyła do centrum danych platformy Azure.
5. W centrum danych dane dysku jest kopiowana do konta usługi Azure storage.
6. Usługa Azure Backup kopiuje dane z konta magazynu do magazynu, a przyrostowych kopii zapasowych.

[Dowiedz się więcej](backup-azure-backup-import-export.md) o rozmieszczania w trybie offline.

### <a name="enable-network-throttling"></a>Włącz ograniczanie przepustowości sieci

Można kontrolować, jak przepustowość sieci jest używane przez agenta usług MARS, należy włączyć ograniczanie przepustowości sieci. Ograniczanie przepływności jest przydatne, jeśli potrzebujesz danych poza godzinami pracy, ale umożliwia kontrolowanie, ile przepustowości jest używany do tworzenia kopii zapasowych i przywracanie kopii zapasowej działania.

- Sieć platformy Azure Backup ograniczania używa [jakości usług (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) w lokalnym systemie operacyjnym.
- Ograniczania dla kopii zapasowej sieci jest dostępna w systemie Windows Server 2008 R2 lub nowszym i Windows 7 lub nowszy. Systemy operacyjne powinna być uruchomiona najnowszymi dodatkami service pack.

Włącz ograniczenie w następujący sposób:

1. Agenta usług MARS kliknij **Zmień właściwości**.
2. Na **ograniczania** karcie wyboru **włączyć internetowe ograniczanie użycia przepustowości dla operacji tworzenia kopii zapasowej**.

    ![Ograniczanie przepustowości sieci](./media/backup-configure-vault/throttling-dialog.png)
3. Określ dozwolone przepustowości podczas pracy i poza godzinami pracy. Wartości przepustowości, Rozpocznij na 512 KB/s i przejdź do 1,023 MB/s. Następnie kliknij przycisk **OK**.

## <a name="run-an-ad-hoc-backup"></a>Uruchom tworzenie kopii zapasowej ad-hoc

1. Agenta usług MARS kliknij **Utwórz kopię zapasową teraz**. To dotyczącego Replikacja początkowa przez sieć.

    ![Natychmiastowe tworzenie kopii zapasowej systemu Windows Server](./media/backup-configure-vault/backup-now.png)

2. W **potwierdzenie**, przejrzyj ustawienia i kliknij przycisk **Utwórz kopię zapasową**.
3. Kliknij przycisk **Zamknij**, aby zamknąć kreatora. Jeśli możesz to zrobić przed zakończeniem wykonywania kopii zapasowej, Kreator będzie nadal działać w tle.

Po zakończeniu tworzenia początkowej kopii zapasowej w konsoli usługi Backup zostanie wyświetlony stan **Ukończono zadanie**.

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak](backup-azure-restore-windows-server.md) przywracania plików.
