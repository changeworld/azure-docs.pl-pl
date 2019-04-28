---
title: Tworzenie kopii zapasowej stanu systemu Windows na platformie Azure
description: Dowiedz się tworzył kopie zapasowe stanu systemu Windows Server i/lub Windows komputerów Azure.
services: backup
author: saurabhsensharma
manager: shivamg
keywords: jak tworzyć kopie zapasowe; tworzenie kopii zapasowych; tworzenie kopii zapasowych plików i folderów
ms.service: backup
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: saurse
ms.openlocfilehash: 6d8cbac7eab797662896a96ed588c9d6370cb230
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782726"
---
# <a name="back-up-windows-system-state-in-resource-manager-deployment"></a>Tworzenie kopii zapasowej stanu systemu Windows podczas wdrażania usługi Resource Manager
W tym artykule opisano sposób tworzenia kopii zapasowej stanu systemu Windows Server na platformie Azure. Ten samouczek zawiera podstawowe informacje.

Jeśli chcesz dowiedzieć się więcej o usłudze Azure Backup, przeczytaj to [omówienie](backup-overview.md).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) umożliwiające dostęp do dowolnej usługi Azure.

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services
Aby utworzyć kopię zapasową stanu systemu Windows Server, należy utworzyć magazyn usługi Recovery Services w regionie, w którym chcesz przechowywać dane. Należy również określić sposób replikowania magazynu.

