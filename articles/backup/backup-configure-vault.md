---
title: Tworzenie kopii zapasowych maszyn z systemem Windows przy użyciu agenta MARS
description: Użyj agenta Azure Backup Microsoft Recovery Services (MARS) do tworzenia kopii zapasowych maszyn z systemem Windows.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: c6e5ea1ed1ec9dd922793dfc6834238c431ddc38
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2020
ms.locfileid: "76290873"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>Tworzenie kopii zapasowych maszyn z systemem Windows za pomocą agenta MARS usługi Azure Backup

W tym artykule opisano sposób tworzenia kopii zapasowych maszyn z systemem Windows przy użyciu usługi [Azure Backup](backup-overview.md) i agenta Microsoft Azure Recovery Services (MARS), znanego również jako agent Azure Backup.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Sprawdź wymagania wstępne i Utwórz magazyn Recovery Services.
> * Pobieranie i konfigurowanie agenta usług MARS
> * Utwórz zasady i harmonogram tworzenia kopii zapasowych.
> * Wykonaj kopię zapasową na żądanie.

## <a name="about-the-mars-agent"></a>Informacje o agencie MARS

Agent MARS jest używany przez Azure Backup do tworzenia kopii zapasowych plików, folderów i stanu systemu z maszyn lokalnych i maszyn wirtualnych platformy Azure w magazynie kopii zapasowych Recovery Services na platformie Azure. Agenta można uruchomić w następujący sposób:

* Uruchom agenta bezpośrednio na lokalnych maszynach z systemem Windows, aby można było tworzyć kopie zapasowe bezpośrednio do magazynu Recovery Services kopii zapasowych na platformie Azure.
* Uruchom agenta na maszynach wirtualnych platformy Azure z systemem Windows (obok rozszerzenia kopii zapasowej maszyny wirtualnej platformy Azure), aby utworzyć kopię zapasową określonych plików i folderów na maszynie wirtualnej.
* Uruchom agenta na serwerze Microsoft Azure Backup (serwera usługi MAB) lub na serwerze programu System Center Data Protection-Manager (DPM). W tym scenariuszu maszyny i obciążenia wykonują kopie zapasowe w programie serwera usługi MAB/DPM, a następnie serwera usługi MAB/DPM tworzy kopię zapasową magazynu na platformie Azure przy użyciu agenta MARS.
Elementy, których można utworzyć kopię zapasową, są zależne od tego, gdzie jest zainstalowany agent programu.