### <a name="to-create-a-recovery-services-vault"></a>Aby utworzyć magazyn usługi Recovery Services
1. Jeśli nie zostało to wcześniej zrobione, zaloguj się witryny [Azure Portal](https://portal.azure.com/) przy użyciu subskrypcji platformy Azure.
2. W menu Centrum kliknij pozycję **Wszystkie usługi**, na liście zasobów wpisz ciąg **Recovery Services**, a następnie kliknij pozycję **Magazyny usługi Recovery Services**.

    ![Tworzenie magazynu usługi Recovery Services — krok 1](./media/backup-azure-system-state/open-rs-vault-list.png) <br/>

    Jeśli w ramach subskrypcji istnieją magazyny usług odzyskiwania, zostaną one wyświetlone.
3. W menu **Magazyny usługi Recovery Services** kliknij pozycję **Dodaj**.

    ![Tworzenie magazynu Usług odzyskiwania — krok 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Zostanie otwarty blok magazynu Usług odzyskiwania i pojawi się monit o podanie wartości w polach **Nazwa**, **Subskrypcja**, **Grupa zasobów** i **Lokalizacja**.

    ![Tworzenie magazynu usługi Recovery Services — krok 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. W polu **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn. Nazwa musi być unikalna w tej subskrypcji platformy Azure. Wpisz nazwę o długości od 2 do 50 znaków. Musi ona rozpoczynać się od litery i może zawierać tylko litery, cyfry i łączniki.

5. W sekcji **Subskrypcja** użyj menu rozwijanego, aby wybrać subskrypcję platformy Azure. Jeśli używasz tylko jednej subskrypcji, zostanie ona wyświetlona i możesz przejść do następnego kroku. Jeśli nie masz pewności, której subskrypcji użyć, wybierz domyślną (lub sugerowaną). Większa liczba opcji do wyboru jest dostępna tylko w przypadku, gdy konto organizacji jest skojarzone z wieloma subskrypcjami platformy Azure.

6. W sekcji **Grupa zasobów**:

    * wybierz pozycję **Utwórz nową**, jeśli chcesz utworzyć grupę zasobów.
    Lub
    * wybierz pozycję **Użyj istniejącej** i kliknij menu rozwijane, aby wyświetlić listę dostępnych grup zasobów.

   Pełne informacje na temat grup zasobów można znaleźć w temacie [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

7. Kliknij pozycję **Lokalizacja**, aby wybrać region geograficzny magazynu. Ten wybór określa region geograficzny, do którego wysyłane są dane kopii zapasowej.

8. W dolnej części bloku magazynu usług Recovery Services kliknij pozycję **Utwórz**.

    Utworzenie magazynu usług Recovery Services może potrwać kilka minut. Monitoruj powiadomienia o stanie wyświetlane w prawej górnej części obszaru portalu. Po utworzeniu magazynu pojawi się on na liście magazynów Usług odzyskiwania. Jeśli po kilku minutach nie widzisz swojego magazynu, kliknij pozycję **Odśwież**.

    ![Klikanie pozycji Odśwież](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Po wyświetleniu magazynu na liście magazynów usług Recovery Services możesz rozpocząć ustawianie nadmiarowości przechowywania.

### <a name="set-storage-redundancy-for-the-vault"></a>Ustawianie nadmiarowości przechowywania dla magazynu
Po utworzeniu magazynu usług Recovery Services upewnij się, że nadmiarowość magazynu została skonfigurowana w preferowany sposób.

1. W bloku **Magazyny usług Recovery Services** kliknij nowy magazyn.

    ![Wybieranie nowego magazynu z listy magazynów usług Recovery Services](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Wybranie magazynu spowoduje zwężenie bloku **Magazyn usług Recovery Services** oraz otwarcie bloków Ustawienia (*o nazwie magazynu wskazanego w górnej części*) i szczegółów magazynu.

    ![Wyświetlanie konfiguracji przechowywania dla nowego magazynu](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. W bloku ustawień nowego magazynu użyj pionowego suwaka, aby przewinąć w dół do sekcji Zarządzanie, a następnie kliknij pozycję **Infrastruktura zapasowa**.
    Zostanie otwarty blok Infrastruktura zapasowa.
3. W bloku Infrastruktura zapasowa kliknij pozycję **Konfiguracja kopii zapasowej** w celu otwarcia bloku **Konfiguracja kopii zapasowej**.

    ![Ustawianie konfiguracji przechowywania dla nowego magazynu](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Wybierz odpowiednią opcję replikacji dla magazynu.

    ![Opcje konfiguracji usługi Storage](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Domyślnie magazyn jest nadmiarowy geograficznie. Jeśli używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, kontynuuj korzystanie z magazynu **geograficznie nadmiarowego**. Jeśli nie używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, wybierz pozycję **Lokalnie nadmiarowy**, co zmniejszy koszty magazynów platformy Azure. Więcej informacji o opcjach magazynu [geograficznie nadmiarowego](../storage/common/storage-redundancy-grs.md) i [lokalnie nadmiarowego](../storage/common/storage-redundancy-lrs.md) można znaleźć w tym [omówieniu nadmiarowości magazynu](../storage/common/storage-redundancy.md).

Teraz, po utworzeniu magazynu, należy go skonfigurować do tworzenia kopii zapasowej stanu systemu Windows.

## <a name="configure-the-vault"></a>Konfigurowanie magazynu
1. W bloku Magazyn usługi Recovery Services (dla właśnie utworzonego magazynu) w sekcji Wprowadzenie kliknij pozycję **Kopia zapasowa**, a następnie w bloku **Wprowadzenie do kopii zapasowej** wybierz pozycję **Cel kopii zapasowej**.

    ![Otwieranie bloku celu kopii zapasowej](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    Zostanie otwarty blok **Cel kopii zapasowej**.

    ![Otwieranie bloku celu kopii zapasowej](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. Z menu **Gdzie działa Twoje obciążenie?** wybierz pozycję **Lokalnie**.

    Pozycję **Lokalnie** trzeba wybrać, ponieważ Twój serwer lub komputer z systemem Windows jest maszyną fizyczną spoza platformy Azure.

3. Z **jakich elementów chcesz utworzyć kopię zapasową?** menu, wybierz opcję **stanu systemu**i kliknij przycisk **OK**.

    ![Konfigurowanie plików i folderów](./media/backup-azure-system-state/backup-goal-system-state.png)

    Po kliknięciu przycisku OK obok pozycji **Cel kopii zapasowej** pojawi się znacznik wyboru i zostanie otwarty blok **Przygotowywanie infrastruktury**.

    ![Cel kopii zapasowej został skonfigurowany, następnym krokiem jest przygotowanie infrastruktury](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. W bloku **Przygotowywanie infrastruktury** kliknij pozycję **Pobierz agenta dla systemu Windows Server lub klienta systemu Windows**.

    ![Przygotowywanie infrastruktury](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Jeśli używasz systemu Windows Server Essential, wybierz opcję pobrania agenta dla systemu Windows Server Essential. Zostanie wyświetlone menu rozwijane z monitem o uruchomienie lub zapisanie pliku MARSAgentInstaller.exe.

    ![Okno dialogowe MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. W menu rozwijanym pobierania kliknij pozycję **Zapisz**.

    Domyślnie plik **MARSagentinstaller.exe** jest zapisywany w folderze Pobrane. Po ukończeniu pobierania pojawi się okno podręczne z pytaniem, czy chcesz uruchomić Instalatora, czy otworzyć folder.

    ![Przygotowywanie infrastruktury](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    Nie musisz jeszcze instalować agenta. Agenta możesz zainstalować po pobraniu poświadczeń magazynu.

6. W bloku **Przygotowywanie infrastruktury** kliknij pozycję **Pobierz**.

    ![Pobieranie poświadczeń magazynu](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    Poświadczenia magazynu zostaną pobrane do folderu Pobrane. Po zakończeniu pobierania poświadczeń magazynu zobaczysz okno podręczne z pytaniem, czy chcesz otworzyć poświadczenia, czy je zapisać. Kliknij pozycję **Zapisz**. Jeśli przypadkowo klikniesz pozycję **Otwórz**, zaczekaj, aż działanie okna dialogowego, które spróbuje otworzyć poświadczenia magazynu, zakończy się niepowodzeniem. Poświadczeń magazynu nie da się otworzyć. Przejdź do następnego kroku. Poświadczenia magazynu znajdują się w folderze Pobrane.   

    ![Zakończenie pobierania poświadczeń magazynu](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > Poświadczenia magazynu musi zostać zapisany tylko do lokalizacji, która jest lokalny dla systemu Windows Server, na którym zamierzasz korzystać z agenta.
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>Instalowanie i rejestrowanie agenta

> [!NOTE]
> Opcja włączania kopii zapasowych za pośrednictwem witryny Azure Portal jest jeszcze niedostępna. Tworzenie kopii zapasowych stanu systemu Windows Server przy użyciu agenta usługi Microsoft Azure Recovery Services.
>

1. Zlokalizuj i kliknij dwukrotnie plik **MARSagentinstaller.exe** w folderze Pobrane (lub innej lokalizacji).

    Instalator wyświetli serię komunikatów w miarę wypakowywania, instalowania i rejestrowania agenta usługi Recovery Services.

    ![Uruchamianie Instalatora agenta usługi Recovery Services — poświadczenia](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Wykonaj kroki kreatora instalacji agenta usługi Microsoft Azure Recovery Services. Aby zakończyć pracę kreatora, wykonaj następujące czynności:

   * Wybierz lokalizację folderu instalacji i folderu pamięci podręcznej.
   * Podaj informacje o serwerze proxy, jeśli korzystasz z niego do łączenia się z Internetem.
   * Podaj swoją nazwę użytkownika i hasło, jeśli korzystasz z uwierzytelnionego serwera proxy.
   * Udostępnianie pobranych poświadczeń magazynu
   * Zapisz hasło szyfrowania w bezpiecznej lokalizacji.

     > [!NOTE]
     > Jeśli utracisz lub zapomnisz hasło, firma Microsoft nie będzie mogła pomóc w odzyskaniu danych kopii zapasowej. Zapisz plik w bezpiecznej lokalizacji. Jest ono wymagane do przywrócenia kopii zapasowej.
     >
     >

Agent jest teraz zainstalowany, a maszyna zarejestrowana w magazynie. Wszystko jest gotowe do skonfigurowania kopii zapasowej i zaplanowania jej tworzenia.

## <a name="back-up-windows-server-system-state"></a>Tworzenie kopii zapasowej stanu systemu Windows Server
Początkowa kopia zapasowa obejmuje dwa zadania:

* Planowanie tworzenia kopii zapasowej
* Tworzenie kopii zapasowej stanu systemu po raz pierwszy

Aby utworzyć początkową kopię zapasową, użyj agenta usługi Microsoft Azure Recovery Services.

> [!NOTE]
> Można utworzyć kopię zapasową stanu systemu w systemie Windows Server 2008 R2 za pomocą systemu Windows Server 2016. Powrót do ochrony stanu systemu nawet nie jest obsługiwana na jednostki SKU klienta. Stan systemu nie jest wyświetlany jako opcja dla klientów Windows lub maszyny z systemem Windows Server 2008 z dodatkiem SP2.
>
>

### <a name="to-schedule-the-backup-job"></a>Aby zaplanować zadanie tworzenia kopii zapasowej

1. Otwórz agenta usługi Microsoft Azure Recovery Services. Aby go znaleźć, wyszukaj na maszynie łańcuch **Microsoft Azure Backup**.

    ![Uruchamianie agenta usługi Azure Recovery Services](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. W agencie usługi Recovery Services kliknij pozycję **Zaplanuj wykonywanie kopii zapasowej**.

    ![Planowanie tworzenia kopii zapasowej systemu Windows Server](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Na stronie Wprowadzenie Kreatora harmonogramu kopii zapasowej kliknij przycisk **Dalej**.

4. Na stronie Wybieranie elementów do wykonania kopii zapasowej kliknij pozycję **Dodaj elementy**.

5. Wybierz **stanu systemu** a następnie kliknij przycisk **OK**.

6. Kliknij przycisk **Dalej**.

7. Wybierz wymagane częstotliwość wykonywania kopii zapasowych i zasady przechowywania dla kopii zapasowych stanu systemu na kolejnych stronach.

8. Przejrzyj informacje na stronie Potwierdzenie, a następnie kliknij przycisk **Zakończ**.

9. Po ukończeniu harmonogramu tworzenia kopii zapasowej przez kreatora kliknij przycisk **Zamknij**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Aby utworzyć kopię zapasową stanu systemu Windows Server po raz pierwszy

1. Upewnij się, że nie ma żadnych oczekujące aktualizacje dla systemu Windows Server, które wymagają ponownego uruchomienia systemu.

2. W agencie Usług odzyskiwania kliknij pozycję **Wykonaj kopię zapasową teraz**, aby zakończyć początkowe umieszczanie za pośrednictwem sieci.

    ![Natychmiastowe tworzenie kopii zapasowej systemu Windows Server](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. Wybierz **stanu systemu** na **wybierz element kopii zapasowej** ekran, który pojawia się, a następnie kliknij przycisk **dalej**.

4. Na stronie Potwierdzenie przejrzyj ustawienia, które zostaną użyte przez Kreatora natychmiastowego tworzenia kopii zapasowej do utworzenia kopii zapasowej maszyny. Następnie kliknij pozycję **Utwórz kopię zapasową**.

4. Kliknij przycisk **Zamknij**, aby zamknąć kreatora. Jeśli zamkniesz kreatora przed zakończeniem procesu tworzenia kopii zapasowej, kreator będzie nadal działać w tle.


Po zakończeniu tworzenia początkowej kopii zapasowej w konsoli usługi Backup zostanie wyświetlony stan **Ukończono zadanie**.

  ![Początkowa replikacja została zakończona](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Pytania?
Jeśli masz pytania lub jeśli brakuje Ci jakiejś funkcji, [prześlij nam opinię](https://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [tworzeniu kopii zapasowej maszyn z systemem Windows](backup-configure-vault.md).
* Teraz, gdy utworzono kopię zapasową stanu systemu Windows Server, możesz [zarządzać swoimi magazynami i serwerami](backup-azure-manage-windows-server.md).
* Jeśli chcesz przywrócić kopię zapasową, w tym artykule znajdziesz informacje dotyczące [przywracania plików na maszynę z systemem Windows](backup-azure-restore-windows-server.md).