> [!NOTE]
> Podstawową metodą tworzenia kopii zapasowych maszyn wirtualnych platformy Azure jest użycie rozszerzenia Azure Backup na maszynie wirtualnej. Spowoduje to utworzenie kopii zapasowej całej maszyny wirtualnej. Jeśli chcesz utworzyć kopię zapasową określonych plików i folderów na maszynie wirtualnej, możesz zainstalować agenta MARS wraz z rozszerzeniem. [Dowiedz się więcej](backup-architecture.md#architecture-built-in-azure-vm-backup).

![Kroki procesu tworzenia kopii zapasowej](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Przed rozpoczęciem

* [Dowiedz się, jak](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) Azure Backup tworzenia kopii zapasowych maszyn z systemem Windows za pomocą agenta MARS.
* [Dowiedz się więcej o](backup-architecture.md#architecture-back-up-to-dpmmabs) architekturze tworzenia kopii zapasowych z uruchomionym agentem Mars na pomocniczym serwerze serwera usługi MAB lub DPM.
* [Zapoznaj](backup-support-matrix-mars-agent.md) się z obsługiwanymi informacjami i informacjami o tym, co można utworzyć przy użyciu agenta Mars.
* Sprawdź dostęp do Internetu na maszynach, dla których chcesz utworzyć kopię zapasową.
* Aby utworzyć kopię zapasową serwera lub klienta na platformie Azure, musisz mieć konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/) w zaledwie kilka minut.

### <a name="verify-internet-access"></a>Weryfikowanie dostępu do Internetu

Jeśli maszyna ma ograniczony dostęp do Internetu, upewnij się, że ustawienia zapory na komputerze lub serwerze proxy zezwalają na te adresy URL i adres IP:

#### <a name="urls"></a>Adresy URL

* www\.msftncsi.com
* *.Microsoft.com
* *.WindowsAzure.com
* *.microsoftonline.com
* *.windows.net

#### <a name="ip-addresses"></a>Adresy IP

* 20.190.128.0/18
* 40.126.0.0/18

Dostęp do wszystkich adresów URL i adresów IP wymienionych powyżej używa protokołu HTTPS na porcie 443.

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn Recovery Services przechowuje wszystkie utworzone kopie zapasowe i punkty odzyskiwania wraz z upływem czasu i zawiera zasady tworzenia kopii zapasowych stosowane do maszyn z kopią zapasową. Utwórz magazyn w następujący sposób:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu subskrypcji platformy Azure.

2. Wyszukaj i wybierz **Recovery Services magazynów**.

    ![Tworzenie magazynu usługi Recovery Services — krok 1](./media/backup-configure-vault/open-recovery-services-vaults.png)

3. W menu **magazyny Recovery Services** kliknij pozycję **+ Dodaj**.

    ![Tworzenie magazynu Usług odzyskiwania — krok 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. W polu **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn.

   * Nazwa musi być unikalna w tej subskrypcji platformy Azure.
   * Może zawierać od 2 do 50 znaków.
   * Musi rozpoczynać się od litery i może zawierać tylko litery, cyfry i łączniki.

5. Wybierz subskrypcję platformy Azure, grupę zasobów i region geograficzny, w którym ma zostać utworzony magazyn. Dane kopii zapasowej są wysyłane do magazynu. Następnie kliknij pozycję **Utwórz**.

    ![Tworzenie magazynu usługi Recovery Services — krok 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   * Utworzenie magazynu może chwilę potrwać.
   * Monitoruj powiadomienia o stanie w prawym górnym rogu portalu. Jeśli po kilku minutach nie widzisz magazynu, kliknij przycisk **Odśwież**.

     ![Klikanie pozycji Odśwież](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>Ustawianie nadmiarowości magazynu

Azure Backup automatycznie obsługuje magazyn dla magazynu. Należy określić sposób replikowania magazynu.

1. W bloku **Magazyny usług Recovery Services** kliknij nowy magazyn. W sekcji **Ustawienia** kliknij pozycję **Właściwości**.
2. W obszarze **Właściwości**, w obszarze **Konfiguracja kopii zapasowej**, kliknij przycisk **Aktualizuj**.

3. Wybierz typ replikacji magazynu i kliknij przycisk **Zapisz**.

      ![Ustawianie konfiguracji przechowywania dla nowego magazynu](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

* Zalecamy, aby Jeśli używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, Kontynuuj, aby użyć domyślnego ustawienia **geograficznie nadmiarowego** .
* Jeśli nie używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, wybierz pozycję **Lokalnie nadmiarowy**, co zmniejszy koszty magazynów platformy Azure.
* Dowiedz się więcej o nadmiarowości [geograficznym](../storage/common/storage-redundancy-grs.md) i [lokalnym](../storage/common/storage-redundancy-lrs.md) .

## <a name="download-the-mars-agent"></a>Pobieranie agenta MARS

Pobierz agenta MARS na potrzeby instalacji na maszynach, dla których chcesz utworzyć kopię zapasową.

* Jeśli Agent został już zainstalowany na wszystkich komputerach, upewnij się, że korzystasz z najnowszej wersji.
* Najnowsza wersja jest dostępna w portalu lub za pomocą [pobierania bezpośredniego](https://aka.ms/azurebackup_agent)

1. W magazynie w obszarze **wprowadzenie**kliknij pozycję **kopia zapasowa**.

    ![Otwieranie bloku celu kopii zapasowej](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. W **miejscu, w którym jest uruchomione Twoje obciążenie?** wybierz pozycję **lokalnie**. Należy wybrać tę opcję, nawet jeśli chcesz zainstalować agenta MARS na maszynie wirtualnej platformy Azure.
3. W **co chcesz utworzyć kopię zapasową?** wybierz pozycję **pliki i foldery** i/lub **stan systemu**. Dostępnych jest wiele innych opcji, ale są one obsługiwane tylko w przypadku korzystania z pomocniczego serwera zapasowego. Kliknij pozycję **Przygotuj infrastrukturę**.

      ![Konfigurowanie plików i folderów](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. W obszarze **Przygotowanie infrastruktury**w obszarze **Zainstaluj Recovery Services agenta**Pobierz agenta Mars.

    ![Przygotowywanie infrastruktury](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. W menu rozwijanym pobierania kliknij pozycję **Zapisz**. Domyślnie plik **MARSagentinstaller.exe** jest zapisywany w folderze Pobrane.

6. Sprawdź **już pobieranie lub używanie najnowszego agenta Recovery Services**, a następnie Pobierz poświadczenia magazynu.

    ![Pobieranie poświadczeń magazynu](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. Kliknij pozycję **Zapisz**. Plik zostanie pobrany do folderu pobierania. Nie można otworzyć pliku poświadczeń magazynu.

## <a name="install-and-register-the-agent"></a>Instalowanie i rejestrowanie agenta

1. Uruchom plik **plik marsagentinstaller. exe** na maszynach, dla których chcesz utworzyć kopię zapasową.
2. W Kreatorze instalacji agenta MARS > **ustawienia instalacji**Określ, gdzie chcesz zainstalować agenta, oraz lokalizację do użycia w pamięci podręcznej. Następnie kliknij przycisk **Next** (Dalej).
   * Azure Backup używa pamięci podręcznej do przechowywania migawek danych przed wysłaniem ich do platformy Azure.
   * W lokalizacji pamięci podręcznej powinna być wolne miejsce równe co najmniej 5% rozmiaru danych, których kopia zapasowa ma zostać utworzona.

    ![Ustawienia instalacji kreatora MARS](./media/backup-configure-vault/mars1.png)

3. W obszarze **Konfiguracja serwera proxy**Określ, w jaki sposób Agent uruchomiony na komputerze z systemem Windows będzie łączył się z Internetem. Następnie kliknij przycisk **Next** (Dalej).

   * Jeśli używasz niestandardowego serwera proxy, określ ustawienia serwera proxy i poświadczenia, jeśli jest to konieczne.
   * Należy pamiętać, że Agent musi mieć dostęp do [tych adresów URL](#verify-internet-access).

     ![Dostęp do Internetu w Kreatorze MARS](./media/backup-configure-vault/mars2.png)

4. W obszarze **Instalacja** Przejrzyj sprawdzanie wymagań wstępnych, a następnie kliknij przycisk **Instaluj**.
5. Po zainstalowaniu agenta kliknij przycisk przechodzenie **do rejestracji**.
6. W **Kreatorze rejestrowania serwera** > **identyfikacji magazynu**Przeglądaj i wybierz pobrany plik poświadczeń. Następnie kliknij przycisk **Next** (Dalej).

    ![Rejestrowanie poświadczeń magazynu](./media/backup-configure-vault/register1.png)

7. W obszarze **ustawienie szyfrowania**określ hasło, które będzie używane do szyfrowania i odszyfrowywania kopii zapasowych na komputerze.

    * Zapisz hasło szyfrowania w bezpiecznej lokalizacji.
    * Jeśli utracisz lub zapomnisz hasło, firma Microsoft nie może odzyskać danych kopii zapasowej. Zapisz plik w bezpiecznej lokalizacji. Jest on potrzebny do przywrócenia kopii zapasowej.

8. Kliknij przycisk **Zakończ**. Agent jest teraz zainstalowany, a maszyna zarejestrowana w magazynie. Wszystko jest gotowe do skonfigurowania kopii zapasowej i zaplanowania jej tworzenia.

## <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych określają, kiedy należy wykonać migawki danych w celu utworzenia punktów odzyskiwania oraz jak długo mają być przechowywane punkty odzyskiwania.

* Zasady tworzenia kopii zapasowej można skonfigurować przy użyciu agenta MARS.
* Azure Backup nie pobiera czasu letniego (DST) na konto. Może to spowodować niezgodność między rzeczywistym czasem a zaplanowaną godziną wykonywania kopii zapasowej.

Utwórz zasady w następujący sposób:
1. Po pobraniu i zarejestrowaniu agenta MARS Uruchom konsolę agenta. Aby go znaleźć, wyszukaj na maszynie łańcuch **Microsoft Azure Backup**.  
2. W obszarze **Akcje**kliknij pozycję **Zaplanuj kopię zapasową**.

    ![Planowanie tworzenia kopii zapasowej systemu Windows Server](./media/backup-configure-vault/schedule-first-backup.png)
3. W Kreatorze harmonogramu tworzenia kopii zapasowych > **wprowadzenie**kliknij przycisk **dalej**.
4. W obszarze **Wybierz elementy do wykonania kopii zapasowej**kliknij pozycję **Dodaj elementy**.

    ![Wybierz elementy do utworzenia kopii zapasowej](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. W obszarze **Wybierz elementy**wybierz, co chcesz utworzyć kopię zapasową, a następnie kliknij przycisk **OK**.

    ![Wybrane elementy do utworzenia kopii zapasowej](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. Na stronie **Wybierz elementy do wykonania kopii zapasowej** kliknij przycisk **dalej**.
7. Na stronie **Określanie harmonogramu tworzenia kopii zapasowych** Określ, kiedy mają być wykonywane codziennie lub cotygodniowe kopie zapasowe. Następnie kliknij przycisk **Next** (Dalej).

    - Punkt odzyskiwania jest tworzony podczas tworzenia kopii zapasowej.
    - Liczba punktów odzyskiwania utworzonych w danym środowisku zależy od harmonogramu tworzenia kopii zapasowych.


8. Codzienne kopie zapasowe można zaplanować maksymalnie trzy razy dziennie. Na przykład zrzut ekranu przedstawia dwie codzienne kopie zapasowe, jeden o północy i jeden o godzinie 6:00 PM.

    ![Dzienny harmonogram](./media/backup-configure-vault/day-schedule.png)


9. Można uruchamiać cotygodniowe kopie zapasowe. Na przykład zrzut ekranu pokazuje kopie zapasowe wykonane każdą alternatywną niedzielę & środę o godzinie 9:30 AM i 1:00 AM.

    ![Harmonogram tygodniowy](./media/backup-configure-vault/week-schedule.png)


10. Na stronie **Wybierz zasady przechowywania** Określ, w jaki sposób mają być przechowywane historyczne kopie danych. Następnie kliknij przycisk **Next** (Dalej).

    - Ustawienia przechowywania określają, które punkty odzyskiwania mają być przechowywane, oraz czas ich przechowywania.
    - Na przykład po ustawieniu dziennego ustawienia przechowywania należy wskazać, że w określonym czasie przechowywania najnowszy punkt odzyskiwania będzie przechowywany przez określoną liczbę dni. Innym przykładem może być określenie miesięcznych zasad przechowywania, które wskazują, że punkt odzyskiwania utworzony na 30. miesiąca powinien być przechowywany przez 12 miesięcy.
    - Dzienne i cotygodniowe przechowywanie punktów odzyskiwania zwykle pokrywa się z harmonogramem tworzenia kopii zapasowych. Oznacza to, że gdy kopia zapasowa jest wyzwalana zgodnie z harmonogramem, punkt odzyskiwania utworzony w ramach kopii zapasowej jest przechowywany przez czas określony w zasadach przechowywania dziennego lub tygodniowego.
    - Na przykład na poniższym zrzucie ekranu:

        -   Codzienne kopie zapasowe o północy i 6:00 PM są przechowywane przez siedem dni.
        -   Kopie zapasowe wykonane w sobotę o północy i 6:00 PM są przechowywane przez cztery tygodnie.
        -   Kopie zapasowe wykonane w sobotę w ciągu ostatniego tygodnia miesiąca o północy i 6:00 PM są przechowywane przez 12 miesięcy.
        -   Kopie zapasowe wykonane w sobotę w ciągu ostatniego tygodnia marca są przechowywane przez 10 lat.

    ![Przykład przechowywania](./media/backup-configure-vault/retention-example.png)


11. W obszarze **Wybierz typ początkowej kopii zapasowej** Zdecyduj, czy chcesz pobrać początkową kopię zapasową za pośrednictwem sieci, lub Użyj kopii zapasowej offline (Aby uzyskać więcej informacji na temat odwołania do kopii zapasowej offline, zobacz ten [artykuł](backup-azure-backup-import-export.md)). Aby pobrać początkową kopię zapasową za pośrednictwem sieci, wybierz opcję **automatycznie przez sieć** , a następnie kliknij przycisk **dalej**.

    ![Typ początkowej kopii zapasowej](./media/backup-azure-manage-mars/choose-initial-backup-type.png)


12. W obszarze **potwierdzenie**Przejrzyj informacje, a następnie kliknij przycisk **Zakończ**.

    ![Potwierdź typ kopii zapasowej](./media/backup-azure-manage-mars/confirm-backup-type.png)


13. Po ukończeniu harmonogramu tworzenia kopii zapasowej przez kreatora kliknij przycisk **Zamknij**.

    ![Potwierdź proces modyfikacji kopii zapasowej](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Należy utworzyć zasady na każdym komputerze, na którym zainstalowano agenta.

### <a name="perform-the-initial-backup-offline"></a>Wykonywanie początkowej kopii zapasowej w trybie offline

Początkową kopię zapasową można uruchomić automatycznie za pośrednictwem sieci lub w trybie offline. Rozmieszczanie w trybie offline dla początkowej kopii zapasowej jest przydatne, jeśli masz duże ilości danych, które będą wymagały przetransferowania wielu przepustowości sieci. Przeprowadzenie transferu w trybie offline odbywa się w następujący sposób:

1. Zapisujesz dane kopii zapasowej w lokalizacji tymczasowej.
2. Narzędzie AzureOfflineBackupDiskPrep służy do kopiowania danych z lokalizacji tymczasowej do co najmniej jednego dysku SATA.
3. Narzędzie tworzy zadanie importu platformy Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) na temat importowania i eksportowania na platformie Azure.
4. Dyski SATA są wysyłane do centrum danych platformy Azure.
5. W centrum danych dane dysku są kopiowane na konto usługi Azure Storage.
6. Azure Backup kopiuje dane z konta magazynu do magazynu, a przyrostowe kopie zapasowe są zaplanowane.

[Dowiedz się więcej](backup-azure-backup-import-export.md) o rozsadzenia w trybie offline.

### <a name="enable-network-throttling"></a>Włącz ograniczanie sieci

Można kontrolować sposób używania przepustowości sieci przez agenta MARS przez włączenie ograniczania przepustowości sieci. Ograniczanie przepustowości jest przydatne, jeśli trzeba utworzyć kopię zapasową danych w godzinach pracy, ale chcesz kontrolować, ile przepustowości jest używany do działania tworzenia kopii zapasowych i przywracania.

* Funkcja ograniczania sieci Azure Backup używa [Quality of Service (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) w lokalnym systemie operacyjnym.
* Funkcja ograniczania przepustowości sieci dla kopii zapasowych jest dostępna w systemie Windows Server 2012 lub nowszym oraz w systemie Windows 8. Systemy operacyjne powinny mieć zainstalowane najnowsze dodatki Service Pack.

Włącz ograniczanie sieci w następujący sposób:

1. W agencie MARS kliknij pozycję **Zmień właściwości**.
2. Na karcie **ograniczanie** poziomu, zaznacz opcję **Włącz ograniczenie przepustowości Internetu dla operacji tworzenia kopii zapasowej**.

    ![Ograniczanie przepustowości sieci](./media/backup-configure-vault/throttling-dialog.png)
3. Określ dozwoloną przepustowość podczas pracy i poza godzinami pracy. Wartości przepustowości zaczynają się od 512 KB/s i mogą trafiać do 1 023 MB/s. Następnie kliknij przycisk **OK**.

## <a name="run-an-on-demand-backup"></a>Uruchamianie kopii zapasowej na żądanie

1. W agencie MARS kliknij pozycję **Wykonaj kopię zapasową teraz**.

    ![Natychmiastowe tworzenie kopii zapasowej systemu Windows Server](./media/backup-configure-vault/backup-now.png)

2. Jeśli Agent MARS ma wersję 2.0.9169.0 lub nowszą, można ustawić niestandardowe przechowywanie. W sekcji **Zachowaj kopię zapasową** do wybierz datę z wyświetlonego kalendarza:

   ![Zachowaj kalendarz kopii zapasowych](./media/backup-configure-vault/mars-ondemand.png)

3. W obszarze **potwierdzenie**przejrzyj ustawienia, a następnie kliknij pozycję **Utwórz kopię zapasową**.
4. Kliknij przycisk **Zamknij**, aby zamknąć kreatora. Jeśli wykonasz tę czynność przed zakończeniem tworzenia kopii zapasowej, Kreator będzie kontynuował pracę w tle.
5. Po zakończeniu tworzenia początkowej kopii zapasowej w konsoli usługi Backup zostanie wyświetlony stan **Ukończono zadanie**.

## <a name="on-demand-backup-policy-retention-behavior"></a>Zachowanie przechowywania zasad tworzenia kopii zapasowych na żądanie

>[!NOTE]
>Dotyczy tylko wersji agenta MARS starszej niż 2.0.9169.0
>

* Aby uzyskać więcej informacji, zobacz krok 8 [tworzenia zasad tworzenia kopii zapasowych](backup-configure-vault.md#create-a-backup-policy)

| Opcja harmonogramu kopii zapasowych | Jak długo będą przechowywane kopie zapasowe danych?
| -- | --
| Zaplanuj tworzenie kopii zapasowej co: * dzień | **Domyślne przechowywanie**: równoważne "przechowywanie w dniach dla codziennych kopii zapasowych" <br/><br/> **Wyjątek**: Jeśli dzienny harmonogram zaplanowanej kopii zapasowej do przechowywania długoterminowego (tygodnie, miesiące, lata) zakończy się niepowodzeniem, kopia zapasowa na żądanie wyzwolona bezpośrednio po nieudanej zaplanowanej kopii zapasowej będzie brana pod uwagę w przypadku przechowywania długoterminowego. W przeciwnym razie Następna zaplanowana kopia zapasowa jest traktowana do długoterminowego przechowywania.<br/><br/> **Przykład**: Jeśli (powiedzieć) zaplanowana kopia zapasowa podjęta w czwartek 8:00 kończy się niepowodzeniem, a ta sama kopia zapasowa była brana pod uwagę w przypadku okresu utrzymywania tygodniowego/miesięcznego/rocznego, wówczas pierwsza kopia zapasowa na żądanie wyzwolona przed następną zaplanowaną kopią zapasową (powiedzmy 8:00) zostanie automatycznie oznaczona jako zastosowana do czwartek 8:00.
| Zaplanuj tworzenie kopii zapasowej co: * Cotygodniowo | **Domyślne przechowywanie**: 1 dzień <br/> Kopie zapasowe na żądanie wykonane dla źródła danych, które mają cotygodniowe zasady tworzenia kopii zapasowych, są usuwane następnego dnia, nawet jeśli są to najnowsze kopie zapasowe źródła danych. <br/><br/> **Wyjątek**: Jeśli cotygodniowy harmonogram tworzenia kopii zapasowych do długoterminowego przechowywania (tygodnie, miesiące, lata) zakończy się niepowodzeniem, kopia zapasowa na żądanie wyzwolona bezpośrednio po nieudanej zaplanowanej kopii zapasowej będzie brana pod uwagę w przypadku przechowywania długoterminowego. W przeciwnym razie Następna zaplanowana kopia zapasowa jest traktowana do długoterminowego przechowywania. <br/><br/> **Przykład**: Jeśli (powiedzieć) zaplanowana kopia zapasowa podjęta w czwartek 8:00 nie powiedzie się, a ta sama kopia zapasowa była brana pod uwagę w przypadku przechowywania miesięcznego/rocznego, to pierwsza kopia zapasowa na żądanie wyzwolona przed następną zaplanowaną kopią zapasową (powiedzmy 8:00 8:00).

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak przywrócić pliki](backup-azure-restore-windows-server.md).
